# Changelog - EHC Pediatric Patient Viewer

All notable changes and milestones for the **BaocaoGiaoBan** (`BaocaoGiaoBan.exe` / `EHC_PediatricTool.exe`) project.

## [2.5.2] - 2026-11-22 (Khắc phục Lệch màu Khi Phóng to Cửa sổ & Chuẩn hóa Nút Sao chép DataGridView - Full Plan Rollout: Phase 01 to 04 Completed)

### 📋 Chuẩn hóa Độ rộng Cột Nút Sao chép DataGridView & Tỷ lệ Co giãn (Phase 01: Copy Button Width & Cell Padding Standardization)
- **Chuẩn hóa Độ rộng Cố định 92px (`src/UI/Controls/UnsignedDocumentControl.Designer.cs`, `src/UI/Controls/UnsignedDocumentControl.cs`, `tests/Test_Phase01_CopyButtonWidthAndCellPadding.cs`)**:
  - Mở rộng cột nút sao chép `colClinicalCopy` và `colOrderCopy` từ 72px/68px lên 92px cố định (`AutoSizeMode = DataGridViewAutoSizeColumnMode.None`, `Resizable = False`, `Alignment = MiddleCenter`), cell padding `(6, 2, 6, 2)`.
  - Khảo sát typography GDI+ Segoe UI 9pt Bold với nhãn `'📋 Sao chép'` (61.80px) đạt khoảng hở ngang an toàn 18.20px (vượt chuẩn SLA >= 12px), loại bỏ triệt để hiện tượng cụt chữ/glyph clipping.
  - Cân bằng `MinimumWidth` các cột co giãn trong `dgvOrderAudit` (`colOrderPatientName` 90px, `colOrderServiceName` 110px, `colOrderStaff` 80px, `colOrderViolation` 90px), đảm bảo bảng co giãn mượt mà và không bao giờ xuất hiện thanh cuộn ngang ngoài ý muốn tại 1280x720 và 1920x1080.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất `tests/Test_Phase01_CopyButtonWidthAndCellPadding.cs` (53/53 PASS).

### 🎨 Đồng bộ Phân tầng Bảng màu & Bề mặt Thị giác (Phase 02: Color Palette Harmonization & Surface Tiering)
- **Hệ thống Phân tầng 3 Cấp Màu Sắc (`src/UI/Controls/UnsignedDocumentControl.cs`, `src/UI/Controls/UnsignedDocumentControl.Designer.cs`, `tests/Test_Phase02_SurfaceColorHarmonizationAndHierarchy.cs`)**:
  - Thiết lập phân tầng 3 cấp: Canvas nền `#F1F5F9` (Master Canvas, SubTabContentPanel, UnsignedDocsContainer, OrderAuditContainer, ClinicalAuditContainer); Thanh điều hướng & Toolbars `#F8FAFC` (QueryToolbar, ClinicalAuditToolbar, ClinicalPillBar, OrderToolbar) với đường kẻ đáy đồng nhất `#E2E8F0` ở `Height - 1`; Khung chứa lưới `#FFFFFF` (GridContainerPanel, ClinicalGridWrapperPanel, OrderGridWrapperPanel, BackgroundColor DataGridViews).
  - Tích hợp cơ chế tự phục hồi `SyncSurfaceColorPalette()` tự động đồng bộ lại màu sắc khi chuyển sub-tab hoặc khởi tạo giao diện.
  - Đồng bộ màu dòng so le `#F8FAFC` trên toàn bộ các bảng lưới và chuẩn hóa padding viền `pnlGridContainer` thành `(8, 4, 8, 8)`.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất `tests/Test_Phase02_SurfaceColorHarmonizationAndHierarchy.cs` (68/68 PASS).

### 🖥️ Vẽ lại Thích ứng Khi Phóng to Cửa sổ & Khử Rung giật (Phase 03: Window Maximize Responsive Redraw & Flicker Elimination)
- **Bộ đệm Kép Toàn diện & Đồng bộ Phóng to (`src/UI/Controls/UnsignedDocumentControl.cs`, `src/UI/MainForm.cs`, `tests/Test_Phase03_WindowMaximizeResponsiveRedraw.cs`)**:
  - Triển khai đệ quy `DoubleBuffered = true` và `ResizeRedraw = true` cho tất cả 18 container panel và DataGridViews.
  - Bắt sự kiện `OnResize` tại `MainForm`: khi chuyển đổi trạng thái `WindowState == FormWindowState.Maximized` hoặc `Normal`, tự động tạm dừng layout (`SuspendLayout()`), cập nhật layout và kích hoạt `Invalidate(true)` để khử hiện tượng xé hình/vệt xám khi phóng to.
  - Tỷ lệ co giãn thích ứng `SplitterDistance = (int)(Width * 0.65)` trên `scOrderSplit` ngăn chặn hoàn toàn thanh cuộn ngang trên `dgvOrderAudit` khi phóng to 1920x1080.
  - Tự động co giãn thẻ xung đột kíp mổ (`flpSurgeryCards`) theo chiều rộng khả dụng (638px ở 1920x1080) với độ phức tạp O(1).
  - Căn giữa tuyệt đối cho tất cả các nhãn trạng thái rỗng (`lblEmptyState`, `lblOrderEmptyState`, `lblClinicalEmptyState`) với độ lệch <= 0px so với tâm toán học.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất `tests/Test_Phase03_WindowMaximizeResponsiveRedraw.cs` (76/76 PASS).

### 📦 Kiểm thử Hồi quy Toàn diện, Làm mờ ConfuserEx & Đóng gói Sản xuất (Phase 04: Regression Suite, ConfuserEx Obfuscation & Inno Setup Packaging)
- **Quy trình Đóng gói Sản xuất Toàn trình (`build.ps1`, `installer_client.iss`, `installer/setup_script.iss`, `tests/Test_Phase04_ProductionPackagingAndRegression.cs`)**:
  - Chạy toàn bộ các bộ kiểm thử hồi quy (Phases 01, 02, 03, Phase 05 Clinical Grid Layout, Phase 04 E2E) đạt 100% PASS, 0 FAIL.
  - Biên dịch mã nhị phân sản xuất Release x86 `bin/Release/BaocaoGiaoBan.exe` bằng Roslyn `csc.exe` (`/optimize+ /platform:x86`).
  - Áp dụng vá byte PE Header cờ `IMAGE_FILE_LARGE_ADDRESS_AWARE` (`0x0020`) mở rộng không gian bộ nhớ 4GB cho cả file gốc và file làm mờ.
  - Thực thi bảo vệ mã nguồn ConfuserEx 2 CLI (`confuser_client.crproj`) với Control Flow (Switch 80), Dynamic Constants Encryption, loại trừ reflection cho UI Controls và DTOs, tạo file thực thi bảo vệ `bin/Release/Obfuscated/BaocaoGiaoBan.exe` (1.15 MB).
  - Kiểm tra an ninh nhị phân (Binary Security Audit): Xác nhận 0 DB credentials, 0 Npgsql references, 0 plaintext API endpoints.
  - Biên dịch bộ cài đặt Inno Setup 6 tạo `Output/Setup_BaocaoGiaoBan_Client_v2.5.0.exe` (2.48 MB) và đồng bộ tới `publish_client/`.
  - Nghiệm thu 100% assertions qua test đơn nhất `tests/Test_Phase04_ProductionPackagingAndRegression.cs`.

## [2.5.1] - 2026-11-22 (Khắc phục Cắt chữ Header Navigation & Tối ưu Hiệu năng Quản lý Y lệnh - Full Plan Rollout: Phase 01 to 04 Completed)

### 🎨 Tối ưu Tỷ lệ Cột Lưới Kiểm toán Lâm sàng & Canh lề Đối xứng (Clinical Grid Column Layout & Alignment Remediation)
- **Chuẩn hóa Tỷ lệ Cột & Phản hồi Co giãn Linh hoạt (`src/UI/Controls/UnsignedDocumentControl.Designer.cs`, `tests/Test_Phase05_ClinicalGridColumnLayoutAndAlignment.cs`)**:
  - Khắc phục triệt để lỗi cào bằng kích thước cột (~102px cào bằng trên tất cả 10 cột của lưới `dgvClinicalAudit`) do WinForms `AutoSizeColumnsMode = Fill` tự động gán mặc định `FillWeight = 100` khi thiếu khai báo `FillWeight` và `AutoSizeMode`.
  - Khóa cứng (`AutoSizeMode = DataGridViewAutoSizeColumnMode.None`) cho các cột nhỏ có độ dài cố định: Cột STT (`colClinicalIndex`, 40px), Buồng/Giường (`colClinicalRoomBed`, 105px), Mã BN (`colClinicalPatientCode`, 85px), Thao tác (`colClinicalCopy`, 72px), Thời điểm (`colClinicalTime`, 120px), ngăn chặn việc phình to lãng phí diện tích hiển thị.
  - Sửa lỗi canh lề bất đối xứng ở cột STT (Header lề trái nhưng Cell canh giữa tạo khoảng trắng giả 80px): Đồng bộ `HeaderCell.Style.Alignment = MiddleCenter` và `DefaultCellStyle.Alignment = MiddleCenter` cho cả cột STT, Mã BN, Thao tác, Thời điểm.
  - Phân bổ trọng số co giãn thông minh (`FillWeight` / `MinimumWidth`) cho các cột chứa nội dung y khoa dài:
    - Họ tên BN (`colClinicalPatientName`): `FillWeight = 18F`, `MinimumWidth = 130px`.
    - Thuốc / Dịch vụ vi phạm (`colClinicalServiceName`): `FillWeight = 22F`, `MinimumWidth = 160px`.
    - Hướng dẫn / Y lệnh gốc (`colClinicalInstruction`): `FillWeight = 28F`, `MinimumWidth = 180px` (mở rộng lên tới 394px trên màn hình Full HD, hiển thị trọn vẹn y lệnh phức tạp).
    - Bác sĩ chỉ định (`colClinicalDoctor`): `FillWeight = 16F`, `MinimumWidth = 115px`.
    - Cảnh báo vi phạm (`colClinicalWarning`): `FillWeight = 16F`, `MinimumWidth = 150px` (hiển thị trọn vẹn huy hiệu cảnh báo nguy cơ an toàn).
  - Áp dụng cấu hình tương tự cho lưới `dgvOrderAudit` (`colOrderIndex` 38px, `colOrderPatientCode` 85px, `colOrderTime` 125px) đồng bộ `AutoSizeMode = None` và canh lề giữa đối xứng.
  - Kế thừa bảng màu hiện đại cho `dgvClinicalAudit`: Áp dụng `ColumnHeadersDefaultCellStyle = dgvHeaderStyle`, `DefaultCellStyle = dgvDefaultStyle`, `EnableHeadersVisualStyles = false`, và màu viền `GridColor = #E2E8F0`.
  - Nghiệm thu 100% assertions thành công qua bộ test tự động `tests/Test_Phase05_ClinicalGridColumnLayoutAndAlignment.cs` (92/92 PASS, 0 FAIL) trên các độ phân giải 1024x768, 1280x720, 1366x768, 1920x1080. Đóng gói bộ cài mới nhất `Setup_BaocaoGiaoBan_Client_v2.5.0.exe` (2.48 MB).

### 🚀 Nghiệm thu Hồi quy Toàn diện & Đóng gói Sản phẩm Máy khách (Phase 04: Full Regression Verification & Production Packaging)
- **Phase 04: Full Regression Verification & Production Packaging (`build.ps1`, `installer_client.iss`, `tests/verify_phase04_header_and_performance_optimization.ps1`)**:
  - Biên dịch thành công mã nhị phân máy khách bằng Microsoft .NET Framework 4.8 C# Compiler (`csc.exe`) với cờ tối ưu hóa `/optimize+ /platform:x86`, tạo file thực thi nguyên bản `bin/Release/BaocaoGiaoBan.exe` (808 KB).
  - Áp dụng kỹ thuật byte-patch cờ PE Header `IMAGE_FILE_LARGE_ADDRESS_AWARE` (`0x0020`), nâng không gian địa chỉ ảo 32-bit từ 2GB lên 4GB trên môi trường Windows x64 cho cả bản nguyên bản và bản làm mờ.
  - Thực thi quy trình làm mờ và bảo vệ mã nguồn ConfuserEx 2 (`confuser_client.crproj`) với chế độ Control Flow (Switch 80), mã hóa hằng số động (Dynamic Constants Encryption), loại trừ triệt để reflection đối với DTOs và UI Controls, tạo file thực thi bảo vệ `bin/Release/Obfuscated/BaocaoGiaoBan.exe` (1.11 MB, đạt chuẩn <= 2.5 MB).
  - Kiểm tra an ninh nhị phân (Binary Security Audit): Xác nhận 0 rò rỉ thông tin nhạy cảm (0 chuỗi kết nối PostgreSQL/IP `192.168.1.88`, 0 tham chiếu Npgsql, 0 plaintext API endpoints).
  - Đóng gói bộ cài đặt sản xuất Inno Setup 6 `Output/Setup_BaocaoGiaoBan_Client_v2.5.0.exe` (2.48 MB, nằm trong khoảng tối ưu 1.5 - 4.0 MB).
  - Tự động đồng bộ bộ cài đặt sang thư mục phát hành `publish_client/` và không gian làm việc thứ cấp `D:\skul9x\Report\EHC\Output\`.
  - Nghiệm thu thành công 100% assertions trên toàn bộ 5 bộ test hồi quy qua test đơn nhất `tests/verify_phase04_header_and_performance_optimization.ps1` (Phase 01, Phase 02, Phase 03 Layout, Phase 03 Stiprol Banner 102/102 PASS, Phase 04 E2E 57/57 PASS, 0 lỗi).

### ⚡ Làm phẳng Bố cục Thẻ Xung đột Kíp mổ & Khử Trùng lặp Tra cứu (Phase 03: Surgery Conflict Cards Layout Flattening & Debounce Deduplication)
- **Phase 03: Surgery Conflict Cards Layout Flattening & Debounce Deduplication (`src/UI/Controls/UnsignedDocumentControl.cs`, `tests/Test_Phase03_SurgeryConflictLayoutAndDeduplication.cs`, `tests/compile_phase03_surgery_conflict_layout.rsp`)**:
  - Làm phẳng cấu trúc bố cục bên trong thẻ xung đột kíp mổ (`CreateSurgeryConflictCard`) bằng cách thiết lập `Dock = DockStyle.Top` kết hợp `SuspendLayout()`/`ResumeLayout(false)` trên các container con (`tlpCardContent`, `flpSurg`, `flpConflictItem`), khử hoàn toàn vòng lặp đệ quy đa cấp (quadratic recursive child walk) trong `AdjustSurgeryConflictCardsWidth()`.
  - Tối ưu hóa `AdjustSurgeryConflictCardsWidth()` đạt hiệu năng O(N) thuần túy với thời gian thực thi trung bình 0.000 ms cho 50 thẻ xung đột (vượt trội so với ngưỡng SLA < 2.0 ms), đồng thời tích hợp bộ đệm `_lastCalculatedCardWidth` chặn đứng 100% bão sự kiện co giãn layout dư thừa.
  - Xây dựng cơ chế bộ đệm RAM phía máy khách (`_lastLoadedOrderAuditParams`) kết hợp hàm tạo khóa tham số chuẩn hóa `GetOrderAuditParamKey(departmentId, fromDate, toDate, chamberIds)`. Phục vụ dữ liệu tức thì trong 1.46 ms (< 10 ms SLA) khi thông số tra cứu không đổi, loại bỏ các lượt gọi mạng trùng lặp khi chuyển đổi qua lại giữa các subtab.
  - Triển khai kỹ thuật gom cụm tác vụ (Request Coalescing / In-Flight Task Deduplication) cho `LoadOrderAuditAsync` (`_inFlightOrderAuditTask`) và `LoadDepartmentRoomsAsync` (`_inFlightRoomsTask`), triệt tiêu hoàn toàn race condition và các request HTTP đồng thời trong quá trình người dùng thao tác nhanh.
  - Nút "🔄 Làm mới" và "⚡ Ca hiện tại" (`btnOrderQuickShift_Click`) được định tuyến vượt qua bộ nhớ đệm (`forceRefresh: true`) để tải dữ liệu mới nhất từ máy chủ theo đúng nghiệp vụ.
  - Bảo toàn trọn vẹn nhãn trạng thái rỗng (`lblSurgeryEmptyState`) ở vị trí trung thực khi chưa tra cứu hoặc khi có 0 ca xung đột kíp mổ.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/Test_Phase03_SurgeryConflictLayoutAndDeduplication.cs` (43/43 assertions PASS, 0 lỗi).

### ⚡ Chuyển đổi DataGridView Sang VirtualMode & Kết xuất Dòng Zero-Alloc (Phase 02: Order Audit VirtualMode & Zero-Alloc Row Rendering)
- **Phase 02: Order Audit DataGridView VirtualMode & Zero-Alloc Row Rendering (`src/UI/Controls/UnsignedDocumentControl.cs`, `src/UI/Controls/UnsignedDocumentControl.Designer.cs`, `tests/Test_Phase02_OrderAuditVirtualModeAndZeroAlloc.cs`)**:
  - Nâng cấp lưới `dgvOrderAudit` trong `UnsignedDocumentControl` sang chế độ `VirtualMode = true`, triệt tiêu hoàn toàn chi phí khởi tạo và cấp phát heap hàng nghìn đối tượng `DataGridViewRow` và `DataGridViewCell`.
  - Triển khai mô hình dòng siêu nhẹ `OrderGridRowItem` (struct) và bộ nhớ đệm `_orderGridItems`, thay thế phương thức cấp phát mảng `Rows.AddRange(rowList.ToArray())` bằng gán trực tiếp `dgvOrderAudit.RowCount = _orderGridItems.Count` kết hợp `Invalidate()`.
  - Giảm thời gian nạp và kết xuất lưới cho 110+ y lệnh từ ~350ms xuống còn < 1ms (đo đạc thực tế 0ms trên máy thử nghiệm), loại bỏ hoàn toàn hiện tượng đóng băng/giật lag giao diện (UI thread freeze).
  - Triển khai sự kiện `dgvOrderAudit_CellValueNeeded` cung cấp dữ liệu tức thì theo chỉ mục cột cho 9 cột hiển thị (`colOrderIndex`, `colOrderRoomBed`, `colOrderPatientCode`, `colOrderCopy`, `colOrderPatientName`, `colOrderServiceName`, `colOrderStaff`, `colOrderTime`, `colOrderViolation`).
  - Triển khai sự kiện `dgvOrderAudit_CellFormatting` định dạng màu nền và màu chữ theo phân loại vi phạm không cấp phát bộ nhớ (màu hồng nhạt `#FEF2F2` / chữ đỏ `#DC2626` cho Rule 1 Chưa thực hiện; màu vàng nhạt `#FFFBEB` / chữ cam `#EA580C` cho Rule 2 Sát giờ) sử dụng các font GDI dùng chung đã cache (`_fontViolationBold`, `_fontPatientCodeBold`).
  - Triển khai sự kiện `dgvOrderAudit_CellToolTipTextNeeded` nạp tooltip giải thích quy tắc kiểm toán theo yêu cầu (on-demand), thay vì lặp qua từng ô để gán ToolTipText.
  - Tối ưu hóa các thao tác sao chép mã bệnh nhân (`CopyOrderPatientCodeAtRow`, click nút Copy, nhấp đúp ô) truy xuất trực tiếp từ danh sách `_orderGridItems` tương ứng với `e.RowIndex`.
  - Cập nhật phương thức `ResetOrderAuditUiState` xử lý an toàn không ném ngoại lệ `InvalidOperationException` trong VirtualMode (`RowCount = 0`).
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/Test_Phase02_OrderAuditVirtualModeAndZeroAlloc.cs` (53/53 assertions PASS, 0 lỗi).

### 🎨 Tối ưu Bố cục Thanh Điều hướng & Khử Cắt chữ (Phase 01: Header Navigation Clipping Remediation)
- **Phase 01: Header Navigation Segmented Tab Layout & Ellipsis Remediation (`src/UI/MainForm.cs`, `src/UI/MainForm.Designer.cs`, `tests/Test_Phase01_HeaderNavigationClippingRemediation.cs`)**:
  - Khắc phục triệt để lỗi cắt chữ và dấu ba chấm (`...`) trên nút điều hướng số 3 (`"🛡️ GIÁM SÁT & KIỂ..."`), đảm bảo hiển thị trọn vẹn 100% dòng chữ `"🛡️ GIÁM SÁT & KIỂM TOÁN"` trên mọi độ phân giải màn hình từ 1024x600, 1280x720, 1366x768 đến 1920x1080 và 4K ở cả trạng thái cửa sổ bình thường và phóng to (Maximized).
  - Trạng thái phóng to (Maximized): Mở rộng `pnlSegmentedNav` từ `490px` lên `540px`, phân bổ `185px` cho `btnTabUnsignedDocs` (tọa độ X=337), đồng thời giữ `btnTabReport` và `btnTabInpatients` ở `165px`.
  - Trạng thái thu gọn/responsive: Tính toán động tỷ lệ co dãn các thanh bên (`pnlHeaderLeft` = 205px, `pnlHeaderRight` = 245px ở <= 1024px) để giải phóng tối thiểu 574px không gian trung tâm, phân bổ cho tab 3 tối thiểu 175px (>= 172px theo yêu cầu), triệt tiêu hoàn toàn hiện tượng đè lấn (zero-overlap) giữa `pnlSegmentedNav.Right` và các nút điều khiển trong `pnlHeaderRight` (`btnRefresh`, `lblHeaderLastUpdate`).
  - Bảo toàn định dạng phím tắt (`UseMnemonic = false`), GDI `TextRenderer` (`UseCompatibleTextRendering = false`), và vùng bo góc mượt mà qua `UpdateControlPillRegion`.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/Test_Phase01_HeaderNavigationClippingRemediation.cs` (0 lỗi).

## [2.5.0] - 2026-11-22 (Kiểm toán Giờ Dùng Thuốc Lâm Sàng & Trung tâm Cảnh báo Lâm sàng - Full Plan Rollout: Phase 01 to 04 Completed)

