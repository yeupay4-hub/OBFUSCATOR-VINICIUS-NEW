VINICIUS — Python Obfuscator / Encoder

VINICIUS — một công cụ mã hoá / obfuscator cho mã Python, thiết kế để nén + mã hoá payload nhiều lớp và xuất ra file Python chứa phần metadata rõ ràng (plaintext) theo yêu cầu.

Mô tả:

Vinicius đóng gói mã Python bằng cách:

Biên dịch sang bytecode (compile + marshal),

Nén nhiều lớp (lzma → gzip → zlib),

Mã hoá Base64 và đảo chuỗi,

Tạo file đầu ra có phần prefix (metadata, kiểm tra hash, loader) và payload (đã mã hoá).

Trong cấu hình hiện tại, một số metadata được giữ nguyên plaintext (không mã hoá) — đây là yêu cầu của bạn để hiển thị/kiểm tra trực tiếp trong file output.

Những biến metadata luôn hiển thị (plaintext)

File output sẽ chứa (dưới dạng gán biến rõ ràng):

_NAME_ = "}__VINICIUS__{"

_OBF_ = "Obfuscator: Vinicius"

_ADMIN_ = "Obfuscator Owner: Nguyen Nhat Nam Anh"

_CONTACT_ = "Telegram: https://t.me/ctevclwar"

_INFO_ = "Facebook: https://www.facebook.com/ng.xau.k25"

Và biến nối __ctevcl_ = ''.join([_NAME_,_OBF_,_ADMIN_,_CONTACT_,_INFO_]) cùng check_block để kiểm tra hash cũng nằm trong phần prefix (không bị mã hoá).

Tính năng chính:

Mã hoá payload nhiều lớp: marshal → lzma → gzip → zlib → base64 → đảo chuỗi.

Tùy chọn anti-crack/anti-debug (noise, checks) — có thể bật/tắt khi build.

Giữ plaintext cho metadata quan trọng theo yêu cầu.

Hạn chế vòng mã hoá để tránh tạo file quá lớn / quá chậm (mặc định/ràng buộc).

Giao diện CLI đơn giản, tương tác.

Thư viện chuẩn: os, sys, zlib, gzip, lzma, base64, marshal, time, hashlib, random, string

Tùy chọn: psutil (cài nếu bạn cần chức năng anti-debug nâng cao)

pip install psutil

Hướng dẫn cài đặt:

Copy Vinicius.py và README.md vào cùng một thư mục.

(Tùy chọn) Cài psutil nếu muốn dùng anti-debug:

pip install psutil

Hướng dẫn sử dụng cơ bản

Chạy script:

python Vinicius.py


Flow tương tác (ví dụ):

Nhập đường dẫn file nguồn (ví dụ example.py).

Chọn bật/tắt Anti-debug (y/n).

Chọn bật/tắt Anti-crack/noise (y/n).

Nhập số vòng mã hoá (nếu có; có giới hạn tối đa).

Kết quả: file output dạng vinicius_<tênfile>.py (nếu trùng tên sẽ tự thêm hậu tố _1, _2...).

Cấu trúc file output

File output gồm:

Prefix (plaintext) — chứa các dòng gán metadata (_NAME_, _OBF_, _ADMIN_, _CONTACT_, _INFO_), __ctevcl_ (nối chuỗi), check_block (so sánh SHA256), loader snippet (ví dụ Loading...) và một decoder lambda. Tất cả phần này không bị mã hoá trong cấu hình hiện tại.

Payload (encoded) — chứa globals_ = <payload mã hoá> và exec((_)(__)) để decode + thực thi payload khi chạy.

Vì vậy, đoạn nối metadata __ctevcl_ = ''.join([...]) và kiểm tra hash sẽ nằm trong phần prefix và không bị mã hoá — đúng theo yêu cầu của bạn.

Bảo mật & lưu ý

Việc giữ metadata ở dạng plaintext tiện cho hiển thị / quản lý nhưng sẽ lộ thông tin cho người đọc file.

Payload nhiều lớp sẽ làm mất thời gian phân tích, nhưng không phải bất khả xâm phạm: marshal + nén tiêu chuẩn có thể bị đảo ngược bởi người có kỹ năng.

Nếu cần bảo mật cao hơn, cân nhắc:

Chuyển phần logic nhạy cảm sang module biên dịch (.pyd/.so) với Cython.

Lấy khóa giải mã từ máy chủ (license) thay vì nhúng tĩnh.

Thêm anti-debug cấp thấp (ptrace detection, kiểm tra sandbox).

Giữ metadata plaintext sẽ khiến __ctevcl_ có thể được so sánh với expected_hash ngay khi chạy — nếu bạn muốn ẩn __ctevcl_ thì cần thay đổi vị trí/điều kiện build.

Ví dụ [cụ thể]:

Giả sử có file example.py

Chạy:

python Vinicius.py

Phần Nhập:
FILE NAME:
ANTI-CRACK?:
ANTI-DEBUG?

Contact:
https://t.me/ctevclwar
