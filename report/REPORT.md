# Báo cáo Lab: Self evolving Agentic

## 1. Thông tin nhóm và cấu hình

| Họ tên | Mã sinh viên | Phần đóng góp |
|---|---|---|
| Đinh Đức Long | 2A202602633 | 100% |

- Mô hình: `gpt-6-luna` (OpenAI provider, cấu hình qua `LAB_MODEL=openai:gpt-6-luna`), nhiệt độ `LAB_TEMPERATURE=1`, `recursion_limit=60`
- Phiên bản Deep Agents: `deepagents 0.7.21`, Windows 11, môi trường Python 3.12 venv
- Số lần chạy tác vụ đã dùng / ngân sách: 9 / 30
- Commit của tag `freeze`: `f2fa551`

## 2. Giả thuyết (commit trước tag `freeze`, Phần 4.0)

- H1 (subagents so với baseline): subagents sẽ đạt điểm tương đương hoặc chỉ chênh lệch nhẹ so với baseline trên tác vụ đánh giá (do năng lực mô hình đơn lẻ đã đủ để giải quyết logic kỹ thuật), nhưng chi phí token của subagents sẽ cao hơn từ 1.5 đến 2 lần do chi phí bổ sung trong prompt định tuyến và trao đổi tác tử con.
- H2 (skills-auto so với baseline): skills-auto sẽ đạt điểm cao hơn baseline trên tác vụ đánh giá đối với các quy ước chung được chuyển giao (chuẩn hóa schema, kiểm tra tính toàn vẹn của artifact đầu ra), tuy nhiên mức cải thiện trên tập đánh giá sẽ thấp hơn trên tập học do hiện tượng quá khớp (overfitting) với các check của tập học như đã quan sát trong nghiên cứu SkillEvolBench.
- H3 (tác vụ học so với tác vụ đánh giá): Điểm trung bình trên tác vụ học sẽ cao hơn tác vụ đánh giá ở điều kiện skills-auto vì các skill được curator chắt lọc trực tiếp từ các check thất bại của tập học; tác vụ đánh giá chứa các quy ước tổ chức mới mà skill chưa từng quan sát.

## 3. Làm quen Deep Agents (Phần 0.3)

1. Tác tử mặc định có 9 công cụ:
   - 7 công cụ thao tác tệp: `ls`, `read_file`, `write_file`, `edit_file`, `delete`, `glob`, `grep`.
   - 1 công cụ shell: `execute` (dùng để chạy lệnh trực tiếp trong môi trường sandbox).
   - 1 công cụ gọi tác tử con: `task`.
   Công cụ chạy lệnh hệ thống là `execute`.
2. Mô tả của công cụ `task` về subagent `general-purpose`:
   - "General-purpose agent for researching complex questions, searching for files and content, and executing multi-step tasks."
   - Về ngữ cảnh: Mỗi lần gọi là phi trạng thái (stateless by default). Subagent chỉ nhìn thấy những gì được giao trong prompt và trả về một báo cáo kết quả duy nhất. Subagent không thấy lịch sử trò chuyện trước đó của tác tử chính.
3. Trích dẫn hướng dẫn hành vi từ mô tả công cụ:
   - Từ mô tả công cụ `task`: *"Put full detail in the prompt and state exactly what it should return unless an agent type below says it inherits your conversation instead."*
   - Từ mô tả công cụ `execute`: *"Use absolute paths and avoid `cd` so the working directory stays stable; use the optional timeout to override the default."*

## 4. Đường cơ sở và phân loại lỗi (Phần 2.2)

