# SƠ ĐỒ USE CASE (USE CASE DIAGRAM) HỆ THỐNG PMS

**Tài liệu:** Use Case Diagram Specification  
**Vai trò thiết kế:** Solution Architect  
**Mô tả:** Sơ đồ Use Case cập nhật bổ sung tác nhân Khách hàng (hủy phòng, tự đổi phòng, thanh toán online) và tác nhân Quản lý (Manager).

---

## 1. SƠ ĐỒ USE CASE MERMAID

```mermaid
graph LR
    %% Styles Configuration
    classDef actor fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-weight:bold;
    classDef usecase fill:#FFF3E0,stroke:#EF6C00,stroke-width:2px,color:#E65100;
    classDef includeStyle stroke:#2E7D32,stroke-width:1.5px,stroke-dasharray: 5 5,color:#2E7D32;
    classDef extendStyle stroke:#C62828,stroke-width:1.5px,stroke-dasharray: 5 5,color:#C62828;

    %% Actors Definition
    Guest["🧑 Khách hàng (Guest)"]
    ResAgent["👨 Nhân viên Đặt phòng"]
    GSA["👩 Lễ tân (Front Desk Agent)"]
    HK["🧹 Nhân viên Buồng phòng"]
    Auditor["🕵️ Kiểm toán viên đêm"]
    GM["📈 Ban Giám Đốc (GM)"]
    Admin["💻 Quản trị viên IT (Admin)"]
    Manager["🧑‍💼 Quản lý (Manager)"]

    %% Use Cases Definition
    UC_Search(["Tra cứu phòng trống"])
    UC_FIT(["Đặt phòng lẻ - FIT"])
    UC_GIT(["Đặt phòng đoàn - GIT"])
    UC_Deposit(["Thanh toán trực tuyến"])
    UC_Checkin(["Nhận phòng - Check-in"])
    UC_Keycard(["Phát hành khóa từ"])
    UC_Checkout(["Trả phòng - Check-out"])
    UC_Move(["Chuyển phòng"])
    UC_Upgrade(["Nâng hạng phòng"])
    UC_Minibar(["Khai báo Minibar"])
    UC_DtyUpdate(["Cập nhật dọn dẹp"])
    UC_Audit(["Kiểm toán đêm - Night Audit"])
    UC_Invoice(["Xuất hóa đơn GTGT"])
    UC_RBAC(["Quản lý & phân quyền nhân viên"])
    UC_BI(["Xem báo cáo & Dashboard BI"])
    
    %% New Use Cases from Customer & Manager requests
    UC_Cancel(["Khách tự hủy đặt phòng"])
    UC_SelfMove(["Khách tự đổi phòng"])
    UC_RoomMgmt(["Quản lý danh mục phòng"])
    UC_ServiceMgmt(["Quản lý danh mục dịch vụ"])

    %% Apply Styles to Actors and Use Cases
    class Guest,ResAgent,GSA,HK,Auditor,GM,Admin,Manager actor;
    class UC_Search,UC_FIT,UC_GIT,UC_Deposit,UC_Checkin,UC_Keycard,UC_Checkout,UC_Move,UC_Upgrade,UC_Minibar,UC_DtyUpdate,UC_Audit,UC_Invoice,UC_RBAC,UC_BI,UC_Cancel,UC_SelfMove,UC_RoomMgmt,UC_ServiceMgmt usecase;

    %% Guest Connections (Online Payment, Cancellation, Room Move)
    Guest --> UC_Search
    Guest --> UC_FIT
    Guest --> UC_Cancel
    Guest --> UC_SelfMove

    %% Manager Connections (Employees, Reports, Rooms, Services)
    Manager --> UC_RBAC
    Manager --> UC_BI
    Manager --> UC_RoomMgmt
    Manager --> UC_ServiceMgmt

    %% Other Actor Connections
    ResAgent --> UC_FIT
    ResAgent --> UC_GIT
    GSA --> UC_Checkin
    GSA --> UC_Checkout
    GSA --> UC_Move
    HK --> UC_Minibar
    HK --> UC_DtyUpdate
    Auditor --> UC_Audit
    GM --> UC_BI
    Admin --> UC_RBAC

    %% Include Relationships (dashed green arrows)
    UC_FIT -.->|includes| UC_Deposit
    UC_GIT -.->|includes| UC_Deposit
    UC_Checkin -.->|includes| UC_Keycard
    UC_Checkout -.->|includes| UC_Minibar
    UC_Cancel -.->|includes| UC_Deposit

    %% Extend Relationships (dashed red arrows)
    UC_Upgrade -.->|extends| UC_Move
    UC_Invoice -.->|extends| UC_Checkout
```
