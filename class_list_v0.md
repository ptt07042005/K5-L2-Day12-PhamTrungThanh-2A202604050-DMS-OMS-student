# Danh sách lớp v0 — Phạm Trung Thành · G01

Viết tiếp từ slide 54 bài giảng. Mỗi lớp trả lời: **model cần nhận ra cái này để làm gì?**

## DMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng DMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `person` (`seat=driver`) | bounding box + `driver` | Xác định sự hiện diện và vùng cơ thể tài xế để neo các thuộc tính khác | Có (thấy rõ đường bao người lái) |
| 2 | `person.seatbelt` | `worn_correct` / `worn_incorrect` / `not_worn` / `unknown` | Cảnh báo tài xế không thắt hoặc thắt sai dây an toàn | Có, trừ khi áo tối màu hoặc tay/đồ vật che vùng ngực |
| 3 | `face.eye_left`, `face.eye_right` | `open` / `closed` / `unknown` | Phát hiện nhắm mắt kéo dài, chớp mắt nhiều, buồn ngủ (PERCLOS) | Có, trừ khi bị loá LED trên tròng kính hoặc kính râm tối |
| 4 | `face.gaze_zone` | `road` / `mirror_l` / `mirror_r` / `mirror_c` / `cluster` / `center` / `lap` / `other` | Phát hiện tài xế mất tập trung, rời mắt khỏi đường quá lâu | Có, dựa vào hướng đầu và mắt nếu không bị phản xạ kính |
| 5 | `face.glasses` | `none` / `clear` / `sun` / `unknown` | Đánh giá mức độ che khuất vùng mắt để giảm báo nhầm trạng thái mắt | Có gọng/phản xạ, nhưng kính râm có thể trông trong suốt trên NIR |
| 6 | `face13` | 13 điểm mặt + cờ `occluded` / `outside` | Ước lượng góc quay đầu (yaw/pitch/roll) và đo hình học độ mở mắt/miệng | Có, nhưng các điểm dưới đốm loá kính phải đánh cờ `occluded` |
| 7 | `hand` (`whose`, `side`, `on_wheel`) | `driver`/`passenger`, `left`/`right`, `yes`/`no`/`unknown` | Kiểm tra tay có đặt trên vô lăng hay đang làm việc riêng gây mất tập trung | Có, nhưng tay quá gần đèn LED dễ bị bão hoà sáng trắng |
| 8 | `phone` (`state`) | `held` / `mounted` / `lap` | Phát hiện hành vi cầm hoặc thao tác điện thoại khi đang lái xe | Có nếu thấy cạnh/góc/màn hình; dễ nhầm với vật tối nếu thiếu chi tiết |

## OMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng OMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `person` (`seat`, `seatbelt`) | 5 vị trí ghế; `not_worn` / `unknown` (SVIRO) | Đếm số hành khách theo từng ghế và kiểm tra thắt dây an toàn hàng sau | Có (nhận ra hình dạng người ngồi, mất thông tin màu áo) |
| 2 | `face` (OMS) | bounding box vùng mặt | Xác nhận vị trí đầu/mặt hành khách để phân biệt người thật với đồ vật | Có (thấy rõ khối đầu và mặt khi không cúi sâu vào bóng tối) |
| 3 | `child_seat` (`seat`, `occupied`) | vị trí ghế + `no` / `unknown` (hoặc `yes` ở schema mở rộng) | Nhận biết ghế có gắn ghế trẻ em để điều chỉnh cảnh báo an toàn | Có (rõ kết cấu tựa lưng và đệm ghế trẻ em) |
| 4 | `infant_seat` (`seat`, `facing`) | vị trí ghế + `rear` / `front` | Cảnh báo bỏ quên trẻ sơ sinh trong xe và hỗ trợ logic túi khí theo hướng nôi | Có (rõ khung nôi xách và tay cầm) |
| 5 | `object` (`type=bag`) | balo / túi xách theo `seat` | Phân biệt hành lý đặt trên ghế với người ngồi, tránh báo nhầm thắt dây | Có (nhận dạng bằng hình khối túi, không còn màu sắc vải) |
| 6 | `object` (`type=box`) | thùng / hộp theo `seat` | Phát hiện hàng hoá/thùng đồ cồng kềnh đặt trên ghế hành khách | Có (thấy rõ cạnh vuông góc, mất màu bìa carton) |
| 7 | `object` (`type=cup`) | cốc / ly theo `seat` | Phát hiện vật nhỏ dễ rơi đổ hoặc bỏ quên trên băng ghế | Có nếu đủ tương phản; vật nhỏ tối màu dễ lẫn vào nền ghế |

## 2 câu hỏi leo thang

1. Câu hỏi: Guideline §3 nêu bộ SVIRO không có trẻ em ngồi và `child_seat.occupied` chỉ dùng `no`/`unknown` (không có `yes`), nhưng trong dữ liệu thực tế nhận được có mô hình trẻ em/em bé ngồi trong `infant_seat` và `child_seat`. Khi gán chính thức có gán đồng thời cả `child_seat`/`infant_seat` (`occupied=yes`) lẫn `person` + `face` bên trong không, hay chỉ giữ box ghế?
   Khung ví dụ: `S02_rgb.png`, `S03_rgb.png`, `S05_rgb.png`, `S07_rgb.png`, `S08_rgb.png`.
   Bạn tạm gán thế nào và vì sao: Gán đầy đủ cả box `child_seat`/`infant_seat` (với `occupied=yes` trên `S03`, `S05` theo schema JSON mở rộng) và box `person` (+ `face` nếu thấy mặt) theo đúng quy tắc tách đối tượng ở §4.8 để không bỏ sót hành khách nhỏ tuổi.
2. Câu hỏi: Trên ảnh hồng ngoại gần (NIR), tròng kính râm có thể truyền qua ánh sáng IR khiến mắt vẫn hiện mờ mờ giống kính cận (`clear`), hoặc tròng kính cận bị phản xạ LED che gần hết mắt khiến khó chắc loại kính. Trường hợp không phân biệt chắc chắn giữa `clear` và `sun` dưới ánh sáng NIR thì quy ước gán `clear` hay bắt buộc đưa về `unknown`?
   Khung ví dụ: `D05.png`, `D01.png`.
   Bạn tạm gán thế nào và vì sao: Tạm gán `glasses=clear` vì vẫn thấy khung gọng mỏng và ánh xuyên mờ qua tròng giống kính cận ở `D02`–`D04`, nhưng ghi chú lại để hỏi người viết guideline chuẩn hoá ngưỡng phân biệt `clear`/`sun`/`unknown` trên ảnh NIR.
