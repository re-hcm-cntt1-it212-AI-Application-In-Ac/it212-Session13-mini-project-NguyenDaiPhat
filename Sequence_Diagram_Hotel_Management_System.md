# BIỂU ĐỒ TUẦN TỰ (SEQUENCE DIAGRAM): CHỨC NĂNG ĐẶT PHÒNG

**Tài liệu:** Sequence Diagram Specification  
**Vai trò thiết kế:** Solution Architect  
**Quy trình mô tả:** Luồng tạo đặt phòng trực tuyến (FIT Online Booking) và thanh toán đặt cọc qua cổng thanh toán bảo mật.

---

## 1. BIỂU ĐỒ TUẦN TỰ MERMAID

```mermaid
sequenceDiagram
    autonumber
    actor Customer as 🧑 Customer
    participant Web as 💻 Web Interface
    participant Ctrl as ⚙️ BookingController
    participant Svc as 🧠 BookingService
    participant DB as 🗄️ Database
    participant PaySvc as 💳 PaymentService

    %% Bước 1: Tra cứu và Tạo Đặt Phòng
    Customer->>Web: Nhập ngày lưu trú, số khách & nhấn Tìm kiếm
    Web->>Ctrl: GET /api/v1/rooms/availability?checkin=...&checkout=...
    Ctrl->>Svc: checkAvailability(checkIn, checkOut, guestCount)
    Svc->>DB: Truy vấn phòng VC thuộc loại yêu cầu
    DB-->>Svc: Trả về danh sách phòng trống khả dụng
    Svc-->>Ctrl: Trả về danh sách hạng phòng & giá (Dynamic Rates)
    Ctrl-->>Web: Trả về HTTP 200 OK (JSON List)
    Web-->>Customer: Hiển thị các hạng phòng trống & bảng giá

    %% Bước 2: Khách hàng tiến hành đặt phòng
    Customer->>Web: Chọn hạng phòng, nhập thông tin cá nhân & nhấn Đặt phòng
    Web->>Ctrl: POST /api/v1/bookings (BookingRequest JSON)
    Ctrl->>Svc: createBooking(bookingRequest)
    
    activate Svc
    Svc->>DB: Khóa tạm thời phòng trống (Room Inventory Lock)
    DB-->>Svc: Khóa thành công
    Svc->>DB: INSERT INTO bookings (status='Pending')
    DB-->>Svc: Trả về booking_id & booking_code
    
    %% Bước 3: Xử lý thanh toán cọc trực tuyến
    Svc->>PaySvc: initiatePayment(booking_id, amount, returnUrl)
    activate PaySvc
    PaySvc->>PaySvc: Tạo phiên thanh toán bảo mật (Payment Session)
    PaySvc-->>Svc: Trả về payment_session_id & checkout_url
    deactivate PaySvc
    
    Svc-->>Ctrl: Trả về BookingResponse (checkout_url, status='Pending')
    deactivate Svc
    Ctrl-->>Web: Trả về HTTP 201 Created (JSON Response)
    Web-->>Customer: Chuyển hướng trình duyệt sang checkout_url (Stripe/VNPay)

    %% Bước 4: Khách hàng thanh toán trên cổng thanh toán
    Customer->>PaySvc: Nhập thông tin thẻ và nhấn Xác nhận thanh toán
    activate PaySvc
    PaySvc->>PaySvc: Xác thực thẻ & Trừ tiền tài khoản khách
    PaySvc-->>Customer: Hiển thị thông báo Giao dịch thành công
    
    %% Bước 5: Cổng thanh toán gửi Webhook xác nhận về hệ thống
    Note over PaySvc, Ctrl: Webhook xử lý bất đồng bộ (Asynchronous Callback)
    PaySvc->>Ctrl: POST /api/v1/payments/webhook (PaymentNotification JSON)
    Ctrl->>Svc: confirmPayment(paymentNotification)
    activate Svc
    Svc->>DB: UPDATE bookings SET status='Confirmed' WHERE id=booking_id
    Svc->>DB: INSERT INTO payments (amount, method, ref_id)
    Svc->>DB: UPDATE room_inventory SET status='Confirmed'
    DB-->>Svc: Cập nhật CSDL thành công
    
    %% Gửi email xác nhận
    Svc->>Svc: triggerConfirmationEmail(booking_id)
    Svc-->>Ctrl: Xác nhận thanh toán thành công
    deactivate Svc
    Ctrl-->>PaySvc: Trả về HTTP 200 OK (Xác nhận Webhook)
    deactivate PaySvc

    %% Bước 6: Trình duyệt chuyển hướng khách về trang kết quả
    Web->>Ctrl: GET /api/v1/bookings/status?code=booking_code
    Ctrl->>Svc: getBookingStatus(booking_code)
    Svc->>DB: Truy vấn thông tin booking trạng thái 'Confirmed'
    DB-->>Svc: Trả về thông tin chi tiết
    Svc-->>Ctrl: Trả về booking details
    Ctrl-->>Web: Trả về HTTP 200 OK (JSON Details)
    Web-->>Customer: Hiển thị màn hình "Đặt phòng thành công" kèm mã QR check-in
