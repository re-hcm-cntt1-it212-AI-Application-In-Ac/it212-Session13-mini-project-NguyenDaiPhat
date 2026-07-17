# TÀI LIỆU ĐẶC TẢ YÊU CẦU PHẦN MỀM & THIẾT KẾ HỆ THỐNG (SYSTEM DESIGN & SRS)
## HỆ THỐNG QUẢN LÝ KHÁCH SẠN VÀ RESORT CAO CẤP (ENTERPRISE PMS)

**Mã tài liệu:** SRS-ePMS-2026-V2.0  
**Chuẩn đặc tả:** IEEE Std 830-1998  
**Vai trò biên soạn:** Senior Solution Architect, IT Consultant & Senior Business Analyst  
**Ngày phát hành:** 17/07/2026  

---

## 1. INTRODUCTION (GIỚI THIỆU HỆ THỐNG)

Trong kỷ nguyên chuyển đổi số và xu hướng "không chạm" (contactless), ngành dịch vụ lưu trú (Hospitality) đòi hỏi tính linh hoạt, tốc độ và độ chính xác cao trong mọi khâu vận hành. **Hệ thống Quản lý Khách sạn (Hotel Management System - PMS)** đóng vai trò là "hệ điều hành trung tâm" của một khách sạn hoặc khu nghỉ dưỡng (resort). 

Hệ thống được thiết kế dưới dạng một nền tảng **Cloud-Native SaaS (Software-as-a-Service)** hiện đại, áp dụng kiến trúc **Microservices** và tiếp cận theo mô hình **API-First**. PMS không chỉ đơn thuần là công cụ ghi nhận đặt phòng, mà là hạt nhân liên kết toàn bộ các điểm sảnh và tiện ích dịch vụ: từ Lễ tân (Front Desk), Buồng phòng (Housekeeping), Nhà hàng & Bar (F&B POS), Spa & Gym, cho đến các kênh phân phối phòng trực tuyến (OTA, Channel Manager) và hệ thống kế toán doanh nghiệp (ERP).

Hệ thống hỗ trợ quản lý tập trung đa chi nhánh (Multi-Property) thời gian thực, cho phép ban quản trị giám sát hiệu suất vận hành và dòng tài chính mọi lúc mọi nơi trên các thiết bị Web và Mobile App.

---

## 2. SCOPE (PHẠM VI HỆ THỐNG)

Phạm vi của hệ thống PMS được phân định rõ ràng giữa các phân hệ cốt lõi chạy trực tiếp trên nền tảng (In-Scope) và các hệ thống bên thứ ba liên kết (Out-of-Scope) qua giao thức API.

### 2.1 Nằm trong phạm vi hệ thống (In-Scope Modules)
* **Phân hệ Đặt phòng & Quản lý Phân phối (Reservation & Distribution)**: Bộ máy đặt phòng trực tuyến (Booking Engine) tích hợp trên website khách sạn; Quản lý booking lẻ (FIT), booking đoàn (GIT) và công ty.
* **Phân hệ Sơ đồ phòng & Lễ tân (Front Office & Room Operations)**: Quản lý sơ đồ phòng tương tác thời gian thực (Room Grid); Xử lý check-in/check-out; Quét định danh khách (OCR); Ghi khóa từ RFID.
* **Phân hệ Buồng phòng & Bảo trì (Housekeeping & Maintenance)**: Housekeeper Mobile App để phân công dọn phòng và báo cáo minibar thời gian thực; Quản lý Lost & Found; Tạo phiếu bảo trì phòng (OOS/OOO).
* **Phân hệ Thu ngân & Tài chính (Cashiering & Finance)**: Quản lý Folio thanh toán; Định tuyến chi phí (Routing Rules); Kiểm toán đêm tự động (Night Audit); Kết nối xuất hóa đơn đỏ điện tử (e-Invoice).
* **Phân hệ CRM & Thành viên (CRM & Loyalty)**: Hợp nhất Guest Profile, theo dõi lịch sử lưu trú, sở thích cá nhân, tích lũy điểm thưởng và lưu trữ hợp đồng doanh nghiệp (Corporate Contracts).
* **Phân hệ Báo cáo & Phân tích (Reporting & Analytics)**: Dashboard chỉ số OCC, ADR, RevPAR; Báo cáo tài chính chốt ngày (Daily Flash Report).

### 2.2 Nằm ngoài phạm vi hệ thống (Out-of-Scope / Integrations)
Hệ thống không tự xây dựng mà chỉ tích hợp thông qua các giao thức API:
* **Channel Manager**: SiteMinder, YieldPlanet để đồng bộ phòng lên Agoda, Booking.com.
* **Hệ thống Phần cứng & IoT**: Đầu đọc khóa thẻ từ RFID (VingCard, Salto), tổng đài PBX, tivi tương tác (Smart IPTV).
* **Kế toán & ERP**: Đồng bộ Ledger cuối ngày sang SAP, MISA.
* **Cổng thanh toán**: Stripe, VNPay (Tokenization bảo mật thẻ).

---

## 3. OBJECTIVES (MỤC TIÊU HỆ THỐNG)

### 3.1 Mục tiêu Vận hành (Operational KPIs)
* **Tối ưu hóa thời gian sảnh**: Giảm thời gian làm thủ tục Check-in xuống dưới 2 phút/khách và Check-out dưới 3 phút/khách bằng số hóa quy trình (OCR, chữ ký điện tử).
* **Vận hành thời gian thực**: Đồng bộ tức thì trạng thái buồng phòng (VD -> VC) giữa bộ phận dọn phòng di động và lễ tân.
* **Tự động hóa đối soát ngày**: Rút ngắn thời gian chạy quy trình Kiểm toán đêm (Night Audit) xuống dưới 15 phút với tỷ lệ khớp số liệu 100%.

### 3.2 Mục tiêu Kinh doanh & Quản trị (Business & Tech KPIs)
* **Tối đa hóa chỉ số RevPAR**: Tích hợp Dynamic Pricing tự động tăng/giảm giá theo công suất và mùa vụ.
* **Hợp nhất hồ sơ khách hàng**: Tạo lập Guest Profile tập trung, nâng cao tỷ lệ khách quay lại bằng cá nhân hóa dịch vụ.
* **Đảm bảo tính sẵn sàng cao**: Hệ thống đạt SLA 99.9% uptime, tự động co giãn (Auto-scaling) trong mùa cao điểm.

---

## 4. ACTORS (CÁC TÁC NHÂN HỆ THỐNG & PHÂN QUYỀN)

| Tên Actor | Vai trò (Role) | Quyền hạn chính (RBAC) | Chức năng chính trên hệ thống |
| :--- | :--- | :--- | :--- |
| **Lễ tân<br>(GSA)** | Đại diện đón tiếp và phục vụ khách trực tiếp tại sảnh. | Quyền vận hành Front Office sảnh. Không có quyền sửa đổi cấu hình giá và xóa log. | • Xử lý Check-in/Check-out.<br>• Quản lý Room Grid, chuyển phòng.<br>• Thu cọc, thanh toán và phát hành thẻ từ phòng. |
| **Nhân viên Đặt phòng<br>(Reservation Agent)** | Quản lý nguồn khách đặt trước và thiết lập quỹ phòng bán. | Quyền Đặt phòng. Không có quyền thanh toán tài chính tại sảnh hoặc phát khóa thẻ từ. | • Tạo mới, sửa, hủy các booking lẻ và đoàn.<br>• Quản lý hợp đồng giá đại lý (TA) và công ty.<br>• Quản lý phòng phân bổ (Allotment) trên các kênh. |
| **Nhân viên Buồng phòng<br>(Housekeeper)** | Dọn dẹp phòng và kiểm soát vật dụng phòng. | Quyền Buồng phòng di động. Chỉ truy cập dữ liệu dọn phòng, minibar. | • Nhận danh sách phòng dọn ưu tiên trên mobile.<br>• Cập nhật trạng thái dọn dẹp (VD -> VC).<br>• Khai báo minibar tiêu dùng, Lost & Found, báo hỏng phòng. |
| **Kiểm toán viên đêm<br>(Night Auditor)** | Đối soát tài chính và khóa sổ kế toán ngày cũ. | Quyền Kiểm toán & Kế toán sảnh. Quyền chạy tiến trình chốt ngày Night Audit. | • Chạy tiến trình chốt sổ ngày (Night Audit).<br>• Post tiền phòng tự động, đối soát doanh thu POS.<br>• Dịch chuyển ngày hệ thống, xuất Daily Flash Report. |
| **Quản lý<br>(Manager)** | Giám sát vận hành các bộ phận và phê duyệt nghiệp vụ. | Quyền Quản lý / Override quyền hạn hạn mức, phê duyệt ghi đè. | • Quản lý cấu hình danh mục phòng và danh mục dịch vụ.<br>• Quản lý danh sách nhân sự và phân ca.<br>• Xem toàn bộ hệ thống báo cáo BI, phê duyệt hoàn tiền. |
| **Khách hàng<br>(Guest)** | Người lưu trú sử dụng dịch vụ khách sạn/resort. | Quyền truy cập công cộng qua Token bảo mật trên Web/App. | • Tra cứu phòng trống, đặt phòng, thanh toán online.<br>• Tự hủy đặt phòng, tự chọn đổi số phòng online.<br>• Thực hiện pre-check-in trực tuyến qua di động. |
| **Quản trị viên IT<br>(Admin)** | Cấu hình hạ tầng, bảo mật và kết nối API. | Quyền tối cao (Super Admin). Toàn quyền cấu hình và phân quyền hệ thống. | • Cấu hình sơ đồ phòng ban đầu, phân quyền tài khoản.<br>• Thiết lập tham số kết nối API khóa từ, POS, cổng Thuế.<br>• Giám sát hệ thống logs và Audit Trail. |

---

## 5. MODULES (THIẾT KẾ CÁC PHÂN HỆ CỐT LÕI)

Hệ thống PMS được chia thành 6 phân hệ lớn:
1. **Module Đặt phòng & Phân phối**: Xử lý luồng đặt phòng trực tuyến (Booking Engine), đồng bộ hai chiều với Channel Manager và quản lý quỹ phòng phân bổ.
2. **Module Quản lý Lễ tân (Front Office)**: Vận hành sơ đồ phòng trực quan Room Grid, xử lý Check-in quét OCR giấy tờ tùy thân, liên kết bộ ghi thẻ RFID.
3. **Module Buồng phòng & Bảo trì**: Quản lý dọn dẹp qua Housekeeping Mobile App, cập nhật minibar tức thì và tạo phiếu bảo trì (OOO/OOS).
4. **Module Thu ngân & Thanh toán**: Quản lý Folio hóa đơn, định tuyến chi phí, kết nối cổng thanh toán trực tuyến, chạy quy trình Kiểm toán đêm và gọi API xuất hóa đơn đỏ.
5. **Module CRM & Loyalty**: Hợp nhất Guest Profile, theo dõi lịch sử lưu trú, sở thích cá nhân, tích lũy điểm thưởng và lưu trữ hợp đồng doanh nghiệp (Corporate Contracts).
6. **Module Báo cáo & BI**: Tổng hợp dữ liệu tài chính kế toán sảnh, hiển thị dashboard đo lường công suất phòng, ADR, RevPAR thời gian thực.

---

## 6. BUSINESS FLOW (QUY TRÌNH NGHIỆP VỤ KHÉP KÍN TỪNG BƯỚC)

### 6.1 Khách đặt phòng (Guest Booking)
1. Khách hàng truy cập Web Booking Engine hoặc liên hệ nhân viên Đặt phòng.
2. Nhập ngày đến/đi, số khách. PMS truy vấn phòng trống VC khả dụng và hiển thị giá bán.
3. Khách chọn phòng, nhập thông tin cá nhân và thông tin thẻ bảo lãnh thanh toán.
4. Nhấn xác nhận. PMS tạo booking ở trạng thái `Pending` (Chờ cọc/xác nhận), cấp mã đặt phòng.

### 6.2 Lễ tân / Nhân viên đặt phòng xác nhận (Reservation Confirmation)
1. Nhân viên đặt phòng nhận thông báo booking mới trên PMS, kiểm tra tính hợp lệ.
2. Xác nhận cọc thành công, nhân viên bấm duyệt. PMS chuyển trạng thái booking sang `Confirmed`.
3. Hệ thống tự động gọi API Channel Manager cập nhật giảm quỹ phòng khả dụng trên các trang OTA.
4. Gửi email xác nhận kèm mã QR đặt phòng và các thông tin điều khoản cho khách.

