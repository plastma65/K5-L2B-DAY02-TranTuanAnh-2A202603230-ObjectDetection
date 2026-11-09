# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | xe `car` dùng để chở người và hành lý mang theo: Thân xe thấp, có 4 bánh xe, thường là một khối liền, số lượng chỗ ngồi thường không vượt quá 9 chỗ | xe taxi, xe sedan, xe toyota thường nhỏ và dùng để chở số lượng nhỏ hành khách và hành lý đem theo thường không vượt quá 5 người và thường thấp và ngắn hơn xe `van`, không có cabin tách rời như xe `truck`, bán tải cabin nhỏ vẫn tính xe `car` không phải `truck` |
| `truck` | xe `truck` dùng để chở hàng và xe đầu kéo: Có từ 4 đến 8 bánh xe, có cabin riêng và thùng hàng (hở hoặc kín) phía sau | xe tải thùng xanh, xe ben, xe tải nhỏ thường có cabin tách khỏi thùng hàng và dùng để chở hàng hóa hoặc nguyên vật liệu khác với xe `van` thường là một khối liền |
| `bus` | xe `bus` và minibus dùng để chở khách: Thân xe dài, cao, nhiều cửa sổ lớn dọc theo thân xe và có cửa lên và xuống. | xe buýt màu xanh lá, xe khách, xe buýt 2 tầng thường dài và cao hơn hẳn xe `van`. `van` chở người cở nhỏ không phải `bus` |
| `van` | xe `van` và minivan dùng để chở người hoặc chở hàng: thân hộp liền khối, cao hơn xe `car` và nhỏ hơn xe `bus` rõ rệt | xe van trắng/xám, xe mini van dùng để giao hàng, xe van đưa thư của bưu điện, khác `car` ở chỗ dáng hộp cao, khác `bus` ở kích cỡ và khác `truck` ở chỗ không có cabin tách rời với thùng hàng. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Ôm sát phần thân xe theo 4 góc gồm trên, dưới, trái, phải. Không lấy thêm các phần thừa như mặt đường, ánh sáng dư thừa, bóng phản chiếu trên mặt đường, nắp cống.
- Vật thể bị che khuất: Vẽ box quanh phần nhìn thấy được, hai xe chồng lên nhau thì vẽ riêng mỗi box trên phần nhìn thấy được của xe đó.
- Vật thể bị cắt bởi rìa ảnh: box chạm đúng rìa ở phía cắt, không đoán hay vẽ phần nằm ngoài khung, tọa độ không được vượt biên [0,1]. xe chỉ để lộ một phần nhỏ ở rìa mà không nhận ra lớp thì xử lý như ca rất nhỏ/mơ hồ.
- Vật thể rất nhỏ/mơ hồ: Xe ở xa trong cảnh đông (như drive_003, 005, 011) vẫn gán nếu còn thấy rõ hình dạng và chắc lớp. Không chắc lớp thì không đoán, không vẽ, ghi vào bảng ca mơ hồ và hỏi giảng viên hoặc lead coach. Van/minivan mơ hồ với bus hoặc car: không tự chọn, ghi case ID rồi hỏi.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_006.jpg` | Xe màu xanh bạc hà, có giá nóc, bên phải ảnh: dáng cao và vuông hơn xe con, phân vân giữa `car` (SUV) và `van` (minivan). | `van` | Schema ghi minivan chở người thuộc `van`; thân hộp cao hơn xe con thông thường, không có cabin tách thùng nên không phải `truck`. |
| `drive_014.jpg` (cùng xe ở `drive_038.jpg`) | Xe cứu hộ/kéo xe của công an: phía trước là cabin kiểu van, phía sau gắn cần cẩu và sàn kéo, phân vân giữa `van` và `truck`. | `truck` | Xe không chở người mà làm nhiệm vụ chuyên dụng, có cabin riêng và phần thùng/cần cẩu phía sau, khớp mô tả "xe tải chở hàng"; không phải thân hộp liền khối của `van`. |
| `drive_002.jpg` | Xe van màu trắng/xám sát rìa phải ảnh, bị cắt một phần thân bên phải. | `van`, box chạm đúng rìa phải ảnh | Phần nhìn thấy đủ để nhận ra lớp (thân hộp, cao hơn các xe con xung quanh). Quy tắc cắt khung: box chạm rìa, không đoán phần ngoài ảnh. |
| `drive_038.jpg` | Xe van đỏ bị chồng lên xe tải nhỏ màu xanh có phủ bạt, hai box giao nhau. | Hai box riêng: `van` cho xe đỏ, `truck` cho xe xanh | Hai xe chồng nhau thì vẽ hai box riêng trên phần nhìn thấy của từng xe, không gộp; xe xanh có cabin tách thùng hàng nên là `truck`. |
