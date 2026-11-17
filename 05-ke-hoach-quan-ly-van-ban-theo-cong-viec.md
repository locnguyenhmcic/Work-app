# 05 – Kế hoạch chức năng "Công việc → Văn bản" (quản lý văn bản theo từng công việc được giao)

Trạng thái: **đề xuất ý tưởng, chưa làm**. Các gói WP25–WP32 theo cùng quy ước với `02-work-packages.md`.

## 1. Vấn đề cần giải quyết
Quy trình thực tế của chủ dự án: được giao một công việc → soạn các văn bản (công văn, phiếu trình, phiếu chuyển, phụ lục…) → trình Giám đốc ký ban hành. Một công việc có **nhiều văn bản**, nhưng Nhật ký hiện chỉ có danh sách phẳng từng văn bản. Hệ quả:
- Muốn biết "công việc X gồm những văn bản nào" phải tự nhớ hoặc tự lọc từ khóa.
- Không biết văn bản nào đang **chờ Giám đốc ký**.
- Một văn bản có **hai số** (số phiếu trình khi trình, số công văn khi ban hành) nhưng chỉ ghi được một chỗ.
- Danh mục 4 đầu việc (file Excel `Workbook1.xlsx`) chưa có chỗ trong web; hôm qua phải xếp tay 274 văn bản.

## 2. Bằng chứng từ dữ liệu thật (451 việc, 61 văn bản soạn thảo / Ban QT / gói thầu)
Văn bản **đã tự gom thành cụm theo công việc** dù chưa có chức năng nào hỗ trợ:

| Cụm (công việc) | Số văn bản | Dấu hiệu gom |
|---|---|---|
| Xây dựng website mới của Trung tâm | khoảng 10 (442, 478, 479, 492, 508, 528, 544, 560, 582, 610) | từ khóa "xây dựng mới / lại Trang thông tin điện tử", "báo giá thiết kế" |
| Báo cáo định kỳ chuyển đổi số theo Kế hoạch 40-KH/BCĐTP | 4 (449, 461, 473, 494) | cùng số kế hoạch, tiêu đề lặp theo tuần |
| Báo cáo theo đề cương của Sở Xây dựng | 4 (235, 254, 267, 282) | cùng cụm "đề cương báo cáo tình hình kết quả" |
| Triển khai Kế hoạch 193/KH-UBND và QĐ 2685/QĐ-UBND | 4 (306, 308, 333, 345) | cùng số kế hoạch / quyết định được dẫn |
| Ban Quản trị và Quy chế trang thông tin điện tử | khoảng 7 (283, 305, 325, 331, Ban QT 1, 2, 3…) | "Ban quản trị", "Quy chế hoạt động trang" |
| Phổ biến Quyết định của UBND | 2 (471, 493) | "phổ biến Quyết định" |

Hai phát hiện quan trọng cho thiết kế:
1. **Có hai số cho một văn bản.** Ở 10 phiếu trình (179, 193, 207, 230, 235, 282, 300, 344, 345, 359), chủ dự án đã tự ghi số công văn ban hành vào trích yếu, ví dụ `345/PTr-TTLT … (5907/TTQLNGĐXD-TTLT 12/06/2026)` hoặc `(CV 2863/TTQLNGĐXD-TTLT ngày 16/4/2026)`. Đó là cách làm tạm vì chưa có ô riêng.
2. **Một văn bản đôi khi chạm hai công việc** (333/PC-TTLT dẫn cả Kế hoạch 193 lẫn QĐ 2685). Khuyến nghị: mỗi văn bản **thuộc một công việc**, các liên quan khác ghi bằng ghi chú.

## 3. Ý tưởng chức năng

### 3.1 Ba tầng và cách gọi
```
Đầu việc (cố định, 4 dòng từ Excel, sửa được)
  └─ Công việc được giao (mỗi lần được giao một việc)
       └─ Văn bản (phiếu trình, công văn, phiếu chuyển, phụ lục, báo cáo, văn bản đến…)
```
- **Đầu việc** = cột "Tên công việc" trong `Workbook1.xlsx`. Là phân loại lâu dài, khác với 7 "danh mục" hiện có trong web (danh mục để báo cáo tuần, đầu việc để quản lý hồ sơ). Hai thứ này **không gộp** để khỏi phá báo cáo đang chạy tốt.
- **Công việc** = một việc cụ thể được giao, có đầu và cuối ("Xây dựng website mới", "Báo cáo định kỳ chuyển đổi số tuần 32–34"…).
- **Văn bản** = các văn bản đã có trong Nhật ký, chỉ được **gắn thêm nhãn** thuộc công việc nào, không nhập lại.

