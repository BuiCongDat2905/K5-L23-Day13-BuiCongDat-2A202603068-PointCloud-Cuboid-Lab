# Báo cáo Day 13 — Bùi Công Đạt, 2A202603068

## 1. Kết luận trước

MSSV 2A202603068, task `D13-2A202603068`, 10 frame: `s01f22`, `s01f58`, `s02f11`, `s02f39`, `s03f25`, `s03f56`, `s04f02`, `s04f21`, `s05f00`, `s05f21`.
Máy Windows (64-bit), trình duyệt Chrome/Edge (Chromium), CVAT 2.83.0 (tài khoản `datco` → About). Đổi kiểm chéo: bạn cùng bàn — MSSV 2A202603068.

Tổng **127 cuboid** trên 10 frame. Frame ít nhất là `s03f25` với **2 cuboid**, trong đó chỉ **một** xe nằm trong tầm 50 m (34,7 m), chiếc còn lại ở 55,9 m đã ngoài vùng chấm — frame đó đường vắng. `selfcheck` còn cờ **3/127** cuboid (đều là cảnh báo đáy). Tôi tin nhất phần **hướng yaw**: cả 98 xe con đều có `length` > `width`; 60 xe có yaw trong 270° ± 20° và 25 xe trong 90° ± 20° (13 xe còn lại đi chéo hoặc đỗ nên yaw khác). Phần yếu nhất là **vật xa trên 40 m và hộp bị che**, vì ở đó chỉ còn vài chục điểm nên đáy và kích thước phải suy luận.

Cách dùng CVAT: kéo chuột trái để xoay khung 3D, chuột phải để dời, lăn chuột để zoom, nháy đúp chỗ trống để về góc mặc định; bấm nút vẽ cuboid → chọn label → **Shape** → nháy đúp đặt hộp, rồi chỉnh ở ba khung Top (dời tâm, kéo cạnh, xoay yaw) và Side/Front (hạ đáy, đặt chiều cao); bật **Appearance → Cuboid orientation** để thấy vạch đỏ `+X` và xem lại hộp trên ảnh camera bằng `preview`.

## 2. Đọc frame (Bước 2)

