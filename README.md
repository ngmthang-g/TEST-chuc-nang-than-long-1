# Thần Long AUTO-PK v0.1.6

**Base trực tiếp:** source `AUTO-PK v0.1.5` do người dùng cung cấp. Controller base SHA256: `7ab8407c714e31f459cc5ddc77ae3ba249e9cb277c0301565a0d21a5e549c1cd`.

Mục tiêu v0.1.6 là sửa lớp điều phối click đa cửa sổ mà **không đổi flow Auto-PK nghiệp vụ của v0.1.5**.

## Flow nghiệp vụ giữ nguyên từ v0.1.5

1. DEAD mới chạy **HỒI SINH / ĐẦU THAI**; ALIVE bỏ qua hồi sinh.
2. Đi Lâu Lan M5 tới Đỗ Thanh Đằng NPC ID 339.
3. Recovery: TRỊ LIỆU → XN trị liệu 1 → XN trị liệu 2 → AUTO → BUFF? → TẮT BẢNG → AUTO → DỪNG.
4. Đi TỌA PHỤ; tới nơi chạy **PK TẠI TỌA PHỤ → LIÊN MINH TẠI TỌA PHỤ**.
5. Barrier PASS khi mọi acc đang chạy đã PHỤ READY.
6. Tất cả route sang TỌA CHÍNH.
7. Tới CHÍNH chạy **AUTO → PK → AUTO → TRAIN** rồi xác minh AutoFight ON.
8. **XÁC NHẬN RA MAP** vẫn dùng timer + tọa riêng từng acc như v0.1.5.

## Quy tắc click vật lý v0.1.6

Các dòng click màn hình/UI và XÁC NHẬN RA MAP vẫn là **click vật lý chiếm chuột**:

`foreground đúng cửa sổ -> SetCursorPos -> SendInput LEFTDOWN -> LEFTUP`.

Không dùng `PostMessageW`/background click cho các dòng UI này.

> Lưu ý: các command bridge có sẵn từ v0.1.5 như AutoPath/Mount/StopPath/`ClickNpc(339)` được giữ nguyên vì chúng không phải chuỗi click màn hình tranh con trỏ chuột.

## Global CLICK SEQUENCE LEASE — chỉ 1 cửa sổ được auto-click

v0.1.5 chỉ giữ mouse lease trong **một click** rồi nhả ngay sau mouse-up. Điều đó cho phép acc khác chen click vào giữa Delay/Repeat của một macro.

v0.1.6 đổi thành:

- HỒI SINH/ĐẦU THAI repeat group giữ một lease cho cả group.
- Recovery giữ lease từ click đầu tiên tới khi toàn bộ Recovery hoàn tất.
- PK + LIÊN MINH tại TỌA PHỤ giữ lease cho cả sequence.
- AUTO → PK → AUTO → TRAIN tại TỌA CHÍNH giữ lease cho cả sequence.
- Trong lúc một acc giữ lease, **mọi auto-click vật lý của acc khác phải chờ**.
- XÁC NHẬN RA MAP là single click: khi tới hạn nhưng đang có sequence lease thì vẫn giữ trạng thái due và chờ lease rảnh.
- Manual TEST không được chen vào một auto sequence đang giữ lease.

### Không còn FREEZE scheduler toàn tool

Lease **chỉ serialize physical clicks**. Acc khác vẫn tiếp tục:

- đọc snapshot/state;
- FSM/timer;
- AutoPath/StartPath/StopPath;
- Mount/Dismount;
- bridge command không dùng chuột;
- route tới PHỤ/CHÍNH.

Nói cách khác: **một con chuột, nhiều state machine**.

## User Mouse Guard 5 giây

Hook chuột vật lý toàn Windows theo dõi move/click/wheel:

- khi người dùng động chuột, mọi **automatic physical click** bị hoãn cho tới 5 giây sau input vật lý cuối;
- injected mouse event của tool và cursor warp do chính tool tạo được lọc để không tự pause chính nó;
- sequence đang chạy giữ nguyên `step/repeat/index` khi bị hoãn;
- route/snapshot/FSM không bị pause;
- TEST thủ công được phép bỏ qua guard, nhưng vẫn không được chen vào auto sequence lease đang active.

Nếu hook không cài được, tool ghi cảnh báo; global sequence lease vẫn hoạt động.

## Fail-safe chống kẹt lease

- click fail -> nhả lease;
- client map/loading freeze -> nhả lease owner;
- user STOP/F4 pause owner -> nhả lease;
- thiếu tọa ở một row giữa sequence -> nhả lease để không làm acc khác starvation, nhưng giữ nguyên sequence index để sửa tọa rồi retry;
- quét lại client -> nhả lease.

## XÁC NHẬN RA MAP giữ nguyên v0.1.5

Mỗi acc/cửa sổ lưu riêng:

- tọa độ Confirm;
- kích thước client lúc capture để scale;
- `IntervalSec`.

Timer không cần `ValidConfirmUi`, MessageBox, MapID hay snapshot proof. v0.1.6 chỉ thêm arbitration: **timer không được chen click vào một sequence đang giữ chuột**.

## Trạng thái kiểm chứng

- Source audit: **PASS** cho invariant click lease trong code.
- Build/CI: xem GitHub Actions của repo phát triển.
- Runtime trong game: **RUNTIME UNTESTED** cho tới khi test thực tế nhiều cửa sổ xác nhận không chen chuột và flow PK vẫn đúng.