### 6.3 Thanh toán trực tuyến (Online Payment)
1. Khách mở liên kết thanh toán an toàn trong email xác nhận đặt phòng.
2. Thanh toán cọc qua cổng Stripe/VNPay. Cổng thanh toán trừ tiền khách và gửi Webhook về PMS.
3. PMS tự động tạo Folio phòng, ghi nhận dòng thanh toán cọc dưới dạng số dư Credit (-).
4. Cập nhật booking sang trạng thái đảm bảo thanh toán.

### 6.4 Quy trình Nhận phòng (Check-in)
1. Khách xuất trình mã QR đặt phòng tại sảnh hoặc tại Kiosk tự check-in.
2. Lễ tân quét giấy tờ tùy thân qua OCR để điền tự động Profile và lưu ảnh quét bảo mật.
3. Hệ thống đề xuất phòng VC cùng hạng trên sơ đồ Grid (Phòng 302). Lễ tân xác nhận gán phòng.
4. Khách ký phiếu đăng ký lưu trú điện tử trên tablet.
5. Lễ tân ghi thẻ từ vật lý qua đầu đọc RFID được PMS điều khiển bằng API.
6. Nhấn hoàn tất Check-in. Trạng thái phòng chuyển sang `Checked In` (phòng OC). Kích hoạt PBX, IPTV phòng.

### 6.5 Quy trình Trả phòng (Check-out)
1. Khách trả lại thẻ phòng tại quầy lễ tân hoặc nhấn yêu cầu Check-out trên App.
2. PMS tổng hợp toàn bộ chi phí phát sinh từ các POS nhà hàng, spa và minibar buồng phòng vào Folio.
3. Khấu trừ khoản đặt cọc đã trả ở bước 3. Khách thanh toán phần chênh lệch còn lại.
4. Số dư Folio về 0. Bấm chốt Check-out. Trạng thái phòng chuyển sang VD (Trống bẩn), vô hiệu hóa thẻ từ.
5. Cập nhật trạng thái booking sang `Checked Out`.

### 6.6 Xuất hóa đơn tài chính (e-Invoice Export)
1. Khách cung cấp Mã số thuế, tên công ty và địa chỉ chính thức.
2. Lễ tân gọi API tra cứu nhanh thông tin doanh nghiệp, xác nhận các hạng mục cần xuất.
3. PMS đóng gói file XML gửi qua API sang hệ thống hóa đơn điện tử liên kết với cơ quan thuế.
4. Nhận file PDF hóa đơn đỏ có mã số thuế cơ quan thuế và gửi tự động vào email khách hàng.
5. Đồng bộ bản ghi doanh thu sang phần mềm kế toán ERP.

---

## 7. FUNCTIONAL REQUIREMENTS (YÊU CẦU CHỨC NĂNG CHI TIẾT)

| ID | Tên Yêu Cầu Chức Năng | Mô tả nghiệp vụ chi tiết | Mức độ ưu tiên | Tác nhân liên quan |
| :--- | :--- | :--- | :--- | :--- |
| **FR-RES-001** | Tìm kiếm phòng trống | Cho phép tra cứu số lượng phòng trống và giá phòng thực tế theo ngày nhận/trả phòng, số khách và loại phòng. | **Critical** | Khách hàng, Nhân viên đặt phòng, Lễ tân |
| **FR-RES-002** | Tạo đặt phòng lẻ (FIT) | Cho phép khởi tạo booking cho khách lẻ, ghi nhận thông tin lưu trú, thông tin liên lạc và bảo đảm thanh toán. | **Critical** | Khách hàng, Nhân viên đặt phòng, Lễ tân |
| **FR-RES-003** | Tạo đặt phòng đoàn (GIT) | Hỗ trợ chọn nhiều phòng, gán mã đoàn (Group Code), phân bổ chung Master Folio thanh toán cho cả đoàn. | **High** | Nhân viên đặt phòng, Lễ tân |
| **FR-RES-004** | Quản lý giá & Khuyến mãi | Cho phép cấu hình giá phòng gốc, giá ưu đãi (Corporate Rates) và luật giá động theo công suất. | **High** | Nhân viên đặt phòng, Ban Giám Đốc |
| **FR-RES-005** | Đồng bộ kênh OTA | Gọi API kết nối với Channel Manager để tự động cập nhật số lượng phòng và giá bán trên các sàn OTA dưới 3 giây. | **Critical** | Quản trị viên IT |
| **FR-RES-006** | Quản lý trạng thái Booking | Cập nhật và theo dõi các trạng thái của booking (`Pending`, `Confirmed`, `Checked In`, `Checked Out`, `Cancelled`). | **High** | Nhân viên đặt phòng, Lễ tân |
| **FR-RES-007** | Quản lý hợp đồng đối tác | Cấu hình và theo dõi hợp đồng đại lý lữ hành (TA) và doanh nghiệp (Corporate), tự động áp dụng giá thỏa thuận. | **High** | Nhân viên đặt phòng |
| **FR-RES-008** | Khách hủy đặt phòng trực tuyến | Cho phép khách hàng tự hủy đặt phòng qua Web/App, hệ thống tự động kiểm tra chính sách phạt hủy và cập nhật trạng thái thành `Cancelled`. | **Critical** | Khách hàng |
| **FR-FO-001** | Sơ đồ phòng trực quan | Cung cấp giao diện Room Grid tương tác kéo thả để gán phòng, chuyển phòng và xem nhanh thông tin lưu trú. | **Critical** | Lễ tân |
| **FR-FO-002** | Nhận diện giấy tờ OCR | Kết nối đầu quét chuyên dụng để tự động chụp ảnh và trích xuất thông tin Hộ chiếu/CCCD lưu vào Guest Profile. | **High** | Lễ tân |
| **FR-FO-003** | Thuật toán gán phòng | Tự động phân bổ số phòng cụ thể dựa trên hạng phòng và tối ưu hóa các khoảng phòng trống lẻ (Room Gap). | **High** | Lễ tân |
| **FR-FO-004** | Xử lý Nhận phòng (Check-in) | Thay đổi trạng thái phòng sang OC (Occupied Clean), kích hoạt thiết bị PBX, IPTV phòng và tạo yêu cầu tạo Folio sảnh. | **Critical** | Lễ tân, Khách hàng (Kiosk) |
| **FR-FO-005** | Phát hành khóa thẻ từ | Gọi lệnh API kết nối với bộ ghi thẻ từ RFID để nạp số phòng và thời gian Check-out của khách vào thẻ khóa phòng. | **Critical** | Lễ tân |
| **FR-FO-006** | Chuyển phòng & Nâng hạng | Cho phép dời khách sang phòng mới khác, tự động chốt phí phòng cũ và tính phí phòng mới nếu có chênh lệch giá. | **High** | Lễ tân, Ban Giám Đốc |
| **FR-FO-007** | Quản lý yêu cầu của khách | Ghi nhận các yêu cầu đặc biệt của khách tại phòng (nước uống, nôi em bé) và chuyển lệnh cho bộ phận liên quan. | **Medium** | Lễ tân |
| **FR-FO-008** | Khách tự đổi phòng trực tuyến | Cho phép khách hàng tự thực hiện đổi số phòng vật lý trống sạch (VC) cùng hạng trên Web/App trong quá trình lưu trú/pre-check-in. | **High** | Khách hàng |
| **FR-HK-001** | Phân công dọn dẹp phòng | Tự động phân bổ danh sách phòng cần dọn cho nhân viên buồng phòng đang trực ca dựa trên sơ đồ tầng (Zones). | **High** | Giám sát buồng phòng |
| **FR-HK-002** | Đổi trạng thái phòng di động | Cho phép nhân viên buồng phòng đổi trạng thái phòng trực tiếp trên app di động ngay khi dọn xong (VD -> VC). | **Critical** | Nhân viên buồng phòng |
| **FR-HK-003** | Khai báo minibar tiêu dùng | Cho phép nhập số lượng đồ uống minibar khách tiêu thụ trên mobile app để hệ thống tự động post phí vào Folio phòng. | **Critical** | Nhân viên buồng phòng |
| **FR-HK-004** | Quản lý tài sản Lost & Found | Ghi nhận thông tin tài sản khách bỏ quên (ảnh chụp, vị trí tìm thấy, mô tả) và theo dõi trạng thái trả lại cho khách. | **Medium** | Nhân viên buồng phòng, Lễ tân |
| **FR-HK-005** | Báo hỏng & Phiếu bảo trì | Cho phép tạo phiếu sửa chữa thiết bị hỏng trong phòng, tự động đổi trạng thái phòng trên PMS sang hỏng hóc (OOO). | **High** | Nhân viên buồng phòng, Kỹ thuật |
| **FR-PAY-001** | Quản lý hóa đơn Folio | Cho phép tạo lập, xem chi tiết giao dịch, tách hóa đơn (Split Bill) và gộp hóa đơn (Combine Bill) linh hoạt. | **Critical** | Lễ tân, Kiểm toán viên đêm |
| **FR-PAY-002** | Quy tắc định tuyến chi phí | Cấu hình tự động chuyển các khoản phí cụ thể (ví dụ: tiền phòng sang Folio công ty, tiền ăn uống spa giữ tại Folio khách). | **High** | Lễ tân, Kiểm toán viên đêm |
| **FR-PAY-003** | Ghi nợ dịch vụ POS | Nhận lệnh API từ POS ngoài (Spa, Nhà hàng) để đối chiếu thông tin phòng và tự động ghi nợ chi phí vào Folio phòng. | **Critical** | Khách hàng (qua POS), Lễ tân |
| **FR-PAY-004** | Thu đặt cọc & Thanh toán | Ghi nhận tiền đặt cọc (Deposit) và xử lý thanh toán hóa đơn bằng tiền mặt, thẻ tín dụng sảnh hoặc link thanh toán. | **Critical** | Lễ tân, Kiểm toán viên đêm |
| **FR-PAY-005** | Quy trình Kiểm toán đêm | Tự động khóa ngày vận hành, tính tiền phòng đêm nay cho khách lưu trú, sao lưu database và chuyển ngày hệ thống. | **Critical** | Kiểm toán viên đêm |
| **FR-PAY-006** | Xuất hóa đơn GTGT điện tử | Gọi API kết nối với hệ thống hóa đơn điện tử được Tổng cục Thuế phê duyệt để cấp mã hóa đơn đỏ chính thức cho khách. | **High** | Lễ tân, Kiểm toán viên đêm |
| **FR-PAY-007** | Hoàn tiền & Giảm trừ (Rebate) | Cho phép trả lại tiền đặt cọc thừa hoặc thực hiện giảm trừ chi phí hóa đơn cho khách trong trường hợp xảy ra sự cố. | **High** | Lễ tân, Ban Giám Đốc |
| **FR-CRM-001** | Hợp nhất hồ sơ khách hàng | Hợp nhất các hồ sơ khách trùng lặp dựa trên căn cước/số điện thoại, chuẩn hóa lịch sử lưu trú và ghi nhận sở thích khách. | **High** | Nhân viên đặt phòng, Lễ tân |
| **FR-CRM-002** | Hồ sơ doanh nghiệp & TA | Quản lý thông tin liên hệ, hạn mức công nợ và điều khoản thanh toán riêng của công ty đối tác và đại lý du lịch. | **High** | Nhân viên đặt phòng |
| **FR-CRM-003** | Điểm thưởng & Thành viên | Tích lũy điểm thưởng dựa trên tổng chi tiêu hóa đơn của khách và nâng hạng thẻ thành viên tự động khi đạt mốc. | **Medium** | Nhân viên đặt phòng, Lễ tân |
| **FR-ADM-001** | Tạo tài khoản & Phân quyền | Quản lý danh sách nhân sự sử dụng phần mềm, cấp quyền dựa trên vai trò chức danh (RBAC) chi tiết đến từng nút bấm. | **Critical** | Quản trị viên IT |
| **FR-ADM-002** | Quản lý ca trực nhân sự | Cấu hình lịch ca trực của nhân viên, ghi nhận thời gian bắt đầu/kết thúc ca làm việc trên hệ thống. | **Medium** | Quản trị viên IT, Ban Giám Đốc |
| **FR-ADM-003** | Nhật ký kiểm toán hệ thống | Tự động ghi lại log toàn bộ các thay đổi nhạy cảm (sửa giá, hủy hóa đơn, đổi phòng) kèm thông tin User ID và Timestamp. | **High** | Quản trị viên IT |
| **FR-ADM-004** | Chấm công ca trực | Hỗ trợ ghi nhận giờ làm việc thực tế, thời điểm Check-in/Check-out ca trực của nhân viên tại sảnh bằng RFID. | **Medium** | Lễ tân, Buồng phòng, Admin |
| **FR-REP-001** | Báo cáo Quản trị Hàng ngày | Tự động kết xuất Daily Flash Report chốt doanh thu các bộ phận, công suất phòng, ADR và RevPAR của ngày hôm qua. | **Critical** | Kiểm toán viên đêm, Ban Giám Đốc |
| **FR-REP-002** | Báo cáo dự báo công suất | Hiển thị biểu đồ dự đoán công suất buồng phòng và doanh thu dự kiến trong 30, 60, 90 ngày tới. | **High** | Ban Giám Đốc, Nhân viên đặt phòng |
| **FR-REP-003** | Xuất dữ liệu tài chính ERP | Kết xuất sổ nhật ký giao dịch tài chính (Ledger Summary) định dạng chuẩn để đồng bộ sang phần mềm kế toán ERP ngoài. | **High** | Kiểm toán viên đêm, Ban Giám Đốc |
| **FR-MGR-001** | Quản lý cấu hình phòng khách sạn | Cấu hình danh sách phòng vật lý, trạng thái phòng và loại phòng. | **High** | Manager |
| **FR-MGR-002** | Quản lý danh mục dịch vụ | Cấu hình bảng giá và danh mục dịch vụ đi kèm khách sạn. | **High** | Manager |

