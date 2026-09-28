# web1 — phpMyAdmin

Website 1 (`web1.kh4idev.id.vn`) trỏ tới container **phpmyadmin** (đã định nghĩa trong `../docker-compose.yml`).

Không cần file tĩnh ở đây — NPM forward `web1.kh4idev.id.vn` → `phpmyadmin:80`.

Nếu muốn web1 là một **website tĩnh riêng**, tạo `index.html` tại đây và thêm service nginx mount thư mục này, rồi trỏ NPM về service đó.
