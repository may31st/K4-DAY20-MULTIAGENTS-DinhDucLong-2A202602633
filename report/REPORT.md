# Báo cáo Lab: Self evolving Agentic

## 1. Thông tin nhóm và cấu hình

| Họ tên | Mã sinh viên | Phần đóng góp |
|---|---|---|
| Đinh Đức Long | 2A202602633 | 100% |

- Mô hình: `gpt-6-luna` (OpenAI provider, cấu hình qua `LAB_MODEL=openai:gpt-6-luna`), nhiệt độ (`LAB_TEMPERATURE=1`), `recursion_limit=60`
- Phiên bản Deep Agents: `deepagents 0.7.21`, hệ điều hành Windows 11, môi trường Python 3.12 venv
- Số lần chạy tác vụ đã dùng / ngân sách: 9 / 30
- Commit của tag `freeze`: (cập nhật sau khi tạo tag freeze)

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

> Dán nội dung `report/table.md` và kết quả `python scripts/check_breakdown.py`. Nêu các lần chạy có `error` hoặc `skills_modified = true` (nếu có) và cách xử lý.

```text
(dán bảng ở đây)
```

## 8. Phân tích

> Trả lời từng câu bằng số liệu từ mục 7 và bằng chứng từ vết. Kết quả âm hoặc không có khác biệt vẫn hợp lệ nếu được phân tích tốt.

1. So với `baseline`, điều kiện nào cải thiện điểm tác vụ **học**? Điều kiện nào cải thiện điểm tác vụ **đánh giá**? Có điều kiện nào cải thiện tác vụ học nhưng không cải thiện tác vụ đánh giá? Nếu có, đó là dấu hiệu gì?
2. Tách điểm thành check kỹ thuật và check quy ước (`rule_`). Skill do curator sinh giúp nhóm check nào? Check quy ước **mới** của tác vụ đánh giá có được skill giúp không, và vì sao?
3. Dựa vào vết và `skills_read`, giải thích một check mà skill giúp đạt và một check mà skill không giúp (skill chưa được đọc, đọc nhưng không làm theo, skill thiếu hoặc sai).
4. Chi phí: so sánh số token trung bình giữa các điều kiện. Điều kiện nào có hiệu quả tốt nhất theo điểm trên mỗi token? Đa tác tử có đáng chi phí trong thí nghiệm này không?
5. Có dấu hiệu rò rỉ dữ liệu hoặc quá khớp nào trong skill sinh ra không? Nhóm đã phòng tránh như thế nào?
6. Nhiễu: so sánh điểm tác vụ học của cùng bộ skill ở Phần 3.4 (đã sao lưu) và sau đóng băng. Chênh lệch bao nhiêu? Nó cho biết điều gì về độ tin cậy của các chênh lệch trong bảng ở mục 7?

## 9. Hạn chế và tính hợp lệ

> Nêu ít nhất 3 hạn chế và ảnh hưởng của từng hạn chế đến kết luận (ví dụ: chỉ 3 tác vụ mỗi vai trò, mỗi cấu hình chạy một lần, nhiễu của mô hình, tác vụ do giảng viên thiết kế sẵn quy ước, chỉ một mô hình).

1.
2.
3.

## 10. Kết luận

> Tối đa 5 câu. Chỉ khẳng định điều số liệu hỗ trợ. Nêu một đề xuất cải tiến tiếp theo.

## Phụ lục

- Lệnh đã chạy (theo thứ tự):
- Thử thách mở rộng (nếu có): hướng chọn, kết quả, nhận xét.
- Ghi chú khác:
