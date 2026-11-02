# Bài 9: Thiết Kế Không Gian Đa Profile Và Kiến Trúc Bookmark Cho Trạm Khám Bệnh

## Phần 1. Báo cáo phân tích kiến trúc đa Profile

### 1. Phân tích nguyên nhân xảy ra hiện tượng văng phiên đăng nhập (Session Conflict)

Google Chrome lưu trữ dữ liệu của mỗi hồ sơ trình duyệt (Profile) trong một thư mục User Data riêng. Dữ liệu bao gồm Cookies, History, Bookmarks, Extensions và thông tin phiên đăng nhập.

Khi nhiều nhân viên cùng sử dụng một Profile Chrome:

- Các website sử dụng chung kho Cookies của Profile đó.
- Cookies chứa thông tin phiên đăng nhập (Session ID) giúp website nhận diện người dùng.
- Khi một nhân viên đăng nhập tài khoản khác trên cùng hệ thống, phiên đăng nhập cũ có thể bị thay thế hoặc hết hiệu lực nếu website chỉ cho phép một phiên đăng nhập.
- Điều này khiến bác sĩ đang làm việc trên CarePulse có thể bị đăng xuất đột ngột, làm gián đoạn quá trình khám bệnh.

**Kết luận:** Nguyên nhân chính là nhiều người sử dụng chung một môi trường lưu trữ dữ liệu trình duyệt. Giải pháp là tạo các Profile riêng biệt để Cookies và dữ liệu của từng môi trường không bị dùng chung.

### 2. Thiết kế mô hình 3 Profile chuyên dụng

Đề xuất sử dụng mô hình 3 Profile gồm Work, Personal và Sandbox/Test.

| Profile | Mục đích sử dụng | Màu Theme | Dữ liệu lưu trữ độc lập |
|---|---|---|---|
| Work - CarePulse | Dành cho bác sĩ và điều dưỡng sử dụng hệ thống khám chữa bệnh. | Xanh dương | Cookies, History, Bookmarks, Extensions phục vụ công việc. |
| Personal | Dành cho nhân viên sử dụng Gmail cá nhân, mạng xã hội và các nhu cầu riêng. | Tím | Cookies, History, Bookmarks, Extensions cá nhân. |
| Sandbox/Test | Dành cho bác sĩ thực tập kiểm tra website, tài khoản thử nghiệm và học tập. | Cam | Cookies, History, Bookmarks, Extensions thử nghiệm. |

**Nguyên tắc sử dụng:**

- Work chỉ sử dụng cho công việc khám chữa bệnh.
- Personal chỉ sử dụng cho nhu cầu cá nhân, không đăng nhập tài khoản CarePulse.
- Sandbox/Test chỉ dùng để kiểm thử, không sử dụng dữ liệu bệnh nhân thật.
- Mỗi Profile có màu sắc và tên riêng để dễ nhận biết.
- Chỉ cài đặt các tiện ích mở rộng cần thiết và được quản trị viên cho phép.
- Không sử dụng chung tài khoản CarePulse giữa các nhân viên.


## Phần 2. Thiết kế hệ thống Bookmark và quy trình an toàn

### 3. Xây dựng cây thư mục Bookmark cho bác sĩ trực

Sử dụng tổ hợp phím `Ctrl + Shift + O` để mở Trình quản lý dấu trang (Bookmark Manager).

Tạo 3 thư mục chính để phân loại các website phục vụ công việc:

```text
Thanh dấu trang
│
├── 📁 01_CarePulse_Clinic
│   ├── CarePulse - Hệ thống quản lý khám bệnh
│   ├── Cổng thông tin nội bộ phòng khám
│   └── Hệ thống quản lý lịch khám
│
├── 📁 02_Pharma_Portal
│   ├── Dược thư Quốc gia Việt Nam
│   ├── Cổng tra cứu thuốc
│   └── Cơ sở dữ liệu thông tin thuốc
│
└── 📁 03_Medical_Tools
    ├── Cổng BHYT
    ├── Google Dịch
    └── Công cụ tra cứu thuật ngữ y khoa
```

**Ý nghĩa từng thư mục:**

- **01_CarePulse_Clinic:** Lưu các website phục vụ quản lý bệnh nhân, lịch khám và hoạt động của phòng khám.
- **02_Pharma_Portal:** Lưu các website tra cứu thuốc, thành phần và thông tin sử dụng thuốc.
- **03_Medical_Tools:** Lưu các công cụ hỗ trợ bác sĩ như tra cứu BHYT, dịch thuật và thuật ngữ y khoa.

**Cách thực hiện:**

1. Mở Chrome bằng Profile Work - CarePulse.
2. Nhấn `Ctrl + Shift + O`.
3. Tạo 3 thư mục theo tên đã thiết kế.
4. Truy cập website chính thức cần lưu.
5. Nhấn `Ctrl + D`.
6. Chọn đúng thư mục và nhấn Lưu.
7. Kiểm tra lại các liên kết để bảo đảm truy cập được.

Có thể bổ sung thêm các website chính thức đã được xác minh vào từng thư mục để đạt tối thiểu 30 liên kết quan trọng theo nhu cầu của phòng khám.

