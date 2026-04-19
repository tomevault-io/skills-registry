---
name: openspec-apply-change
description: Thực thi các task từ một thay đổi OpenSpec. Sử dụng khi người dùng muốn bắt đầu thực hiện, tiếp tục thực hiện, hoặc hoàn thành các task. Use when this capability is needed.
metadata:
  author: hongquandev
---

Thực thi các task từ một thay đổi OpenSpec.

**Đầu vào**: Tùy chọn chỉ định tên thay đổi. Nếu bỏ trống, hãy kiểm tra xem có thể suy luận từ ngữ cảnh cuộc hội thoại hay không. Nếu mơ hồ hoặc không rõ ràng, bạn PHẢI yêu cầu xem danh sách các thay đổi có sẵn.

**Các bước**

1. **Chọn thay đổi**

   Nếu tên được cung cấp, hãy sử dụng nó. Nếu không:
   - Suy luận từ ngữ cảnh hội thoại nếu người dùng đã đề cập đến một thay đổi.
   - Tự động chọn nếu chỉ có một thay đổi đang hoạt động (active change).
   - Nếu mơ hồ, chạy lệnh `openspec list --json` để lấy danh sách các thay đổi có sẵn và sử dụng công cụ **AskUserQuestion** để người dùng chọn.

   Luôn thông báo: "Đang sử dụng thay đổi: <name>" và cách để ghi đè (ví dụ: `/opsx:apply <tên-khác>`).

2. **Kiểm tra trạng thái (status) để hiểu schema**
   ```bash
   openspec status --change "<name>" --json
   ```
   Phân tích JSON để hiểu:
   - `schemaName`: Workflow đang được sử dụng (ví dụ: "spec-driven").
   - Artifact nào chứa các task (thường là "tasks" đối với spec-driven, kiểm tra status cho các loại khác).

3. **Lấy hướng dẫn thực thi (apply instructions)**

   ```bash
   openspec instructions apply --change "<name>" --json
   ```

   Kết quả trả về bao gồm:
   - Đường dẫn các file context (thay đổi tùy theo schema - có thể là proposal/specs/design/tasks hoặc spec/tests/implementation/docs).
   - Tiến độ (tổng cộng, hoàn thành, còn lại).
   - Danh sách task kèm theo trạng thái.
   - Hướng dẫn động dựa trên trạng thái hiện tại.

   **Xử lý các trạng thái:**
   - Nếu `state: "blocked"` (thiếu artifact): hiển thị thông báo, đề xuất sử dụng `openspec-continue-change`.
   - Nếu `state: "all_done"`: chúc mừng và đề xuất lưu trữ (archive).
   - Ngược lại: tiến hành triển khai (implementation).

4. **Đọc các file context**

   Đọc các file được liệt kê trong `contextFiles` từ kết quả của hướng dẫn thực thi.
   Các file này phụ thuộc vào schema đang sử dụng:
   - **spec-driven**: proposal, specs, design, tasks.
   - Các schema khác: làm theo `contextFiles` từ đầu ra của CLI.

5. **Hiển thị tiến độ hiện tại**

   Hiển thị:
   - Schema đang được sử dụng.
   - Tiến độ: "Hoàn thành N/M task".
   - Tổng quan các task còn lại.
   - Hướng dẫn động từ CLI.

6. **Thực thi các task (lặp lại cho đến khi hoàn thành hoặc bị chặn)**

   Với mỗi task đang chờ (pending task):
   - Hiển thị task đang được thực hiện.
   - Thực hiện các thay đổi code cần thiết.
   - Giữ các thay đổi ở mức tối giản và tập trung.
   - Đánh dấu task đã hoàn thành trong file tasks: `- [ ]` → `- [x]`.
   - Tiếp tục sang task tiếp theo.

   **Tạm dừng nếu:**
   - Task không rõ ràng → yêu cầu làm rõ.
   - Quá trình implementation phát hiện vấn đề về thiết kế (design issue) → đề xuất cập nhật các artifact.
   - Gặp lỗi hoặc vật cản (blocker) → báo cáo và chờ chỉ dẫn.
   - Người dùng ngắt quãng.

7. **Khi hoàn thành hoặc tạm dừng, hiển thị trạng thái**

   Hiển thị:
   - Các task đã hoàn thành trong phiên (session) này.
   - Tiến độ tổng thể: "Hoàn thành N/M task".
   - Nếu đã hoàn thành tất cả: đề xuất lưu trữ (archive).
   - Nếu tạm dừng: giải thích lý do và chờ chỉ dẫn.

**Đầu ra trong quá trình Implementation**

```
## Đang thực thi: <change-name> (schema: <schema-name>)

Đang xử lý task 3/7: <mô tả task>
[...quá trình implementation đang diễn ra...]
✓ Task hoàn thành

Đang xử lý task 4/7: <mô tả task>
[...quá trình implementation đang diễn ra...]
✓ Task hoàn thành
```

**Đầu ra khi hoàn thành**

```
## Hoàn tất Implementation

**Thay đổi:** <change-name>
**Schema:** <schema-name>
**Tiến độ:** Hoàn thành 7/7 task ✓

### Đã hoàn thành trong phiên này
- [x] Task 1
- [x] Task 2
...

Tất cả task đã hoàn thành! Sẵn sàng để lưu trữ (archive) thay đổi này.
```

**Đầu ra khi tạm dừng (Gặp vấn đề)**

```
## Tạm dừng Implementation

**Thay đổi:** <change-name>
**Schema:** <schema-name>
**Tiến độ:** Hoàn thành 4/7 task

### Vấn đề gặp phải
<mô tả vấn đề>

**Các lựa chọn:**
1. <lựa chọn 1>
2. <lựa chọn 2>
3. Cách tiếp cận khác

Bạn muốn thực hiện điều gì tiếp theo?
```

**Nguyên tắc an toàn (Guardrails)**
- Tiếp tục thực hiện các task cho đến khi hoàn thành hoặc bị chặn.
- Luôn đọc các file context trước khi bắt đầu (từ kết quả instructions apply).
- Nếu task mơ hồ, hãy tạm dừng và hỏi trước khi thực thi.
- Nếu việc implementation phát hiện ra vấn đề, hãy tạm dừng và đề xuất cập nhật artifact.
- Giữ các thay đổi code tối giản và nằm trong phạm vi của từng task.
- Cập nhật checkbox của task ngay sau khi hoàn thành mỗi task.
- Tạm dừng khi gặp lỗi, vật cản hoặc yêu cầu không rõ ràng - không được đoán.
- Sử dụng `contextFiles` từ đầu ra CLI, không giả định tên file cụ thể.

**Tích hợp Workflow linh hoạt**

Skill này hỗ trợ mô hình "thực hiện các hành động trên một thay đổi":

- **Có thể gọi bất kỳ lúc nào**: Trước khi tất cả artifact hoàn thành (nếu đã có task), sau khi thực thi một phần, hoặc xen kẽ với các hành động khác.
- **Cho phép cập nhật artifact**: Nếu quá trình implementation phát hiện vấn đề thiết kế, hãy đề xuất cập nhật các artifact - không bị khóa cứng theo giai đoạn, làm việc linh hoạt.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hongquandev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
