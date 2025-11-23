# Hướng Dẫn Deploy GitBook Lên Vercel

## Chuẩn Bị

GitBook của bạn đã được cấu hình và sẵn sàng để deploy với các files:
- `book.json` - Cấu hình GitBook
- `SUMMARY.md` - Cấu trúc menu
- `vercel.json` - Cấu hình Vercel deployment
- `.gitignore` - Loại trừ các files không cần thiết

## Phương Pháp 1: Deploy Qua GitHub + Vercel Dashboard (Khuyến nghị)

### Bước 1: Push code lên GitHub

```bash
# Thêm tất cả files
git add .

# Commit changes
git commit -m "Setup GitBook with Honkit for Vercel deployment"

# Push lên GitHub
git push origin main
```

### Bước 2: Kết nối với Vercel

1. Truy cập [Vercel Dashboard](https://vercel.com)
2. Đăng nhập hoặc đăng ký tài khoản (có thể dùng GitHub account)
3. Click **"Add New"** → **"Project"**
4. Chọn repository GitHub của bạn: `hongbietcode/api-testing-tutorial`
5. Vercel sẽ tự động phát hiện cấu hình từ `vercel.json`
6. Kiểm tra các thiết lập:
   - **Build Command**: `yarn build` (đã được cấu hình)
   - **Output Directory**: `_book` (đã được cấu hình)
   - **Install Command**: `yarn install` (đã được cấu hình)
7. Click **"Deploy"**

### Bước 3: Chờ Deploy Hoàn Thành

Vercel sẽ:
- Cài đặt dependencies
- Build GitBook
- Deploy lên CDN
- Cung cấp URL truy cập (ví dụ: `https://api-testing-tutorial.vercel.app`)

## Phương Pháp 2: Deploy Qua Vercel CLI

### Bước 1: Cài đặt Vercel CLI

```bash
yarn global add vercel
# hoặc
npm install -g vercel
```

### Bước 2: Login vào Vercel

```bash
vercel login
```

### Bước 3: Deploy

```bash
# Lần đầu tiên - sẽ hỏi một số câu hỏi về project
vercel

# Hoặc deploy trực tiếp production
vercel --prod
```

Vercel CLI sẽ:
- Tự động phát hiện cấu hình từ `vercel.json`
- Build và deploy project
- Cung cấp URL preview hoặc production

## Cập Nhật GitBook Sau Khi Deploy

### Với GitHub + Vercel Dashboard:
Mỗi khi bạn push code lên GitHub, Vercel sẽ tự động rebuild và deploy:

```bash
# Sửa nội dung tài liệu
# ...

# Commit và push
git add .
git commit -m "Update documentation"
git push origin main
```

### Với Vercel CLI:
```bash
# Deploy lại
vercel --prod
```

## Test Local Trước Khi Deploy

Để xem GitBook trên máy local trước khi deploy:

```bash
# Serve GitBook trên http://localhost:4000
yarn dev

# Hoặc build và xem static files
yarn build
# Sau đó mở file _book/index.html trong trình duyệt
```

## Cấu Hình Domain Tùy Chỉnh (Tùy Chọn)

Nếu bạn có domain riêng:

1. Vào **Project Settings** trên Vercel Dashboard
2. Chọn tab **"Domains"**
3. Thêm domain của bạn (ví dụ: `api-testing.yourdomain.com`)
4. Cấu hình DNS records theo hướng dẫn của Vercel

## Xử Lý Lỗi

### Lỗi Build Failed

Nếu build bị lỗi trên Vercel, check:
1. **Build Logs** trên Vercel Dashboard
2. Đảm bảo `yarn build` chạy thành công trên local
3. Kiểm tra `book.json` có đúng syntax không

### Lỗi 404 Page Not Found

- Đảm bảo `vercel.json` có `outputDirectory: "_book"`
- Check `_book/index.html` có tồn tại sau khi build

### Các Files Không Hiển Thị Đúng

- Check `.gitignore` không block các files cần thiết
- Đảm bảo tất cả markdown files được list trong `SUMMARY.md`

## Thông Tin Thêm

### Scripts Có Sẵn

```bash
yarn dev      # Serve GitBook local (http://localhost:4000)
yarn build    # Build GitBook thành static HTML
yarn clean    # Xóa thư mục _book
```

### Cấu Trúc Project

```
api-testing/
├── 01-gioi-thieu/
├── 02-kien-thuc-co-ban/
├── 03-postman-co-ban/
├── ...
├── book.json           # GitBook config
├── SUMMARY.md          # Menu structure
├── README.md           # Homepage
├── vercel.json         # Vercel deployment config
└── _book/              # Built files (auto-generated)
```

## Kết Quả Mong Đợi

Sau khi deploy thành công, bạn sẽ có:
- ✅ GitBook online với URL từ Vercel
- ✅ Tự động deploy khi push code (nếu dùng GitHub integration)
- ✅ HTTPS mặc định
- ✅ CDN toàn cầu - tốc độ nhanh
- ✅ Preview deployment cho mỗi pull request

## Liên Hệ & Hỗ Trợ

Nếu gặp vấn đề, check:
- [Vercel Documentation](https://vercel.com/docs)
- [Honkit Documentation](https://honkit.netlify.app/)
- [Project Issues](https://github.com/hongbietcode/api-testing-tutorial/issues)
