# Mini guideline - nhóm: Nhóm 2  |  người gán: Hoàng Kim Thiện  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Ước lượng vị trí giải phẫu chỏm xương đùi (mấu chuyển lớn) nằm ngay dưới đai thắt lưng, cách trục giữa cơ thể ngang sang khoảng 1/4 bề rộng hông. Nếu áo/quần phủ kín thì đánh `v=1`; nếu nhìn rõ eo và nếp gấp đùi thì đánh `v=2`. | Khớp hông không bao giờ lộ trên da người mặc quần áo; cần mốc giải phẫu khung xương chậu cố định để tránh lệch 20-30px giữa các thành viên. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu nhìn thấy từ 50% diện tích vành tai trở lên: chọn `v=2`. Nếu bị tóc/mũ che trên 50% hoặc che mất lỗ tai nhưng đầu còn trong khung hình: đặt chấm theo trục ngang mắt-mũi và chọn `v=1`. | Tránh lạm dụng `v=0` khi đầu còn nguyên trong khung hình; tạo ranh giới định lượng giữa lộ diện và bị che. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp thân dưới (gối, cổ chân) nằm ngoài rìa ảnh bắt buộc đánh cờ `v=0` (Outside) và không kéo chấm ra ngoài canvas. | Cờ `v=0` giúp hàm loss bỏ qua khớp nằm ngoài khung hình, không phạt mô hình khi không tìm thấy điểm này. |
| Cổ tay nằm sau tay lái / sau thân mình | Nếu cánh tay/cẳng tay còn trong ảnh và hướng về vật che (tay lái, túi quần, sau lưng), đặt chấm ước lượng tại vị trí khớp giải phẫu và đánh cờ `v=1` (Occluded). | Cổ tay chỉ bị che khuất tạm thời bởi vật cản chứ không rời khỏi khung cảnh; giúp mô hình bảo toàn cấu trúc liên kết xương chi trên. |
| Hai người chồng lên nhau | Gán xong đủ 17 điểm của người phía trước trước, sau đó mới gán người phía sau. Điểm của người sau bị người trước che thì đặt chấm ước lượng và đánh `v=1`. Tuyệt đối không nối xương sang người khác. | Tránh triệt để lỗi nguy hiểm `nham_nguoi` (nhầm người) làm chéo xương giữa hai cơ thể cạnh nhau. |
| Người nhỏ đến mức nào thì không gán nữa | Gán toàn bộ người có chiều cao bounding box $\ge 50$ pixel và phân biệt được rõ thân người. Người ở quá xa hậu cảnh chỉ là bóng mờ dưới 30px thì bỏ qua. | Đảm bảo đồng bộ số lượng người giữa các thành viên trong nhóm và khớp với tập Gold chuẩn. |

> **Quy tắc về ảnh mẫu:** Với các khớp giải phẫu không lộ diện như `hip` (hông) hay khớp bị che trong túi áo (`wrist`), nhóm thống nhất căn cứ trục xương từ khớp liền kề (vai $\rightarrow$ khuỷu $\rightarrow$ cổ tay hoặc vai $\rightarrow$ hông $\rightarrow$ đầu gối) để kéo điểm đến vị trí giải phẫu tự nhiên nhất.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_04.jpg`, người thứ `1`, khớp `left_wrist`

- **Mơ hồ ở chỗ nào:** Bàn tay và cổ tay trái cầm một tập tài liệu dày che khuất hoàn toàn khớp cổ tay. Dễ phân vân giữa việc bỏ qua (`v=0`) hay ước lượng (`v=1`).
- **Bạn quyết thế nào:** Đặt chấm ước lượng tại vị trí giao nhau giữa xương cẳng tay và tập tài liệu, gắn cờ `v=1` (Occluded).
- **Vì sao:** Cẳng tay và tập tài liệu đều nằm gọn bên trong khung hình (cách mép ảnh hơn 50px), khớp chỉ bị vật cản che chứ không hề ra ngoài ảnh.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** Nếu chọn `v=0`, mô hình sẽ học rằng khi có vật cầm trên tay thì khớp cổ tay biến mất, dẫn đến bỏ sót khớp cổ tay (false negative) khi phát hiện người cầm vật dụng.

### Ca 2 - ảnh `train_03.jpg`, người thứ `1`, khớp `left_ankle` và `right_ankle`

- **Mơ hồ ở chỗ nào:** Hai người đứng sau vật cản che khuất phần bàn chân và mắt cá chân. Dễ nhầm lẫn giữa việc chân chạm đáy mép ảnh (`v=0`) và chân bị che (`v=1`).
- **Bạn quyết thế nào:** Kéo thẳng đường cẳng chân xuống vị trí giải phẫu cổ chân sau vật che và đánh cờ `v=1`.
- **Vì sao:** Toàn bộ phần chân và sàn nhà vẫn nằm trong bố cục khung hình, chân chỉ bị vật cản phía trước che lấp.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** Nếu chọn `v=0`, mô hình sẽ bị mất điểm OKS và học sai quy luật hình thể khi người đứng sau vật cản thấp, làm giảm khả năng nhận diện pose toàn thân trong thực tế.

### Ca 3 - ảnh `train_13.jpg`, người thứ `3`, toàn bộ skeleton

- **Mơ hồ ở chỗ nào:** Người thứ 3 đứng ở hậu cảnh xa, kích thước tương đối nhỏ và hơi tối màu so với hai người phía trước. Phân vân liệu có phải là người cần gán trong bài lab hay không.
- **Bạn quyết thế nào:** Quyết định gán bổ sung đầy đủ bộ 17 điểm cho người thứ 3 (các khớp nhìn rõ để `v=2`, khớp bị che để `v=1`).
- **Vì sao:** Nhân vật có chiều cao trên 60px, phân biệt được rõ đầu, thân và chân, đồng thời trong tập Gold chuẩn cũng yêu cầu gán người này (tổng gold có 3 người).
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** Nếu bỏ qua không gán, mô hình dự đoán đúng người này sẽ bị coi là dự đoán thừa (false positive penalty), hoặc mô hình học thói quen bỏ qua người ở cự ly trung bình.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_ear` (bạn `54%` / họ `36%`, lệch `18%`) và `right_wrist` (bạn `32%` / họ `18%`, lệch `14%`).
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**:
  Nguyên nhân chính là **guideline chưa rõ ràng**: Một bên coi tóc che một phần tai vẫn là `v=2` (nhìn thấy được), bên kia coi là `v=1` (bị che). Tương tự, khi đút tay vào túi quần, một bên đánh `v=0` vì không thấy tay, bên kia đánh `v=1` vì cổ tay vẫn trong khung hình.
- Luật mới bổ sung vào mục 2 sau khi thống nhất:
  - Khi đút tay vào túi áo hoặc túi quần: Miễn là vị trí túi còn nằm trong khung hình, bắt buộc phải ước lượng vị trí cổ tay giải phẫu và đánh cờ `v=1` (Occluded), tuyệt đối cấm dùng `v=0` (Outside).
  - Khi tóc che tai: Nếu tóc che trên một nửa vành tai, thống nhất chung cả nhóm đánh cờ `v=1`.
