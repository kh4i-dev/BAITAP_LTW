# BẰNG CHỨNG THỰC HIỆN — LẬP TRÌNH WEB (BÀI 1 & 2)

> Log lệnh chạy trực tiếp trên VM **Ubuntu 22.04** (VMware). Hình ảnh minh chứng xem ở [README](../README.md).

---

## BÀI 1 — BƯỚC 1: Giả lập hệ điều hành Linux

```text
$ cat /etc/os-release
PRETTY_NAME="Ubuntu 22.04.5 LTS"
VERSION="22.04.5 LTS (Jammy Jellyfish)"

$ uname -a
Linux kh4idev-linux 6.8.0-40-generic #40~22.04.3-Ubuntu SMP PREEMPT_DYNAMIC Tue Jul 30 17:30:19 UTC 2 x86_64 x86_64 x86_64 GNU/Linux

$ systemd-detect-virt
vmware

$ lsb_release -a
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.5 LTS
Release:        22.04
Codename:       jammy
```

## BÀI 1 — BƯỚC 2: Cài đặt Docker & Docker Compose

```text
$ docker --version
Docker version 29.8.1, build 4a63305

$ docker compose version
Docker Compose version v5.5.1

$ docker info
Server Version: 29.8.1 | Storage Driver: overlayfs | OS: Ubuntu 22.04.5 LTS
```

## BÀI 1 — BƯỚC 3: Các dịch vụ đã cài bằng docker compose

```text
$ docker compose ps
NAME               IMAGE                             STATUS
cloudflared-web1   cloudflare/cloudflared:latest     Up
cloudflared-web2   cloudflare/cloudflared:latest     Up
mariadb            mariadb:10.11                     Up (healthy)   3306/tcp
nodered            nodered/node-red:latest           Up (healthy)   0.0.0.0:1880->1880/tcp
npm                jc21/nginx-proxy-manager:latest   Up             80/tcp, 443/tcp, 0.0.0.0:81->81/tcp
phpmyadmin         phpmyadmin:latest                 Up             0.0.0.0:8081->80/tcp
portainer          portainer/portainer-ce:latest     Up             0.0.0.0:9000->9000/tcp
web1_app           nginx:alpine                      Up             80/tcp
web2_app           nginx:alpine                      Up             80/tcp
```

## BÀI 1 — BƯỚC 4: nginx chạy 2 website với 2 domain

```text
$ GET /api/nginx/proxy-hosts        (Nginx Proxy Manager)
  id=1  domains=['web1.kh4idev.id.vn']  ->  http://web1_app:80  locations=[]
  id=2  domains=['web2.kh4idev.id.vn']  ->  http://web2_app:80  locations=['/api']

$ Kiểm tra nginx trả đúng website theo Host:
  web1.kh4idev.id.vn  ->  <title>Web 1 - kh4idev</title>
  web2.kh4idev.id.vn  ->  <title>Web 2 - Node-RED API Consumer</title>

$ mariadb --version
mariadb  Ver 15.1 Distrib 10.11.19-MariaDB, for debian-linux-gnu (x86_64)
```

---

## BÀI 2 — BƯỚC 1: Node-RED tạo API bằng http in + http response

```text
$ Đếm node trong nodered/flows.json
      1  type = http in
      1  type = function
      1  type = http response
      1  type = debug

$ GET http://127.0.0.1:1880/api/tacke
{"ok":1,"msg":"thành công","dssv":[{"name":"Trần Văn Khải","money":999999},{"name":"Nguyễn Văn An","money":123},{"name":"Lê Thị Bình","money":456}]}
```

## BÀI 2 — BƯỚC 2: nginx proxy /api sang Node-RED (JS gọi API không dính CORS)

```text
$ GET web2.kh4idev.id.vn/api/tacke        (qua Nginx Proxy Manager)
{"ok":1,"msg":"thành công","dssv":[{"name":"Trần Văn Khải","money":999999},{"name":"Nguyễn Văn An","money":123},{"name":"Lê Thị Bình","money":456}]}
```

---

## CLOUDFLARE TUNNEL (công bố ra Internet)

```text
$ docker ps | grep cloudflared
cloudflared-web1   Up
cloudflared-web2   Up

$ cloudflared-web1 — ingress đang dùng
  {"ingress":[{"hostname":"web1.kh4idev.id.vn","service":"http://npm:80"},{"service":"http_status:404"}]}

$ cloudflared-web2 — ingress đang dùng
  {"ingress":[{"hostname":"web2.kh4idev.id.vn","service":"http://npm:80"},{"service":"http_status:404"}]}

$ Kiểm tra truy cập công khai từ Internet (HTTPS)
  https://web1.kh4idev.id.vn             -> HTTP 200
  https://web2.kh4idev.id.vn             -> HTTP 200
  https://web2.kh4idev.id.vn/api/tacke   -> {"ok":1,"msg":"thành công","dssv":[...]}
```

> Ghi chú: `cloudflared` và `npm` cùng mạng Docker nên đích đúng là `http://npm:80`
> (không dùng `http://localhost:80` — trong container thì `localhost` là chính nó).
