# Nhật ký công việc hằng ngày – Mô tả chức năng

Ứng dụng web một file (`index.html`) dùng cho Phòng Thông tin – Lưu trữ, Trung tâm Quản lý nhà và Giám định xây dựng (HMCIC). Dùng để ghi nhật ký công việc, theo dõi bài các phòng gửi đăng lên hmcic.vn, lưu số hiệu văn bản của Ban Quản trị, quản lý quy trình gói thầu và in báo cáo.

- Giao diện tiếng Việt, tối ưu cho **máy tính** (không làm riêng cho điện thoại).
- Mở trực tiếp file `index.html` bằng trình duyệt, không cần cài đặt, không cần máy chủ.
- Toàn bộ dữ liệu lưu trong **localStorage của trình duyệt** trên máy đang dùng. Muốn chuyển máy hoặc dự phòng thì dùng **Xuất / Nhập JSON**.
- Có chế độ **sáng / tối** (nút ở thanh trên cùng, được nhớ cho lần sau).
- Thư viện tải qua CDN nên cần có mạng khi mở: Tailwind CSS, Font Awesome, Chart.js (biểu đồ), docx.js (xuất Word).

---

## 1. Bố cục chung

| Khu vực | Nội dung |
|---|---|
| Thanh nhắc việc (trên cùng) | Nhắc phòng đến lượt gửi bài trong tháng; nhắc sao lưu JSON nếu từ 7 ngày chưa sao lưu. Có nút "Bỏ qua" (ẩn trong ngày). |
| Thanh tiêu đề | 5 tab: **Tổng quan**, **Nhật ký công việc**, **Bài đăng các phòng**, **Văn bản Ban QT**, **Quy trình gói thầu**. |
| Nút nhanh | **In báo cáo** (nổi bật), **Danh mục**, **Xuất JSON**, **Nhập JSON**, nút đổi sáng / tối. |

---

## 2. Tab "Tổng quan" (Dashboard)

### 2.1 Phần đầu
- Tiêu đề, ngày hôm nay, số tuần hiện tại (tuần bắt đầu từ thứ Hai, tuần 1 là tuần chứa ngày 1/1).
- 3 thẻ tóm tắt: số việc tuần này, số bài đăng tháng này, số văn bản Ban QT tháng này.
- Nút **In báo cáo tuần**.

### 2.2 Cần chú ý
Tự động liệt kê những việc nên xử lý hôm nay:
- Phòng đến lượt gửi bài trong tháng nhưng chưa gửi.
- Bài đăng chờ duyệt quá 3 ngày, bài đang trả lại phòng sửa.
- Tuần này chưa backup Trang thông tin.
- Chưa sao lưu dữ liệu JSON từ 7 ngày trở lên.
- Nếu không có gì: hiện "Không có việc nào cần chú ý".

Mỗi mục có nút xử lý ngay (ví dụ "Đã backup xong", "Sao lưu ngay", "Xem bài đăng").

### 2.3 Việc định kỳ hằng tuần
- Ô tích **Backup Trang thông tin** của tuần hiện tại. Khi tích, ứng dụng tự ghi một việc "Backup Trang thông tin" vào Nhật ký (danh mục Công nghệ thông tin). Bỏ tích thì việc đó bị gỡ.
- Dải 8 tuần gần nhất, ô xanh là tuần đã backup.

### 2.4 Chọn khoảng thời gian xem số liệu
- Các nút chọn nhanh: **Tuần này, Tuần trước, Tháng này, Tháng trước, Quý này, Năm nay, Tất cả, Tùy chọn** (chọn từ ngày – đến ngày).
- Lựa chọn được nhớ cho lần mở sau. Mặc định là Tháng này.
- Dòng thông tin cho biết đang xem khoảng nào, dài bao nhiêu ngày và đang so với kỳ nào.
- **Kỳ trước** là khoảng liền trước có cùng số ngày (kỳ chưa kết thúc như "Tháng này" được tính đến hôm nay để so sánh công bằng). Chế độ "Tất cả" không có kỳ trước.
- Nút **In báo cáo kỳ này**: mở hộp thoại in báo cáo với đúng khoảng ngày đang xem.