### 🚀 Triển khai Dịch vụ Sản xuất & Nghiệm thu Trực tiếp CSDL (Phase 04: Production Build, SCM Service Deployment & Live Verification)
- **Phase 04: Production Build, Windows Service Deployment & Live Verification (`src/Server/GoServer/ehc-server.exe`, `tools/deploy_production_service.ps1`, `Output/Setup_BaocaoGiaoBan_Server_v2.5.0.exe`, `tests/verify_phase04_production_audit_deployment.ps1`)**:
  - Biên dịch thành công Golang Production Server stripped binary (`ehc-server.exe`) với cờ `-tags=nomsgpack -ldflags="-s -w" -trimpath` và `CGO_ENABLED=0`, dung lượng 12.57 MB (nằm trong giới hạn tối ưu < 18MB).
  - Triển khai an toàn không va chạm khóa tệp (zero-collision) tới Windows SCM Service (`EhcReportApiService`): dừng dịch vụ có kiểm tra trạng thái `Stopped`, sao chép nhị phân sang tất cả thư mục mục tiêu (`publish\Server`, `publish_server`, `D:\skul9x\Report\EHC\publish\Server`, `D:\skul9x\Report\EHC\ehc-server.exe`), khởi động lại dịch vụ và xác nhận trạng thái `Running`.
  - Đóng gói bộ cài đặt máy chủ Inno Setup 6 `Output/Setup_BaocaoGiaoBan_Server_v2.5.0.exe` (5.2 MB) và đồng bộ tự động sang `publish_server\` và không gian thứ cấp `D:\skul9x\Report\EHC\Output\`.
  - Nghiệm thu trực tiếp dữ liệu lâm sàng trên CSDL bệnh viện (`bvsnbacninh`):
    - Kiểm toán thủ thuật (`GetProcedureAudit`): Khẳng định 0 cảnh báo giả đối với các dịch vụ cận lâm sàng/gây mê (`gây mê khác`, `nhuộm hai màu`, `phẫu tích các loại bệnh phẩm`), xóa bỏ cảnh báo sai lệch cho BN 20026607 và BN 25036615.
    - Kiểm toán ngày giường (`GetBedAudit`): Khẳng định 0 cảnh báo thiếu ngày giường cho bệnh nhân điều trị kết hợp BN Vũ Diệu Nhi (26033874).
    - Kiểm toán y lệnh (`GetOrderAudit`): Phân lập 100% bệnh nhân nội trú (`status = 1`), đồng bộ tuyệt đối giữa route chuyên khoa `/api/clinical-reports/pediatric/order-audit` và route chuẩn tắc `/api/clinical/order-audit`.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/verify_phase04_production_audit_deployment.ps1`.

### 🛡️ Đồng bộ & Xác minh Nhất quán Máy chủ Dự phòng (Phase 03: Standby Server & Multi-Engine Parity)
- **Phase 03: Standby Server & Multi-Engine Consistency Verification (`src/Server/BaocaoGiaoBan.Server/Services/ClinicalReportDataService.cs`, `tests/Test_Phase03_AuditParityAndContractVerification.cs`)**:
  - Đồng bộ 100% cấu trúc truy vấn CTE Materialized trong `ORDER_AUDIT_SQL` của C# ASP.NET Core Standby Server (`ClinicalReportDataService.cs`), bao gồm `active_mr AS MATERIALIZED`, `excluded_rooms AS MATERIALIZED`, và `split_masters AS MATERIALIZED`, khớp tuyệt đối với Go Production Server.
  - Xác nhận tính nhất quán và tương đương 100% cho toàn bộ 4 endpoints kiểm toán lâm sàng:
    - `GetOrderExecutionAudit`: Phân lập tuyệt đối bệnh nhân nội trú (`dm_medicalrecordstatusid = 1`), loại bỏ triệt để mệnh đề lịch sử ra viện, đồng bộ quy tắc phân tích liều dùng.
    - `GetStiprolAudit`: Nhận diện mã BHYT 40.707 và ngoại lệ dịch vụ yêu cầu (`dm_serviceobjectid = 2 AND iskhoadoituongdv = 1`).
    - `GetProcedureAudit`: Đồng bộ 3 vị từ loại trừ dịch vụ cận lâm sàng/gây mê (`gây mê khác`, `nhuộm hai màu`, `phẫu tích các loại bệnh phẩm`).
    - `GetBedAudit`: Đồng bộ điều kiện miễn trừ điều trị kết hợp (`dm_hinhthucvaovienid <> 6 AND medicalrecordid_master = 0`).
  - Kiểm tra toàn diện hợp đồng REST DTO và cấu hình serialization (camelCase, ISO 8601, nullability) trên cả hai máy chủ.
  - Biên dịch thành công Standby Server (`BaocaoGiaoBan.Server.csproj`) trên nền .NET 8 SDK với 0 cảnh báo, 0 lỗi.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/Test_Phase03_AuditParityAndContractVerification.cs`.

### 🛡️ Chuẩn hóa Phạm vi Điều trị Nội trú & Tối ưu Hóa Truy vấn Y lệnh (Phase 02: Active Inpatient Scope Refinement)
- **Phase 02: Active Inpatient Order Audit Scoping & Performance Optimization (`src/Server/GoServer/internal/repository/audit.go`, `src/Server/BaocaoGiaoBan.Server/Services/ClinicalReportDataService.cs`, `tests/Test_Phase02_ActiveInpatientsOrderAuditScope.cs`)**:
  - Tinh giản triệt để CTE `active_mr` trong `orderAuditSql`: thay thế chuỗi điều kiện OR phức tạp lịch sử (`medicalrecorddate_out >= @dtFrom`, `medicalrecorddate_in >= @dtFrom...`) bằng vị từ bất biến nội trú `WHERE mr.departmentid = @deptId AND mr.dm_medicalrecordstatusid = 1`.
  - Khử hoàn toàn cảnh báo giả từ bệnh nhân đã xuất viện (`dm_medicalrecordstatusid = 99`) phát sinh y lệnh thiếu giờ dùng thuốc trong khoảng thời gian ca trực.
  - Tối ưu hóa PostgreSQL Query Planner, cho phép sử dụng triệt để Composite Index Scan trên `(departmentid, dm_medicalrecordstatusid)` thay vì tuần tự quét bảng `tb_medicalrecord`.
  - Giữ nguyên bộ lọc phòng/buồng bệnh (`@chamberIds`) và lọc thời gian y lệnh lâm sàng (`@dtFrom`, `@dtTo`).
  - Đồng bộ logic giữa Go Production Server và C# Standby Server.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/Test_Phase02_ActiveInpatientsOrderAuditScope.cs`.

### 🛡️ Tinh chỉnh Kiểm toán Lâm sàng & Khử Báo động Giả (Phase 01: Procedure & Bed Audit Refinements)
- **Phase 01: Procedure Audit False Positive Exclusion & Bed Audit Combination Treatment Exemption (`src/Server/GoServer/internal/repository/audit.go`, `src/Server/BaocaoGiaoBan.Server/Services/ClinicalReportDataService.cs`, `tests/Test_Phase01_ProcedureAndBedAuditRefinements.cs`)**:
  - Khử triệt để báo động giả (False-Positive) trong kiểm toán thủ thuật `GetProcedureAudit`: bổ sung mệnh đề lọc `NOT ILIKE` cho 3 dịch vụ thuộc trách nhiệm Gây mê hồi sức và Giải phẫu bệnh (`%gây mê khác%`, `%nhuộm hai màu%`, `%phẫu tích các loại bệnh phẩm%`).
  - Miễn trừ bệnh nhân điều trị kết hợp (`dm_hinhthucvaovienid = 6` hoặc `medicalrecordid_master > 0`) trong kiểm toán ngày giường `GetBedAudit` (ví dụ: BN VŨ DIỆU NHI - 26033874 từ Khoa Sơ sinh sang Ngoại Nhi), ngăn chặn cảnh báo thiếu ngày giường sai lệch do khoa chính đã phụ trách kê giường.
  - Bảo toàn 100% kiểm toán ngày giường cho bệnh nhân nhập viện trực tiếp vào Ngoại Nhi (`dm_hinhthucvaovienid <> 6 AND medicalrecordid_master = 0`).
  - Đồng bộ chuẩn hóa đồng thời cả Golang Production Server và C# ASP.NET Core Standby Server.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/Test_Phase01_ProcedureAndBedAuditRefinements.cs`.

### 🛡️ Chuẩn hóa UX Lâm sàng & Xóa bỏ Phím tắt Mnemonic (Phase 01-04: Full Rollout Completed)
- **Phase 04: Integration, Regression & Production Packaging (`build.ps1`, `installer_client.iss`, `confuser_client.crproj`, `Output/Setup_BaocaoGiaoBan_Client_v2.5.0.exe`, `tests/verify_phase04_client_build_and_packaging.ps1`)**:
  - Thực thi toàn bộ regression test suite (Phase 01, Phase 02, Phase 03) với tỷ lệ đạt 100% (zero failures).
  - Biên dịch nhị phân C# WinForms Client `BaocaoGiaoBan.exe` (x86 Release) với cờ PE Header `IMAGE_FILE_LARGE_ADDRESS_AWARE` (0x0020) mở rộng không gian địa chỉ ảo lên 4GB trên hệ điều hành 64-bit.
  - Áp dụng bảo vệ ConfuserEx 2 (v1.6.0 mkaring) với Control Flow mangling (switch 80), Dynamic Constants Encryption, loại trừ an toàn toàn bộ DTOs, WinForms Controls và Models chống crash reflection.
  - Vượt qua kiểm toán an ninh nhị phân (Binary Security Audit): 0 thông tin CSDL, 0 tham chiếu Npgsql, mã hóa toàn bộ endpoint API nhạy cảm.
  - Đóng gói bộ cài đặt sản xuất Inno Setup 6 `Output/Setup_BaocaoGiaoBan_Client_v2.5.0.exe` (2.48 MB, nằm trong giới hạn tối ưu 1.5 MB - 4.0 MB).
  - Đồng bộ tự động gói cài đặt sang `publish_client/` và không gian triển khai thứ cấp `D:\skul9x\Report\EHC\Output\`.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/verify_phase04_client_build_and_packaging.ps1`.

- **Phase 03: Layout Restructuring & Filter Standardization (`src/UI/Components/MultiSelectRoomFilterControl.cs`, `src/UI/Controls/UnsignedDocumentControl.Designer.cs`, `src/UI/Controls/UnsignedDocumentControl.cs`, `tests/Test_Phase03_LayoutRestructuringAndFilterStandardization.cs`)**:
  - Đảo ngược phân cấp thị giác trên Tab 3 (`Kiểm toán & Cảnh báo lâm sàng`): chuyển thanh công cụ bộ lọc (`flpClinicalAuditToolbar` / `flpAuditFilterBar`) lên đỉnh container (Y=0), đặt hàng nút pill danh mục cảnh báo (`flpClinicalPillBar`) ngay bên dưới (Y=39), đảm bảo tuân thủ nguyên tắc Gestalt (phạm vi lọc đứng trước dữ liệu phụ thuộc).
  - Đồng bộ khung giờ ca trực lâm sàng bệnh viện `06:00 - 06:00` (sáng nay đến sáng mai): triển khai `ResetAuditTimeRange()` tự động tính toán mốc 06:00 và cập nhật đồng thời cả Tab 2 (`dtpAuditFrom`, `dtpAuditTo`) và Tab 3 (`dtpClinicalFrom`, `dtpClinicalTo`).
  - Gắn sự kiện `[⚡ Ca hiện tại]` trên cả Tab 2 (`btnOrderQuickShift`) và Tab 3 (`btnAuditQuickShift`) vào `ResetAuditTimeRange()`, chuẩn hóa giao diện cyan thanh lịch (`BackColor = #E0F2FE`, `ForeColor = #0369A1`).
  - Cân đối tỷ lệ cột bộ bài KPI trên Tab 2 (`pnlOrderKpiDeck`) thành 65% (Chưa thực hiện y lệnh) và 35% (Xung đột kíp PTTT), khớp thẳng hàng với đường phân cách bảng dữ liệu bên dưới.
  - Chuẩn hóa văn phong microcopy bộ lọc buồng bệnh sang `"Tất cả buồng bệnh"` trên cả Tab 2 (`MultiSelectRoomFilterControl`) và Tab 3 (`cboAuditChamber`).
  - Chuẩn hóa nhãn thời gian thống nhất `"Thời gian:" ... "đến"` trên cả Tab 2 và Tab 3.
  - Ẩn nút `[Làm mới]` thừa thãi bên cạnh nút `[Tra cứu]` trên thanh công cụ Tab 1 (`btnRefresh`) và Tab 2 (`btnOrderRefresh`).
  - Chuyển đổi nút thao tác phụ trên Tab 1 (`btnCopyClipboard` / `btnCopyZalo` và `btnExportExcel`) sang phong cách viền outline phẳng tinh tế (`FlatStyle = Flat`, `BorderSize = 1`, `BorderColor = #CBD5E1`, `BackColor = White`, `ForeColor = #334155`).
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/Test_Phase03_LayoutRestructuringAndFilterStandardization.cs`.

- **Phase 02: False Reassurance Remediation (`src/UI/Controls/UnsignedDocumentControl.Designer.cs`, `src/UI/Controls/UnsignedDocumentControl.cs`, `tests/Test_Phase02_FalseReassuranceRemediation.cs`)**:
  - Loại bỏ hoàn toàn trạng thái an toàn giả lập ("False Reassurance" - số 0 và dấu tích xanh premature) trên Tab 2 (`Quản lý y lệnh`) và Tab 3 (`Kiểm toán & Cảnh báo lâm sàng`) trước khi người dùng thực hiện tra cứu.
  - Khởi tạo giá trị trung tính `"-"` với màu Slate 500 (`Color.FromArgb(100, 116, 139)`) cho các thẻ KPI (`lblOrderCard1Value`, `lblOrderCard3Value`).
  - Chuyển đổi trạng thái ban đầu của nhóm xung đột kíp mổ sang `● Xung đột kíp PTTT: Chưa kiểm tra` kèm thông điệp hướng dẫn rõ ràng `🔍 Chưa thực hiện tra cứu. Nhấn [Tra cứu] để kiểm tra y lệnh và kíp mổ.`.
  - Khởi tạo 4 pill buttons kiểm toán lâm sàng với nhãn trung tính `(-)` và màu Slate 600 (`Color.FromArgb(71, 85, 105)`): `⚠️ Y lệnh thiếu giờ (-)`, `🛡️ Stiprol BHYT (-)`, `⚠️ Thủ thuật / PT lỗi (-)`, `🛏️ Chưa gõ ngày giường (-)`.
  - Thay thế thông báo bảng trống ban đầu bằng lời nhắc trung thực: `🔍 Chưa thực hiện tra cứu. Vui lòng chọn buồng bệnh và nhấn [Tra cứu] (Enter/F5) để kiểm tra.`.
  - Triển khai máy trạng thái chặt chẽ với cờ `_orderAuditQueried` và `_clinicalAuditQueried`, các phương thức hoàn nguyên `ResetOrderAuditUiState()` và `ResetClinicalAuditUiState()`, chỉ hiển thị huy hiệu `0 - An toàn` và `✓` khi máy chủ phản hồi xác nhận 0 vi phạm.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/Test_Phase02_FalseReassuranceRemediation.cs`.

- **Phase 01: Information Architecture & Mnemonic Fix (`src/UI/MainForm.Designer.cs`, `src/UI/MainForm.cs`, `src/UI/Controls/UnsignedDocumentControl.cs`, `src/UI/Controls/UnsignedDocumentControl.Designer.cs`)**:
  - Chuẩn hóa tên tab điều hướng master từ `✍️ TÀI LIỆU CHƯA KÝ` thành `🛡️ GIÁM SÁT & KIỂM TOÁN` bao quát trọn vẹn cả 3 module (Hồ sơ chưa ký, Quản lý y lệnh, Kiểm toán lâm sàng).
  - Triển khai pattern decoupled event `SubTabChanged` và class `SubTabChangedEventArgs` trong `UnsignedDocumentControl`, đồng bộ lập tức tiêu đề `lblActiveFilter` và thanh trạng thái `lblStatusFilter`, `lblStatusTotal` khi chuyển đổi tab con.
  - Vô hiệu hóa phân tích phím tắt mnemonic (`UseMnemonic = false`) trên `btnSubTabClinicalAudit` và `lblOrderCard1Sub`, mở rộng chiều rộng nút lên 275px ngăn chặn hoàn toàn lỗi cắt cụt chữ (`...`) và dấu gạch chân `_C` sai lệch.
  - Chuẩn hóa phụ đề thẻ y lệnh thành `Thuốc / Dịch vụ chưa bấm TH/KTH`, khắc phục hiện tượng mất dấu ampersand `&`.
  - Nghiệm thu thành công 100% assertions qua test đơn nhất toàn diện `tests/Test_Phase01_InformationArchitectureAndMnemonicFix.cs`.

### 🏥 Giới hạn Phạm vi Khoa cho Bác sĩ Chỉ định (Ordering Physician Department Scope Boundary Audit)
- **Phase 01: Database Query Department Scope Filter (`src/Server/GoServer/internal/repository/audit.go`, `audit_test.go`, `dept_scope_test.go`)**:
  - Bổ sung ràng buộc `JOIN tb_nhanvien nv_doc ON sd.userid = nv_doc.nhanvienid` và `AND (nv_doc.khoachucnangid = @deptId)` vào CTE `raw_orders` trong `orderAuditSql`.
  - Loại bỏ hoàn toàn 19 y lệnh ngoại khoa/chẩn đoán hình ảnh từ các bác sĩ khoa khác (Gây mê hồi sức Dept 18: BS Nguyễn Sỹ Toản, BS Đàm Văn Du, BS Vũ Thị Phượng; Chẩn đoán hình ảnh Dept 19: BS Nguyễn Thị Viết) gây báo động giả tại Khoa Ngoại Nhi.
  - Số lượng vi phạm Rule 4 tại Khoa Ngoại Nhi (01/11 - 21/11/2026) giảm chính xác từ 43 xuống còn đúng 24 vi phạm thực tế.
  - Bảo toàn 100% y lệnh của các bác sĩ thuộc Khoa Ngoại Nhi (BS Đặng Thế Quỳnh, BS Vũ Đăng Tuyến, BS Nguyễn Duy Hòa).
  - Đồng bộ mã nguồn trực tiếp sang thư mục triển khai `D:\skul9x\Report\EHC\src\Server\GoServer\internal\repository\audit.go`.
  - Nghiệm thu thành công 100% qua test toàn diện `dept_scope_test.go` và `audit_test.go`.
- **Phase 02: Binary Compilation and Packaging (`publish/Server/ehc-server.exe`, `publish_server/ehc-server.exe`, `build_go_server.ps1`, `tests/verify_binary_build.ps1`)**:
  - Biên dịch stripped & trimmed Windows x64 daemon binary `ehc-server.exe` (13,185,536 bytes / 12.57 MB <= 18 MB threshold) với cờ tối ưu hóa `-tags=nomsgpack -ldflags "-s -w" -trimpath`.
  - Bổ sung CLI handlers sớm cho `-v`, `--version`, `-h`, `--help` tại `main.go` và cách ly error stream trong `config.go` (`fs.SetOutput(io.Discard)`) ngăn chặn crash và socket collision ngoài ý muốn.
  - Tự động đồng bộ artifact phân phối hoàn chỉnh vào `publish\Server\ehc-server.exe` và `publish_server\ehc-server.exe`.
  - Hoàn thành kiểm thử đơn nhất toàn diện `tests/verify_binary_build.ps1` kiểm chứng tính hợp lệ, độ tươi mới, dung lượng và phản hồi CLI flags đạt 100% PASS.
- **Phase 03: Service Lifecycle Deployment & E2E Verification (`update_server_service.bat`, `tests/verify_e2e_audit.go`)**:
  - Tự động hóa nâng quyền UAC thông minh và quản lý vòng đời Windows Service `EhcReportApiService` (chờ `STOPPED` triệt để để chống xung đột khóa tệp ERROR_SHARING_VIOLATION trước khi sao chép và chờ `RUNNING` sau khi khởi động).
  - Triển khai binary tối ưu hóa vào môi trường chạy thực tế `D:\skul9x\Report\EHC\publish\Server\ehc-server.exe` và `publish_server\ehc-server.exe`.
  - Nghiệm thu kiểm thử End-to-End toàn diện qua HTTP API endpoint `http://localhost:5000/api/clinical/order-audit` với xác thực `X-Hospital-ApiKey` tại `tests/verify_e2e_audit.go`:
    * Thời gian phản hồi API đạt 7.68ms (vượt xa chỉ tiêu NFR-02 < 1.5s).
    * Tổng số y lệnh thiếu giờ tại Khoa Ngoại Nhi giảm chính xác từ 43 xuống đúng 24 vi phạm (loại bỏ sạch sẽ 19 y lệnh ngoài khoa từ BS Toản, BS Du, BS Phượng, BS Viết).
    * Bảo toàn 100% y lệnh của các bác sĩ thuộc Khoa Ngoại Nhi (BS Quỳnh, BS Tuyến, BS Hòa).
    * Bệnh nhân Nguyễn Quang Tú (MR1267151) với lỗi thiếu giờ Paracetamol truyền tĩnh mạch được phát hiện chính xác là vi phạm thực tế.
- **Remediation: Rectal Medication Timing Exemption Narrowing (`internal/repository/audit.go`, `audit_test.go`, `dept_scope_test.go`, `tests/verify_e2e_audit.go`)**:
  - Thu hẹp bộ lọc miễn trừ thủ thuật lâm sàng: chuyển `strings.Contains(lower, "thụt")` thành `strings.Contains(lower, "thụt tháo")`.
  - Giữ nguyên miễn trừ thủ thuật thụt tháo phân (Natri Clorid thụt tháo), nhưng bắt buộc kiểm toán giờ dùng thuốc đối với y lệnh thuốc bơm/thụt điều trị (như Stiprol thụt hậu môn trực tràng).
  - Khắc phục triệt để lỗi bỏ sót vi phạm thiếu giờ đối với BN Nguyễn Anh Thư (mã 26033864 - Stiprol ngày 19/11/2026).
  - Biên dịch lại Go server daemon `ehc-server.exe` (SHA256: 6E5759C8...), tái khởi động Windows Service `EhcReportApiService` và đóng gói lại bộ cài `Setup_BaocaoGiaoBan_Server_v2.5.0.exe`.

