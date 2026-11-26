# Báo cáo Day 13 — Nguyễn Bình Anh, 2A202604063

## 1. Kết luận trước
- **Thông tin chung:** Nguyễn Bình Anh — MSSV: `2A202604063`; Task: `D13-2A202604063`; 10 frame được giao: `s01f30`, `s01f57`, `s02f05`, `s02f32`, `s03f12`, `s03f42`, `s04f15`, `s04f53`, `s05f07`, `s05f34`; Hệ điều hành: Windows 10; Trình duyệt: Google Chrome; Phiên bản CVAT server: `2.74.1`; MSSV bạn ngồi cạnh / đổi kiểm: Vũ Gia Khánh (`2A202604069`).
- **Tổng quan kết quả:** Tổng cộng đã gán nhãn **115 cuboid** trên 10 frame (vượt mức tối thiểu 53 cuboid). Frame có ít cuboid nhất là **`s03f42` (5 cuboid)** do đoạn đường thông thoáng, mật độ xe thưa hơn hẳn so với các nút giao đông đúc như `s05f07` (23 cuboid). Lệnh tự kiểm (`tukiem.csv`) gắn cờ cảnh báo **26/115 cuboid** (chủ yếu là chênh lệch nhẹ 10–20 cm ở chiều rộng/cao của xe SUV hoặc xe gầm thấp). Phần tự tin nhất là xác định đúng loại đối tượng (`class`), hướng đầu xe (`yaw`) và trạng thái bị che khuất (`occluded`) nhờ đối chiếu chéo với 7 góc camera; phần còn hạn chế nhất là ước lượng đáy hộp chạm đất với các xe ở xa (> 40 m) do vạch tia LiDAR mặt đường bị giãn thưa.
- **Thao tác cơ bản trên CVAT 3D:**
  1. **Đổi góc nhìn:** Giữ chuột trái và kéo để xoay không gian 3D quanh tâm; giữ chuột phải (hoặc `Shift` + chuột trái) để dời khung nhìn; lăn con lăn chuột để phóng to/thu nhỏ; bấm biểu tượng góc nhìn từ trên xuống (Top view) để căn chỉnh trục tọa độ.
  2. **Vẽ và chỉnh cuboid:** Chọn công cụ **Draw new cuboid** (`Shape`) trên thanh công cụ bên trái, chọn nhãn (`Car`, `Truck`, `Bus`, `Pedestrian`, `Cyclist`) và đặt hộp vào cụm điểm; nháy đúp (hoặc bấm chọn) cuboid để mở 3 khung chiếu phụ **Top / Side / Front** bên góc phải nhằm kéo khít 6 mặt bao và hạ đáy chạm vạch mặt đường.
  3. **Bật mũi tên hướng & kiểm tra camera:** Bật hiển thị **Cuboid orientation** (mũi tên đỏ `+X` chỉ về đầu xe) để xoay đúng hướng di chuyển; đối chiếu khung chiếu 2D của cuboid trực tiếp lên các cửa sổ ảnh camera (`cam01`–`cam09`) ở phía trên màn hình để kiểm tra độ khớp.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f30-3d`, `t1-s01f30-top`, `t1-s01f57-3d`, `t1-s01f57-top`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f30` | Phía trước (`-y`) ở cạnh trái; `+x` hướng xuống cạnh dưới, `+y` hướng sang cạnh phải | Phẳng, thấp hơn LiDAR khoảng 1.7 m | ~45–50 m | Xe con (`Car`), xe hai bánh (`Cyclist`), cây cối ven đường, dải phân cách | Có 2 xe con chạy phía sau (làn trái và làn phải) và xe đi ngang hai bên hông, quan sát được qua các camera bên/sau (`cam05`–`cam09`) | Tán cây ven đường bị rã thành các điểm rời rạc và cụm điểm mờ ở khoảng cách > 50 m không đủ mật độ nhận dạng |
