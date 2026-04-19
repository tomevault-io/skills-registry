---
name: openspec-onboard
description: Hướng dẫn Onboarding cho OpenSpec - đi qua một chu kỳ workflow hoàn chỉnh kèm lời dẫn dắt và làm việc thực tế với codebase. Use when this capability is needed.
metadata:
  author: hongquandev
---

Hướng dẫn người dùng vượt qua chu kỳ workflow OpenSpec hoàn chỉnh đầu tiên của họ. Đây là một trải nghiệm học tập—bạn sẽ làm việc thực tế trên codebase của họ đồng thời giải thích từng bước.

---

## Kiểm tra trước khi bắt đầu (Preflight)

Trước khi bắt đầu, hãy kiểm tra xem OpenSpec đã được khởi tạo chưa:

```bash
openspec status --json 2>&1 || echo "NOT_INITIALIZED"
```

**Nếu chưa khởi tạo:**
> OpenSpec chưa được thiết lập trong dự án này. Hãy chạy lệnh `openspec init` trước, sau đó quay lại lệnh `/opsx:onboard`.

Dừng lại tại đây nếu chưa khởi tạo.

---

## Giai đoạn 1: Chào mừng

Hiển thị:

```
## Chào mừng bạn đến với OpenSpec!

Tôi sẽ hướng dẫn bạn trải qua một chu kỳ thay đổi hoàn chỉnh—từ ý tưởng đến khi triển khai—bằng cách thực hiện một task thực tế trong codebase của bạn. Qua đó, bạn sẽ học được workflow thông qua việc thực hành trực tiếp.

**Những gì chúng ta sẽ làm:**
1. Chọn một task nhỏ, thực tế trong codebase của bạn.
2. Khám phá sơ lược về vấn đề.
3. Tạo một thay đổi (container chứa công việc của chúng ta).
4. Xây dựng các artifact: proposal → specs → design → tasks.
5. Triển khai (implement) các task.
6. Lưu trữ (archive) thay đổi đã hoàn thành.

**Thời gian dự kiến:** ~15-20 phút

Hãy bắt đầu bằng việc tìm một công việc để thực hiện nhé.
```

---

## Giai đoạn 2: Chọn Task

### Phân tích Codebase

Quét codebase để tìm các cơ hội cải thiện nhỏ. Hãy tìm:

1. **Các comment TODO/FIXME** - Tìm tìm `TODO`, `FIXME`, `HACK`, `XXX` trong các file code.
2. **Thiếu xử lý lỗi** - Các khối `catch` bị bỏ trống, các thao tác mạo hiểm mà không có try-catch.
3. **Các hàm chưa có test** - Đối chiếu thư mục `src/` với các thư mục test.
4. **Các vấn đề về kiểu dữ liệu (Type issues)** - Các kiểu `any` trong file TypeScript (`: any`, `as any`).
5. **Các tàn dư khi debug** - Các câu lệnh `console.log`, `console.debug`, `debugger` trong code sản phẩm.
6. **Thiếu tính năng xác thực (validation)** - Các trình xử lý đầu vào người dùng mà không có xác thực dữ liệu.

Đồng thời kiểm tra các hoạt động git gần đây:
```bash
git log --oneline -10 2>/dev/null || echo "Không có lịch sử git"
```

### Đưa ra gợi ý

Từ phân tích của bạn, hãy đưa ra 3-4 gợi ý cụ thể:

```
## Gợi ý các Task

Dựa trên việc quét codebase, đây là một số task phù hợp để bắt đầu:

**1. [Task hứa hẹn nhất]**
   Vị trí: `src/path/to/file.ts:42`
   Phạm vi: ~1-2 file, ~20-30 dòng code
   Lý do chọn: [lý do ngắn gọn]

**2. [Task thứ hai]**
   Vị trí: `src/another/file.ts`
   Phạm vi: ~1 file, ~15 dòng code
   Lý do chọn: [lý do ngắn gọn]

**3. [Task thứ ba]**
   Vị trí: [vị trí]
   Phạm vi: [ước tính]
   Lý do chọn: [lý do ngắn gọn]

**4. Một task khác?**
   Hãy cho tôi biết bạn muốn thực hiện điều gì.

Bạn quan tâm đến task nào? (Hãy chọn một con số hoặc mô tả task của riêng bạn)
```

