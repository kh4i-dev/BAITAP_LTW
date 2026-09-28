# bt01 — Docker Compose: nginx + nodered + mariadb + phpmyadmin + cloudflared

## Yêu cầu đề bài
1. Giả lập Linux OS: Hyper-V, VirtualBox, VMware hoặc WSL.
2. Cài đặt Docker Compose trên OS đó.
3. Cài các dịch vụ: nginx, nodered, mariadb, phpmyadmin, cloudflared (cần domain).
4. Cấu hình nginx chạy 2 website với 2 domain khác nhau.

## Mô hình luồng dữ liệu

```text
Client (Internet)
   │
   ▼
Cloudflare Edge (HTTPS web1 / web2.kh4idev.id.vn)
   │
   ▼ (chui qua Tunnel)
Container: cloudflared
   │ (đẩy vào cổng 80 nội bộ Docker)
   ▼
Container: npm (Nginx Proxy Manager - cổng 80)
   ├── Host = web1.kh4idev.id.vn ──> phpmyadmin (cổng 80)
   └── Host = web2.kh4idev.id.vn ──> web2_app (cổng 80)
                                        └── /api ──> nodered (cổng 1880)
```

`cloudflared` chỉ đẩy mọi request vào **duy nhất cổng 80 của `npm`**; NPM đọc tên miền (`Host`) để điều phối tiếp vào container tương ứng.

## Cấu trúc thư mục
```
bt01_docker-compose/
├── docker-compose.yml
├── .env.example          # mẫu, commit được
├── .env                  # token/mật khẩu thật, KHÔNG commit (đã gitignore)
├── _secrets/             # token tunnel cũ (gitignore) - KHÔNG nằm trong web root
│   ├── web1.env
│   └── web2.env
├── web1_phpmyadmin/      # Website 1 (trỏ tới phpmyadmin)
└── web2_nodered/         # Website 2 (index.html được nginx mount làm web root)
```

## Chạy
```bash
cp .env.example .env      # rồi điền token + mật khẩu thật vào .env
docker compose up -d
```

Truy cập:
- NPM UI: `http://<IP_may>:81` (đăng nhập bằng tài khoản khai trong `.env`).
- Portainer / Node-RED / phpMyAdmin: nên mở qua subdomain (tunnel) thay vì map port ra host.

## Cấu hình Cloudflare (Zero Trust → Networks → Tunnels → Public Hostnames)
Thêm 2 public hostname, cả hai cùng trỏ HTTP về `npm:80`:
- `web1` · `kh4idev.id.vn` → `npm:80`
- `web2` · `kh4idev.id.vn` → `npm:80`

## Cấu hình Nginx Proxy Manager (NPM UI)
**Proxy Host 1 — web1:**
- Domain: `web1.kh4idev.id.vn` · Scheme `http` · Forward `phpmyadmin:80`
- Bật `Block Common Exploits`, `Websockets Support`

**Proxy Host 2 — web2:**
- Domain: `web2.kh4idev.id.vn` · Scheme `http` · Forward `web2_app:80`
- Bật `Block Common Exploits`, `Websockets Support`
- Tab **Custom Locations** → Add: location `/api`, scheme `http`, forward `nodered:1880`
  (nhờ vậy JS gọi `/api/...` không dính CORS)

## Lưu ý bảo mật (quan trọng)
- **Token Cloudflare là bí mật.** Nếu đã từng dán/lộ ra ngoài → vào Zero Trust → Tunnels → **Refresh token** rồi tạo lại.
- Không để token/mật khẩu thẳng trong `docker-compose.yml`; truyền qua `.env` (`env_file`).
- Repo là **public** → kiểm tra `git status` trước khi commit, chắc chắn `.env` đã bị ignore.
- Khi đã có tunnel, **không cần map** `80/443/9000/1880` ra host (giảm rủi ro, tránh đụng cổng).