---

## 8. NON FUNCTIONAL REQUIREMENTS (YÊU CẦU PHI CHỨC NĂNG)

### 8.1 Yêu cầu về Bảo mật (Security - NFR-SEC)
* **NFR-SEC-001**: Xác thực tập trung **OAuth2 / OIDC** và mã hóa Token JWT. Kích hoạt MFA cho các tài khoản quản lý và kiểm toán.
* **NFR-SEC-002**: Tuân thủ chuẩn **PCI-DSS Level 1**. Giao dịch thẻ phải được mã hóa (Tokenization) qua cổng thanh toán đối tác, không lưu thẻ thô.
* **NFR-SEC-003**: Mã hóa dữ liệu truyền tải trên mạng sử dụng giao thức bảo mật **HTTPS với TLS 1.3**. Dữ liệu nhạy cảm được mã hóa ở CSDL bằng thuật toán **AES-256 (Encryption at Rest)**.
* **NFR-SEC-004**: Triển khai WAF và giải pháp bảo vệ biên chống tấn công DDoS (Cloudflare Enterprise).

### 8.2 Yêu cầu về Hiệu năng (Performance - NFR-PER)
* **NFR-PER-001**: Tải Room Grid 500 phòng dưới **1.5 giây**. Truy vấn tìm kiếm phòng trống trên Booking Engine dưới **1.0 giây**. API ghi nhận giao dịch tài chính dưới **1.5 giây**.
* **NFR-PER-002**: Đáp ứng tối thiểu **1.000 CCU** đồng thời ở khối Back-office và **10.000 CCU** ở khối Booking Engine hướng ngoại.
* **NFR-PER-003**: Đồng bộ phòng trống từ PMS qua Channel Manager đến OTA dưới **3 giây**. Phát hành thẻ RFID dưới **1.5 giây**.

### 8.3 Yêu cầu về Tính sẵn sàng (Availability - NFR-AVL)
* **NFR-AVL-001**: Độ sẵn sàng hoạt động **Uptime SLA >= 99.9%** hàng năm.
* **NFR-AVL-002**: Triển khai hệ thống đa vùng (Multi-Region) trên Cloud theo mô hình Active-Active tự động chuyển vùng khi có sự cố.
* **NFR-AVL-003**: Lịch bảo trì hệ thống thực hiện vào khung giờ thấp điểm (02:00 - 04:00 sáng Thứ Ba) và phải thông báo trước 48 giờ.

### 8.4 Yêu cầu về Độ tin cậy (Reliability - NFR-REL)
* **NFR-REL-001**: Chỉ số MTBF (Mean Time Between Failures) đạt tối thiểu **5.000 giờ** hoạt động liên tục không lỗi Critical.
* **NFR-REL-002**: Chỉ số MTTR (Mean Time To Repair) khắc phục sự cố dưới **30 phút**.
* **NFR-REL-003**: Đảm bảo thuộc tính **ACID** cho toàn bộ giao dịch tài chính. Tự động rollback khi API thanh toán bị gián đoạn.

### 8.5 Yêu cầu về Khả năng mở rộng (Scalability - NFR-SCA)
* **NFR-SCA-001**: Kubernetes Pods tự động co giãn (Horizontal Pod Autoscaler) khi tài nguyên CPU/Memory vượt ngưỡng **70%**.
* **NFR-SCA-002**: Áp dụng phân tách Read/Write Splitting cho cơ sở dữ liệu. Phục vụ truy vấn báo cáo qua Read Replicas.
* **NFR-SCA-003**: Kiến trúc Microservices phân rã giúp phát triển và nhân rộng các services độc lập.

### 8.6 Yêu cầu về Khả năng bảo trì (Maintainability - NFR-MNT)
* **NFR-MNT-001**: Mã nguồn tuân thủ nguyên lý SOLID, mẫu thiết kế Clean Architecture.
* **NFR-MNT-002**: Tài liệu API đầy đủ theo chuẩn OpenAPI 3.0 (Swagger). Độ phủ Unit Test tối thiểu **80%** cho các logic tài chính.
* **NFR-MNT-003**: Tự động hóa Deploy bằng CI/CD (GitHub Actions) sử dụng Blue-Green Deployment để đảm bảo cập nhật Zero-Downtime.

### 8.7 Yêu cầu về Ghi nhật ký (Logging - NFR-LOG)
* **NFR-LOG-001**: Log tập trung qua ELK Stack hoặc CloudWatch theo thời gian thực.
* **NFR-LOG-002**: Phân chia log thành các cấp độ `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL`.
* **NFR-LOG-003**: Masking làm mờ các trường dữ liệu nhạy cảm (số thẻ, mật khẩu) trước khi ghi log.

### 8.8 Yêu cầu về Sao lưu (Backup - NFR-BKP)
* **NFR-BKP-001**: Tự động Full Backup lúc **03:00 sáng hàng ngày**; Incremental Backup mỗi giờ một lần.
* **NFR-BKP-002**: Lưu trữ backup 30 ngày (hàng ngày), 12 tháng (hàng tháng), 5 năm (hàng năm).
* **NFR-BKP-003**: File backup mã hóa AES-256 và nhân bản khác vùng địa lý (Cross-Region Replication).

### 8.9 Yêu cầu về Phục hồi (Recovery - NFR-REC)
* **NFR-REC-001**: Chỉ số điểm phục hồi dữ liệu **RPO < 1 giờ**.
* **NFR-REC-002**: Chỉ số thời gian khôi phục hệ thống **RTO < 4 giờ**.
* **NFR-REC-003**: Diễn tập quy trình khôi phục dữ liệu thảm họa DR định kỳ 1 năm 2 lần.

### 8.10 Yêu cầu về Kiểm toán (Audit - NFR-AUD)
* **NFR-AUD-001**: Log nhật ký kiểm toán bất biến (Immutable Audit Trail), cấm sửa/xóa kể cả quyền Admin.
* **NFR-AUD-002**: Log ghi nhận đầy đủ: Timestamp, User ID, Client IP, Action Type, Old/New Value.
* **NFR-AUD-003**: Thời hạn lưu trữ audit log tối thiểu **7 năm**.

---

## 9. ASSUMPTIONS (CÁC GIẢ ĐỊNH & PHỤ THUỘC NGOẠI VI)

### 9.1 Giả định hệ thống (System Assumptions)
* **Khả năng tiếp nhận công nghệ**: Giả định khách hàng đầu cuối và nhân viên khách sạn có thiết bị di động kết nối internet ổn định và hiểu biết cơ bản để tương tác với giao diện Web/App và Kiosk tự phục vụ.
* **Độ ổn định của thiết bị phần cứng**: Giả định thiết bị đọc/ghi thẻ từ RFID sảnh và máy quét giấy tờ hộ chiếu OCR hoạt động ổn định, có sẵn driver kết nối tương thích với Local Agent của hệ thống.

### 9.2 Phụ thuộc ngoại vi (External Dependencies)
* **API cổng thanh toán trực tuyến**: Hệ thống phụ thuộc vào sự sẵn sàng và tốc độ xử lý của Stripe/VNPay để thực hiện giao dịch thanh toán cọc hoặc thanh toán hóa đơn. Nếu cổng thanh toán gặp sự cố, hệ thống không thể xác nhận booking sang trạng thái đảm bảo trực tuyến.
* **Dịch vụ Hóa đơn điện tử và Tổng cục Thuế**: Quy trình xuất hóa đơn GTGT phụ thuộc vào API của nhà cung cấp giải pháp e-Invoice và cổng phản hồi của Cơ quan Thuế để cấp mã xác thực.
* **Đồng bộ Channel Manager**: Việc phân phối phòng trống và giá phụ thuộc vào tính ổn định của API kết nối với SiteMinder/YieldPlanet.

---

## 10. BUSINESS RULES (CÁC QUY TẮC NGHIỆP VỤ HỆ THỐNG)