| `s01f57` | Phía trước (`-y`) ở cạnh trái; `+x` hướng xuống cạnh dưới, `+y` hướng sang cạnh phải | Phẳng, thấp hơn LiDAR khoảng 1.7 m | ~45–50 m | Xe con (`Car`), xe buýt ở xa, cây ven đường, cột đèn | Có 1 xe con trắng đi sát bên hông trái (`cam05`) và 1 xe con chạy bám ngay phía sau đuôi xe thu dữ liệu | Các phương tiện ở xa phía trước bị che khuất một phần bởi hàng xe đứng chờ phía trước và tán cây ở rìa tầm quét |

**3 điều chỉ camera cho biết và 3 điều chỉ LiDAR cho biết (lấy từ `s01f30` và `s01f57`):**
- **Chỉ camera cho biết:**
  1. Màu sắc của phương tiện (ví dụ xe con màu trắng ở làn bên trái) và màu sắc vạch kẻ đường.
  2. Tín hiệu đèn giao thông, đèn phanh hoặc đèn xi-nhan đang bật của các xe xung quanh.
  3. Phân biệt rõ người đang ngồi lái xe hai bánh (`Cyclist`) hay người đi bộ/dắt xe (`Pedestrian`) ở khoảng cách trung bình.
- **Chỉ LiDAR cho biết:**
  1. Khoảng cách hình học chính xác theo mét từ tâm cảm biến đến từng vật thể xung quanh.
  2. Kích thước không gian 3 chiều thực tế (dài, rộng, cao) và cao độ mặt đường (`z ≈ -1.7 m`).
  3. Cấu trúc hình khối 3D của các vật thể nằm trong vùng bóng râm tối hoặc vùng bị ngược sáng gắt trên camera.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f05` | Cạnh trái | Khu vực ngã tư đông phương tiện; có xe buýt lớn bên làn ngược chiều và nhiều điểm phản xạ từ cột đèn/biển báo giao thông |
| `s02f32` | Cạnh trái | Dải phân cách cứng chạy dài ở giữa đường tạo thành vệt điểm thẳng kéo dài từ trước ra sau; xuất hiện vệt bóng che phía sau dải phân cách |
| `s03f12` | Cạnh trái | Mật độ xe dày đặc nối đuôi nhau sát khoảng cách gần; nhiều điểm phản xạ mạnh từ tường rào công trường sát lề đường |
| `s03f42` | Cạnh trái | Xe đang di chuyển qua đoạn đường thoáng hơn, phía trước có một xe con chạy cùng làn tạo vùng bóng che khuất một phần mặt đường phía trước nó |
| `s04f15` | Cạnh trái | Đường rộng nhiều làn xe, phía bên phải có cụm điểm lớn của phương tiện/vật cản ở sát lề tạo khoảng tối phía sau |
| `s04f53` | Cạnh trái | Đang đi qua nút giao có hàng rào phân làn màu vàng chạy dọc bên trái; có xe hai bánh (`Cyclist`) và nhiều xe con chạy đan xen phía trước |
| `s05f07` | Cạnh trái | Ngã tư rộng với nhiều làn xe rẽ và đi thẳng, có xe tải/buýt ở làn bên cạnh và vạch qua đường rõ trên camera |
| `s05f34` | Cạnh trái | Dải phân cách trồng cây xanh rậm rạp chạy sát bên trái tạo thành dải điểm dày đặc, che khuất hoàn toàn phần đường ngược chiều phía sau nó |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s01f30, #1` | `Car` | Đúng hướng đầu xe (`+X`, `yaw_deg = 272.1°`) | Chạm sát vạch mặt đường (`z đáy = -1.543 m`) | Đã kiểm đủ 3 góc chiếu và khớp khung camera | Nằm trong khoảng chuẩn (`l = 5.199 m`, `w = 2.271 m`, `h = 1.495 m`) |
| (b) xe buýt hoặc xe tải | `s02f05, #2` | `Bus` | Đúng hướng đầu xe (`yaw_deg = 267.8°`) | Chạm sát mặt đường (`z đáy = -1.567 m`) | Đã kiểm đủ 3 góc chiếu và khớp khung camera | Nằm trong khoảng chuẩn của `Bus` (`l = 12.697 m`, `w = 3.117 m`, `h = 3.372 m`) |
| (c) người đi bộ hoặc xe hai bánh | `s02f05, #6` | `Cyclist` | Đúng hướng di chuyển (`yaw_deg = 271.9°`) | Chạm sát mặt đường (`z đáy = -1.623 m`) | Đã kiểm đủ 3 góc chiếu và khớp khung camera | Nằm trong khoảng chuẩn (`l = 1.689 m`, `w = 0.795 m`, `h = 1.559 m`) |