Mọi số liệu và biểu đồ bên dưới đều theo khoảng thời gian này.

### 2.5 Khối "Nhật ký công việc"
- **5 thẻ số liệu**, mỗi thẻ có mũi tên tăng / giảm và phần trăm so với kỳ trước, kèm thanh tỷ trọng trên tổng số việc:
  - Tổng công việc
  - Văn bản đã đăng tải
  - Văn bản soạn thảo
  - Lượt trích lục hồ sơ (phiếu yêu cầu PYC)
  - Phiếu trình CNTT
- **Biểu đồ cột chồng "Xu hướng công việc"**: chia theo Đã đăng tải / Soạn thảo / Công việc khác. Nhóm theo Ngày, Tuần, Tháng hoặc Tự động (tự chọn theo độ dài khoảng). Rê chuột vào cột để xem tổng.
- **Biểu đồ tròn** phân bổ theo danh mục.
- **Biểu đồ thanh ngang** so sánh số việc theo danh mục giữa kỳ này và kỳ trước.
- **Danh sách "Công việc trong kỳ"**: 6 việc mới nhất.
- Nút **Xem danh sách công việc trong kỳ**: chuyển sang tab Nhật ký với bộ lọc ngày đã điền sẵn.

### 2.6 Khối "Bài đăng các phòng"
- 4 thẻ: bài nhận trong kỳ, đã đăng, chưa đăng trong kỳ, số phòng đã gửi bài.
- Thanh trạng thái các bài nhận trong kỳ (Chờ duyệt / Đã duyệt / Đã đăng / Trả lại sửa).
- Số bài theo từng phòng (phần xanh là đã đăng).
- Khung **lượt cung cấp bài của tháng hiện tại**: danh sách phòng đến lượt, phòng nào đã gửi (xanh) hay chưa (đỏ), thanh tiến độ, nút "Đổi phòng tháng này". Khung này luôn tính theo tháng hiện tại.
- Danh sách bài nhận trong kỳ.

### 2.7 Khối "Quy trình gói thầu"
- Mỗi gói đang thực hiện hoặc tạm dừng hiển thị tên gói, thanh tiến độ (số bước đã xong / tổng số bước) và bước hiện tại. Bấm vào để mở tab Quy trình.

### 2.8 Khối "Văn bản Ban QT"
- Số văn bản trong kỳ (so với kỳ trước), tổng số văn bản và danh sách văn bản trong kỳ.

### 2.9 Lối tắt
Bốn nhóm nút làm ngay không cần chuyển tab: Nhật ký công việc, Bài đăng các phòng, Văn bản Ban QT, Báo cáo và dữ liệu.

---

## 3. Tab "Nhật ký công việc"

### 3.1 Thêm / sửa công việc
Hai nút thêm nhanh: **Thêm công tác đăng tải thông tin** và **Thêm công tác khác**. Các trường:

| Trường | Ghi chú |
|---|---|
| Ngày hoàn thành | Mặc định là hôm nay |
| Danh mục | Chọn trong danh sách danh mục |
| Số VB | Ví dụ `16294/TB-TTQLNGĐXD-PTN`. Cảnh báo ngay nếu số này đã tồn tại |
| Ngày tháng của văn bản | Ngày ban hành văn bản |
| Nội dung công việc | Trích yếu hoặc mô tả |
| Văn bản do phòng soạn thảo | Ô tích (hiện ở các danh mục khác ngoài "Đăng tải thông tin") |

### 3.2 Phân loại 3 nhóm công việc
- **Đã đăng tải**: công việc thuộc danh mục "Đăng tải thông tin".
- **Soạn thảo**: công việc ở danh mục khác có tích "văn bản do phòng soạn thảo".
- **Công việc khác**: còn lại.

Việc cũ chưa phân loại được đoán tự động dựa vào ký hiệu số văn bản (TTLT, TLT, PTr…).

