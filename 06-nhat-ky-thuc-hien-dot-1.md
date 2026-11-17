# Nhật ký thực hiện – Đợt 1 (WP25 + WP26 + WP28)

Tài liệu bàn giao cho AI/người làm tiếp. Đọc cùng `05-ke-hoach-quan-ly-van-ban-theo-cong-viec.md` (đặc tả đầy đủ) và `MO-TA-CHUC-NANG.md` (hiện trạng trước Đợt 1).

## 1. Bối cảnh
- Ứng dụng: một file `index.html` (HTML + Tailwind + JS thuần, lưu `localStorage`, không có máy chủ). Khoảng 2.250 dòng trước Đợt 1, khoảng 2.450 dòng sau Đợt 1.
- Mục tiêu kế hoạch 05: quản lý văn bản theo **công việc được giao** (một công việc gom nhiều văn bản: phiếu trình, công văn, phụ lục…), thuộc một **đầu việc**.
- Người dùng đã duyệt làm **WP25 + WP26 + WP28** ở Đợt 1. Các gói còn lại chưa làm.

## 2. File liên quan
| File | Vai trò |
|---|---|
| `index.html` (bản đầu ra, đã vá) | Sản phẩm. Bản gốc người dùng tải lên ở `/mnt/user-data/uploads/` là chỉ đọc, luôn sao chép sang `outputs/` rồi sửa |
| `import-du-lieu-tu-google-sheet.json` | Dữ liệu thật để nghiệm thu (451 việc, 62 văn bản soạn thảo, 7 danh mục, 4 `bqt`, 1 `procs`) |
| `05-ke-hoach-...md` | Đặc tả các WP25–WP32 |
| `MO-TA-CHUC-NANG.md` | Mô tả chức năng, **chưa cập nhật** cho Đợt 1 (thuộc WP32) |
| Chưa có trong phiên | `Workbook1.xlsx`, `02-work-packages.md`, `01-architecture.md`, `tools/test_jobs.py`, `tools/sample_real.json` |

## 3. Quyết định đã chốt với người dùng
1. Giữ nguyên 41 việc mang ngày tương lai (Backup website, hỗ trợ phục hồi dữ liệu…). Đây là lịch việc định kỳ, không xóa.
2. Số hiệu trùng `16932/TB-TTQLNGĐXD-QLKTCT`: người dùng đã tự xóa ở dữ liệu của họ.
3. Đợt 1 = WP25 + WP26 + WP28.
4. Đầu việc nạp sẵn (3 dòng, người dùng nói 4 nhưng chỉ gửi 3; dòng thứ tư họ tự thêm):
   - Các nội dung liên quan đến công tác chuyển đổi số, khoa học công nghệ, đổi mới sáng tạo
   - Tham mưu các văn bản liên quan đến công tác thông tin
   - Quản lý, vận hành và đăng tải thông tin lên Website Trung tâm
5. Làm theo khuyến nghị mục 8 của kế hoạch: đầu việc sửa được, một văn bản chỉ thuộc một công việc, mọi trường mới đều tùy chọn. Câu hỏi 1 của mục 8 (vì sao trước đây bỏ "Hồ sơ công việc") **chưa được trả lời**.

## 4. Đã làm trong `index.html`

### WP25 – Dữ liệu
- Hằng và biến: `DT_KEY='worklog_duties_v1'`, `JB_KEY='worklog_jobs_v1'`, `JB_ST` (doing/wait/done), `JB_ROLE` (in, trinh, cv, pc, pl, bc, khac), `JB_SG` (soan, trinh, ky, gui), `DT_DEFAULT`, `duties`, `jobs`.
- Hàm: `jbLoad`, `dtStore`, `jbStore`, `jbClean`, `jbImport`, `jbSafe`, `jobOf`, `dutyOf`, `jbRoleGuess`, `jbDel`.
- `jbClean()` chạy ngay trước lần `render()` đầu tiên (cuối script) và cuối `jbImport`. Nó xóa `jid` trỏ tới công việc không còn, `role` ngoài `JB_ROLE`, `sg` ngoài `JB_SG`, và sửa `status`/`duty` của công việc.
- Xuất JSON thêm `duties`, `jobs`. Nhập JSON: trường `jid, role, par, sg, outNo, outDate` của `tasks` được giữ lại (trước đây bị loại, vì dòng nhập chỉ giữ 7 trường). File không có `duties`/`jobs` vẫn nhập được: `jobs` về rỗng, `duties` giữ nguyên.
- `id` của đầu việc và công việc khi nhập được lọc qua `jbSafe` (chỉ chữ, số, `_`, `-`) vì dùng trong thuộc tính `onclick` ở HTML.
- Xóa công việc chỉ bỏ `jid` trên văn bản, không xóa văn bản.

### WP26 – Tab "Công việc"
- Nút tab `data-v="jobs"`, vùng `#vJobs > #jbBox`, thêm nhánh `renderJobs()` vào `render()`.
- Bộ lọc: viên theo đầu việc (có số đếm), trạng thái, ô tìm không dấu (tìm cả trong số hiệu và trích yếu văn bản con). Trạng thái lọc ở `jbF`, phần mở/đóng ở `jbOpen`.
- Thẻ công việc: trạng thái, đầu việc, ngày giao, nguồn giao, ghi chú; mở danh sách văn bản (nhãn vai trò, số hiệu, ngày, trích yếu, nút gỡ liên kết).
- Hộp thoại `#dJob` (thêm/sửa công việc), `#dDuty` (thêm, sửa tên và "sản phẩm", đổi thứ tự, xóa đầu việc; chặn xóa nếu còn công việc).
- Dòng nhắc "n văn bản soạn thảo chưa thuộc công việc nào".

