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

## Nhiều phòng
- Phòng 0 là `state.room.pts` (+ `state.room.name`) để file cũ vẫn mở được; các phòng thêm ở `state.rooms = [{id, name, pts}]`. Luôn đi qua `roomList()` (mỗi phòng `{k, name, pts}`), `roomPts(k)`, `allRoomPts()`, `totalArea()`; đừng đọc `state.room.pts` trực tiếp cho việc tính toán chung.
- Ô góc tường `data-v="k:i"`, cạnh tường `data-e="k:i"`, `drag.k` cho biết đang kéo góc của phòng nào. `nearRoomVertex()` cho điểm vẽ và góc kéo bắt dính vào góc của phòng khác (chung vách).
- `finishWall()`: `wallAdd` = true thì thêm phòng, false thì thay toàn bộ. Nút `#wallTg` trong ô gợi ý đổi qua lại.
- 3D dựng sàn từng phòng; vách chung chỉ dựng một lần (khử trùng theo cặp điểm). 2D bỏ số đo trùng trên vách chung, hiện tên + diện tích từng phòng khi có từ 2 phòng.
- `validate()`: vật thể nằm ngoài khi có điểm không thuộc phòng nào.

## Ánh sáng & truss
- `truss` (khung cổng: 2 chân + xà ngang ở độ cao h), `trussbar` (thanh treo ở độ cao h), `beam`, `par` (đèn treo ở độ cao h, chiếu về +z, nghiêng xuống). Cả 4 có cờ `hang` nên không tính chồng lấn. Chùm sáng 3D là nón `AdditiveBlending` + vệt sáng trên sàn, không dùng SpotLight thật để nhẹ máy. Màu đèn lấy `it.color` hoặc `c3`.

## Bàn sự kiện và gian 4,5 m
- `etable` (Bàn sự kiện 1,6 m): chiều rộng w được chia thành `round(w/1,6)` bàn; 2D vẽ vạch chia, 3D dựng từng bàn có khăn phủ. Panel hiện "Số bàn 1,6 m". Dùng một vật thể cho cả dãy bàn của một đơn vị.
- `booth45` (Gian hàng 4,5×4,5) dùng chung builder 3D với `booth33`.
- Mẫu `bookstreet` (`bookStreetTpl()`): trục 150 × 16 m, dãy gian 4,5 m hai bên (y 0–4,5 và 11,5–16), lòng đường 7 m ở giữa; vị trí sân khấu, cây xanh, cụm bàn, khu trò chơi quy đổi từ bản Excel của BTC.

## Chụp ảnh mặt bằng dài (cắt đoạn)
- `shotLayout()` quyết định bố cục từ `contentBox()` và lựa chọn `#sLay` (auto/one/strip), `#sN` (số đoạn). Auto: cắt đoạn khi dài/sâu ≥ 3. Số đoạn mặc định: mặt bằng ngang `√(L/1,5D)`, dọc `√(L/D)`, giới hạn 2–6.
- `shotStrip()` vẽ: (góc nhìn 3D nếu đang ở 3D) + dải tổng thể có số đoạn + từng đoạn (dùng lại `planSVG` với `view` riêng, gối `ov` ≤ 1,5 m làm mờ) + thước đo mét. `shotHeader()` dùng chung cho cả hai bố cục.

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

## Vật thể 3D của người dùng (.glb)
- Chỉ nhận `.glb` (glTF 2.0 nhị phân, texture nhúng), tối đa 20 MB (`MODEL_MAX`); cảnh báo khi quá 200k tam giác (`TRI_WARN`). Đuôi khác báo lỗi kèm cách xuất GLB (`EXT_TIP`). Meshopt, KTX2 và file tham chiếu ngoài bị từ chối; Draco nạp `DRACOLoader` khi cần.
- `GLTFLoader` (three@0.128.0, jsdelivr) chỉ tải khi cần (`getLoader()`). `parseGLB()` kiểm tra header rồi dựng; `normModel()` bỏ đèn/camera, đổi skinned mesh thành mesh thường, đặt đáy ở y = 0 và tâm ngang ở gốc, gắn `userData.shared` để `disposeTree()` không huỷ geometry dùng chung.
- File lưu IndexedDB key `model:<id>`; thư viện “Vật thể của tôi” là `MLIB` trong localStorage key `matbang-sukien-models` (`{id, name, w, d, h, nw, nd, nh, tris, size, thumb}`), dùng lại ở mọi bản vẽ trên trình duyệt đó. Ảnh thu nhỏ dựng bằng `modelThumb()`.
- Vật thể trên bản vẽ: `k: 'custom'` (`CM.custom`, nhóm màu `mine`), `it.model = {id, name, nw, nd, nh}` (kích thước gốc tính bằng mét, dùng cho “Về tỉ lệ gốc”). Tên hiển thị qua `nameOf(it)`. 3D: `buildItem` case `custom` clone `modelTpl[id].obj` và co giãn theo w/d/h; chưa nạp thì khối mờ, `modelRestore()` nạp rồi dựng lại.
- Hộp thoại tải lên `#modelModal` (`modelImport()`, `renderModelModal()`): đoán đơn vị cm/mm khi mô hình quá lớn, nhập kích thước thật, tick giữ tỉ lệ. Panel vật thể có `modelSection()`/`bindModel()`, nút “Chọn lại file .glb” khi máy chưa có file (`modelRelink()`).
- Xuất JSON có tick “Kèm ảnh và mô hình 3D”: thêm `models: {id: dataURL}` ở cấp gốc (mỗi file một lần). `loadData()` lưu lại vào IndexedDB, thêm vào thư viện và xoá `s.models` khỏi state. `bgGc()` giữ `model:` còn trong thư viện, bản vẽ hoặc lịch sử hoàn tác.

## Đặt vật thể lên sân khấu / bàn
- Chỉ áp dụng cho danh sách cố định: `SURF` là vật thể làm mặt đỡ (`stage`; bàn `roundtable`, `longtable`, `etable`, `buffet`, `counter`), `ON[stage|table]` là vật thể được đặt lên (sân khấu: loa, LED, bàn điều khiển, cây, hoa, ghế, bàn, bình phong, người, vật thể 3D; bàn: chỉ hoa và vật thể 3D).
- Điều kiện: tâm vật thể nằm trong mặt đỡ. `calcSupport()` (gọi trong `validate()` và `build3D()`) dựng `supOf` (id → `{p, top}`), cho phép chồng tối đa 3 tầng (hoa trên bàn trên sân khấu). `elevOf(it)` là độ cao đặt trên 3D.
- Vật đặt trên không tính chồng lấn với nơi đỡ (`restsOn()`), không tính vào diện tích chiếm chỗ, vẽ 2D sau nơi đỡ (`rank`). Panel hiện "Đặt trên … +x m".

## Thêm một loại vật thể mới
1. Thêm mục vào `CATALOG`.
2. Nếu cần hình 3D riêng, thêm `case` trong `buildItem`.
3. Nếu cần chi tiết riêng trên 2D, thêm nhánh trong `detail()`.

## Lưu ý
- Giữ three.js ở bản r128, vì `examples/js/controls/OrbitControls.js` (bản không dùng module) đã bị bỏ từ r148.
- Nhãn 3D dùng sprite có `sizeAttenuation: false` để giữ cỡ cố định trên màn hình.
- Sau mỗi thay đổi, mở trang trong trình duyệt, thử ở cả 2D và 3D, và thử chụp ảnh.