Hai frame đọc kỹ. Ảnh: `t1-s01f22-3d.png`, `t1-s01f22-top.png`, `t1-s01f58-3d.png`, `t1-s01f58-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f22 | Phía trước (−y) là **cạnh trên**; +x chỉ sang **trái**, +y chỉ **xuống dưới** (về phía sau) | Phẳng; đáy các hộp chạm đường cho z mặt đường ≈ −1,73 m, thấp hơn LiDAR ≈ 1,7 m | ≈ 54,5 m (xe con #7) | Xe con (7 hộp) và một xe buýt; trên ảnh camera còn thấy người đi bộ, biển báo ở vỉa hè nhưng điểm quá thưa nên không vẽ | Phía sau có #2, #4, #5, #6, #7 (xa nhất #7 ở +54,4 m); phía trước có #3 và #8; #1 ở ngay bên trái | Vài cụm điểm ở rìa ngoài 60 m (bụi cây, cột) không đọc được là vật gì |
| s01f58 | Như trên: phía trước là **cạnh trên**, +x sang trái, +y xuống dưới | Phẳng; z mặt đường ≈ −1,80 m, thấp hơn LiDAR ≈ 1,8 m và thấp hơn s01f22 khoảng 0,07 m | ≈ 29,1 m (xe con #9) | Toàn xe con (10/10 Car), không có xe buýt | Phía sau có #1 (+6,8 m), #2 (+0,7 m), #9 (+29,0 m); phía trước có #3–#8 và #10 | Cụm thưa phía trước-trái khoảng 35–45 m, không rõ là xe hay bụi cây |

**Ba điều chỉ camera cho biết:** màu xe và chi tiết nhận dạng (đèn, kính — tôi không ghi lại nội dung biển số); người đi bộ và biển báo trên vỉa hè, thứ mà point cloud chỉ hiện thành cụm điểm; phần khuất của vật vẫn nhìn thấy một phần để suy ra hình dạng.
**Ba điều chỉ LiDAR cho biết:** khoảng cách và kích thước thật bằng mét (bảng A: `dist_m`, `length`, `width`, `height`); độ cao và đáy của vật so với mặt đường (`z_bottom`); vật nằm ngoài hình quạt của mọi camera (sau xe, hai bên góc mù) — camera không thấy nhưng LiDAR vẫn quét tới.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f11 | Cạnh trên | Nhiều xe hai bánh và người đi bộ dày bên **phải** (5/7 hộp có x < 0); có 2 xe buýt |
| s02f39 | Cạnh trên | Hầu hết xe ở **phía sau** (y > 0); một cyclist bên phải ở 11,8 m |
| s03f25 | Cạnh trên | Rất vắng: 2 xe con, chỉ một xe trong tầm 50 m |
| s03f56 | Cạnh trên | Hai xe buýt ở **phía sau-trái** (x > 0); nhiều hộp bị che (`occluded`) |
| s04f02 | Cạnh trên | 20 cuboid: 1 xe tải và 5 người đi bộ bên **phải** (x < 0) |
| s04f21 | Cạnh trên | **36 cuboid — đông nhất**; xe cả hai chiều; một xe tải ở 9,8 m **phía trước-trái** (x > 0) |
| s05f00 | Cạnh trên | Xe tải lớn ở 9,5 m **phía sau** (cam09), che mất vùng sau nó |
| s05f21 | Cạnh trên | 4 cyclist bên **phải** (x < 0); một xe tải xa 43,6 m ở **phía sau** |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f22, #1 | Car | Có: yaw 267,7°, lệch 2,3° so với 270°, vạch `+X` dọc thân xe | Có: `z_bottom` −1,730 m | Có: đủ 6 mặt khít điểm, đối chiếu cam05 | 4,38 × 2,11 × 1,48 m; dài và cao đúng dòng sedan (3,8–5,0 × 1,40–1,60 m), nhưng **rộng 2,11 m vượt trần sedan 1,90 m khoảng 11%** (và vượt trần SUV/van 2,10 m đúng 0,007 m) — nghi hộp bao dư sang gương/bánh, không kết luận là SUV |
| (b) xe tải | s04f21, #15 | Truck | Có: yaw 86,8° — xe ngược chiều nên đầu xe hướng ngược lại, vạch `+X` vẫn dọc thân | Có: `z_bottom` −1,750 m | Có: kiểm cả ba khung, đối chiếu cam06 | 5,63 × 2,33 × 2,36 m; dài trong 5,5–9,0 m, rộng trong 2,00–2,55 m, cao 2,36 m sát dưới mốc 2,40 m của dòng tải hộp nhẹ — hợp xe tải nhẹ |
| (c) xe hai bánh | s02f11, #8 | Cyclist | Có: yaw 350,4° theo hướng đi chéo của xe | Có: `z_bottom` −1,678 m | Có: kiểm Top/Side/Front, đối chiếu cam08 (vùng sau-phải, x < 0) | 1,61 × 0,64 × 1,60 m; nằm trong khoảng xe đạp 1,6–1,9 × 0,50–0,70 × 1,60–1,90 m |

Vòng hiệu chuẩn ở task chung `D13-HC485087`: khi ghép file của cả lớp, chỗ lệch nhiều nhất là **tâm hộp và yaw**, rồi tới chiều dài; cả lớp chốt lại theo guideline — cạnh dài phải dọc thân xe, `+X` của cuboid chỉ về đầu xe, đáy đo ở Side/Front chứ không đoán từ Top. Các vật tôi **bỏ qua vì không đủ cơ sở**: cụm thưa phía trước-trái `s01f58` khoảng 35–45 m và các cụm điểm ở rìa ngoài 60 m của `s01f22` chỉ còn vài điểm, dưới 15–20% thể tích và không nhận ra hình dạng trên ảnh camera, nên không vẽ và ghi nhận mức LOW_VISIBILITY.

## 4. Bốn hiện tượng chất lượng (Bước 4)

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s05f00 #3, `t4-1-bong-che.png` (frame của tôi) | Thân xe tải cao 3,4 m chắn các tia LiDAR 40 tia phía sau nó | Trong dải 8 m ngay sau hộp: **0 điểm**; hai bên cùng khoảng cách: 3118 điểm (trái) và 1602 điểm (phải) | Vùng trống sau vật che là **chưa quan sát được**, không phải đường trống: không kết luận làn trống, vật bị che một phần phải bật `occluded` và cuboid chỉ bao phần suy luận được |
| 2. Thưa ở xa | s04f02 #11, `t4-2-thua-o-xa.png` (frame của tôi) | Tia quét thưa dần theo bình phương khoảng cách; ở 41,9 m mỗi vòng quét chỉ còn vài tia trúng xe | Hộp 41,9 m chỉ còn **43 điểm**, trong khi #10 ở 41,2 m có 94 điểm và #14 ở 44,8 m có 94 điểm | Chỉ vẽ khi thấy khoảng 15–20% thể tích hoặc đủ cơ sở từ ảnh camera; hộp này giữ vì camera thấy rõ xe, nhưng không nới rộng theo cảm giác |
| 3. Phản xạ thấp / ít điểm bất thường | s04f21 #11, `t4-3-phan-xa-thap.png` (frame của tôi) | Giả thuyết: mặt xe tối màu hoặc kính phản xạ kém, hoặc phần lớn hộp nằm trong vùng bị che; chưa đủ căn cứ để khẳng định | Ở 12,7 m chỉ **251 điểm**, trong khi #4 ở 11,7 m có 1226 điểm và #14 ở 18,5 m vẫn 751 điểm; intensity trung vị của cả ba hộp đều ≈ 0 nên không dùng intensity để kết luận | Ghi giả thuyết, không khẳng định; không nới hộp theo quầng sáng, kiểm lại bằng ảnh camera và giữ kích thước theo bảng 4.3 |
| 4. Điểm lạ | s01f22, `t4-4-diem-la.png` (frame của tôi) | Giả thuyết: phản xạ đa đường hoặc điểm lỗi của cảm biến | Một điểm ở (x 7,2; y −24,4; z **−11,9**) m, cách 25,5 m theo mặt bằng (r = 28,1 m), thấp hơn mặt đường khoảng 10,3 m; nhìn ngang mới thấy rõ | Không gán thành vật; điểm dưới mặt đường là sai hình học chắc chắn nhưng nguyên nhân chưa rõ, chỉ ghi giả thuyết |

## 5. Số từ cuboid của tôi (Bước 5)

Lấy từ `submission/D13-2A202603068-bangA.csv`; số điểm lấy từ cột `points_in` của `D13-2A202603068-tukiem.csv`.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét |
|---|---|---|---|---|---|---|---|
| s01f22 #5 Car | −0,994 m | 1,570 m | **−1,779 m** | 20,29 m | 20,31 m | 271,7° | Chạm đường, lệch 0,08 m so với −1,7 m |
| s04f21 #15 Truck | −0,570 m | 2,360 m | **−1,750 m** | 9,79 m | 9,80 m | 86,8° | Chạm đường; xe ngược chiều nên yaw gần 90° |
| s03f56 #8 Bus | −0,060 m | 3,200 m | **−1,660 m** | 29,25 m | 29,25 m | 269,0° | Chạm đường, lệch 0,04 m |

(a) **Gần nhất:** s01f58 #2 Car, `dist_m` = 3,00 m, r = 3,15 m. **Xa nhất:** s04f21 #21 Car, `dist_m` = 60,78 m, r = 60,79 m (ngoài vùng chấm 50 m nhưng vẫn đọc được; cả bài có 6 hộp vượt 50 m).
(b) **Kích thước so với bảng 4.3:** người đi bộ s02f11 #10 là 0,65 × 0,58 × 1,86 m — cả ba chiều nằm trong khoảng người lớn 0,4–0,9 × 0,5–0,9 × 1,50–1,95 m (cao sát trần, có thể đội mũ). Xe con s01f22 #1 là 4,38 × 2,11 × 1,48 m — dài trong 3,8–5,0 m, cao trong 1,40–1,60 m, nhưng **rộng 2,11 m vượt 1,70–1,90 m** khoảng 11%: chỉ chiều rộng ngoài khoảng, có thể hộp bao dư sang gương/vành bánh.
(c) **yaw:** xe cùng chiều s01f58 #2 có yaw 269,6° (gần 270°); xe ngược chiều s02f11 #1 có yaw 89,0° (gần 90°).

Mật độ theo khoảng cách:

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s01f22 #2 Car | 6,85 m | 1948 |
| s05f21 #4 Car | 15,18 m | 512 |
| s04f02 #2 Car | 24,73 m | 235 |
| s04f21 #25 Car | 40,96 m | 43 |
| s03f25 #2 Car | 55,94 m | 32 |

Quy luật: từ 6,85 m đến 40,96 m (xa gấp 6 lần) số điểm còn 1948 → 43, tức **2,2%**; đến 55,94 m chỉ còn 32 điểm. So với đường cong ở slide bài giảng (xe con: 1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m, 19 ở 60–80 m) số của tôi khớp ở khoảng gần (1948 ở 6,9 m), thấp hơn khoảng 29% ở 15,2 m (512 so với 718) và thấp hơn ở 41 m (43 so với 72 ở bin 30–40 m) — hộp 41 m nằm lệch làn nên ít tia trúng hơn. Trong cùng bin 30–50 m, xe con dao động 43–206 điểm tùy hộp.

Tự kiểm: tôi giữ nguyên **3 cảnh báo** — s04f02 #7 (đáy cao hơn mặt đường 0,54 m), s04f21 #17 (chìm 0,67 m) và s04f21 #25 (chìm 0,27 m). Lý do: cả ba hộp bám đúng cụm điểm khi kiểm ở Top/Side/Front. Hộp s04f02 #7 và s04f21 #25 nằm ở chỗ mặt đường lên/xuống so với mặt phẳng −1,7 m mà công cụ dùng làm mốc; hộp s04f21 #17 ở 13,9 m chỉ còn 417 điểm và đáy khít cụm điểm khi soi ở Side/Front, có thể điểm mặt đường quanh hộp bị thưa nên ước lượng mặt phẳng bị lệch — tôi giữ hộp theo điểm, không hạ/nâng theo cờ. Ba hộp dẫn trong bảng trên có đáy −1,779 m, −1,750 m và −1,660 m, đều cách −1,7 m không quá 0,08 m. Trong lúc soát `preview` trước khi chốt bảng A, các hộp lệch đã được chỉnh ngay trên CVAT (đặt lại hộp theo cụm điểm, kéo cạnh ở Top, hạ đáy ở Side/Front); sau lần `sync` cuối tôi không chỉnh thêm hộp nào và không chỉnh chỉ để tắt cờ.

Đổi kiểm: đã nhờ bạn cùng bàn (MSSV 2A202603068) đối chiếu hai con số với bảng A mở trên máy tôi — `dist_m` của s04f21 #21 (60,78 m) và `z_bottom` của s03f56 #8 (−1,660 m) — hai số khớp.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

(a) Nhãn 3D đo bằng **mét** vì model học hình học thật của vật trong không gian: dự đoán vị trí, kích thước và hướng để lập kế hoạch chuyển động; nếu dùng pixel thì cùng một cái xe sẽ đổi kích thước theo camera và theo khoảng cách. Vật **thưa điểm** cần quy tắc riêng vì số điểm giảm theo bình phương khoảng cách (ở 41 m còn 43 điểm, ở 56 m còn 32 điểm): buộc phải đủ điểm thì mất hết vật xa, còn vẽ dễ dãi thì nhãn sai hình học — nên dùng ngưỡng 15–20% thể tích kèm cờ LOW_VISIBILITY.

(b) Có dùng AI, ghi rõ: **công cụ** — trợ lý AI trong IDE và một script Python tôi tự viết để đọc `.bin`, `calib.json`; **câu hỏi chính** — kiểm số trong bảng A (kích thước theo class, mật độ điểm theo khoảng cách, độ dốc mặt đường) và soạn lại phần chữ của báo cáo; **cách dùng kết quả** — mọi con số đều đối chiếu lại với bảng A và phiếu tự kiểm, còn toàn bộ cuboid vẫn do tôi vẽ tay trên CVAT.

(c) Điều còn chưa chắc: chiều rộng xe con s01f22 #1 (2,11 m, vượt trần sedan khoảng 11% — có thể do hộp bao dư sang gương); kích thước các hộp `occluded` ở s03f56 (chỉ suy ra từ phần nhìn thấy); đáy của các hộp trên 40 m, nơi mặt đường dốc làm `z_bottom` lệch tới 0,3–0,5 m so với mốc −1,7 m; và 6 hộp vượt 50 m vẫn được vẽ dù ngoài vùng chấm.
