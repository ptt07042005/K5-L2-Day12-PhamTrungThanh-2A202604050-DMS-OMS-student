# Tự đánh giá — Phạm Trung Thành · G01

## 1. Tự rà soát 4 cảnh

Loại lỗi: `seat_flip`, `wrong_class`, `guess`, `box`, `missing`, `extra`, `lr_flip` (trái/phải mặt), `flag` (thiếu cờ điểm), `ok`.

| Ảnh | Đối tượng | Loại lỗi | Ghi chú | Đã sửa? |
|---|---|---|---|---|
| `S03_rgb` / `S03_ir` | `face` | `box` | Box mặt trẻ em ban đầu hơi cao/rộng xuống phần cổ áo; đã thu gọn lại sát trán–cằm. | Có |
| `S05_rgb` / `S05_ir` | `face` | `box` | Hai box mặt ở `rear_right` và `rear_left` hơi trùm quá phần đầu/cổ; đã chỉnh sát vùng mặt ở cả bản RGB và IR. | Có |
| `D01.png` | `face13` | `flag`, `box` | Các điểm mắt và miệng trên ảnh xoay 90° cần căn lại sát mép mũi/môi và đảm bảo 8 điểm mắt dưới kính phản xạ/tối có cờ `occluded=1`. | Có |
| `D02.png` | `hand` / `phone` | `wrong_class` | Vật cầm sát miệng tài xế là chai nước trong suốt, dễ nhìn nhầm thành điện thoại; đã kiểm tra kỹ và không gán `phone`, chỉ giữ `hand`. | Có |

## 2. Lỗi mình lặp lại nhiều nhất hôm nay

Lỗi: Căn box `face` hơi lỏng và đặt lệch một vài điểm `face13` trên ảnh tài xế bị xoay 90°.

Vì sao mình mắc: Trên ảnh `D01`–`D06` xoay ngang 90°, mắt người dễ mất định hướng chiều trên/dưới của mặt và miệng; còn trên `S##` trẻ em cúi đầu nên dễ kéo box trùm xuống cổ.

Lần sau tránh bằng cách: Luôn phóng to vùng mặt trước khi đặt điểm, kiểm tra quy ước `y` (mắt trái có `y` lớn hơn mắt phải trên ảnh `D##`), và rà lại đường biên trán–cằm, tai–tai cho box `face`.

## 3. Ghi chú về disagreement_log

Qua 20 dòng `diff` với Lê Tuấn Anh (`2A202604064`), ở các dòng `S02_ir` và `S07_ir` (`person.seatbelt`), bạn chọn `unknown` (thận trọng khi vùng ngực nằm sâu trong nôi/tối) còn mình chọn `not_worn` (theo đặc thù SVIRO không có dây an toàn). Mình học được rằng không nên suy mặc định `not_worn` chỉ vì biết đặc trưng dataset; khi vùng ngực bị che khuất thực sự trên khung hình đó thì cân nhắc `unknown` theo luật `4.4-1` sẽ an toàn và khách quan hơn cho model. Ngoài ra, đối chiếu `S02_rgb/ir` (`face`) và `D01`/`D02` (`person`, `face13`) giúp mình kiểm chứng lại việc giữ nguyên `export_final.zip` là hợp lệ, không cần xuất `v2`.
