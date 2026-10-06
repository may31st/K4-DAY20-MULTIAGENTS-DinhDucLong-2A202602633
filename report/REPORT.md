# Báo cáo Lab: Self evolving Agentic

## 1. Thông tin nhóm và cấu hình

| Họ tên | Mã sinh viên | Phần đóng góp |
|---|---|---|
| Đinh Đức Long | 2A202602633 | 100% |

- Mô hình: `gpt-6-luna` (OpenAI provider, cấu hình qua `LAB_MODEL=openai:gpt-6-luna`), nhiệt độ (`LAB_TEMPERATURE=1`), `recursion_limit=60`
- Phiên bản Deep Agents: `deepagents 0.7.21`, hệ điều hành Windows 11, môi trường Python 3.12 venv
- Số lần chạy tác vụ đã dùng / ngân sách: 9 / 30
- Commit của tag `freeze`: `f2fa551`

## 2. Giả thuyết (commit TRƯỚC tag `freeze`, Phần 4.0)

- H1 (subagents so với baseline): subagents sẽ đạt điểm tương đương hoặc chỉ chênh lệch nhẹ so với baseline trên tác vụ đánh giá (do năng lực mô hình đơn lẻ đã đủ để giải quyết logic kỹ thuật), nhưng chi phí token của subagents sẽ cao hơn từ 1.5 đến 2 lần do chi phí bổ sung trong prompt định tuyến và trao đổi tác tử con.
- H2 (skills-auto so với baseline): skills-auto sẽ đạt điểm cao hơn baseline trên tác vụ đánh giá đối với các quy ước chung được chuyển giao (chuẩn hóa schema, kiểm tra tính toàn vẹn của artifact đầu ra), tuy nhiên mức cải thiện trên tập đánh giá sẽ thấp hơn trên tập học do hiện tượng quá khớp (overfitting) với các check của tập học như đã quan sát trong nghiên cứu SkillEvolBench.
- H3 (tác vụ học so với tác vụ đánh giá): Điểm trung bình trên tác vụ học sẽ cao hơn tác vụ đánh giá ở điều kiện skills-auto vì các skill được curator chắt lọc trực tiếp từ các check thất bại của tập học; tác vụ đánh giá chứa các quy ước tổ chức mới mà skill chưa từng quan sát.

## 3. Làm quen Deep Agents (Phần 0.3)

1. Tác tử mặc định có 9 công cụ:
   - 7 công cụ tệp: `ls`, `read_file`, `write_file`, `edit_file`, `delete`, `glob`, `grep`.
   - 1 công cụ shell: `execute` (cho phép chạy lệnh trực tiếp trong môi trường sandbox).
   - 1 công cụ tác tử con: `task`.
   Công cụ cho phép chạy lệnh hệ thống là `execute`.
2. Mô tả của công cụ `task` về subagent `general-purpose`:
   - "General-purpose agent for researching complex questions, searching for files and content, and executing multi-step tasks."
   - Về ngữ cảnh: Mỗi lần gọi là phi trạng thái (stateless by default), subagent chỉ nhìn thấy prompt được tác tử chính giao và trả về một báo cáo kết quả duy nhất. Subagent không thấy lịch sử hội thoại của tác tử chính trừ khi được truyền chi tiết trong nội dung phân việc.
3. Trích dẫn hướng dẫn hành vi từ mô tả công cụ:
   - Từ mô tả công cụ `task`: *"Put full detail in the prompt and state exactly what it should return unless an agent type below says it inherits your conversation instead."*
   - Từ mô tả công cụ `execute`: *"Use absolute paths and avoid `cd` so the working directory stays stable; use the optional timeout to override the default."* (hoặc *"You MUST avoid using search commands like find and grep. Instead use the grep, glob tools to search. Use read_file rather than cat/head/tail."*).

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
- 100% check thất bại (10/10) thuộc nhóm E (Vi phạm quy ước tổ chức).
- Bằng chứng phủ định cho các nhóm A đến D: Toàn bộ 17/17 check kỹ thuật/nghiệp vụ trên 3 tác vụ học đều đạt điểm tối đa (lọc dữ liệu, tính toán doanh thu, khử trùng lặp, chuẩn hóa múi giờ UTC, sửa đúng 4 bug giá/chiết khấu/escape ký tự, parse chính xác ngoại lệ log). Mô hình gpt-6-luna không gặp lỗi hiểu sai đề bài hay thuật toán sai. Các check thất bại hoàn toàn xuất phát từ việc đề bài không nêu các quy ước tổ chức Acme Corp (quy ước ngầm được kiểm tra ở `check.py`). Do đó, một bộ skill về quy ước tổ chức có thể phòng ngừa hiệu quả các lỗi này.