### 4. Áp dụng mẹo Favicon-Only

Favicon-Only là cách chỉ hiển thị biểu tượng website trên thanh Bookmark và ẩn phần tên chữ, giúp tiết kiệm diện tích thanh công cụ.

Áp dụng cho 4 website thường xuyên sử dụng:

| Website | Cách hiển thị |
|---|---|
| CarePulse | Chỉ hiển thị biểu tượng |
| Cổng BHYT | Chỉ hiển thị biểu tượng |
| Dược thư | Chỉ hiển thị biểu tượng |
| Google Dịch | Chỉ hiển thị biểu tượng |

**Các bước thực hiện:**

1. Lưu website vào thanh dấu trang.
2. Nhấp chuột phải vào Bookmark cần chỉnh sửa.
3. Chọn Chỉnh sửa (Edit).
4. Xóa nội dung trong ô Tên (Name).
5. Giữ nguyên địa chỉ website.
6. Nhấn Lưu (Save).
7. Thực hiện tương tự với 3 website còn lại.

**Kết quả:** Thanh Bookmark chỉ hiển thị 4 biểu tượng website thay vì cả biểu tượng và tên chữ. Nhờ đó, bác sĩ có thể lưu được nhiều liên kết hơn và truy cập nhanh hơn.

### 5. Xử lý tình huống khách mượn máy và cảnh báo Cloud Sync

**Lựa chọn: Guest Profile (Chế độ khách).**

Khi có đoàn thanh tra hoặc khách được phép mượn máy tính trong 30 phút, nhân viên nên sử dụng Guest Profile thay vì Incognito Mode.

**Lý do lựa chọn Guest Profile:**

- Tạo môi trường duyệt web tạm thời, tách biệt với các Profile thông thường.
- Không hiển thị trực tiếp Bookmark, lịch sử và phiên đăng nhập đã lưu trong Profile Work hoặc Personal.
- Khi đóng toàn bộ cửa sổ Guest, dữ liệu duyệt web của phiên khách sẽ bị xóa.
- Hạn chế nguy cơ người mượn máy nhìn thấy thông tin cá nhân và dữ liệu công việc.

**Tại sao không chọn Incognito Mode?**

- Incognito chỉ tạo cửa sổ ẩn danh bên trong Profile hiện tại.
- Không tạo một môi trường người dùng độc lập hoàn toàn.
- Không bảo vệ máy tính khỏi người có quyền truy cập vào hệ thống.
- Không thay thế được việc phân quyền tài khoản Windows.

**Cảnh báo khi bật Cloud Sync tài khoản cá nhân:**

Không nên đăng nhập và bật đồng bộ hóa Cloud Sync bằng tài khoản Google cá nhân trên máy tính phòng khám dùng chung vì:

- Bookmark và lịch sử duyệt web cá nhân có thể được đồng bộ lên tài khoản Google.
- Nếu quên đăng xuất, người sử dụng tiếp theo có thể tiếp cận tài khoản cá nhân.
- Dữ liệu cá nhân có thể bị lưu lại trên máy tính dùng chung.
- Có nguy cơ đồng bộ nhầm dữ liệu nhạy cảm lên dịch vụ đám mây không được cơ sở y tế phê duyệt.

**Quy trình an toàn khi bàn giao máy:**

1. Bác sĩ kết thúc công việc trên hệ thống CarePulse theo đúng quy trình.
2. Đăng xuất tài khoản công việc khi cần thiết.
3. Đóng các cửa sổ chứa thông tin bệnh nhân.
4. Mở Guest Profile cho khách hoặc đoàn thanh tra sử dụng.
5. Không cho khách truy cập Profile Work, Personal hoặc Sandbox/Test.
6. Sau khi khách sử dụng xong, đóng toàn bộ cửa sổ Guest.
7. Kiểm tra máy tính và khóa màn hình khi rời khỏi vị trí làm việc.

**Trường hợp mất kết nối Internet:**

- Không sử dụng tài khoản hoặc thiết bị cá nhân để lưu tạm dữ liệu bệnh nhân.
- Chỉ sử dụng quy trình làm việc ngoại tuyến đã được cơ sở y tế phê duyệt.
- Không tải dữ liệu bệnh nhân về USB hoặc thư mục dùng chung không được bảo vệ.
- Khi có kết nối Internet trở lại, cập nhật dữ liệu theo hướng dẫn của đơn vị.

## Tổng kết cuối bài

Mô hình 3 Profile Work - Personal - Sandbox giúp phân tách môi trường làm việc, tài khoản và dữ liệu trình duyệt, hạn chế xung đột phiên đăng nhập giữa các nhân viên.

Bên cạnh đó, việc xây dựng hệ thống Bookmark theo từng nhóm công việc và áp dụng Favicon-Only giúp bác sĩ tìm kiếm thông tin nhanh chóng, tiết kiệm diện tích thanh công cụ.

Khi có khách mượn máy, sử dụng Guest Profile kết hợp với việc kiểm soát Cloud Sync, tài khoản Windows và quyền truy cập dữ liệu bệnh nhân sẽ giúp nâng cao tính bảo mật cho hệ thống máy tính tại trạm khám CarePulse.