### 3.2 Nguyên tắc thiết kế (để chức năng không thành gánh nặng)
- **Không nhập hai lần.** Văn bản vẫn nằm trong Nhật ký; gắn vào công việc chỉ là chọn một ô.
- **Mọi lối vào đều dẫn đến cùng kết quả:** (a) ô "Thuộc công việc" ngay trong biểu mẫu thêm việc ở Nhật ký, (b) gán hàng loạt ở thanh chọn nhiều, (c) nút "Thêm văn bản" trong màn hình công việc, (d) gợi ý gom tự động.
- **Chỉ thứ tự và trạng thái**, không hạn chót, đúng ý chủ dự án ở Quy trình gói thầu.
- **Một văn bản thuộc một công việc**; văn bản chưa gán nằm trong "hộp chưa gán".
- Chỉ đọc / sửa dữ liệu văn bản đã có; không tự xóa văn bản trong Nhật ký khi gỡ khỏi công việc.

### 3.3 Mô hình dữ liệu (tương thích ngược)
```
duty {id, name, products, order}                         // khóa worklog_duties_v1, nạp sẵn 4 dòng từ Excel
job  {id, duty, name, source, dateAssigned, status:'doing'|'wait'|'done', note}   // khóa worklog_jobs_v1
        // source: ai giao / văn bản giao việc (ô văn bản tự do)

Thêm vào mỗi bản ghi task (đều tùy chọn, bản ghi cũ vẫn hợp lệ):
  jid     id công việc
  role    'in' (văn bản đến / chỉ đạo) | 'trinh' (phiếu trình) | 'cv' (công văn) | 'pc' (phiếu chuyển)
          | 'pl' (phụ lục) | 'bc' (báo cáo) | 'khac'      // gợi ý tự động từ ký hiệu PTr / PC / TB / BC / KH / QĐ
  par     id văn bản cha (phụ lục đi kèm văn bản nào)
  sg      trạng thái ký: 'soan' (đang soạn) | 'trinh' (đã trình Giám đốc) | 'ky' (đã ký, ban hành) | 'gui' (đã gửi đi)
  outNo   số văn bản ban hành khi đã ký (khác số phiếu trình)
  outDate ngày ban hành
```
- Chọn **gắn trên bản ghi task** thay vì bảng liên kết riêng, vì quan hệ là nhiều-một; truy vấn đơn giản, nhập / xuất JSON không đổi cấu trúc, ứng dụng cũ nạp file mới vẫn chạy (trường lạ bị bỏ qua).
- Xuất JSON thêm hai khóa `duties`, `jobs`; nhập file cũ không có hai khóa này vẫn hợp lệ.
- Quy trình gói thầu **giữ nguyên**, không gộp vào chức năng này ở đợt đầu (xem mục 7).

### 3.4 Màn hình
**Tab mới "Công việc"** (tab thứ sáu):
1. **Thanh đầu:** 4 viên chọn đầu việc kèm số công việc / số văn bản; lọc theo trạng thái (Đang làm / Chờ / Xong); ô tìm; nút **Thêm công việc**.
2. **Thẻ công việc:** tên, đầu việc, nguồn giao, ngày giao, nhãn trạng thái, "x văn bản · y đang chờ ký", và **chuỗi văn bản thu gọn** dạng các chấm màu theo trạng thái ký (xám = soạn, vàng = đã trình, xanh = đã ký).
3. **Chi tiết công việc** (bấm vào thẻ, mở rộng tại chỗ hoặc ngăn bên phải):
   - Danh sách văn bản theo thời gian; phụ lục thụt vào dưới văn bản cha.
   - Mỗi dòng: nhãn vai trò, số hiệu, trích yếu, ngày, nhãn trạng thái ký và **nút một chạm**: *Đã trình* → *Đã ký* (mở ô nhập số + ngày ban hành) → *Đã gửi*.
   - Nút **Thêm văn bản**: hai tab "Chọn từ Nhật ký" (danh sách văn bản chưa gán, có tìm và lọc) / "Thêm mới" (tạo việc trong Nhật ký, tái dùng khối `ds*` của WP15).
   - Nút gỡ văn bản khỏi công việc (không xóa trong Nhật ký).
