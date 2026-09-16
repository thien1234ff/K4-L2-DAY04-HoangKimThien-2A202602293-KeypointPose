# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Hoàng Kim Thiên   Nhóm: Nhóm 32   Ngày: 16/09/2026

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 28 |
| v=2 / v=1 / v=0 | 321 / 116 / 39 |
| Thời gian trung bình mỗi ảnh | 4.5 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` (54% - 15 khớp v=1)
2. `right_ear` (39% - 11 khớp v=1)
3. `right_wrist` (32% - 9 khớp v=1)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

<!-- Trả lời 2–4 câu. Phân biệt “hay bị che” với “khó xác định vị trí giải phẫu”; nêu bằng
chứng nhìn thấy thay vì chỉ nêu cảm giác. -->

Khớp tai (`left_ear`, `right_ear`) có tỉ lệ `v=1` cao nhất do đặc thù tư thế người nghiêng hoặc bị tóc, mũ che khuất, nhưng đây không phải là những khớp khó xác định nhất vì ta vẫn suy luận được toạ độ giải phẫu khá chính xác qua đường thẳng nối từ mắt và mũi. Ngược lại, khớp khó gán nhất trên thực tế là khớp hông (`left_hip`, `right_hip`) và cổ tay (`wrist`) khi bị che lấp bởi quần áo rộng thùng thình, vạt áo hoặc đồ vật cầm tay. Khớp hông hoàn toàn không có đường viền cơ thể trực tiếp để bám vào mà phải ước lượng vị trí giải phẫu của xương chậu dựa trên thắt lưng và đùi.

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.941 | 0.965 |
| OKS@0.50 | 0.966 | 1.000 |
| OKS@0.75 | 0.966 | 1.000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 4 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

<!-- Mỗi dòng phải có: tên ảnh + người thứ mấy + keypoint + thao tác sửa. Không viết “đã sửa
lại một số lỗi”. -->

- `train_13.jpg`, người thứ 3: Bổ sung 1 skeleton bị thiếu ở hậu cảnh (trong gold có 3 người, bản đầu chỉ gán 2 người).
- `train_03.jpg`, người thứ 1, keypoint `left_ankle` và `right_ankle`: Sửa cờ từ `v=0` (outside) thành `v=1` (occluded) và đặt chấm ước lượng tại vị trí giải phẫu sau vật cản.
- `train_03.jpg`, người thứ 2, keypoint `left_ankle` và `right_ankle`: Sửa cờ từ `v=0` thành `v=1` do chân còn trong khung hình nhưng bị che khuất.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

<!-- Nếu không có lỗi, ghi rõ “Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh.” -->

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh. Ngay từ khâu warm-up, tôi luôn kiểm tra trực quan bằng `tools/visualize_pose.py` (theo dõi màu xanh bên trái và màu cam bên phải không cắt chéo nhau) và luôn áp dụng quy tắc đứng vào góc nhìn cơ thể của nhân vật để xác định trái/phải thay vì nhìn theo hướng bức ảnh.

## 3. Kiểm chéo

Bạn cùng nhóm: Nguyễn Văn A (Nhóm 2)

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 54% | 36% | 18% | Guideline chưa nói rõ trường hợp tóc dài che một phần tai thì chọn `v=1` hay `v=2`. |
| `right_wrist` | 32% | 18% | 14% | Bất đồng khi nhân vật đút tay vào túi: một bên để `v=0`, một bên để `v=1`. |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

<!-- Viết một rule kiểm chứng được: điều kiện nhìn thấy/căn cứ vị trí → chọn v=1 hoặc v=0.
Không chỉ ghi “cẩn thận hơn khi gán”. -->

- Với bàn tay/cổ tay đút vào túi quần hoặc túi áo: Nếu cẳng tay vẫn kéo dài vào miệng túi và phần túi nằm trọn trong khung hình (chưa chạm mép ảnh), bắt buộc phải đặt keypoint `wrist` tại vị trí ước lượng giải phẫu sau lớp vải và đánh cờ `v=1` (Occluded), không được dùng `v=0` (Outside).

## 4. Model

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model
   điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?

   - `pose_mAP50-95` tăng từ `0.6853` lên `0.6908` (tăng `+0.0055`, tương đương ~0.55%), đồng thời `pose_precision` tăng từ `0.9734` lên `0.9792` (`+0.0058`).
   - 20 ảnh được gán nhãn nhất quán, chính xác về vị trí khớp và bảo toàn đầy đủ các khớp bị che (`v=1`) đã giúp mô hình học thêm cách định vị điểm mốc cơ thể trong các tư thế khó ở dải IoU khắt khe (0.75 - 0.95). Tuy nhiên, tập dữ liệu nhỏ 20 ảnh khiến chỉ số `box_mAP50-95` giảm nhẹ (-0.0078) do mô hình hơi thiên lệch (overfit) vào kích thước và phân phối bounding box cục bộ của tập train nhỏ.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm
   *khớp* dễ hơn? Vì sao?

   - Ở mốc baseline: `box_mAP50-95` đạt `0.8119` trong khi `pose_mAP50-95` chỉ đạt `0.6853` (chênh lệch `0.1266` tức 12.66%). Ở mốc mAP50, box đạt `0.9785` còn pose đạt `0.8450` (chênh `0.1335`).
   - Model tìm *người* (bounding box) dễ hơn tìm *khớp* (keypoints) rất nhiều. Bounding box chỉ cần bắt được đặc trưng vùng hình thể tổng quát của cơ thể người. Trong khi đó, định vị keypoints đòi hỏi mạng phải hồi quy chính xác 17 toạ độ riêng biệt tới từng pixel trong điều kiện tư thế vặn xoắn phức tạp, quần áo che lấp và góc nhìn đa dạng.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43
   (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):

   - Trong ảnh `test_02.jpg` và `test_06.jpg` (những ảnh có 2 người đứng sát nhau), model xuất hiện lỗi **nhầm người** (cánh tay của người phía sau bị nối nhầm sang thân người phía trước) và lỗi **lệch nhẹ** ở các khớp cổ chân do bị bóng tối dưới sàn che khuất.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?

   - Dựa vào bảng OKS ở mục 6: Ảnh có OKS thấp nhất là `train_06` (OKS = `0.652`) và `train_15` (OKS = `0.656`). Ngoài ra có 3 ảnh lệch về số lượng người được phát hiện (`train_03`, `train_10`, `train_13`).
   - Ở ảnh `train_06`: Nhãn của tôi đúng. Người trong `train_06` có tư thế quay người sang bên, cẳng tay buông che một phần thân. Tôi đã ước lượng khớp khuỷu và cổ tay bám theo chiều dọc xương cánh tay (`v=1`). Model bị đánh lừa bởi màu áo tối và nếp nhăn nên dự đoán điểm khuỷu tay trượt xuống thấp gần 20 pixel so với vị trí giải phẫu thật.

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó
   nói gì về bức ảnh đó?

   - Có sự trùng hợp rõ rệt: Khi chấm với Gold, `train_03` và `train_13` là các ảnh có vấn đề lớn nhất (thiếu người hoặc nhầm cờ chân `v=0/v=1`). Khi so với Model, `train_03` và `train_13` cũng chính là các ảnh model bị lệch số lượng người (`train_03` model tìm ra 4 người trong khi nhãn có 2; `train_13` model tìm ra 3 người trong khi nhãn có 2).
   - Điều này chứng minh đây là những bức ảnh có **độ mơ hồ thị giác cao (visual ambiguity)**: người ở hậu cảnh có kích thước nhỏ, mờ ảo khiến ranh giới quyết định gán nhãn hay bỏ qua rất mong manh đối với cả người gán nhãn lẫn thuật toán object detection.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

<!-- Cấu trúc gợi ý: (1) train_XX + người thứ mấy + keypoint; (2) căn cứ thị giác như phần cơ
thể liền kề, trang phục hoặc vật che; (3) vì sao khớp còn trong khung (v=1) hay đã ra khỏi
khung (v=0). -->

- **Đối tượng:** Ảnh `train_04.jpg`, người thứ 1, keypoint cổ tay trái (`left_wrist`).
- **Căn cứ thị giác:** Nhân vật đang đứng nghiêng người, toàn bộ cánh tay trái buông xuôi và bàn tay đang cầm một tập tài liệu dày che khuất hoàn toàn khớp cổ tay. Tuy nhiên, đường trục của xương cẳng tay trái nhìn thấy rất rõ ràng và hướng thẳng vào vị trí cầm tài liệu; đồng thời cả tập tài liệu và cẳng tay đều nằm cách mép ảnh hơn 50 pixel.
- **Lý do quyết định:** Do khớp cổ tay chắc chắn vẫn nằm trọn trong khung hình (chưa hề bị cắt khỏi rìa ảnh), việc không nhìn thấy chỉ là do vật thể phía trước che khuất. Vì vậy, theo quy tắc bắt buộc, tôi đặt chấm ước lượng tại vị trí giải phẫu của cổ tay và gắn cờ `v=1` (Occluded), dứt khoát không đánh `v=0` (Outside).
