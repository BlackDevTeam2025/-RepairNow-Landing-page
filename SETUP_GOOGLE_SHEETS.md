# Google Sheets Setup Guide - RepairNow Waitlist

## Bước 1: Tạo Google Sheet

1. Vào [Google Sheets](https://sheets.google.com)
2. Tạo spreadsheet mới, đặt tên: `RepairNow Waitlist`
3. Đổi tên Sheet1 thành `Waitlist Data`
4. Tạo header row (dòng 1):
   ```
   A1: Timestamp
   B1: Email
   C1: Full Name
   D1: City
   ```

## Bước 2: Tạo Google Apps Script

1. Trong Google Sheet, click `Extensions` → `Apps Script`
2. Xóa code mặc định, paste code này:

```javascript
function doPost(e) {
  try {
    // Get the active spreadsheet
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Waitlist Data');

    // Parse the incoming data
    var data = JSON.parse(e.postData.contents);

    // Append data to sheet
    sheet.appendRow([
      data.timestamp,
      data.email,
      data.name,
      data.city
    ]);

    // Return success
    return ContentService.createTextOutput(JSON.stringify({
      'result': 'success',
      'message': 'Data added successfully'
    })).setMimeType(ContentService.MimeType.JSON);

  } catch(error) {
    // Return error
    return ContentService.createTextOutput(JSON.stringify({
      'result': 'error',
      'message': error.toString()
    })).setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  return ContentService.createTextOutput("RepairNow Waitlist API is running");
}
```

3. Click `Save` (💾 icon)
4. Đặt tên project: `RepairNow Waitlist API`

## Bước 3: Deploy Apps Script

1. Click `Deploy` → `New deployment`
2. Click gear icon ⚙️ → chọn `Web app`
3. Cấu hình:
   - **Description**: RepairNow Waitlist Form Handler
   - **Execute as**: Me (email của bạn)
   - **Who has access**: Anyone
4. Click `Deploy`
5. **Authorize access**: Click `Authorize access` → chọn tài khoản Google → `Advanced` → `Go to [project name] (unsafe)` → `Allow`
6. **Copy Web App URL**: URL sẽ có dạng:
   ```
   https://script.google.com/macros/s/AKfycbx.../exec
   ```

## Bước 4: Update Landing Page

Mở file `index.html` tìm dòng:
```javascript
const GOOGLE_SCRIPT_URL = 'YOUR_GOOGLE_SCRIPT_URL_HERE';
```

Thay bằng URL vừa copy:
```javascript
const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbx.../exec';
```

## Bước 5: Test

1. Mở `index.html` trên browser
2. Fill form và submit
3. Check Google Sheet xem data có vào không

## Troubleshooting

### Lỗi "Authorization required"
- Vào Apps Script → Run → chạy function `doPost` một lần để authorize

### Data không vào Sheet
- Check tên sheet phải là `Waitlist Data` (case sensitive)
- Check console browser (F12) xem có error gì không

### CORS errors
- Bình thường! Mode `no-cors` đã handle, data vẫn vào sheet

## Bonus: Auto Email Notification (Optional)

Thêm vào cuối function `doPost`:

```javascript
// Send email notification
MailApp.sendEmail({
  to: 'your-email@gmail.com',
  subject: '🎉 New Waitlist Signup!',
  body: `
    New user joined the waitlist:

    Name: ${data.name}
    Email: ${data.email}
    City: ${data.city}
    Time: ${data.timestamp}
  `
});
```

## Export Data

Khi muốn export:
- `File` → `Download` → `CSV` hoặc `Excel`