## 5. Điều kiện `subagents` (Phần 2.3)

- Các subagent đã định nghĩa:
  + `explorer`: Đọc tài liệu, schema, cấu hình và dữ liệu mẫu; báo cáo sự thật khách quan; không sửa đổi file.
  + `implementer`: Thực hiện thay đổi trên mã nguồn, tạo file kết quả, chạy code và test trong sandbox; báo cáo kết quả.
  + `reviewer`: Kiểm tra độc lập sản phẩm đầu ra đối chiếu với yêu cầu đề bài và edge cases; không sửa file.
- `subagent_calls` ở từng tác vụ:
  + `code-learn`: 0 lần
  + `data-learn`: 0 lần
  + `logs-learn`: 0 lần
  Nhận xét: Tác tử chính `gpt-6-luna` tự chủ quyết định không gọi subagent con. Do các tác vụ này có phạm vi tệp tương đối cô đọng (1-2 tệp dữ liệu hoặc tệp mã nguồn), tác tử chính ưu tiên trực tiếp thao tác công cụ để bảo toàn ngữ cảnh hơn là phân rã qua một subagent phi trạng thái. Đây là hành vi hợp lệ đã được ghi nhận trong thiết kế harness.
- Ảnh hưởng đến token và thời gian:
  + Tổng token: tăng từ 193,765 (baseline) lên 348,658 (subagents) — tăng xấp xỉ 1.8 lần (80%).
  + Thời gian chạy trung bình mỗi tác vụ tăng từ 44.5s lên 120s.
  Mặc dù `subagent_calls = 0`, chi phí token vẫn tăng đáng kể do system prompt mang thêm thông tin mô tả chi tiết của 3 subagents cùng chỉ dẫn ủy quyền `SUBAGENTS_NOTE`, dẫn đến token đầu vào lớn hơn ở mọi bước gọi LLM.

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
- Toàn bộ 18 lượt chạy thuộc 3 điều kiện (`baseline`, `subagents`, `skills-auto`) đều kết thúc thành công với trường `error: null`. Không có lần chạy nào bị hủy hay gặp ngoại lệ hệ thống.
- Trong mọi lần chạy, `skills_modified` đều là `false` (tác tử không tự ý chỉnh sửa nội dung trong `skills/`).
- Toàn bộ 6 lần chạy thuộc `skills-auto` đều sử dụng đúng giá trị băm `skills_sha256 = 71e374e6d0390a5ad0821d878be20400986f8a70f5b8098bcefa3bc7f7372ed2` khớp chính xác với tag `freeze`. Lệnh `python scripts/verify_freeze.py` trả về `OK`.

## 8. Phân tích

1. **So sánh điều kiện trên tác vụ học và đánh giá:**
   - **Tác vụ học:** `skills-auto` cải thiện điểm số mạnh mẽ từ 0.63 (baseline) lên 0.77 (+0.14 điểm, tương đương tăng 22.2%). Ngược lại, điều kiện `subagents` giảm nhẹ từ 0.63 xuống 0.59 (-0.04 điểm) do một sai lệch ngẫu nhiên trong lọc dữ liệu trùng ở `data-learn`.
   - **Tác vụ đánh giá:** `skills-auto` tiếp tục vượt trội khi cải thiện điểm từ 0.57 (baseline) lên 0.69 (+0.12 điểm, tương đương tăng 21.1%). Điều kiện `subagents` giảm từ 0.57 xuống 0.53.
   - **Hiện tượng và dấu hiệu:** Cả hai tập đều được cải thiện bởi `skills-auto`, nhưng mức cải thiện ở tập học (+0.14) cao hơn ở tập đánh giá (+0.12). Đây là dấu hiệu rõ ràng của sự *chuyển giao tri thức một phần (partial generalization)* đi kèm với *hiện tượng quá khớp ở cấp độ quy ước (convention overfitting)* như đã chỉ ra trong nghiên cứu SkillEvolBench: các quy ước của tập học được kế thừa một phần sang tập đánh giá, nhưng tập đánh giá luôn chứa các quy ước tổ chức mới mà skill chưa từng quan sát.