### 3.3 Danh sách, lọc, tìm kiếm
- Danh sách nhóm theo tuần (Tuần n, từ ngày – đến ngày).
- Bộ lọc: từ ngày – đến ngày, danh mục, chỉ hiện văn bản soạn thảo, ô tìm kiếm tự do (số thông báo, tên chung cư, địa điểm, từ khóa…).
- Mỗi dòng có nút sửa, xóa và ô chọn.

### 3.4 Thao tác hàng loạt
Chọn nhiều dòng rồi: **Đổi danh mục**, **Đánh dấu / Bỏ đánh dấu soạn thảo**, **Xóa đã chọn**, **Bỏ chọn**.

---

## 4. Tab "Bài đăng các phòng"

Theo dõi các bài mà các phòng gửi để Phòng Thông tin – Lưu trữ đăng lên trang hmcic.vn.

### 4.1 Thông tin một bài đăng
Phòng gửi bài, chuyên mục trên website, số văn bản, ngày tháng của văn bản, trích yếu, ngày nhận, trạng thái, ngày đăng, link bài trên hmcic.vn, ghi chú.

### 4.2 Trạng thái
**Chờ duyệt → Đã duyệt → Đã đăng**, hoặc **Trả lại sửa** (phòng sửa lại).

### 4.3 Tự động ghi vào Nhật ký
- Khi chuyển bài sang **Đã đăng**, ứng dụng tự tạo một việc trong Nhật ký (danh mục Đăng tải thông tin) với ngày đăng, số văn bản, ngày văn bản, trích yếu.
- Sửa bài thì việc trong Nhật ký được cập nhật theo. Đổi trạng thái khỏi "Đã đăng" thì việc đó bị gỡ.
- Nếu số văn bản đã có sẵn trong Nhật ký thì không ghi thêm (tránh trùng). Ứng dụng cũng cảnh báo số văn bản trùng khi nhập bài.

### 4.4 Lọc và tìm kiếm
Khoảng ngày nhận (nút nhanh Tháng này / Quý này / Tất cả), phòng, trạng thái, ô tìm theo số văn bản, trích yếu, chuyên mục, ghi chú, link.

### 4.5 Thống kê theo phòng
Bảng cho từng phòng: đã gửi, đã đăng, chưa đăng, ngày gửi gần nhất. Phòng im lặng quá 30 ngày được tô nổi bật. Bấm vào một dòng để lọc theo phòng đó.

### 4.6 Quay vòng 4 phòng mỗi tháng
- Danh sách phòng mặc định: TCHC, QLKTCT, QLVH, GĐXD, TVXD, QLN, PTN, TSH, TTLT.
- Mỗi tháng **4 phòng** đến lượt cung cấp bài; sang tháng sau lượt xoay sang 4 phòng kế tiếp theo thứ tự.
- **Quản lý phòng**: thêm, đổi tên, xóa, sắp thứ tự phòng.
- **Đổi phòng tháng này**: chỉ định tay 4 phòng của tháng hiện tại, có thể đặt lại về lịch quay vòng tự động.
- Phòng đã có bài nhận trong tháng được tính là đã gửi.

### 4.7 Xuất Word
Xuất danh sách bài đăng theo bộ lọc hiện tại ra file `.docx`.

---

## 5. Tab "Văn bản Ban QT"

Sổ lưu số hiệu văn bản của Ban Quản trị trang thông tin điện tử.
- Mỗi văn bản gồm: **số hiệu** (ví dụ `01/QĐ-BQTTTĐT`), **ngày tháng**, **trích yếu**.
- Cảnh báo khi số hiệu bị trùng.
- Tìm kiếm theo số hiệu hoặc trích yếu; sửa, xóa từng văn bản.

---

## 6. Tab "Quy trình gói thầu"

Quản lý thứ tự các bước và văn bản của từng gói thầu. Chỉ theo dõi **thứ tự và trạng thái**, không có hạn chót.

