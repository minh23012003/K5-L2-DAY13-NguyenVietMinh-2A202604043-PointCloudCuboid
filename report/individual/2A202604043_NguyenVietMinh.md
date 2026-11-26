# Báo cáo Day 13 — Nguyễn Viết Minh, 2A202604043

## 1. Kết luận trước
- Mã số `2A202604043`, task `D13-2A202604043`. 10 frame: s01f04, s01f36, s02f04, s02f41, s03f17, s03f46, s04f02, s04f46, s05f12, s05f40.
- Windows 11, Chrome, CVAT server 2.81.0 (cvat-sdk 2.83.0, chạy được dù có cảnh báo phiên bản). Bạn ngồi cạnh: không có (tôi làm một mình).
- Tổng **56 cuboid** (51 Car, 2 Truck, 2 Bus, 1 Cyclist). Frame đầu s01f04 có 8 cuboid; các frame ít nhất có 5 cuboid (s01f36, s02f41, s03f46, s04f46, s05f12, s05f40); lý do: tôi vẽ đủ mức yêu cầu của đề và ở nhiều vật còn lại cụm điểm không rõ nên không vẽ thêm.
- `selfcheck` cờ **5/56** cuboid sau khi sửa (s01f04 #2, s01f36 #4, s05f12 #2, s05f40 #2, s05f40 #4); trước khi sửa là 6/56. 4 cuboid có tâm xa hơn 50 m (s01f36 #4 66,63 m, s01f36 #5 79,62 m, s03f17 #3 55,61 m, s05f40 #4 58,19 m) nên không được chấm.
- Tin nhất: hướng (không cuboid nào có yaw lệch kiểu ngược đầu so với 90° / 270°). Yếu nhất: chưa biết ước lượng ngưỡng 50 m khi vẽ, nên có 4 cuboid nằm ngoài 50 m.

Cách dùng CVAT:
- Đổi góc nhìn: kéo chuột trái để xoay khung 3D tới khi nhìn thẳng xuống, kéo chuột phải để dời, lăn con lăn để phóng to/thu nhỏ, nháy đúp chỗ trống để về góc mặc định.
- Vẽ cuboid: nút cuboid → chọn label → **Shape** → nháy đúp lên cụm điểm; chỉnh tâm, dài, rộng, yaw ở Top; chỉnh đáy và chiều cao ở Side/Front (không xoay ở Side/Front).
- Hướng và kiểm trên ảnh: bật Appearance → Cuboid orientation để thấy vạch đỏ `+X`; chạy `python scripts/lab.py preview` để xem cuboid chiếu lên 7 ảnh camera trong `work/xem/`.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f04-3d`, `t1-s01f04-top`, `t1-s01f36-3d`, `t1-s01f36-top`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f04 | Cạnh trên (ngược vạch xanh lá +y; cùng hướng cam01); +x chỉ sang cạnh trái; +y chỉ xuống cạnh dưới | Phẳng; thấp hơn LiDAR ~1,77 m (trung vị z đáy các cuboid trong 30 m) | ~34,8 m (Car #8, phía sau) | Đã gán: 7 Car, 1 Truck; còn vài Car khác chưa gán vì đã đủ số cuboid đề yêu cầu | Phía sau: Car #4 (~15,7 m), Car #5 (~23,3 m), Car #8 (~34,8 m); bên trái: Truck #2 (~12,1 m), Car #3 (~9,6 m), Car #6 (trái-sau ~16,4 m) | Có vài cụm điểm là xe nhưng tôi chưa gán vì đã đủ số cuboid đề yêu cầu |
| s01f36 | Cạnh trên (ngược vạch xanh lá +y; cùng hướng cam01); +x chỉ sang cạnh trái; +y chỉ xuống cạnh dưới | Phẳng; thấp hơn LiDAR ~1,77 m (trung vị z đáy các cuboid trong 30 m) | ~79,6 m (Car #5, phía trước, 16 điểm) | Đã gán: 5 Car; còn vài Car khác chưa gán vì đã đủ số cuboid đề yêu cầu | Phía sau: Car #1 (~9,7 m); trước-trái: Car #2 (~9,9 m) | Có vài cụm điểm là xe nhưng tôi chưa gán vì đã đủ số cuboid đề yêu cầu |

Chỉ camera cho biết: (1) màu và loại xe cụ thể (sedan hay SUV, xe tải chở hàng hay bán tải), giúp chọn class; (2) đầu và đuôi xe qua đèn pha, đèn hậu, kính chắn gió, giúp đặt hướng yaw; (3) biển báo, vạch kẻ và đèn tín hiệu. Chỉ LiDAR cho biết: (1) khoảng cách theo mét tới từng vật (ví dụ s01f04 #1 cách 6,70 m, #8 cách 34,84 m); (2) kích thước thật theo mét và độ cao mặt đường (~−1,77 m); (3) vật ở vùng camera không thấy hoặc thấy kém, ví dụ các xe phía sau ở s01f04 (#4, #5, #8) và xe sát hai bên.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f04 | Cạnh trên | Không |
| s02f41 | Cạnh trên | Không |
| s03f17 | Cạnh trên | Không |
| s03f46 | Cạnh trên | Không |
| s04f02 | Cạnh trên | Không |
| s04f46 | Cạnh trên | Không |
| s05f12 | Cạnh trên | Không |
| s05f40 | Cạnh trên | Không |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f04 #1 (dist_m 6,70 m) | Car | Đúng: yaw 266,7°, xe cùng chiều; vạch đỏ chỉ đầu xe khi đối chiếu camera | Có: z đáy −1,767 m; khoảng hở với mặt đường 0,00 m | Có | l 4,527 m, h 1,494 m trong khoảng sedan; w 2,141 m vượt 1,70–1,90 m (vượt cả 2,10 m của SUV) |
| (b) xe buýt hoặc xe tải | s01f04 #2 (dist_m 12,08 m) | Truck | Đúng: yaw 88,4°, xe ngược chiều; vạch đỏ chỉ đầu xe khi đối chiếu camera | Có: z đáy −1,814 m; khoảng hở 0,18 m | Có | l 5,871 m, h 2,862 m hợp với xe tải nhẹ (5,5–9,0 m; 2,40–3,50 m); w 1,486 m dưới 2,00 m: bị `selfcheck` cờ |
| (c) người đi bộ hoặc xe hai bánh | s02f04 #6 (dist_m 24,34 m) | Cyclist | Đúng: yaw 271,2°; vạch đỏ chỉ đầu xe khi đối chiếu camera | Có: z đáy −1,626 m; khoảng hở 0,04 m | Có | l 1,675 m, w 0,689 m, h 1,618 m đều nằm trong khoảng xe đạp có người (camera cho thấy là xe đạp) |

Vòng hiệu chuẩn: tôi không làm bước này (không tham gia vòng hiệu chuẩn trên lớp).

Vật bỏ qua vì không đủ cơ sở: không có.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s02f04 (frame của tôi) / `t4-1-bong-che` | Giả thuyết: xe buýt (Bus #5, dài ~12 m, cách ~13 m) chắn tia laser nên phần không gian phía sau nó (tính từ LiDAR) không được quét | Phía sau xe buýt theo hướng tia từ LiDAR là một vùng hình quạt gần như không có điểm, trong khi hai bên vùng đó vẫn có điểm mặt đường và công trình; vùng trống này là chưa quan sát được, không phải đường trống | Bật occluded; cuboid bao cả phần bị che suy luận được |
| 2. Thưa ở xa | s01f36 #4 Car (frame của tôi) / `t4-2-thua-o-xa` | Giả thuyết: LiDAR 40 tia nên càng xa khoảng cách giữa các tia càng lớn, vật ở ~66,6 m chỉ bị vài tia quét qua | Cuboid chỉ chứa 20 điểm (`points_in`), chỉ thấy một mảng điểm thưa, khó nhận rõ hình dạng xe; ngoài 50 m nên không được chấm | Vẽ nếu thấy khoảng 15–20% thể tích; không thì bỏ qua và ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | s03f46 #5 Car (frame của tôi) / `t4-3-phan-xa-thap` | Giả thuyết (không khẳng định): bề mặt xe tối màu hoặc nhiều kính phản xạ yếu, hoặc xe bị che một phần bởi xe phía trước cùng làn (#1, ~14,1 m) | Ở 21,6 m cuboid chỉ có 97 điểm, chưa bằng một nửa mức ~214 điểm của slide cho 20–30 m; trong khi #4 xa hơn (28,1 m) vẫn có 150 điểm | Ghi giả thuyết, không khẳng định; vẽ theo quy tắc vật thưa điểm |
| 4. Điểm lạ | s02f35, mượn từ task chung D13-HC485087 / `t4-4-diem-la` | Giả thuyết: điểm ảo do tia laser phản xạ nhiều lần (ví dụ qua kính hoặc bề mặt bóng) nên đo sai khoảng cách | Cụm khoảng 46 điểm phía sau bên trái, cách ~29 m, ở z ≈ −13,5 m, tức thấp hơn mặt đường quanh đó ~11 m; phải xoay nghiêng khung 3D mới thấy | Không gán thành vật |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202604043-bangA.csv`; số điểm lấy từ cột `points_in` của `submission/D13-2A202604043-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f04 #1 Car | −1,020 m | 1,494 m | −1,767 m | 6,70 m | 6,77 m | 266,7° | Chạm đường (lệch 0,07 m so với −1,7 m) |
| s03f17 #6 Car | −0,293 m | 1,807 m | −1,197 m | 15,65 m | 15,65 m | 271,2° | Cao hơn −1,7 m 0,50 m: lơ lửng theo ngưỡng −1,7 m; `selfcheck` đo khoảng hở với mặt đường cục bộ 0,27 m; chưa sửa, cần hạ đáy ở Side/Front |
| s02f04 #4 Car | −1,336 m | 1,554 m | −2,113 m | 18,84 m | 18,89 m | 87,0° | Thấp hơn −1,7 m 0,41 m: chìm theo ngưỡng −1,7 m; `selfcheck` đo khoảng hở cục bộ 0,12 m (mặt đường chỗ này có thể thấp hơn, nên theo mặt đường cục bộ hộp gần chạm đường); giữ nguyên |

(a) Cuboid **gần nhất**: s02f04 #2 Car, dist_m 2,98 m, r 3,14 m. Cuboid **xa nhất**: s01f36 #5 Car, dist_m 79,62 m, r 79,63 m (ngoài 50 m, không được chấm). Xa nhất trong phạm vi 50 m: s03f17 #2 Car, dist_m 47,23 m, r 47,25 m.

(b) Kích thước so với bảng 4.3:
- Cyclist s02f04 #6: l 1,675 m, w 0,689 m, h 1,618 m. Với xe đạp có người (1,6–1,9 m; 0,50–0,70 m; 1,60–1,90 m) cả ba chiều nằm trong khoảng; camera cho thấy đây là xe đạp có người nên không có chiều nào nằm ngoài khoảng.
- Car s01f04 #1: l 4,527 m, w 2,141 m, h 1,494 m. l và h trong khoảng sedan (3,8–5,0 m; 1,40–1,60 m); w vượt 1,70–1,90 m (và vượt 2,10 m của SUV): có thể tôi kéo hai mặt bên rộng dư so với điểm, hoặc bao cả gương chiếu hậu; cần thu hẹp ở Top.

(c) Xe cùng chiều phía trước: s01f04 #7 Car (y −11,356 m), yaw 269,8°, gần 270°. Xe ngược chiều phía trước: s02f04 #3 Car (y −14,851 m), yaw 95,3°, gần 90°.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s04f02 #4 Car | 4,62 m | 5393 |
| s01f04 #7 Car | 11,56 m | 847 |
| s01f04 #5 Car | 23,29 m | 324 |
| s01f04 #8 Car | 34,84 m | 108 |

Slide bài giảng (xe con, trung vị): khoảng 1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m, 19 ở 60–80 m. Ở 60–80 m, cuboid của tôi s01f36 #4 có 20 điểm (66,63 m).

Quy luật: số điểm giảm rất nhanh theo khoảng cách, gần với tỉ lệ 1/d²: từ 4,62 m lên 34,84 m (xa hơn ~7,5 lần) số điểm giảm từ 5393 xuống 108 (~50 lần, gần 7,5² ≈ 56). Xu hướng giống đường cong của slide; số điểm của tôi cao hơn trung vị slide ở mọi khoảng (847 so với 718 ở 10–20 m, 324 so với 214 ở 20–30 m, 108 so với 72 ở 30–40 m), có thể vì các xe tôi chọn ít bị che và nằm gần làn của xe thu dữ liệu. Ở 60–80 m, s01f36 #4 có 20 điểm, gần mức 19 của slide.

Hai cuboid đã sửa sau `preview` / `selfcheck`:
- s02f04 #5 Bus: thu hẹp chiều rộng từ 3,348 m xuống 2,797 m, chiều cao từ 3,357 m xuống 3,204 m, nâng đáy từ −1,821 m lên −1,645 m; hết cờ `width ngoài khoảng`.
- s01f04 #5 Car: thu hẹp chiều rộng từ 1,949 m xuống 1,744 m, chiều cao từ 1,940 m xuống 1,837 m cho sát điểm; số điểm trong hộp từ 345 còn 324.

Cảnh báo giữ nguyên: s01f36 #4 và s05f40 #4 ở ngoài 50 m (66,63 m; 58,19 m) nên không được chấm; s01f04 #2, s05f12 #2, s05f40 #2 tôi chưa kịp sửa (đã nộp muộn) và ghi lại là điểm yếu: #2 ở s05f12 và s05f40 có thể là xe tải hoặc van chở hàng chứ không phải Car.

Đổi kiểm: không đổi kiểm (tôi làm một mình, không có bạn ngồi cạnh).

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Nhãn 3D đo bằng mét vì model học từ nhãn này phải dự đoán vị trí và kích thước thật của vật quanh xe để xe tính khoảng cách phanh, tránh va chạm; pixel phụ thuộc camera và khoảng cách nên không cho biết vật thật to bao nhiêu hay cách bao xa. Vật thưa điểm cần quy tắc riêng (vẽ khi thấy khoảng 15–20% thể tích, không thì bỏ và ghi LOW_VISIBILITY) vì nếu mỗi người đoán một kiểu, model sẽ học nhãn không nhất quán: vừa học vẽ hộp ở nơi gần như không có điểm (báo động giả), vừa học bỏ sót vật xa.

(b) Dùng AI:
- Công cụ: Claude Code (trong VS Code). Coach cho phép dùng AI viết báo cáo.
- Việc chính: tóm tắt đề và guideline; chạy các lệnh `scripts/lab.py`, `check_submission.py`; đọc bảng A và phiếu tự kiểm để tìm cuboid nghi sai kích thước, đáy, khoảng cách; điền số từ bảng A và soạn câu chữ báo cáo từ câu trả lời của tôi, bảng A và tài liệu của đề. AI không xem ảnh camera hay dữ liệu VF.
- Cách dùng kết quả: tôi tự vẽ và sửa mọi cuboid trên CVAT, tự chụp ảnh, trả lời các câu hỏi về quan sát; tôi đọc lại và chịu trách nhiệm toàn bộ báo cáo.

(c) Điều còn chưa chắc: vật ở xa không xác định rõ được thì có nên vẽ không; và cách ước lượng ngưỡng 50 m ngay trên CVAT khi đang vẽ (tôi chỉ biết sau khi xuất bảng A và xem cột `dist_m`).
