# Bài 3 : Xử lý lỗi bố cục giao diện hệ thống theo dõi xe
## Phần 1. Phân tích
### 1. Định vị phần tử lỗi bằng Inspect Element
Inspect Element là công cụ cho phép kiểm tra cấu trúc HTML (DOM Tree), thuộc tính CSS và kích thước của từng phần tử trên website.

**Các bước thực hiện:**
1. Mở giao diện website theo dõi lộ trình xe.
2. Nhấn F12 để mở Developer Tools.
3. Chọn biểu tượng mũi tên (Select Element).
4. Click vào khung Thông tin tài xế đang bị chồng lấn.
5. Quan sát thẻ HTML tương ứng trong tab Elements.
6. Kiểm tra các thuộc tính CSS trong tab Styles.

Ví dụ cấu trúc HTML:
```HTML
<div class="tracking-container">
    <div class="driver-info">
        Thông tin tài xế
    </div>

    <div class="station-list">
        Danh sách trạm
    </div>
</div>
```
Phần tử cần kiểm tra là `.driver-info`, `.station-list` và phần tử cha `.tracking-container`.
### 2. Phân tích CSS Box Model
**CSS Box Model gồm 4 lớp:**
|Thành phần|	Chức năng|
|---|---|
|Content	|Vùng chứa nội dung bên trong phần tử|
|Padding|	Khoảng cách từ nội dung đến đường viền|
|Border	|Đường viền bao quanh phần tử|
|Margin|	Khoảng cách bên ngoài phần tử với các phần tử khác|
### 3. Kiểm tra sơ đồ Box Model qua Computed
**Trong DevTools:**
1. Chọn phần tử `.driver-info`.
2. Chọn tab Computed.
3. Kéo xuống phần Box Model.
4. Quan sát các thông số Margin, Border, Padding và Content.
## Phần 2: Triển khai
### 1. Đề xuất thông số CSS mới
Mục tiêu là bố trí hai khung nội dung cạnh nhau, có khoảng cách rõ ràng và không chồng lấn.

**CSS đề xuất:**
```CSS
.tracking-container {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 24px;
    width: 100%;
    box-sizing: border-box;
}

.driver-info,
.station-list {
    position: static;
    width: auto;
    margin: 0;
    padding: 16px;
    border: 1px solid #ddd;
    box-sizing: border-box;
}
```
Giải thích:
|Thuộc tính|	Giá trị|Tác dụng|
|---|---|---|
|`display`|	`grid`|	Sắp xếp các khung theo dạng lưới|
|`grid-template-columns`|	`1fr 1fr`|	Chia thành hai cột bằng nhau|
|`gap`|	`24px`|	Tạo khoảng cách giữa hai khung|
|`position`|	`static`	|Đưa phần tử về luồng bố cục thông thường|
|`margin`|	`0`	|Loại bỏ Margin cũ|
|`padding`|	`16px`	|Tạo khoảng trống bên trong|
|`box-sizing`|	`border-box`	|Tính cả Padding và Border trong kích thước khai báo|
### 2. Thực hiện trực tiếp trên DevTools
**Bước 1:** Chọn tab Elements và xác định phần tử cha.

**Bước 2:** Trong tab Styles, thêm các thuộc tính CSS mới.

**Bước 3:** Quan sát giao diện và kiểm tra hai khung đã tách biệt chưa.
## Phần 3: Kiểm thử
**Tình huống: Thay đổi Margin nhưng phần tử vẫn không di chuyển.**
Có thể có một số thuộc tính CSS khác đang ảnh hưởng hoặc ghi đè lên thuộc tính vừa chỉnh sửa.

### 1. Kiểm tra !important
Ví dụ:
```CSS
.driver-info {
    margin: 0 !important;
}
```
Nếu một quy tắc có `!important`, quy tắc Margin thông thường có thể không ghi đè được.
### 2. Kiểm tra Inline Style
Ví dụ:
```CSS
<div class="driver-info"
     style="position: absolute; top: 0;">
</div>
```
Inline Style có độ ưu tiên cao hơn CSS thông thường trong stylesheet.
### 3. Kiểm tra User Agent Stylesheet
Đây là các quy tắc CSS mặc định do trình duyệt cung cấp.

Trong tab Styles, kiểm tra mục: `user agent stylesheet`

Nếu thuộc tính mặc định của trình duyệt ảnh hưởng đến giao diện, cần xác định và ghi đè bằng quy tắc CSS phù hợp.
### 4. Kiểm tra thuộc tính Position
Nếu phần tử đang sử dụng:
```CSS
position: absolute;
```
Phần tử có thể bị đưa ra khỏi luồng bố cục thông thường và chồng lên phần tử khác.

Có thể thử thay đổi thành:
```CSS
position: static;
```
**Quy trình kiểm tra trong DevTools**
- Mở tab Computed để xem giá trị CSS thực tế.
- Mở rộng thuộc tính `margin` để xem quy tắc nào đang áp dụng.
- Quay lại tab Styles để tìm quy tắc bị ghi đè hoặc có `!important`.
- Kiểm tra `position`, `display`, `width` và các thuộc tính bố cục của phần tử cha.

**Kết luận:** Cần kiểm tra thứ tự ưu tiên CSS (CSS Cascade), Inline Style, `!important` và User Agent Stylesheet để tìm nguyên nhân khiến thay đổi Margin không có tác dụng.
