# 📚 MỤC LỤC TỔNG HỢP TÀI LIỆU — Day 13 Lab

**Tên bài:** Đọc point cloud VF và vẽ cuboid 3D đầu tiên  
**Loại:** Cá nhân · 10 frame LiDAR + 7 camera · 4 giờ · Python 3.11+ · CVAT 3D v2.83.0  
**Deadline:** 23:59 GMT+7 ngày làm lab

---

## 📌 MỤC LỤC NHANH

| Phần | File | Dùng khi... |
|------|------|-----------|
| **README** | [README.md](README.md) | Muốn tổng quan nhanh về bài lab |
| **GUIDE (5 bước)** | [GUIDE.md](GUIDE.md) | Làm từng bước: chuẩn bị, đọc frame, vẽ cuboid, tìm hiện tượng, nộp |
| **RULES (Quy tắc)** | [RULES.md](RULES.md) | Quy tắc làm bài, giữ dữ liệu, dùng AI, tính trung thực |
| **SUBMISSION (Nộp)** | [SUBMISSION.md](SUBMISSION.md) | Nộp bài: tên repo, deadline, các bước trước nộp |
| **CHECKPOINTS (Mốc giờ)** | [CHECKPOINTS.md](CHECKPOINTS.md) | Theo dõi tiến độ trong 4 giờ buổi học |
| **RUBRIC (Chấm điểm)** | [RUBRIC.md](RUBRIC.md) | Tiêu chí chấm điểm từng phần |
| **docs/GUIDELINE** | [docs/GUIDELINE.md](docs/GUIDELINE.md) | 5 class, bảng 4.3 kích thước, quy tắc cuboid |
| **docs/COORDINATES** | [docs/COORDINATES.md](docs/COORDINATES.md) | Hệ tọa độ VF, 7 camera, 7 tham số |
| **docs/SELF_CHECK** | [docs/SELF_CHECK.md](docs/SELF_CHECK.md) | Đọc `preview`, hiểu cờ của `selfcheck` |
| **docs/PHENOMENA** | [docs/PHENOMENA.md](docs/PHENOMENA.md) | 4 hiện tượng (bóng che, thưa, phản xạ thấp, điểm lạ) |
| **docs/COMMON_ERRORS** | [docs/COMMON_ERRORS.md](docs/COMMON_ERRORS.md) | Lỗi hay gặp và cách gỡ |
| **docs/COMMANDS** | [docs/COMMANDS.md](docs/COMMANDS.md) | Mọi lệnh Python, Windows, macOS |
| **docs/SETUP** | [docs/SETUP.md](docs/SETUP.md) | Cài đặt Git, Python, môi trường, tải dữ liệu |
| **docs/CVAT_SETUP** | [docs/CVAT_SETUP.md](docs/CVAT_SETUP.md) | Cài CVAT, tạo task, gợi ý giao diện |

---

## 🎯 REALME (5 BỨC & TIMELINE)

### **Bước 1: Chuẩn bị (~15')**
**Làm trước buổi học**
- Chọn **Use this template** trên GitHub → Tạo repo public theo tên quy định
- Clone repo → Cài `.venv`, dependencies
- Cài Docker, CVAT trên máy
- Tải dữ liệu VF (file `.bin` và ảnh camera)
- Điền `configs/lab.ini` → Chạy `python scripts/lab.py dry`
- **Kết quả:** 10 frame, mỗi frame camera=7

**Lệnh:** [docs/SETUP.md](docs/SETUP.md) · [docs/CVAT_SETUP.md](docs/CVAT_SETUP.md) · [docs/COMMANDS.md](docs/COMMANDS.md)

### **Bước 2: Đọc frame (~25')**
**Trên 2 frame đầu (kỹ), 8 frame còn lại (lướt)**

Năm bước đọc mỗi frame:
1. Gốc & trục (x trái, y sau, z lên; phía trước−y ở cạnh nào?)
2. Mặt đất (phẳng/dốc, thấp hơn LiDAR ~m)
3. Tầm & góc nhìn (vật xa nhất, hướng không camera)
4. Ảnh camera (3 điều chỉ camera, 3 điều chỉ LiDAR)
5. Quét cảnh (loại vật, cụm chưa đọc được)

