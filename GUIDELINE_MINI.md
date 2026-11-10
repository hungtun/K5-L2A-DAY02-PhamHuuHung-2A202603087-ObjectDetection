# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Ô tô cá nhân hoặc xe chở người cỡ nhỏ đến vừa (4–7 chỗ), thân thấp hoặc gầm cao vừa phải, có đầu ca-pô tách biệt. | Sedan, hatchback, SUV/crossover, taxi, xe bán tải nhỏ. **Phân biệt**: không có thùng hàng lớn tách rời như `truck`, không có thân hộp cao liền khối như `van`. |
| `truck` | Xe tải chở hàng hoặc xe chuyên dụng đóng trên khung gầm tải, phần cabin đầu xe tách rời với thùng hàng hoặc thiết bị phía sau. | Xe tải thùng kín, xe mui bạt, xe ben, xe đầu kéo, xe cứu hộ giao thông. **Phân biệt**: có thùng chở hàng/thiết bị phía sau thay vì khoang kính hành khách dài như `bus`. |
| `bus` | Xe chở khách cỡ trung và lớn, thân xe dài dạng hộp với dãy cửa sổ kính liên tiếp dọc hai bên hông. | Xe buýt nội đô, xe buýt 2 tầng, xe buýt khớp nối, xe khách, minibus. **Phân biệt**: kích thước dài và cao hơn hẳn `van`, có cửa lên xuống và khoang ghế hành khách rõ ràng. |
| `van` | Xe van/minivan chở người hoặc chở hàng nhẹ có thiết kế thân hộp liền khối, mũi ngắn, trần cao phẳng kéo dài tới đuôi. | Minivan, microvan, xe van chở hàng kín hông, xe có cửa trượt bên sườn. **Phân biệt**: đầu ngắn và thân vuông cao hơn `car`/SUV, nhưng liền một khối (không tách rời cabin–thùng như `truck`) và ngắn hơn `bus`. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Kẻ khung vừa khít phần thân xe, bánh xe và hàng hóa chằng cố định trên xe đang nhìn thấy; không lấy dư mặt đường, bóng đổ dưới gầm hay khoảng trống xung quanh, mỗi xe một box riêng.
- Vật thể bị che khuất: Nếu phần lộ ra vẫn đủ rõ để nhận biết đúng loại xe thì vẫn gán nhãn; chỉ vẽ box bao quanh phần thực tế nhìn thấy được, không tự ước lượng phần bị xe khác hoặc vật cản che mất.
- Vật thể bị cắt bởi rìa ảnh: Kéo cạnh box chạm sát mép ảnh, không vượt ra ngoài khung hình. Chỉ gán khi phần xe còn lại trong ảnh đủ lớn để nhận diện chắc chắn lớp, bỏ qua các mẩu cản hoặc góc đuôi xe quá nhỏ sát mép.
- Vật thể rất nhỏ/mơ hồ: Không gán các đốm xe ở quá xa gần đường chân trời bị mờ nhòe, dính chùm không phân biệt được loại xe. Không gán người đi bộ, xe đạp, xe máy, xe ba gác, biển báo hay đèn giao thông.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_002` | Phía làn đường xa gần cột đèn giao thông có đoàn xe nối đuôi nhau rất nhỏ và mờ, khó tách rời từng chiếc và không nhìn rõ kiểu dáng. | Không gán đám xe quá xa bị nhòe ở cuối đường; chỉ gán các xe ở tiền cảnh và trung cảnh còn thấy rõ hình khối. | Xe ở quá xa không đủ chi tiết để xác định chắc thuộc `car` hay `van`, theo quy tắc không tự đoán với vật thể quá nhỏ/mơ hồ. |
| `drive_008` | Ngã tư đông xe: xe buýt khớp nối che khuất nửa thân vài ô tô con phía sau, có các chiếc microvan trắng/bạc đi lẫn với SUV, kèm xe máy bên trái và một mẩu nhỏ đầu xe đen ở sát góc dưới phải. | Gán `van` cho microvan thân hộp, `car` cho SUV và phần lộ ra của các ô tô bị che, `bus` cho 2 xe buýt, `truck` cho xe ben đỏ; bỏ qua xe máy và mẩu xe góc dưới phải. | Xe bị che một phần nhưng vẫn nhận diện rõ là `car` nên vẽ box phần nhìn thấy; xe máy nằm ngoài 4 lớp mục tiêu, còn mẩu xe góc phải bị cắt quá sát không đủ thông tin. |
| `drive_034` | Giữa ngã tư trời mưa có xe cứu hộ giao thông (cabin trắng gắn cần kéo phía sau) đi cạnh các xe ba gác có thùng/mái che và xe máy. | Gán `truck` cho xe cứu hộ giao thông; bỏ qua toàn bộ xe ba gác, xe đạp thồ và xe máy. | Xe cứu hộ phát triển trên khung gầm xe tải chuyên dụng có cabin rời phần sàn/cẩu kéo nên thuộc `truck`; xe 2–3 bánh có mái che vẫn là phương tiện ngoài schema. |
| `drive_042` | Xe tải chở các bao hàng chất cao quá nóc che một phần xe buýt 2 tầng phía sau; góc dưới trái có xe buýt bị cắt đuôi ở rìa ảnh và che nửa dưới chiếc taxi. | Box `truck` bao luôn khối bao hàng trên thùng; gán `bus` chạm sát mép dưới ảnh cho xe buýt bị cắt và gán `car` ôm phần thân trên nhìn thấy của chiếc taxi. | Hàng hóa chằng cố định trên thùng là một phần thể tích xe tải trên ảnh; xe bị che khuất hoặc cắt khung nhưng còn nhận diện rõ loại xe thì vẫn gán phần nhìn thấy. |
| `drive_047` | Chiếc xe bạc ở mép trái bị cắt mất phần đầu, đi gần chiếc SUV trắng và một xe tải thùng kín màu trắng bên phải. | Gán `van` cho xe bạc (cạnh trái chạm mép ảnh), `car` cho chiếc SUV trắng và `truck` cho xe tải thùng kín. | Xe bạc vẫn lộ rõ thân hộp cao liền khối và cửa lùa đặc trưng của `van`; SUV có đầu ca-pô thuộc `car`, còn xe có cabin rời thùng hàng vuông phía sau thuộc `truck`. |