| ID | Tên quy tắc nghiệp vụ | Mô tả đặc tả chi tiết | Phân hệ áp dụng | Loại ràng buộc |
| :--- | :--- | :--- | :--- | :--- |
| **BR-RES-001** | Tránh trùng phòng đặt | Không được phép gán số phòng vật lý đã có khách đang ở (OC) hoặc đã được gán cho một booking khác có ngày lưu trú trùng lặp. | Đặt phòng | **Hard** (Hệ thống chặn) |
| **BR-RES-002** | Đặt nhiều phòng | Một hồ sơ khách hàng (Guest Profile) hoặc tài khoản khách có quyền tạo một booking đặt nhiều phòng khác nhau trong cùng một khoảng thời gian. | Đặt phòng | **Soft** (Cho phép) |
| **BR-RES-003** | Ràng buộc ngày lưu trú | Ngày trả phòng (Check-out Date) bắt buộc phải xảy ra sau ngày nhận phòng (Check-in Date) tối thiểu 1 ngày. | Đặt phòng | **Hard** (Hệ thống chặn) |
| **BR-RES-004** | Kiểm soát Overbooking | Cho phép bán vượt công suất phòng (Overbooking) tối đa 5% tổng số phòng của từng loại phòng, và chỉ thực hiện được bởi nhân viên có quyền Manager Override. | Đặt phòng | **Hard** (Cần phê duyệt) |
| **BR-RES-005** | Giá sàn phòng | Nhân viên không được phép nhập giá phòng bán thực tế thấp hơn mức giá sàn (Rate Floor) đã được cấu hình cho loại phòng đó, trừ khi có phê duyệt ghi đè. | Đặt phòng | **Hard** (Cần phê duyệt) |
| **BR-RES-006** | Phí phạt hủy phòng muộn | Nếu khách hủy booking muộn (Late Cancellation - dưới 24 giờ trước giờ check-in), hệ thống tự động post phí phạt 100% tiền đêm đầu tiên vào Folio. | Đặt phòng | **Hard** (Hệ thống tự động) |
| **BR-RES-007** | Khách tự hủy đặt phòng trực tuyến | Khách hàng được tự hủy đặt phòng trực tuyến trên Web/App trước giờ Check-in tối thiểu 24 giờ. Trạng thái booking chuyển sang `Cancelled`. | Đặt phòng | **Hard** (Hệ thống tự động) |
| **BR-FO-001** | Trạng thái phòng khi Check-in | Lễ tân chỉ được phép làm thủ tục Check-in cho khách vào các phòng vật lý đang ở trạng thái Trống Sạch (VC - Vacant Clean) trên Room Grid. | Lễ tân | **Hard** (Hệ thống chặn) |
| **BR-FO-002** | Đặt cọc bắt buộc | Khách lẻ (FIT) bắt buộc phải có thông tin đặt cọc (Deposit) tiền mặt hoặc thẻ tín dụng bảo lãnh trước khi Check-in, trừ khi có cờ miễn cọc của GM. | Lễ tân | **Hard** (Cần phê duyệt) |
| **BR-FO-003** | Check-out trước Check-in | Trạng thái của một booking không thể chuyển sang "Đã trả phòng" (Checked-out) nếu chưa thực hiện bước "Đã nhận phòng" (Checked-in). | Lễ tân | **Hard** (Hệ thống chặn) |
| **BR-FO-004** | Đăng ký lưu trú bắt buộc | Quy trình Check-in không thể hoàn tất nếu booking thiếu thông tin định danh (số hộ chiếu/CCCD, họ tên, quốc tịch) của ít nhất một khách chính lưu trú. | Lễ tân | **Hard** (Hệ thống chặn) |
| **BR-FO-005** | Tính phí Check-out muộn | Khách check-out muộn sau 12:00 PM sẽ tự động bị áp phí check-out muộn: 30% giá phòng trước 15:00, 50% trước 18:00, và 100% sau 18:00. | Lễ tân / Thu ngân | **Soft** (Có thể ghi đè) |
| **BR-FO-006** | Tính phí Check-in sớm | Khách check-in sớm trước 06:00 AM tính 100% tiền phòng đêm trước; check-in từ 06:00 AM đến 10:00 AM tính 50% giá phòng. | Lễ tân / Thu ngân | **Soft** (Có thể ghi đè) |
| **BR-FO-007** | Ưu tiên gán phòng VIP | Khách hàng thuộc hạng thành viên Gold hoặc Platinum bắt buộc phải được ưu tiên gán các phòng trống sạch VC có vị trí tốt nhất (view đẹp, tầng cao). | Lễ tân | **Soft** (Cảnh báo) |
| **BR-FO-008** | Khách tự đổi phòng trực tuyến | Khách hàng được tự đổi số phòng cùng hạng thông qua Web/App trước khi làm thủ tục Check-in (Pre-check-in) nếu phòng mới ở trạng thái VC. | Lễ tân | **Hard** (Hệ thống chặn) |
| **BR-PAY-001** | Số dư Folio khi Check-out | Số dư (Balance) của tất cả Folio liên kết với phòng phải bằng đúng 0.00 VND thì hệ thống mới cho phép đóng Folio và hoàn tất Check-out. | Thu ngân | **Hard** (Hệ thống chặn) |
| **BR-PAY-002** | Hạn mức tín dụng phòng | Mỗi phòng có một hạn mức nợ (Credit Limit). Nếu số dư nợ trên Folio vượt quá hạn mức này, hệ thống tự động gán cờ `NO_POST` khóa chức năng Room Charge tại các POS. | Thu ngân / POS | **Hard** (Hệ thống chặn) |
| **BR-PAY-003** | Bất biến hóa đơn đã đóng | Folio đã đóng và hóa đơn GTGT điện tử đã cấp mã số thuế thì không được phép sửa đổi hoặc xóa dữ liệu. Mọi sửa đổi phải dùng nghiệp vụ hoàn phí (Rebate). | Thu ngân | **Hard** (Hệ thống chặn) |
| **BR-PAY-004** | Phân quyền hoàn tiền | Giao dịch hoàn tiền (Refund) hoặc giảm trừ hóa đơn (Rebate) vượt quá 1.000.000 VND bắt buộc phải được xác thực bằng mật khẩu của Quản lý sảnh (FOM) trở lên. | Thu ngân | **Hard** (Cần phê duyệt) |
| **BR-HK-001** | Trạng thái phòng sau Check-out | Ngay khi Lễ tân bấm xác nhận Check-out giải phóng phòng trên PMS, trạng thái phòng đó phải tự động chuyển thành Trống Bẩn (VD - Vacant Dirty). | Buồng phòng | **Hard** (Hệ thống tự động) |
| **BR-HK-002** | Giới hạn khai báo minibar | Số lượng vật phẩm tiêu dùng minibar do nhân viên buồng phòng nhập vào app không được vượt quá số lượng tồn kho định mức của phòng đó. | Buồng phòng | **Hard** (Hệ thống chặn) |
| **BR-HK-003** | Loại trừ phòng hỏng (OOO) | Các phòng đang để trạng thái Out of Order (OOO - Hỏng hóc dừng bán) phải bị loại trừ ra khỏi quỹ phòng khả dụng để bán trên toàn hệ thống. | Buồng phòng | **Hard** (Hệ thống tự động) |
| **BR-HK-004** | Giữ phòng dừng dịch vụ (OOS) | Các phòng Out of Service (OOS - Dừng dịch vụ tạm thời) vẫn nằm trong quỹ phòng tính toán công suất, nhưng bị chặn không cho gán check-in. | Buồng phòng | **Hard** (Hệ thống chặn) |
| **BR-NA-001** | Điều kiện chạy Night Audit | Không được phép chạy tiến trình Kiểm toán đêm nếu hệ thống còn các booking trạng thái "Chờ đến" hoặc "Chờ đi" chưa xử lý. | Kiểm toán đêm | **Hard** (Hệ thống chặn) |
| **BR-NA-002** | Chốt ca thu ngân trước chốt ngày | Tất cả các điểm bán hàng POS và quầy thu ngân sảnh phải thực hiện chốt ca (Shift Close) trước khi tiến trình Night Audit bắt đầu. | Kiểm toán đêm | **Hard** (Hệ thống chặn) |
| **BR-NA-003** | Bước nhảy ngày hệ thống | Ngày vận hành hệ thống (System Date) chỉ được phép dịch chuyển tiến lên đúng +1 ngày sau khi tiến trình Night Audit báo cáo thành công 100%. | Kiểm toán đêm | **Hard** (Hệ thống tự động) |
| **BR-ADM-001** | Vết kiểm toán bất biến | Nhật ký kiểm toán (Audit Trail) ghi nhận lịch sử thay đổi dữ liệu của nhân viên phải được ghi vào bộ nhớ bất biến, cấm sửa đổi hoặc xóa bởi mọi tài khoản. | Quản trị hệ thống | **Hard** (Hệ thống chặn) |

---

## 11. USER STORIES & ACCEPTANCE CRITERIA (TIÊU CHÍ NGHIỆM THU)

* **US-001 (Tìm kiếm phòng trống)**:
  * *Mẫu:* **As a** Khách hàng, **I want to** tìm kiếm phòng trống theo ngày lưu trú và số lượng người, **So that** tôi có thể xem giá phòng khả dụng và lựa chọn đặt phòng phù hợp.
  * *Tiêu chí nghiệm thu (AC):*
    * **AC1.1**: Người dùng nhập ngày đến, ngày đi, số người (người lớn, trẻ em).
    * **AC1.2**: Hệ thống trả về danh sách các hạng phòng trống kèm hình ảnh, mô tả tiện ích và giá thực tế tương ứng.
    * **AC1.3**: Nếu không còn phòng trống, hệ thống hiển thị thông báo "Hạng phòng này đã hết trong ngày đã chọn" và gợi ý ngày khác.
    * **AC1.4**: Thời gian phản hồi kết quả tìm kiếm phải dưới 1.0 giây.
* **US-002 (Thanh toán cọc online)**:
  * *Mẫu:* **As a** Khách hàng, **I want to** thanh toán tiền cọc phòng trực tuyến qua cổng thanh toán bảo mật, **So that** đặt phòng của tôi được hệ thống xác nhận và bảo đảm giữ phòng.
  * *AC:*
    * **AC2.1**: Hệ thống chuyển hướng khách sang cổng thanh toán Stripe/VNPay sau khi xác nhận thông tin đặt phòng.
    * **AC2.2**: Giao dịch thành công phải sinh mã giao dịch duy nhất, gửi email xác nhận tự động kèm mã QR đặt phòng cho khách trong 5 giây.
    * **AC2.3**: Folio phòng được khởi tạo trên PMS và tự động ghi nợ âm khoản tiền cọc.
    * **AC2.4**: Quỹ phòng trống trên PMS và các kênh OTA giảm đi tương ứng ngay lập tức.
* **US-003 (Lễ tân Check-in)**:
  * *Mẫu:* **As a** Nhân viên Lễ tân, **I want to** thực hiện Check-in cho khách bằng cách quét giấy tờ tùy thân OCR và gán phòng, **So that** quy trình nhận phòng diễn ra nhanh chóng, tránh khách chờ đợi lâu.
  * *AC:*
    * **AC3.1**: Lễ tân đặt CCCD/Hộ chiếu lên máy quét, hệ thống tự động điền thông tin Profile trong vòng 5 giây với độ chính xác >95%.
    * **AC3.2**: Thuật toán đề xuất các phòng Trống Sạch (VC) khớp với sở thích của khách hàng.
    * **AC3.3**: Hệ thống gọi API ghi thẻ khóa phòng RFID thành công với đúng số phòng vật lý và thời gian lưu trú.
    * **AC3.4**: Trạng thái phòng đổi sang OC trên Room Grid; điện thoại phòng (PBX) và truyền hình (IPTV) được kích hoạt.
* **US-004 (Lễ tân Check-out)**:
  * *Mẫu:* **As a** Nhân viên Lễ tân, **I want to** thực hiện Check-out và thanh toán hóa đơn cho khách lưu trú, **So that** tôi có thể giải phóng phòng trống cho bộ phận dọn dẹp và thu hồi công nợ.
  * *AC:*
    * **AC4.1**: PMS tự động tổng hợp đầy đủ tất cả chi phí phát sinh (tiền phòng, minibar, F&B, Spa) vào Folio khách.
    * **AC4.2**: Hệ thống khấu trừ khoản tiền đặt cọc (Deposit) đã đóng trước đó ra khỏi tổng hóa đơn chi tiêu.
    * **AC4.3**: Hệ thống chặn không cho bấm Check-out nếu số dư Folio khác 0.
    * **AC4.4**: Khi số dư về 0 và bấm Check-out, trạng thái phòng tự động chuyển sang VD, vô hiệu hóa thẻ từ phòng và gửi thông báo dọn phòng.
* **US-005 (Buồng phòng di động)**:
  * *Mẫu:* **As a** Nhân viên Buồng phòng, **I want to** xem danh sách phòng được phân công dọn dẹp và cập nhật trạng thái phòng dọn xong trên app di động, **So that** lễ tân biết phòng đã sẵn sàng để đón khách mới.
  * *AC:*
    * **AC5.1**: Giao diện hiển thị danh sách phòng cần dọn dẹp sắp xếp theo thứ tự ưu tiên (phòng khách VIP, phòng check-in sớm).
    * **AC5.2**: Cho phép chuyển trạng thái phòng sang "Đang dọn" (Cleaning) và "Trống Sạch" (VC) với tối đa 2 thao tác chạm.
    * **AC5.3**: Hỗ trợ nhập số lượng đồ uống minibar khách đã dùng để lập tức post phí vào Folio phòng thời gian thực.
    * **AC5.4**: Cho phép đính kèm hình ảnh khi tạo phiếu báo hỏng thiết bị phòng (Maintenance ticket).
* **US-006 (Kiểm toán đêm chốt ngày)**:
  * *Mẫu:* **As a** Kiểm toán viên đêm, **I want to** thực hiện tiến trình Kiểm toán đêm chốt ngày tài chính cũ và chuyển sang ngày vận hành mới, **So that** số liệu kế toán hàng ngày được khóa sổ chính xác, không bị sai lệch.
  * *AC:*
    * **AC6.1**: Hệ thống chạy kiểm tra biên và chặn tiến trình nếu còn khách "chờ đến" hoặc "chờ đi" chưa xử lý.
    * **AC6.2**: Hệ thống tự động chốt doanh thu các phòng đang ở (Post Room Charge) cho đêm hiện tại.
    * **AC6.3**: Thực hiện sao lưu dữ liệu tự động (Database Snapshot) và dịch chuyển ngày vận hành hệ thống (System Date) lên +1 ngày.
    * **AC6.4**: Tự động kết xuất và gửi báo cáo Daily Flash Report dạng PDF về email của GM và Kế toán trưởng.