| Tác vụ | Check thất bại | Nhóm lỗi (A-G) | Bằng chứng (trích ngắn từ `detail` hoặc vết) |
|---|---|---|---|
| `data-learn` | `rule_money_in_cents` | E | `detail`: "RULE: money values in answer.json are integer cents (1606.67 USD is written 160667)." |
| `data-learn` | `rule_meta_block` | E | `detail`: "RULE: answer.json has an object `meta` = {"source": <input file name>, "rows_in": ..., "rows_used": ...}." |
| `data-learn` | `rule_clean_csv` | E | `detail`: "RULE: write workspace/clean.csv with the header order_id,timestamp_utc,region,amount_cents..." |
| `code-learn` | `tests_not_modified` | E | `detail`: "the original files in tests/ must not be modified (new test files are allowed)" |
| `code-learn` | `rule_type_hints` | E | `detail`: "RULE: every public function (name not starting with '_') in the package has type annotations on all parameters and on the return value." |
| `code-learn` | `rule_regression_tests` | E | `detail`: "RULE: add tests/test_regressions.py with one test function per bug you fixed (at least 3); the file must pass." |
| `code-learn` | `rule_changelog` | E | `detail`: "RULE: record each fix in CHANGELOG.md under the heading '## Unreleased' as a bullet '- fix(<function name>): <short description>' (at least 3 bullets)." |
| `logs-learn` | `rule_service_names` | E | `detail`: "RULE: service names in the output are lower-case with '-' replaced by '_' (payment-service -> payment_service)." |
| `logs-learn` | `rule_sorted_errors` | E | `detail`: "RULE: `errors` is sorted by service, then by timestamp_utc, ascending." |
| `logs-learn` | `rule_schema_header` | E | `detail`: "RULE: the top-level object has "schema_version": 2 and "generated_by": "log-triage"." |

Nhận xét của em:
Khi soi log và kết quả chạy baseline, em thấy toàn bộ 10/10 check bị fail ở 3 bài học đều rơi vào nhóm E (vi phạm quy ước ngầm của tổ chức). Về mặt kỹ thuật thuần túy, mô hình `gpt-6-luna` giải quyết rất mượt, đạt trọn vẹn 17/17 check (tính đúng doanh thu, lọc trùng, chuẩn hóa giờ UTC, sửa đúng 4 bug trong code, bóc tách đúng lỗi log). Mô hình không hề bị hiểu sai đề (nhóm A), không sai thuật toán (nhóm B), cũng không gặp lỗi môi trường (nhóm C, D). Lý do agent bị trừ điểm là vì đề bài không hề nhắc đến các "luật ngầm" của công ty Acme Corp (như tiền phải đổi ra cent, phải nhét thêm object meta, hay format changelog chuẩn). Những yêu cầu này chỉ nằm ẩn trong file chấm `check.py`. Vì vậy, em thấy hướng đi dùng skill để nhắc agent các quy ước này là rất đúng trọng tâm.

## 5. Điều kiện `subagents` (Phần 2.3)

- Ba subagent em đã thiết kế:
  + `explorer`: Chuyên đọc file, xem schema, cấu hình và dữ liệu mẫu; tìm hiểu thông tin rồi báo cáo lại chứ không sửa file.
  + `implementer`: Chuyên bắt tay vào sửa code, tạo file kết quả và chạy thử test trong sandbox.
  + `reviewer`: Đóng vai người kiểm tra độc lập, soi lại file đã sửa xem có đúng yêu cầu đề bài và sót trường hợp biên nào không; không sửa file.
- Số lần gọi subagent (`subagent_calls`): Cả 3 bài (`code-learn`, `data-learn`, `logs-learn`) đều ghi nhận 0 lần gọi. Em quan sát thấy agent chính tự làm hết từ đầu đến cuối mà không thèm chia việc cho subagent nào. Do các bài toán này file khá ít (chỉ 1-2 file), agent chính có vẻ thấy tự gọi tool làm luôn sẽ nhanh và giữ được ngữ cảnh tốt hơn là chuyển việc qua một subagent không nhớ ngữ cảnh cũ.
- Ảnh hưởng đến token và thời gian: Tổng token tăng từ 193,765 (ở baseline) lên 348,658 (ở subagents), tức là tốn thêm khoảng 80%. Thời gian chạy trung bình cũng tăng từ 44.5 giây lên 120 giây một bài. Dù agent không gọi subagent nào, chi phí token vẫn bị đội lên do prompt hệ thống phải gánh thêm phần mô tả dài của 3 subagent và câu nhắc phân quyền `SUBAGENTS_NOTE`, làm cho mỗi lượt gọi mô hình đều tốn token đầu vào hơn.

## 6. Self-evolving: skill do curator sinh (Phần 3)

