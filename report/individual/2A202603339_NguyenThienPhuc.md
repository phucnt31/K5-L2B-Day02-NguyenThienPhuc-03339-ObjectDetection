# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Nguyễn Thiên Phúc
- MSSV: 2A202603339
- Email: nguyenthienphuc.02@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/phucnt31/K5-L2B-Day02-NguyenThienPhuc-03339-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/github/phucnt31/K5-L2-Day02-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb#scrollTo=3e0508d8

## 2. Schema và mini guideline

- Chi tiết hướng dẫn xem tại: [GUIDELINE_MINI.md](https://github.com/phucnt31/K5-L2B-Day02-NguyenThienPhuc-03339-ObjectDetection/blob/main/GUIDELINE_MINI.md).

### 2.1. Tóm tắt ba ca mơ hồ đã quyết định
1. **`drive_025.jpg` — Xe ô tô bị che khuất bởi dải phân cách cây xanh:**
   - *Tình huống:* Ô tô di chuyển ở chiều đường đối diện bị hàng cây dải phân cách che mất khoảng 40–50% phần thân dưới (bánh xe và cản dưới).
   - *Quyết định:* Gán nhãn `car` (ID: 0), vẽ bounding box ôm sát phần thân xe phía trên nhìn thấy được; đáy box dừng chính xác tại mép trên của dải cây xanh.
   - *Lý do theo schema:* Tuân thủ quy tắc vật thể bị che khuất (occlusion). Chỉ vẽ box cho phần diện tích nhìn thấy được (visible bounding box) khi vẫn nhận diện rõ loại xe, không tự suy đoán phần gầm/bánh xe bị khuất sau bụi cây.

2. **`drive_012.jpg` — Xe khách loại nhỏ (Minibus) màu vàng cát:**
   - *Tình huống:* Phương tiện chở khách cỡ trung (dạng Toyota Coaster / 16–29 chỗ ngồi) đi qua nút giao. Rất dễ bị nhầm lẫn giữa `bus` và `van`.
   - *Quyết định:* Gán nhãn `bus` (ID: 2).
   - *Lý do theo schema:* Schema quy định rõ ràng lớp `bus` bao gồm xe buýt công cộng và "minibus chở khách". Lớp `van` chỉ áp dụng cho xe tải van hình hộp chở hàng hoặc minivan/MPV cá nhân cỡ nhỏ. Do đó minibus chở khách thương mại phải thuộc lớp `bus`.

3. **`drive_011.jpg` — Xe máy / xe đạp điện có thùng hàng giao đồ ăn màu vàng:**
   - *Tình huống:* Shipper đi xe 2 bánh chở theo một thùng hàng lớn màu vàng phía sau, chiếm diện tích đáng kể trên đường và có cấu trúc dạng khối hộp.
   - *Quyết định:* **Không gán nhãn** (bỏ qua).
   - *Lý do theo schema:* Schema giới hạn nghiêm ngặt 4 lớp phương tiện (`car`, `truck`, `bus`, `van`) và loại trừ hoàn toàn người đi bộ, xe máy, xe mô tô, xe đạp. Dù có thùng hàng hay kích thước lớn, đây vẫn là phương tiện 2 bánh, không thuộc bất kỳ lớp nào trong schema.

### 2.2. Cách áp dụng schema trong thực tế
- **Nguyên tắc phân lớp nhất quán:** Phân loại theo loại xe. Xe đang di chuyển, dừng chờ đèn tín hiệu hay đỗ ven đường đều được gắn đúng nhãn loại xe tương ứng.
- **Quy tắc hình học và độ ôm sát (Tightness):** Bounding box ôm sát các điểm biên ngoài cùng của thân vỏ xe nhìn thấy được (gương, mép cản, lốp xe chạm đất). Tuyệt đối không bao gồm bóng râm (shadow) dưới gầm xe hoặc vệt phản chiếu hình ảnh xe trên mặt đường ướt sũng do mưa.
- **Quy tắc cắt biên (Truncation):** Đối với các xe ở sát mép ảnh bị cắt một phần thân, cạnh của box được kéo chạm đúng viền ảnh, không suy đoán phần thân xe nằm ngoài khung hình.
- **Nguyên tắc xe buýt khớp nối:** Đối với xe buýt 2 toa có khớp nối cao su, chỉ vẽ đúng 1 bounding box duy nhất bao trọn toàn bộ xe buýt (lớp `bus`), không tách thành 2 box.
- **Loại trừ vật thể ngoài scope:** Triệt để bỏ qua người đi bộ, xe 2 bánh, xe 3 bánh / xe tuk-tuk và các hình ảnh xe xuất hiện trên biển quảng cáo/trạm xe buýt.

### 2.3. Phần cần giảng viên làm rõ
1. **Ngưỡng kích thước (Scale threshold) đối với xe ở quá xa:** Trong các cảnh kẹt xe hoặc góc nhìn sâu, các xe ở cuối tầm mắt chỉ có kích thước rất nhỏ và mờ nhòe. Cần làm rõ ngưỡng kích thước pixel tối thiểu hoặc độ mờ để quyết định nên gắn nhãn hay bỏ qua.
2. **Quy ước đóng box cho xe tải chở hàng cồng kềnh vượt khung:** Với các trường hợp như `drive_042.jpg` (xe tải chở các bao hàng chất cao ngất ngưởng vượt qua nóc cabin), box cần bao trọn cả khối hàng chất trên thùng xe hay chỉ bám sát khung sắt/sàn thùng xe?
3. **Ranh giới giữa xe minivan/MPV đa dụng và xe con thông thường:** Một số dòng MPV 7 chỗ gia đình có hình dáng nằm giữa `car` (SUV/wagon) và `van` (xe van chở người). Cần giảng viên xác định rõ tiêu chí phân biệt.

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: 
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: drive_001, drive_002, drive_003, drive_004, drive_005, drive_006, drive_007, drive_008, drive_009, drive_010
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: ban đầu không phát hiện bỏ sót
- Commit khóa nhãn ban đầu: fdd61a0

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 362 | |
| FP | 544 | |
| FN | 291 |  |
| Precision | 0.657 |  |
| Recall | 0.5544 |  |
| F1 | 0.6013 |  |
| mean_iou_of_matches | 0.7604 |  |
| passed | true |  |

Dẫn tới `https://github.com/phucnt31/K5-L2B-Day02-NguyenThienPhuc-03339-ObjectDetection/blob/main/outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

Không có rework

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: T4 GPU
- Các tham số thay đổi so với notebook nếu có và lý do: 
- Đường dẫn best checkpoint trong phiên chạy: /content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.3575039374419265 |
| Recall | 0.2854683555888669 |
| mAP50 | 0.28020376710406264 |
| mAP50–95 | 0.19636183905592852 |

Đặt cả 2 folder `https://github.com/phucnt31/K5-L2B-Day02-NguyenThienPhuc-03339-ObjectDetection/tree/main/report/individual/assets/eval_predictions/visuals` và `https://github.com/phucnt31/K5-L2B-Day02-NguyenThienPhuc-03339-ObjectDetection/tree/main/report/individual/assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

ảnh: drive_052.jpg, bus: 0.69

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
- lớp bus recall thấp nhất, ảnh: drive_052.jpg

2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?
- Độ khít của viền hộp
- Xử lý các chi tiết về bóng đổ, gương và hàng hóa
- Tách cụm xe sát nhau
- Độ phân giải và góc phối cảnh

3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?
- Cặp van và car: Nhầm lẫn giữa các dòng xe 7 chỗ, SUV, MPV (như Innova, Carnival, Xpander).
- Cặp van và bus: Nhầm lẫn giữa xe chở khách cỡ nhỏ (xe 16 chỗ như Solati, Ford Transit) và xe khách/xe buýt tiêu chuẩn.
- Cặp van và truck: Nhầm lẫn giữa xe tải thùng kín cỡ nhỏ (blind van), xe bán tải (pickup) và xe tải thông thường.

- Mâu thuẫn lớn giữa nhãn thực tế (Ground-Truth) và nhãn dự đoán của mô hình:
+ Trong bảng đánh giá ground-truth của tập eval (10 ảnh), chỉ có duy nhất 2 instances của bus.   
+ Tuy nhiên, trong phần log dự đoán trực quan (results = best_model.predict...), mô hình lại nhận diện ra tới 9 lần xuất hiện của bus trên nhiều ảnh: drive_052 (1 bus), drive_053 (3 buss), drive_056 (1 bus), drive_058 (3 buss), drive_060 (1 bus).   
+ Việc dự đoán dư thừa (False Positives cao dẫn đến Precision = 0) phản ánh tình trạng: trong tập train có những xe được gán là bus, nhưng ở tập eval các xe tương tự lại bị gán thành van, truck hoặc car (hoặc bị bỏ sót).   

4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
- Theo dõi tiến độ học: Cứ sau mỗi epoch train trên 50 ảnh, notebook lại lấy 10 ảnh này ra kiểm tra thử xem điểm số ($mAP$) đang tiến bộ hay thụt lùi.  
- Chọn phiên bản tốt nhất (best.pt): Trong suốt 50 epoch, ở epoch nào mô hình làm bài trên 10 ảnh này đạt điểm cao nhất, notebook sẽ tự động lưu lại phiên bản đó thành file best.pt (thay vì lấy bừa file ở epoch 50 cuối cùng).

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Review qua các nhãn | Sau khi gắn nhãn mới nhận ra là add label sai schema | Đổi vị trí của label trong mục "raw" nhưng ko đổi được -> phải xóa tasks đi -> tạo task mới -> import lại ảnh và nhãn cũ (nhãn em tự gắn) với class_id đã sửa để trùng với label đã tạo trong task mới | Các nhãn cũ đã xuất hiện trong ảnh mà không cần tốn thời gian gắn lại nhãn |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.

- Hoàn thành và định dạng YOLO:	50 file cùng basename, 5 cột, normalized, bốn class ID đúng
- Đầy đủ vật thể: gán đủ xe thuộc schema, kể cả xe che/cắt khung có thể nhận diện
- Độ bám sát và hình học box: box sát phần vật thể nhìn thấy, không lấn nền/gộp vật thể
- Phân lớp nhất quán: phân biệt car/truck/bus/van, không gán vật thể ngoài scope
- Mini guideline và quyết định mơ hồ: định nghĩa lớp, quy tắc box, ít nhất ba case có lý do
Gold-set annotation QC: F1 >= 0.50 (được lab coach điều chỉnh) và mean IoU >= 0.70 hoặc có vòng rework được giải thích rõ

## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.

- Dùng gemini, dùng ở phần Trả lời bốn câu hỏi cuối notebook, sửa class_id trong dữ liệu file txt (file này em tự gắn nhãn, không dùng nhãn của gold_train_labels) để import lại vô task gắn nhãn mới
- Hỏi code hoạt động ra sao, giải thích thuật ngữ, luồng chạy và nguyên lý hoạt động của bước "Huấn Luyện" trong notebook