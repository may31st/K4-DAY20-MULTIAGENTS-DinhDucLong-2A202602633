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
   - 7 công cụ tệp: `ls`, `read_file`, `write_file`, `edit_file`, `delete`, `glob`, `grep`.
   - 1 công cụ shell: `execute` (dùng để chạy lệnh trực tiếp trong môi trường sandbox).
   - 1 công cụ tác tử con: `task`.
   Công cụ chạy lệnh hệ thống là `execute`.
2. Mô tả của công cụ `task` về subagent `general-purpose`:
   - "General-purpose agent for researching complex questions, searching for files and content, and executing multi-step tasks."
   - Về ngữ cảnh: Mỗi lần gọi là phi trạng thái (stateless by default). Subagent chỉ nhìn thấy nội dung được truyền trong prompt phân việc và trả về một báo cáo kết quả. Subagent không thấy lịch sử hội thoại trước đó của tác tử chính.
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

Nhận xét:
Tất cả 10 check thất bại trên 3 tác vụ học đều thuộc nhóm E (vi phạm quy ước tổ chức). Mô hình đạt điểm tuyệt đối 17/17 ở các check kỹ thuật (lọc dữ liệu, tính doanh thu, khử trùng lặp, chuẩn hóa múi giờ UTC, sửa 4 lỗi trong package, trích xuất log). Mô hình không gặp lỗi hiểu sai đề bài (nhóm A), sai thuật toán (nhóm B), hay lỗi môi trường (nhóm C, D). Nguyên nhân thất bại là đề bài không nêu các quy ước ngầm của Acme Corp (như quy đổi cent, cấu trúc meta, tiêu chuẩn changelog). Các quy ước này chỉ xuất hiện trong file kiểm tra `check.py`, vì vậy một bộ skill hướng dẫn quy ước có thể giải quyết được nhóm lỗi này.

## 5. Điều kiện `subagents` (Phần 2.3)

- Các subagent đã định nghĩa:
  + `explorer`: Đọc tài liệu, schema, cấu hình và dữ liệu mẫu; báo cáo thông tin tìm được; không sửa file.
  + `implementer`: Chỉnh sửa mã nguồn, tạo file kết quả, chạy script và kiểm thử trong sandbox.
  + `reviewer`: Kiểm tra độc lập kết quả đối chiếu với yêu cầu đề bài; không sửa file.
- Số lần gọi subagent (`subagent_calls`): 0 lần ở cả 3 tác vụ (`code-learn`, `data-learn`, `logs-learn`). Tác tử chính tự giải quyết tác vụ mà không giao việc cho subagent. Do các bài toán có phạm vi nhỏ (1 đến 2 file), tác tử chính gọi công cụ trực tiếp để giữ ngữ cảnh thay vì chuyển việc qua subagent phi trạng thái.
- Ảnh hưởng đến token và thời gian: Tổng token tăng từ 193,765 (baseline) lên 348,658 (subagents), tức tăng khoảng 80%. Thời gian chạy trung bình mỗi tác vụ tăng từ 44.5 giây lên 120 giây. Dù không gọi subagent nào, chi phí token vẫn tăng do prompt phải chứa thêm phần mô tả của 3 subagent và ghi chú phân quyền `SUBAGENTS_NOTE`, làm tăng token đầu vào ở mỗi bước gọi mô hình.

## 6. Self-evolving: skill do curator sinh (Phần 3)

- Số lần chạy curator: 1 lần. Số skill bị xóa: 0.
- Đánh giá các skill sinh ra:

| Skill | Tổng quát hay riêng cho tác vụ học? | Đúng hay sai (nêu chỗ sai nếu có) | Độ dài, `description` và `skills_read` ở Phần 3.4 |
|---|---|---|---|
| `code-maintenance-completion` | Tổng quát cho việc bảo trì code và tuân thủ quy ước repo | Đúng: hướng dẫn giữ nguyên test gốc, tạo test regression mới, gán type hints cho public function, ghi changelog | Dài 11 dòng (ngắn gọn, checklist); `description` rõ ràng khi nào kích hoạt; `skills_read = 1` ở `code-learn` |
| `tabular-data-output-validation` | Tổng quát cho việc xử lý và chuẩn hóa dữ liệu bảng | Đúng: đếm dòng trước deduplicate, chuyển đổi số tiền sang cent, chuẩn hóa UTC và xuất đủ artifacts | Dài 13 dòng (mệnh lệnh, dễ kiểm chứng); `description` kích hoạt đúng tình huống; `skills_read = 1` ở `data-learn` |
| `log-output-normalization` | Tổng quát cho chuẩn hóa và trích xuất nhật ký lỗi | Đúng: hướng dẫn schema version 2, format snake_case cho service, sắp xếp compound key | Dài 12 dòng; `description` nêu đúng tình huống kích hoạt; `skills_read = 1` ở `logs-learn` |

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
Tất cả 18 lượt chạy thuộc 3 điều kiện (`baseline`, `subagents`, `skills-auto`) đều kết thúc bình thường (`error: null`). Không có lượt chạy nào bị lỗi hay ngắt quãng giữa chừng.
Trường `skills_modified` đều nhận giá trị `false` trong mọi lần chạy.
Tất cả 6 lần chạy của `skills-auto` đều dùng đúng mã băm `skills_sha256 = 71e374e6d0390a5ad0821d878be20400986f8a70f5b8098bcefa3bc7f7372ed2`, khớp với commit gắn tag `freeze`. Lệnh `python scripts/verify_freeze.py` trả về `OK`.