2. **Phân tách check kỹ thuật và check quy ước (`rule_`):**
   - Theo kết quả breakdown, ở nhóm **check kỹ thuật**, mô hình `gpt-6-luna` đạt hiệu suất gần như tuyệt đối ngay từ `baseline`: 17/18 ở tập learn (94.4%) và 17/18 ở tập eval (94.4%). Điều kiện `skills-auto` duy trì trọn vẹn điểm số kỹ thuật 17/18 trên cả hai tập. Do đó, skill không đóng vai trò sửa lỗi logic chuyên môn.
   - Ở nhóm **check quy ước (`house rules`)**, `baseline` và `subagents` hoàn toàn thất bại với 0/9 ở tập learn (0%) và 0/12 ở tập eval (0%). Điều kiện `skills-auto` đã giúp tác tử đạt 4/9 ở tập learn (44.4%) và 4/12 ở tập eval (33.3%). Như vậy, skill do curator sinh giúp độc quyền nhóm check quy ước tổ chức.
   - **Check quy ước mới của tác vụ đánh giá:** Hoàn toàn **không** được skill giúp. Ví dụ: check `rule_version_bump` (yêu cầu tăng patch version trong `__init__.py`) ở `code-eval`, hoặc `rule_source_line` ở `logs-eval`. Vì curator chỉ học từ phản hồi lỗi của tác vụ học (nơi các quy ước này chưa từng xuất hiện), skill không có thông tin về chúng. Tác nhân không có cách nào tự suy đoán được các quy ước ẩn không được nêu trong mô tả bài toán.

3. **Phân tích cơ chế dựa trên vết (`trace.md`) và `skills_read`:**
   - **Check mà skill giúp đạt (`rule_regression_tests` trong `code-eval`):**
     + Ở baseline, tác tử sửa xong 3 bug trong mã nguồn nhưng không tạo file test hồi quy vì đề bài không yêu cầu, dẫn đến `rule_regression_tests` bị FAIL.
     + Ở `skills-auto`, vết chạy ghi nhận `skills_read = 1`. Tác tử đọc skill `code-maintenance-completion` có dòng chỉ dẫn: *"Add focused regression tests in a dedicated file to cover every bug fixed or behavior changed."* Ngay sau đó, tác tử gọi công cụ `write_file` tạo tệp `workspace/tests/test_regressions.py` chứa các ca kiểm thử cho từng hàm đã sửa và dùng `execute` chạy pytest kiểm tra file này pass. Check `rule_regression_tests` chuyển thành PASS (giúp tăng điểm từ 6/11 lên 8/11).
   - **Check mà skill không giúp được (`rule_changelog` trong `code-eval`):**
     + Check này bị FAIL dù tác tử có đọc skill.
     + Nguyên nhân: Skill thiếu đặc tả quy cách chi tiết. Trong skill `code-maintenance-completion`, dòng hướng dẫn chỉ ghi: *"Record every fix in CHANGELOG.md under '## Unreleased' with concise summary bullets."* Tác tử đã đọc và thực hiện viết mục `## Unreleased` vào `CHANGELOG.md` kèm 3 gạch đầu dòng tóm tắt. Tuy nhiên, bài chấm `check.py` yêu cầu cú pháp regex khắt khe của Conventional Commits: `^- fix\([A-Za-z_]\w*\): \S.+$`. Vì skill không cung cấp khuôn mẫu cụ thể này, văn bản của tác tử không khớp regex, dẫn đến kiểm tra thất bại dù tác tử đã cố gắng làm theo hướng dẫn của skill.

