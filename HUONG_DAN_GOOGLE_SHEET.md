# HƯỚNG DẪN THIẾT LẬP GOOGLE APPS SCRIPT

## Bước 1: Tạo Google Sheet

1. Truy cập https://sheets.google.com
2. Tạo một Sheet mới
3. Đặt tên cho Sheet (ví dụ: "Danh sách khách mời tốt nghiệp K47")
4. Tại hàng đầu tiên, tạo các cột sau:
   - Cột A: `Danh xưng`
   - Cột B: `Họ và tên`
   - Cột C: `Phản hồi`
   - Cột D: `Thời gian`

## Bước 2: Tạo Google Apps Script

1. Trong Google Sheet, click vào menu `Extensions` (Tiện ích mở rộng) → `Apps Script`
2. Xóa code mặc định và dán đoạn code sau:

```javascript
function doPost(e) {
  try {
    // Lấy spreadsheet hiện tại
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    
    // Parse dữ liệu JSON từ request
    var data = JSON.parse(e.postData.contents);
    
    // Thêm dữ liệu vào sheet
    sheet.appendRow([
      data.title,
      data.name,
      data.response,
      new Date(data.timestamp)
    ]);
    
    // Trả về response thành công
    return ContentService.createTextOutput(JSON.stringify({
      'status': 'success',
      'message': 'Đã ghi nhận phản hồi'
    })).setMimeType(ContentService.MimeType.JSON);
    
  } catch (error) {
    // Trả về response lỗi
    return ContentService.createTextOutput(JSON.stringify({
      'status': 'error',
      'message': error.toString()
    })).setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  return ContentService.createTextOutput("API đang hoạt động!");
}
```

3. Lưu project (Ctrl+S hoặc click biểu tượng Save)
4. Đặt tên cho project (ví dụ: "API Thư Mời Tốt Nghiệp")

## Bước 3: Deploy Web App

1. Click nút `Deploy` (Triển khai) → `New deployment` (Triển khai mới)
2. Click biểu tượng bánh răng bên cạnh "Select type" và chọn `Web app`
3. Thiết lập như sau:
   - **Description**: Nhập mô tả (ví dụ: "API nhận phản hồi khách mời")
   - **Execute as**: Chọn `Me` (tài khoản của bạn)
   - **Who has access**: Chọn `Anyone` (Bất kỳ ai)
4. Click `Deploy`
5. Trong hộp thoại xuất hiện:
   - Click `Authorize access` (Cấp quyền truy cập)
   - Chọn tài khoản Google của bạn
   - Click `Advanced` → `Go to [Project name] (unsafe)` (nếu có cảnh báo)
   - Click `Allow` (Cho phép)
6. Copy **Web app URL** (có dạng: `https://script.google.com/macros/s/AKfycby.../exec`)

## Bước 4: Cập nhật URL vào Website

1. Mở file `invitation.html`
2. Tìm dòng:
   ```javascript
   const GOOGLE_SCRIPT_URL = 'YOUR_GOOGLE_APPS_SCRIPT_URL_HERE';
   ```
3. Thay thế `YOUR_GOOGLE_APPS_SCRIPT_URL_HERE` bằng URL bạn vừa copy
4. Ví dụ:
   ```javascript
   const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycby.../exec';
   ```

## Bước 5: Test

1. Mở file `organizer.html` trong trình duyệt
2. Tạo một thư mời test
3. Copy link và mở trong tab mới
4. Cuộn xuống và chọn phản hồi
5. Kiểm tra Google Sheet xem dữ liệu đã được ghi vào chưa

## Lưu ý quan trọng:

- **Bảo mật**: URL này là public, bất kỳ ai có URL đều có thể gửi dữ liệu vào Sheet
- **Giới hạn**: Google Apps Script có giới hạn 20,000 requests/ngày cho web app
- Nếu cần deploy lại sau khi sửa code:
  1. Click `Deploy` → `Manage deployments`
  2. Click biểu tượng bút chì để edit
  3. Chọn `New version` trong dropdown "Version"
  4. Click `Deploy`

## Khắc phục sự cố:

### Lỗi "Authorization required"
- Quay lại Apps Script
- Click `Deploy` → `Test deployments`
- Test lại với tài khoản của bạn

### Dữ liệu không được ghi vào Sheet
- Kiểm tra URL trong file `invitation.html` có đúng không
- Mở Console trong trình duyệt (F12) để xem lỗi
- Kiểm tra quyền truy cập của Web App (phải là "Anyone")

### Lỗi CORS
- Đây là lỗi bình thường do Google Apps Script
- Code đã sử dụng `mode: 'no-cors'` để xử lý
- Dữ liệu vẫn được ghi vào Sheet dù có lỗi này

## Tùy chỉnh thêm:

Bạn có thể thêm các cột khác vào Sheet và cập nhật code Apps Script tương ứng. Ví dụ:
- Số điện thoại
- Email
- Ghi chú
