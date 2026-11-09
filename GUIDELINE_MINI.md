# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | phương tiện giao thông cá nhân từ 4 đến 9 chỗ | Sedan/Hatchback/SUV, không tính xe van dạng hộp chở hàng/chở khách, Xe bán tải có cabin đôi và thùng hàng nhỏ phía sau |
| `truck` | Xe tải hạng nhẹ, trung và nặng, xe đầu kéo (container), xe ben công trình, xe cứu hộ/xe cẩu chuyên dụng. | Xe ben chở đất/cát, xe tải nhỏ chở bao hàng, Xe cứu hộ giao thông sàn phẳng có cần cẩu kéo xe |
| `bus` | Xe buýt công cộng nội đô, xe khách liên tỉnh cỡ lớn, xe buýt 2 tầng và cả minibus (dạng Toyota Coaster / 16-29 chỗ). | xe buýt công cộng đơn thân hoặc xe buýt 2 toa có khớp nối co giãn, xe khách du lịch đường dài cỡ lớn, xe buýt 2 tầng, xe khách minibus chở đoàn khách |
| `van` | Xe tải van hộp cỡ nhỏ hoặc xe minivan | Xe microvan hình hộp mini chở hàng phổ biến, Xe minivan/MPV chở người. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Box ôm sát phần thân xe nhìn thấy được, không lấy thêm mặt đường, bóng râm, vệt phản chiếu hình ảnh xe, trời hay xe bên cạnh.
- Vật thể bị che khuất: vẫn gán box quanh phần nhìn thấy được nếu nhận diện được lớp. Nếu xe bị che khuất quá nặng thì bỏ qua
- Vật thể bị cắt bởi rìa ảnh: vẫn gán box quanh phần nhìn thấy được nếu nhận diện được lớp, tuyệt đối không suy đoán phần thân xe nằm ngoài khung hình.
- Vật thể rất nhỏ/mơ hồ: Không gán box để tránh tạo nhiễu cho mô hình và tránh lệch nhãn ground truth.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_025.jpg` | Xe ô tô ở làn bên kia bị hàng rào cây xanh (bụi cây phân cách) che mất nửa dưới thân xe. | Gán nhãn car | Tuân thủ quy tắc vật thể bị che khuất: vẽ box ôm sát phần nhìn thấy được, đáy box dừng ngay tại mép trên của dải cây xanh. |
| `drive_012.jpg` | Minibus màu vàng cát | Gán nhãn bus | Schema quy định rõ ràng: bus bao gồm cả xe buýt lớn lẫn "minibus chở khách", phân biệt với van chở hàng hoặc minivan gia đình. |
| `drive_011.jpg` | Người điều khiển xe máy/xe đạp điện có thùng hàng giao đồ ăn màu vàng ở yên sau. | Không gán nhãn | Schema quy định rõ ràng: không gán người đi bộ, xe máy, xe mô tô hay xe đạp dù có chở thùng hàng. |