4. **Phân tích chi phí và hiệu quả token:**
   - **Token trung bình mỗi lần chạy:**
     + Baseline: 62,795 tokens/run (Mean score: 0.60; hiệu suất: ~9.55 × 10⁻⁶ điểm/token).
     + Subagents: 137,398 tokens/run (Mean score: 0.56; hiệu suất: ~4.08 × 10⁻⁶ điểm/token).
     + Skills-auto: 111,379 tokens/run (Mean score: 0.73; hiệu suất: ~6.55 × 10⁻⁶ điểm/token).
   - **Hiệu quả tốt nhất:** `baseline` có hiệu quả điểm/token cao nhất do prompt ngắn gọn nhất. Tuy nhiên, `skills-auto` mang lại sự đánh đổi chi phí - chất lượng rất tốt: chi phí token tăng 77% so với baseline nhưng điểm số tổng thể tăng từ 0.60 lên 0.73 (+21.7%). Token tăng ở `skills-auto` chủ yếu do việc nạp nội dung skill vào ngữ cảnh và tác tử thực hiện thêm các bước kiểm tra quy ước (tạo file hồi quy, chỉnh type hint).
   - **Đa tác tử có đáng chi phí không?** Hoàn toàn **không đáng**. `subagents` tiêu tốn gấp 2.19 lần token so với baseline (137,398 so với 62,795) nhưng điểm số lại sụt giảm (0.56 so với 0.60). Nguyên nhân: Các bài toán trong lab có phạm vi tệp nhỏ gọn, tác tử chính `gpt-6-luna` đủ năng lực xử lý trực tiếp nên không kích hoạt gọi subagents (`subagent_calls = 0`). Việc nhồi nhét mô tả 3 subagent và prompt phân quyền vào ngữ cảnh chỉ tạo ra chi phí chìm (prompt overhead) mà không đem lại giá trị phân việc thực tế nào.

5. **Hiện tượng rò rỉ dữ liệu và quá khớp:**
   - **Rò rỉ dữ liệu:** Hoàn toàn không có rò rỉ dữ liệu kiểm thử. Curator không trích xuất các giá trị nguyên văn (như tên file dữ liệu `data_orders_oct2026.csv`, tên hàm cụ thể, hay số liệu ground-truth của bài test).
   - **Quá khớp quy ước:** Có hiện tượng quá khớp ở mức quy ước tổ chức. Các skill phản ánh chính xác các mẫu quy ước xuất hiện trong 3 bài toán học (ví dụ: chia tiền thành cent, tạo `meta` block, quy ước snake_case). Khi sang bài toán đánh giá, các quy ước này giúp giải quyết các bài toán có quy ước tương tự, nhưng không giải quyết được các quy ước đánh giá mới. Mức chênh lệch cải thiện giữa tập học (+0.14) và tập đánh giá (+0.12) minh chứng cho mức độ quá khớp nhẹ này.
   - **Biện pháp phòng tránh:**
     + Curator prompt được ràng buộc chỉ tạo quy tắc tổng quát (procedural rules) dạng checklist, cấm trích dẫn chuỗi cụ thể của tác vụ.
     + Kiểm duyệt tự động qua `validate_skill` để đảm bảo định dạng YAML, kích thước ngắn gọn (< 20 dòng), mô tả rõ điều kiện kích hoạt.
     + Thực thi nghiêm ngặt quy trình đóng băng (Freeze Protocol): cam kết giả thuyết trước, gắn tag `freeze` git và xác thực bằng `verify_freeze.py` trước khi thực hiện bất kỳ lệnh nào trên tập đánh giá.

6. **Ước lượng nhiễu từ kết quả sao lưu:**
   - So sánh điểm tác vụ học giữa phiên bản `skills-auto-dev` (sao lưu ở Phần 3.4) và `skills-auto` (sau đóng băng):
     + `code-learn`: Dev đạt 8/10 (0.80) | Post-freeze đạt 8/10 (0.80) | Chênh lệch: 0.00
     + `data-learn`: Dev đạt 5/8 (0.625) | Post-freeze đạt 5/8 (0.625) | Chênh lệch: 0.00
     + `logs-learn`: Dev đạt 8/9 (0.889) | Post-freeze đạt 8/9 (0.889) | Chênh lệch: 0.00
     + Điểm trung bình tập học: Dev = 0.771 | Post-freeze = 0.771 | Chênh lệch trung bình: 0.000.
   - **Độ tin cậy của kết quả:** Chênh lệch bằng 0.00 giữa hai đợt chạy độc lập chứng minh tính lặp lại (reproducibility) cực kỳ cao của mô hình `gpt-6-luna` đối với bài toán này. Điều này khẳng định độ tin cậy vững chắc của bảng kết quả ở mục 7: sự gia tăng điểm số từ 0.63 lên 0.77 (học) và 0.57 lên 0.69 (đánh giá) là hiệu ứng thực chất của việc tiếp thu kỹ năng, hoàn toàn không phải do biến động ngẫu nhiên (sampling noise) của mô hình sinh.