**Nếu không tìm thấy gì:** Quay lại hỏi người dùng họ muốn xây dựng điều gì:
> Tôi không tìm thấy điểm cải thiện nhanh chóng nào rõ ràng trong codebase của bạn. Có việc gì nhỏ mà bạn đang định thêm vào hoặc sửa chữa không?

### Ràng buộc về Phạm vi (Scope Guardrail)

Nếu người dùng chọn hoặc mô tả điều gì đó quá lớn (tính năng lớn, công việc kéo dài nhiều ngày):

```
Đó là một task có giá trị, nhưng nó có vẻ hơi lớn so với mục tiêu làm quen với OpenSpec lần đầu.

Để học workflow, task càng nhỏ càng tốt—nó giúp bạn thấy được toàn bộ chu kỳ mà không bị sa lầy vào các chi tiết triển khai phức tạp.

**Các lựa chọn:**
1. **Chia nhỏ nó ra** - Phần nhỏ nhất và hữu ích nhất của [task của họ] là gì? Có thể chỉ là [phần cụ thể]?
2. **Chọn task khác** - Một trong những gợi ý khác, hoặc một task nhỏ khác?
3. **Vẫn thực hiện nó** - Nếu bạn thực sự muốn làm, chúng ta sẽ làm. Nhưng hãy lưu ý là nó sẽ mất nhiều thời gian hơn.

Bạn muốn chọn phương án nào?
```

Hãy để người dùng quyết định nếu họ nhấn mạnh—đây chỉ là một rào cản mềm.

---

## Giai đoạn 3: Demo tính năng Explore

Khi một task đã được chọn, hãy demo ngắn gọn chế độ explore:

```
Trước khi tạo một thay đổi, hãy để tôi giới thiệu nhanh về **chế độ explore**—đây là cách bạn suy nghĩ thấu đáo về các vấn đề trước khi cam kết chọn một hướng đi.
```

Dành 1-2 phút điều tra code liên quan:
- Đọc các file liên quan.
- Vẽ một sơ đồ ASCII nhanh nếu nó giúp ích.
- Ghi lại bất kỳ lưu ý nào.

```
## Khám phá nhanh

[Phân tích ngắn gọn của bạn—những gì bạn tìm thấy, các điểm cần lưu ý]

┌─────────────────────────────────────────┐
│ [Tùy chọn: Sơ đồ ASCII nếu cần thiết]   │
└─────────────────────────────────────────┘

Chế độ Explore (`/opsx:explore`) dùng cho kiểu tư duy này—điều tra trước khi triển khai. Bạn có thể sử dụng nó bất kỳ lúc nào cần suy nghĩ kỹ về một vấn đề.

Bây giờ chúng ta hãy tạo một thay đổi (change) để lưu giữ công việc của mình.
```

**TẠM DỪNG** - Chờ người dùng xác nhận trước khi tiếp tục.

---

## Giai đoạn 4: Tạo Thay đổi (Change)

**GIẢI THÍCH:**
```
## Tạo một Thay đổi (Change)

Một "thay đổi" trong OpenSpec là một container chứa tất cả các tư duy và kế hoạch xung quanh một phần công việc. Nó nằm ở `openspec/changes/<tên>/` và chứa các artifact của bạn—proposal, specs, design, tasks.

Hãy để tôi tạo một cái cho task của chúng ta.
```

**THỰC HIỆN:** Tạo thay đổi với tên định dạng kebab-case suy ra từ task:
```bash
openspec new change "<derived-name>"
```

**HIỂN THỊ:**
```
Đã tạo: `openspec/changes/<tên>/`

Cấu trúc thư mục:
```
openspec/changes/<tên>/
├── proposal.md    ← Tại sao chúng ta làm việc này (đang trống, chúng ta sẽ điền vào)
├── design.md      ← Chúng ta sẽ xây dựng nó như thế nào (đang trống)
├── specs/         ← Các yêu cầu chi tiết (đang trống)
└── tasks.md       ← Checklist triển khai (đang trống)
```

Bây giờ hãy điền vào artifact đầu tiên—bản đề xuất (proposal).
```

---

## Giai đoạn 5: Bản đề xuất (Proposal)