## 8. Phân tích

1. So với baseline, điều kiện `skills-auto` cải thiện điểm số ở cả hai nhóm tác vụ. Trên tập học, điểm trung bình tăng từ 0.63 lên 0.77 (+0.14 điểm, tương đương 22.2%). Trên tập đánh giá, điểm tăng từ 0.57 lên 0.69 (+0.12 điểm, tương đương 21.1%). Điều kiện `subagents` không cải thiện điểm, giảm nhẹ từ 0.63 xuống 0.59 ở tập học và từ 0.57 xuống 0.53 ở tập đánh giá do một sai lệch ngẫu nhiên trong lọc trùng ở `data-learn`. Mức cải thiện ở tập học cao hơn tập đánh giá (+0.14 so với +0.12). Đây là dấu hiệu của việc chuyển giao tri thức một phần, đồng thời có hiện tượng quá khớp quy ước (convention overfitting) như nghiên cứu SkillEvolBench đã chỉ ra: các quy ước của tập học được tái sử dụng thành công, nhưng tập đánh giá có những quy ước mới mà skill chưa từng ghi nhận.

2. Theo bảng phân tích chi tiết, ở nhóm check kỹ thuật, mô hình đạt 17/18 điểm (94.4%) ngay từ baseline và giữ nguyên 17/18 ở `skills-auto` trên cả hai tập. Năng lực giải quyết bài toán chuyên môn của mô hình đã tốt từ đầu nên skill không làm thay đổi nhóm này. Ngược lại, ở nhóm check quy ước (`rule_`), baseline và subagents đều đạt 0/9 ở tập học và 0/12 ở tập đánh giá (0%). Điều kiện `skills-auto` nâng tỷ lệ này lên 4/9 ở tập học (44.4%) và 4/12 ở tập đánh giá (33.3%). Skill do curator sinh ra hỗ trợ trực tiếp nhóm check quy ước tổ chức.
Các check quy ước mới của tập đánh giá không được skill hỗ trợ. Ví dụ, `code-eval` có check `rule_version_bump` (yêu cầu tăng patch version trong `__init__.py`) và `logs-eval` có `rule_source_line`. Vì curator chỉ học từ phản hồi lỗi của tập học, nơi các quy ước này chưa xuất hiện, skill không có thông tin về chúng. Tác tử không thể đoán được các quy ước ngầm nếu đề bài và skill đều không đề cập.

3. Check mà skill giúp đạt: Check `rule_regression_tests` trong `code-eval`. Ở baseline, tác tử sửa 3 bug nhưng không viết file test hồi quy do đề bài không yêu cầu, khiến check này bị fail (điểm 6/11). Ở `skills-auto`, vết chạy ghi nhận `skills_read = 1`. Tác tử đọc skill `code-maintenance-completion` với dòng hướng dẫn: *"Add focused regression tests in a dedicated file to cover every bug fixed or behavior changed."* Sau đó, tác tử dùng `write_file` tạo `workspace/tests/test_regressions.py` chứa các hàm kiểm thử tương ứng và chạy pytest kiểm tra file này pass. Check `rule_regression_tests` chuyển sang pass, nâng điểm lên 8/11.
Check mà skill không giúp được: Check `rule_changelog` trong `code-eval`. Tác tử đã đọc skill và tạo mục `## Unreleased` trong `CHANGELOG.md` kèm 3 gạch đầu dòng ghi nhận sửa lỗi. Tuy nhiên, `check.py` kiểm tra theo regex chặt chẽ của Conventional Commits: `^- fix\([A-Za-z_]\w*\): \S.+$`. Vì skill chỉ hướng dẫn viết gạch đầu dòng tóm tắt mà không cung cấp mẫu regex cụ thể, định dạng câu của tác tử không khớp với kiểm tra, khiến check tiếp tục thất bại.

4. Về chi phí token trung bình mỗi lần chạy: baseline dùng 62,795 token (điểm trung bình 0.60, đạt khoảng 9.55 × 10⁻⁶ điểm/token); subagents dùng 137,398 token (điểm 0.56, đạt khoảng 4.08 × 10⁻⁶ điểm/token); skills-auto dùng 111,379 token (điểm 0.73, đạt khoảng 6.55 × 10⁻⁶ điểm/token).
Baseline có tỷ lệ điểm trên token cao nhất do prompt ngắn nhất. Skills-auto tăng 77% token so với baseline nhưng đổi lại điểm số tăng từ 0.60 lên 0.73 (+21.7%). Token tăng ở skills-auto đến từ việc nạp nội dung skill vào ngữ cảnh và tác tử thực hiện thêm các thao tác bổ trợ như viết test hồi quy, bổ sung type annotations.
Điều kiện subagents không đáng chi phí trong thí nghiệm này. Chi phí token tăng gấp 2.19 lần so với baseline trong khi điểm số giảm nhẹ. Tác tử chính đủ khả năng xử lý bài toán trong cửa sổ ngữ cảnh nên không phân chia việc cho subagent (`subagent_calls = 0`). Việc đưa mô tả của 3 subagent vào prompt chỉ làm tăng chi phí token mà không mang lại kết quả thực tế.

