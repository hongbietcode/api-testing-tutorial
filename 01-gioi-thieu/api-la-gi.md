# 1.1 API Là Gì?

## Định Nghĩa

**API** là viết tắt của **Application Programming Interface** (Giao diện lập trình ứng dụng).

Nói một cách đơn giản: **API là cầu nối cho phép các ứng dụng khác nhau giao tiếp và trao đổi dữ liệu với nhau**.

## Ví Dụ Trong Cuộc Sống

### Ví dụ 1: Nhà hàng

Hãy tưởng tượng bạn đến một nhà hàng:

- **Bạn** = Ứng dụng cần dữ liệu
- **Người phục vụ** = API
- **Nhà bếp** = Server/Database

**Quy trình:**
1. Bạn xem menu và gọi món (Request - Yêu cầu)
2. Người phục vụ chuyển yêu cầu đến nhà bếp
3. Nhà bếp chế biến món ăn
4. Người phục vụ mang món ăn ra cho bạn (Response - Phản hồi)

API hoạt động tương tự: nó nhận yêu cầu từ ứng dụng, xử lý, và trả về kết quả.

### Ví dụ 2: Ổ cắm điện

- **Ổ cắm** = API
- **Thiết bị điện** = Ứng dụng
- **Nguồn điện** = Server/Database

Bạn chỉ cần cắm phích vào ổ cắm (gọi API) mà không cần biết điện được phát ra như thế nào.

## API Trong Thực Tế

### 1. Đăng nhập bằng Facebook/Google

Khi bạn thấy nút "Đăng nhập bằng Facebook" trên một website:
- Website đó gọi **Facebook API**
- Facebook xác thực tài khoản của bạn
- Facebook trả về thông tin cơ bản (tên, email...)
- Website nhận thông tin và cho bạn đăng nhập

### 2. Ứng dụng thời tiết

Khi bạn mở app xem thời tiết:
- App gọi **Weather API** (VD: OpenWeatherMap)
- Gửi vị trí của bạn
- API trả về nhiệt độ, độ ẩm, dự báo...
- App hiển thị thông tin đẹp mắt cho bạn

### 3. Thanh toán online

Khi bạn mua hàng trên Shopee/Lazada:
- Website gọi **Payment API** (VNPay, MoMo, Stripe...)
- Bạn nhập thông tin thanh toán
- API xử lý giao dịch
- API trả về kết quả (thành công/thất bại)

### 4. Google Maps

Khi bạn đặt Grab/GoJek:
- App gọi **Google Maps API**
- Gửi điểm đón và điểm đến
- API tính toán tuyến đường
- API trả về bản đồ và thời gian dự kiến

## Cách API Hoạt Động

```
┌─────────────┐         ┌─────────────┐         ┌─────────────┐
│   Client    │         │     API     │         │   Server    │
│  (Ứng dụng) │         │  (Cầu nối)  │         │ (Dữ liệu)   │
└──────┬──────┘         └──────┬──────┘         └──────┬──────┘
       │                       │                       │
       │  1. Gửi Request       │                       │
       │──────────────────────>│                       │
       │   (Yêu cầu)           │  2. Xử lý yêu cầu     │
       │                       │──────────────────────>│
       │                       │                       │
       │                       │  3. Trả về dữ liệu    │
       │                       │<──────────────────────│
       │  4. Nhận Response     │                       │
       │<──────────────────────│                       │
       │   (Phản hồi)          │                       │
```

### Các Thành Phần Chính:

1. **Client (Khách hàng)**
   - Ứng dụng web, mobile app, hoặc hệ thống khác
   - Gửi yêu cầu (request) đến API

2. **API (Giao diện)**
   - Nhận yêu cầu từ client
   - Xác thực và kiểm tra quyền truy cập
   - Chuyển tiếp yêu cầu đến server
   - Nhận và trả về kết quả cho client

3. **Server (Máy chủ)**
   - Xử lý logic nghiệp vụ
   - Truy vấn database
   - Trả về dữ liệu cho API

## Loại API Phổ Biến

### 1. REST API (RESTful API)
- Phổ biến nhất hiện nay
- Sử dụng HTTP methods (GET, POST, PUT, DELETE)
- Dữ liệu thường ở dạng JSON
- **Đây là loại API chúng ta sẽ học trong khóa này**

### 2. SOAP API
- Cũ hơn, phức tạp hơn
- Sử dụng XML
- Thường dùng trong hệ thống ngân hàng, tài chính