* **US-007 (Tạo đặt phòng đoàn)**:
  * *Mẫu:* **As a** Nhân viên Đặt phòng, **I want to** tạo một đặt phòng đoàn (GIT) áp dụng hợp đồng giá doanh nghiệp (Corporate Contract), **So that** tôi có thể quản lý lịch lưu trú và công nợ của công ty lữ hành đối tác hiệu quả.
  * *AC:*
    * **AC7.1**: Người dùng chọn Profile của công ty lữ hành, hệ thống tự động áp dụng khung giá ưu đãi theo hợp đồng hợp lệ.
    * **AC7.2**: Cho phép chọn nhiều phòng thuộc các hạng phòng khác nhau và gán chung một mã đoàn (Group Code).
    * **AC7.3**: Cho phép cấu hình quy tắc định tuyến (Billing Routing Rules) tự động chuyển tiền phòng về Master Folio của công ty.
* **US-008 (Dashboard BI Quản trị)**:
  * *Mẫu:* **As a** Ban Giám Đốc, **I want to** xem biểu đồ phân tích trực quan các chỉ số công suất phòng, ADR và RevPAR theo thời gian thực, **So that** tôi có thể đưa ra các quyết định điều chỉnh giá phòng tối ưu hóa doanh thu.
  * *AC:*
    * **AC8.1**: Dashboard BI hiển thị các biểu đồ (line chart, bar chart) trực quan hóa OCC, ADR, RevPAR thời gian thực.
    * **AC8.2**: Hỗ trợ bộ lọc dữ liệu theo khoảng thời gian, loại phòng và nguồn khách.
    * **AC8.3**: Dữ liệu báo cáo chốt khớp 100% với số liệu từ tiến trình Kiểm toán đêm.
* **US-009 (IT Admin cấu hình phân quyền)**:
  * *Mẫu:* **As a** Quản trị viên IT, **I want to** cấu hình quyền hạn truy cập của nhân viên trên hệ thống dựa trên vai trò làm việc, **So that** tôi có thể đảm bảo an toàn thông tin và ngăn ngừa các thao tác trái phép.
  * *AC:*
    * **AC9.1**: Admin chọn một tài khoản nhân viên và gán vào một vai trò định sẵn.
    * **AC9.2**: Hệ thống chặn quyền truy cập đối với các tính năng hoặc nút bấm ngoài phạm vi vai trò (403 Forbidden).
    * **AC9.3**: Mọi hành động của nhân viên được ghi log tự động vào nhật ký Audit Trail.
    * **AC9.4**: Hệ thống lưu trữ audit log dưới dạng bất biến (Read-only).
* **US-010 (Quản lý hợp đồng giá doanh nghiệp)**:
  * *Mẫu:* **As a** Nhân viên Đặt phòng, **I want to** cấu hình các điều khoản hợp đồng giá của đại lý lữ hành (TA) và doanh nghiệp, **So that** khi tạo booking cho các đối tác này, giá phòng sẽ tự động áp dụng giá thỏa thuận.
  * *AC:*
    * **AC10.1**: Cho phép tải và quản lý file hợp đồng số dạng PDF đính kèm Corporate Profile.
    * **AC10.2**: Thiết lập các mốc giá theo mùa vụ riêng biệt của từng hợp đồng và mức trần công nợ của doanh nghiệp.
    * **AC10.3**: Hệ thống tự động điền giá thỏa thuận khi tạo đặt phòng liên kết với mã đối tác hợp lệ.
* **US-011 (RFID Chấm công)**:
  * *Mẫu:* **As a** Nhân viên Lễ tân / Buồng phòng, **I want to** thực hiện chấm công Check-in/Check-out ca trực bằng cách quét thẻ RFID nhân viên tại sảnh, **So that** giờ làm việc thực tế được ghi nhận chính xác vào bảng lương.
  * *AC:*
    * **AC11.1**: Nhân viên quét thẻ nhân viên lên đầu đọc RFID đặt tại sảnh.
    * **AC11.2**: Hệ thống ghi nhận chính xác Timestamp clock-in/out và ID ca trực khớp với bảng phân ca.
    * **AC11.3**: Hiển thị thông báo chào mừng kèm theo tên nhân viên và ca trực trên màn hình đầu đọc sảnh.
    * **AC11.4**: IT Admin có thể xem báo cáo chấm công trực tuyến.
* **US-012 (Khách tự hủy đặt phòng trực tuyến)**:
  * *Mẫu:* **As a** Khách hàng, **I want to** tự hủy đặt phòng (Booking) trực tuyến trên Web/App, **So that** tôi có thể chủ động hủy chuyến đi mà không cần gọi tổng đài sảnh.
  * *AC:*
    * **AC12.1**: Khách hàng mở chi tiết Booking trên Web/App, chọn nút "Hủy đặt phòng".
    * **AC12.2**: Hệ thống tự đối chiếu chính sách hủy phòng (BR-RES-006) và hiển thị số tiền phạt/hoàn lại dự kiến.
    * **AC12.3**: Khi khách xác nhận, trạng thái booking chuyển thành `Cancelled`, phòng được giải phóng ngay lập tức.
    * **AC12.4**: Tự động gửi email xác nhận hủy đặt phòng kèm thông tin hoàn tiền cho khách hàng.
* **US-013 (Khách tự chọn đổi số phòng trực tuyến)**:
  * *Mẫu:* **As a** Khách hàng, **I want to** tự lựa chọn đổi sang phòng trống sạch (VC) cùng hạng trực tuyến trên Web/App, **So that** tôi có thể lựa chọn phòng có số phòng đẹp hoặc tầng mong muốn trước khi nhận phòng.
  * *AC:*
    * **AC13.1**: Khách hàng đã check-in trực tuyến (Pre-check-in) có thể chọn chức năng "Đổi số phòng".
    * **AC13.2**: Hệ thống hiển thị danh sách phòng VC cùng hạng phòng đã đặt và sơ đồ tầng trực quan.
    * **AC13.3**: Khi khách chọn số phòng mới, hệ thống đổi phòng lập tức và vô hiệu hóa số phòng cũ.
    * **AC13.4**: Cập nhật thông tin số phòng mới trên Guest App và Kiosk sảnh.

---

## 12. USE CASES (ĐẶC TẢ CHI TIẾT USE CASES)

### Phân hệ Đặt phòng (Reservation & Distribution - UC-RES)

#### UC-RES-01: Tra cứu phòng trống & Tạo đặt phòng lẻ (FIT)
* **Actor:** Khách hàng (Guest), Nhân viên Đặt phòng (Reservation Agent), Lễ tân (GSA)
* **Preconditions:** Quỹ phòng trống (Inventory) và bảng giá phòng (Rate Codes) đã được cấu hình trên hệ thống.
* **Main Flow:**
  1. Tác nhân nhập thông tin tìm kiếm bao gồm: Ngày nhận phòng, Ngày trả phòng, số lượng khách.
  2. Hệ thống kiểm tra quỹ phòng trống trong khoảng thời gian yêu cầu và trả về danh sách các loại phòng khả dụng kèm giá tiền tương ứng.
  3. Tác nhân chọn loại phòng muốn đặt và nhập thông tin khách hàng.
  4. Tác nhân chọn hình thức bảo đảm đặt phòng (quẹt thẻ tín dụng bảo lãnh hoặc chuyển khoản cọc).
  5. Hệ thống khởi tạo hồ sơ Booking ở trạng thái `Pending` và cấp mã đặt phòng duy nhất.
  6. Hệ thống tự động giữ phòng tạm thời trong 30 phút và gửi link thanh toán cọc sang email của khách.
* **Alternative Flow:**
  * **Alt-1: Khách hàng cũ đã có Guest Profile**: Nhập số điện thoại hoặc số hộ chiếu. Hệ thống tự động điền thông tin và hiển thị mức giảm giá theo hạng thành viên.
* **Exception Flow:**
  * **Ex-1: Hết phòng trống khả dụng**: Hiển thị thông báo hết phòng và gợi ý ngày khác hoặc hạng phòng khác.
  * **Ex-2: Lỗi thanh toán**: Khách thanh toán cọc bị lỗi hoặc quá hạn 30 phút không thanh toán -> Tự động hủy booking tạm thời, giải phóng phòng.

#### UC-RES-02: Đặt phòng cho Khách đoàn (GIT Booking)
* **Actor:** Nhân viên Đặt phòng, Lễ tân
* **Preconditions:** Hồ sơ công ty lữ hành hoặc đối tác liên quan đã được tạo trên hệ thống.
* **Main Flow:**
  1. Chọn hồ sơ Travel Agent/Corporate và chọn loại đặt phòng Đoàn.
  2. Cấu hình thông tin chung: Mã đoàn (Group Code), Ngày đến/đi, Tên Trưởng đoàn.
  3. Chọn số lượng phòng và loại phòng cần đặt cho đoàn.
  4. Hệ thống kiểm tra quỹ phòng khả dụng. Nếu đủ, hệ thống tạo một Master Booking ID đại diện.
  5. Thiết lập quy tắc định tuyến hóa đơn (Routing Rules) chuyển tiền phòng về Master Folio, các chi phí dịch vụ giữ lại ở Folio riêng.
  6. Nhập danh sách thành viên (Rooming List) để gán tên từng khách vào từng số phòng.
* **Alternative Flow:**
  * **Alt-1: Chưa có danh sách thành viên**: Có thể để trống tên khách (để tên tạm) và cập nhật bổ sung trước ngày đoàn đến tối thiểu 24 giờ.
* **Exception Flow:**
  * **Ex-1: Không đủ phòng trống cho đoàn**: Hệ thống báo lỗi đỏ và chặn tạo đoàn. Phải điều chỉnh hạng phòng hoặc nâng hạng phòng.

#### UC-RES-03: Cấu hình Bảng giá & Luật giá động (Yield Management)
* **Actor:** Nhân viên Đặt phòng, Ban Giám Đốc
* **Preconditions:** Danh mục loại phòng đã được định nghĩa trên hệ thống.
* **Main Flow:**
  1. Truy cập phân hệ Cấu hình bảng giá (Rate Management).
  2. Tạo mới mã giá và thiết lập giá cơ bản (Base Rate) theo mùa.
  3. Cấu hình quy tắc giá động (Yield Rules): ví dụ tự động tăng giá phòng thêm 10% khi công suất phòng tổng đạt trên 80%.
  4. Hệ thống ghi nhận các luật giá và tự động áp dụng khi khách tra cứu.
* **Alternative Flow:**
  * **Alt-1: Áp dụng khuyến mãi cụ thể (Promo Code)**: Cấu hình mã giảm giá giới hạn số lượng và thời gian hiệu lực.
* **Exception Flow:**
  * **Ex-1: Xung đột luật giá**: Cấu hình luật mới trùng lặp hoặc mâu thuẫn luật cũ -> Hệ thống chặn lưu và yêu cầu chỉnh sửa luật cũ.

#### UC-RES-04: Đồng bộ Kênh bán phòng OTA (Channel Manager Sync)
* **Actor:** Hệ thống PMS (tự động), Quản trị viên IT (khi xử lý lỗi)
* **Preconditions:** Tích hợp API giữa PMS và Channel Manager đã được thiết lập thành công.
* **Main Flow:**
  1. Phát sinh sự kiện thay đổi quỹ phòng trống hoặc thay đổi bảng giá phòng trên PMS.
  2. PMS đóng gói dữ liệu quỹ phòng trống cập nhật và bảng giá phòng mới theo định dạng XML chuẩn HTNG.
  3. PMS gửi yêu cầu API cập nhật sang máy chủ của Channel Manager.
  4. Channel Manager phản hồi kết quả cập nhật thành công (HTTP 200 OK) và tự cập nhật lên các sàn OTA.
* **Exception Flow:**
  * **Ex-1: Lỗi kết nối API**: PMS đưa vào hàng đợi tin nhắn thực hiện tự động gọi lại tối đa 5 lần. Nếu vẫn lỗi, gửi cảnh báo đỏ cho IT Admin để đóng phòng thủ công trên OTA.

