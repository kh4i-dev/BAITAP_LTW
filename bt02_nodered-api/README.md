# bt02 — Node-RED API + JS gọi API

## Yêu cầu đề bài
1. Dùng Node-RED: node `http in` + `http response` → tạo API đơn giản.
2. Cấu hình nginx để web dùng JS gọi được API Node-RED (thuật toán cho API tự nghĩ).
3. Code JS vào trang HTML để gọi API.

## Luồng Node-RED
```
[http in]  GET /api/tacke  ->  [function]  ->  [http response]
```

Nội dung node **function**:
```javascript
msg.payload = {
    "ok": 1,
    "msg": "thành công",
    "dssv": [
        { "name": "Trần Văn Khải", "money": 999999 },
        { "name": "Nguyễn Văn An", "money": 123 },
        { "name": "Lê Thị Bình", "money": 456 }
    ]
};
return msg;
```
Bấm **Deploy** sau khi nối 3 node.

## Hợp đồng API
- Method: `GET`
- URL: `/api/tacke`
- Response JSON:
```json
{ "ok": 1, "msg": "thành công", "dssv": [ { "name": "Nguyễn Văn An", "money": 123 } ] }
```

## Nối web với API (không lỗi CORS)
Trang HTML (`../bt01_docker-compose/web2_html/index.html`) gọi `fetch('/api/tacke')` bằng **path tương đối**.
NPM đã cấu hình Custom Location `/api` → `nodered:1880`, nên cùng origin, không dính CORS.

## Kết quả
- `https://web2.kh4idev.id.vn` → bấm nút → hiển thị bảng danh sách sinh viên từ Node-RED.