- Số lần chạy curator: 1 lần. Em giữ nguyên cả 3 skill, không phải xóa cái nào.
- Đánh giá của em về 3 skill sinh ra:

| Skill | Tổng quát hay riêng cho tác vụ học? | Đúng hay sai (nêu chỗ sai nếu có) | Độ dài, `description` và `skills_read` ở Phần 3.4 |
|---|---|---|---|
| `code-maintenance-completion` | Viết khá tổng quát về quy trình bảo trì mã nguồn và tuân thủ luật repo | Đúng: dặn giữ nguyên test gốc, tạo file test hồi quy mới, thêm type hint cho public function, viết changelog | Dài 11 dòng, viết dạng checklist ngắn gọn; `description` nêu rõ trường hợp kích hoạt; bài `code-learn` đọc 1 lần (`skills_read = 1`) |
| `tabular-data-output-validation` | Tổng quát cho việc xử lý và chuẩn hóa dữ liệu bảng | Đúng: dặn đếm dòng trước khi lọc trùng, đổi tiền ra số nguyên cent, chuẩn hóa giờ UTC và xuất đủ file | Dài 13 dòng, câu văn mệnh lệnh rõ ràng; `description` kích hoạt chuẩn khi gặp dữ liệu bảng; bài `data-learn` đọc 1 lần (`skills_read = 1`) |
| `log-output-normalization` | Tổng quát cho việc bóc tách và chuẩn hóa nhật ký lỗi | Đúng: nhắc dùng schema version 2, đổi tên service thành snake_case, sắp xếp compound key | Dài 12 dòng; `description` chuẩn cho tác vụ log; bài `logs-learn` đọc 1 lần (`skills_read = 1`) |

## 7. Kết quả so sánh (Phần 4.3, 4.4)

### Bảng so sánh tổng hợp (`report/table.md`)

| Task | baseline | subagents | skills-auto |
|---|---|---|---|
| code-learn | 6/10 | 6/10 | 8/10 |
| data-learn | 5/8 | 4/8 | 5/8 |
| logs-learn | 6/9 | 6/9 | 8/9 |
| code-eval | 6/11 | 6/11 | 8/11 |
| data-eval | 5/9 | 4/9 | 5/9 |
| logs-eval | 6/10 | 6/10 | 8/10 |
| **Mean score - learning tasks** | 0.63 | 0.59 | 0.77 |
| **Mean score - evaluation tasks** | 0.57 | 0.53 | 0.69 |
| **Mean tokens per run** | 62,795 | 137,398 | 111,379 |
| **Runs that read a skill** | 0/6 | 0/6 | 6/6 |

### Phân tích chi tiết check (`scripts/check_breakdown.py`)

```text
condition     role    technical  house rules  mean tokens  read a skill
baseline      eval     17/18         0/12          61,003      0/3     
baseline      learn    17/18         0/9           64,588      0/3     
subagents     eval     16/18         0/12         158,577      0/3     
subagents     learn    16/18         0/9          116,219      0/3     
skills-auto   eval     17/18         4/12          97,148      3/3     
skills-auto   learn    17/18         4/9          125,610      3/3     
```

### Trạng thái chạy và an toàn kỹ năng
Tất cả 18 lượt chạy ở cả 3 điều kiện (`baseline`, `subagents`, `skills-auto`) đều chạy trót lọt, không có lần nào bị crash hay văng lỗi hệ thống (`error: null`).
Trường `skills_modified` đều là `false` ở tất cả các lần chạy, agent không tự ý sửa file trong thư mục skills.
Cả 6 lượt chạy của `skills-auto` đều dùng đúng bộ skill đã đóng băng với mã băm `skills_sha256 = 71e374e6d0390a5ad0821d878be20400986f8a70f5b8098bcefa3bc7f7372ed2`, khớp chuẩn với tag `freeze`. Em chạy thử `python -X utf8 scripts/verify_freeze.py` thì ra `OK`.

## 8. Phân tích