#### UC-RES-05: Khách tự hủy đặt phòng trực tuyến (Guest Online Cancellation)
* **Actor:** Khách hàng (Guest)
* **Preconditions:** Đặt phòng ở trạng thái `Pending` hoặc `Confirmed`.
* **Main Flow:**
  1. Khách hàng đăng nhập vào cổng thông tin Web/App và chọn danh sách đặt phòng cá nhân.
  2. Khách chọn Đặt phòng muốn hủy và nhấn nút "Hủy đặt phòng".
  3. Hệ thống kiểm tra điều kiện hủy đặt phòng và chính sách hoàn tiền cọc (BR-RES-006 & BR-RES-007).
  4. Hệ thống hiển thị thông báo xác nhận kèm số tiền phí phạt hủy (nếu có) và số tiền hoàn lại dự kiến.
  5. Khách xác nhận đồng ý hủy.
  6. Hệ thống cập nhật trạng thái Booking thành `Cancelled`, giải phóng phòng trống trên PMS và các sàn OTA.
  7. Hệ thống tự động kích hoạt tiến trình hoàn tiền (Refund Process) sang PaymentService.
  8. Hệ thống gửi email thông báo xác nhận hủy đặt phòng cho khách.
* **Alternative Flow:**
  * **Alt-1: Hủy booking quá giờ bảo đảm (Late Cancellation)**: Nếu hủy đặt phòng trễ (dưới 24h trước check-in), hệ thống tính phí phạt 100% đêm đầu tiên, tự động trừ vào số tiền đặt cọc đã đóng trước đó.
* **Exception Flow:**
  * **Ex-1: Lỗi cổng thanh toán hoàn tiền**: Hệ thống vẫn cập nhật trạng thái thành `Cancelled`, ghi nhận dòng công nợ hoàn tiền ở trạng thái "Chờ xử lý thủ công" và gửi thông báo cho kế toán trưởng.

---

### Phân hệ Lễ tân & Vận hành phòng (Front Office - UC-FO)

#### UC-FO-01: Nhận phòng Khách lẻ / Đoàn (Guest Check-in)
* **Actor:** Lễ tân, Khách hàng (tại Kiosk)
* **Preconditions:** Booking ở trạng thái `Confirmed`, phòng gán đang ở trạng thái Trống Sạch (VC).
* **Main Flow:**
  1. Lễ tân quét mã QR đặt phòng để mở hồ sơ Booking trên PMS.
  2. Lễ tân quét giấy tờ tùy thân của khách qua máy quét OCR. Hệ thống tự động nhận diện và cập nhật vào Profile.
  3. Hệ thống chạy thuật toán gán phòng tối ưu và hiển thị số phòng vật lý (ví dụ: Phòng 302).
  4. Khách ký xác nhận Phiếu đăng ký lưu trú trên tablet.
  5. Thu cọc (tiền mặt/thẻ), ghi nhận vào Folio phòng.
  6. Ghi thẻ khóa phòng từ RFID qua API tích hợp.
  7. Hoàn tất Check-in. Trạng thái phòng chuyển sang `Checked In`, kích hoạt PBX và IPTV phòng.
* **Alternative Flow:**
  * **Alt-1: Khách hàng tự Check-in tại Kiosk**: Khách tự quét QR, quét giấy tờ, quét thẻ thanh toán cọc và nhận thẻ khóa phòng tự động tại máy Kiosk sảnh.
* **Exception Flow:**
  * **Ex-1: Phòng chưa sẵn sàng (VD hoặc OOO)**: Chọn phòng VC khác cùng hạng trên sơ đồ Grid, hoặc Pre-check-in trước không thẻ, gửi đồ và gắn cờ ưu tiên dọn dẹp cho phòng đó.

#### UC-FO-02: Chuyển phòng lưu trú & Nâng hạng (Room Move & Upgrade)
* **Actor:** Lễ tân, Ban Giám Đốc (khi cần phê duyệt override)
* **Preconditions:** Khách đang ở trạng thái lưu trú hoạt động (`Checked In`). Phòng mới chuyển tới phải ở trạng thái Trống Sạch (VC).
* **Main Flow:**
  1. Lễ tân tiếp nhận yêu cầu chuyển phòng từ khách.
  2. Lễ tân mở sơ đồ phòng Grid, chọn chức năng Chuyển phòng (Room Move).
  3. Lễ tân chọn phòng mới cùng hạng trên Room Grid.
  4. Hệ thống tự động chốt chi phí phòng cũ, chuyển giao dịch tài chính sang Folio phòng mới.
  5. Phát thẻ khóa phòng mới và vô hiệu hóa thẻ phòng cũ.
  6. Trạng thái phòng cũ chuyển sang VD (Trống bẩn), phòng mới chuyển sang `Checked In`.
* **Alternative Flow:**
  * **Alt-1: Nâng hạng phòng (Upgrade)**: Chọn phòng mới ở hạng cao hơn. Nếu nâng hạng miễn phí cho khách VIP, nhập mã phê duyệt (Manager Override) để giảm giá chênh lệch về 0.
* **Exception Flow:**
  * **Ex-1: API kết nối khóa từ bị lỗi**: Hệ thống hiển thị cảnh báo. Lễ tân phải sử dụng thẻ Master của kỹ thuật để reset ổ khóa phòng cũ bằng tay để bảo đảm an toàn.

#### UC-FO-03: Ghi nhận và Điều phối yêu cầu dịch vụ phòng
* **Actor:** Lễ tân, Nhân viên buồng phòng, Nhân viên kỹ thuật
* **Preconditions:** Phòng của khách đang ở trạng thái có khách lưu trú (`Checked In`).
* **Main Flow:**
  1. Khách gọi điện yêu cầu dịch vụ phòng hoặc gửi yêu cầu qua Guest Mobile App.
  2. Lễ tân nhập phiếu yêu cầu (Service Request Ticket) trên giao diện PMS.
  3. Hệ thống tự động gửi thông báo đẩy đến di động của nhân viên buồng phòng trực ca ở khu vực tương ứng.
  4. Nhân viên nhận việc trên app, mang đồ đến và chạm nút "Start Cleaning/In Progress".
  5. Bàn giao đồ xong, nhân viên chạm nút "Completed" trên app di động.
  6. Hệ thống đóng ticket yêu cầu và cập nhật trạng thái hoàn tất lên lễ tân.
* **Alternative Flow:**
  * **Alt-1: Yêu cầu sửa chữa thiết bị (Bảo trì)**: Hệ thống tự động gửi ticket sang app di động của nhân viên Kỹ thuật thay vì Buồng phòng.
* **Exception Flow:**
  * **Ex-1: Quá hạn xử lý yêu cầu (SLA Breach)**: Sau 15 phút chưa có nhân viên nhận việc, PMS phát chuông cảnh báo đỏ tại lễ tân và gửi SMS thông báo cho Trưởng bộ phận.

#### UC-FO-04: Khách tự đổi phòng trực tuyến (Guest Self-Service Room Move)
* **Actor:** Khách hàng (Guest)
* **Preconditions:** Đặt phòng ở trạng thái `Confirmed` (Khách đã check-in trực tuyến trước - Pre-check-in nhưng chưa nhận phòng vật lý).
* **Main Flow:**
  1. Khách hàng mở ứng dụng di động (Guest App) hoặc truy cập Web, mở chi tiết đặt phòng hiện tại.
  2. Khách chọn chức năng "Đổi số phòng".
  3. Hệ thống truy vấn danh sách các phòng vật lý đang ở trạng thái Trống Sạch (VC) thuộc cùng hạng phòng khách đã đặt.
  4. Hệ thống hiển thị sơ đồ tầng trực quan kèm vị trí các phòng trống để khách tự chọn.
  5. Khách chọn một số phòng vật lý mới và nhấn xác nhận.
  6. Hệ thống thực hiện chuyển gán phòng vật lý trên sơ đồ phòng PMS, vô hiệu hóa việc gán phòng cũ.
  7. Ứng dụng Guest App cập nhật số phòng mới và mã QR/Bluetooth key khóa phòng tương ứng.
* **Exception Flow:**
  * **Ex-1: Phòng vừa bị gán cho người khác trong quá trình chọn**: Hệ thống hiển thị thông báo "Phòng này vừa được gán, vui lòng chọn số phòng khác" và tải lại sơ đồ phòng trống.

---

### Phân hệ Buồng phòng & Bảo trì (Housekeeping - UC-HK)

#### UC-HK-01: Phân công dọn dẹp & Cập nhật trạng thái phòng di động
* **Actor:** Giám sát buồng phòng (Supervisor), Nhân viên buồng phòng (Housekeeper)
* **Preconditions:** Ca làm việc của nhân viên buồng phòng đã được kích hoạt trên hệ thống.
* **Main Flow:**
  1. Giám sát mở giao diện "Phân công dọn phòng" hiển thị danh sách phòng bẩn cần dọn trong ngày.
  2. Giám sát nhấn nút "Phân công tự động", dựa trên Zone tự động chia đều danh sách cho các nhân viên đang trực.
  3. Nhân viên buồng phòng mở Housekeeping App trên di động để xem danh sách phòng được giao.
  4. Khi vào dọn phòng cụ thể, nhân viên chạm nút "Start Cleaning".
  5. dọn xong, nhân viên chạm nút "Set Clean". Hệ thống đổi trạng thái phòng thành VC (hoặc OC đối với phòng đang có khách ở).
* **Alternative Flow:**
  * **Alt-1: Giám sát phân công thủ công**: Kéo thả điều chỉnh phân chia danh sách phòng dọn trực tiếp trên giao diện máy tính.
* **Exception Flow:**
  * **Ex-1: Phát hiện phòng hỏng nặng khi dọn**: Chọn chức năng "Báo hỏng" trên app di động. Hệ thống dừng tiến trình dọn dẹp, tạo ticket bảo trì và chuyển trạng thái phòng sang OOO.

#### UC-HK-02: Báo cáo tiêu dùng Minibar buồng phòng
* **Actor:** Nhân viên buồng phòng, Lễ tân
* **Preconditions:** Phòng đang dọn ở trạng thái chuẩn bị check-out.
* **Main Flow:**
  1. Nhân viên buồng phòng kiểm tra tủ minibar tại phòng khách sạn, đối chiếu số lượng thực tế với định mức.
  2. Mở Housekeeping App trên di động, chọn số phòng và chọn chức năng "Khai báo Minibar".
  3. Chọn vật phẩm và nhập số lượng tiêu thụ, nhấn xác nhận.
  4. Hệ thống PMS tiếp nhận thông tin, tự động post phí minibar vào Folio phòng.
  5. Màn hình thanh toán của Lễ tân lập tức hiển thị khoản phí minibar này để thu tiền khi check-out.
* **Alternative Flow:**
  * **Alt-1: Lễ tân tự nhập tay chi phí minibar**: Khách tự khai báo tại quầy khi check-out, lễ tân mở Folio và nhập trực tiếp mã dịch vụ minibar.
* **Exception Flow:**
  * **Ex-1: Khai báo vượt định mức phòng**: Nhập số lượng vượt định mức minibar tối đa -> Hệ thống báo lỗi chặn không cho lưu và yêu cầu kiểm tra lại.

#### UC-HK-03: Quản lý tài sản thất lạc Lost & Found
* **Actor:** Nhân viên buồng phòng, Lễ tân, Giám sát buồng phòng
* **Main Flow:**
  1. Nhân viên phát hiện đồ đạc khách bỏ quên trong phòng sau check-out.
  2. Mở Housekeeping App, chọn chức năng "Báo cáo Lost & Found".
  3. Chụp ảnh vật phẩm, nhập mô tả, vị trí tìm thấy và nhấn lưu.
  4. Hệ thống tạo một bản ghi Lost & Found mới ở trạng thái "In Storage" kèm ID.
  5. Lễ tân nhận được thông báo, đối chiếu thông tin khách phòng vừa check-out để liên hệ trả lại.
  6. Khách nhận lại đồ, giám sát cập nhật trạng thái bản ghi thành "Returned to Guest".
* **Exception Flow:**
  * **Ex-1: Khách không liên lạc được**: Quá thời hạn quy định (6 tháng), hệ thống tự động chuyển trạng thái bản ghi sang "Liquidated" để giám sát thanh lý đồ.

