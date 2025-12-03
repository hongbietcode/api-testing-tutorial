# 3.1 Cài Đặt và Giao Diện Postman

## Mục Tiêu

Sau bài học này, bạn sẽ:
- ✅ Cài đặt được Postman trên máy tính
- ✅ Làm quen với giao diện Postman
- ✅ Hiểu các panels chính và chức năng của chúng
- ✅ Biết các shortcuts hữu ích

## 1. Cài Đặt Postman

### Tải Postman Desktop

1. Truy cập: https://www.postman.com/downloads/
2. Chọn phiên bản phù hợp với hệ điều hành:
   - **Windows**: Tải file `.exe`
   - **macOS**: Tải file `.dmg` (hỗ trợ cả Intel và Apple Silicon)
   - **Linux**: Tải file `.tar.gz` hoặc cài qua Snap

3. Cài đặt:
   - **Windows**: Chạy file `.exe` và làm theo hướng dẫn
   - **macOS**: Mở file `.dmg` và kéo Postman vào thư mục Applications
   - **Linux**: Giải nén và chạy file `Postman`

### Tạo Tài Khoản (Tùy Chọn)

Postman có thể sử dụng offline, nhưng đăng ký tài khoản miễn phí để:
- ✅ Đồng bộ collections giữa các thiết bị
- ✅ Chia sẻ collections với team
- ✅ Backup dữ liệu trên cloud
- ✅ Sử dụng tính năng collaboration

**Cách đăng ký:**
1. Mở Postman
2. Click "Sign In" hoặc "Sign Up"
3. Đăng ký bằng email hoặc Google account
4. Xác nhận email (nếu cần)

> **💡 Lưu ý**: Bạn có thể bỏ qua bước này và dùng "Skip and go to the app"

## 2. Giao Diện Postman

### Tổng Quan Giao Diện

Khi mở Postman lần đầu, bạn sẽ thấy giao diện gồm 4 phần chính:

```
┌─────────────────────────────────────────────────────────────┐
│  Header (Top Bar)                                           │
├──────────────┬──────────────────────────────────────────────┤
│              │                                              │
│   Sidebar    │         Main Work Area                       │
│              │                                              │
│              │                                              │
├──────────────┴──────────────────────────────────────────────┤
│  Footer (Status Bar)                                        │
└─────────────────────────────────────────────────────────────┘
```

### 2.1 Header (Top Bar)

Nằm ở trên cùng, chứa:
- **New** button: Tạo mới request, collection, environment, etc.
- **Import** button: Import collection từ file hoặc URL
- **Runner** button: Chạy collection tests
- **Search**: Tìm kiếm requests, collections
- **Settings** (⚙️): Cấu hình Postman
- **Account**: Quản lý tài khoản, workspace

### 2.2 Sidebar (Bên Trái)

Chứa các tabs chính:

#### **Collections Tab**
- Quản lý tất cả collections
- Tổ chức requests theo folders
- Import/Export collections

#### **Environments Tab**
- Quản lý các environments (Dev, Staging, Prod)
- Xem và chỉnh sửa variables

#### **History Tab**
- Lịch sử các requests đã gửi
- Có thể lưu lại thành collection
- Hữu ích khi cần test lại

#### **APIs Tab** (Nâng cao)
- Quản lý API schemas
- API documentation
- Mock servers

### 2.3 Main Work Area (Giữa)

Đây là nơi làm việc chính:

#### **Request Builder**
```
┌─────────────────────────────────────────────────────────┐
│ [GET ▼]  [https://api.example.com/users]    [Send]     │
├─────────────────────────────────────────────────────────┤
│ Params | Authorization | Headers | Body | Pre-request  │
│        | Scripts | Tests | Settings                    │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  (Tab content: parameters, headers, body, etc.)        │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Các phần quan trọng:**
1. **Method dropdown**: Chọn HTTP method (GET, POST, PUT, DELETE, etc.)
2. **URL bar**: Nhập endpoint URL
3. **Send button**: Gửi request
4. **Tabs**: Cấu hình request
   - **Params**: Query parameters
   - **Authorization**: Authentication config
   - **Headers**: HTTP headers
   - **Body**: Request body (cho POST, PUT)
   - **Pre-request Script**: Code chạy trước khi gửi request
   - **Tests**: Code để test response
   - **Settings**: Cấu hình riêng cho request

#### **Response Viewer**
Hiển thị sau khi click Send:
```
┌─────────────────────────────────────────────────────────┐
│ Status: 200 OK | Time: 234 ms | Size: 1.2 KB           │
├─────────────────────────────────────────────────────────┤
│ Body | Cookies | Headers | Test Results                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  {                                                      │
│    "id": 1,                                            │
│    "name": "John Doe"                                  │
│  }                                                      │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Thông tin response:**
- **Status**: HTTP status code và message
- **Time**: Thời gian response (ms)
- **Size**: Kích thước response
- **Body**: Nội dung response (JSON, HTML, XML, etc.)
- **Cookies**: Cookies trả về
- **Headers**: Response headers
- **Test Results**: Kết quả tests (nếu có)