### 6.1 Tạo gói thầu từ mẫu
| Mẫu | Ghi chú |
|---|---|
| Chỉ định thầu tư vấn – quy trình của đơn vị (7 bước) | Quy trình đơn vị đã làm với gói thiết kế website |
| Chỉ định thầu thông thường | Khung theo luật |
| Chỉ định thầu rút gọn | Khung theo luật |
| Chào hàng cạnh tranh | Khung theo luật |
| Đấu thầu rộng rãi | Khung theo luật |
| Trống | Tự thêm từng bước |

Các mẫu "khung theo luật" dựa trên Luật Đấu thầu 22/2023/QH15 và Nghị định 24/2024/NĐ-CP, **chỉ là khung tham khảo**. Cần đối chiếu quy định hiện hành và quy chế của đơn vị rồi sửa lại cho đúng.

Thông tin gói thầu: tên, số / ký hiệu, giá gói thầu, nhà thầu / đơn vị tư vấn, ngày, ghi chú, trạng thái gói (**Đang thực hiện / Tạm dừng / Đã hoàn thành**). Dữ liệu mẫu có sẵn một gói thầu thiết kế website.

### 6.2 Các bước của quy trình
- Mỗi bước có: tên bước, văn bản cần có, ngày, ghi chú, trạng thái (**Chưa làm / Đang làm / Chờ phản hồi / Đã xong / Bỏ qua**).
- Thêm, sửa, xóa, sắp xếp lại các bước. Hiển thị dạng đường thời gian (timeline) kèm tiến độ "x/y bước" và "Đang ở bước n".

### 6.3 Văn bản trong từng bước
Mỗi bước chứa các văn bản thuộc 3 loại: **Văn bản đến**, **Văn bản đi**, **Tờ trình / Nội bộ**. Khi thêm văn bản có hai cách:
1. **Chọn văn bản đã soạn**: lấy từ những việc đã đánh dấu "soạn thảo" trong Nhật ký (có tìm kiếm, lọc ngày, chọn nhiều văn bản một lần, chọn loại cho từng văn bản). Văn bản được **liên kết**: xóa khỏi bước thì công việc trong Nhật ký vẫn còn.
2. **Thêm văn bản mới**: nhập số hiệu, ngày, trích yếu, link tham chiếu. Có thể chọn tự ghi vào Nhật ký ở một danh mục (ứng dụng nhớ danh mục chọn lần trước). Việc được tạo kèm theo sẽ bị xóa khi xóa văn bản, bước hoặc gói thầu đó.

Cùng một số hiệu được gộp, không tạo trùng.

### 6.4 Khác
- Thẻ tóm tắt (số gói theo trạng thái), lọc theo trạng thái, tìm kiếm theo tên gói, nhà thầu, tên bước, số hiệu hoặc trích yếu văn bản.
- **Xuất Word** quy trình ra file `.docx`.

---

## 7. Báo cáo (In / Xuất Word)

Nút **In báo cáo** trên thanh trên cùng, trên Dashboard và ở lối tắt.

- Chọn **từ ngày – đến ngày**; hộp thoại hiển thị ngay tổng hợp số văn bản đã đăng tải, soạn thảo và việc khác trong khoảng đó.
- Tùy chọn **ghi thêm phần tổng hợp số liệu** vào báo cáo.
- Tiêu đề: *Báo cáo tuần (từ ngày dd/mm/yyyy đến ngày dd/mm/yyyy)*.
- Các việc **nhóm theo danh mục**, sắp theo ngày. Mỗi mục trình bày theo mẫu: dấu `+`, số văn bản, ngày dd/mm/yyyy, rồi trích yếu.
- **In / Lưu PDF**: dùng hộp thoại in của trình duyệt (khổ A4). **Xuất Word (.docx)**: tải file Word cùng nội dung.

---

## 8. Quản lý danh mục công việc

