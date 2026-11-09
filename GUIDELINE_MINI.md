# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` |Phương tiện chở người, thường có 4 bánh, kích thước nhỏ hoặc trung bình, khoang hành khách và khoang hành lý được thiết kế liền nhau hoặc tách biệt. |Sedan, hatchback, SUV, coupe. Phân biệt với van dựa trên kiểu thân xe và thiết kế khoang chở hàng. |
| `truck` |Phương tiện cơ giới được thiết kế chủ yếu để vận chuyển hàng hóa, thường có cabin lái và thùng hoặc khoang chở hàng riêng biệt. |Xe tải thùng, xe tải ben, xe đầu kéo. Không nhầm với xe van chở hàng có thân xe liền khối. |
| `bus` | Phương tiện được thiết kế chủ yếu để vận chuyển nhiều hành khách, thường có khoang hành khách lớn và nhiều cửa sổ hoặc cửa lên xuống.| Xe buýt đô thị, xe khách, xe buýt trường học. Không chỉ dựa vào kích thước để phân biệt với truck.|
| `van` |	Phương tiện có thân xe dạng hộp, nóc tương đối cao, khoang hành khách hoặc khoang chở hàng kéo dài phía sau cabin; thường nhỏ hơn xe buýt và xe tải lớn. |Minivan, passenger van, cargo van. Phân biệt với car dựa trên kiểu thân xe; phân biệt với truck dựa trên cấu trúc thân xe và khoang hàng. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Đặt bounding box sát các rìa ngoài cùng có thể quan sát được của phương tiện, bao gồm phần thân xe nhìn thấy được; không lấy thừa quá nhiều nền đường, bầu trời hoặc phương tiện bên cạnh. Không tự mở rộng box để bao phủ phần bị che khuất.

- Vật thể bị che khuất: Nếu vẫn nhận diện được phương tiện theo schema, gán nhãn và vẽ box theo phần vật thể thực sự nhìn thấy. Không vẽ đè qua phương tiện che khuất để phỏng đoán đường viền bị mất, trừ khi quy định dự án yêu cầu box bao toàn bộ vật thể.

- Vật thể bị cắt bởi rìa ảnh: Nếu phương tiện vẫn đủ đặc điểm để xác định lớp, gán nhãn phần vật thể hiện diện trong ảnh và để box kết thúc tại rìa ảnh. Nếu không đủ bằng chứng để phân loại, xử lý theo quy tắc loại bỏ hoặc đánh dấu không xác định của dự án.

- Vật thể rất nhỏ/mơ hồ: Chỉ gán nhãn khi có thể xác định tương đối chắc chắn phương tiện và lớp tương ứng. Không tự suy đoán khi hình ảnh quá mờ, quá nhỏ hoặc bị che khuất nghiêm trọng. Tuân thủ ngưỡng kích thước tối thiểu và quy tắc bỏ qua của dự án nếu có.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_029` |xe bus bị mất khoảng 1 ít ở đầu |gán nhãn class là bus |nhiều cửa sổ nóc cao hơn xe car và van |
| `drive_013` |xe con bị che bởi hàng cây |gán nhãn class là car |kích thước nhỏ, chở 4 người |
| `drive_023` |xe chỉ thấy phần thân 1 bên |gán nhãn class là bus |xe to nhiều cửa sổ |