4. **Hộp "Chưa thuộc công việc nào"** ở đầu tab: số văn bản soạn thảo chưa gán và nút **Gợi ý gom** (mục 3.5).

**Ở các nơi đã có:**
- **Nhật ký:** biểu mẫu thêm / sửa việc có thêm ô "Thuộc công việc" (kèm "+ Công việc mới…") và "Vai trò"; thanh chọn nhiều thêm nút **Gán vào công việc…**; thẻ việc hiện nhãn tên công việc và bấm vào là nhảy sang công việc.
- **Dashboard:** thẻ **"Chờ Giám đốc ký"** (số văn bản đã trình chưa ký, văn bản chờ lâu nhất, bấm để mở công việc) và thẻ **"Công việc đang làm"**; nằm gần "Cần chú ý".
- **Báo cáo:** thêm tuỳ chọn "nhóm theo công việc" ở hộp thoại báo cáo, **giữ nguyên mẫu** `+ số hiệu ngày dd/mm/yyyy` rồi trích yếu.
- **Xuất Word:** "Danh mục văn bản theo đầu việc" (đầu việc → công việc → văn bản), cùng kiểu với bảng Excel đã làm tay. Không thêm xuất Excel vì chủ dự án đã yêu cầu bỏ xuất Excel / CSV trước đó.

### 3.5 Gợi ý gom tự động (phần tạo khác biệt)
Để chuyển 61 văn bản soạn thảo hiện có mà không phải gán tay từng cái:
- Thuật toán **luật, không dùng AI**: với mỗi văn bản chưa gán, rút (a) số kế hoạch / quyết định được dẫn (`193/KH-UBND`, `2685/QĐ-UBND`, `40-KH/BCĐTP`), (b) cụm từ khóa đã chuẩn hóa (`norm()`, bỏ số tuần / số hiệu), (c) khoảng cách ngày. Hai văn bản chung (a) hoặc chung ≥ 2 cụm trong (b) và gần nhau về thời gian thì vào một cụm.
- Mỗi cụm đề xuất tên (câu trích yếu phổ biến nhất), đầu việc (theo từ khóa, có thể đổi) và danh sách văn bản, kèm **lý do** ("cùng dẫn Kế hoạch 193/KH-UBND"). Người dùng **Chấp nhận / Sửa / Bỏ qua** từng cụm; không áp dụng gì khi chưa bấm.
- Kỳ vọng từ dữ liệu thật: nhận ra khoảng 6 cụm (mục 2) bao quát khoảng 30 / 61 văn bản; phần còn lại để gán tay.
- Tự đoán `role` từ ký hiệu số hiệu; tự tách số ban hành nằm trong ngoặc ở cuối trích yếu (như `(5907/TTQLNGĐXD-TTLT 12/06/2026)`) thành `outNo` / `outDate` và **hỏi xác nhận** trước khi sửa trích yếu.

## 4. Luồng làm việc mẫu
1. Được giao việc: **Thêm công việc** (tên, đầu việc, nguồn giao).
2. Soạn phiếu trình: ở Nhật ký thêm việc, chọn "Thuộc công việc"; vai trò tự đoán là *Phiếu trình*; trạng thái *Đang soạn*.
3. Trình Giám đốc: bấm **Đã trình**. Dashboard bắt đầu tính "chờ ký".
4. Giám đốc ký: bấm **Đã ký**, nhập số và ngày công văn ban hành. Số này hiện cạnh số phiếu trình.
5. Đính kèm phụ lục: thêm văn bản vai trò *Phụ lục*, chọn văn bản cha.
6. Xong: đổi công việc sang *Xong*; công việc vẫn tra cứu được và vẫn lên báo cáo.

## 5. Các gói việc (làm lần lượt; mỗi gói một phiên)

