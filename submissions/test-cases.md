# Test Cases — Bảng trường hợp kiểm thử

> **Hướng dẫn**: Viết tối thiểu **20 TC** phủ đủ các chức năng chính (REQ-01 → REQ-08).
> Xem [examples/sample-test-case.md](../examples/sample-test-case.md) để hiểu cách viết TC tốt.
> Tự tổ chức và phân nhóm test case theo cách hợp lý nhất.

| Thông tin | |
|---|---|
| **Nhóm** | `group 2` |
| **Ngày tạo** | `27/05/2026` |
| **Hệ thống** | https://stqa.rbc.vn |
| **Tham chiếu** | SRS v1.0 |

---

## Bước 1: Mô hình hóa miền đầu vào — Input Domain Modeling (IDM)

> 📖 **Textbook:** Chương 6 — *Input Domain Modeling*, Paul Ammann & Jeff Offutt.
>
> **Trước khi viết Test Case**, nhóm **phải** phân tích miền đầu vào bằng bảng IDM bên dưới.
> Mỗi chức năng cần xác định: **Đặc tính (Characteristic)**, **Phân vùng (Block/Partition)**, và **Giá trị đại diện (Value)**.

### IDM — Đăng nhập (REQ-01)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Email có tồn tại trong DB? | Có | `librarian@library.com` | Đăng nhập thành công |
| | Không | `noone@email.com` | Thông báo lỗi |
| Mật khẩu có đúng? | Đúng | `admin123` | Đăng nhập thành công |
| | Sai | `wrongpass` | Thông báo lỗi |
| Ô nhập có rỗng? | Không rỗng | (giá trị bất kỳ) | Xử lý bình thường |
| | Rỗng | `""` | Thông báo "Vui lòng nhập..." |

### IDM — Tìm kiếm sách (REQ-03)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Từ khóa có tồn tại trong DB? | Có (tên sách) | `"Flutter"` | Hiển thị sách chứa "Flutter" |
| | Có (tên tác giả) | `"Nguyễn"` | Hiển thị sách của tác giả Nguyễn |
| | Không | `"XYZ123"` | Danh sách rỗng |
| Phân biệt HOA/thường? | Chữ thường | `"flutter"` | Kết quả giống "Flutter" |
| | Chữ HOA | `"FLUTTER"` | Kết quả giống "Flutter" |

### IDM — Mượn sách (REQ-04, REQ-05)

| Đặc tính (Characteristic) | Phân vùng (Block) | Giá trị đại diện (Value) | Kết quả mong đợi |
|---|---|---|---|
| Trạng thái sách? | Có sẵn | BOOK001 | Cho phép mượn |
| | Đang mượn | BOOK003 | Không cho phép |
| | Thất lạc | BOOK007 | Không cho phép |
| Trạng thái thành viên? | Hoạt động | MEM002 | Cho phép mượn |
| | Tạm ngưng | MEM004 | Từ chối, thông báo lỗi |
| | Hết hạn | MEM005 | Từ chối, thông báo lỗi |
| Số sách đang mượn? | < 3 (BVA: 0, 1, 2) | MEM006 (0 sách) | Cho phép mượn |
| | = 3 (BVA: giới hạn) | MEM đã mượn 3 sách | Từ chối, thông báo vượt giới hạn |

### IDM — Check Overdue (REQ-06)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
| -------------- | ----------------- | -------------------- | --------------- |
| Due date compared to today? | Before today | BR001  | Flagged as "Overdue" |
| | Equal to today  | New record with dueDate = today | Must be flagged as "Overdue" |
| | After today  | BR003  | Not flagged, stays "Borrowing" |
| Current record status? | Borrowing | BR001 | Can be flagged |
| | Returned | BR002 | Status unchanged |
| Who performs the action? | Librarian  | librarian@library.com | Action available |
| | Member  | ba.nguyen@email.com | Button not visible |

### IDM — Member Management (REQ-07)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
| -------------- | ----------------- | -------------------- | --------------- |
| Full name valid? | Has content | `Nguyen Van Test` | Created successfully |
| | Empty  | `""` | Rejected with error |
| Email format valid? | Valid  | `test@email.com` | Created successfully |
| | Missing @ | `testemail.com` | Rejected — invalid email |
| | Has @ but missing . in domain | `test@email` | Rejected — invalid email |
| | Empty  | `""` | Rejected with error |
| Email unique? | Not yet in system | `new.member@email.com` | Created successfully |
| | Already exists in system | `ba.nguyen@email.com` | Rejected — duplicate email |
| Who performs the action? | Librarian  | librarian@library.com | Action available |
| | Member  | ba.nguyen@email.com | Tab not visible |

### IDM — Borrow Records (REQ-08)

| Characteristic | Block (Partition) | Representative Value | Expected Result |
| -------------- | ----------------- | -------------------- | --------------- |
| Viewer's role? | Librarian | librarian@library.com | Can view all records |
| | Member | ba.nguyen@email.com | Can only view own records |
| Whose record is it? | Own record | ba.nguyen views BR001 | Displayed correctly |
| | Another member's record | ba.nguyen views BR002 (dam.tran) | Not displayed |
| Record status displayed? | Borrowing | BR001, BR003 | Shows "Borrowing" |
| | Returned | BR002, BR004, BR005 | Shows "Returned" |
| | Overdue | BR001 after overdue check | Shows "Overdue" |


> 💡 **Gợi ý kỹ thuật**: Sử dụng **Phân lớp tương đương (EP)** cho các phân vùng rời rạc, **Phân tích giá trị biên (BVA)** cho các phân vùng số (ví dụ: giới hạn 3 sách). Xem textbook §6.1–6.3.

---

## Bước 2: Test Cases

<!-- Tự tổ chức bảng test case: có thể chia nhóm theo chức năng, theo REQ, hoặc theo luồng nghiệp vụ — tùy nhóm quyết định. -->
<!-- Mỗi TC phải ánh xạ ngược về ít nhất 1 dòng trong bảng IDM ở Bước 1. -->

| Mã TC | Mục tiêu kiểm thử | Tiền điều kiện | Bước thực hiện | Dữ liệu đầu vào | Kết quả mong đợi | REQ | Kỹ thuật |
|-------|-------------------|---------------|---------------|-----------------|------------------|-----|---------|
| TC-01 | Đăng nhập thành công — Thủ thư| Đã mở stqa.rbc.vn, chưa đăng nhập | 1. Nhập email. 2. Nhập mật khẩu. 3. Nhấn Đăng nhập. | Email: librarian@library.com / PW: admin123 | Chuyển sang trang chính. AppBar hiển thị tên "Thủ thư" + vai trò "Librarian". Tab "Thành viên" xuất hiện. | REQ-01 | EP

---

## Tổng hợp

| Nhóm chức năng | Số TC | REQ phủ | Kỹ thuật IDM áp dụng |
|----------------|-------|---------|----------------------|
| | | | |
| **Tổng** | **<!-- ≥ 20 -->** | | |