#### UC-HK-04: Báo hỏng thiết bị và Tạo phiếu bảo trì (Maintenance)
* **Actor:** Nhân viên buồng phòng, Nhân viên kỹ thuật, Lễ tân
* **Main Flow:**
  1. Nhân viên phát hiện thiết bị hỏng tạo yêu cầu sửa chữa trên app di động.
  2. Nhập loại thiết bị, mô tả lỗi, mức độ khẩn cấp và chụp ảnh lỗi đính kèm.
  3. Hệ thống tạo phiếu bảo trì, chuyển trạng thái phòng sang OOS hoặc OOO.
  4. Gửi thông báo đẩy đến di động của nhân viên kỹ thuật trực ca.
  5. Kỹ thuật đến sửa chữa, cập nhật trạng thái ticket trên app thành "In Progress".
  6. Sửa xong, chọn "Completed" và mô tả vật tư thay thế. Phòng tự động trả về VC hoặc OC.
* **Exception Flow:**
  * **Ex-1: Thiếu vật tư thay thế**: Kỹ thuật chọn trạng thái ticket "Chờ vật tư". Hệ thống giữ trạng thái phòng OOO và gửi yêu cầu mua hàng sang ERP.

---

### Phân hệ Thu ngân & Thanh toán (Cashiering - UC-PAY)

#### UC-PAY-01: Ghi nợ dịch vụ từ các điểm POS resort (Room Charge Posting)
* **Actor:** Nhân viên thu ngân POS (Nhà hàng, Spa), Khách hàng, Hệ thống PMS
* **Preconditions:** Khách hàng đang lưu trú hợp lệ tại resort, phòng không bị khóa nợ (No Post).
* **Main Flow:**
  1. Khách hàng sử dụng dịch vụ tại Nhà hàng và yêu cầu ghi nợ phòng. Cung cấp Số phòng và Họ tên.
  2. Thu ngân nhà hàng nhập số phòng và tên khách lên phần mềm POS nhà hàng.
  3. Hệ thống POS gửi yêu cầu xác thực API thời gian thực sang PMS Core.
  4. PMS Core kiểm tra trạng thái phòng, xác thực tên khách và Folio phòng không bị khóa nợ.
  5. PMS Core trả về phản hồi phê duyệt `APPROVED` kèm theo Credit Limit khả dụng.
  6. Thu ngân nhà hàng in hóa đơn, khách ký xác nhận. POS gửi lệnh API ghi nhận chi phí sang PMS.
  7. PMS tự động ghi nhận một dòng phí ăn uống vào Folio phòng tương ứng thời gian thực.
* **Exception Flow:**
  * **Ex-1: Xác thực thất bại tại PMS**: PMS báo lỗi Denied. Thu ngân POS từ chối ghi nợ phòng và thu tiền trực tiếp.
  * **Ex-2: Chi phí vượt hạn mức tín dụng phòng**: PMS báo lỗi vượt hạn mức. Thu ngân POS yêu cầu khách thanh toán trực tiếp phần tiền chênh lệch.

#### UC-PAY-02: Tách / Ghép và Thanh toán hóa đơn Folio (Folio Settlement)
* **Actor:** Lễ tân, Thu ngân, Khách hàng
* **Preconditions:** Folio phòng đang ở trạng thái hoạt động (Active).
* **Main Flow:**
  1. Khách yêu cầu thanh toán hóa đơn phòng để check-out. Lễ tân mở Folio của phòng trên PMS.
  2. Bảng kê hiển thị toàn bộ các khoản chi phí phát sinh từ khi check-in.
  3. Lễ tân khấu trừ tiền đặt cọc (Deposit) đã đóng trước đó.
  4. Chọn phương thức thanh toán cho số dư còn lại (Tiền mặt, Thẻ tín dụng, Chuyển khoản) và nhấn xác nhận.
  5. Kết nối thiết bị quẹt thẻ POS sảnh, ghi nhận giao dịch thành công.
  6. Số dư Folio về 0. Khóa Folio, đổi trạng thái phòng sang VD và hoàn tất Check-out.
* **Alternative Flow:**
  * **Alt-1: Tách hóa đơn (Split Bill)**: Chọn các dòng chi phí phòng chuyển sang Folio B, chi phí ăn uống giữ ở Folio A. Thanh toán độc lập.
  * **Alt-2: Ghép hóa đơn (Combine Bill)**: Gộp tiền phòng của nhiều phòng về thanh toán một lần trên Master Folio của trưởng đoàn.
* **Exception Flow:**
  * **Ex-1: Khách hàng khiếu nại chi phí**: Lễ tân kiểm tra lỗi, thực hiện tính năng giảm trừ (Rebate) nhập dòng chi phí âm (-) khấu trừ tiền thừa sau khi được quản lý phê duyệt.

#### UC-PAY-03: Quy trình Kiểm toán đêm (Night Audit Workflow)
* **Actor:** Kiểm toán viên đêm (Night Auditor), Hệ thống PMS
* **Preconditions:** Đã đến khung giờ chốt ngày (sau 00:00 AM). Các quầy POS đã chốt ca.
* **Main Flow:**
  1. Kiểm toán viên đêm mở module Night Audit trên PMS và bắt đầu tiến trình chạy kiểm toán (Audit Wizard).
  2. Hệ thống chạy chương trình tự động kiểm tra điều kiện biên (Pre-Audit Checks).
  3. Nhân viên xử lý các booking sót theo hướng dẫn hệ thống.
  4. Hệ thống chạy tiến trình Post Room Charge tự động chốt tiền phòng đêm nay cho các phòng đang lưu trú.
  5. Hệ thống chốt số liệu tài chính ngày cũ, thực hiện sao lưu DB tự động (Database Snapshot).
  6. Hệ thống thực hiện dịch chuyển ngày vận hành hệ thống (System Date Roll) tiến lên +1 ngày.
  7. Tự động xuất Daily Flash Report gửi email cho GM.
* **Exception Flow:**
  * **Ex-1: Sự cố sập hệ thống/mất điện giữa chừng**: PMS tự động rollback giao dịch về trạng thái an toàn trước khi chạy audit. Khi hệ thống hoạt động lại ngày hệ thống vẫn giữ ở ngày cũ.

#### UC-PAY-04: Phát hành hóa đơn GTGT điện tử (e-Invoice Export)
* **Actor:** Lễ tân, Thu ngân, Cổng thuế
* **Preconditions:** Folio phòng đã được thanh toán hoàn tất và đóng số dư bằng 0.
* **Main Flow:**
  1. Lễ tân chọn chức năng "Phát hành hóa đơn điện tử" trên Folio đã đóng.
  2. Nhập thông tin doanh nghiệp xuất hóa đơn (MST, Tên công ty, Địa chỉ, Email).
  3. Nhấn tra cứu nhanh để hệ thống tự gọi API cổng thuế điền thông tin chính xác.
  4. Chọn danh mục các phí cần xuất hóa đơn và nhấn nút "Phát hành".
  5. PMS gửi file dữ liệu XML qua API cổng hóa đơn điện tử đối tác.
  6. Hệ thống thuế cấp mã và trả về tệp hóa đơn PDF chính thức ký số lưu vào Folio.
  7. Gửi email hóa đơn PDF cho khách, đồng bộ doanh thu sang ERP.
* **Exception Flow:**
  * **Ex-1: Sai mã số thuế hoặc cổng thuế bị nghẽn mạng**: Hệ thống báo lỗi. Lễ tân chuyển trạng thái yêu cầu sang "Chờ xuất lại" để hệ thống tự gửi lại sau, phát phiếu thu tạm cho khách.

---

### Phân hệ Khách hàng & Quản trị (CRM & ADMIN)

#### UC-CRM-01: Quản lý & Hợp nhất hồ sơ khách hàng (Guest Profile)
* **Actor:** Lễ tân, Nhân viên đặt phòng, Quản trị viên IT
* **Main Flow:**
  1. Tác nhân mở phân hệ CRM để tra cứu hoặc tạo mới Guest Profile cho khách hàng.
  2. Nhập thông tin: Họ tên, số định danh, SĐT, Email, sở thích lưu trú.
  3. Hệ thống lưu hồ sơ và tạo mã Guest ID duy nhất.
  4. Hệ thống tự động chạy thuật toán quét trùng lặp dữ liệu định kỳ theo tuần.
  5. Hiển thị danh sách hồ sơ nghi trùng lặp (trùng CCCD hoặc SĐT).
  6. Tác nhân chọn các hồ sơ trùng và nhấn Merge để gộp dữ liệu về hồ sơ chính.
* **Exception Flow:**
  * **Ex-1: Hợp nhất nhầm hồ sơ**: Cho phép thực hiện tính năng hoàn tác (Unmerge) khôi phục hồ sơ cũ từ thùng rác tạm trong vòng 24 giờ dựa trên log kiểm toán.

#### UC-CRM-03: Quản lý Hợp đồng Đối tác & Công ty (Corporate & TA Contracts)
* **Actor:** Nhân viên đặt phòng, Quản lý kinh doanh (Sales Manager)
* **Preconditions:** Hồ sơ đại lý lữ hành (TA) hoặc doanh nghiệp (Corporate) đã được tạo trong CRM.
* **Main Flow:**
  1. Tác nhân mở hồ sơ doanh nghiệp tương ứng và chọn tab "Quản lý hợp đồng".
  2. Nhấn "Tạo mới hợp đồng", nhập thông tin: Số hợp đồng, ngày hiệu lực, ngày hết hạn, hạn mức tín dụng công nợ (Credit Limit), và chính sách bảo lãnh.
  3. Cấu hình bảng giá đặc biệt (Contract Rate) chi tiết cho từng loại phòng theo các mùa vụ thỏa thuận.
  4. Đính kèm tệp số hợp đồng (file PDF đã ký đóng dấu) và lưu bản ghi.
  5. Hệ thống đổi trạng thái hợp đồng thành "Hoạt động" (Active), tự động áp dụng giá phòng thỏa thuận này khi nhân viên tạo đặt phòng liên kết với mã đối tác này.
* **Alternative Flow:**
  * **Alt-1: Gia hạn hợp đồng**: Chọn hợp đồng sắp hết hạn, nhấn "Gia hạn hợp đồng", điều chỉnh ngày hết hạn mới và tải lên bản phụ lục hợp đồng PDF bổ sung.
* **Exception Flow:**
  * **Ex-1: Công nợ vượt hạn mức tín dụng**: Hệ thống báo lỗi đỏ và chặn lưu booking. Tác nhân phải yêu cầu đối tác thanh toán bớt công nợ cũ hoặc xin phê duyệt tăng hạn mức tạm thời.

#### UC-ADM-01: Cấp tài khoản và Phân quyền người dùng (RBAC)
* **Actor:** Quản trị viên IT (System Admin)
* **Preconditions:** Đăng nhập bằng tài khoản Super Admin tối cao.
* **Main Flow:**
  1. Admin truy cập trang Quản trị người dùng (User Management Console) trên PMS.
  2. Tạo mới tài khoản nhân viên: Nhập tên đăng nhập, email công việc, số điện thoại, bộ phận trực thuộc.
  3. Gán tài khoản vào một Vai trò cụ thể (Role - ví dụ: Front Desk Agent, Night Auditor).
  4. Hệ thống tự động tải bảng quyền hạn mặc định tương ứng với vai trò đó.
  5. Admin nhấn lưu để kích hoạt tài khoản. Hệ thống gửi email tự động chứa link kích hoạt và yêu cầu thiết lập mật khẩu, thiết lập mã xác thực đa yếu tố (MFA).
* **Alternative Flow:**
  * **Alt-1: Tùy chỉnh quyền hạn nâng cao**: Cấp thêm quyền hoặc loại bỏ một số quyền cụ thể ngoài vai trò mặc định đã gán.
* **Exception Flow:**
  * **Ex-1: Khóa tài khoản do vi phạm bảo mật**: Nhập sai mật khẩu quá 5 lần hoặc phát hiện gian lận dữ liệu -> Hệ thống tự động khóa tài khoản tạm thời và gửi email cảnh báo cho IT Admin.

