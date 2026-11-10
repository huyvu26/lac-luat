# Lạc Luật

Giao diện tra cứu điều luật bằng React, API bằng FastAPI và bộ tìm kiếm BM25 của nhóm. Bản demo tìm và mở nguồn cho Điều 24–27 trong bộ dữ liệu thử nghiệm. Trang **Hỏi đáp pháp luật** dùng nội dung mẫu. Luồng BM25 nằm ở **Tìm điều luật**.

## Chuẩn bị trên Windows

Cần Git, Python 3.12 và Node.js 20.19 trở lên trong nhánh 20 hoặc Node.js 22.12 trở lên. Các lệnh dưới đây chạy trong PowerShell. Sau khi clone repo này, mở thư mục gốc bằng VS Code.

Từ thư mục gốc, lấy repo BM25 của nhóm vào thư mục `bm25` và chuyển tới phiên bản đã kiểm thử. Nếu đã có đúng repo và commit ở thư mục này, có thể dùng lại.

```powershell
git clone https://github.com/adin-67/vn-labor-law-rag.git bm25
git -C bm25 checkout --detach fee28ef5624cdf7d0a5b55a7bf5f525498ba9f7a
```

## Chạy FastAPI ở terminal 1

```powershell
cd backend
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-lock.txt
$env:RETRIEVAL_MODE = 'b'
.\.venv\Scripts\python.exe -m uvicorn app.main:app --host 127.0.0.1 --port 8001
```

Giữ terminal này mở. Tại trang kiểm tra API, xác nhận có `"retrieval_ready": true` và `"mode": "b"`. [Kiểm tra API](http://127.0.0.1:8001/health)

## Chạy React ở terminal 2

Mở terminal mới ở thư mục gốc của repo.

```powershell
cd frontend
npm.cmd ci
npm.cmd run dev
```

Giữ terminal này mở. Nhập **thử việc bao nhiêu ngày**, bấm **Tìm nguồn** và chọn **Mở nguồn** ở Điều 25. Hộp xem nguồn hiện **Trang PDF 11–12**. [Tìm điều luật](http://127.0.0.1:5173/#/search-demo)

## Thử API và chạy bộ kiểm thử

1. Trong Swagger, mở `POST /api/v1/search` và bấm **Try it out**. Gửi `{"query":"thử việc bao nhiêu ngày","top_k":1}` và kiểm tra Điều 25 đứng đầu. [Swagger](http://127.0.0.1:8001/docs)
2. Lấy `unit_id` từ kết quả rồi gọi `GET /api/v1/sources/{unit_id}`. API trả lại đúng nội dung nguồn.
3. Giữ hai dịch vụ đang chạy, mở terminal thứ ba ở thư mục gốc và chạy lệnh dưới đây. Bộ kiểm thử in 17 dòng `PASS` khi mọi kiểm tra thành công.

```powershell
cd backend
.\.venv\Scripts\python.exe -B -X utf8 .\check_live_bm25.py
```

Nếu `/health` trả `retrieval_ready: false`, kiểm tra `bm25/scripts/search_bm25.py` và tệp dữ liệu trong `bm25/data/processed`. Khi lưu repo BM25 ở thư mục khác, đặt `$env:B_REPO_PATH` thành đường dẫn đầy đủ tới repo trước khi khởi động lại FastAPI.
