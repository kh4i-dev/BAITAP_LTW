# Hướng dẫn dựng bài & chụp ảnh (GUI)

> Bài Lập trình web — Docker Compose: nginx + nodered + mariadb + phpmyadmin + cloudflared, 2 website 2 domain.

## 0) Thông tin máy
| Mục | Giá trị |
|---|---|
| VM (VMware) | `<IP_VM>` — user `kh4idev` (pass xem file `_secrets`) |
| Thư mục code trên VM | `/home/kh4idev/lab-web` |
| Domain | `web1.kh4idev.id.vn`, `web2.kh4idev.id.vn` |

## 1) Các dịch vụ đang chạy (địa chỉ để chụp ảnh)
| Dịch vụ | Địa chỉ | Đăng nhập |
|---|---|---|
| Nginx Proxy Manager (UI) | http://<IP_VM>:81 | tài khoản khai trong `.env` |
| Node-RED (editor) | http://<IP_VM>:1880 | (không cần) |
| Portainer | http://<IP_VM>:9000 | tự tạo admin ở lần đầu |
| phpMyAdmin | http://<IP_VM>:8081 | `root` (mật khẩu trong `.env`) |
| Website 1 | https://web1.kh4idev.id.vn | — |
| Website 2 (gọi API) | https://web2.kh4idev.id.vn | — |
| API Node-RED | https://web2.kh4idev.id.vn/api/tacke | — |

## 2) Đã chạy sẵn (bạn KHÔNG cần gõ lệnh)
- 9 container đang chạy: `npm`, `cloudflared-web1`, `cloudflared-web2`, `nodered`, `mariadb`, `phpmyadmin`, `web1_app`, `web2_app`, `portainer`.
- NPM đã có sẵn 2 Proxy Host: `web1` → `web1_app:80`, `web2` → `web2_app:80` (+ location `/api` → `nodered:1880`).
- Node-RED đã có sẵn flow: `http in GET /api/tacke` → `function` → `http response`.

## 3) VIỆC BẠN CẦN LÀM (trên GUI) — QUAN TRỌNG
### A. Route Cloudflare (ĐÃ CẤU HÌNH — domain chạy 200)
Vào: **Cloudflare Zero Trust → Networks → Tunnels** → chọn tunnel → tab **Public Hostnames**. Đích phải là `npm:80` (tên service Docker), **KHÔNG** dùng `localhost:80`:

| Subdomain | Domain | Type | URL |
|---|---|---|---|
| `web1` | `kh4idev.id.vn` | HTTP | `npm:80` |
| `web2` | `kh4idev.id.vn` | HTTP | `npm:80` |

Lưu (Save). Sau đó mở lại `https://web1...` / `https://web2...` là chạy.

### B. Portainer lần đầu
Mở http://<IP_VM>:9000 → đặt user/pass admin → chọn **Local** → xem danh sách container.

## 4) Danh sách ảnh cần chụp (gợi ý theo đề bài)
1. **Cây thư mục code**: mở File Manager tới `/home/kh4idev/lab-web` — thấy `docker-compose.yml`, `.env`, `web1_html/`, `web2_html/`, `nodered/flows.json`.
2. **Mở `docker-compose.yml`** (cả danh sách service).
3. **Danh sách container**: Terminal `docker ps` hoặc Portainer → Containers (9 cái đang chạy).
4. **Cloudflare → Tunnels → Public Hostnames** (2 hostname trỏ `npm:80`).
5. **NPM UI**: Hosts → Proxy Hosts (2 host, mở chi tiết web2 thấy Custom Location `/api`).
6. **Node-RED editor**: flow 3 node (`http in` → `function` → `http response`).
7. **Gọi API**: mở `https://web2.kh4idev.id.vn/api/tacke` → thấy JSON.
8. **Website 1**: `https://web1.kh4idev.id.vn`.
9. **Website 2**: `https://web2.kh4idev.id.vn` → bấm nút → bảng dữ liệu hiện ra.
10. **phpMyAdmin**: http://<IP_VM>:8081 (đăng nhập root).
11. **Portainer**: danh sách container.

## 5) Lệnh tham khảo (nếu cần gõ)
```bash
cd ~/lab-web
docker compose ps          # xem trạng thái
docker compose down        # tắt
docker compose up -d       # bật lại
```
