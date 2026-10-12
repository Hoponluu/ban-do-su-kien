# Mặt Bằng Sự Kiện

Công cụ vẽ mặt bằng không gian sự kiện theo kiểu kéo thả trên 2D, kèm mô phỏng 3D khái quát. Dùng để phác nhanh bố trí sân khấu, gian hàng, bàn ghế, trang trí và luồng di chuyển, kiểm tra diện tích và sức chứa, rồi chụp ảnh minh họa gửi khách hàng hoặc đội vận hành.

Toàn bộ công cụ nằm trong một file `index.html`, không cần build và không cần server. Mở trực tiếp bằng trình duyệt hoặc đăng lên GitHub Pages là chạy.

## Chạy thử

- **Trên máy:** mở `index.html` bằng Chrome, Edge, Safari hoặc Firefox. Cần có mạng để tải thư viện 3D và font.
- **Trên GitHub Pages:** vào Settings → Pages của repo, chọn Source là "Deploy from a branch", branch `main`, thư mục `/ (root)`. Trang sẽ chạy ở `https://<tên-tài-khoản>.github.io/<tên-repo>/`.

## Tính năng

### Không gian
- Nhập chiều dài × chiều rộng để tạo phòng hình chữ nhật.
- Công cụ **Vẽ tường** (phím `W`) vẽ đa giác tự do, tự bắt thẳng góc và hiện chiều dài từng cạnh.
- Nhấp lên tường để thêm điểm góc rồi kéo để tạo góc mới. Nhấp đúp vào một điểm góc để xoá điểm đó.
- Kích thước từng cạnh hiển thị trực tiếp trên bản vẽ. Chiều cao trần dùng cho phần 3D.

### Thư viện vật thể (25 loại)
| Nhóm | Vật thể |
|---|---|
| Sân khấu & kỹ thuật | Sân khấu, màn LED, loa đứng, bàn điều khiển |
| Gian hàng | Gian hàng 3×3, gian hàng 3×2, ki-ốt tròn, quầy lễ tân |
| Bàn ghế | Bàn tròn 10 khách, bàn dài, ghế, khối ghế khán giả, chiếu ngồi |
| Trang trí | Chậu cây, chậu hoa, cổng chào, chùm đèn lồng, bình phong, thảm lối đi |
| Tiện ích & kết cấu | Bàn buffet, cột, cửa ra vào, vùng chức năng |
| Người · đối chiếu tỉ lệ | Người nam 1,7 m, người nữ 1,6 m |

Trên 2D, vật thể chỉ thể hiện chỗ chiếm và diện tích, có mũi tên chỉ mặt trước. Trên 3D, mỗi loại có hình khối khái quát riêng.

### Luồng di chuyển
- Có 4 loại luồng: khách tham quan, khách VIP, nhân sự · hậu cần, thoát hiểm.
- Vẽ bằng công cụ **Luồng** (phím `F`) hoặc bấm thẻ luồng trong thư viện. Nhấp lần lượt từng điểm theo hướng đi, nhấp đúp hoặc Enter để kết thúc.
- Kéo đường để dời cả luồng, kéo điểm để chỉnh hình. Nhấp đúp lên đường để thêm điểm.
- Panel phải hiện chiều dài và thời gian đi bộ ước tính (1 m/giây).
- Nút **Luồng: hiện/ẩn** bật/tắt cả lớp luồng trên 2D, 3D và ảnh chụp.

### Thao tác chọn và chỉnh sửa
- Kéo trên nền trống để quét chọn nhiều vật thể. Shift + nhấp để thêm hoặc bớt.
- Kéo tay nắm của khung nhóm để co giãn hàng loạt. Giữ Shift để giữ tỉ lệ.
- Căn trái, phải, trên, dưới, giữa và phân bố đều cho nhóm vật thể.
- Giữ Alt (Windows) hoặc Option (macOS) khi kéo để nhân bản.
- Panel thuộc tính cho chỉnh kích thước, góc xoay, vị trí, màu và tên hiển thị.

### Kiểm tra mặt bằng
- Vật thể chồng lấn nhau hoặc nằm ngoài tường được tô gạch chéo đỏ.
- Thống kê gồm diện tích sàn, diện tích vật thể chiếm chỗ, diện tích lưu thông còn lại, số chỗ ngồi, số m² sàn trên mỗi chỗ ngồi và số luồng di chuyển. Danh mục vật thể có thể thu gọn.

