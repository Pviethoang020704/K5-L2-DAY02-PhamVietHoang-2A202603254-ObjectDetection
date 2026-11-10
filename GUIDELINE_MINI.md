# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp
| Lớp     | Định nghĩa tham khảo                       | Đặc điểm phân biệt                               |
| ------- | ------------------------------------------ | ------------------------------------------------ |
| `car`   | Ô tô cá nhân, sedan, SUV, taxi, xe bán tải | Xe chở người thông thường hoặc pickup            |
| `truck` | Xe tải vận chuyển hàng hóa, xe đầu kéo     | Có kết cấu phục vụ vận chuyển hàng               |
| `bus`   | Xe buýt và minibus chở khách               | Phương tiện thiết kế chuyên chở nhiều hành khách |
| `van`   | Xe van hoặc minivan chở người, chở hàng    | Thân xe dạng hộp, khoang chở tương đối lớn       |


## Quy tắc chung

- Box bám sát vật thể nghĩa là: Hình chữ nhật bao quanh phần thân xe nhìn thấy, không bao gồm phần nền không cần thiết.
- Vật thể bị che khuất: Nếu xe vẫn nhận diện được thì vẽ box quanh phần nhìn thấy
- Vật thể bị cắt bởi rìa ảnh: Box chạm rìa ảnh tại phần xe bị cắt, không suy đoán phần ngoài ảnh
- Vật thể rất nhỏ/mơ hồ: Không tự đoán lớp nếu không đủ căn cứ; ghi lại tình huống để hỏi giảng viên

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_003.jpg` |xe khách bị che mất một phần |vẽ box xung quanh không gồm phần bị cắt |Xe vẫn nhận diện được nên vẫn gán nhãn, box chỉ bao quanh phần xe nhìn thấy |
| `drive_044.jpg` |khó phân biệt xe van và car |Chọn class theo hình dáng thực tế: thân cao, vuông là van; thân thấp, kiểu sedan/hatchback là car |Ưu tiên đặc điểm hình dáng bên ngoài, không suy đoán khi thiếu thông tin |
| `drive_019.jpg` |khó phân biệt xe bus và van |Chọn bus nếu thân xe dài, cao, có nhiều cửa sổ chở khách; chọn van nếu thân xe ngắn và nhỏ gọn |Dựa vào kích thước, cấu trúc thân xe và đặc điểm khoang hành khách |