### 🛡️ Kiểm toán Giờ Dùng Thuốc Lâm sàng (Rule 4: Medication Administration Timing Engine)
- **Phase 01: Backend Timing Validation Engine & Order Audit Rule 4 (`internal/model/audit.go`, `internal/repository/audit.go`)**:
  - Bổ sung struct `MissingMedicationTimeItem` và mở rộng `OrderAuditResponse` với `TotalMissingMedicationTime`, `MissingMedicationTimeOrders`.
  - Triển khai engine kiểm tra giờ dùng thuốc tuyến tính $O(N)$ bằng `reTimingMarkers` (precompiled regex) kết hợp bộ lọc nhanh zero-allocation `strings.ContainsAny(lower, "h:")`.
  - Thiết lập cơ chế miễn trừ thông minh cho dung môi/nước cất (`pha tiêm axuka`, `pha tiêm amikacin`, `hòa tan`, `nước cất`), thuốc cấp cứu/SOS (`khi sốt`, `khi đau`, `sos`, `cấp cứu`) và thuốc dùng ngoài (`bôi ngoài da`, `nhỏ mắt`, `nhỏ mũi`, `súc miệng`).
  - Mở rộng SQL `orderAuditSql` truy vấn `thuoc_huongdansudung`, `thuoc_duongdung`, `thuoc_lieudung_4210`, `thuoc_dm_medicinedungmoiid`, và liên kết `LEFT JOIN tb_nhanvien` lấy họ tên bác sĩ chỉ định.
  - Kiểm thử toàn diện 15/15 PASS (`tests/test_phase01_medication_timing_audit_engine.go`): thông lượng đạt 869,663 ops/sec (> 50,000 SLA), độ trễ truy vấn toàn khoa 126.9ms (< 250ms SLA), phát hiện chính xác lỗi thiếu giờ Paracetamol (ID 62348341) trên BN Nguyễn Quang Tú (MR1267151), phê duyệt hợp lệ Axuka, Domuvar và Nước cất tiêm.
- **Phase 02: C# Client DTOs, SubTabEnum & API Client Layer (`src/Models/OrderAuditModels.cs`, `src/Server/BaocaoGiaoBan.Server/Models/OrderAuditModels.cs`, `src/Services/ApiClientService.cs`, `src/UI/Controls/UnsignedDocumentControl.cs`)**:
  - Triển khai `MissingMedicationTimeItemDto` với thuộc tính `[Obfuscation(Exclude = true, ApplyToMembers = true)]` và khởi tạo mặc định chuỗi rỗng kèm nhãn cảnh báo `[🔴 Thiếu giờ dùng thuốc]`.
  - Mở rộng `OrderAuditResponse` trên cả Client .NET 4.8 và Server .NET 8 với `TotalMissingMedicationTime`, danh sách `MissingMedicationTimeOrders`, và cập nhật công thức tính `TotalViolations = TotalUnexecuted + TotalSurgeryConflicts + TotalMissingMedicationTime`.
  - Mở rộng `SubTabEnum.ClinicalAudit = 2` trong `UnsignedDocumentControl.cs` chuẩn bị cho tab Trung tâm Kiểm toán Lâm sàng tập trung.
  - Tăng cường phòng thủ tại `ApiClientService.GetOrderExecutionAuditAsync`: tự động gán danh sách rỗng nếu phản hồi trả về null, chống NullReferenceException khi tương thích ngược.
  - Nghiệm thu đơn nhất toàn diện 91/91 assertions PASS (100%) tại `tests/Test_Phase02_MedicationAuditDtoAndApiClient.cs`, biên dịch 0 warnings, 0 errors trên cả Client csc.exe và Server dotnet build.
- **Phase 03: WinForms UI Restructuring & Consolidated Clinical Audit Center (`src/UI/Controls/UnsignedDocumentControl.cs`, `src/UI/Controls/UnsignedDocumentControl.Designer.cs`)**:
  - Mở rộng thanh điều hướng `pnlHeaderTabBar` với sub-tab thứ 3 `btnSubTabClinicalAudit` ("🛡️ Kiểm toán & Cảnh báo lâm sàng") bình đẳng với Hồ sơ chưa ký và Quản lý y lệnh.
  - Loại bỏ hoàn toàn cảnh báo chật hẹp `pnlClinicalAlertDeck` khỏi `pnlUnsignedDocsContainer`, trả lại 100% không gian chiều dọc cho lưới ký số hồ sơ bệnh án.
  - Xây dựng Trung tâm Kiểm toán Lâm sàng tập trung `pnlClinicalAuditContainer` hiện đại:
    * Thanh segmented pill bar (`flpClinicalPillBar`) gồm 4 pills: `⏱️ Y lệnh thiếu giờ`, `🛡️ Stiprol BHYT`, `⚠️ Thủ thuật / PT lỗi`, `🛏️ Chưa gõ ngày giường` với bộ đếm lỗi động (Xanh `#166534` khi 0, Đỏ đậm `#B91C1C` trên nền `#FEE2E2` khi > 0).
    * Thanh công cụ lọc `flpClinicalAuditToolbar`: tích hợp chọn buồng bệnh (`cboAuditChamber`), chọn khoảng ngày, nút `[🔄 Tra cứu]`, `[📋 Sao chép tất cả]` và `[📊 Xuất Excel]`.
    * Lưới dữ liệu `DoubleBufferedDataGridView` 10 cột, tự động áp dụng màu nền phân biệt theo loại vi phạm (`#FFF1F2` thiếu giờ, `#FFFBEB` Stiprol BHYT, `#FFE4E6` thủ thuật, `#F1F5F9` ngày giường).
    * Hỗ trợ nhấp đúp sao chép mã bệnh nhân kèm thông báo toast động (`lblClinicalToast`) và sao chép báo cáo giao ban lâm sàng chuẩn Bộ Y tế / Quyết định 2433/QĐ-BYT.
  - Nghiệm thu đơn nhất toàn diện 128/128 assertions PASS (100%) tại `tests/Test_Phase03_ClinicalAuditUiNavigationAndLayout.cs`, toàn bộ pipeline biên dịch client (ConfuserEx + LARGEADDRESSAWARE + Binary Security Audit) hoàn tất thành công 0 lỗi.
- **Phase 04: Full Integration, Clinical Parity & Performance SLA Verification (`tests/test_phase04_medication_timing_and_clinical_audit_e2e.go`, `src/Server/GoServer/cmd/server/main.go`, `src/Server/GoServer/ehc-server.exe`)**:
  - Bổ sung định tuyến `r.GET("/api/clinical/order-audit", orderHandler.GetOrderAudit)` đồng bộ hoàn toàn với route alias `/api/orders/audit` và `/api/clinical-reports/pediatric/order-audit`.
  - Biên dịch daemon `ehc-server.exe` đạt kích thước 12.56 MB (vượt chỉ tiêu $\le 18\text{ MB}$ SLA), đồng bộ vào `publish/Server` và `publish_server`.
  - Nghiệm thu tính chính xác lâm sàng (Clinical Parity):
    * BN Nguyễn Quang Tú (MR1267151): Phát hiện chính xác y lệnh Paracetamol truyền tĩnh mạch thiếu giờ thực hiện với nhãn cảnh báo `[🔴 Thiếu giờ dùng thuốc]`.
    * BN Ngô Văn Hùng (MR1277729): Phê duyệt 28 y lệnh uống đa liều (Bactirid, Domuvar, Duphalac) với 0 vi phạm.
    * BN Ngô Phương Nhi (MR1273175): Phê duyệt Axuka tiêm tĩnh mạch có mốc giờ và miễn trừ hoàn toàn dung môi Nước cất tiêm (`pha tiêm axuka`) không gây báo động giả (0 false alarms).
  - Nghiệm thu hiệu năng & khả năng chịu tải (Performance SLA):
    * Truy vấn kiểm toán toàn khoa 24 đạt 56.1ms khi Cold run (< 200ms SLA) và 0s (< 5ms SLA) khi Cache HIT.
    * Stress test chịu tải 20 concurrent workers qua 50 iterations: 0 lỗi HTTP 5xx, 0 rớt kết nối, P50 = 6.38ms, P90 = 59.74ms (< 100ms SLA), P99 = 60.27ms (< 200ms SLA).
    * Khẳng định triệt tiêu rò rỉ goroutine: Delta = +0 sau khi hoàn tất stress test.
  - Vượt qua toàn bộ 33/33 assertions PASS (100%) tại `tests/test_phase04_medication_timing_and_clinical_audit_e2e.go`.

## [2.4.3] - 2026-11-13 (Khắc phục Triệt để Hiệu năng Golang Backend & Nghiệm thu Toàn diện Parity - Phases 01-04)

### 🚀 Tối ưu hóa Toàn diện Backend Golang (Comprehensive Performance Remediation & Plan Caching)
- **Phase 01: Zero-Allocation Timestamp Serialization & Fast-Path Parsing (`internal/model/report.go`, `models.go`, `repository/report.go`)**:
  - Chuyển đổi toàn bộ `PlainTime.MarshalJSON()` và `CustomTime.MarshalJSON()` sang `AppendFormat`, triệt tiêu hoàn toàn reflection và boxing `interface{}`, giảm 66.67% allocations cho các DTO thời gian.
  - Bổ sung Fast-path cho `CleanTransferDiagnosis`: kiểm tra nhanh ký tự số trước khi chạy NFA regex engine, tăng tốc độ xử lý CPU lên 1.5x.
- **Phase 02: HTTP Transport Hardening & Singleflight Context Decoupling (`cmd/server/main.go`, `internal/middleware/cache.go`)**:
  - Sử dụng `context.WithoutCancel` (Go 1.21+) bên trong Singleflight middleware, triệt tiêu 100% nguy cơ rớt chùm request (Cascading Dropouts) khi request leader bị client hủy hoặc ngắt kết nối mạng.
  - Bổ sung cấu hình socket timeouts cho `http.Server`: `ReadTimeout: 30s`, `WriteTimeout: 60s`, `IdleTimeout: 120s`, `ReadHeaderTimeout: 10s`, ngăn chặn vĩnh viễn rò rỉ socket descriptor và treo goroutine trên Windows Service.
- **Phase 03: Tối ưu Hóa Truy vấn SQL & Plan Caching (`internal/repository/audit.go`, `document.go`)**:
  - Loại bỏ 4 mệnh đề `OR` ngày tháng trong `orderAuditSql`, chỉ giữ lại `COALESCE(...) BETWEEN @dtFrom AND @dtTo`: giảm độ trễ kiểm toán y lệnh toàn khoa từ 257ms - 473ms xuống **173.4ms** (vượt chỉ tiêu SLA < 250ms).
  - Triệt tiêu truy vấn con tương quan N+1 trong tất cả 10 CTE của `GetUnsignedDocuments`, thay thế bằng `active_patient_signs AS MATERIALIZED` Hash Join: giảm thời gian tra cứu tài liệu chưa ký toàn viện từ 480ms xuống **174.1ms** (vượt chỉ tiêu SLA < 250ms).
  - Cố định câu lệnh SQL bằng index range bounds SARGable `AND d.ngaytao BETWEEN COALESCE(@fromDate, '1900-01-01'::timestamp) AND COALESCE(@toDate, '2099-12-31'::timestamp)`: bảo toàn 100% PostgreSQL Prepared Execution Plan Caching (`plan_cache_mode = auto`).
- **Phase 04: Full Integration, Benchmark Verification & Parity Testing (`tests/test_fix_phase04_end_to_end_benchmark_parity.go`)**:
  - Tạo master test suite nghiệm thu toàn diện 65/65 assertions đạt 100% PASS trong 8.03s (< 20s SLA).
  - Stress-test chịu tải cao với 20 concurrent workers qua 100 iterations: 0 lỗi HTTP 5xx, 0 rớt kết nối, P50 = 2.37ms, P90 = 6.27ms, P99 = 8.24ms.
  - Khẳng định 0 goroutine rò rỉ (Delta: +0) và thời gian GC pause tối đa chỉ 593.5µs.
  - Bảo toàn 100% logic lâm sàng (Phương trình cân bằng buồng bệnh $TồnĐầu + Nhập = Xuất + TồnCuối$, phân loại mổ cấp cứu/mổ phiên/thủ thuật, và kiểm toán y lệnh/tài liệu).

## [2.4.2] - 2026-11-08 (Chuyển đổi Giờ Giao ban Mặc định sang 06:00 Sáng & Nghiệm thu Parity Kiểm toán Lâm sàng - Phases 01-04)

### ⏱️ Chuyển đổi Khung Giờ Ca Trực Lâm Sàng sang 06:00 AM (Default Shift Handover Time)
- **Phase 01: Core Domain Models & Hằng số Tập trung (`src/Models/ShiftCycleInfo.cs`, `cycle.go`)**:
  - Định nghĩa hằng số công khai `DefaultShiftStartHour = 6`, `DefaultShiftStartMinute = 0` trên cả client C# (.NET 4.8) và Go backend.
  - Chuẩn hóa các factory methods (`CreateToday`, `CreateYesterday`, `CreateWeekend`, `CreateDefaultClinicalCycle`) sang mốc 06:00:00.
  - Cập nhật logic phân định ca sáng theo hướng dẫn Bộ Y Tế: trước 06:00 thuộc ca hôm trước, từ 06:00 trở đi thuộc ca mới.
- **Phase 02: Đồng bộ API Backend GoServer & Phân giải Tham số (`src/Server/GoServer`)**:
  - Tự động phân giải các yêu cầu API không truyền `from`/`to` về mốc 06:00:00.
  - Metadata ca trực phản hồi từ GoServer luôn chứa `"shiftStartHour": 6, "shiftStartMinute": 0`.
- **Phase 03: Giao diện Người dùng WinForms & Hộp thoại Chọn Ca Trực (`src/UI/Forms/FormSelectCycle.cs`, `ReportGeneratorControl.cs`)**:
  - Hộp thoại chọn ca trực khởi tạo mặc định tại 06:00, nút reset hiển thị `"↺ Mặc định (06:00)"`.
  - Cập nhật nhãn động: `"(Từ 06:00 hôm nay đến 06:00 ngày mai)"`, `"(Từ 06:00 hôm qua đến 06:00 hôm nay)"`.
  - Giữ nguyên khả năng cấu hình tùy biến khi bác sĩ nhập giờ lẻ (07:00, 08:30,...).
- **Phase 04: Nghiệm thu Ranh giới Ca trực & Bảo toàn Chỉ số Kiểm toán Lâm sàng (`tests/test_phase04_shift_boundary_census_parity.cs`)**:
  - Kiểm thử ranh giới nửa mở `[06:00:00, 06:00:00)`: sự kiện tại `05:59:59` thuộc ca trước, sự kiện tại `06:00:00` và `06:15:00` thuộc ca mới đạt độ chính xác 100%.
  - Định luật bảo toàn số lượng bệnh nhân kiểm toán (Census Conservation Invariant): $\text{Hiện có} = \text{Cũ} + \text{Vào viện} + \text{Chuyển đến} - \text{Ra viện} - \text{Chuyển đi} - \text{Tử vong}$ được thỏa mãn tuyệt đối với sai số bằng 0.
  - Đồng bộ và vượt qua toàn bộ self-test trong `src/Program.cs` và các bộ kiểm thử phụ thuộc.

## [2.4.1] - 2026-11-07 (Khắc phục triệt để Hiệu năng Cao & Trung bình - Phases 01-05: VirtualMode Client, Tối ưu Truy vấn & Đồng bộ Bộ nhớ)

### 🚀 Tối ưu hóa Hiệu năng Toàn diện (High & Medium Performance Remediation)
- **Phase 01: Điều chỉnh Connection Pool & Bộ đệm Kế hoạch Thực thi (`src/Server/GoServer/internal/db/db.go`)**:
  - Tinh chỉnh `pgxpool.Config`: `MaxConns = 16`, `MinConns = 4`, `MaxConnLifetime = 30m`, `MaxConnIdleTime = 5m`.
  - Thiết lập `plan_cache_mode = describe_exec` (statement description caching) và `statement_cache_capacity = 256` qua PostgreSQL driver nhị phân, loại bỏ chi phí biên dịch lại query plans.
- **Phase 02: Đồng thời Bộ nhớ Cache & Buffer Pooling (`src/Server/GoServer/internal/middleware/cache.go`, `memory.go`)**:
  - Triển khai `sync.Pool` tái sử dụng `bytes.Buffer` cho HTTP Response Buffering, triệt tiêu cấp phát bộ nhớ rác (GC pressure).
  - Tích hợp bảo vệ Memory Bloat: chỉ trả buffer vào pool nếu dung lượng $\le$ 256 KB.
  - Phân vùng khóa cache và đồng bộ hóa qua `singleflight.Group` chống hiện tượng Cache Stampede.
- **Phase 03: Hợp nhất Truy vấn Gói Ca trực Unified Shift Bundle (`src/Server/GoServer/internal/repository/report.go`)**:
  - Hợp nhất các truy vấn bệnh nhân thành một đợt đọc cơ sở dữ liệu duy nhất kèm phân loại trong bộ nhớ (in-memory classification).
  - Cắt giảm >65% thời gian xử lý và giảm 80% số lần round-trip kết nối cơ sở dữ liệu.
- **Phase 04: Tối ưu Truy vấn Kiểm toán Y lệnh & Tài liệu (`src/Server/GoServer/internal/repository/audit.go`, `document.go`)**:
  - Bổ sung ràng buộc ngày tháng SARGable (`servicedatadate` bounding box) cho các truy vấn kiểm toán y lệnh thuốc, thủ thuật và giường bệnh.
  - Loại bỏ hoàn toàn reflection và interface boxing trong hàm giải mã JSON y lệnh chia liều (`ylenh_tachjson`), tối ưu hóa tốc độ parsing gấp 5 lần.
  - Tối ưu hóa biểu thức so khớp nhân viên - tài liệu chưa ký (`EvaluateStaffPredicate`).
- **Phase 05: Client-Side VirtualMode & Chuẩn hóa Bất đồng bộ (`src/UI/Controls/UnsignedDocumentControl.cs`, `ReportGeneratorControl.cs`)**:
  - Kích hoạt chế độ ảo hóa `VirtualMode = true` cho `DataGridView` tài liệu chưa ký (`dgvDocuments`), tách rời hoàn toàn số lượng bản ghi khỏi việc tạo đối tượng `DataGridViewRow` trên heap x86.
  - Cung cấp dữ liệu theo yêu cầu qua sự kiện `CellValueNeeded`, định dạng màu sắc/font qua `CellFormatting`, và tooltip qua `CellToolTipTextNeeded`.
  - Áp dụng kỹ thuật `Rows.Clear()` trước khi gán `RowCount` khi số dòng giảm, triệt tiêu hoàn toàn độ trễ Unshare Rows của WinForms.
  - Hiệu năng vượt trội: Nạp 3,000 hồ sơ chưa ký chỉ mất **2 ms** (tiêu chuẩn < 5ms); tìm kiếm/lọc văn bản chỉ mất **2 ms** (tiêu chuẩn < 10ms); toggle checkbox trách nhiệm trực tiếp chỉ mất **1-2 ms** (tiêu chuẩn < 10ms).
  - Đánh dấu `[Obsolete]` các hàm đồng bộ cũ (`FetchReportData`, `ExportBothReports`) trong `ReportGeneratorControl.cs`, bảo đảm 100% UI event handlers chạy bất đồng bộ không gây đóng băng giao diện.
  - Kiểm thử độc lập qua `tests/test_phase05_client_virtual_mode_and_async_hygiene.cs`: Vượt qua toàn bộ **114/114 assertions (100% PASS)**.

## [2.4.0] - 2026-11-07 (Chuyển đổi toàn diện Backend API sang Golang, Cắt giảm >78% RAM & Cutover Windows Service Cổng 5000)

### ⚡ Chuyển đổi Backend C# (.NET 8) sang Golang (Phases 01 - 06)
- **Kiến trúc Golang Hiệu năng cao & Tinh giản tài nguyên (`src/Server/GoServer`)**:
  - Viết lại toàn bộ 16 API endpoints bằng Go 1.22+ kết hợp `gin-gonic/gin` và PostgreSQL driver nhị phân `jackc/pgx/v5` (`pgxpool`).
  - Thiết lập kết nối tối ưu với tham số runtime `plan_cache_mode=force_custom_plan` và `jit=off` tương thích hoàn toàn hành vi của Npgsql.
  - Tích hợp Middleware xác thực API Key thời gian hằng số `X-Hospital-ApiKey` (`EHC-SANNHI-BACNINH-SECURE-KEY-2026`).
  - Tích hợp HTTP Output Caching (60s TTL) với cơ chế đồng bộ hóa `golang.org/x/sync/singleflight` chống bão truy vấn (Cache Stampede) và nén phản hồi Gzip.
- **Đối chiếu ngữ nghĩa JSON 1:1 qua AST Comparator & Kiểm thử Đa ca trực (`Test_MultiShift_ParityVerification.ps1`)**:
  - Xây dựng công cụ so sánh AST JSON tự động bằng thư viện `google/go-cmp` hỗ trợ chuẩn hóa timestamp, lọc nhiễu jitter, sắp xếp tập hợp mảng và bullet lines.
  - Nghiệm thu 100% khớp dữ liệu qua 16 endpoints lâm sàng: Báo cáo giao ban, thống kê nội trú snapshot, ca mổ/thủ thuật, hồ sơ chưa ký 3 cấp độ, kiểm toán y lệnh thuốc đa lô, vi phạm <10 phút, xung đột lịch mổ, và các gói bundle composite.
  - Kiểm thử đối chiếu trực tiếp dữ liệu 7 ca trực liên tiếp từ 27/10 đến 02/11/2026 và 5 ca trực từ 01/11 đến 05/11/2026 giữa Go (5000) và C# (7214): Tất cả 42/42 và 30/30 endpoints cùng chỉ số lâm sàng (Hiện có, Cũ, Phẫu thuật, Y lệnh, Chưa ký, Lỗi thủ thuật, Thiếu giường) đạt tỷ lệ khớp tuyệt đối **100% (0% diff)**.
  - Chuẩn hóa ánh xạ chuyển khoa `chuyenDen`, đồng bộ trường `MaBenhVienChuyenDi` giữa `cycle-data` và `shift-bundle`, và tích hợp tie-breaker `ORDER BY pttt.ngaypttt DESC, pttt.servicedata_ptttid ASC` loại trừ tính bất định khi các thủ thuật trùng giây timestamp.