| Gói | Tên | Phụ thuộc | Cỡ | Vùng chạm vào |
|---|---|---|---|---|
| WP25 | Dữ liệu: đầu việc, công việc, trường mới trên `tasks`, chuyển đổi dữ liệu, nhập / xuất JSON | WP02, WP07 | M | khối `// ===== Công việc: dữ liệu`, `exp/imp`, `load()` |
| WP26 | Tab "Công việc": danh sách, thẻ, thêm / sửa / xóa công việc, quản lý đầu việc | WP25 | L | `#vJobs`, `#dJob`, `#dDuty`, `jb*` |
| WP27 | Chi tiết công việc: chuỗi văn bản, trạng thái ký, số ban hành, phụ lục | WP26 | L | `jbDetail`, `#dSign`, `jbSign*` |
| WP28 | Gắn văn bản vào công việc: ô trong Nhật ký, gán hàng loạt, hộp "Chọn từ Nhật ký / Thêm mới" | WP26, WP15 | M | `#dlg` (`mJob`, `mRole`), `bulkBar`, `#dJDoc` |
| WP29 | Gợi ý gom tự động và tách số ban hành | WP28 | L | `jbSuggest`, `#dSuggest` |
| WP30 | Dashboard: "Chờ Giám đốc ký", "Công việc đang làm", nhắc việc | WP27 | M | `renderDashJobs`, `DASH_ACT` |
| WP31 | Báo cáo nhóm theo công việc; xuất Word danh mục theo đầu việc | WP27, WP06 | M | `rptData`, `jbDocx` |
| WP32 | Kiểm thử bằng dữ liệu thật và tài liệu | tất cả | M | `tools/test_jobs.py`, `tools/sample_real.json` |

### Chi tiết từng gói (tóm tắt, đủ để giao phiên làm việc)
- **WP25:** hằng `DT_KEY='worklog_duties_v1'`, `JB_KEY='worklog_jobs_v1'`; `duties` nạp sẵn 4 dòng (tên + sản phẩm lấy từ `Workbook1.xlsx`); `jobs=[]`. Làm sạch trường mới của `tasks` trong `load()` (`jid` không tồn tại → xóa, `role`, `sg` ngoài danh sách → bỏ). Hàm `jbStore()`, `jbImport(raw)` (nhận `raw.duties`, `raw.jobs`), xuất JSON thêm hai khóa, nhập file cũ không lỗi. Khi xóa công việc: các văn bản **bỏ nhãn** `jid`, không bị xóa. Nghiệm thu: xuất → xóa → nhập khôi phục đủ; file JSON hiện tại của chủ dự án (451 việc) nhập không lỗi.
- **WP26:** tab thứ sáu `data-v="jobs"` và `#vJobs`; `renderJobs()`; viên đầu việc có số đếm; thẻ công việc; hộp thoại thêm / sửa công việc (`#dJob`: tên, đầu việc, nguồn giao, ngày giao, trạng thái, ghi chú) và quản lý đầu việc (`#dDuty`: thêm / sửa / sắp xếp / xóa, xóa đầu việc đang dùng bị chặn). Nghiệm thu: CRUD, lọc, tìm không dấu, sáng / tối.
- **WP27:** `jbDetail(job)`: danh sách văn bản theo ngày, phụ lục thụt dưới cha, nhãn vai trò và trạng thái ký; nút một chạm đổi `sg`; hộp `#dSign` nhập `outNo`, `outDate` khi chuyển sang *Đã ký* (số ban hành mặc định để trống, không bắt buộc); trạng thái ký chỉ đi **tiến hoặc lùi một bậc** do người dùng bấm, không tự động. Nghiệm thu: số ban hành hiện cạnh số phiếu trình; đổi trạng thái cập nhật chấm màu trên thẻ.
- **WP28:** biểu mẫu `#dlg` thêm `mJob` (danh sách công việc + "Công việc mới…") và `mRole` (tự đoán theo ký hiệu, sửa được); thanh `#bulkBar` thêm "Gán vào công việc…"; thẻ `card(t)` hiện nhãn công việc; hộp `#dJDoc` hai tab (chọn từ Nhật ký / thêm mới) dựa trên mẫu của `#dPDoc` (WP17). Nghiệm thu: gán 3 văn bản một lần, gỡ không xóa việc, văn bản chỉ thuộc một công việc (gán lại thì chuyển).
- **WP29:** `jbCluster(tasks)` trả `[{name, duty, ids, reasons}]` theo mục 3.5; hộp `#dSuggest` duyệt từng cụm; `jbSplitOutNo(content)` tách số ban hành trong ngoặc cuối trích yếu và hỏi xác nhận. Nghiệm thu bằng dữ liệu thật: có cụm "xây dựng website" ≥ 9 văn bản, cụm "40-KH/BCĐTP" đúng 4 văn bản (449, 461, 473, 494), cụm "Kế hoạch 193" có 306, 333, 345; không cụm nào gộp văn bản không liên quan; không có thay đổi dữ liệu trước khi người dùng bấm chấp nhận.
- **WP30:** thẻ "Chờ Giám đốc ký" (văn bản `sg==='trinh'`, sắp theo ngày trình, tô vàng khi quá 3 ngày; số ngày tính từ ngày ghi việc, chỉ để tham khảo, không phải hạn chót) và "Công việc đang làm"; mục trong "Cần chú ý"; theo khoảng thời gian Dashboard khi hợp lý. Nghiệm thu: số liệu khớp, bấm mở đúng công việc.
- **WP31:** hộp báo cáo thêm công tắc "nhóm theo công việc"; mẫu dòng văn bản **giữ nguyên**; tiêu đề nhóm `CÔNG VIỆC: <tên>`; văn bản chưa gán gom ở nhóm cuối. `jbDocx()`: đầu việc → công việc → văn bản, có số ban hành. Nghiệm thu: file Word mở được, số liệu khớp, mẫu không đổi khi tắt công tắc.
- **WP32:** `tools/sample_real.json` (bản ẩn danh của dữ liệu thật) và `tools/test_jobs.py`: nhập dữ liệu, chạy gợi ý, chấp nhận, kiểm tra số liệu Dashboard, báo cáo, xuất Word, nhập / xuất JSON; cập nhật `MO-TA-CHUC-NANG.md` và bảng id trong `01-architecture.md`.

