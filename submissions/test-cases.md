# Test Cases — Bảng trường hợp kiểm thử

> **Hướng dẫn**: Viết tối thiểu **20 TC** phủ đủ các chức năng chính (REQ-01 → REQ-08).
> Xem [examples/sample-test-case.md](../examples/sample-test-case.md) để hiểu cách viết TC tốt.
> Tự tổ chức và phân nhóm test case theo cách hợp lý nhất.

| Thông tin | |
|---|---|
| **Nhóm** | <`group 2`> |
| **Ngày tạo** | <`27/05/2026`> |
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
| TC-01 | Login successfully — Librarian | stqa.rbc.vn is open, not logged in | 1. Enter email. 2. Enter password. 3. Click Login. | Email: `librarian@library.com` / PW: `admin123` | Redirected to main page. AppBar shows name + role "Librarian". "Members" tab is visible. | REQ-01 | EP |
| TC-02 | Login successfully — Member | stqa.rbc.vn is open, not logged in | 1. Enter email. 2. Enter password. 3. Click Login. | Email: `ba.nguyen@email.com` / PW: `password123` | Redirected to main page. AppBar shows name + role "Member". "Members" tab is NOT visible. | REQ-01 | EP |
| TC-03 | Reject — email does not exist | stqa.rbc.vn is open | 1. Enter non-existent email. 2. Enter any password. 3. Click Login. | Email: `nobody@email.com` / PW: `password123` | Error message displayed: "Không tìm thấy thành viên". Page does not change. | REQ-01 | EP |
| TC-04 | Reject — wrong password | stqa.rbc.vn is open | 1. Enter valid email. 2. Enter wrong password. 3. Click Login. | Email: `ba.nguyen@email.com` / PW: `wrongpassword` | Error message displayed: "Mật khẩu không đúng". Page does not change. | REQ-01 | EP |
| TC-05 | Reject — both fields empty (BVA boundary) | stqa.rbc.vn is open | 1. Leave email empty. 2. Leave password empty. 3. Click Login. | Email: _(empty)_ / PW: _(empty)_ | Error message displayed: "Vui lòng nhập email và mật khẩu". Page does not change. | REQ-01 | BVA |
| TC-06 | Display all 20 books correctly | Logged in as Librarian | 1. Go to "Books" tab. 2. Count the total books displayed. | — | Exactly 20 books displayed. Each book shows: title, author, category, year, status. | REQ-02 | EP |
| TC-07 | Seed data book statuses are correct | Logged in as any account | 1. Go to "Books" tab. 2. Check BOOK003, BOOK013, BOOK007, BOOK020. | — | BOOK003: "Borrowed". BOOK013: "Borrowed". BOOK007: "Lost". BOOK020: "Lost". All others: "Available". | REQ-02 | EP |
| TC-08 | Real-time status update after borrow | Logged in as Librarian. BOOK001 is "Available". | 1. Note BOOK001 = "Available". 2. Borrow BOOK001 for MEM003. 3. Return to Books tab. | — | BOOK001 immediately shows "Borrowed" without page refresh. | REQ-02 | State Model |
| TC-09 | Search by book title | Logged in, on Books tab | 1. Type keyword in search box. | Keyword: `Flutter` | Only books containing "Flutter" in title are shown. Unrelated books are hidden. | REQ-03 | EP |
| TC-10 | Search is case-insensitive | Logged in, on Books tab | 1. Type keyword in lowercase. | Keyword: `flutter` | Result is identical to TC-09 — same books displayed. | REQ-03 | Metamorphic |
| TC-11 | Search by author name | Logged in, on Books tab | 1. Type author name in search box. | Keyword: `Nguyen Minh Duc` | All books by Nguyen Minh Duc are displayed: BOOK001 and BOOK009. | REQ-03 | EP |
| TC-12 | Search returns no results | Logged in, on Books tab | 1. Type keyword that does not exist. | Keyword: `xyzabc123` | Message displayed: "No books found." No books shown. | REQ-03 | EP |
| TC-13 | Filter by category | Logged in, on Books tab | 1. Select a category from the dropdown. | Category: `Technology` | Only books in "Technology" category are displayed. All other books are hidden. | REQ-03 | EP |
| TC-14 | Borrow book successfully — Happy Path | Logged in as Librarian. Refresh to reset data. | 1. Select member dam.tran. 2. Select BOOK001 (Available). 3. Click Borrow. 4. Confirm. | Member: MEM003 (dam.tran) / Book: BOOK001 | ① Success message shown. ② BOOK001 → "Borrowed". ③ New record appears in Borrow/Return tab. ④ dueDate = today + 14 days. ⑤ dam.tran borrowCount = 1. | REQ-04 | EP |
| TC-15 | Reject — book is already borrowed | Logged in as Librarian. BOOK003 is "Borrowed". | 1. Select any active member. 2. Attempt to borrow BOOK003. | Book: BOOK003 (status = "Borrowed") | Rejected with message: "Sách không có sẵn để mượn". No new record created. BOOK003 remains "Borrowed". | REQ-04 | EP, Decision Table |
| TC-16 | Reject — member is suspended (BUG-04) | Logged in as Librarian. Refresh. | 1. Select cu.le (MEM004 — Suspended). 2. Select BOOK001 (Available). 3. Attempt borrow. | Member: MEM004 (cu.le — Suspended) / Book: BOOK001 | Rejected with EXACT message: "Thành viên đang bị tạm ngưng". NOT "expired". No record created. BOOK001 remains "Available". | REQ-04 | EP, Decision Table |
| TC-17 | Reject — member account is expired | Logged in as Librarian. Refresh. | 1. Select binh.pham (MEM005 — Expired). 2. Select BOOK001 (Available). 3. Attempt borrow. | Member: MEM005 (binh.pham — Expired) / Book: BOOK001 | Rejected with EXACT message: "Thành viên đã hết hạn". NOT "suspended". No record created. | REQ-04 | EP, Decision Table |
| TC-18 | Reject — borrow limit reached (BVA boundary = 3) | Logged in as Librarian. Borrow 2 more books for ba.nguyen (already has BR001 active = 1 book, total = 3). | 1. Attempt to borrow a 4th book for ba.nguyen. | Member: MEM002 (ba.nguyen, borrowCount = 3) / Book: any Available book | Rejected with message: "Bạn đã mượn tối đa 3 sách". borrowCount stays at 3. No new record created. | REQ-04 | BVA, Decision Table |
| TC-19 | BVA — borrow succeeds at boundary (count 2 → 3) | Logged in as Librarian. dam.tran is currently borrowing 2 books. | 1. Borrow one more book for dam.tran (3rd book). | Member: MEM003 (borrowCount = 2) / Book: BOOK001 (Available) | Borrow SUCCESSFUL. borrowCount = 3. This is the BVA test at the exact upper boundary. | REQ-04 | BVA |
| TC-20 | Return book on time — successfully | Logged in as biet.hoang (MEM006). BR003 is active. | 1. Go to "Borrow/Return" tab. 2. Find BR003. 3. Click Return. 4. Confirm. | Member: MEM006 / Record: BR003 / Book: BOOK013 | ① BR003 → "Returned". ② BOOK013 → "Available". ③ No overdue warning shown. ④ MEM006 borrowCount = 0. | REQ-05 | EP, FSM |
| TC-21 | Return overdue book — warning must appear (BUG-03) | Logged in as Librarian. Press "Check Overdue" first. Then log in as ba.nguyen. BR001 is overdue. | 1. Librarian clicks "Check Overdue". 2. Log in as ba.nguyen. 3. Go to Borrow/Return. 4. Return BOOK003 (BR001). | Record: BR001 (overdue since 15/09/2024) | ① Return successful. ② BR001 → "Returned". ③ BOOK003 → "Available". ④ System MUST display overdue warning. If no warning → BUG-03. | REQ-05 | EP, FSM |
| TC-22 | Overdue check — old record flagged correctly | Logged in as Librarian. Default data (BR001 overdue since 15/09/2024). | 1. Click "Check Overdue" button. | — | BR001 status changes to "Overdue". BR001 appears in overdue list visible to Librarian. | REQ-06 | EP |
| TC-23 | BVA — dueDate = today must be flagged (BUG-07) | Logged in as Librarian. Create a borrow record with dueDate = today. | 1. Borrow a book for MEM003. 2. Note dueDate = today. 3. Click "Check Overdue". | dueDate = today's date | Record MUST be flagged as "Overdue". SRS: dueDate <= today → Overdue. If NOT flagged → BUG-07. | REQ-06 | BVA |
| TC-24 | Record not yet due must NOT be flagged | Logged in as Librarian. BR003 has dueDate in the future. | 1. Click "Check Overdue". 2. Check BR003 status. | BR003 (dueDate > today) | BR003 remains "Borrowing". Must NOT change to "Overdue". | REQ-06 | BVA |
| TC-25 | Member cannot access Check Overdue | Logged in as ba.nguyen (Member). | 1. Observe the interface after login. | — | "Check Overdue" button is NOT visible in the Member interface. Only Librarian sees this button. | REQ-06 | EP |
| TC-26 | Add new member successfully | Logged in as Librarian. | 1. Go to "Members" tab. 2. Click "Add Member". 3. Fill in all fields. 4. Confirm. | Full name: `Nguyen Van Test` / Email: `nguyen.test@email.com` / Phone: `0901234567` | ① New member appears in the list. ② Default status: "Active". ③ borrowCount = 0. | REQ-07 | EP |
| TC-27 | Reject — email missing dot in domain (BUG-01) | Logged in as Librarian. | 1. Click "Add Member". 2. Enter email with no dot after @. 3. Fill remaining fields. 4. Confirm. | Email: `test@email` | System REJECTS. Displays "Invalid email" error. No member created. If accepted → BUG-01. | REQ-07 | EP |
| TC-28 | Reject — email missing @ symbol | Logged in as Librarian. | 1. Click "Add Member". 2. Enter email with no @ symbol. 3. Confirm. | Email: `testemail.com` | System rejects. Displays "Invalid email" error. No member created. | REQ-07 | EP |
| TC-29 | Reject — duplicate email | Logged in as Librarian. | 1. Click "Add Member". 2. Enter an email already in the system. 3. Confirm. | Email: `ba.nguyen@email.com` | System rejects. Displays duplicate email error. No new member created. | REQ-07 | EP |
| TC-30 | Reject — empty full name (BVA boundary) | Logged in as Librarian. | 1. Click "Add Member". 2. Leave full name empty. 3. Fill valid email and phone. 4. Confirm. | Full name: _(empty)_ / Email: `valid@email.com` / Phone: `0901234567` | System rejects. Displays error message. No member created with empty name. | REQ-07 | BVA |
| TC-31 | Member cannot add new members | Logged in as ba.nguyen (Member). | 1. Observe navigation bar after login. | — | "Members" tab and "Add Member" button are NOT visible to Member role. | REQ-07 | EP |
| TC-32 | Librarian can view all borrow records | Logged in as Librarian. | 1. Go to "Borrow/Return" tab. 2. View record list. | — | All records from all members displayed: BR001, BR002, BR003, BR004, BR005 and any newly created records. | REQ-08 | EP |
| TC-33 | Member can only view own records | Logged in as ba.nguyen (MEM002). | 1. Go to "Borrow/Return" tab. 2. View record list. | — | Only BR001 and BR004 (ba.nguyen's records) are shown. BR002, BR003, BR005 are NOT visible. | REQ-08 | EP |
| TC-34 | Security — member cannot view another member's records (BUG-05) | Logged in as ba.nguyen (MEM002). | 1. Go to "Borrow/Return" tab. 2. Attempt to access or search for MEM003's records (dam.tran). | Target: MEM003 (dam.tran) | ba.nguyen must NOT see any records belonging to dam.tran. Result: 0 records or access denied. If records visible → BUG-05 (Critical / P1). | REQ-08 | EP |
| TC-35 | Borrow record displays all 5 required fields | Logged in as Librarian. | 1. Go to "Borrow/Return" tab. 2. View details of BR001. | Record: BR001 | BR001 shows all 5 fields: Record ID (BR001) + Book (BOOK003) + Borrow Date (01/09/2024) + Due Date (15/09/2024) + Status (Borrowing). | REQ-08 | EP |

| | | | |

---

## Tổng hợp

| Feature Group | No. of TCs | REQ Covered | IDM Techniques Applied |
| ------------- | ---------- | ----------- | ---------------------- |
| Login | 5 | REQ-01 | EP, BVA |
| Book List | 3 | REQ-02 | EP, State Model |
| Search & Filter | 5 | REQ-03 | EP, Metamorphic |
| Borrow Book | 6 | REQ-04 | EP, BVA, Decision Table |
| Return Book | 2 | REQ-05 | EP, FSM |
| Check Overdue | 4 | REQ-06 | EP, BVA |
| Member Management | 6 | REQ-07 | EP, BVA |
| Borrow Records | 4 | REQ-08 | EP |
| **Total** | **35** | **REQ-01 → REQ-08** | **EP, BVA, Decision Table, FSM, Metamorphic** |
|