### 2.4 Footer (Status Bar)

Hiển thị:
- Console logs (click để mở Console)
- Network errors
- Notifications

## 3. Các Panels Quan Trọng

### Console Panel

Mở bằng: `View > Show Postman Console` hoặc `Ctrl/Cmd + Alt + C`

**Công dụng:**
- Debug requests/responses
- Xem logs từ scripts
- Xem network errors chi tiết

**Ví dụ sử dụng:**
```javascript
// Trong Pre-request Script hoặc Tests
console.log("Testing user:", pm.variables.get("userId"));
console.log("Response:", pm.response.json());
```

### Environment Quick Look

Click vào icon "eye" (👁️) ở góc trên bên phải để:
- Xem giá trị các variables hiện tại
- Kiểm tra environment đang active
- Quick view mà không cần vào tab Environments

## 4. Shortcuts Hữu Ích

### Windows/Linux

| Shortcut | Chức năng |
|----------|-----------|
| `Ctrl + N` | Tạo request/tab mới |
| `Ctrl + S` | Save request |
| `Ctrl + Enter` | Send request |
| `Ctrl + K` | Search collections/requests |
| `Ctrl + Alt + C` | Mở Console |
| `Ctrl + E` | Quản lý Environments |
| `Ctrl + B` | Toggle Sidebar |
| `Ctrl + \` | Switch to next request |

### macOS

| Shortcut | Chức năng |
|----------|-----------|
| `Cmd + N` | Tạo request/tab mới |
| `Cmd + S` | Save request |
| `Cmd + Enter` | Send request |
| `Cmd + K` | Search collections/requests |
| `Cmd + Alt + C` | Mở Console |
| `Cmd + E` | Quản lý Environments |
| `Cmd + B` | Toggle Sidebar |
| `Cmd + \` | Switch to next request |

### Xem tất cả shortcuts

- Windows/Linux: `Ctrl + /`
- macOS: `Cmd + /`

## 5. Cấu Hình Khuyến Nghị

### Settings Cơ Bản

Vào **Settings** (⚙️ icon) và cấu hình:

#### General
- ✅ **Request timeout**: 0 (unlimited) hoặc 30000 (30s)
- ✅ **SSL certificate verification**: ON (tắt khi test local/dev)
- ✅ **Automatically persist variable values**: ON

#### Themes
- Chọn Light hoặc Dark theme tùy thích

#### Data
- ✅ **History**: Giữ lịch sử requests (hữu ích cho debug)
- **Sync**: Bật nếu muốn đồng bộ với cloud

#### Add-ons
- Cài Postman Interceptor nếu cần capture browser requests

## 6. Thực Hành Đầu Tiên

Hãy làm quen với giao diện bằng bài tập đơn giản:

### Bài tập 1: Khám phá giao diện

1. **Mở Postman** và làm quen với 4 phần chính
2. **Mở Console**: `Ctrl/Cmd + Alt + C`
3. **Toggle Sidebar**: `Ctrl/Cmd + B` để ẩn/hiện
4. **Mở Settings**: Click vào icon ⚙️ và xem các options
5. **Xem Shortcuts**: Nhấn `Ctrl/Cmd + /`

### Bài tập 2: Tạo workspace đầu tiên

1. Click vào **Workspaces** dropdown ở header
2. Click **Create Workspace**
3. Đặt tên: "API Testing Practice"
4. Chọn **Personal** (hoặc Team nếu có)
5. Click **Create Workspace**

> **💡 Mẹo**: Workspace giúp tổ chức công việc. Bạn có thể tạo workspace riêng cho mỗi project.

### Bài tập 3: Khám phá tabs

1. Click vào **Collections** tab ở sidebar
2. Click vào **Environments** tab
3. Click vào **History** tab
4. Quay lại **Collections** tab

## 7. Tổng Kết

Sau bài học này, bạn đã:
- ✅ Cài đặt được Postman
- ✅ Làm quen với 4 phần chính của giao diện
- ✅ Biết các panels: Request Builder, Response Viewer, Console
- ✅ Nhớ được một số shortcuts cơ bản
- ✅ Cấu hình Postman phù hợp

## 8. Câu Hỏi Thường Gặp

### Postman có miễn phí không?
Có, Postman có bản miễn phí với đầy đủ tính năng cho cá nhân. Bản trả phí dành cho teams và các tính năng nâng cao.

### Có cần đăng ký tài khoản không?
Không bắt buộc, nhưng nên đăng ký để backup và đồng bộ dữ liệu.

### Postman có chạy offline không?
Có, bạn có thể dùng Postman hoàn toàn offline.

### Có thể import requests từ cURL không?
Có, vào **Import > Raw text** và paste cURL command.

## Next Steps

Bây giờ bạn đã biết giao diện Postman, hãy tiếp tục:
- **Bài tiếp theo**: [3.2 Gửi Request Đầu Tiên](./gui-request-dau-tien.md)

---

[⬅️ Tổng Quan Chương 3](./README.md) | [Tiếp Theo: Gửi Request Đầu Tiên ➡️](./gui-request-dau-tien.md)