**GIẢI THÍCH:**
```
## Bản đề xuất (Proposal)

Bản đề xuất ghi lại **lý do tại sao** chúng ta thực hiện thay đổi này và nó bao gồm những gì ở mức độ tổng quát. Nó giống như một bản giới thiệu ngắn gọn (elevator pitch) cho công việc.

Tôi sẽ phác thảo một bản dựa trên task của chúng ta.
```

**THỰC HIỆN:** Phác thảo nội dung proposal (chưa lưu vội):

```
Đây là bản phác thảo proposal:

---

## Tại sao (Why)

[1-2 câu giải thích vấn đề/cơ hội]

## Những gì thay đổi (What Changes)

[Các gạch đầu dòng về những gì sẽ khác biệt]

## Các năng lực (Capabilities)

### Năng lực mới
- `<tên-capability>`: [mô tả ngắn]

### Năng lực được sửa đổi
<!-- Nếu sửa đổi hành vi hiện có -->

## Tác động (Impact)

- `src/path/to/file.ts`: [những gì thay đổi]
- [các file khác nếu có]

---

Bản phác thảo này đã nắm bắt đúng ý định chưa? Tôi có thể điều chỉnh trước khi chúng ta lưu nó.
```

**TẠM DỪNG** - Chờ người dùng phê duyệt/phản hồi.

Sau khi được phê duyệt, lưu proposal:
```bash
openspec instructions proposal --change "<tên>" --json
```
Sau đó ghi nội dung vào `openspec/changes/<tên>/proposal.md`.

```
Đã lưu Proposal. Đây là tài liệu "tại sao" của bạn—bạn luôn có thể quay lại và tinh chỉnh nó khi hiểu biết về vấn đề phát triển hơn.

Tiếp theo là: specs.
```

---

## Giai đoạn 6: Specs

**GIẢI THÍCH:**
```
## Specs

Specs định nghĩa **những gì** chúng ta đang xây dựng thông qua các điều khoản chính xác, có thể kiểm chứng được. Chúng sử dụng định dạng yêu cầu/kịch bản giúp hành vi dự kiến trở nên rõ ràng như pha lê.

Đối với một task nhỏ như thế này, chúng ta có thể chỉ cần một file spec.
```

**THỰC HIỆN:** Tạo file spec:
```bash
mkdir -p openspec/changes/<tên>/specs/<tên-capability>
```

Phác thảo nội dung spec:

```
Đây là spec:

---

## Các yêu cầu ĐƯỢC THÊM (ADDED Requirements)

### Yêu cầu: <Tên>

<Mô tả về những gì hệ thống nên thực hiện>

#### Kịch bản (Scenario): <Tên kịch bản>

- **KHI (WHEN)** <điều kiện kích hoạt>
- **THÌ (THEN)** <kết quả dự kiến>
- **VÀ (AND)** <kết quả bổ sung nếu cần>

---

Định dạng này—KHI/THÌ/VÀ—làm cho các yêu cầu có thể kiểm thử được. Bạn có thể đọc chúng như là các test case.
```

Lưu vào `openspec/changes/<tên>/specs/<capability>/spec.md`.

---

## Giai đoạn 7: Thiết kế (Design)

**GIẢI THÍCH:**
```
## Thiết kế (Design)

Bản thiết kế ghi lại cách chúng ta sẽ xây dựng nó—các quyết định kỹ thuật, sự đánh đổi, phương pháp tiếp cận.

Đối với các thay đổi nhỏ, phần này có thể ngắn gọn. Điều đó hoàn toàn ổn—không phải thay đổi nào cũng cần thảo luận thiết kế sâu sắc.
```

**THỰC HIỆN:** Phác thảo design.md:

```
Đây là bản thiết kế:

---

## Ngữ cảnh (Context)

[Bối cảnh ngắn gọn về trạng thái hiện tại]

## Mục tiêu / Những gì không nằm trong mục tiêu (Goals / Non-Goals)

**Mục tiêu:**
- [Những gì chúng ta đang cố gắng đạt được]

**Không nằm trong mục tiêu:**
- [Những gì rõ ràng nằm ngoài phạm vi]

## Các quyết định (Decisions)

### Quyết định 1: [Quyết định then chốt]

[Giải thích về phương pháp tiếp cận và lý do]

---

Đối với một task nhỏ, bản thiết kế này đã nắm bắt được các quyết định quan trọng mà không cần quá cầu kỳ.
```