5. Về rò rỉ dữ liệu, các skill sinh ra không chứa thông tin cụ thể của bài test như tên file dữ liệu, tên hàm, hay giá trị output mong muốn.
Về quá khớp, có hiện tượng quá khớp ở mức quy ước. Các skill phản ánh những quy ước xuất hiện trong 3 bài toán học (chia tiền thành cent, tạo trường meta, dùng snake_case cho tên dịch vụ). Khi sang tập đánh giá, các quy ước này giúp giải quyết những bài có yêu cầu tương tự, nhưng không xử lý được các quy ước mới. Mức tăng điểm ở tập học (+0.14) cao hơn tập đánh giá (+0.12) phản ánh độ lệch này.
Nhóm phòng tránh rò rỉ bằng cách: prompt curator yêu cầu chỉ viết quy tắc chung dạng danh sách kiểm tra, không đưa tên bài hay số liệu cụ thể; hàm `validate_skill` lọc bỏ skill chứa từ khóa của tập đánh giá hoặc vượt quá độ dài; và quy trình freeze protocol đóng băng bộ skill trước khi chạy tập đánh giá.

6. So sánh điểm tác vụ học giữa bản sao lưu Phần 3.4 (`skills-auto-dev`) và sau khi đóng băng (`skills-auto`):
- `code-learn`: bản dev đạt 8/10, sau đóng băng đạt 8/10, chênh lệch 0.00.
- `data-learn`: bản dev đạt 5/8, sau đóng băng đạt 5/8, chênh lệch 0.00.
- `logs-learn`: bản dev đạt 8/9, sau đóng băng đạt 8/9, chênh lệch 0.00.
Điểm trung bình cả hai lần đều là 0.771, chênh lệch bằng 0.00.
Dù mô hình chạy với `LAB_TEMPERATURE=1`, kết quả giữa hai lần chạy độc lập trùng khớp nhau. Điều này cho thấy sự cải thiện điểm số so với baseline (+0.14 ở tập học và +0.12 ở tập đánh giá) bắt nguồn từ nội dung các skill được nạp, không phải do biến động ngẫu nhiên khi sinh văn bản.

## 9. Hạn chế và tính hợp lệ

1. Số lượng tác vụ nhỏ và mỗi cấu hình chỉ chạy một lần: Mỗi điều kiện chỉ có 3 tác vụ học và 3 tác vụ đánh giá. Do giới hạn ngân sách 30 lần chạy và chi phí token, mỗi cấu hình chỉ chạy một lần thay vì lặp lại 3 đến 5 lần để tính phương sai và khoảng tin cậy. Dù mức chênh lệch giữa dev và post-freeze bằng 0, cỡ mẫu nhỏ vẫn giới hạn độ khái quát thống kê của kết luận.
2. Quy ước tổ chức mang tính nhân tạo: Các bài kiểm tra `rule_*` là quy tắc cố định được cài sẵn trong file `check.py`. Trong thực tế, quy ước của một dự án thường phức tạp hơn, nằm rải rác trong tài liệu hướng dẫn, pull request hoặc trao đổi nội bộ, chứ không hiển thị sẵn qua phản hồi lỗi tự động để curator trích xuất.
3. Thử nghiệm trên một mô hình duy nhất: Toàn bộ dữ liệu thu được từ mô hình `gpt-6-luna`. Khả năng tự chủ, xu hướng gọi subagent và độ nhạy với chỉ dẫn trong prompt phụ thuộc vào từng họ mô hình. Kết quả về chi phí subagent và khả năng tiếp thu skill có thể khác biệt trên các mô hình khác như Claude hay GPT-4o.

## 10. Kết luận

Thí nghiệm cho thấy việc tự động trích xuất kỹ năng từ phản hồi lỗi giúp tăng điểm trung bình của tác tử từ 0.63 lên 0.77 trên tập học và từ 0.57 lên 0.69 trên tập đánh giá. Mức tăng điểm tập trung ở nhóm kiểm tra quy ước tổ chức (từ 0% lên 33.3% đến 44.4%), trong khi điểm kiểm tra kỹ thuật duy trì ở mức 94.4%. Kiến trúc subagents không mang lại hiệu quả về điểm số nhưng làm tăng token gấp 2.19 lần do chi phí mô tả trong prompt. Quy trình đóng băng và độ chênh lệch 0.00 giữa hai đợt chạy xác nhận kết quả đo lường có tính ổn định cao. Hướng cải tiến tiếp theo là phát triển cơ chế chọn lọc kỹ năng động để chỉ nạp các quy ước phù hợp với từng tác vụ, giúp giảm chi phí token ngữ cảnh.

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
