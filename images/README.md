# Hướng dẫn đặt ảnh

Đặt ảnh chụp vào đúng thư mục này với **đúng tên file** dưới đây, README sẽ tự hiển thị.
Định dạng: `.png` (hoặc `.jpg` — nhớ sửa đuôi trong README).

| Tên file | Nội dung ảnh |
|---|---|
| `01-os-vmware.png` | Terminal: `uname -a`, `systemd-detect-virt` (ubuntu + vmware) |
| `02-docker-version.png` | Terminal: `docker --version`, `docker compose version` |
| `03-docker-services.png` | `docker compose ps` (9 container) hoặc Portainer → Containers |
| `04-npm-proxy-hosts.png` | NPM → Hosts → Proxy Hosts (2 host) |
| `05-npm-web2-location.png` | NPM → chi tiết host web2 → Custom Location `/api` → nodered:1880 |
| `06-cloudflare-hostnames.png` | Cloudflare → Tunnels → web1 → **Published application routes** (web1.kh4idev.id.vn → `http://npm:80`) |
| `07-nodered-flow.png` | Node-RED editor: `http in → function → http response` |
| `08-web1.png` | Trình duyệt `https://web1.kh4idev.id.vn` |
| `09-web2-api.png` | Trình duyệt `https://web2.kh4idev.id.vn` (đã bấm nút, hiện bảng) |
| `10-api-json.png` | Trình duyệt `https://web2.kh4idev.id.vn/api/tacke` (JSON) |
| `11-phpmyadmin.png` | phpMyAdmin `http://<IP_VM>:8081` (đã đăng nhập) |
| `12-portainer.png` | Portainer `http://<IP_VM>:9000` (danh sách container) |
| `13-cloudflare-tunnels.png` | Cloudflare → Tunnels & Mesh (web1, web2 = Healthy) |
| `14-cloudflare-web1-healthy.png` | Chi tiết tunnel web1 (Healthy, connector Connected) |
| `15-cloudflare-web2-healthy.png` | Chi tiết tunnel web2 (Healthy, connector Connected) |
| `16-cloudflare-hostnames-web2.png` | Cloudflare → Tunnels → web2 → **Published application routes** (`http://npm:80`) |