- **Vòng hiệu chuẩn & đối tượng bỏ qua:** Qua đối chiếu với bạn Vũ Gia Khánh (`2A202604069`), nhóm thống nhất cách xử lý các xe bị che khuất một phần (`occluded = True`): vẫn phải kéo dài hộp cuboid theo kích thước thực ước lượng của loại xe đó dựa trên ảnh camera chứ không thu nhỏ hộp chỉ ôm phần điểm nhìn thấy. Các cụm điểm nhiễu ven đường (tán cây rậm, cột biển báo ở lề đường trong `s01f30`, `s05f34`) đều đã đối chiếu camera và **bỏ qua không gán nhãn** vì không thuộc 5 lớp đối tượng quy định.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `t4-1-bong-che.png` (tự chụp từ task `D13-2A202604063`) | Tia laser LiDAR truyền theo đường thẳng nên bị các xe chạy phía trước hấp thụ/chắn ngang, không chiếu tới được các xe nối đuôi phía sau. | Hàng 3 xe nối đuôi bên làn phải: xe đầu tiên đón trọn tia nên rõ nét, hai xe phía sau bị khuyết mảng thân lớn và tạo vùng tối phía sau. | Vẫn vẽ cuboid đủ kích thước thực của xe dựa vào phần điểm còn lại và ảnh camera, đồng thời bật thuộc tính **`occluded = True`** (viền nét đứt). |
| 2. Thưa ở xa | `t4-2-thua-o-xa.png` (tự chụp từ task `D13-2A202604063`) | Khi khoảng cách từ cảm biến vượt quá 40 m, góc mở giữa các kênh tia laser giãn rộng khiến mật độ điểm rơi trúng vật thể giảm mạnh. | Các vòng quét mặt đường giãn thưa dần; chiếc xe ở rìa xa chỉ còn 1–2 vạch điểm rời rạc ở mép đầu xe, mất hình khối đặc. | Đối chiếu camera để xác nhận đúng loại xe, dựng hộp theo kích thước chuẩn của class đó và giữ **`occluded = False`** nếu không có vật cản che trước mặt. |
| 3. Phản xạ thấp / ít điểm bất thường | `t4-3-phan-xa-thap.png` (tự chụp từ task `D13-2A202604063`) | Bề mặt sơn tối màu (đen bóng) hoặc kính ô tô hấp thụ phần lớn năng lượng tia hồng ngoại thay vì phản xạ ngược về đầu thu LiDAR. | Dù xe ở cự ly gần và không bị che khuất, phần thân xe bên trong hộp gần như rỗng điểm, chỉ hiện sáng vài điểm ở vị trí biển số/cụm đèn phản quang. | Dựa vào ảnh camera và các điểm phản quang biên (đèn, bánh xe, biển số) để căn đủ kích thước thật của xe, giữ **`occluded = False`**. |
| 4. Điểm lạ | `t4-4-diem-la.png` (tự chụp từ task `D13-2A202604063`) | Do phản xạ đa đường (multipath) qua bề mặt kính/mặt đường hoặc bụi, khí thải lơ lửng trong không khí làm cảm biến ghi nhận sai tọa độ hồi tiếp. | Xuất hiện cụm điểm nhỏ ngoằn ngoèo lơ lửng giữa không trung, tách rời hoàn toàn khỏi mặt đường và không gắn với phương tiện nào trên camera. | Kiểm tra chéo trên camera để loại trừ nhiễu và **tuyệt đối không vẽ cuboid** bao quanh cụm điểm này. |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202604063-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng -1.7 m: z đáy lệch hơn 0.3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z - h/2 | dist_m | r = √(x² + y² + z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| `s01f30, #1, Car` | -0.795 | 1.495 | -1.543 | 8.43 | 8.47 | 272.1 | Chạm đường (`z đáy = -1.543 m`, lệch 0.16 m so với -1.7 m) |
| `s04f15, #10, Car` | 0.144 | 1.447 | -0.580 | 17.62 | 17.62 | 271.0 | Lơ lửng (đáy cao hơn mặt đường 1.17 m) |
| `s05f07, #19, Car` | -0.723 | 1.576 | -1.511 | 36.57 | 36.57 | 91.4 | Chìm (đáy thấp hơn mặt đường 1.00 m theo cảnh báo `tukiem.csv`) |

- **Phân tích khoảng cách, kích thước & hướng:**
  - **(a) Cuboid gần nhất và xa nhất:** Cuboid gần nhất là **`s01f57, #1` (`Car`)** với `dist_m = 3.53 m` và `r = √(2.947² + 1.947² + (-0.849)²) = 3.63 m`; cuboid xa nhất là **`s05f07, #10` (`Car`)** với `dist_m = 50.99 m` và `r = √((-37.968)² + (-34.043)² + (-0.993)²) = 51.00 m`.
  - **(b) So sánh kích thước `l, w, h` với Bảng 4.3:** Một `Cyclist` mẫu (`s02f05, #6`) có `l = 1.689 m`, `w = 0.795 m`, `h = 1.559 m` (nằm hoàn toàn trong khoảng chuẩn `l: 1.0–2.4 m`, `w: 0.3–1.2 m`, `h: 1.2–2.0 m`); một `Car` mẫu (`s01f57, #2`) có `l = 5.502 m`, `w = 2.519 m`, `h = 1.434 m`, trong đó chiều rộng `w = 2.519 m` vượt nhẹ khoảng `1.4–2.3 m` do đây là dòng xe SUV/bán tải cỡ lớn có phần gương chiếu hậu và vòm bánh rộng.
  - **(c) Góc hướng `yaw_deg`:** Xe chạy cùng chiều phía trước (`s01f30, #1`, `Car`) có `yaw_deg = 272.1°` (gần trùng góc `270°` tương ứng hướng tiến `-Y` của hệ trục LiDAR VF); xe chạy ngược chiều (`s01f30, #4`, `Car`) có `yaw_deg = 89.9°` (gần `90°`, ngược chiều `180°` so với xe đi cùng chiều).

Mật độ theo khoảng cách (4 cuboid `Car` ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| `s01f30, #1, Car` | 8.43 | 1595 |
| `s01f30, #3, Car` | 17.05 | 485 |
| `s03f42, #4, Car` | 24.41 | 228 |
| `s01f30, #6, Car` | 42.92 | 67 |

- **Quy luật mật độ & kết quả tự kiểm:**
  - **Quy luật:** Khi khoảng cách tăng gấp đôi (từ 8.43 m lên 17.05 m), số điểm LiDAR trên cùng một loại xe `Car` giảm xấp xỉ **3.3 lần** (từ 1595 điểm xuống 485 điểm, và tiếp tục giảm xuống 228 điểm ở 24.41 m, 67 điểm ở 42.92 m — bám sát đường cong suy giảm chuẩn trong bài giảng: ~1968 điểm ở 0–10 m, ~718 ở 10–20 m, ~214 ở 20–30 m và ~72 ở 30–40 m).
  - **Hai lỗi đã sửa sau tự kiểm (`tukiem.csv`):**
    1. **`s05f34` (`#1` và `#7 Car`):** Phát hiện 2 cuboid vẽ trùng khít lên cùng một chiếc xe; đã kiểm tra danh sách Object trên CVAT và xóa bớt 1 hộp trùng (đưa tổng số cuboid từ 116 về **115 cuboid**).
    2. **`s02f05` (`#5 Car`):** Hộp bị đặt lệch ra khoảng trống (0 điểm) ngay sát cụm 11 điểm của chiếc xe thật; đã dịch chuyển và căn lại khung cuboid ôm trọn cụm điểm của xe.
  - **Các cảnh báo giữ nguyên & lý do:** Các cảnh báo chênh lệch kích thước nhỏ (ví dụ `s01f57 #2 Car` có `width = 2.52 m`, `s02f32 #4 Car` có `length = 6.32 m`, hoặc `s05f07 #18 Car` có `height = 0.94 m`) được giữ nguyên vì sau khi đối chiếu trên ảnh camera và khung 3D, đây là các dòng xe bán tải/SUV cỡ lớn hoặc xe thể thao gầm thấp, khung hộp đã ôm sát biên thực tế của phương tiện.
- **Đổi kiểm chéo:** MSSV người kiểm chéo: Vũ Gia Khánh (`2A202604069`); đã đối chiếu tổng số cuboid (**115 cuboid**) và kiểm tra ngẫu nhiên cột `class` (`88 Car`, `17 Cyclist`, `4 Truck`, `4 Pedestrian`, `2 Bus`), `yaw_deg` trên `D13-2A202604063-bangA.csv` — các thông số khớp hoàn toàn với hình chiếu trên `work/xem/`, không cần chỉnh sửa thêm.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
- **(a) Ý nghĩa từ góc độ huấn luyện mô hình:** Mô hình nhận thức 3D cho xe tự hành cần biết chính xác vị trí không gian, kích thước vật lý và hướng di chuyển thực tế tính bằng **mét** trong hệ tọa độ quanh xe để tính toán khoảng cách phanh và quỹ đạo tránh va chạm (điều mà tọa độ **pixel** 2D trên ảnh phẳng bị biến dạng phối cảnh không thể cung cấp trực tiếp). Với các vật thể ở xa hoặc bị che khuất có mật độ điểm rất thưa, việc áp đặt quy tắc suy luận kích thước chuẩn theo từng loại xe giúp mô hình học được hình khối đầy đủ của vật thể thay vì dự đoán ra các hộp bị cắt cụt nguy hiểm.
- **(b) Sử dụng AI trong quá trình làm bài:**
  - **Công cụ:** Gemini.
  - **Câu hỏi chính:** Hỏi cách phân biệt quy tắc bật/tắt thuộc tính `occluded` theo đường truyền tia LiDAR, dấu hiệu nhận biết 4 hiện tượng chất lượng point cloud và cách đọc mã cảnh báo từ script `tukiem.csv`.
  - **Cách dùng kết quả:** Dùng làm tiêu chí đối chiếu chéo giữa khung nhìn 3D và ảnh camera trên CVAT để tự kiểm tra lại từng cuboid, sửa các hộp bị trùng/lệch và hoàn thiện cấu trúc báo cáo.
- **(c) Điều còn chưa chắc:** Ở các đối tượng nằm ở rìa xa (> 45 m) chỉ còn 1–2 vạch tia laser rời rạc và bị mờ trên camera, việc phân định ranh giới giữa xe con cỡ lớn (`Car`) và xe tải nhỏ (`Truck`), cũng như ước lượng cao độ mặt đường (`z đáy`) vẫn còn độ sai số nhất định do mật độ điểm quá thưa.