Lưu vào `openspec/changes/<tên>/design.md`.

---

## Giai đoạn 8: Các Task

**GIẢI THÍCH:**
```
## Các Task

Cuối cùng, chúng ta chia công việc thành các task triển khai—các checkbox sẽ dẫn dắt giai đoạn thực thi (apply).

Các task này nên nhỏ, rõ ràng và theo thứ tự logic.
```

**THỰC HIỆN:** Tạo các task dựa trên specs và design:

```
Đây là các task triển khai:

---

## 1. [Danh mục hoặc file]

- [ ] 1.1 [Task cụ thể]
- [ ] 1.2 [Task cụ thể]

## 2. Xác minh (Verify)

- [ ] 2.1 [Bước xác minh]

---

Mỗi checkbox sẽ trở thành một đơn vị công việc trong giai đoạn triển khai. Bạn đã sẵn sàng để thực hiện chưa?
```

**TẠM DỪNG** - Chờ người dùng xác nhận đã sẵn sàng triển khai.

Lưu vào `openspec/changes/<tên>/tasks.md`.

---

## Giai đoạn 9: Triển khai (Apply/Implementation)

**GIẢI THÍCH:**
```
## Triển khai

Bây giờ chúng ta sẽ thực hiện từng task, đánh dấu hoàn thành khi xong. Tôi sẽ thông báo về từng task và thỉnh thoảng ghi chú xem các spec/design đã ảnh hưởng đến cách tiếp cận như thế nào.
```

**THỰC HIỆN:** Với mỗi task:

1. Thông báo: "Đang làm việc trên task N: [mô tả]"
2. Thực hiện thay đổi trong codebase.
3. Tham chiếu specs/design một cách tự nhiên: "Spec yêu cầu X, nên tôi đang thực hiện Y"
4. Đánh dấu hoàn thành trong `tasks.md`: `- [ ]` → `- [x]`
5. Trạng thái ngắn gọn: "✓ Task N đã hoàn thành"

Hãy diễn giải một cách nhẹ nhàng—đừng giải thích quá đà từng dòng code.

Sau tất cả các task:

```
## Hoàn tất Triển khai

Tất cả các task đã xong:
- [x] Task 1
- [x] Task 2
- [x] ...

Thay đổi đã được triển khai! Thêm một bước cuối cùng nữa—hãy lưu trữ (archive) nó.
```

---

## Giai đoạn 10: Lưu trữ (Archive)

**GIẢI THÍCH:**
```
## Lưu trữ (Archiving)

Khi một thay đổi hoàn thành, chúng ta sẽ lưu trữ nó. Thao tác này sẽ di chuyển nó từ `openspec/changes/` vào `openspec/changes/archive/YYYY-MM-DD-<tên>/`.

Các thay đổi được lưu trữ sẽ trở thành lịch sử quyết định của dự án—bạn luôn có thể tìm lại chúng sau này để hiểu tại sao một thứ gì đó được xây dựng theo một cách nhất định.
```

**THỰC HIỆN:**
```bash
openspec archive "<tên>"
```

**HIỂN THỊ:**
```
Đã lưu trữ vào: `openspec/changes/archive/YYYY-MM-DD-<tên>/`

Thay đổi này hiện đã là một phần trong lịch sử dự án của bạn. Code đã nằm trong codebase, và bản ghi quyết định đã được bảo tồn.
```

---

## Giai đoạn 11: Tóm tắt & Các bước tiếp theo

