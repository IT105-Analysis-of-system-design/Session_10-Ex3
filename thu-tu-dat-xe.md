# Sắp xếp thứ tự thông điệp — Chức năng Đặt xe RikkeiLogistics

## 1. Thứ tự đúng và loại thông điệp

| # | Thông điệp | Chiều | Loại | Vì sao |
|---|---|---|---|---|
| 1 | `taoDonHang()` | Khách hàng → App Điều Phối | **Sync** | Khách hàng cần chờ ngay kết quả (đề bài quy định rõ) |
| 2 | Gửi yêu cầu nhận đơn | App Điều Phối → Điện thoại Tài xế | **Async** | "không chờ ngay lập tức" — gửi xong đi tiếp, không khóa activation bar |
| 3 | Phản hồi Đồng ý/Từ chối | Điện thoại Tài xế → App Điều Phối | **Async** | Là thông điệp độc lập, không phải Return, vì yêu cầu gửi đi ở bước 2 là Async (Tài xế không bị giữ chờ nên có thể phản hồi ở bất kỳ thời điểm nào sau đó) |
| 4 | Khởi tạo bản ghi Chuyến Đi (Trip) mới | App Điều Phối → Chuyến Đi (Trip) | **Create** | Chỉ xảy ra trong nhánh `[Tài xế đồng ý]` của khối `alt`; lifeline của Trip bắt đầu đúng tại đây |
| 5 | Báo kết quả cuối cùng ("Đã tìm thấy xe" / "Không tìm được tài xế") | App Điều Phối → Khách hàng | **Return** | Khép lại activation bar của lời gọi Sync ở bước 1; nằm **ngoài** khối `alt`, sau cả 2 nhánh, vì luôn xảy ra dù tài xế đồng ý hay từ chối |

## 2. Cấu trúc khối `alt`

Khối `alt` bắt đầu **ngay sau bước 3** (khi App Điều Phối nhận được phản hồi từ tài xế):

- **Nhánh `[Tài xế đồng ý]`**: chứa bước 4 (Create Chuyến Đi mới).
- **Nhánh `[Tài xế từ chối]`** (else): không có thông điệp nào — không tạo Chuyến Đi.

Bước 5 (báo kết quả cho Khách hàng) nằm **ngoài** khung `alt`, phía sau, vì nó luôn thực thi bất kể nhánh nào được chọn.

## 3. Lưu ý về lifeline của Trip

Vì bước 4 là thông điệp **Create**, lifeline của đối tượng "Chuyến Đi (Trip)" **không được vẽ từ đầu sơ đồ** như 3 lifeline còn lại — nó chỉ bắt đầu xuất hiện đúng tại vị trí mũi tên Create trỏ tới, bên trong nhánh `[Tài xế đồng ý]` của khối `alt`.

Xem file `datxe-sequence.drawio` để có bản vẽ đầy đủ.
