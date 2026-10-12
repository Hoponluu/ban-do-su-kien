# Ghi chú cho Claude Code

Dự án là một web app một file: toàn bộ HTML, CSS và JS nằm trong `index.html`. Không có package.json, không build, không test tự động. Chủ dự án là designer UI/UX, giao tiếp bằng tiếng Việt. Mọi chữ hiển thị trong giao diện viết bằng tiếng Việt.

## Đưa lên GitHub
1. `git init`, commit `index.html`, `README.md`, `CLAUDE.md`, `.gitignore`, `.nojekyll`.
2. Tạo repo (ví dụ `mat-bang-su-kien`) rồi push lên nhánh `main`.
3. Nếu chủ dự án muốn có link xem trực tiếp: bật GitHub Pages từ nhánh `main`, thư mục root. File `.nojekyll` đã có sẵn.
4. Hỏi chủ dự án trước khi chọn repo public hay private, và trước khi thêm LICENSE.

## Bố cục code trong `index.html`
- **CSS tokens** ở đầu `<style>`: màu theo theme sáng/tối (`--c-*` cho nhóm vật thể, `--fl-*` cho loại luồng). Mọi màu đi qua token.
- **Màu nhấn**: `--accent` (#FFC945, vàng thương hiệu) chỉ dùng cho mảng nền (nút chính, nhãn, logo), chữ trên nền vàng dùng `--accent-ink`. Viền, tay nắm, chữ nhấn dùng `--accent-line` (#8A5F00 ở theme sáng để đủ tương phản trên nền sáng, #FFC945 ở theme tối).
- **Tài nguyên**: `assets/` chứa logo, favicon, avatar tác giả và ảnh OG. Logo trong ảnh chụp được vẽ lại bằng canvas trong `takeShot()`.
- **`CATALOG`**: thư viện vật thể. Mỗi mục có `k` (mã), `n` (tên), `w/d/h` (mét), `c` (nhóm màu), cùng các cờ: `shape:'circle'`, `dir` (có mặt trước), `flat` (vật thể nền, không tính chồng lấn hay chiếm chỗ), `hang` (treo trên cao), `ref` (người đối chiếu tỉ lệ), `seats`, `c3` (màu mặc định trên 3D).
- **`FT`**: các loại luồng di chuyển.
- **State**: `state = {name, room:{pts,h}, items[], flows[], showFlows}`, tự lưu vào localStorage với key `matbang-sukien-v1`. Hoàn tác/làm lại dùng snapshot JSON (`checkpoint()`, `undo()`, `redo()`).
- **Chọn**: `S` (Set id vật thể), `selFlow` (id luồng). `mode` nhận một trong `select`, `wall`, `flow`.
- **2D**: `planSVG(W, H, ex)` dựng chuỗi SVG theo `view = {s, ox, oy}` (s là số px trên 1 m). `ex = true` là bản xuất ảnh, không có tay nắm và vùng chọn. `render2D()` gắn chuỗi này vào `#plan`. Các sự kiện con trỏ xử lý trên `svg` theo các loại `drag.t`: `pan`, `marq`, `move`, `rot`, `size`, `vert`, `gscale`, `fvert`, `fmove`.
- **Kiểm tra**: `validate()` dùng SAT (`sepAxis`) để phát hiện chồng lấn và `inPoly` để phát hiện vật thể ra ngoài tường.
- **3D**: `enter3D()`, `build3D()`, `buildItem(it)` (mỗi loại vật thể một `case`), `buildFlows()` và `placeFlows(dt)` cho mũi tên chạy. Camera tự do nằm ở `step3D(dt)`, `bindFree3D()` và `floorHit()`. Toạ độ 2D `(x, y)` ứng với 3D `(x, 0, y)`, `rotation.y = -rot`.
- **Chụp ảnh**: `takeShot()` ghép ảnh trên canvas. `saveShot()` dùng capability `downloads` khi chạy trong Claude artifact, ngoài ra tải bằng thẻ `<a download>`.

## Mô hình người
- `HUMAN` chứa lưới tam giác của người nam và nữ (Quaternius "Background Posed Humans", CC0), toạ độ nguyên, chiều cao chuẩn hoá 1000, mặt trước hướng +z. Mỗi vật liệu một phần: `skin`, `shirt`, `pants`, `shoes`, `hair`. `humanPart()` dựng và lưu sẵn geometry; trong `buildItem`, màu áo lấy theo `it.color`.
- Muốn đổi tư thế hay kiểu tóc: tải bản OBJ của pack, gom mỗi `usemtl` thành một phần, chuẩn hoá chiều cao về 1000, rồi thay dữ liệu trong `HUMAN`.

## Thêm một loại vật thể mới
1. Thêm mục vào `CATALOG`.
2. Nếu cần hình 3D riêng, thêm `case` trong `buildItem`.
3. Nếu cần chi tiết riêng trên 2D, thêm nhánh trong `detail()`.

## Lưu ý
- Giữ three.js ở bản r128, vì `examples/js/controls/OrbitControls.js` (bản không dùng module) đã bị bỏ từ r148.
- Nhãn 3D dùng sprite có `sizeAttenuation: false` để giữ cỡ cố định trên màn hình.
- Sau mỗi thay đổi, mở trang trong trình duyệt, thử ở cả 2D và 3D, và thử chụp ảnh.