### 3. GraphQL API
- Mới hơn, linh hoạt hơn
- Client có thể yêu cầu chính xác dữ liệu cần thiết
- Phổ biến trong các ứng dụng hiện đại

### 4. WebSocket API
- Kết nối realtime 2 chiều
- Dùng cho chat, game online, stock trading

## Ví Dụ Cụ Thể

### API Request (Yêu cầu)

```
GET https://api.example.com/users/123
```

Giải thích:
- `GET` = phương thức (lấy dữ liệu)
- `https://api.example.com` = địa chỉ server
- `/users/123` = endpoint (lấy thông tin user có ID = 123)

### API Response (Phản hồi)

```json
{
  "id": 123,
  "name": "Nguyễn Văn A",
  "email": "nguyenvana@example.com",
  "age": 25
}
```

Đây là dữ liệu trả về dạng JSON (JavaScript Object Notation).

## Tại Sao API Quan Trọng?

### 1. Tái sử dụng
- Một API có thể phục vụ nhiều ứng dụng
- Ví dụ: Google Maps API được dùng bởi Grab, GoJek, Foody...

### 2. Bảo mật
- Không cần chia sẻ database trực tiếp
- API kiểm soát được ai truy cập cái gì

### 3. Hiệu quả
- Tiết kiệm thời gian phát triển
- Không cần viết lại code đã có

### 4. Tích hợp
- Kết nối các hệ thống khác nhau
- Ví dụ: Website bán hàng + Hệ thống thanh toán + Hệ thống vận chuyển

### 5. Mobile & Web
- Cùng một API phục vụ cả web và mobile app
- Giảm công sức phát triển và bảo trì

## Thuật Ngữ Cơ Bản

| Thuật Ngữ | Tiếng Việt | Ý Nghĩa |
|-----------|-----------|---------|
| API | Giao diện lập trình ứng dụng | Cầu nối giữa các ứng dụng |
| Request | Yêu cầu | Thông điệp gửi đến API |
| Response | Phản hồi | Thông điệp API trả về |
| Endpoint | Điểm cuối | URL cụ thể của một tính năng API |
| Client | Khách hàng | Ứng dụng gọi API |
| Server | Máy chủ | Nơi xử lý logic và lưu dữ liệu |
| JSON | - | Định dạng dữ liệu phổ biến |

## Câu Hỏi Ôn Tập

Hãy thử trả lời các câu hỏi sau:

1. API là gì? Giải thích bằng ngôn ngữ của bạn.
2. Hãy kể 3 ví dụ về API mà bạn sử dụng hàng ngày.
3. Ba thành phần chính trong kiến trúc API là gì?
4. REST API là gì? Tại sao nó phổ biến?
5. Request và Response khác nhau như thế nào?

<details>
<summary><b>Đáp án gợi ý</b></summary>

1. **API là gì?**
   - API là giao diện cho phép các ứng dụng giao tiếp với nhau. Giống như người phục vụ nhà hàng, API nhận yêu cầu từ ứng dụng và trả về kết quả.

2. **3 ví dụ về API:**
   - Đăng nhập bằng Facebook/Google
   - App xem thời tiết
   - Thanh toán online (MoMo, VNPay)
   - Google Maps trong Grab
   - (Bất kỳ ví dụ nào bạn nghĩ ra đều được!)

3. **Ba thành phần chính:**
   - Client (ứng dụng gọi API)
   - API (cầu nối xử lý yêu cầu)
   - Server (xử lý logic và dữ liệu)

4. **REST API:**
   - REST API sử dụng HTTP methods và JSON
   - Phổ biến vì đơn giản, dễ hiểu, dễ sử dụng
   - Hoạt động tốt với web và mobile

5. **Request vs Response:**
   - Request: Yêu cầu gửi từ client đến API (hỏi)
   - Response: Kết quả API trả về cho client (trả lời)
</details>

## Tổng Kết

Trong bài này, bạn đã học:

- ✅ API là giao diện kết nối các ứng dụng
- ✅ API hoạt động như người phục vụ nhà hàng
- ✅ API được sử dụng rất nhiều trong cuộc sống
- ✅ REST API là loại phổ biến nhất
- ✅ API có Request (yêu cầu) và Response (phản hồi)

## Bài Tiếp Theo

Bây giờ bạn đã hiểu API là gì, hãy tìm hiểu [Tại Sao Cần Test API?](./tai-sao-can-test-api.md)

---

[⬅️ Về Chương 1](./README.md) | [Tiếp Theo: Tại Sao Cần Test API? ➡️](./tai-sao-can-test-api.md)