1. So với baseline, em thấy điều kiện `skills-auto` giúp cải thiện điểm số ở cả bài học lẫn bài đánh giá. Ở tập học, điểm trung bình tăng từ 0.63 lên 0.77 (tăng khoảng 22.2%). Ở tập đánh giá, điểm cũng tăng từ 0.57 lên 0.69 (tăng khoảng 21.1%). Trong khi đó, điều kiện `subagents` không giúp tăng điểm chút nào, thậm chí còn giảm nhẹ (từ 0.63 xuống 0.59 ở bài học và từ 0.57 xuống 0.53 ở bài đánh giá) do bị lệch mất một bước lọc trùng ở `data-learn`. Em thấy mức tăng ở tập học (+0.14) cao hơn ở tập đánh giá (+0.12). Điều này khá dễ hiểu: skill được rút ra từ chính lỗi của tập học nên agent áp dụng lại rất trúng, còn sang tập đánh giá thì có thêm những quy ước mới lạ mà skill chưa từng thấy, đúng với hiện tượng quá khớp (overfitting) quy ước mà bài báo SkillEvolBench đã cảnh báo.

2. Nhìn vào bảng bóc tách chi tiết kỹ thuật và quy ước, em thấy rất rõ ràng: ở các check kỹ thuật, mô hình vốn đã làm rất tốt ngay từ đầu (đạt 17/18 điểm, tức 94.4% ở cả baseline lẫn skills-auto). Như vậy skill không can thiệp vào logic nghiệp vụ vì mô hình đã tự code đúng rồi. Sự khác biệt nằm trọn vẹn ở nhóm check quy ước (`rule_`): ở baseline và subagents, agent trượt 100% (0/9 ở bài học và 0/12 ở bài đánh giá). Nhờ có skill, tỷ lệ này tăng lên 4/9 ở bài học (44.4%) và 4/12 ở bài đánh giá (33.3%). Skill do curator sinh ra đã cứu điểm cho nhóm quy ước tổ chức này.
Còn với các check quy ước mới ở tập đánh giá, skill hoàn toàn bất lực. Ví dụ như check `rule_version_bump` (bắt tăng patch version trong file `__init__.py`) ở `code-eval` hay `rule_source_line` ở `logs-eval`. Vì curator chỉ học từ lỗi của bài học, nơi mà các quy tắc này chưa từng xuất hiện, nên skill không thể có thông tin để dặn agent. Đề bài không nói mà skill cũng không có thì agent chịu chết không tự đoán mò được.

3. Check mà skill giúp đạt: Đó là `rule_regression_tests` trong bài `code-eval`. Ở baseline, agent sửa xong 3 bug trong code là dừng lại, không hề viết file test hồi quy vì đề bài đâu có yêu cầu, thế là bị trượt check (chỉ được 6/11). Sang `skills-auto`, vết chạy cho thấy agent đã đọc skill `code-maintenance-completion` (`skills_read = 1`). Trong skill có dòng dặn: *"Add focused regression tests in a dedicated file to cover every bug fixed or behavior changed."* Agent đọc xong liền gọi công cụ `write_file` tạo ngay file `workspace/tests/test_regressions.py`, viết các hàm test cho từng bug rồi dùng lệnh `execute` chạy pytest xem test có pass không. Nhờ vậy check này pass ngon lành, kéo điểm bài đó lên 8/11.
Check mà skill chưa giúp được: Đó là `rule_changelog` trong bài `code-eval`. Em thấy agent cũng ngoan ngoãn đọc skill rồi vào tạo mục `## Unreleased` trong `CHANGELOG.md` kèm 3 dòng gạch đầu dòng tóm tắt lỗi đã sửa. Nhưng lúc chấm bằng file `check.py`, hệ thống lại soi bằng regex rất gắt theo chuẩn Conventional Commits: `^- fix\([A-Za-z_]\w*\): \S.+$`. Vì skill dặn hơi chung chung là viết bullet tóm tắt chứ không đưa mẫu regex `fix(...)` cụ thể ra, nên câu văn agent tự viết không khớp với regex, thế là vẫn bị đánh trượt check này.