**Sản phẩm:** 4 ảnh (2 frame × 2 ảnh), báo cáo §2  
**Hướng dẫn:** [GUIDE.md](GUIDE.md) Bước 2 · [docs/COORDINATES.md](docs/COORDINATES.md)

### **Bước 3: Vẽ cuboid (~90' + nghỉ)**

**3a. Vòng hiệu chuẩn (~6')**
- Chọn 3 vật trong task chung `D13-HC485087`
- Vẽ → Save → `python scripts/lab.py report --code HC485087`
- Gửi file bảng A cho coach → Chốt cách hiểu guideline

**3b. Frame 1 của bạn (~35')**
- Vẽ 3 cuboid mẫu: (a) xe con gần, (b) xe buýt/tải, (c) người/xe 2 bánh
- Vẽ tiếp vật khác tới 8–10 cuboid
- **Checklist mỗi cuboid:** class đúng, bao điểm, mũi tên hướng, đáy chạm đường, Top/Side/Front đầy đủ, kích thước Bảng 4.3, occluded

**3c. Frame 2–10 (~55')**
- Mỗi frame 6 phút, ít nhất 5 cuboid/frame
- Ưu tiên vật gần (<30m), rõ điểm
- Tối đa 50m (ngang)
- Save, chạy `python scripts/lab.py report` kiểm số hộp

**Sản phẩm:** 60–70 cuboid, 3 ảnh mẫu, báo cáo §3  
**Hướng dẫn:** [GUIDE.md](GUIDE.md) Bước 3 · [docs/GUIDELINE.md](docs/GUIDELINE.md)

### **Bước 4: Tìm bốn hiện tượng (~25')**

| # | Hiện tượng | Từ guideline |
|---|-----------|-----------|
| 1 | **Bóng che** | Bật occluded, bao phần suy luận |
| 2 | **Thưa ở xa** | Vẽ nếu ≥15–20% thể tích; nếu không → ghi LOW_VISIBILITY |
| 3 | **Phản xạ thấp / ít điểm bất thường** | Ghi giả thuyết, không khẳng định |
| 4 | **Điểm lạ** | Không gán thành vật |

**Nguồn frame:** 10 frame bạn trước, hoặc task chung, hoặc mượn bạn kế bên (ghi rõ)

**Sản phẩm:** 4 ảnh, báo cáo §4  
**Hướng dẫn:** [docs/PHENOMENA.md](docs/PHENOMENA.md) · [GUIDE.md](GUIDE.md) Bước 4

### **Bước 5: Nộp bài (~30')**

1. **Save** trong CVAT
2. `python scripts/lab.py sync` → Xuất bảng A, phiếu tự kiểm, preview
3. Mở ảnh `work/xem/` → **Sửa ít nhất 2 cuboid** → Save → `sync` lại
4. Điền báo cáo §5: z_bottom, kích thước, yaw_deg, mật độ điểm
5. Điền báo cáo §6: AI, lỗi phát hiện sau
6. **Đổi kiểm:** 2 con số với bạn kế bên
7. `python scripts/lab.py pack` → Nén ảnh zip
8. `python scripts/check_submission.py` → Kiểm lỗi
9. **Commit, push** → Nộp **link repo + ZIP** lên LMS trước **23:59 GMT+7**

**Lệnh:** [docs/COMMANDS.md](docs/COMMANDS.md) · [SUBMISSION.md](SUBMISSION.md)

---

## 📋 GUIDE (Hướng dẫn 5 bước)

👉 **Đầy đủ:** [GUIDE.md](GUIDE.md)

**Tóm tắt:**
- Bước 1: `python scripts/lab.py dry` → `python scripts/lab.py task` → Mở CVAT, kiểm 10 frame
- Bước 2: Đọc 2 frame kỹ, 8 frame lướt → Chụp 4 ảnh → Điền báo cáo §2
- Bước 3: Vòng hiệu chuẩn → Vẽ 3 mẫu + frame 1 → Vẽ frame 2–10
- Bước 4: Tìm 4 hiện tượng → Chụp 4 ảnh
- Bước 5: `sync` → Sửa hộp → Điền báo cáo → `pack` → `check` → Commit, push, nộp

**Mốc giờ:** [CHECKPOINTS.md](CHECKPOINTS.md)

---

## ⚖️ RULES (Quy tắc bắt buộc)

👉 **Đầy đủ:** [RULES.md](RULES.md)

**Hình thức & trung thực:**
- Bài **cá nhân:** bạn vẽ hết, không vẽ hộ hay chép từ người khác
- **Không chỉnh tay** bảng A, phiếu tự kiểm (nếu sửa → sửa trên CVAT → `sync` lại)
- Vật không đủ cơ sở → **bỏ qua & ghi vào báo cáo** (không đoán)
- Các bạn vẽ giống nhau = **nhất quán**, chưa chắc **đúng**

**AI & dữ liệu:**
- Được hỏi AI: lệnh Python, PowerShell, Terminal, Docker, cách CVAT, khái niệm (yaw, BEV, intensity)
- **Không được:** đưa ảnh camera, `data-VF/`, ảnh chụp, các file dữ liệu cho AI
- **Không được:** nhờ AI vẽ cuboid, viết nhận xét, đọc bảng A
- Ghi **3 dòng** trong báo cáo §6 nếu dùng AI (công cụ, câu hỏi, cách dùng) hoặc ghi "không dùng"

**Git & nộp:**
- Commit nhỏ, thông điệp rõ
- Không commit `work/`, `.venv/`, `data-VF/`, ảnh trong báo cáo (`.gitignore` đã chặn)
- **Deadline:** 23:59 GMT+7 ngày làm lab
- **Nộp muộn:** −10% mỗi ngày, quá 3 ngày không nhận

**Dữ liệu & bảo mật:**
- Dữ liệu VF **chỉ dùng trong lớp**
- Repo **public** (báo cáo, bảng A, phiếu tự kiểm)
- **Không ghi:** biển số, tên đường, địa danh, tên người cụ thể (ghi MSSV thay)
- Không commit ảnh camera, point cloud, ZIP dữ liệu

---

## 📤 SUBMISSION (Nộp bài)

👉 **Đầy đủ:** [SUBMISSION.md](SUBMISSION.md)

**Tên repo:** `K5-L2-DAY13-NguyenBinhAnh-2A202604063-PointCloudCuboid`  
**Deadline:** 23:59 GMT+7 ngày làm lab  

**Trước nộp:**
1. Điền README: `# Day 13 Lab — 2A202604063 - Nguyễn Bình Anh`
2. Đổi tên báo cáo: `report/individual/2A202604063_NguyenBinhAnh.md`
3. Điền đủ báo cáo (xóa TODO)
4. `python scripts/lab.py pack` → `work/K5-L23-DAY13-2A202604063-images.zip`
5. `python scripts/check_submission.py` → Sửa **LỖI**
6. `git add -A` → `git commit -m "..."` → `git push`
7. Kiểm repo có: báo cáo, bảng A, phiếu tự kiểm (không ảnh)
8. Nộp lên LMS: **link repo** + **ZIP ảnh**

**Cấu trúc nộp:**
```
README.md                      # tiêu đề có MSSV, họ tên
configs/lab.ini                # student, cvat_user (không mật khẩu)
submission/
├── D13-2A202604063-bangA.csv      # 7 tham số từng cuboid
└── D13-2A202604063-tukiem.csv     # điểm, khoảng hở, cảnh báo
report/individual/
├── 2A202604063_NguyenBinhAnh.md    # báo cáo 6 mục
└── images/                         # 11 ảnh (chỉ trong ZIP LMS)
    ├── t1-s01f30-3d.png            # 4 ảnh Bước 2 (2 frame × 2)
    ├── t1-s01f30-top.png
    ├── t1-s01f57-3d.png
    ├── t1-s01f57-top.png
    ├── t2-a-car.png                # 3 ảnh Bước 3 (3 mẫu)
    ├── t2-b-bus-truck.png
    ├── t2-c-ped-cyclist.png
    ├── t4-1-bong-che.png           # 4 ảnh Bước 4 (4 hiện tượng)
    ├── t4-2-thua-o-xa.png
    ├── t4-3-phan-xa-thap.png
    └── t4-4-diem-la.png
```

**Sửa sau nộp (trước deadline):**
- Sửa cuboid CVAT → Save → `sync` → Cập nhật báo cáo → `check` → Commit, push
- Thêm/đổi ảnh → `pack` → `check` → Nộp lại ZIP LMS

---

## 📊 RUBRIC (Chấm điểm)

👉 **Đầy đủ:** [RUBRIC.md](RUBRIC.md)

**Tổng:** 100 điểm = 60 (vẽ) + 40 (báo cáo)

| Phần | Điểm | Chi tiết |
|------|------|---------|
| **Vẽ (Bảng A)** | **60** | Số lượng (53–70+), khoảng cách, kích thước, hướng, đáy |
| Số cuboid | 20 | ≥53 (đủ), 60+ (tốt), 70+ (xuất sắc) |
| Khoảng cách | 5 | Chủ yếu ≤30m (ưu tiên), có ≤50m |
| Kích thước | 20 | So Bảng 4.3, sai số Car/Bus/Truck ±5%, Pedestrian/Cyclist ±10% |
| Hướng (yaw) | 10 | Xe cùng chiều ~270°, ngược ~90°, người theo hướng đi/mặt |
| Đáy (z_bottom) | 5 | z − h/2 ≈ −1,7 m, lệch >0,3m bị trừ |
| **Báo cáo (§1–6)** | **40** | Trung thực, lỗi phát hiện, không guessing |
| §1 Kết luận trước | 2 | MSSV, task, 10 frame, OS, CVAT version, tổng cuboid, frame ít |
| §2 Đọc frame | 8 | Trục, mặt đất, tầm, camera vs LiDAR (mỗi 2 điểm) |
| §3 Ba cuboid mẫu | 8 | Ba hộp, hướng, đáy, kích thước (mỗi 2 điểm) |
| §4 Bốn hiện tượng | 8 | 4 hiện tượng, nguyên nhân, point cloud, hệ quả gán nhãn |
| §5 Số đo & kiểm | 10 | z_bottom, dist_m, r, kích thước, yaw, mật độ điểm, quy luật |
| §6 Ý nghĩa & AI | 4 | Vì sao 3D (mét), vì sao vật thưa, AI (nếu có), lỗi sau |

---

## 📐 GUIDELINE (5 class, Bảng 4.3, Quy tắc cuboid)

👉 **Đầy đủ:** [docs/GUIDELINE.md](docs/GUIDELINE.md) · [3D_Cuboid_Annotation_Guideline.docx](docs/3D_Cuboid_Annotation_Guideline.docx)

### **5 Class (không thêm, không đổi tên)**

| Class | Gồm | Không gồm |
|-------|-----|-----------|
| **Car** | Sedan, hatchback, wagon, van, minivan, SUV, jeep, taxi, cấp cứu (thân xe con) | Bán tải, xe chở hàng → Truck |
| **Truck** | Bán tải, xe tải, xe ben, đầu kéo, van chở hàng, xe công trình, rơ-moóc (vẽ riêng) | |
| **Bus** | Xe buýt thường, xe buýt nối toa, minibus chở khách | |
| **Pedestrian** | Người đi bộ, người dắt xe đạp/xe máy | Người lái xe 2 bánh → Cyclist; người trong xe |
| **Cyclist** | Xe đạp, xe đạp điện, xe máy, xe tay ga, xe ba bánh **có người lái** | Xe 2 bánh dựng bên đường (không vẽ) |

### **Bảng 4.3 — Kích thước tham chiếu (mét)**

| Class | Loại | Dài (l) | Rộng (w) | Cao (h) |
|-------|------|--------|---------|--------|
| **Car** | Sedan, hatchback | 3,8–5,0 | 1,70–1,90 | 1,40–1,60 |
| **Car** | SUV, minivan, van | 4,4–5,3 | 1,80–2,10 | 1,65–2,10 |
| **Truck** | Bán tải | 5,2–6,2 | 1,85–2,10 | 1,80–2,00 |
| **Truck** | Xe tải hộp, nhẹ | 5,5–9,0 | 2,00–2,55 | 2,40–3,50 |
| **Truck** | Xe tải nặng, đầu kéo | 6,0–12,0 | 2,40–2,60 | 3,00–4,00 |
| **Truck** | Rơ-moóc | 2,0–16,0 | 1,50–2,60 | 0,80–4,10 |
| **Truck** | Máy xúc, cần cẩu, xe lu | 4,0–12,0 | 2,00–3,20 | 2,50–4,00 |
| **Bus** | Thường | 10,0–12,5 | 2,50–2,60 | 3,00–3,50 |
| **Bus** | Nối toa | 17,0–18,5 | 2,50–2,60 | 3,00–3,40 |
| **Pedestrian** | Người lớn | 0,4–0,9 | 0,5–0,9 | 1,50–1,95 |
| **Pedestrian** | Trẻ em | 0,3–0,6 | 0,3–0,6 | 0,90–1,40 |
| **Cyclist** | Xe đạp có người | 1,6–1,9 | 0,50–0,70 | 1,60–1,90 |
| **Cyclist** | Xe máy có người | 1,8–2,3 | 0,70–0,95 | 1,40–1,80 |
| **Cyclist** | Xe ba bánh có người | 2,5–3,5 | 1,00–1,50 | 1,50–2,00 |

### **Quy tắc cuboid (7 điểm)**

1. **Hình dạng:** Bao trọn điểm LiDAR, gồm phần suy luận (gương, thùng), không bao dư
2. **Hướng:** Vạch đỏ (+X cuboid) chỉ về đầu xe (đối chiếu ảnh camera); người theo hướng đi
3. **Đáy:** Chạm mặt đường (z_bottom = z − h/2 ≈ −1,7 m), lệch >0,3m là lơ lửng/chìm
4. **Kiểm:** Top, Side, Front đầy đủ, rồi đối chiếu ảnh camera
5. **Sai số:** Car/Bus/Truck ±5%, Pedestrian/Cyclist ±10%
6. **Vật thưa:** Thấy ≥15–20% thể tích thì vẽ; ít hơn → chỉ vẽ nếu có cơ sở suy luận; không thì bỏ qua → ghi LOW_VISIBILITY
7. **Occluded:** Bật nếu che phần, cuboid vẫn bao phần suy luận được

### **Khi không chắc**

| Loại nghi vấn | Ví dụ |
|---------------|------|
| **UNCERTAIN_CLASS** | Car hay Truck (van, bán tải); Bus hay Truck; Pedestrian hay Cyclist |
| **UNCERTAIN_SCOPE** | Vật quanh ngưỡng 50m; quá thưa điểm |
| **ATTRIBUTE_CHECK** | Không chắc occluded |
| **LOW_VISIBILITY** | Vật khó quan sát (đêm, mưa, ngược sáng, xa, ít điểm) |

👉 **Ghi vào báo cáo § 3–4, hỏi coach**

---

## 🧭 COORDINATES (Hệ tọa độ, 7 camera, 7 tham số)

👉 **Đầy đủ:** [docs/COORDINATES.md](docs/COORDINATES.md)

**Hệ tọa độ LiDAR VF:**
- **Gốc:** Tâm LiDAR của xe thu dữ liệu (cân ngang, không lệch)
- **+X:** Sang trái (từ driver sang passenger)
- **+Y:** Phía sau xe (khoảng cách từ trước ra sau)
- **+Z:** Lên trên
- **Mặt đường:** z ≈ −1,7 m

**Phía trước xe:** −Y (phía dưới ảnh 2D từ góc trên xuống)

**7 camera:**
```
cam01: Phía trước (nhìn về −Y)
cam02: Phía trước-trái (nhìn về −Y-X)
cam03: Phía trái (nhìn về −X)
cam04: Phía sau-trái (nhìn về +Y-X)
cam05: Phía trái cánh (nhìn về −X, cạnh)
cam06: Phía sau (nhìn về +Y)
cam07: Phía phải (nhìn về +X)
cam08: Phía trước-phải (nhìn về −Y+X)
cam09: Phía sau-phải (nhìn về +Y+X)
```

**7 tham số cuboid (CSV Bảng A):**
1. `length` (l) — Dài (theo +Y)
2. `width` (w) — Rộng (theo +X)
3. `height` (h) — Cao (theo +Z)
4. `x` — Tọa độ X tâm (mét)
5. `y` — Tọa độ Y tâm (mét)
6. `z` — Tọa độ Z tâm (mét)
7. `yaw_deg` — Hướng (độ, 0°–360°)

**Thêm:**
- `z_bottom = z − h/2` — Độ cao đáy
- `dist_m = √(x² + y²)` — Khoảng cách ngang
- `r = √(x² + y² + z²)` — Khoảng cách 3D
- `points_in` — Số điểm LiDAR trong cuboid

---

## 🛠️ SELF_CHECK (Đọc preview, cảnh báo, cột CSV)

👉 **Đầy đủ:** [docs/SELF_CHECK.md](docs/SELF_CHECK.md)

**`python scripts/lab.py preview`:**
- Tạo ảnh chiếu cuboid 3D lên 7 ảnh camera + ảnh từ trên xuống
- Lưu `work/xem/<frame>_<camera>.jpg`
- **Dùng để:** Xem cuboid có khớp ảnh camera hay không (vì CVAT 2.83 không có "biểu tượng vàng" như guideline)

**Phiếu tự kiểm (`submission/D13-<MSSV>-tukiem.csv`):**
- `points_in` — Số điểm LiDAR trong cuboid
- `points_on_edge` — Điểm ở cạnh hộp
- `height_above_ground` — Độ cao đáy so −1,7 m
- `flag_height`, `flag_distance`, `flag_dimension` — Cảnh báo

**Cảnh báo = gợi ý xem lại**, không phải lỗi chắc chắn.  
Cuboid đúng vẫn có thể bị cờ; cuboid sai có thể không bị cờ.

---

## 🎨 PHENOMENA (4 hiện tượng)

👉 **Đầy đủ:** [docs/PHENOMENA.md](docs/PHENOMENA.md)

| # | Hiện tượng | Guideline | Frame gợi ý |
|---|-----------|-----------|-----------|
| 1 | **Bóng che** | Bật occluded, bao phần suy luận | Nhiều frame có vật ở phía sau vật khác |
| 2 | **Thưa ở xa** | ≥15–20% thể tích vẽ; <15% → chỉ vẽ nếu có cơ sở | Vật cách LiDAR >40m |
| 3 | **Phản xạ thấp / ít điểm bất thường** | Ghi giả thuyết | Vật phi kim (nhựa, cao su) hoặc quanh góc LiDAR |
| 4 | **Điểm lạ** | Không gán thành vật (phản xạ sai, rác) | Điểm rơi lơ lửng hoặc tập trung lạ |

**Cách tìm:**
1. 10 frame của bạn trước
2. Nếu thiếu → xem task chung `D13-HC485087`
3. Nếu vẫn thiếu → mượn bạn kế bên (ghi rõ MSSV)

**Báo cáo §4:** Hiện tượng + frame → Nguyên nhân (giả thuyết) → Point cloud thấy gì → Hệ quả gán nhãn

---

## ⚠️ COMMON_ERRORS (Lỗi hay gặp)

👉 **Đầy đủ:** [docs/COMMON_ERRORS.md](docs/COMMON_ERRORS.md)

| Triệu chứng | Cách xử lý |
|-----------|----------|
| Chưa điền `student` trong `lab.ini` | Điền MSSV chính xác |
| Không thấy 10 frame | `data` trong `lab.ini` phải trỏ thư mục chứa `sequence_01..05` trực tiếp |
| Lỗi import cvat-sdk, Pillow | Chưa kích hoạt `.venv` hoặc chưa `pip install -r requirements.txt` |
| Không nối CVAT | `docker compose up -d` trong thư mục CVAT, kiểm `localhost:8080` |
| Cuboid dạng Track (cảnh báo) | Xóa, vẽ lại bằng **Shape** (không phải Track) |
| Mũi tên ngược đầu xe | Kéo nút xoay nửa vòng (180°) ở khung **Top** |
| Đáy lơ lửng/chìm | Chỉnh ở **Side** và **Front**, xem z_bottom |
| `length` và `width` đổi chỗ ở mọi xe | Lỗi công cụ CVAT, báo coach |
| Bảng A trống, bị `down -v`, Excel lưu đè | Không Save → Save; chưa `sync` → `sync`; Excel chiếm file → đóng Excel |

---

## 💻 COMMANDS (Mọi lệnh Python, Windows, macOS)

👉 **Đầy đủ:** [docs/COMMANDS.md](docs/COMMANDS.md)

**Python (giống Windows & macOS):**
```bash
# Bước 1
python scripts/lab.py dry              # Kiểm 10 frame, không nối CVAT
python scripts/lab.py task             # Tạo task D13-<MSSV>
python scripts/lab.py task --code HC485087  # Tạo task chung

# Bước 3
python scripts/lab.py report            # Xuất Bảng A (d13-<MSSV>-bangA.csv)
python scripts/lab.py report --code HC485087  # Xuất Bảng A task chung

# Bước 5
python scripts/lab.py sync              # Xuất Bảng A, preview, phiếu tự kiểm
python scripts/lab.py preview           # Chiếu cuboid lên 7 ảnh camera
python scripts/lab.py pack              # Nén ảnh báo cáo ZIP

# Kiểm
python scripts/check_submission.py      # Kiểm bài nộp (lỗi & cảnh báo)
```

**macOS (thêm tắt `make`):**
```bash
make dry / make task / make sync / make preview / make pack / make check
```

**Windows (PowerShell 5.1):**
- Không dùng `&&` trong một dòng; chạy từng lệnh riêng
- Phải có `.venv` được kích hoạt

---

## 📦 SETUP (Cài đặt Git, Python, môi trường, tải dữ liệu)

👉 **Đầy đủ:** [docs/SETUP.md](docs/SETUP.md)

**Trước buổi học:**

1. **Repo:** Chọn **Use this template** → Tạo public repo `K5-L2-DAY13-…`
2. **Clone:** `git clone <link-repo>` → `cd <repo>`
3. **Python:** Python 3.11+ → `python -m venv .venv`
4. **Kích hoạt `.venv`:**
   - Windows: `.venv\Scripts\activate.bat`
   - macOS: `source .venv/bin/activate`
5. **Dependencies:** `python -m pip install -r requirements.txt`
6. **Git config:** Nếu chưa → `git config user.name "…"` / `user.email "…"`
7. **Điền `configs/lab.ini`:** `student` = MSSV, `cvat_user` = tên CVAT
8. **Dữ liệu:** Tải `.zip` → Giải nén → Ghi path vào `configs/lab.ini` (`data = …`)
9. **Kiểm:** `python scripts/lab.py dry` → 10 frame, camera=7

---

## 🎮 CVAT_SETUP (Cài CVAT, tạo task, gợi ý giao diện)

👉 **Đầy đủ:** [docs/CVAT_SETUP.md](docs/CVAT_SETUP.md)

**Cài CVAT local:**
```bash
docker compose up -d           # Khởi động CVAT
docker compose down            # Dừng CVAT
docker compose down -v         # Dừng + xóa volume (cẩn thận! mất dữ liệu)
```

**Mở CVAT:** `http://localhost:8080` (Chrome/Edge, không Firefox)

**Nhãn 7 class:** `cvat/labels.json` (nếu tạo task bằng tay)

**Giao diện 3D (mẹo):**
- Kéo chuột trái: Xoay khung 3D
- Kéo chuột phải: Dời view
- Lăn con lăn: Phóng to/thu nhỏ
- **Top, Side, Front:** Chỉnh cuboid theo view đó
- Nháy đúp vào chỗ trống: Về góc mặc định
- Thanh công cụ: Shape (cuboid, không Track) → Nháy đúp để đặt

---

## 📊 DATA (Dữ liệu VF: tải, cấu trúc, phạm vi dùng)

👉 **Đầy đủ:** [data/README.md](data/README.md)

**5 sequence × 60 frame:**
- `sequence_01` đến `sequence_05`
- Mỗi frame: `*.bin` (point cloud), 7 ảnh camera `*.jpg`
- Script chọn **2 frame random** từ mỗi sequence → 10 frame cố định theo MSSV

**Tải:** Link & mật khẩu giáo viên gửi (không ghi vào repo)

**Cấu trúc:**
```
data-VF/
├── sequence_01/
│   ├── lidar/
│   │   └── *.bin              # Point cloud 40 tia
│   └── camera/
│       └── *.jpg              # 7 ảnh (cam01..cam09)
├── sequence_02/ … sequence_05/
└── calib.json                 # Hiệu chuẩn camera
```

**Phạm vi dùng:** ≤50m (ngang), ưu tiên ≤30m

---

## 🎯 CHECKPOINTS (Mốc giờ, Timeline 4 giờ)

👉 **Đầy đủ:** [CHECKPOINTS.md](CHECKPOINTS.md)

| Mốc | Giờ | Bước | Mục tiêu |
|-----|-----|------|---------|
| **Khởi động** | 16:00–16:10 | Điểm danh, cài đặt |  |
| **CP1** | 16:10–16:25 | 1 | `task`, kiểm 10 frame |
| **CP2** | 16:25–16:50 | 2 | Đọc 2 frame kỹ, 8 lướt, chụp ảnh |
| **CP3** | 16:50–17:00 | Hiệu chuẩn | 3 vật chung, gửi file |
| **Nghỉ** | 17:00–17:15 |  |  |
| **CP4** | 17:15–18:45 | 3 | Vẽ frame 1 (8–10), rồi frame 2–10 (5+ mỗi) |
| **CP5** | 18:45–19:00 | 4 | Tìm 4 hiện tượng, chụp ảnh |
| **CP6** | 19:00–19:30 | 5a | `sync`, sửa hộp, cập nhật báo cáo |
| **CP7 (nộp)** | 19:30 | 5b | Push lần 1, nộp LMS |
| **CP8** | 19:30–20:00 | 5c | Sửa tiếp (tới deadline 23:59) |
| **Exit ticket** | 20:00 | Góp ý |  |

---

## 📄 RUBRIC (Chi tiết chấm điểm)

👉 **Đầy đủ:** [RUBRIC.md](RUBRIC.md)

**Tổng:** 100 điểm = 60 (vẽ) + 40 (báo cáo)

**Vẽ (60):** Số lượng, khoảng cách, kích thước, hướng, đáy  
**Báo cáo (40):** §1–6 trung thực

---

## 🎓 THÊM: Bạn sẽ học được gì

- ✅ Mở, dùng CVAT 3D: xoay khung, vẽ cuboid, bật mũi tên, kiểm 6 view
- ✅ Đọc frame LiDAR: trục, mặt đất, tầm, camera vs LiDAR
- ✅ Vẽ cuboid 7 tham số: class, hộp bao, hướng, đáy, kích thước
- ✅ Nhận diện point cloud không đáng tin: bóng, thưa, phản xạ thấp, điểm lạ
- ✅ Kiểm bằng số: bảng A, so kích thước, báo cáo trung thực

---

## 📞 LIÊN HỆ & GÓP Ý

- **Lỗi:** Xem [docs/COMMON_ERRORS.md](docs/COMMON_ERRORS.md)
- **Câu hỏi:** Giáo viên hoặc đội hỗ trợ trong buổi học
- **Góp ý bài lab:** [Form góp ý](https://docs.google.com/forms/d/e/1FAIpQLSd8Mh7YkGEn-hnaePgbli4KqJ8-rkzCrFOIjr3YyCcrDs1UHA/viewform?usp=publish-editor)

---

**Tạo lúc:** 26-11-2026  
**Phiên bản:** 1.0 (tổng hợp từ 12 file tài liệu chính)