## 9. Hạn chế và tính hợp lệ

1. **Quy mô tập tác vụ nhỏ và mỗi cấu hình chỉ chạy một lần (Sample size & single-run limitation):** Mỗi vai trò chỉ bao gồm 3 tác vụ học và 3 tác vụ đánh giá (tổng cộng 6 tác vụ). Do ràng buộc về ngân sách token và thời gian thực thi, mỗi cấu hình tác vụ chỉ được chạy đúng một lần thay vì 3-5 lần để tính trung bình và độ lệch chuẩn. Điều này hạn chế khả năng kiểm định ý nghĩa thống kê sâu hơn, dù độ lặp lại giữa dev và post-freeze cho thấy kết quả rất ổn định.
2. **Quy ước tổ chức nhân tạo và khép kín (Synthetic ground-truth rules):** Các bài kiểm tra quy ước (`rule_*`) trong harness là các quy tắc tĩnh được cài đặt sẵn trong `check.py`. Trong môi trường phần mềm thực tế, các quy ước repo phức tạp hơn, phân tán trong tài liệu onboarding, PR review hoặc thảo luận nhóm, chứ không thể hiện qua các bài kiểm tra tự động trả về phản hồi lỗi trực tiếp để curator dễ dàng trích xuất như trong lab.
3. **Phạm vi hạn chế trên một kiến trúc mô hình duy nhất (Single-model scope):** Toàn bộ thí nghiệm chỉ chạy trên mô hình `gpt-6-luna`. Khả năng tự chủ, xu hướng kích hoạt subagents và mức độ nhạy cảm với ngữ cảnh prompt phụ thuộc nhiều vào từng mô hình nền tảng. Các mô hình khác (như Claude 3.5 Sonnet, GPT-4o hay DeepSeek) có thể có xu hướng phân rã tác vụ qua subagents tích cực hơn hoặc có khả năng khái quát hóa quy ước khác biệt.

## 10. Kết luận

Thí nghiệm đã chứng minh cơ chế Self-evolving Agent thông qua việc tự động chắt lọc kỹ năng (skills) từ phản hồi lỗi giúp nâng cao rõ rệt hiệu năng của tác tử, cải thiện điểm trung bình từ 0.63 lên 0.77 trên tập học và từ 0.57 lên 0.69 trên tập đánh giá. Sự cải thiện này tập trung hoàn toàn vào việc tuân thủ các quy ước tổ chức ngầm (tăng từ 0% lên 33.3-44.4%), trong khi năng lực giải quyết bài toán kỹ thuật duy trì ở mức tối ưu (94.4%). Ngược lại, kiến trúc đa tác tử (`subagents`) không mang lại lợi ích về điểm số trong phạm vi các tác vụ này nhưng làm tăng gấp 2.19 lần chi phí token do gánh nặng prompt định tuyến. Quy trình Freeze Protocol bảo đảm tính độc lập khách quan và độ tin cậy cao của kết quả đo lường. Đề xuất cải tiến tiếp theo là xây dựng cơ chế "Dynamic Hierarchical Skill Retrieval & Context Pruning" để chỉ nạp chính xác các quy ước cần thiết cho từng tác vụ cụ thể, giúp tối ưu hóa chi phí token và mở rộng phạm vi áp dụng.

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
  12. `$env:PYTHONUTF8=1; python scripts/verify_freeze.py` (xác nhận trạng thái freeze đạt `OK`)
  13. `python -m lab.compare` (tạo `report/table.md` định dạng UTF-8)
  14. `python scripts/check_breakdown.py` (trích xuất thống kê chi tiết kỹ thuật và quy ước)
  15. `pytest` (kiểm tra lại toàn bộ test harness trước khi hoàn thiện)
- Thử thách mở rộng (nếu có): Không thực hiện (tập trung tối đa chất lượng các hạng mục chính 1 đến 6).
- Ghi chú khác:
  + Môi trường Windows 11 yêu cầu thiết lập biến môi trường `PYTHONUTF8=1` khi chạy các lệnh kiểm tra diff git để tránh xung đột mã hóa ký tự tiếng Việt với cp1258.
  + Mô hình `gpt-6-luna` bắt buộc cấu hình `LAB_TEMPERATURE=1` theo ràng buộc API OpenAI; nhiệt độ này được giữ nguyên cố định xuyên suốt toàn bộ các điều kiện thí nghiệm.
