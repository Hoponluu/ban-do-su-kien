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

## Chân cột phải
- `.side-foot` (avatar + "Sản phẩm phi thương mại dành cho cộng đồng của Nhân Lưu", link nhanluu.com) nằm cuối `.side`; `.side` là flex cột nên `margin-top:auto` đẩy nó xuống đáy.

## Giao diện điện thoại (≤900px)
- Nút 2D/3D chỉ hiện "2D" / "3D" (phần chữ dài nằm trong `.lg`, bị ẩn). `#bShot` và `#bJson` nằm trong `.top-act`: trên desktop là `display:contents`, trên điện thoại thành thanh cố định ở đáy màn hình (`.app` có `padding-bottom` tương ứng để nội dung cuối trang không bị che).

## Tạo mới và thẻ gợi ý
- Nút `#bNew` (nút vàng `.btn.primary`, ngay sau tên sự kiện) mở `#newModal` qua `openNew()`; phím `N` ở chế độ chọn 2D, Ctrl/⌘ + N ở mọi chế độ (Chrome, Edge, Safari giữ Ctrl/⌘ + N nên thường không tới được trang). `newDoc()` thay state bằng trang trống (`room.pts` rỗng, không vật thể, không ảnh nền), có `checkpoint()` nên hoàn tác được.
- Thẻ `#starter` hiện khi `isBlank()` (chưa có tường, vật thể, ảnh nền), đang ở chế độ `select` và xem 2D; `updStarter()` gọi trong `renderAll()`, `setMode()`, `setView()`. Khi thẻ hiện thì ô `#hint` ẩn. Bấm × đặt `starterOff` cho tới lần Tạo mới kế tiếp.

## Lưu file và mẫu bản vẽ
- Nút `#bJson` (nhãn "Lưu file") mở `#modal` "Lưu và mở bản vẽ": `#mSave` tải file `<tên>.json` (qua `fileSlug()`, dùng capability `downloads` nếu có), `#mCopy` sao chép, `#mOpen` + `#mFile` mở file, khung dán `#mText` + `#mLoad`. Cả mở file và dán đều qua `loadData(txt, fname)`.
- `TEMPLATES` (mỗi mục `{id, n, d, build}`) hiện trong hộp Tạo mới dưới dạng thẻ có ảnh thu nhỏ (`tplThumb()` dựng bằng `planSVG` với state tạm, đổi id pattern để không trùng bản vẽ chính). Thêm mẫu mới: thêm một mục có `build()` trả về state đầy đủ.

## Ảnh mặt bằng nền
- `state.bg = {id, name, iw, ih, x, y, w, rot, op, lock, show}` (tâm ảnh và chiều rộng tính bằng mét). File ảnh lưu trong IndexedDB (`IDB`, key `bg:<id>`), không nằm trong localStorage. `bgSrc[id]` là object URL để hiển thị, `bgData[id]` là data URL dùng khi chụp ảnh hoặc xuất JSON (ảnh trong SVG xuất ra phải là data URL thì mới vẽ được).
- `bgSVG()` vẽ ảnh ngay trên lưới, dưới tường. Ảnh chỉ kéo được khi chưa khoá và đang ở chế độ `select` (`data-bg`, `drag.t = 'bgmove'`).
- Căn tỉ lệ: `mode = 'cal'`, `calPts`, khung nhập `#calBox`, `calApply()` phóng ảnh quanh điểm đo đầu tiên.
- `bgGc()` xoá ảnh cũ trong IndexedDB nhưng giữ ảnh còn trong lịch sử hoàn tác. PDF đọc bằng pdf.js 2.16.105 từ cdnjs, chỉ tải khi cần (`pdfToBlob()`).

## Ảnh trên màn hình LED
- `it.img = {id, name, iw, ih}` chỉ có ở vật thể `led`. File lưu trong IndexedDB với key `img:<id>` (thu về cạnh dài 2048 px), dùng chung `bgSrc`/`bgData` với ảnh nền. `ledImgEl[id]` là ảnh đã nạp để làm texture.
- `buildItem` case `led`: có ảnh thì mặt màn hình dùng `MeshBasicMaterial` với texture, cắt kiểu cover theo tỉ lệ màn. Ảnh chưa nạp thì gọi `ledRestore()` rồi dựng lại 3D.
- Panel vật thể LED có mục "Ảnh trên màn hình" (`ledSection()`, `bindLed()`, input `#ledFile`). Xuất JSON có tick "Kèm ảnh" thì thêm `img.data`.
- `bgGc()` giữ cả ảnh nền và ảnh LED còn dùng (kể cả trong lịch sử hoàn tác).
- Sân khấu bỏ tấm phông 3,2 m khi có màn LED đặt sát mép sau (trong khoảng 1,2 m), để phông không che màn LED.

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