#### UC-ADM-02: Kiểm tra Nhật ký kiểm toán hệ thống (Audit Trail Logging)
* **Actor:** Quản trị viên IT, Ban Giám Đốc
* **Preconditions:** Yêu cầu quyền truy cập cấp giám sát hệ thống hoặc kiểm toán bảo mật.
* **Main Flow:**
  1. Tác nhân đăng nhập PMS, truy cập module Nhật ký kiểm toán (Audit Trail Logs).
  2. Thiết lập các bộ lọc tìm kiếm: Bộ lọc theo khoảng thời gian, theo User ID nhân viên, theo loại hành động hoặc theo ID bản ghi cụ thể.
  3. Hệ thống truy vấn vùng nhớ bất biến chứa log kiểm toán và hiển thị danh sách các hoạt động khớp với bộ lọc.
  4. Tác nhân nhấn vào một dòng hoạt động cụ thể để xem chi tiết thông tin: IP thiết bị, thời gian chính xác, giá trị cũ của dữ liệu trước khi sửa (Old Value) và giá trị mới sau khi lưu (New Value).
  5. Tác nhân thực hiện kết xuất danh sách log ra tệp Excel ký số để làm chứng cứ báo cáo nội bộ.

#### UC-ADM-03: Chấm công & Quản lý Ca trực (Shift & Time Attendance)
* **Actor:** Lễ tân, Nhân viên buồng phòng, Nhân viên kỹ thuật, Quản trị viên IT (Admin)
* **Preconditions:** Bảng phân ca làm việc (Shift Schedule) tuần hiện tại đã được cấu hình trên hệ thống.
* **Main Flow:**
  1. Nhân viên đến khách sạn bắt đầu ca trực, đặt thẻ nhân viên RFID lên đầu đọc thẻ đặt tại sảnh.
  2. Đầu đọc thẻ nhận dạng mã RFID, gửi yêu cầu xác thực API về PMS.
  3. PMS xác thực thông tin thẻ, ghi nhận Timestamp Clock-in, tự động mapping với ID ca trực được phân công cho nhân viên này trong ngày.
  4. Đầu đọc phát tín hiệu tiếng bíp thành công và hiển thị thông báo chào mừng nhân viên bắt đầu ca trực.
  5. Khi kết thúc ca trực, nhân viên quét thẻ lần 2. Hệ thống ghi nhận Timestamp Clock-out và tính toán tổng số giờ làm việc thực tế của ca trực.
* **Alternative Flow:**
  * **Alt-1: Chấm công thủ công (Manual Adjust)**: Nhân viên quên mang thẻ hoặc đầu đọc RFID bị sự cố, trưởng bộ phận đăng nhập PMS và chọn chức năng điều chỉnh giờ công, nhập thủ công giờ bắt đầu/kết thúc thực tế và nhập lý do điều chỉnh để lưu vết kiểm toán.
* **Exception Flow:**
  * **Ex-1: Chấm công sai ca hoặc sai ngày**: Nhân viên quét thẻ clock-in vào ngày không được phân công trực ca hoặc quét sai khung giờ ca trực quá 2 tiếng -> Hệ thống vẫn ghi nhận giờ quét nhưng hiển thị trạng thái "Ca trực không hợp lệ" và gửi cảnh báo đến Trưởng bộ phận để rà soát.

---

### Phân hệ Báo cáo & Phân tích (Reporting - UC-REP)

#### UC-REP-01: Xuất báo cáo quản trị và Chỉ số hiệu suất kinh doanh (BI)
* **Actor:** Ban Giám Đốc, Kiểm toán viên đêm
* **Preconditions:** Dữ liệu chốt ngày từ tiến trình Kiểm toán đêm đã được hoàn thành.
* **Main Flow:**
  1. Tác nhân đăng nhập hệ thống, mở phân hệ Báo cáo Quản trị (BI Dashboard).
  2. Hệ thống hiển thị biểu đồ tương tác thời gian thực biểu diễn 3 chỉ số cốt lõi: Công suất phòng (OCC), Giá phòng trung bình hàng ngày (ADR), Doanh thu trên phòng sẵn có (RevPAR).
  3. Tác nhân chọn loại báo cáo mong muốn từ menu (ví dụ: Báo cáo Quản trị hàng ngày - Daily Flash Report).
  4. Hệ thống tổng hợp dữ liệu doanh thu phòng, F&B, Spa từ database chốt ngày cũ và kết xuất file báo cáo.
  5. Tác nhân xem báo cáo trực tiếp trên giao diện web hoặc nhấn nút "Xuất file" chọn định dạng PDF/Excel để lưu giữ hoặc in ấn.
* **Alternative Flow:**
  * **Alt-1: Đặt lịch gửi báo cáo tự động (Scheduled Email)**: Tác nhân thiết lập lịch gửi báo cáo tự động. Cấu hình gửi tệp báo cáo Daily Flash Report vào lúc 06:00 sáng hàng ngày đến địa chỉ email của các thành viên Hội đồng quản trị.

---

## 13. USE CASE DIAGRAM (SƠ ĐỒ USE CASE - USE CASE DIAGRAM)

### [VỊ TRÍ CHÈN SƠ ĐỒ USE CASE - USE CASE DIAGRAM]
> *Hướng dẫn dành cho đội ngũ phát triển: Hãy chèn tệp hình ảnh xuất từ mã nguồn Mermaid của Sơ đồ Use Case cập nhật bổ sung tác nhân Guest và Manager tại đây.*

#### Mô tả các tác nhân và liên kết quan trọng trên sơ đồ:
* **Tác nhân Khách hàng (Guest)**: Liên kết trực tiếp với các Use Case tự phục vụ hướng ngoại bao gồm: *Tra cứu phòng trống*, *Đặt phòng lẻ (FIT)*, *Khách tự hủy đặt phòng*, và *Khách tự đổi phòng*. Use Case *Đặt phòng lẻ (FIT)* và *Khách tự hủy đặt phòng* có quan hệ `<<include>>` với Use Case *Thanh toán trực tuyến*.
* **Tác nhân Quản lý (Manager)**: Liên kết trực tiếp với các Use Case quản trị cấp cao bao gồm: *Quản lý & phân quyền nhân viên* (RBAC), *Xem báo cáo & Dashboard BI*, *Quản lý danh mục phòng*, và *Quản lý danh mục dịch vụ*.
* **Tác nhân Lễ tân (GSA)**: Liên kết với các Use Case sảnh bao gồm: *Nhận phòng - Check-in*, *Trả phòng - Check-out*, và *Chuyển phòng*. Use Case *Nhận phòng - Check-in* `<<include>>` việc *Phát hành khóa từ*. Use Case *Trả phòng - Check-out* `<<include>>` việc *Khai báo Minibar* tiêu dùng.
* **Quan hệ `<<extend>>`**: Use Case *Nâng hạng phòng* mở rộng (`<<extend>>`) cho Use Case *Chuyển phòng*. Use Case *Xuất hóa đơn GTGT* mở rộng (`<<extend>>`) cho Use Case *Trả phòng - Check-out*.

---

## 14. ERD (SƠ ĐỒ THỰC THỂ QUAN HỆ - ERD)

### [VỊ TRÍ CHÈN SƠ ĐỒ THỰC THỂ QUAN HỆ - ERD]
> *Hướng dẫn dành cho DBA và Developer: Hãy chèn tệp hình ảnh xuất từ lược đồ ERD dạng chuẩn 3 (3NF) chứa đầy đủ các bảng MembershipTier, CorporateProfile, DailyRate, MaintenanceTicket, AuditLog, Shift và EmployeeShift tại đây.*

#### Chi tiết các bảng thay đổi và lý do chuẩn hóa 3NF:
* **Bảng `Customer` & `MembershipTier` (Quan hệ 1-N)**: Thông tin hạng thành viên được tách riêng thành bảng `MembershipTier` để tránh trùng lặp thông tin giảm giá phòng và hệ số tích điểm tại bảng `Customer`, ngăn ngừa lỗi bất nhất khi cập nhật quy định hạng thẻ.
* **Bảng `CorporateProfile` (Quan hệ 1-N với `Booking` và `Invoice`)**: Tách thông tin doanh nghiệp (Tên công ty, MST, Địa chỉ) khỏi bảng `Invoice` và `Booking` để tránh sự phụ thuộc bắc cầu (transitive dependency), đảm bảo chuẩn hóa dữ liệu doanh nghiệp.
* **Bảng `Folio` (Tài khoản hóa đơn)**: Loại bỏ các cột chứa dữ liệu tính toán tổng hợp như `total_amount`, `total_tax`, `balance` để tuân thủ dạng chuẩn 3NF. Số liệu hóa đơn được tính toán động (Dynamic Query) từ bảng `FolioDetail` và `Payment` để đảm bảo tính toàn vẹn tài chính.
* **Bảng `Reservation.negotiated_rate`**: Sử dụng thuộc tính giá thỏa thuận làm thuộc tính lịch sử giao dịch (Transactional Attribute) của từng phòng đặt cụ thể, đảm bảo tính độc lập với bảng giá phòng động hàng ngày `DailyRate`.

---

## 15. SEQUENCE DIAGRAM (BIỂU ĐỒ TUẦN TỰ - SEQUENCE DIAGRAM)

### [VỊ TRÍ CHÈN BIỂU ĐỒ TUẦN TỰ - SEQUENCE DIAGRAM]
> *Hướng dẫn dành cho QA/QC và Developer: Hãy chèn tệp hình ảnh xuất từ Biểu đồ tuần tự luồng đặt phòng trực tuyến có tương tác giữa Customer - Web - BookingController - BookingService - Database - PaymentService tại đây.*

#### Mô tả chi tiết luồng xử lý và thay đổi trạng thái trong sơ đồ:
1. **Tra cứu**: Customer gửi yêu cầu tra cứu qua Web Interface, BookingController chuyển tiếp đến BookingService thực hiện truy vấn các phòng Trống Sạch (VC) trong Database.
2. **Khởi tạo đặt phòng**: Customer chọn phòng, BookingController gửi yêu cầu POST tạo đặt phòng. BookingService thực hiện khóa tạm thời phòng trống (Inventory Lock) và lưu thông tin đặt phòng vào Database với trạng thái `Pending`.
3. **Thanh toán đặt cọc**: BookingService gọi PaymentService để tạo phiên giao dịch (Payment Session) và trả về đường dẫn thanh toán (`checkout_url`). Web Interface điều hướng trình duyệt của khách hàng sang cổng thanh toán (Stripe/VNPay).
4. **Webhook xử lý bất đồng bộ**: Cổng thanh toán gửi Webhook kết quả giao dịch thành công về BookingController. BookingController gọi BookingService thực hiện cập nhật trạng thái đặt phòng thành `Confirmed` (Xác nhận đặt phòng), lưu thông tin giao dịch vào bảng `payments` và ghi nhận cọc.
5. **Đồng bộ và gửi email**: BookingService gửi email xác nhận đặt phòng tự động cho Customer và giải phóng phiên khóa phòng tạm. Customer được chuyển hướng về trang Web kết quả hiển thị thông tin đặt phòng thành công kèm mã QR check-in.

---

## 16. CONCLUSION (KẾT LUẬN)

Tài liệu Đặc tả Yêu cầu Phần mềm (SRS) và Thiết kế Hệ thống theo chuẩn IEEE 830 của dự án **Hotel Management System (ePMS)** đã xây dựng một nền tảng tài liệu nghiệp vụ hoàn chỉnh, thực tế và khả thi. Bằng cách áp dụng tư duy thiết kế hệ thống hiện đại, ePMS giải quyết triệt để các bài toán vận hành phức tạp của khách sạn và resort cao cấp 4-5 sao: từ việc đồng bộ hóa dữ liệu thời gian thực, ngăn ngừa thất thoát doanh thu dịch vụ sảnh, đến việc chuẩn hóa cơ sở dữ liệu đạt dạng chuẩn 3 (3NF) để bảo vệ tính toàn vẹn tài chính.

Kiến trúc Cloud-Native SaaS, mô hình Microservices, và API-First đảm bảo hệ thống có độ tin cậy và khả năng mở rộng cao, sẵn sàng phục vụ hàng chục ngàn lượt truy cập đồng thời. Sơ đồ Use Case và biểu đồ tuần tự được cập nhật bổ sung tác nhân **Khách hàng** (tự phục vụ online) và tác nhân **Quản lý** (quản trị hệ thống) cung cấp cái nhìn chi tiết và toàn diện cho đội ngũ phát triển phần mềm, kỹ sư cơ sở dữ liệu và đội ngũ kiểm thử (QA/QC) triển khai xây dựng hệ thống trong các giai đoạn tiếp theo của dự án.