Có thể giao song song sau WP25: {WP26 → WP27} và {WP28} tách riêng; WP29 chờ WP28; WP30–WP31 chờ WP27. Dùng chung các hàm đã có (`$ esc norm kindOf dsSyncLog safeUrl`), **không đổi tên**.

## 6. Rủi ro và cách giảm
| Rủi ro | Cách giảm |
|---|---|
| Chức năng thành gánh nặng nhập liệu (chủ dự án đã từng yêu cầu bỏ "Hồ sơ công việc") | Không nhập lại văn bản; gắn một ô; gợi ý gom tự động; mọi trạng thái đều tùy chọn; có thể dùng Nhật ký như cũ mà không bị ép. |
| Gom nhầm văn bản | Chỉ **đề xuất**, giải thích lý do, người dùng duyệt từng cụm; có hoàn tác bằng gỡ nhãn. |
| Lẫn "đầu việc" với "danh mục" | Đặt tên và giao diện tách biệt; tài liệu giải thích; báo cáo tuần vẫn theo danh mục như cũ. |
| Hỏng dữ liệu cũ | Trường mới đều tùy chọn; làm sạch khi nạp; kiểm thử nhập bằng file thật. |
| Trùng lặp với Quy trình gói thầu | Giữ hai chức năng độc lập ở đợt đầu; xem mục 7. |
| Một văn bản liên quan nhiều công việc | Quy ước một văn bản một công việc; liên quan khác ghi vào ghi chú; nếu thực tế cần nhiều thì mở rộng `jid` thành danh sách ở đợt sau. |

## 7. Hướng mở rộng (chưa làm)
- Cho **gói thầu** gắn vào một công việc (`proc.jid`) để văn bản của quy trình hiện luôn trong công việc cha.
- Mẫu công việc ("Trình ký công văn", "Báo cáo định kỳ") tạo sẵn chuỗi văn bản gợi ý.
- Tìm kiếm bằng chatbot (xem `04-ke-hoach-chatbot.md`) có thêm công cụ `get_job` để trả lời "công việc X gồm những văn bản nào".

## 8. Cần chủ dự án quyết định
1. **Hồ sơ công việc ngày trước bị bỏ vì lý do gì?** (quá nặng nhập liệu, giao diện rối, hay không dùng đến?) Câu trả lời quyết định mức tối giản của bản này.
2. Danh sách **đầu việc** có sửa được không, hay đúng 4 dòng như Excel? (Khuyến nghị: nạp sẵn 4 dòng và cho sửa.)
3. Có cần **trạng thái ký** (soạn / trình / ký / gửi) và **số ban hành** không? (Khuyến nghị: có, vì giải quyết đúng vấn đề hai số và chờ ký.)
4. Một văn bản thuộc **một** hay **nhiều** công việc? (Khuyến nghị: một.)