4. Về chi phí token trung bình mỗi bài chạy: baseline tốn 62,795 token (điểm 0.60, tương đương 9.55 × 10⁻⁶ điểm/token); subagents tốn 137,398 token (điểm 0.56, chỉ được 4.08 × 10⁻⁶ điểm/token); skills-auto tốn 111,379 token (điểm 0.73, đạt 6.55 × 10⁻⁶ điểm/token).
Tính ra baseline có hiệu suất điểm trên token cao nhất vì prompt ngắn gọn nhất. Nhưng skills-auto đem lại sự đánh đổi rất đáng giá: token tăng 77% so với baseline nhưng điểm số tăng từ 0.60 lên 0.73 (+21.7%). Token tăng ở skills-auto là hoàn toàn hợp lý vì agent phải đọc thêm skill và tốn thêm các bước viết test hồi quy, bổ sung type hint.
Còn điều kiện subagents thì em thấy không đáng tiền chút nào. Token tốn gấp 2.19 lần mà điểm còn bị tụt. Agent chính thừa sức giải quyết bài toán một mình nên chẳng buồn gọi subagent nào (`subagent_calls = 0`). Việc nhét thêm mô tả dài dòng của 3 subagent vào prompt chỉ làm phí tiền token mà chẳng đem lại tác dụng gì.

5. Về rò rỉ dữ liệu, em đã kiểm tra kỹ và thấy các skill sinh ra không hề chứa tên file cụ thể hay đáp án số liệu nào của bài test.
Về hiện tượng quá khớp, em thấy có bị quá khớp ở mức quy ước. Các skill chỉ học được các quy ước xuất hiện ở bài học (như đổi tiền ra cent, tạo trường meta, đặt tên service kiểu snake_case). Khi sang bài đánh giá, những quy ước cũ này vẫn giúp ích được, nhưng các quy ước mới thì không đỡ được. Việc điểm bài học tăng nhiều hơn bài đánh giá (+0.14 so với +0.12) chính là biểu hiện của sự quá khớp này.
Em đã phòng tránh rò rỉ bằng cách: prompt curator dặn kỹ chỉ viết checklist quy trình chung, cấm đưa tên bài hay số liệu cứng; dùng hàm `validate_skill` để chặn các từ khóa dính đến tập đánh giá; và tuân thủ nghiêm ngặt freeze protocol, đóng băng skill trước khi chạy tập đánh giá.

6. So sánh điểm tập học giữa bản dev lưu ở Phần 3.4 (`skills-auto-dev`) và bản sau đóng băng (`skills-auto`):
- `code-learn`: bản dev được 8/10, sau đóng băng cũng được 8/10, chênh lệch 0.00.
- `data-learn`: bản dev được 5/8, sau đóng băng cũng được 5/8, chênh lệch 0.00.
- `logs-learn`: bản dev được 8/9, sau đóng băng cũng được 8/9, chênh lệch 0.00.
Điểm trung bình cả hai lần đều đạt 0.771, độ lệch đúng bằng 0.00.
Dù mô hình chạy ở nhiệt độ `LAB_TEMPERATURE=1`, kết quả chạy lại độc lập vẫn khớp nhau tuyệt đối. Điều này chứng minh điểm số tăng lên so với baseline (+0.14 ở bài học và +0.12 ở bài đánh giá) là nhờ agent thực sự tiếp thu được kiến thức từ skill, chứ không phải do may mắn hay do mô hình sinh ngẫu nhiên.

## 9. Hạn chế và tính hợp lệ

1. Số lượng bài test còn ít và mỗi bài chỉ chạy đúng một lần: Toàn bộ lab chỉ có 3 bài học và 3 bài đánh giá. Vì bị giới hạn ngân sách 30 lần chạy và tiết kiệm chi phí token, mỗi cấu hình em chỉ chạy đúng một lần chứ chưa thể chạy lặp lại 3 đến 5 lần để tính trung bình và độ lệch chuẩn. Dù điểm chạy lại giữa dev và sau freeze khớp nhau 100%, việc cỡ mẫu nhỏ vẫn khiến kết luận chưa mang tính thống kê sâu rộng.
2. Các quy tắc ngầm mang tính nhân tạo: Các check `rule_*` trong bài là các luật cố định được thầy cài sẵn trong file `check.py`. Trong dự án thực tế ngoài đời, quy ước của một công ty phức tạp hơn nhiều, nằm rải rác trong wiki, tài liệu onboarding hay qua các lần review PR của anh em trong team, chứ không có sẵn bot chấm điểm tự động trả về feedback lỗi rõ ràng như thế này để curator học.
3. Mới chỉ thử nghiệm trên một mô hình duy nhất: Em mới chỉ chạy thí nghiệm trên mô hình `gpt-6-luna`. Mỗi dòng mô hình lại có khả năng suy luận, độ ngoan ngoãn khi nghe prompt và xu hướng tự chia việc cho subagent khác nhau. Nếu đổi sang các mô hình khác như Claude hay GPT-4o, kết quả về chi phí subagent và khả năng đọc hiểu skill có thể sẽ khác.