- **Tích hợp Windows Service Daemon (`kardianos/service`) & Triệt tiêu SCM Error 1053**:
  - Hỗ trợ chạy kép linh hoạt: Chế độ Console tương tác (phục vụ debug/phát triển) và chế độ Windows Service SCM Daemon (`EhcReportApiService`).
  - Triệt tiêu hoàn toàn lỗi Windows SCM Error 1053: hàm `Start()` trả về tức thì và khởi tạo HTTP listener trong background goroutine.
  - Phân giải đường dẫn cấu hình qua `os.Executable()`, xử lý triệt để bẫy thư mục làm việc `C:\Windows\System32` khi chạy dưới tài khoản LocalSystem.
  - Cơ chế Graceful Shutdown 15 giây: rút cạn an toàn các yêu cầu HTTP đang xử lý dở và đóng kết nối `pgxpool` sạch sẽ.
  - Tự động cấu hình hành động phục hồi sự cố SCM (Failure Recovery Actions: restart sau 5s, 10s, 30s) đảm bảo dịch vụ tự chữa lành.
- **Đóng gói Nhị phân Đơn nhất & Cắt giảm đột phá Bộ nhớ (Benchmark Verification)**:
  - Tự động hóa biên dịch nhị phân tĩnh đơn nhất qua `build_go_server.ps1` với các cờ `-tags=nomsgpack -ldflags="-s -w" -trimpath`, đưa kích thước file `ehc-server.exe` về **12.52 MB** (thấp hơn nhiều so với giới hạn 18 MB và nhẹ hơn 61% so với C# 32.3 MB).
  - Kết quả kiểm thử tải 200 truy vấn đồng thời: Bộ nhớ RAM đỉnh của Go chỉ tiêu tốn **25.87 MB RSS** (giảm >78% so với C# ~120 MB - 142 MB).
- **Tráo cổng Production (Cutover) & Bộ công cụ Rollback tức thì**:
  - Tráo cổng chính thức: Go server tiếp quản cổng **5000** làm backend phục vụ cho toàn bộ Client WinForms tại các khoa phòng.
  - Xây dựng công cụ phục hồi khẩn cấp `tools/rollback_to_csharp.ps1`: tự động tráo ngược `binPath` của SCM về `publish_server/BaocaoGiaoBan.Server.exe` chỉ trong **9.64 giây** (< 30s target) với zero rủi ro vận hành.
  - Toàn bộ 6/6 bước kiểm thử trong `tests/Test_Phase06_WindowsServiceAndBenchmarkCutover.ps1` vượt qua thành công 100%.

## [2.3.8] - 2026-10-25 (Đồng bộ phân loại trạng thái ký số EHC 3 cấp độ, Miễn kiểm toán Plasma & Phân loại báo cáo 2 tầng)

### 🏥 Kiểm toán tài liệu & Quy trình ký số EMR (EHC Document Status Alignment)
- **Đồng bộ hóa 3 cấp độ trạng thái vòng đời tài liệu EHC (`Đang ký`, `Chưa tạo`, `Đã ký`)**:
  - Khắc phục triệt để lỗi tài liệu cận lâm sàng (CĐHA/X-quang, Xét nghiệm) bị huỷ ký hoặc cuộn ngược trạng thái trong EHC bị biến mất khỏi báo cáo chưa ký do tồn tại bản ghi mồ côi trong `tb_mediboxdocument_sign` (điển hình: X-quang bụng `20789933` BN Dương Quỳnh Mai).
  - Bổ sung xác thực kiểm tra chữ ký điện tử thực tế qua bảng băm `tb_mediboxdocument_sign_hash` (`NOT EXISTS (SELECT 1 FROM tb_mediboxdocument_sign s JOIN tb_mediboxdocument_sign_hash sh ...)`).
  - Bổ sung các trường DTO: `EhcStatusText`, `EhcStatusColor`, `SoChuKyDaKy`, `SoChuKyInit`, `SignProgress` (ví dụ: `"1/2"`), `StatusBadgeDisplay` (`"Đang ký (1/2)"`).
  - Tích hợp vẽ Badge trạng thái trực quan trong DataGridView (`UnsignedDocumentControl.cs`) với mã màu cam EHC chuẩn (`#F59E0B`), nền pastel và bo góc mượt mà, bộ đệm GDI+ static chống rò rỉ bộ nhớ.
  - Hỗ trợ cơ chế phân loại báo cáo 2 tầng (Tier 1: Danh mục loại tài liệu y tế; Tier 2: Trạng thái ký số EHC) và tìm kiếm/lọc nhanh theo trạng thái.

### 💉 Miễn kiểm toán thủ thuật PTTT cho Chiếu tia plasma lạnh điều trị (`dm_servicedatastatusid = 3`)
- Miễn trừ điều kiện bắt buộc phải có phiếu phẫu thuật/thủ thuật (`tb_servicedata_pttt`) đối với các dịch vụ chiếu tia plasma lạnh điều trị vết thương (`sd.servicename ILIKE '%chiếu tia plasma lạnh điều trị%'`, bao gồm các mã `DVND20250481`, `DVND20250482`, `DVND20250483`) khi trạng thái y lệnh đã hoàn thành / trả kết quả (`dm_servicedatastatusid = 3`).
- Áp dụng đồng bộ cả ở tầng backend SQL query (nhánh Defect Type 2) và tầng memory classification loop trong `ClinicalReportDataService.cs`.
- Giữ nguyên kiểm toán nghiêm ngặt cho tất cả các dịch vụ phẫu thuật / thủ thuật plasma khác (điều trị vết thương mạn tính bằng plasma, cắt amidan/nạo VA bằng dao plasma, v.v.) và các y lệnh plasma chưa hoàn thành.
- Tối ưu hóa SARGability, thời gian thực thi câu truy vấn SQL kiểm toán đạt < 150ms trên cơ sở dữ liệu PostgreSQL `bvsnbacninh`.
- Đóng gói chính thức bộ cài đặt production v2.3.8 tại `Output/Setup_BaocaoGiaoBan_Client_v2.3.8.exe` và `Output/Setup_BaocaoGiaoBan_Server_v2.3.8.exe`.

## [2.3.7] - 2026-10-24 (Gộp kiểm toán tài liệu Single Leased Connection, Khôi phục 60 FPS WinForms UI Obfuscation & Khắc phục hiệu năng toàn diện)

### 🚀 Nâng cấp hiệu năng, Hạ tầng & Kiểm toán tài liệu (Performance & Infrastructure Remediation)
- **Gộp kiểm toán tài liệu Single Leased Connection (Phase 04 / COMP-05)**:
  - Bổ sung endpoint composite `/api/documents/audit-bundle` trong `ClinicalReportDataService.cs` và `ReportEndpoints.cs`. Gộp 4 truy vấn kiểm toán tài liệu (`UnsignedDocument`, `StiprolAudit`, `ProcedureAudit`, `BedAudit`) chạy tuần tự trên **1 single open NpgsqlConnection lease duy nhất**, giảm 75% áp lực leasing connection pool.
  - Client (`ApiClientService.cs` & `UnsignedDocumentControl.cs`) tích hợp `GetAuditBundleAsync` với cơ chế fallback tự động 100% trong suốt về 4 endpoint cũ khi gặp Server đời cũ (404), tương thích hoàn toàn C# 5.0 (.NET 4.8 via `csc.exe`).
- **Khôi phục 60 FPS WinForms qua tinh chỉnh ConfuserEx (Phase 04 / COMP-09)**:
  - Loại bỏ switch-mode control flow mangling và dynamic constants decryption khỏi `UI.Controls`, `UI.Components`, `UI.Forms` và `MainForm`, khôi phục 60 FPS WinForms không giật lag khi cuộn bảng hoặc CellFormatting, trong khi vẫn bảo mật tối đa hạ tầng mạng và DTOs.
  - Bộ kiểm thử `Test_Phase04_CompositeAuditBundleAndObfuscationPlan.cs` vượt qua 76/76 assertions (0 failures).
- **Vá cờ PE Header LARGEADDRESSAWARE (0x0020) & Tối ưu hóa Bộ nhớ**:
  - Tự động áp dụng cờ `LARGEADDRESSAWARE (0x0020)` trong `build.ps1`, mở rộng không gian ảo x86 lên 4GB.
  - Đóng gói chính thức bộ cài đặt production tại `Output/Setup_BaocaoGiaoBan_Client_v2.3.7.exe` (2.46 MB) và `Output/Setup_BaocaoGiaoBan_Server_v2.3.7.exe` (32.29 MB).
- **Truy vấn ca trực Single-Pass & Tối ưu hóa bộ nhớ Server (Phase 02 / CRIT-01, SRV-01, SRV-02, DB-02, DB-05)**:
  - Tái cấu trúc `/api/reports/shift-bundle` thành 1 lần truy vấn dữ liệu gộp duy nhất (3 truy vấn bệnh nhân + 1 truy vấn phẫu thuật) và ánh xạ trực tiếp trong RAM, cắt giảm từ 8–10 câu lệnh SQL xuống còn 4 câu lệnh.
  - Triển khai `readonly struct PatientRecordOrdinals` lưu vị trí cột cố định, triệt tiêu 68 lần tra cứu tên chuỗi và hiện tượng boxing cho mỗi bản ghi bệnh nhân khi nạp dữ liệu từ DataReader.
  - Mở rộng giới hạn Connection Pool PostgreSQL (`Maximum Pool Size=50; Minimum Pool Size=5`) xử lý triệt để tình trạng nghẽn kết nối trong giờ giao ban buổi sáng.
  - Tối ưu hóa bộ nhớ middleware: cache mảng byte API key trong `ApiKeyAuthenticationMiddleware`, cố định mảng `QueryKeys` / `HeaderNames` trong `HospitalOutputCachePolicy` và nhóm sự kiện kiểm toán y lệnh theo `StaffId` giảm độ phức tạp từ $O(S \times E)$ xuống $O(S + E)$.
- **Đồng bộ luồng Client, Triệt tiêu rò rỉ GDI+ & Tìm kiếm Debounce (Phase 03 / CRIT-04, UI-01, UI-02, UI-03, UI-04, NET-01)**:
  - Bổ sung `[JsonIgnore]` trên Server DTOs cho các danh sách phái sinh (`InCycleTreatingPatients`, `CarryoverPatients`, `NewAdmissions`), giảm 60–70% dung lượng JSON truyền qua mạng.
  - Client tự động tái cấu trúc (`ReconstructDerivativeCollections`) các danh sách phái sinh sau khi nhận snapshot, đảm bảo nguyên vẹn 100% thứ tự sắp xếp theo Phòng - Giường - Tên bệnh nhân.
  - Triệt tiêu 100% rò rỉ Win32 HWND và GDI+ HFONT: hủy giải phóng control con trước khi xóa deck cảnh báo và tái sử dụng Font tĩnh ở cấp lớp trong `UnsignedDocumentControl`.
  - Quản lý vòng đời `CancellationTokenSource` chặt chẽ kèm bộ đếm tuần tự `_requestSequence` loại bỏ các phản hồi tìm kiếm trễ hoặc đã bị hủy.
  - Tăng tốc tìm kiếm bệnh nhân không độ trễ: tích hợp thuộc tính `NormalizedSearchIndex` (chuỗi chuẩn hóa bỏ dấu tiếng Việt) và bộ đệm Debounce 150ms.
- **An toàn đa luồng UI, Nén ảnh Fast-path & Tối ưu hóa Obfuscation (Phase 04 / EXP-01, EXP-02, SEC-01)**:
  - Đảm bảo an toàn tuyệt đối ranh giới luồng WinForms: đồng bộ hóa dữ liệu và trích xuất tùy chọn trên UI thread trước khi bàn giao cho luồng chạy ngầm tải lên Google Drive.
  - Thay thế chuỗi HTML trung gian bằng luồng trực tiếp `_wordService.GenerateWordDocument` khi xuất Word qua Drive.
  - Đường ống xử lý ảnh nhanh (Fast-path): tự động trả về mảng byte JPEG gốc khi ảnh đính kèm đã chuẩn định dạng ($\le 1400$px và orientation $\le 1$), loại bỏ hoàn toàn việc tạo `Bitmap` 32bpp và nén mất dữ liệu lần 2.
  - Tối ưu hóa ConfuserEx: loại bỏ chế độ switch control flow và giải mã hằng số động khỏi `Models` và `UI.Components` để khôi phục hiệu năng JIT, đồng thời duy trì mã hóa nghiêm ngặt trên toàn bộ các lớp hạ tầng mạng nhạy cảm.
- **Nghiệm thu toàn diện (164/164 assertions passed)**:
  - Vượt qua toàn bộ các bộ test tự động: `Test_Phase02_SinglePassBundleAndServerMemoryPlan.cs` (49/49), `Test_Phase03_ClientConcurrencyAndGdiLifecyclePlan.cs` (54/54), và `Test_Phase04_ExportPipelineAndObfuscationPlan.cs` (61/61).

## [2.3.7] - 2026-10-24 (Cô lập chẩn đoán chuyển khoa lâm sàng, loại bỏ tiền tố ICD-10 và đồng bộ hiển thị Word & PowerPoint)

### 🏥 Nghiệp vụ lâm sàng & Chuẩn hóa dữ liệu báo cáo giao ban
- **Cô lập chẩn đoán chuyển khoa (`chandoan_dieutri`) cho diện ra viện `dm_hinhthucravienid = 8`**:
  - Tách rời hoàn toàn cơ chế lấy chẩn đoán chuyển khoa khỏi chuỗi cascading `COALESCE(chandoanravien, chandoanmoinhat, chandoan, chandoan_in)`.
  - Trích xuất trực tiếp kết luận điều trị cuối cùng ghi nhận tại tab 'Xử trí' (`tb_medicalrecord.chandoan AS chandoan_dieutri`) trên cả Server (`ClinicalReportDataService.cs`) và Client engine (`EhcReportDataEngine.cs`).
  - Khắc phục triệt để lỗi ghi đè chẩn đoán: ngăn chặn chẩn đoán sơ bộ lúc vào viện hoặc ghi chú phiếu điều trị trung gian (như 'Đau bụng cấp tính/ TD viêm ruột thừa cấp') ghi đè lên kết luận chẩn đoán chuyển khoa thực tế (như 'Dị ứng, không xác định/ TD ban nhiễm trùng' của BN 26030839).
- **Bộ lọc làm sạch chẩn đoán chuyển khoa (`CleanTransferDiagnosis`)**:
  - Xây dựng hàm chuẩn hóa chuỗi tĩnh `CleanTransferDiagnosis` tự động loại bỏ tiền tố mã ICD-10 (`T78.4 - `, `[T78.4]`, `[T78.4] - `, `T78.4: `) và hậu tố (` (T78.4)`, ` [T78.4]`, ` - T78.4`).
  - Bảo toàn 100% văn bản chẩn đoán lâm sàng thuần túy, không cắt nhầm các ký tự viết tắt y khoa thông dụng (như 'TD', 'BN', 'CLS').
  - Cơ chế fallback an toàn: nếu trường kết luận điều trị trống/null, tự động quay về chẩn đoán quy ước có làm sạch.
- **Bảo toàn 100% không hồi quy (Zero-Regression) cho các diện bệnh nhân khác**:
  - Giữ nguyên vẹn 100% logic trích xuất chẩn đoán cho 8 bệnh nhân ra viện thông thường, chuyển viện (`dm_hinhthucravienid = 13`), bệnh nhân vào viện, ca mổ cấp cứu, mổ phiên và thủ thuật.
- **Đồng bộ hiển thị Word (.doc) và PowerPoint (.pptx)**:
  - Bảng "Bệnh nhân chuyển khoa" trong Word document (`EhcWordReportService.cs`) hiển thị chẩn đoán lâm sàng sạch, tên bệnh nhân và khoa nhận (`Khoa Nội nhi tổng hợp`).
  - Slide "BỆNH NHÂN CHUYỂN KHOA" trong PowerPoint presentation (`EhcPowerPointReportService.cs`) hiển thị văn bản chẩn đoán sạch trong cột 4.10 inch, tự động co chữ 10.5pt khi chẩn đoán dài > 55 ký tự với `wrap="square"` và `<a:normAutofit/>`.
- **Nghiệm thu toàn diện (98/98 assertions passed)**:
  - Hoàn tất kiểm thử Phase 01 (`Test_Phase01_DepartmentTransferDiagnosisPlan.cs`) và Phase 02 (`Test_Phase02_DepartmentTransferReportingParityPlan.cs`) trực tiếp trên CSDL PostgreSQL thực tế `bvsnbacninh`.

## [2.3.6] - 2026-10-24 (Đột phá kiến trúc truyền phát bộ nhớ thấp OpenXML PPTX, Streaming Google Drive ToBase64Transform & Mở rộng 4GB Virtual Address Space)

### 🚀 Nâng cấp hiệu năng & Kiến trúc Stream bộ nhớ thấp (Critical & Moderate Remediation Roadmap)
- **Truyền phát trực tiếp PresentationML ra đĩa (Khử hoàn toàn LOH Spikes & ms.ToArray)**:
  - Tái cấu trúc `EhcPowerPointReportService.GeneratePresentation` xây dựng gói OpenXML ZIP trực tiếp trên luồng đích `FileStream` với bộ đệm 64KB (`bufferSize = 65536`).
  - Triệt tiêu hoàn toàn hiện tượng phình to `MemoryStream` động và cấp phát mảng byte liên tục khổng lồ qua `ms.ToArray()` khi xuất báo cáo ra đĩa.
  - Hỗ trợ truyền phát trực tiếp các tệp ảnh đĩa dung lượng lớn thông qua `WriteZipBinaryStreamEntry` với bộ đệm 64KB (< ngưỡng 85.000 byte của LOH).
- **Vận chuyển dữ liệu Google Drive bộ nhớ siêu thấp (<4KB RAM)**:
  - Tái cấu trúc đường ống truyền tải `EhcGoogleDriveUploadService` loại bỏ hoàn toàn các chuỗi trung gian khổng lồ trong bộ nhớ.
  - Truyền phát nhị phân trực tiếp vào thân HTTP request body thông qua `CryptoStream` kết hợp `System.Security.Cryptography.ToBase64Transform`, bọc cấu trúc JSON chuẩn (`{"filename":"...", "mimeType":"...", "base64Data":"..."}`).
  - Tính toán độ dài HTTP `Content-Length` chính xác tuyệt đối theo công thức toán học $4 \times \lfloor (N + 2) / 3 \rfloor + \text{WrapperOverhead}$ mà không cần nạp tệp vào bộ nhớ, vô hiệu hóa `AllowWriteStreamBuffering` để vượt qua triệt để lỗi proxy `Transfer-Encoding: chunked` của Google Web Apps.
  - Giảm thiểu bộ nhớ managed tạm thời khi tải tệp 20MB từ >190 MB xuống dưới 0.5 MB.
- **Mở rộng không gian địa chỉ ảo 32-bit (LARGEADDRESSAWARE 0x0020)**:
  - Tự động kiểm tra và áp dụng cờ `/LARGEADDRESSAWARE` (`0x0020` trong `IMAGE_FILE_HEADER.Characteristics`) cho `BaocaoGiaoBan.exe` thông qua bản vá byte nhị phân PE bằng PowerShell thuần trong `build.ps1`.
  - Mở khóa toàn bộ 4 GB không gian địa chỉ ảo (user-mode virtual address space) cho tiến trình 32-bit trên hệ điều hành Windows 64-bit.
- **Tối ưu hóa Server & Client Concurrency (Phase 01 - Phase 03)**:
  - Triển khai endpoint gộp ca trực `/api/reports/shift-bundle` giảm thiểu từ 3 request mạng độc lập xuống còn 1 request duy nhất với cơ chế tự động fallback 404.
  - Tối ưu hóa bộ nhớ đệm `BoundedCycleDictionary` và quản lý vòng đời tài nguyên GDI (Pen/Brush) triệt tiêu rò rỉ bộ nhớ.
  - Giải phóng hoàn toàn khóa luồng tĩnh `_serializerLock` trong giải tuần tự hóa JSON bằng `ThreadLocal<JsonSerializer>`, đạt khả năng xử lý song song không độ trễ.
  - Sửa lỗi truy vấn bộ giải mã ảnh thành bộ mã hóa ảnh chuẩn `ImageCodecInfo.GetImageEncoders()` và cache kết quả cho nén JPEG.
- **Nghiệm thu toàn diện & Đóng gói sản xuất**:
  - Vượt qua toàn bộ 37/37 bài test tự động khắt khe trong `Test_Phase04_LowMemoryStreamingPlan.cs` với mức tiêu thụ RAM ổn định < 45 MB.
  - Đóng gói hoàn tất bộ cài đặt sản xuất: `Setup_BaocaoGiaoBan_Client_v2.3.6.exe` (2.57 MB) và `Setup_BaocaoGiaoBan_Server_v2.3.6.exe` (32.29 MB).

## [2.3.5] - 2026-10-20 (Đột phá tối ưu hóa hiệu năng DataReader, Tải song song Google Drive & Truy vấn kiểm toán Stiprol)

### 🚀 Nâng cấp hiệu năng & Tối ưu hóa toàn diện
- **Tối ưu hóa DataReader Hydration không boxing (Tăng tốc 50%)**:
  - Triển khai `ReportPatientOrdinals` lưu cache vị trí cột (column ordinals) trước vòng lặp đọc dữ liệu, loại bỏ hoàn toàn việc tra cứu tên chuỗi `r["column_name"]` trong `MapReaderToReportPatient`.
  - Sử dụng getter nhị phân định kiểu mạnh (`r.GetInt32`, `r.GetString`, `r.GetDateTime`, `r.IsDBNull`), loại bỏ triệt để heap object boxing cho kiểu dữ liệu nguyên thủy trên hàng nghìn bản ghi bệnh nhân.
- **Tối ưu hóa Stream Deserialization & Khử LOH Allocation (PERF-002, PERF-003)**:
  - Khởi tạo kết nối mạng với `HttpCompletionOption.ResponseHeadersRead` trong `ApiClientService`, đọc trực tiếp stream từ `ReadAsStreamAsync()` qua `StreamReader` và `JsonTextReader` vào `_sharedSerializer.Deserialize<T>()`, triệt tiêu hoàn toàn việc cấp phát chuỗi hàng Megabyte vào Large Object Heap (LOH).
  - Tái sử dụng phiên bản `JsonSerializer` dùng chung kèm khóa luồng an toàn (CS-03-03).
  - Triệt tiêu 100% cuộc gọi blocking `.GetAwaiter().GetResult()` và `.Result` trên UI thread; duy trì chu kỳ lặp message pump 60 FPS (độ trễ chỉ 4 ms khi tải dữ liệu nền).
  - Tối ưu hóa `BindDataToUi` với `SuspendLayout()` và `ResumeLayout(true)` ngăn chặn giật layout (Layout Thrashing) khi nạp danh sách hàng nghìn bệnh nhân.
  - Nghiệm thu trọn vẹn 25/25 bài test tự động đạt chuẩn trong `Test_Phase03_StreamingAndAsyncOptimizations.cs`.
- **Tải đồng thời tài liệu lên Google Drive (Giảm 45% thời gian chờ)**:
  - Nâng cấp `UploadDualReports` và `UploadDualReportsAsync` trong `EhcGoogleDriveUploadService` truyền tải đồng thời cả file Slide (.pptx) và Biên bản (.doc) qua `Task.WhenAll`.
  - Giảm thời gian chờ từ $T_{\text{pptx}} + T_{\text{doc}}$ (5.5s) xuống $\max(T_{\text{pptx}}, T_{\text{doc}})$ (~3.0s).
  - Tích hợp cơ chế báo cáo tiến độ hợp nhất và xử lý cô lập lỗi khi một trong hai tệp gặp sự cố.
- **Tối ưu hóa kế hoạch thực thi truy vấn kiểm toán thuốc Stiprol (PERF-010)**:
  - Ứng dụng Materialized Common Table Expression (`WITH active_mr AS MATERIALIZED (...)`) cô lập hồ sơ bệnh án đang nằm viện nội trú trước khi liên kết với hàng triệu bản ghi trong `tb_servicedata`.
  - Đánh giá mã hoạt chất BHYT `'40.707'` trước khi quét chuỗi ký tự đại diện để tận dụng tối đa chỉ mục btree, giảm thời gian thực thi xuống dưới 50ms.

## [2.3.4] - 2026-10-16 (Tối ưu hóa siêu tốc truy vấn Quản lý y lệnh, Khắc phục triệt để đơ lag & Mở rộng Timeout kết nối)

### 🚀 Nâng cấp hiệu năng vượt trội & Khắc phục đơ giật
- **Tối ưu hóa siêu tốc truy vấn y lệnh nội trú (Tăng tốc 85–175 lần)**:
  - Loại bỏ điều kiện lọc dư thừa `sd.departmentid = @deptId` trong CTE `raw_orders`, ngăn chặn triệt để hiện tượng PostgreSQL quét Bitmap Index 2.3 triệu dòng lặp lại 44 lần cho từng bệnh nhân nội trú (giảm từ 13.5 giây xuống 0.03 ms).
  - Vô hiệu hóa JIT compilation (`SET jit = off;`) trên kết nối kiểm toán y lệnh, loại bỏ hoàn toàn 2.2 giây độ trễ biên dịch LLVM bytecode của PostgreSQL.
  - Thời gian tra cứu 1 ngày (1 ca trực) giảm ngoạn mục từ **18.17 giây xuống 0.21 giây (213 ms)**.
  - Thời gian tra cứu 15 ngày giảm từ **19.20 giây xuống 0.87 giây (873 ms)**, xử lý dứt điểm tình trạng giao diện bị đơ giật, quay vòng tròn.
- **Nâng ngưỡng Timeout & Khắc phục thông báo lỗi mạng gián đoạn giả mạo**:
  - Nâng timeout mặc định lên 30 giây và mạng lưới an toàn toàn cục 60 giây trên Client.
  - Bắt đúng ngoại lệ `TimeoutException` để phân biệt rõ ràng giữa hết thời gian chờ và mất kết nối mạng.

## [2.3.3] - 2026-10-16 (Tối ưu hóa hiệu năng Quản lý y lệnh, Khắc phục triệt để rò rỉ tài nguyên & Cơ chế lọc mượt mà)

### 🚀 Nâng cấp hiệu năng & An toàn tài nguyên
- **Tối ưu hóa truy vấn PostgreSQL & Định phạm vi chỉ mục (Index Scoping)**:
  - Rút ngắn phạm vi kiểm tra y lệnh con trong câu truy vấn `NOT EXISTS (SELECT 1 FROM tb_servicedata c WHERE c.servicedataid_master = sd.servicedataid)` bằng cách gắn điều kiện `c.medicalrecordid = sd.medicalrecordid`.
  - Giới hạn liên kết bảng phẫu thuật thủ thuật `tb_servicedata_pttt` theo `pttt.medicalrecordid = sd.medicalrecordid`, loại bỏ hoàn toàn nguy cơ quét toàn bộ bảng (Sequential Scan) trên hàng triệu bản ghi y lệnh.
- **Khắc phục rò rỉ GDI Handle & Tăng tốc nạp bảng dữ liệu y lệnh (Bulk Loading)**:
  - Tái sử dụng font GDI tĩnh đã lưu vào bộ nhớ cache (`_fontViolationBold` và `_fontPatientCodeBold`), chấm dứt triệt để việc khởi tạo liên tục `new Font()` làm cạn kiệt giới hạn 10.000 handle của Windows dẫn tới crash ứng dụng.
  - Tạm thời vô hiệu hóa `AutoSizeColumnsMode` trong suốt quá trình nạp dữ liệu và chuyển sang nạp hàng loạt `Rows.AddRange(rowList.ToArray())`, giảm thời gian hiển thị bảng từ ~300ms–1000ms xuống dưới 10ms (nhanh gấp 30–100 lần), loại bỏ hoàn toàn hiện tượng đơ giật giao diện.
- **Giải phóng hoàn toàn Control Handle (HWND Leaks) trên thẻ trùng kíp PTTT**:
  - Thay thế phương thức `flpSurgeryCards.Controls.Clear()` bằng vòng lặp ngược gọi tường minh `c.Dispose()`, phá hủy đúng chuẩn các handle cửa sổ Win32 và giải phóng bộ nhớ RAM.
  - Bảo toàn nhãn trạng thái trống (`lblSurgeryEmptyState`) không bị hủy ngoài ý muốn.
- **Triệt tiêu vòng lặp sự kiện co giãn giao diện (Cascading Resize Event Storm)**:
  - Bổ sung cờ chặn lặp (`_isAdjustingSurgeryCardWidth`) trong hàm `AdjustSurgeryConflictCardsWidth()`.
  - Loại bỏ delegate ẩn danh `card.Resize += delegate` lồng bên trong từng thẻ mổ, loại bỏ xung đột tính toán kích thước gây tăng tải CPU và giật khung hình khi người dùng cuộn xem.
- **Cơ chế chờ chống nghẽn (Debounce 300ms) & Hủy yêu cầu cũ (Cancellation Token)**:
  - Tích hợp bộ đếm thời gian 300ms (`_roomFilterDebounceTimer`) khi người dùng tích/bỏ tích nhanh danh sách buồng bệnh trên bộ lọc phòng, gom các thao tác click liên tiếp thành 1 lần tải duy nhất.
  - Quản lý `CancellationTokenSource` (`_orderAuditCts`) trong `LoadOrderAuditAsync`, tự động hủy ngay lập tức các yêu cầu mạng đang dang dở khi có thao tác lọc mới, ngăn ngừa triệt để tình trạng tranh chấp dữ liệu (Race Condition).

## [2.3.2] - 2026-10-16 (Surgery Patient Address Retrieval Fix, Dynamic PPTX On-Demand Note & Order UI Streamlining)

### Fixed & Enhanced
- **Surgery & Procedure Patient Address Retrieval**:
  - Fixed root-cause SQL query in `BASE_SURGERY_SELECT_SQL` by selecting patient address fields (`sonha`, `thonxom`, `dm_xaname`, `dm_huyenname`, `dm_tinhname`) from `tb_patientrecord`.
  - Added address properties to `SurgeryRecordItem` in client and DTO models.
  - Updated data mappers (`EhcSurgeryDataEngine.cs`, `ClinicalReportDataService.cs`) to populate `ReportPatientItem.Address`, enabling Emergency Surgeries, Elective Surgeries, and Procedures to display address subtitles in PowerPoint (.pptx) and address columns in Word (.doc).
- **PowerPoint PPTX Slide 2 On-Demand Statistics Note**:
  - Dynamically computes and formats non-zero category counts under "PHẪU THUẬT - THỦ THUẬT" for Elective surgeries YC (`ca mổ phiên YC`), Emergency surgeries YC (`ca mổ cấp cứu YC`), and Procedures YC (`ca thủ thuật YC`).
  - Handles natural Vietnamese grammar conjunctions ("và", commas) across 3, 2, 1 categories, and cleanly omits the note box when 0 on-demand cases exist.
- **Order Management UI Streamlining**:
  - Removed redundant copy buttons `btnOrderCopyAll` ("📋 Copy tất cả Mã BN lỗi") from `flpOrderToolbar` and `btnCopySurgeryAll` ("📋 Copy Mã BN") from `pnlSurgeryHeader`.
  - Cleaned up event handlers and tooltips, while allowing `lblSurgeryHeader` to cleanly expand across the surgical conflict card header with `Dock = DockStyle.Fill`.
  - Preserved granular single-patient code copying on DataGridView row click/double-click and conflict card inspection.
- **Production Packaging**:
  - Version bumped to `v2.3.2`.
  - ConfuserEx 2 binary obfuscation applied and verified with 0 sensitive credentials.
  - Generated production installer `Setup_BaocaoGiaoBan_Client_v2.3.2.exe` (2.65 MB).

## [2.3.1] - 2026-10-15 (Elective Surgeries Integration, Patient Address Options & Clinical Export Polish)

### Added & Completed
- **Dashboard Elective Surgeries Tri-Split Layout**:
  - Added dedicated "Bệnh nhân mổ phiên" (Elective Surgeries) section with Ocean Blue branding (`#0284C7`).
  - Search filter, inline surgical method editing, clinical photo sync, and automatic KPI metric synchronization.
- **Report Export Options Deck**:
  - Added checkboxes: "Hiện địa chỉ bệnh nhân", "Hiện địa chỉ ở tất cả danh sách", and "Rút gọn địa chỉ (Xã - Tỉnh)".
- **PowerPoint PPTX Generation**:
  - Subtitle address rendering under patient name in Họ và tên column (Option A).
  - Ocean Blue theme for elective surgeries slide, pill badge, zebra striping, and auto-pagination.
- **Word DOC Generation & Clinical Styling**:
  - Dedicated "Địa chỉ" column for Elective Surgeries and conditionally for all tables based on export options.
  - Print-safe page break CSS (`tr { page-break-inside: avoid; }`, `thead { display: table-header-group; }`).
  - Subtle slate borders (`#cbd5e1`), comfortable padding (`5pt 7pt`), soft header background (`#f1f5f9`), zebra striping, and signature block protection.
  - UTF-8 with BOM encoding for Vietnamese diacritics compatibility in Microsoft Word.
- **Production Packaging**:
  - ConfuserEx 2 binary obfuscation applied to client binaries.
  - Compiled production installers via Inno Setup 6: `Setup_BaocaoGiaoBan_Client_v2.3.1.exe` and `Setup_BaocaoGiaoBan_Server_v2.3.1.exe`.

## [2.3.0] - 2026-10-13 (Clinical Order Audit Refinements, Department Scoping & Terminology Alignment)

### Added & Completed
- **Clinical Order Audit Department Scoping**:
  - Enforced strict department scoping (`sd.departmentid = @deptId`) within the order audit CTE query, ensuring external department orders (e.g. Paraclinical medications from Khoa Chẩn đoán hình ảnh and Khoa Thăm dò chức năng for Patient 26028746) are excluded from the pediatric inpatient ward audit.
- **Multi-Lot Medication Deduplication**:
  - Eliminated phantom unexecuted order violations for prescriptions split across multiple dispensary lots (e.g. Patient 20026820 Paracetamol 10mg/ml).
  - Utilized `DISTINCT ON (COALESCE(NULLIF(sd.servicedataid_master, 0), sd.servicedataid))` ordered by master ID and execution metadata presence (`ylenh_tachjson IS NOT NULL DESC`), ensuring executed lots properly consolidate and satisfy order fulfillment.
- **Clinical Terminology Standardization**:
  - Standardized surgery and procedure conflict card UI terminology in `UnsignedDocumentControl.cs`:
    - Updated button label from `"📋 BN mổ: "` to `"📋 BN có PTTT: "` (Patient with Surgery / Procedure).
    - Updated copy feedback toast notification to `"✓ Đã sao chép mã BN có PTTT: [Mã BN]"`.
    - Enhanced button tooltip: `"Sao chép mã bệnh nhân có phẫu thuật / thủ thuật vào clipboard"`.
- **E2E Integration, Packaging & Service Deployment**:
  - Hardened `tools/update_service.bat` for seamless hot-reloading of Windows Service `EhcReportApiService` with UAC elevation, non-blocking stream redirection, and MSBuild node-reuse disabling.
  - Rebuilt production installation packages via Inno Setup 6:
    - `Output/Setup_BaocaoGiaoBan_Client_v2.3.0.exe` (2.64 MB).
    - `Output/Setup_BaocaoGiaoBan_Server_v2.3.0.exe` (32.28 MB).
  - Validated zero-leakage security guarantee (OWASP A05:2021) with 0 plaintext IP/port occurrences across client binaries and UI.

## [2.3.1] - 2026-10-12 (Major Milestone: Clinical Order Audit Refinements, Multi-Room Filter & Zero-Leakage Security Hardening)

### Added & Completed
- **Clinical Order Audit Scope & Quality Refinements**:
  - **Retail Pharmacy Exclusion**: Excluded orders originating from pharmacy store 52 (`NHA_THUOC` / `dm_roomtypeid = 50`), ensuring take-home discharge prescriptions (e.g. Patient Trịnh Thị Thu) are not falsely audited as inpatient ward orders.
  - **Strict Medication Scope**: Restricted audit exclusively to medication orders (`dm_servicegroupid = 7`), fully excluding minor diagnostic and surgical procedures (`dm_servicegroupid = 5`, e.g. Patient 26029392 wound dressing).
  - **Rapid Execution Audit Purge**: Completely removed the $\le 180$s rapid execution check and orange KPI card, eliminating false-positive warnings for multi-medication administration rounds.
  - **Default Query Timeframe**: Defaulted time picker to full-day window `00:00:00 - 23:59:59` of today (`0h00 -> 23h59`).
- **Multi-Chamber Filter Component (`MultiSelectRoomFilterControl`)**:
  - Replaced single-select room dropdown with a modern **Multi-Select Checked Dropdown Filter**:
    - Diacritic-insensitive live search box for fast room code filtering (e.g. typing "83" matches 831, 833, 835).
    - Tri-state master checkbox `[✓] Tất cả các buồng` (Checked, Unchecked, Indeterminate).
    - Persistent checkbox popup allowing multi-room selections without dismiss on click.
    - Underlying state preservation via `HashSet<int>` ensuring search queries never lose selected rooms.
    - Smart text summarization: `"Tất cả các buồng"`, `"Buồng 803"`, `"803, 805"`, or `"Đã chọn N buồng"` with tooltip.
  - Backend query parameter `chamberIds` supports comma-separated (`?chamberIds=803,805`), repeated (`?chamberIds=803&chamberIds=805`), and legacy single room (`?chamberId=803`) via PostgreSQL `= ANY(@chamberIds::int[])`.
- **Surgery Conflict Panel UI Fix & Modernization**:
  - Resolved zero-width layout collapse bug by removing conflicting `AutoSizeMode.GrowAndShrink` container loop, fixing cards to container width (`flpSurgeryCards.ClientSize.Width - 16`) with `AutoSizeMode.GrowOnly`.
  - Modernized conflict cards with purple doctor badges (`#6b21a8`), slate surgery schedule banners (`#475569`), red violation order lists (`#b91c1c`), and dynamic hover borders.
  - Single-click clipboard copy for both surgical and conflicting order patient codes with anchored floating toast notification.
- **Zero-Leakage Security Hardening (OWASP A05:2021)**:
  - Sanitized all technical connection failure strings across Status Bar, MessageBox, and Header Tooltips to eliminate all occurrences of server IP (`10.0.8.22`), port (`5000`), and service name (`Người Đưa Tin`).
  - Guaranteed `Data/ClientSettings.json` contains zero plaintext IP/port on disk.
  - Dynamic byte assembly runtime resolution via bit-packed octets and XOR masking in `ServerEndpointProvider.cs`.
- **Packaging, Service Update & Comprehensive E2E Verification**:
  - Hot-reloaded `EhcReportApiService` Windows Service via `tools/update_service.bat` with automatic administrative privilege elevation and zero-hang exit code 0.
  - Built production installers: `Setup_BaocaoGiaoBan_Client_v2.3.0.exe` (2.65 MB) and `Setup_BaocaoGiaoBan_Server_v2.3.0.exe` (32.29 MB).
  - Executed single comprehensive file test `tests/Test_Phase04_E2E_OrderAuditAndUiVerification.cs` passing 57/57 assertions.

## [2.3.0] - 2026-10-12 (Major Milestone: Clinical Order Execution Audit ("Quản lý y lệnh"), Room Filter & Production Packaging)

### Added & Completed
- **Clinical Order Execution Audit Engine ("Quản lý y lệnh")**:
  - **Backend REST API Endpoints (`/api/documents`)**:
    - `GET /api/documents/department-rooms`: Queries active rooms/chambers in department (`tb_dm_buongbenh`) with internal memory caching.
    - `GET /api/documents/order-execution-audit`: Audits inpatient clinical orders (`tb_medicalrecord_chandoan`, `tb_medicalrecord_chandoan_chitiet`) across 3 critical quality dimensions:
      - **Unexecuted Orders (`Unexecuted`)**: Identifies orders where execution flag `dathuchien = 0` or execution timestamp is missing after designated shift window.
      - **Rapid Sequential Execution (`RapidSequential`)**: Detects suspiciously rapid sequential executions with $\Delta t < 3$ minutes between consecutive orders.
      - **Surgical Execution Conflict (`SurgeryConflict`)**: Cross-checks order execution timestamps against patient intraoperative surgical windows (`tb_medicalrecord_phauthuat`), flagging orders executed during surgery.
  - **Client WinForms Sub-Tab & Control Integration (`UnsignedDocumentControl`)**:
    - Dual-tier navigation header with smooth sub-tab switching between **"Hồ sơ chưa ký"** and **"Quản lý y lệnh"**.
    - **KPI Alert Cards**: 3 interactive pill badge cards ("Chưa thực hiện", "Thực hiện sát giờ (< 3p)", "Trùng giờ phẫu thuật") with click-to-filter support.
    - **Chamber / Room Filter**: Searchable dropdown (`cboOrderRoom`) to scope audits to specific department rooms/wards.
    - **Audit Window Picker**: Customizable time picker with instant 1-click synchronization to active shift cycle.
    - **Interactive DataGridView**: Color-coded rows with status badges and 1-click patient code copy `📋` with instant visual toast feedback.
    - **Surgery Conflict Panel**: Dedicated right-hand card panel highlighting conflicted surgical times and patient details.
- **Production Packaging & Security Hardening**:
  - Version incremented to `v2.3.0` (`2.3.0.0`) across all assemblies, project manifests, and installers.
  - Obfuscated client binary with ConfuserEx 2 (Control Flow, Dynamic Constants Encryption, and DTO preservation).
  - Packaged Inno Setup 6 production installers:
    - `Output/Setup_BaocaoGiaoBan_Client_v2.3.0.exe` (~2.6 MB).
    - `Output/Setup_BaocaoGiaoBan_Server_v2.3.0.exe` (~33 MB).
  - Synchronized standalone client binary to `BaocaoGiaoBan_Standalone\BaocaoGiaoBan.exe` and root `EHC_PediatricTool.exe`.
  - Self-contained .NET 8 Server published to `publish/Server` and verified live with `EhcReportApiService`.

## [2.2.6] - 2026-10-12 (Bugfix Release: Surgical Consent Form Patient Signature Disambiguation & Production Installer Packaging)

### Fixed
- **Informed Consent Signature Disambiguation (`Phiếu cam đoan PTTT`)**:
  - Fixed a critical query bug in `ClinicalReportDataService.cs` (`unsigned_consent_docs` CTE in both department-wide and single-surgeon modes) where patient/family fingerprint signatures (`Chukynguoibenh$`) recorded under the attending doctor's account ID were misinterpreted as the doctor's completed signature.
  - Added filter `AND (s.tenbien IS NULL OR s.tenbien NOT ILIKE '%nguoibenh%')` to ensure surgical consent forms remain flagged as unsigned until the responsible surgeon completes their digital signature (`ChuKyBS1$`).
  - Verified with real hospital case: Patient **NGUYỄN HUY ANH QUÂN** (`24044623`) now immediately and correctly surfaces in Dr. **Nguyễn Duy Trường**'s unsigned documents list.

### Packaging
- Compiled and packaged official Inno Setup installers:
  - `Output/Setup_BaocaoGiaoBan_Client_v2.2.6.exe` (Lean client < 3.0 MB).
  - `Output/Setup_BaocaoGiaoBan_Server_v2.2.6.exe` (Self-contained Windows Service ~ 32.5 MB).

## [2.2.5] - 2026-10-09 (Major Milestone: Unsigned Surgery & Informed Consent Documents Audit, Surgeon Filtering & Production Packaging)

### Added & Completed
- **Unsigned Surgery & Informed Consent Documents Audit (`/api/documents/unsigned`)**:
  - **Operative Procedure Reports (`Phiếu phẫu thuật, thủ thuật`)**: Detects unsigned major surgical operations (`dm_pttt_loaiid IN (1, 2, 3, 4, 9)`) where EMR PDF has not been generated or signature count is incomplete. Attributed to primary operating surgeon (`userid_phauthuatvien_1`) or entering user.
  - **Informed Consent Documents (`Phiếu cam đoan PTTT`)**: Audits surgical consent forms where EMR PDF has not been generated or counseling/operating surgeon (`bacsi_id`) signature is incomplete.
  - **Role & Direct Duty Assignment**: Mapped role variables `phauthuatvien` and `phauthuatvien_camdoan` to display name `"Phẫu thuật viên"` with direct responsibility (`IsDirectResponsibility = true`).
  - **Department-Wide & Single-Surgeon Query Modes**: Supports both `checkAll = true` and single-surgeon filtering (e.g. `bspvdat` for BSCKII. Phạm Văn Đạt).
- **Client UI & Grid Presentation**:
  - Added dedicated styling and badge aesthetics for role `"Phẫu thuật viên"` and duty `"Trực tiếp"`.
  - Clean surgeon naming without repetitive prefixes in `colSignerName`.
  - Filterable by `SearchableStaffDropdown` with live KPI counter updates.
  - Native OpenXML XLSX export preserves surgical document rows and surgeon details.
- **Production Packaging & Obfuscation**:
  - ConfuserEx 2 obfuscation with 0 plaintext occurrences of server IP `10.0.8.22` and database credentials.
  - Hot-deployed self-contained win-x64 server to Windows Service `EhcReportApiService` on port 5000.
  - Inno Setup 6 installers compiled: `Setup_BaocaoGiaoBan_Client_v2.2.5.exe` (< 3.0 MB) and `Setup_BaocaoGiaoBan_Server_v2.2.5.exe` (~32.5 MB).

## [2.2.4] - 2026-10-07 (Major Milestone: Clinical Quality Audits, Searchable Staff Dropdown, XLSX Export & 3-Tab Alert Deck)

### Added & Completed
- **Tri-Fold Clinical Quality Audits (Stiprol, Procedures & Bed Day Services)**:
  - **Hardened Stiprol Compliance**: Verified compliance strictly when both Fee-based (`dm_serviceobjectid = 2`) AND Service Object is Locked (`iskhoadoituongdv = 1`). Violations (BHYT or unlocked fee-based) flagged with clear actionable guidance.
  - **Procedure & Surgical Errors Audit (`/api/documents/procedure-audit`)**: Audits missing medical equipment (`equipmentid = 0`) for nebulization ("Khí dung mũi họng"), intussusception air reduction ("Tháo lồng bằng bơm khí/nước"), and all surgical operations; detects missing surgical personnel and blank narrative remarks.
  - **Missing Bed Service Audit (`/api/documents/bed-audit`)**: Audits patients admitted during the shift window (16:30 -> 07:30) who lack inpatient bed day charges (`dm_servicegroupid = 10`), flagging patients like Nguyễn Huy Anh Quân while clearing compliant patients like Trịnh Văn Quốc Anh.
- **Searchable Staff Selector (`SearchableStaffDropdown.cs`)**:
  - Replaced standard combobox with searchable dropdown featuring surface-wide click-to-open, real-time accent-insensitive search, and keyboard navigation.
- **Signer Name Sanitization**:
  - Stripped academic titles ("BS.", "BS ", "Bs.", "BSCKI.", "BSCKII.") from `colSignerName` in documents grid, presenting clean staff names.
- **Native OpenXML XLSX Export Engine (`ExcelExportHelper.cs`)**:
  - Implemented zero-dependency Excel exporter producing styled `.xlsx` workbooks with professional navy headers, alternating row striping, number formats, auto-fit columns, and instant-open confirmation dialog.
- **3-Tab Clinical Alert Deck (`UnsignedDocumentControl.cs`)**:
  - Unified alert deck with 3 pill buttons: Stiprol BHYT, Thủ thuật thiếu VTYT, and Chưa gõ ngày giường, complete with dynamic badges, warning cards, and 1-click clipboard copy.
- **Production Packaging & Hot Deployment**:
  - ConfuserEx 2 obfuscation with 0 plaintext IP occurrences (`10.0.8.22`).
  - Hot-deployed Windows service `EhcReportApiService` on port 5000.
  - Packaged Inno Setup 6 production installers: `Setup_BaocaoGiaoBan_Client_v2.2.4.exe` (2.6 MB) and `Setup_BaocaoGiaoBan_Server_v2.2.4.exe` (32.27 MB).

## [2.2.3] - 2026-10-06 (Major Milestone: Zero UI Text Clipping, Full Staff Directory Loading, On-Demand Query Engine & Dedicated Signer Column)

### Added & Completed
- **Zero UI Text Clipping & Anti-Clipping Rebalancing**:
  - Rebalanced header layout bounds (`btnSelectCycle` 180px, `pnlSegmentedNav` X=195, `pnlHeaderRight` 265px, `pnlHeaderLeft` 190px) across both `MainForm.Designer.cs` and runtime `UpdateHeaderLayout()`, eliminating right corner clipping of the segmented capsule nav across 1264x681 and 1024x768 resolutions.
  - Re-anchored KPI cards with AutoEllipsis and minimum sizing, preventing counter number and label clipping under multi-monitor DPI scaling.
- **Full Staff Directory Loading (Root Cause Fix)**:
  - Resolved `InitializeServices` condition bug (`cboAccount.Items.Count <= 1`) so all department doctors and nurses are populated into `cboAccount` alongside `⭐ Check all (Toàn khoa)` at index 0.
- **On-Demand Query Engine (Lazy Loading Semantics)**:
  - Decoupled dropdown and tab selection events from automatic server queries.
  - Queries execute strictly on user intention: clicking **"🔍 Tra cứu"**, pressing `Enter`, or clicking **"🔄 Làm mới (F5)"**.
  - Informative initial guidance state (`lblEmptyState`) and hidden Stiprol banner until an explicit search is performed.
- **Dedicated "Bác sĩ / Người ký" Column (`colSignerName`)**:
  - Added dedicated `colSignerName` column to `dgvDocuments`, cleanly separating signer identity from role titles (`colSignRole`) and duty badges (`colResponsibility`).
  - Doctor-scoped Stiprol audit filtering when querying individual medical staff (`checkAll = false`).
- **Packaging, Obfuscation & Hot Deployment Pipeline**:
  - ConfuserEx 2 obfuscation with Dynamic Byte Assembly verification asserting 0 plaintext IP occurrences (`10.0.8.22`).
  - Hot-deployed Windows Service `EhcReportApiService` on port 5000.
  - Packaged Inno Setup 6 production installers: `Setup_BaocaoGiaoBan_Client_v2.2.3.exe` (< 3.0 MB) and `Setup_BaocaoGiaoBan_Server_v2.2.3.exe` (self-contained .NET 8).

## [2.2.2] - 2026-10-06 (Major Milestone: Stiprol BHYT Prescription Audit, Department-Wide Scope, Tab Corner Fix & Client IP Obfuscation)

### Added & Completed
- **Stiprol BHYT Prescription Audit Engine**:
  - Backend REST API `GET /api/documents/stiprol-audit` auditing active Stiprol prescriptions (code `40.707` / `servicename ILIKE '%stiprol%'`) ordered under BHYT object (`dm_serviceobjectid IN (3, 4)` / `tongbhyt > 0`).
  - Smart Audit Alert Banner at bottom of Unsigned Documents tab: safe state (emerald green `#F0FDF4`) vs warning state (amber/red `#FEF2F2`) displaying violating patients with inline 1-click patient code copy `📋` and copy-all button for Zalo notifications.
- **Department-Wide Scope ("⭐ Check all (Toàn khoa)")**:
  - Added `⭐ Check all (Toàn khoa)` as index 0 in Doctor/Staff dropdown (`cboAccount`), querying entire department (`dm_departmentid = 24`) for both unsigned documents and Stiprol audits without Cartesian SQL multiplication, with responsible doctor identity (`SignerName`) rendered in grid.
- **Segmented Navigation Header Right Round Corner Fix**:
  - Rebalanced header layout bounds (`btnSelectCycle` 180px, `pnlSegmentedNav` X=195, `pnlHeaderRight` 265px) across both `MainForm.Designer.cs` and runtime `UpdateHeaderLayout()` in `MainForm.cs`, eliminating 26px clipping under `pnlHeaderRight` on non-maximized windows (1264x681 and 1024x768).
- **Default Direct Responsibility Filter**:
  - Initialized `chkOnlyDirect.Checked = true` on startup for streamlined single-doctor review.
- **Client IP Obfuscation via Dynamic Byte Assembly**:
  - Eradicated 100% plaintext IP string literals (`10.0.8.22`, `http://10.0.8.22:5000`) from MSIL metadata (`#Strings` / `#US`) via runtime bitwise octets and rolling XOR decoding stubs.
  - Confirmed 0 occurrences of `10.0.8.22` in binary raw byte inspection.
- **Packaging & Hot Deployment Pipeline**:
  - ConfuserEx 2 obfuscation with Switch intensity 80, dynamic constants encryption, and DTO rename exclusions.
  - Hot-reloaded Windows Service `EhcReportApiService` on port 5000 via elevated `deploy_server.ps1`.
  - Packaged Inno Setup 6 production installers: `Setup_BaocaoGiaoBan_Client_v2.2.2.exe` (2.56 MB) and `Setup_BaocaoGiaoBan_Server_v2.2.2.exe` (32.27 MB).
  - Validated with 96/96 assertions passing in `Test_Phase04_E2E_ProductionVerification.exe` and 0 errors in `BaocaoGiaoBan.exe --verify-api`.

## [2.2.0] - 2026-10-06 (Major Milestone: Unsigned Electronic Document Tracker, 3-Way Navigation Tab & Draft Treatment Sheets)

### Added & Enhanced
- **Unsigned Electronic Document Tracking Dashboard (`UnsignedDocumentControl.cs`, `ClinicalReportDataService.cs`)**:
  - **Comprehensive 2-Stage EMR Document Lifecycle**:
    - **Draft / Unsaved Sheets**: Integrated `tb_mediboxdocument_chuatao` (`dm_mediboxdocumenttypeid = 8` for Treatment Sheets and `11` for Infusion Sheets) linked with `tb_medicalrecord_khambenh` and `tb_medicalrecord_truyendich`. Automatically captures newly entered clinical notes and infusion orders before doctors click "Save/Sign" in EMR Sanita.
    - **Formal EMR Electronic Documents**: Queries `tb_mediboxdocument` and `tb_mediboxdocument_sign` with candidate pool deduplication (excluding already signed shared positions).
  - **Backend REST API Endpoints**:
    - `GET /api/documents/unsigned`: Returns complete unsigned documents list, direct/shared counts, affected patients, and oldest pending days with `< 150ms` execution time.
    - `GET /api/documents/staff`: Returns active doctors/nurses per department for search autocomplete.
  - **Modern UI & Anti-Clipping 3-Way Navigation**:
    - 3-card KPI deck (Total Pending, Direct Primary Duty, Affected Patients).
    - `DoubleBufferedDataGridView` with 32px rows and auto fill-weight.
    - Rebalanced segmented header capsule (490px total: Report 160px, Inpatients 160px, Unsigned Docs 162px) guaranteeing zero text clipping and zero button overlap across 1024x768 to 1920x1080 screens.
    - Quick search, direct duty filter checkbox, formatted Zalo clipboard copy, and CSV export with UTF-8 BOM.
- **Obfuscation & Production Tooling (`confuser_client.crproj`, `build.ps1`, `update_service.bat`)**:
  - ConfuserEx 2 v1.6.0 switch intensity 80 + dynamic constants encryption protecting Client binary with 0 plaintext credentials.
  - Created `tools/update_service.bat` and `tools/deploy_server.ps1` for clean Windows Service hot-reloading.
  - Packaged Inno Setup installers: `Setup_BaocaoGiaoBan_Client_v2.2.exe` (2.54 MB) and `Setup_BaocaoGiaoBan_Server_v2.2.exe` (32.27 MB).

## [2.1.0] - 2026-10-04 (Major Architecture Milestone: 3-Tier API Migration & Dual Production Packaging)

### Added & Enhanced
- **3-Tier Architecture Migration & Security Hardening**:
  - **Server Workstation API (.NET 8 / ASP.NET Core Minimal APIs)**: Developed and deployed a high-performance backend API service running 24/7 on workstation `10.0.8.22` (`SANNHI49`), binding Kestrel to `http://*:5000` with `NpgsqlDataSource` connection pooling, RFC 7807 problem details, and `.NET 8 OutputCache` stampede locking.
  - **Zero-Credential Client Security**: Decoupled the WinForms client application completely from PostgreSQL. Purged all database connection strings, credentials, and raw SQL queries from the client codebase. Binary string analysis and assembly metadata inspection confirmed 0 occurrences of database IP, credentials, Npgsql references, or raw SQL table names.
  - **Windows Service Automation (`EhcReportApiService`)**: Configured the server to run as a native auto-starting Windows Service with 3-tier failure recovery policy (restarts after 60s) and automatic Windows Defender Firewall rules for inbound TCP port 5000.
- **Ultra-Lightweight Client Deliverable (`Setup_BaocaoGiaoBan_Client_v2.1.exe`)**:
  - **Drastic Footprint Reduction**: Pruned bulky legacy libraries (`Sanita.UI.dll` 206MB, `Sanita.Core.dll` 4MB, `Sanita.Utility.dll` 9.8MB, `Npgsql.dll` 416KB).
  - Reduced total client installer size from > 46 MB down to **2.23 MB** (< 5 MB target achieved).
  - Bundles exclusively: `BaocaoGiaoBan.exe` (482 KB), `ehc.ico`, `Dll\Newtonsoft.Json.dll` (540 KB), and local configuration JSONs.
- **Robust Client HTTP Networking (`ApiClientService.cs`)**:
  - Implemented singleton `HttpClient` architecture with `ServicePointManager.DefaultConnectionLimit = 50` and `ConnectionLeaseTimeout = 60000` (1 min DNS refresh).
  - Added seamless hostname failover from `http://10.0.8.22:5000` to `http://SANNHI49:5000` with descriptive Vietnamese diagnostics for network cable disconnections.
  - Full dual-view support: Handover Reports (`/api/reports/cycle-data`), Inpatient Census Snapshot (`/api/inpatients/snapshot`), and Surgeries (`/api/surgeries/cycle-data`).
  - Preserved local photo attachment management, BICUBIC photo compression, and client-side PowerPoint (.pptx) & Word (.doc) document generation.
- **Dual Deliverables & Unified Build Pipeline (`build.ps1`, `installer_server.iss`, `installer.iss`)**:
  - Multi-target build script supporting `.\build.ps1 -Target Client`, `.\build.ps1 -Target Server`, and `.\build.ps1 -Target All`.
  - Automated binary security audit integrated into the build pipeline.
  - Dual Inno Setup installers:
    - `Output/Setup_BaocaoGiaoBan_Server_v2.1.exe` (32.26 MB self-contained .NET 8 runtime + Windows Service).
    - `Output/Setup_BaocaoGiaoBan_Client_v2.1.exe` (2.23 MB ultra-lean doctor workstation installer).
- **Version Bump & Compatibility**:
  - Bumped version to `2.1.0.0` across assembly metadata, executables, window titles, and update services.
  - Retained full compatibility with `GitHubUpdateService.cs` and `publish_release.ps1` for frictionless client auto-updates.

### Fixed & Polished
- **Runtime Assembly Resolution (`AssemblyResolve`)**:
  - Hooked `AppDomain.CurrentDomain.AssemblyResolve` in `Program.cs` to resolve `Newtonsoft.Json.dll` from both root and `Dll\` subfolder dynamically, resolving fresh installation loading faults.
  - Added `BaocaoGiaoBan.exe.config` containing `<probing privatePath="Dll" />` and binding redirects to installer and standalone packages.
- **Header Security & Zero-Clipping Layout**:
  - Hidden internal Server API endpoint string (`10.0.8.22:5000`) completely from top header per user privacy request.
  - Redesigned `pnlHeaderRight` and expanded `lblHeaderLastUpdate` to 200px width with dynamic space anchoring, eliminating date/time text clipping.
  - Implemented responsive auto-sizing tab navigation (`btnTabReport` and `btnTabInpatients`) with adaptive font sizing (8.25pt/9pt), guaranteeing zero text truncation across non-maximized and compact windows.

## [2.0.0] - 2026-10-03 (Major Milestone: Monitored Patient Photo Deduplication, Clinical Slide Reordering & Chuyển Khoa Standardization)

### Added & Enhanced
- **Monitored Patient Photo Priority & Slide Deduplication (`EhcPowerPointReportService.cs`)**:
  - **Clinical Priority Rule**: Clinical photos for monitored patients (`MonitoredPatients`) are deferred solely to the "BỆNH NHÂN THEO DÕI" section, preventing photo duplicates in earlier sections while maintaining interactive thumbnail jump hyperlinks from intake tables directly to the photo slide.
  - **Global Presentation Deduplication**: Strictly guaranteed each patient has at most 1 photo slide throughout the entire PowerPoint report.
- **Clinical Slide Reordering & Terminology Polish**:
  - Reordered slides: "Bệnh nhân theo dõi" placed immediately after "Bệnh nhân vào viện", with procedure tables moved to the very end of the presentation.
  - Standardized terminology across UI and reports to "DỊCH VỤ YÊU CẦU".
  - Standardized discharged transfer patients (`dm_hinhthucravienid = 8` / `CHUYEN_KHOA`) on sidebar tree from erroneous "Điều trị kết hợp" to **"Chuyển khoa"** (`IsChuyenKhoa`), mapping `departmentid_next` and destination department names.
- **Packaging & Setup Wizard v2.0**:
  - Updated Inno Setup installer wizard to version 2.0 (`Setup_BaocaoGiaoBan_v2.0.exe`) with `users-modify` permissions for seamless auto-updates without UAC friction.
  - Bumped version to `2.0.0.0` across all assemblies, executables, and update services.

## [1.9.1] - 2026-10-03 (Milestone Release: Monitored Patient Photo Deduplication & Priority Rendering)

### Added & Enhanced
- **Phase 01 - Monitored Patient Photo Priority & Slide Deduplication (`EhcPowerPointReportService.cs`)**:
  - **Clinical Priority Rule**: Enforced clinical priority where patients designated in "BỆNH NHÂN THEO DÕI" have their photo slides deferred solely to the monitored patients section, ensuring comprehensive case evaluation in depth without cluttering earlier intake slides.
  - **Intake Duplicate Suppression**: Suppressed duplicate photo slides in earlier intake categories (Admissions / `vao_vien`, Emergency Surgeries / `mo_cap_cuu`, Elective Surgeries / `mo_phien`).
  - **Cross-Category Thumbnail Jump Hyperlinks**: Resolved table thumbnail hyperlinks so clicking patient photos in earlier intake tables (e.g. Admissions) seamlessly jumps directly forward to the dedicated photo slide under the Monitored Patients section.
  - **Global Presentation Deduplication Guarantee**: Maintained presentation-wide tracking (`globalRenderedPhotoPatientIds`) guaranteeing that every patient has at most one photo slide across the entire PowerPoint presentation deck.
- **Phase 02 - Production Standalone Build, Inno Setup Installer & Full Regression (`build.ps1`, `installer.iss`, `AssemblyInfo.cs`, `GitHubUpdateService.cs`)**:
  - **Milestone Increment to v1.9.1.0**: Updated assembly version, file version, window title, and GitHub update baseline to `1.9.1.0` / `v1.9.1`.
  - **Production Compilation**: Recompiled standalone binaries via `build.ps1` and synchronized `BaocaoGiaoBan_Standalone\BaocaoGiaoBan.exe`.
  - **Inno Setup Installer Packaging**: Re-packaged installer wizard `Output\Setup_BaocaoGiaoBan_v1.9.1.exe` via Inno Setup 6.
  - **Zero Regressions Verified**: Executed full comprehensive regression verification covering standalone binaries, headless `--verify`, slide sequence, Word HTML sequence, UI controls, and photo deduplication jump routing.

## [1.9.0] - 2026-10-03 (Milestone Release: Slide Reordering, Export UX Streamlining & Program Files Installer)

### Added & Enhanced
- **Phase 01 - Report Restructuring & Document Polish (`EhcPowerPointReportService.cs`, `EhcWordReportService.cs`)**:
  - **Transfer Column Streamlining**: Eliminated the redundant "Lý do" (Transfer Reason) column from both "BỆNH NHÂN CHUYỂN KHOA" and "BỆNH NHÂN CHUYỂN VIỆN" sections in both PowerPoint (.pptx) and Word (.doc) exports. Reallocated table width to broaden "Chẩn đoán" (~4.10") and "Chuyển đến" (~3.10") columns for optimal readability.
  - **PowerPoint Slide Reordering**: Moved "BỆNH NHÂN THEO DÕI" to immediately follow "BỆNH NHÂN VÀO VIỆN", with attached clinical photo slides rendered directly after the monitored patient table. Moved "THỦ THUẬT" presentation to the very end of the slide deck.
  - **Duplicate-Suppression Barrier Removal**: Lifted all 4 duplicate-suppression barriers (`emergencyWithPhotos`, `processedPhotoPatients`, `isEmergencyAlready`, and collision in `patientPhotoSlideMap`) so patients present in both Emergency Surgeries and Monitored Patients have dedicated photo slides and table jump links properly displayed in both clinical sections.
  - **Word Document Section Sequence Sync**: Reordered clinical sections in `BuildWordDocumentHtml` to 100% match PowerPoint sequence: Emergency Surgeries -> Elective Surgeries -> Admissions -> Monitored Patients -> Department Transfers -> Hospital Transfers -> Procedures.
- **Phase 02 - UI Streamlining & Modern Export Dialog (`MainForm.cs`, `MainForm.Designer.cs`, `ReportGeneratorControl.cs`, `FormExportSuccess.cs`, `Program.cs`)**:
  - **Header Tab Label Standardization**: Renamed inpatient statistics navigation tab from `"📋 CHI TIẾT THỐNG KÊ"` to `"📋 THỐNG KÊ CHI TIẾT"` across `MainForm.Designer.cs`, `MainForm.cs`, and synchronized `--verify` assertion in `Program.cs`.
  - **Export Options Panel Streamlining**: Removed obsolete checkboxes `chkShortenAddress` ("Rút gọn địa chỉ (Xã - Tỉnh)") and `chkMaskPatientName` ("Ẩn bớt tên bệnh nhi khi chiếu") from `ReportGeneratorControl` UI while maintaining backward-compatible property stubs. Compacted groupbox height to 80px, ensuring patient names are always rendered in full.
  - **Modern Deep Teal Export Success Dialog (`FormExportSuccess.cs`)**: Implemented a modern modal dialog featuring Deep Teal aesthetics, file info cards, and a prominent `📂 Mở thư mục chứa file` button executing `Process.Start("explorer.exe", "/select,...")` with normalized backslashes and directory fallback.
- **Phase 03 - Installer Optimization & Production Standalone Build (`installer.iss`, `AssemblyInfo.cs`, `GitHubUpdateService.cs`, `build.ps1`)**:
  - **Program Files Installer with Frictionless Auto-Update**: Configured Inno Setup script to target standard Windows system directory `DefaultDirName={autopf}\{#MyAppName}` (`C:\Program Files (x86)\Báo cáo Giao ban`) with `PrivilegesRequired=admin` and granted `users-modify authusers-modify` directory permissions to preserve the atomic 1-click in-place updater without UAC friction.
  - **Milestone Increment to v1.9.0.0**: Updated assembly metadata, window title, and GitHub update baseline to `1.9.0.0` / `v1.9.0`.
  - **Production Compilation**: Recompiled standalone binaries via `build.ps1` and synchronized `BaocaoGiaoBan_Standalone\BaocaoGiaoBan.exe`.

## [1.8.0] - 2026-10-03 (Milestone Release: Startup Auto-Load & Header "Last Update" Status Display)

### Added & Enhanced
- **Phase 01 - Header "Last Update" UI & Status Display (`MainForm.Designer.cs`, `MainForm.cs`)**:
  - Implemented an elegant two-tier stacked layout in `pnlHeaderRight` directly to the left of the `btnRefresh` (F5) button:
    - Top tier: Database connectivity indicator (`● Đã kết nối CSDL` in `#A5F3FC` / `#10B981` dot).
    - Bottom tier: Real-time Last Update status label (`lblHeaderLastUpdate`) displaying `Đã cập nhật: {HH:mm:ss} ({dd/MM})` in soft cyan `#A5F3FC`.
  - Added visual error handling: on database update failure or connection loss, switches status text to `⚠️ Lỗi cập nhật ({HH:mm:ss})` in warning coral/red `#F87171` while preserving the last successful update timestamp and error details in a ToolTip.
- **Phase 02 - Startup Auto-Load & Thread-Safety Hardening (`MainForm.cs`, `ReportGeneratorControl.cs`)**:
  - **Zero-F5 Startup**: Transformed data loading on launch into idiomatic C# 5.0 TAP (`async/await` with `await Task.Run`) and `WindowsFormsSynchronizationContext`, seamlessly loading both shift handover report metrics and inpatient statistics on startup with animated Deep Teal spinner overlay (`LoadingOverlayControl`).
  - **Startup Lifecycle Guard**: Introduced `_isInitializing` guard flag in `MainForm` to completely prevent premature synchronous `LoadData` execution when `cboDepartment` initializes prior to form rendering.
  - **Thread-Safety Hardening**: Exposed thread-safe `IsExcludeCombinationChecked` property on `ReportGeneratorControl` with `Invoke` marshaling and added overloaded `FetchReportData(deptId, cycle, excludeComb)` to bypass cross-thread UI calls from background worker threads under strict `CheckForIllegalCrossThreadCalls = true`.
  - **F5 Refresh & Cycle Switch Consistency**: Updated manual refresh (`RefreshDataAsync`) and cycle selection to asynchronously fetch report handover data alongside department snapshots, eliminating all UI freezes.
  - **Stale-Cycle Race Protection**: Guarded UI application in `LoadDataStartupAsync` with `_activeCycle == cycle` checking, preventing background startup queries from overwriting user-selected duty cycles.
- **Phase 03 - Standalone Production Build & Regression Verification (`build.ps1`, `tests/Test_Phase03_ProductionBuildAndVerification.cs`)**:
  - Incremented version to `1.8.0.0` (ProductVersion `1.8.0`, window title `BaocaoGiaoBan v1.8.0`, and fallback baseline in `GitHubUpdateService`).
  - Compiled standalone executable `EHC_PediatricTool.exe` (552.5 KB, x86) and synchronized to `BaocaoGiaoBan_Standalone\BaocaoGiaoBan.exe`.
  - Comprehensive single file-based regression verification test suite passed 100% with 0 errors across both root and standalone binaries, headless verification mode (`--verify`), header last update display, zero-F5 startup auto-load, terminology standardization, and PPTX 3-layer auto-fit layout.
- **Phase 04 - GitHub Releases Deployment & Parser Hotfix (`publish_release.ps1`, `GitHubUpdateService.cs`)**:
  - Successfully packaged Inno Setup installer (`Setup_BaocaoGiaoBan_v1.8.0.exe`, 43.87 MB) and deployed release tag `v1.8.0` to GitHub repository `skul9x/Report_Tool_Releases`.
  - Fixed UTF-8 payload encoding and Content-Type headers in `publish_release.ps1` for GitHub REST API release creation.
  - **Critical Hotfix (Depth-Aware Bracket Parsing)**: Resolved asset extraction failure in `GitHubUpdateService.cs` where nested `"uploader": { ... }` objects broke `\{([^{}]+)\}` regex, causing `DownloadUrl` to be empty. Replaced with depth-aware bracket scanning, ensuring foolproof 1-click automatic updates across all future versions.

## [1.7.1] - 2026-10-01 (On-Demand Terminology Standardization & PowerPoint 3-Layer Text Overflow Protection)

### Added & Enhanced
- **Phase 01 - On-Demand Terminology Standardization (`EhcPowerPointReportService.cs`, `EhcWordReportService.cs`, `ReportGeneratorControl.cs`, `EhcStatisticsTreeService.cs`, `FormSelectMonitoredPatient.cs`)**:
  - Standardized terminology from "MỔ YÊU CẦU" to "DỊCH VỤ YÊU CẦU" across UI (KPI Card 8, Statistics Tree, and Monitored Patient Selector dropdown), PPTX Slide 2 & Slide 3, and Word Section II & Section III.
  - Added dynamic footnote on PPTX Slide 2 decomposing on-demand counts into elective surgeries and minor procedures (`Bao gồm X ca mổ phiên YC và Y ca thủ thuật YC.`).
  - Strictly preserved `[YC]` clinical tag suffix on patient names across all exports and views.
- **Phase 02 - PowerPoint 3-Layer Text Overflow Protection Architecture (`EhcPowerPointReportService.cs`)**:
  - **Layer 1 (Geometry Rebalancing)**: Rebalanced procedure table column widths by allocating 2.95" to `pp_mo` (Phẫu thuật, thủ thuật) out of 12.20" total width.
  - **Layer 2 (Dynamic Font Scaling)**: Dynamically reduced font size from standard 12.5pt (`sz="1250"`) to 10.5pt (`sz="1050"`) for clinical procedure strings exceeding 55 characters.
  - **Layer 3 (DrawingML Text AutoFit)**: Embedded `<a:normAutofit/>` safety net tag inside `<a:bodyPr>` across all table cells to eliminate overflowing, row overlapping, or truncated text at slide bottoms.
- **Phase 03 - Standalone Production Build & Verification (`build.ps1`, `tests/Test_Phase03_ProductionBuildAndVerification.cs`)**:
  - Recompiled standalone executable `EHC_PediatricTool.exe` (551 KB, x86) and synchronized to `BaocaoGiaoBan_Standalone\BaocaoGiaoBan.exe`.
  - Verified 100% PASS rate across all requirements.

## [1.7.0] - 2026-09-30 (Standardized Filenames, Startup Auto-Load, Multi-Day Presentation Polish & Remote Auto-Update)

### Added & Enhanced
- **Phase 01 - Canonical Report Filename Standardization (`ReportGeneratorControl.cs`)**:
  - Implemented hospital-compliant standard naming for single-day reports: `GiaoBan_dd-MM-yyyy.pptx` and `BienBanGiaoBan_dd-MM-yyyy.doc`.
  - Implemented multi-day naming for duty cycles spanning > 1.1 days: `GiaoBan_{N}_Ngay_{start}_{end}.pptx` and `BienBanGiaoBan_{N}_Ngay_{start}_{end}.doc`.
  - Guaranteed clean overwrite of closed files without appending incremental numeric suffixes (`_1`, `_2`).
- **Phase 02 - Right Column UI Cleanup & Async Startup Auto-Load (`ReportGeneratorControl.cs`, `MainForm.cs`)**:
  - Permanently eliminated `grpWeekendSummary` (`dgvWeekend`) from the right column of `ReportGeneratorControl`, bringing export actions and options into direct viewport visibility.
  - Implemented immediate asynchronous startup data loading with rotating GDI+ Deep Teal spinner overlay (`LoadingOverlayControl`), eliminating initial empty UI state and freezing.
- **Phase 03 - Multi-Day Presentation Polish (`EhcPowerPointReportService.cs`, `EhcWordReportService.cs`)**:
  - Slide 1 Subtitle: Formatted as `Báo cáo giao ban {N} ngày: {d1}/{m1}, {d2}/{m2}, ..., {dn}/{mn}` for multi-day shifts ($N > 1$); strictly preserved full Vietnamese date (`Thứ..., ngày... tháng... năm...`) for 24h single-day shifts.
  - Slide 3 in PPTX: Dynamic header `TỔNG HỢP {N} NGÀY TRỰC`, pill badge `{start:dd/MM} – {end:dd/MM}`, summary total row `CỘNG {N} NGÀY`, and completely eliminated legacy reporting italic note.
  - Word Section III: Standardized title `III. Tổng hợp {N} ngày trực ({start:dd/MM} – {end:dd/MM})`, summary row `Cộng {N} ngày`, and removed legacy note below table.
- **Phase 04 - Remote Auto-Update via GitHub Releases (`GitHubUpdateService.cs`, `FormUpdatePrompt.cs`)**:
  - Added non-blocking silent background update checking on application startup against GitHub repository `skul9x/Report_Tool_Releases`.
  - Enforced TLS 1.2 protocol for secure asset downloading and SemVer-compliant version comparisons.
  - Created polished modal update prompt (`FormUpdatePrompt`) featuring Deep Teal header, vector cross badge, version diff tag comparison, markdown-style changelog viewer, and Emerald action button.
  - Designed zero-dependency atomic self-updating command script via `cmd.exe` timeout handoff, replacing executable and cleanly restarting application.
- **Phase 05 - Standalone Production Build & Verification (`build.ps1`, `Test_Phase05_ProductionBuildAndVerification.cs`)**:
  - Recompiled standalone executable `EHC_PediatricTool.exe` (548 KB, x86) and synchronized to `BaocaoGiaoBan_Standalone\BaocaoGiaoBan.exe`.
  - Verified 100% PASS rate across all 5 phases and complete headless regression test suite (`--verify`).

### Fixed
- **Single-Day Report Multi-Day Phantom Subtitle Fix (`ReportModels.cs`, `EhcPowerPointReportService.cs`)**:
  - Fixed bug where single-day 24h reports displayed `Báo cáo giao ban 3 ngày: 1/1, 1/1, 1/1` on PPTX Slide 1.
  - Corrected `ReportWeekendSummaryItem.GetDailyItems()` to only include days with valid dates (`FridayDate != DateTime.MinValue`) instead of evaluating initialized non-null metric items.
  - Added `ReportWeekendSummaryItem.HasData` property to accurately detect populated weekend/multi-day tables.
  - Constrained Slide 1 subtitle logic so duty cycles $\le 1.1$ days strictly render full Vietnamese date and never fall through to unpopulated weekend summary days.
  - Synchronized `_currentData.ShiftInfo.CycleStart`, `CycleEnd`, and `ShiftDate` with `_currentCycle` in `ReportGeneratorControl` prior to export.

## [1.6.0] - 2026-09-30 (Modern Header Redesign, Segmented Nav Capsule, Contextual Weekend Summary & Shift Badge Polish)

### Added & Enhanced
- **Modern Hospital Header Redesign (`MainForm.Designer.cs`, `MainForm.cs`)**:
  - **Brand & Department Identity (`pnlHeaderLeft`)**: Crisp vector medical cross icon badge (`#10B981` to `#0D9488` gradient), title **Báo Cáo Giao Ban** (11.25pt Bold White), and subtitle **Khoa Ngoại Nhi (K18)** (9pt Soft Cyan `#67E8F9`) dynamically bound to active department.
  - **Shift Cycle Dropdown Pill (`btnSelectCycle`)**: Styled as a rounded pill capsule (`#083344` Navy with `#155E75` border, `#0E7490` hover, Hand cursor) with canonical text format `📅 Ca trực: {dd/MM HH:mm} - {dd/MM HH:mm} ▾`.
  - **Segmented Navigation Capsule (`pnlSegmentedNav`)**: Unified capsule container (`#083344`) grouping `btnTabReport` and `btnTabInpatients`; active tab highlighted in vibrant cyan `#0891B2` with bold white text, inactive tab in dark navy `#083344` with muted cyan text `#CFFAFE`.
  - **Status Indicator & Action Deck (`pnlHeaderRight`)**: Increased width to 280px, displaying side-by-side database connection indicator `lblHeaderDbStatus` (`● Đã kết nối CSDL` with `#10B981` green dot and `#A5F3FC` text) and flat borderless emerald refresh button `btnRefresh` (`#0D9488` with `#10B981` hover).
  - **Flicker-Free Rendering & Anti-Aliasing**: Implemented GDI+ `GraphicsPath` with `SmoothingMode.AntiAlias` and enabled `DoubleBuffered` across `pnlHeader`, `pnlHeaderLeft`, `pnlHeaderCenter`, `pnlHeaderRight`, and `pnlSegmentedNav`.
  - **Responsive Layout**: High-DPI and multi-resolution layout verified with zero text clipping across 1024x600, 1280x720, 1366x768, and 1920x1080.
- **Contextual Weekend Summary Panel (`ReportGeneratorControl.cs`)**:
  - Dynamically collapses `pnlWeekendSummary` during normal 24h weekday shifts, expanding automatically only for weekend/multi-day handover cycles (> 48 hours / Friday-Monday windows).
- **Staff Banner Polish & Reload Button Streamlining (`ReportGeneratorControl.cs`)**:
  - Removed clipped redundant reload button in staff banner while maintaining 100% backward-compatible hidden proxy for automated suites; styled banner in soft ice-cyan `#E0F2FE` with dark cyan `#0369A1` cycle label.

## [1.5.2] - 2026-09-29 (Streamlined Clinical Exports, [YC] Tag Integration, No-Suffix Overwrite & Pre-Flight Lock Alerts)

### Added & Enhanced
- **PowerPoint Presentation Streamlining (`EhcPowerPointReportService.cs`)**:
  - Omitted detailed slide sections for Discharged Patients and On-Demand Surgeries, keeping aggregate counts and balance equation on the summary overview slide.
  - Reordered procedure slides (`thu_thuat`) to immediately follow admissions (`vao_vien`) for a natural clinical handover flow.
  - Dynamically appended ` [YC]` tag to patient names in emergency surgeries, elective surgeries, and minor procedures when performed on demand (`IsMoYeuCau == true`).
- **Word Document Streamlining & 100% Parity (`EhcWordReportService.cs`)**:
  - Excluded detailed tables for Discharged Patients and On-Demand Surgeries while retaining aggregate KPIs in Section II.
  - Relocated Minor Procedures table to immediately follow Admissions.
  - Dynamically appended ` [YC]` tag to patient names across emergency, elective, and procedure tables.
- **Strict Canonical File Naming & Direct Overwrite (`ReportGeneratorControl.cs`)**:
  - Eliminated incremental suffix generator loops (`_2`, `_3`...) across `GetAvailableFilePath` and `GetAvailableDualFilePaths`.
  - Enforced strict canonical filenames (`GiaoBan_dd-MM-yyyy.pptx`, `BienBanGiaoBan_dd-MM-yyyy.doc`).
  - Allowed direct overwrite on closed files without redundant confirmation dialogs (`OverwritePrompt = false`).
- **Pre-Flight File Lock Detection & Clear Actionable Modal Warnings**:
  - Added pre-flight file lock validation before entering async export pipelines in `ExportBothReportsAsync`, `ExportPptxAsync`, `ExportWordAsync`, and Google Drive upload (`UploadReportsToGoogleDrive` / `FormDriveUploadProgress.cs`).
  - Standardized user-friendly warning message identifying locked file(s) and guiding the user to close them before retrying.

## [1.4.3] - 2026-09-29 (Surgery SQL Cartesian Product Deduplication, Data Sanitization & Memory Leak Fixes)

### Fixed & Enhanced
- **Surgery/Procedure SQL Cartesian Product Fix (`EhcSurgeryDataEngine.cs`)**:
  - Fixed database 1-N Cartesian product multiplication where `tb_dm_pttt_canhthuc` lacked unique constraint on `dm_pttt_canhthucid` (e.g. `id=1` had 6 rows: 1 from Obstetrics "Rạch ngang eo tử cung lấy thai" and 5 placeholder "." rows).
  - Applied `DISTINCT ON (dm_pttt_canhthucid) ... ORDER BY dm_pttt_canhthucid, dm_pttt_canhthucdbid DESC` to guarantee exact 1-to-1 matching for all 20,818+ hospital surgeries.
  - Eliminated phantom procedure duplication (e.g., intussusception reduction for baby 26003471 now renders exactly 1 row instead of 6, and shift procedure count matches HIS exactly at 3 procedures / 4 total surgeries).
  - Sanitized placeholder `.` / `-` values in SQL and `MapReaderToSurgeryRecord`, rendering clean `-` in `dgvSurgeries_CellFormatting`.
- **Memory & Resource Leak Fixes (GDI+ Handles & Events)**:
  - Implemented recursive disposal of `Image`/`Bitmap` and custom thumbnail controls in `FormAttachPhotos.cs`.
  - Cached tab header `Font` and `SolidBrush` in `MainForm.cs` to prevent GDI handle exhaustion during `DrawItem`.
  - Cleaned up event listeners (`Parent.Resize`, `ParentChanged`) in `LoadingOverlayControl.cs`.
  - Removed duplicate `GetSurgeryRecords` query execution in `ReportGeneratorControl.cs`.

## [1.4.2] - 2026-09-28 (Emergency & Monitored Split UI, 2-Way Photo Sync, PPTX Option B Deduplication & App Icon)

### Added & Enhanced
- **Split-Screen Left Column (Chống Text Clipping)**:
  - Divided left column into Top-Bottom split (`Orientation = Horizontal`): Top panel for **Bệnh nhân mổ cấp cứu** (Crimson header), Bottom panel for **Bệnh nhân theo dõi / nặng** (Teal header).
  - Optimized grid sizing to completely prevent text clipping: `RowTemplate.Height = 28`, `ColumnHeadersHeight = 28`, column widths and `FillWeight` tuned with `MinimumWidth >= 90px`, `ShowCellToolTips = true`, and `AutoEllipsis` on toolbar buttons.
- **Manual Emergency Surgery Addition & KPI Synchronization**:
  - Enabled doctors to manually add emergency surgeries via `➕ Thêm ca mổ cấp cứu` using customizable `FormSelectMonitoredPatient` dialog.
  - Automatically synchronized KPI count `numMoCapCuu` and `CurrentData.Metrics.MoCapCuu` on patient addition and deletion, updating the balancing badge in real-time.
- **Two-Way Clinical Photo Synchronization**:
  - Implemented automatic two-way photo synchronization across Emergency Surgeries and Monitored Patients matching `MedicalRecordID`.
  - Adding or modifying photos in either list instantly syncs attached images to the corresponding patient in the other list.
- **Clean Monitored Patients Defaulting**:
  - Configured `data.MonitoredPatients` to default to an empty list (`new List<ReportPatientItem>()`) rather than duplicating surgical lists, giving medical staff complete control over tracked cases.
- **PowerPoint Presentation Option B (Slide Deduplication)**:
  - If a patient with photos is present in both Emergency Surgeries and Monitored Patients, photo slides are rendered once under Emergency Surgeries.
  - In the Monitored Patients section, redundant photo slides and table thumbnail jump links are omitted, keeping slides concise and clean.
- **App Icon Updated to Bar-Chart**:
  - Generated multi-resolution `ehc.ico` (16x16 up to 256x256) from `bar-chart.png` and embedded directly into executable resources via `build.ps1` (`/win32icon`) and WinForms `MainForm`.
- **Automated Verification**:
  - Created single comprehensive test file `tests/Test_EmergencyAndMonitoredPhotosSync.cs` validating all 5 features with 100% PASS rate.



### Fixed & Enhanced
- **FormAttachPhotos (Đính kèm ảnh lâm sàng)**:
  - Fixed bug where `btnSave` ("Lưu & Áp dụng") and `btnCancel` ("Đóng / Hủy") disappeared off-screen due to WinForms anchor recalculations prior to panel attachment.
  - Implemented dynamic `pnlBottom_Resize` calculating precise client bounds, added bottom hint label, wired `AcceptButton` (`Enter`) and `CancelButton` (`Esc`).
- **FormSelectMonitoredPatient (Chọn bệnh nhân theo dõi / nặng)**:
  - Replaced raw, plain text `ListBox` with dedicated modern medical dialog `FormSelectMonitoredPatient.cs`.
  - Added real-time instant search bar (`txtSearch`) filtering by Patient Name, Code, Room/Bed, Diagnosis, and Surgical Method.
  - Added category filter dropdown (`cboCategory`) separating Emergency Surgeries, Elective Surgeries, On-Demand Surgeries, Admissions, and Inpatients.
  - Implemented sleek `DoubleBufferedDataGridView` with color-coded classification badges, keyboard shortcuts (`Enter`/`Esc`), double-click row instant selection, and multi-selection support.

## [1.4.0] - 2026-09-28 (PowerPoint Fix, Privacy Compliance, Monitored Patients Seeding, Branding & Standalone Package)

### Added & Enhanced
- **PowerPoint Clean OpenXML Generation & 0-Repair Validation**:
  - Replaced presentation scaffold, theme XML, and slideMaster with standard OpenXML PresentationML schema containing full `p:txStyles` (`lvl1pPr`..`lvl9pPr`).
  - Fixed `a:rPr` sequence and `a:ln` styling inside `EhcPowerPointReportService.cs`.
  - Tested directly against Microsoft PowerPoint COM automation with 100% clean presentation opening (0 corruption/repair prompts across all 10 slides).
- **Patient Privacy Compliance (Address Removal in PPTX)**:
  - Completely omitted patient address columns from all generated PowerPoint slides.
  - Reallocated column widths to increase readable space for Patient Name, Diagnosis, and Surgical Method.
- **Monitored Patients Clinical Flow ("Bệnh nhân theo dõi / nặng")**:
  - Automatically pre-populated shift emergency surgeries and elective surgeries into `data.MonitoredPatients` with default clinical progress ("Hậu phẫu..., toàn trạng ổn định") and management directions ("Theo dõi sát toàn trạng, sinh hiệu, vết mổ...").
  - Added cache management in `ReportGeneratorControl.cs` to preserve edited monitored patient rows across shift cycle changes and refreshes.
- **Tab Defaulting & View Stability**:
  - Updated `SetShiftCycle`, `ApplySnapshot`, and `ApplyFilter` to keep and default to **BÁO CÁO GIAO BAN** (`ActiveViewMode.Report`) when changing duty cycle.
- **App Branding & Version 1.0**:
  - Rebranded application name to **BaocaoGiaoBan V1.0**.
  - Created `AssemblyInfo.cs` with `AssemblyTitle("BaocaoGiaoBan")`, `AssemblyProduct("BaocaoGiaoBan V1.0")`, `AssemblyFileVersion("1.0.0.0")`, and `AssemblyInformationalVersion("1.0")`.
  - Added bottom status strip indicator: **🔒 Lưu hành nội bộ** with distinct styling.
- **App Icon from `analytics.png`**:
  - Converted `analytics.png` to high-resolution 7-frame `ehc.ico` (16x16 up to 256x256).
  - Embedded icon into executable via `/win32icon` and applied to MainForm header and taskbar.
- **Standalone Deployable Package (`BaocaoGiaoBan_Standalone`)**:
  - Created standalone folder containing `BaocaoGiaoBan.exe`, `Sanita.Utility.dll`, `Sanita.Core.dll`, `Sanita.UI.dll`, `Dll\` (`Npgsql.dll`, `Mono.Security.dll`, `Newtonsoft.Json.dll`, `DevComponents.DotNetBar2.dll`, `Antlr3.Runtime.dll`, `ICSharpCode.SharpZipLib.dll`), `Data\` (`Settings.xml`, `GiaoBanSettings.json`), and assets.
  - Verified isolated execution with PostgreSQL database and anti-tamper compliance.
  - Updated `build.ps1` to automatically synchronize new builds into `BaocaoGiaoBan_Standalone`.

## [1.3.0] - 2026-09-28 (Dual-Tab Dashboard Architecture & Inpatient UI/UX Redesign)

### Added & Enhanced
- **Phase 01 - Models & Report Generator Refinements**:
  - Eliminated shift leader ("Lãnh đạo trực") from data models, Word export, PowerPoint slides, and UI inputs.
  - Cleared hardcoded sample staff names; configured free-text dropdowns with dynamic auto-suggest history persisted to `Data\GiaoBanSettings.json`.
  - Automated PostgreSQL recount on load, cycle selection, and refresh, removing manual "Đếm lại CSDL" button.
  - Standardized export file naming conventions: `BienBanGiaoBan_{dd-MM-yyyy}.doc` and `GiaoBan_{dd-MM-yyyy}.pptx`.
  - Optimized staff panel layout into clean 3-column structure with generous internal padding and zero text clipping.
- **Phase 02 - Header Navigation & Dual-Tab UI**:
  - Added 2 prominent segmented navigation buttons to fixed top header: `btnTabReport` ("📊 BÁO CÁO GIAO BAN") and `btnTabInpatients` ("📋 BỆNH NHÂN NỘI TRÚ") with high-contrast active cyan `#0891B2` and inactive deep navy `#083344` styling.
  - Set Shift Handover Report dashboard as the default startup view (`ActiveViewMode.Report`) docked 100% full-width.
  - Relocated live search box (`pnlSearch`) from top header into inpatient grid header (`pnlGridHeader`) with right alignment, keeping top header clean and focused.
  - Completely removed surgery view tab toggle (`pnlViewToggle` / `btnViewSurgeries`) from UI.
  - Added global keyboard shortcuts: `Ctrl+F` (switch to Inpatients and focus search), `Alt+B` (Report view), `Alt+N` (Inpatient view), `Esc` (clear search), `F5` (refresh).
- **Phase 03 - E2E Verification, Text Clipping Audit & Standalone Packaging**:
  - Extended `--verify` headless self-test suite in `Program.cs` to rigorously validate dual-tab architecture, default startup view, search relocation, filename generation, absence of shift lead & recount button, and multi-resolution text-clipping audit.
  - Multi-resolution layout audit across 1280x720, 1366x768, and 1920x1080 bounds asserting zero text truncation and `AutoEllipsis = true`.
  - Recompiled standalone executable `EHC_PediatricTool.exe` for x86 (485.5 KB <= 500 KB limit, zero external dependencies).
- **Tab Giao Ban Auto-Refresh & Balancing Badge Fix**:
  - Resolved KPI calculation cascade by guarding `SetMetrics()` with `_isUpdatingMetrics` flag, preventing intermediate false-alarm `"LỆCH 1 ca"` badges during data load.
  - Linked `btnRefresh` ("🔄 Làm mới (F5)") and `SetShiftCycle()` to reload `ReportGeneratorControl` whenever the active view is in Report mode or shift cycle changes.
  - Hooked `chkExcludeCombination.CheckedChanged` to trigger instant database recount without manual tab reloads.

## [1.2.0] - 2026-09-28 (Shift Handover Report Generator - PowerPoint 16:9 & Word A4 Export)

### Added & Enhanced
- **Phase 01 - Core Report Data Models & PostgreSQL Engine (`EhcReportDataEngine.cs`)**:
  - `ReportModels.cs`: Clinical data models including `ReportHandoverData`, `ReportShiftInfo`, `ReportMetricsItem`, `ReportPatientItem`, `ReportWeekendSummaryItem`, `ReportDisplayOptions`, `ReportSettings`, and `ReportImageAttachment`.
  - Non-locking read-only queries against PostgreSQL `bvsnbacninh` (`tb_medicalrecord`, `tb_patientrecord`, `tb_servicedata_pttt`).
  - Mathematical conservation check: $\text{HienCo} = \text{BnCu} + \text{Vao} - \text{Ra} - \text{ChuyenKhoa} - \text{ChuyenVien}$.
  - Single-roundtrip 3-day weekend census aggregation for Friday, Saturday, and Sunday shifts.
- **Phase 02 - Word Document Generator Service (`EhcWordReportService.cs`)**:
  - Standard Word MIME HTML (`application/msword;charset=utf-8`) export with UTF-8 BOM (`0xEF, 0xBB, 0xBF`) and Word XML namespaces without COM/Office dependencies.
  - Standard A4 portrait layout with Republic banner, hospital/department headers, shift members, two KPI balancing tables, weekend summary, categorized clinical tables, instructions/conclusions, and 3-column signature block (*Trưởng khoa, Bác sĩ trực, Điều dưỡng trực*).
  - Configurable privacy masking and address shortening.
- **Phase 03 - PowerPoint Presentation Generator Service (`EhcPowerPointReportService.cs`)**:
  - Pure OpenXML PresentationML generator producing widescreen 16:9 `.pptx` presentations packaged via `System.IO.Compression`.
  - Visual hierarchy adhering to hospital palette: `#0E3A54` (Navy), `#1C6EA4` (Blue), `#0F9A97` (Teal), `#F1F6FA` (Card surface).
  - Dynamic pagination for long lists (max 7 rows for standard, max 5 rows for monitored cases).
  - Two-way hyperlinked slide jumps (`ppaction://hlinksldjump`) between patient list rows and high-resolution clinical photo slides.
  - Image scaling preserving 100% original aspect ratio without distortion ($r = \min(W_f/W_i, H_f/H_i)$) with centered letterboxing.
- **Phase 04 - Modern UI Tab "Báo cáo giao ban", Photo Picker & UX Polish**:
  - `ReportGeneratorControl.cs`: Interactive 2-column split layout with zero vertical scrolling.
  - Staff duty banner with autocomplete ComboBoxes and persistent configuration stored in `Data\GiaoBanSettings.json`.
  - 10 interactive KPI cards with dynamic balancing pill badge (Emerald `#10B981` when balanced, Crimson `#EF4444` when unbalanced).
  - Monitored patient tracking grid with instant multi-field search filter (Name, Code, Room/Bed) and inline editing.
  - `FormAttachPhotos.cs`: Clinical photo picker supporting up to 4 photos per patient, high-quality Bicubic scaling and JPEG compression (max 1400px, 82% quality).
  - `MainForm.cs`: 3-way view navigation toggle (*Bệnh nhân nội trú*, *Phẫu thuật & Thủ thuật*, *Báo cáo giao ban*).
- **Phase 05 - Standalone Packaging & End-to-End Verification**:
  - Updated `Program.RunHeadlessVerification` (`--verify`) to comprehensively assert Steps 10, 11, 12, 13 (Report data engine, Word export, PowerPoint export, photo compression).
  - Standalone executable `EHC_PediatricTool.exe` compiled for x86 (475 KB <= 500 KB limit, zero installer dependencies).

## [1.1.3] - 2026-09-27 (UI Polish, Surgery Display Refinements & On-Demand Surgeries)

### Added & Enhanced
- **Department Lock & Static Teal Header**:
  - Replaced the dropdown ComboBox with a static Medical Teal badge (`lblDepartmentBadge`) locked to `"Khoa: Khoa Ngoại nhi (K18)"`, completely hiding and disabling department switching as requested.
- **End-of-Period Census ("Tồn cuối kỳ") Tree Node & Filter**:
  - Added census tree root node `"Tồn cuối kỳ (41)"` (`FILTER_TREE_END_PERIOD`) with child node `"Đang điều trị (41)"` (`FILTER_TREE_END_PERIOD_TREATING`) positioned between "Số lượng xuất" and "PHẪU THUẬT & THỦ THUẬT".
  - Synchronized selection in TreeView to filter the patient grid to the 41 active undischarged inpatients at shift period end.
- **Minor Procedure Status ("Thủ thuật") Disambiguation**:
  - Refactored `SurgeryRecordItem.TinhHinhName` and `FormatTinhHinh` to strictly return `"Thủ thuật"` for all bedside and operating procedures (`IsThuThuat == true`), regardless of database `TinhHinhID`.
- **Surgery Service Name & Method Fallback Resolution**:
  - Enhanced `EhcSurgeryDataEngine` SQL to `LEFT JOIN tb_service` (`s.servicename`) and `tb_dm_pttt_canhthuc` (`ct.dm_pttt_canhthucname`), resolving empty procedure names and surgical techniques.
- **Surgery Grid Column Trimming & "Đối tượng" Column**:
  - Hidden 3 redundant columns from `dgvSurgeries`: "Bắt đầu" (`colSurgeryNgayBatDau`), "Kết thúc" (`colSurgeryNgayKetThuc`), and "BS Gây mê" (`colSurgeryBsGayMe`).
  - Added new column **"Đối tượng"** (`colSurgeryDoiTuong`, 150px) bound to `DM_ServiceObjectName`.
  - Added Gold/Amber badge highlight (`BackColor: #FEF3C7`, `ForeColor: #B45309`, Bold) in `dgvSurgeries_CellFormatting` for on-demand cases.
- **On-Demand Surgery ("Mổ yêu cầu") Tracking**:
  - Joined `tb_servicedata` (`sd.dm_serviceobjectid`) and `tb_dm_serviceobject` (`so.dm_serviceobjectname`) via `pttt.servicedataid = sd.servicedataid`.
  - Added `IsMoYeuCau` property flagging on-demand surgeries (`DM_ServiceObjectID == 4` or `1`).
  - Added dedicated sub-node `"Mổ yêu cầu (X)"` (`FILTER_SURGERY_YEU_CAU`) under `"PHẪU THUẬT & THỦ THUẬT"` tree group with real-time count badge and grid filtering.
- **Shift Cycle Dialog Visual Polish (`FormSelectCycle`)**:
  - Removed duplicate radio button circle emoji prefixes: `"Ca trực hôm nay"`, `"Ca trực hôm qua"`, `"Tùy chỉnh khoảng thời gian"`.
  - Expanded dialog width to 560px and `grpPresets` width to 528px.
  - Repositioned `btnWeekendReport` to `Point(280, 28)`.
  - Configured description labels (`lblTodayDesc`, `lblYesterdayDesc`) with `AutoSize = true` to guarantee zero text truncation.
- **Headless Self-Verification & Packaging**:
  - Extended `Program.RunHeadlessVerification` (`--verify`) to comprehensively assert all 7 new clinical and UI requirements.
  - Recompiled standalone executable `EHC_PediatricTool.exe` (299.5 KB <= 500 KB constraint, zero installer dependencies).

## [1.1.2] - 2026-09-27 (Beginning Census & Patient Status Alignment)

### Fixed & Aligned
- **Beginning Census (Tồn đầu kỳ) Count & Universe Synchronization**:
  - Aligned beginning census to evaluate across the entire in-cycle patient population (`allPatientsInCycle`), correctly capturing active undischarged patients (28) plus in-cycle discharged patients (6) to total exactly **34 patients**.
  - Synchronized Top KPI Card `Carryover`, Left Sidebar TreeView root node `Tồn đầu kỳ (34)`, and right DataGridView filtering (`DANH SÁCH BỆNH NHÂN: Tồn đầu kỳ (34 bệnh nhân)`).
- **Inpatient Status Classification (Đang điều trị vs. Chờ nhập khoa)**:
  - Added 30-day cutoff (`mr.medicalrecorddate >= dtEnd.AddDays(-30)`) to `GetPendingAdmissions`, eliminating 6 stale/abandoned historical records from 2024-2025 and returning exactly **3 pending admission patients** matching hospital HIS.
  - Aligned "Đang điều trị" under "Bệnh nhân hiện diện trong kỳ" to include all patients treated in the ward during that cycle (41 active undischarged + 6 discharged = **47 patients**).
  - Maintained canonical hospital present census: Hiện diện: 50 (Đang điều trị: 47 + Chờ nhập khoa: 3).
- **Hospital Mathematical Conservation Laws**:
  - Validated census conservation equality across the canonical cycle `25/09 06:30` - `28/09 06:30`:
    $$\text{Tồn đầu kỳ (34)} + \text{Nhập trong kỳ (13)} - \text{Xuất trong kỳ (6)} = \text{Tồn cuối kỳ (41)}$$
  - Conservation of total present: Present (50) == Treating (47) + Pending (3).
  - Conservation of treated population: Treating (47) == Carryover (34) + Admitted (13).
- **Headless Self-Verification & Packaging**:
  - Updated `Program.RunHeadlessVerification` to comprehensively assert new census metrics and WinForms tree/grid synchronization under `--verify`.
  - Recompiled standalone executable `EHC_PediatricTool.exe` (280.5 KB, zero installer requirements).

## [1.1.1] - 2026-09-27 (Cycle Selector UI & Surgery Classification Optimization)

### Fixed & Enhanced
- **FormSelectCycle Button Visibility & Layout**:
  - Re-anchored `btnApply` and `btnCancel` within bottom control panel (`pnlBottom`), ensuring prominent visibility and preventing control clipping.
  - Set custom time pickers to default to shift handover hour (`06:30`) instead of current PC clock time.
- **Smart Weekend Report**:
  - Added "Báo cáo cuối tuần" button enabled exclusively on Sundays and Mondays, automatically selecting the custom shift window from Friday 06:30 to Monday 06:30.
- **Surgery & Procedure (PTTT) Classification**:
  - Corrected `SurgeryKpiMetrics.cs` and `EhcStatisticsTreeService.cs` so "Mổ phiên" (`FILTER_SURGERY_SCHEDULED`) strictly includes only major surgeries (`IsPhauThuat && TinhHinhID == 2`), completely excluding minor procedures (`IsThuThuat`).
  - Guaranteed conservation of total: Emergency (2) + Scheduled (2) + Procedures (4) = 8.
- **MainForm Dual-View Synchronization**:
  - Selecting "Mổ phiên" tree node filters surgery DataGridView to strictly display major elective surgeries, preventing procedures from appearing under elective surgery list.
- **Headless Self-Verification Suite**:
  - Extended `--verify` mode in `Program.cs` to automatically validate Smart Weekend Report eligibility, Friday-Monday window calculation, and canonical 8-record surgery partitioning.

## [1.1.0] - 2026-09-27 (Shift Cycle & Surgery Statistics Integration)

### Added
- **Phase 01 - Flexible Shift Cycle Range Engine**:
  - `ShiftCycleInfo.cs`: Dynamic model supporting Today, Yesterday, and Custom arbitrary shift cycles with editable base hour and minute (default 06:30, customizable to e.g. 07:00).
  - Backward-compatible overloads in `EhcPatientDataEngine.cs` for cycle range retrieval.
- **Phase 02 - Surgery & Procedure (PTTT) Data Engine**:
  - `EhcSurgeryDataEngine.cs` & `SurgeryRecordItem.cs`: High-performance read-only queries against `tb_servicedata_pttt`, joined with patient records, room/bed, and medical personnel (`tb_nhanvien`).
  - Robust multi-department association covering performing department, admitting department, and medical record department.
- **Phase 03 - Surgery KPI Metrics & Statistics Tree**:
  - `SurgeryKpiMetrics.cs`: Aggregated calculation of total, emergency vs. scheduled operations, major surgeries, and bedside procedures.
  - `EhcStatisticsTreeService.cs`: Surgical branch added to sidebar TreeView hierarchy (`FILTER_TREE_SURGERY`) with 4 synchronized sub-nodes (`SURGERY_ALL`, `SURGERY_EMERGENCY`, `SURGERY_SCHEDULED`, `SURGERY_PROCEDURE`).
- **Phase 04 - Modern UI Cycle Selector & Dual-View Grid**:
  - `FormSelectCycle.cs`: Intuitive modal dialog allowing shift preset selection and real-time shift hour adjustment.
  - `MainForm.cs` & `MainForm.Designer.cs`: Header shift cycle button with dropdown indicator, dual-view toggle buttons between Inpatients and Surgeries, 15-column high-density surgery data grid with color chips and context menu shortcuts.
- **Phase 05 - Standalone Packaging & End-to-End Verification**:
  - `Program.cs`: Extended `--verify` headless self-test suite covering anti-tamper, decryption, DB connection, shift cycle manipulation, surgery queries, dual-branch stats, and dual-grid WinForms lifecycle.
  - `build.ps1`: Registered all 5 new source files for x86 compilation.
  - `tests/Test_Cycle_Phase05_E2E.cs`: Comprehensive automated verification test confirming zero regressions.

## [1.0.1] - 2026-09-27 (UI Rendering & Text Clipping Fix)

### Fixed
- **KPI Card Text Clipping**:
  - Re-implemented `KpiSummaryCard.cs` with direct GDI+ ClearType rendering (`TextRenderer.DrawText`) in `OnPaint`, replacing nested Label docking to eliminate vertical clipping of numbers (16.5pt Bold) and subtitles.
  - Increased `pnlKpiDeck` height to 94px and individual KPI cards to 76px for comfortable vertical breathing room.
- **DataGridView Blank Canvas Fix**:
  - Removed incorrect `SetStyle(ControlStyles.UserPaint, false)` in `DoubleBufferedDataGridView.cs` that was preventing managed cell and row drawing.
- **Cycle Badge Layout**:
  - Expanded `lblCycleBadge` width to 276px to cleanly display the complete 43-character cycle interval.

## [1.0.0] - 2026-09-27 (Phase 01 - 05 Complete)

### Added
- **Phase 01 - Database & Encryption Service**:
  - `EhcDbService.cs`: Automatic decryption of `Data\Settings.xml` (`DB_USERPASSWORD` via `Decrypt_PRIVATE`).
  - Preloading of `Sanita.UI.dll` to satisfy `SanitaUtility.DLL_OK` anti-tamper check.
  - Assembly resolution hook for `Dll\` folder.
- **Phase 02 - Patient Data Engine**:
  - `EhcPatientDataEngine.cs`: Daily cycle range computation (06:30 - 06:30).
  - PostgreSQL query for Khoa Ngoại nhi (Dept 24, Code K18).
  - Demographic mapping and pediatric age formatting (< 60 days, < 72 months, >= 6 years).
  - Guardian contact information extraction (Father/Mother Name, Phone).
- **Phase 03 - Statistics Tree & KPI Calculator**:
  - `EhcStatisticsTreeService.cs`: In-memory calculation of top 6 KPI cards and sidebar navigation hierarchy.
  - Filter predicate engine (< 10 ms execution).
- **Phase 04 - Modern WinForms UI**:
  - `MainForm.cs` & `MainForm.Designer.cs`: Medical Teal header (`#0E7490`), high-DPI scaling.
  - `KpiSummaryCard.cs`: 6 interactive metric cards with count badges and hover effects.
  - `DoubleBufferedDataGridView.cs`: High-density flicker-free clinical table with context menu copy shortcuts.
  - Live instant search (`Ctrl+F`, `Esc`).
- **Phase 05 - Standalone Packaging & E2E Verification**:
  - `Program.cs`: Multi-mode entry point (interactive GUI + `--verify` headless self-test).
  - `EHC_PediatricTool.exe.config`: Private probing for `Dll\` and High-DPI PerMonitorV2 awareness.
  - `build.ps1`: Automated x86 build script with `csc.exe`.
  - Complete backup in `D:\skul9x\Report\EHC\Report_Tool`.