Nút **Danh mục**:
- Danh mục mặc định: Đăng tải thông tin, Công nghệ thông tin, Quản trị thiết bị, Lưu trữ, Phúc đáp văn bản & Khác.
- Thêm danh mục mới (tên, màu), đổi tên, đổi màu, xóa.
- Danh mục "Đăng tải thông tin" có vai trò đặc biệt: việc thuộc danh mục này được tính là "văn bản đã đăng tải" và là nơi bài đăng tự ghi vào.

---

## 9. Sao lưu, khôi phục dữ liệu

- **Xuất JSON**: tải file sao lưu chứa toàn bộ dữ liệu (công việc, danh mục, bài đăng, phòng, lịch quay vòng, văn bản Ban QT, quy trình gói thầu).
- **Nhập JSON**: khôi phục từ file đã xuất, có hộp xác nhận trước khi ghi đè. File sao lưu cũ (kể cả bản còn phần "hồ sơ công việc" của phiên bản trước) vẫn nhập được; phần không còn dùng sẽ bị bỏ qua.
- Ứng dụng ghi nhớ ngày sao lưu gần nhất và nhắc sau 7 ngày.

### Khóa dữ liệu trong localStorage
| Khóa | Nội dung |
|---|---|
| `worklog_hmcic_v1` | Công việc (Nhật ký) |
| `worklog_cats_v1` | Danh mục |
| `worklog_posts_v1` | Bài đăng các phòng |
| `worklog_depts_v1` | Danh sách phòng |
| `worklog_rot_v1` | Lịch quay vòng phòng theo tháng |
| `worklog_bqt_v1` | Văn bản Ban QT |
| `worklog_procs_v1` | Quy trình gói thầu |
| `worklog_dash_range` | Khoảng thời gian đang chọn ở Dashboard |
| `worklog_theme` | Chế độ sáng / tối |
| `worklog_last_backup`, `worklog_*_dismiss` | Ngày sao lưu gần nhất và trạng thái các nhắc việc |
| `worklog_ds_logcat` | Danh mục ghi nhật ký lần trước (cho văn bản trong quy trình) |

> Dữ liệu chỉ nằm trong trình duyệt. Xóa dữ liệu duyệt web hoặc đổi trình duyệt / máy sẽ mất dữ liệu nếu chưa xuất JSON. Khóa `worklog_dossiers_v1` của chức năng Hồ sơ công việc (đã bỏ) nếu còn trong trình duyệt thì không còn được dùng.

---

## 10. Nhắc việc tự động (tổng hợp)

| Nhắc | Điều kiện | Nơi hiển thị |
|---|---|---|
| Phòng đến lượt chưa gửi bài | Trong tháng có phòng thuộc lượt chưa có bài nhận | Thanh trên cùng, Cần chú ý, tab Bài đăng |
| Bài chờ duyệt quá hạn | Trạng thái "Chờ duyệt" từ 3 ngày trở lên | Cần chú ý, tab Bài đăng |
| Bài bị trả lại | Có bài ở trạng thái "Trả lại sửa" | Tab Bài đăng |
| Backup Trang thông tin | Tuần hiện tại chưa có việc backup | Cần chú ý, Việc định kỳ hằng tuần |
| Sao lưu JSON | Chưa sao lưu hoặc từ 7 ngày trở lên | Thanh trên cùng, Cần chú ý |
| Phòng im lặng | Phòng không gửi bài quá 30 ngày | Bảng thống kê theo phòng |

---

## 11. Công nghệ và lưu ý kỹ thuật

- Một file HTML duy nhất: HTML, CSS (Tailwind) và JavaScript thuần, không cần xây dựng (build).
- Biểu đồ: Chart.js 4.4.1. Xuất Word: docx 8.5.0. Biểu tượng: Font Awesome 6.5.1.
- Ngày hiển thị dạng dd/mm/yyyy; lưu nội bộ dạng yyyy-mm-dd.
- Tuần tính từ thứ Hai; tuần 1 là tuần chứa ngày 1/1.
- Giá trị nhập vào luôn được xử lý an toàn khi hiển thị (chống chèn mã HTML).