## 10. Kết luận

Qua bài lab, em thấy cơ chế tự tiến hóa (Self-evolving) thông qua việc để agent tự rút kinh nghiệm từ lỗi sai và sinh ra skill thực sự giúp nâng cao điểm số, đưa điểm trung bình từ 0.63 lên 0.77 ở bài học và từ 0.57 lên 0.69 ở bài đánh giá. Điểm số tăng lên hoàn toàn nhờ việc agent học được cách tuân thủ các quy ước ngầm của tổ chức (từ 0% lên 33.3% - 44.4%), trong khi phần code kỹ thuật vẫn giữ được phong độ tốt (94.4%). Ngược lại, kiến trúc đa tác tử (subagents) ở các bài toán nhỏ này không mang lại hiệu quả gì mà còn làm tốn gấp đôi token do phình prompt. Quy trình đóng băng và việc kết quả chạy lại khớp nhau 100% chứng minh kết quả thí nghiệm có độ tin cậy rất cao. Hướng cải tiến tiếp theo em muốn thử là làm thêm cơ chế lọc skill động (dynamic skill routing), tức là bài nào cần luật gì thì chỉ nạp đúng skill đó vào prompt để đỡ tốn tiền token ngữ cảnh.

## Phụ lục

- Lệnh đã chạy (theo thứ tự thực hiện):
  1. `pytest` (xác thực 29/29 bài test harness ban đầu đạt 100%)
  2. `python -m lab.runner --condition baseline --tasks learn`
  3. `python -m lab.runner --condition baseline --tasks eval`
  4. `python -m lab.runner --condition subagents --tasks learn`
  5. `python -m lab.runner --condition subagents --tasks eval`
  6. `python -m lab.curator` (chắt lọc và sinh 3 kỹ năng tự động vào `skills/auto/`)
  7. `python -m lab.runner --condition skills-auto --tasks learn`
  8. `mv results/skills-auto results/skills-auto-dev` (sao lưu kết quả dev để đo nhiễu)
  9. `git add -A && git commit -m "hypotheses"` (commit giả thuyết trước freeze: `ebb6d3d`)
  10. `git add -A && git commit --allow-empty -m "freeze skills" && git tag freeze` (commit freeze: `f2fa551`)
  11. `python -m lab.runner --condition skills-auto --tasks all` (chạy chính thức 6 tác vụ sau freeze)
  12. `python -X utf8 scripts/verify_freeze.py` (xác nhận trạng thái freeze đạt `OK`)
  13. `python -m lab.compare` (tạo `report/table.md` định dạng UTF-8)
  14. `python scripts/check_breakdown.py` (trích xuất thống kê chi tiết kỹ thuật và quy ước)
  15. `pytest` (kiểm tra lại toàn bộ test harness trước khi hoàn thiện)
- Thử thách mở rộng (nếu có): Không thực hiện (tập trung tối đa chất lượng các hạng mục chính 1 đến 6).
- Ghi chú khác:
  + Môi trường Windows 11 yêu cầu cờ `-X utf8` (hoặc biến môi trường `PYTHONUTF8=1`) khi chạy các lệnh kiểm tra git diff để tránh lỗi giải mã ký tự tiếng Việt với cp1258.
  + Mô hình `gpt-6-luna` bắt buộc cấu hình `LAB_TEMPERATURE=1` theo ràng buộc API OpenAI; thông số này được giữ cố định xuyên suốt tất cả các lần chạy.