```
## Chúc mừng bạn!

Bạn vừa hoàn thành một chu kỳ OpenSpec đầy đủ:

1. **Khám phá (Explore)** - Suy nghĩ thấu đáo về vấn đề.
2. **Mới (New)** - Tạo một container chứa thay đổi.
3. **Đề xuất (Proposal)** - Ghi lại LÝ DO (WHY).
4. **Specs** - Định nghĩa CÁI GÌ (WHAT) một cách chi tiết.
5. **Thiết kế (Design)** - Quyết định thực hiện NHƯ THẾ NÀO (HOW).
6. **Tasks** - Chia nhỏ thành các bước.
7. **Triển khai (Apply)** - Thực hiện công việc.
8. **Lưu trữ (Archive)** - Bảo tồn bản ghi.

Nhịp độ làm việc này phù hợp cho bất kỳ quy mô thay đổi nào—từ sửa lỗi nhỏ đến tính năng lớn.

---

## Tham khảo Lệnh

| Lệnh | Ý nghĩa |
|---------|--------------|
| `/opsx:explore` | Suy nghĩ thấu đáo vấn đề trước/trong khi làm việc |
| `/opsx:new` | Bắt đầu một thay đổi mới, thực hiện từng artifact |
| `/opsx:ff` | Chuyển nhanh: tạo tất cả các artifact cùng một lúc |
| `/opsx:continue` | Tiếp tục làm việc trên một thay đổi hiện có |
| `/opsx:apply` | Triển khai các task của một thay đổi |
| `/opsx:verify` | Xác minh việc triển khai khớp với các artifact |
| `/opsx:archive` | Lưu trữ một thay đổi đã hoàn thành |

---

## Tiếp theo là gì?

Hãy thử `/opsx:new` hoặc `/opsx:ff` cho điều gì đó mà bạn thực sự muốn xây dựng. Giờ bạn đã nắm vững nhịp độ làm việc rồi đấy!
```

---

## Xử lý việc thoát sớm

### Người dùng muốn dừng giữa chừng

Nếu người dùng nói họ cần dừng lại, muốn tạm nghỉ hoặc có vẻ không còn hứng thú:

```
Không vấn đề gì! Thay đổi của bạn đã được lưu tại `openspec/changes/<tên>/`.

Để tiếp tục từ nơi chúng ta đã dừng lại sau này:
- `/opsx:continue <tên>` - Tiếp tục tạo artifact.
- `/opsx:apply <tên>` - Chuyển ngay đến triển khai (nếu task đã tồn tại).

Công việc của bạn sẽ không bị mất. Hãy quay lại bất cứ khi nào bạn sẵn sàng.
```

Thoát một cách lịch sự, không gây áp lực.

### Người dùng chỉ muốn xem tham khảo lệnh

Nếu người dùng nói họ chỉ muốn xem danh sách các lệnh hoặc bỏ qua phần hướng dẫn:

```
## Tài liệu tham khảo nhanh OpenSpec

| Lệnh | Ý nghĩa |
|---------|--------------|
| `/opsx:explore` | Suy nghĩ vấn đề (không thay đổi code) |
| `/opsx:new <tên>` | Bắt đầu thay đổi mới, từng bước một |
| `/opsx:ff <tên>` | Chuyển nhanh: tất cả artifact cùng một lúc |
| `/opsx:continue <tên>` | Tiếp tục một thay đổi hiện có |
| `/opsx:apply <tên>` | Triển khai các task |
| `/opsx:verify <tên>` | Xác minh việc triển khai |
| `/opsx:archive <tên>` | Lưu trữ khi hoàn thành |

Hãy thử `/opsx:new` để bắt đầu thay đổi đầu tiên của bạn, hoặc `/opsx:ff` nếu bạn muốn làm việc nhanh.
```

Thoát một cách lịch sự.

---

## Nguyên tắc an toàn (Guardrails)

- **Tuân theo mô hình GIẢI THÍCH → THỰC HIỆN → HIỂN THỊ → TẠM DỪNG** tại các lần chuyển đổi quan trọng (sau explore, sau bản thảo proposal, sau tasks, sau archive).
- **Dẫn dắt nhẹ nhàng** trong quá trình triển khai—hướng dẫn mà không mang tính giảng giải.
- **Không bỏ qua các giai đoạn** ngay cả khi thay đổi nhỏ—mục tiêu là dạy về workflow.
- **Tạm dừng để xác nhận** tại các điểm đã đánh dấu, nhưng đừng tạm dừng quá lâu.
- **Xử lý việc thoát một cách lịch sự**—không bao giờ ép buộc người dùng tiếp tục.
- **Sử dụng các task thực tế từ codebase**—không mô phỏng hoặc sử dụng ví dụ giả.
- **Điều chỉnh phạm vi một cách khéo léo**—hướng dẫn người dùng chọn các task nhỏ hơn nhưng vẫn tôn trọng lựa chọn của họ.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hongquandev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