### WP28 – Gắn văn bản
- Biểu mẫu Nhật ký (`#dlg`): thêm `#mJob` (có tùy chọn "＋ Công việc mới…") và `#mRole`. Ẩn với việc thuộc danh mục "Đăng tải thông tin". Vai trò tự đoán từ số hiệu cho đến khi người dùng chọn tay (`dataset.touched`).
- `saveM`: chỉ ghi `jid`/`role` khi `dlgMode==='other'`.
- Thanh chọn nhiều: `#bulkJob` + `#bulkJobBtn`, tùy chọn "(Gỡ khỏi công việc)".
- Thẻ việc (`card`): nhãn tên công việc, bấm gọi `jbGo(id)` để nhảy sang tab Công việc.
- Hộp thoại `#dJDoc`: chọn từ Nhật ký (tìm, lọc "chỉ soạn thảo", chọn nhiều, giữ lựa chọn khi lọc lại, tối đa 150 dòng hiển thị), chọn vai trò hoặc tự đoán, nút "Tạo văn bản mới" mở biểu mẫu Nhật ký với công việc chọn sẵn (biến `jbPre`).

## 5. Mô hình dữ liệu hiện tại
```
duties[]: {id, name, products, order}
jobs[]:   {id, duty, name, source, dateAssigned, status(doing|wait|done), note}
tasks[]:  ... + jid?, role?, par?, sg?, outNo?, outDate?   (chỉ jid và role đang được giao diện ghi)
```
`par`, `sg`, `outNo`, `outDate` mới chỉ được **giữ lại khi nhập/xuất** và làm sạch; chưa có giao diện (thuộc WP27).

## 6. Chưa làm
- **WP27**: trạng thái ký (`sg`), số ban hành (`outNo`, `outDate`), phiếu trình – công văn đi kèm (`par`). Xem mục tương ứng trong kế hoạch 05. Nhật ký này không chép lại đặc tả.
- **WP29** gợi ý gom văn bản, **WP30** Dashboard, **WP31** báo cáo/Word, **WP32** kiểm thử, tài liệu, cập nhật `MO-TA-CHUC-NANG.md`.
- Nội dung "sản phẩm" của từng đầu việc (cần `Workbook1.xlsx`).

## 7. Chưa kiểm chứng
- Mới chạy `node --check` trên đoạn `<script>` cuối: **cú pháp hợp lệ**. **Chưa chạy trong trình duyệt**, chưa thử nhập file 451 việc, chưa thử giao diện tối.
- Việc cần thử đầu tiên: xuất JSON sao lưu → nhập `import-du-lieu-tu-google-sheet.json` → số việc còn 451 → tạo công việc, gán văn bản, xuất, xóa dữ liệu, nhập lại → `jid`/`role` còn nguyên.

## 8. Lưu ý dữ liệu thật (cho WP27/WP29)
- Ký hiệu số hiệu không đồng nhất: `PTr-TTLT` (34), `Ptr-TTLT` (3), `PC-TLT` (2, có vẻ gõ sai), 2 văn bản không rõ ký hiệu. `jbRoleGuess` đã không phân biệt hoa thường, nhưng `PC-TLT` vẫn rơi vào nhánh `/pc/` nhờ tiền tố, không nhờ TTLT.
- Cụm "40-KH/BCĐTP" trong dữ liệu chỉ có **3** văn bản (449, 473, 494); văn bản 461/PTr không tồn tại. Tiêu chí nghiệm thu WP29 trong kế hoạch ghi 4, cần sửa thành 3 trừ khi người dùng xác nhận 461 có thật.
- Số ban hành ghi trong ngoặc cuối trích yếu: dò được 7 trường hợp, kế hoạch nêu 10 (thiếu 207, 300, 344, có thể ghi theo kiểu khác).
- 41 việc ngày tương lai không có số văn bản; chúng làm lệch số liệu "Năm nay/Tất cả" ở Dashboard, đã được người dùng chấp nhận.

## 9. Mẹo làm việc với file này
- Sao chép `index.html` sang `outputs/` rồi vá bằng script Python dùng `str.replace` có kiểm tra `count==1`; không dùng `cut -c` trên file tiếng Việt (cắt giữa ký tự UTF-8 làm hỏng đầu ra).
- Kiểm tra cú pháp: trích khối `<script>` cuối bằng regex, ghi ra `/tmp/chk.js`, chạy `node --check`.
- `emptyBox(msg,act,label)` dùng `data-act` chỉ có tác dụng với `DASH_ACT` ở Dashboard; đừng truyền hàm khác vào.
- Mẫu hộp thoại, lớp `.btn`, `.inp`, `rowCls`, `toast`, `esc`, `norm`, `dmy`, `ymd` dùng chung; giữ phong cách và thêm biến thể `dark:` cho mọi lớp màu mới.
- Mọi `id` đưa vào `onclick` phải qua `jbSafe` hoặc do ứng dụng tự sinh.