### Mô phỏng 3D
- Ba góc nhìn có sẵn: tổng quan, từ trên xuống, tầm mắt người đi (1,65 m).
- Camera tự do: lăn chuột để zoom theo con trỏ, W A S D để đi lại, Q/E để hạ/nâng, Shift để đi nhanh, nhấp đúp để bay đến một điểm.
- Tường có 3 chế độ: mờ, đặc, ẩn. Nhãn tên có thể bật/tắt.

### Chụp ảnh và dữ liệu
- **Chụp ảnh** tạo một file PNG rộng 2400 px, gồm góc nhìn hiện tại (2D hoặc 3D), mặt bằng tổng thể có đánh dấu vị trí camera hoặc khung đang xem, và các số liệu chính.
- **Dữ liệu** xuất toàn bộ bản vẽ ra JSON để sao chép, và nạp lại JSON để mở bản vẽ.
- Bản vẽ tự lưu trong trình duyệt (localStorage). Mỗi trình duyệt và mỗi địa chỉ trang có một bản lưu riêng.

### Hướng dẫn & thông tin
- Nút **ⓘ** ở góc phải thanh công cụ mở bảng hướng dẫn nhanh (sử dụng, vẽ, xem 2D & 3D, lưu & xuất ảnh) và phần giới thiệu tác giả, kèm nút dẫn về [nhanluu.com](https://nhanluu.com).

## Tác giả

Tool được tạo bởi **Nhân Lưu**, người sáng lập TELOS Academy, trung tâm đào tạo UI/UX Product Design. Đội ngũ dùng tool này để phác sơ đồ khi lên kế hoạch sự kiện. Xem thêm tại [nhanluu.com](https://nhanluu.com).

## Phím tắt

| Phím | Tác dụng |
|---|---|
| `V` / `W` / `F` | Chọn / Vẽ tường / Vẽ luồng |
| `Space` + kéo, chuột phải, vuốt trackpad | Dời bản vẽ |
| `Ctrl`/`⌘` + cuộn, `+` / `−` | Phóng to / thu nhỏ |
| `Shift` + `1` | Vừa khung |
| `R` / `Shift` + `R` | Xoay 90° / −90° |
| `Ctrl`/`⌘` + `D` | Nhân bản |
| `Ctrl`/`⌘` + `A` | Chọn tất cả |
| `Delete` / `Backspace` | Xoá |
| Phím mũi tên | Nhích 0,1 m (giữ Shift: 1 m) |
| `Ctrl`/`⌘` + `Z`, `Ctrl`/`⌘` + `Shift` + `Z` | Hoàn tác / Làm lại |
| `Enter` / `Esc` / `Backspace` khi vẽ | Kết thúc / Huỷ / Lùi 1 điểm |
| `W A S D Q E` ở chế độ 3D | Di chuyển camera |

## Cấu trúc dữ liệu

Bản vẽ được lưu dưới dạng JSON. Đơn vị là mét, gốc toạ độ ở góc trên bên trái, trục y hướng xuống trên mặt bằng.

```json
{
  "v": 1,
  "name": "Hội chợ Tết · Chợ Quê 2027",
  "room": { "pts": [[0,0],[32,0],[32,20],[0,20]], "h": 5 },
  "items": [
    { "id": "a1b2c3d", "k": "booth33", "x": 1.5, "y": 6.5, "w": 3, "d": 3, "h": 2.5,
      "rot": 270, "label": "Gốm Bát Tràng", "color": "" }
  ],
  "flows": [
    { "id": "f1g2h3i", "type": "guest", "name": "Khách tham quan",
      "pts": [[16,19.6],[16,16.8],[4.5,16.8]] }
  ],
  "showFlows": true
}
```

- `room.pts`: các điểm góc tường theo thứ tự. `room.h` là chiều cao trần.
- `items[].k`: mã loại vật thể, khớp với `CATALOG` trong code. `x`, `y` là tâm vật thể. `w`, `d`, `h` là rộng, sâu, cao. `rot` là góc xoay tính bằng độ, chiều kim đồng hồ.
- `flows[].type`: `guest`, `vip`, `staff` hoặc `exit`. Điểm đầu tiên trong `pts` là nơi bắt đầu.

## Công nghệ

- HTML, CSS và JavaScript thuần trong một file, không có bước build.
- [three.js r128](https://threejs.org/) và OrbitControls, tải từ CDN, dùng cho phần 3D.
- Font Be Vietnam Pro và JetBrains Mono từ Google Fonts.
