# vps-setup

Bản tùy biến của [system_optimization-v2.sh](https://github.com/bibicadotnet/Docker-LCMP-Multisite-WordPress-Minimal/blob/main/system_optimization-v2.sh) (từ [bibicadotnet/Docker-LCMP-Multisite-WordPress-Minimal](https://github.com/bibicadotnet/Docker-LCMP-Multisite-WordPress-Minimal)).

**Khác biệt so với bản gốc:**

- Không tắt IPv6 (đã bỏ block sysctl `disable_ipv6`).
- Hỗ trợ Debian 13 (trixie): tự tạo `/etc/sysctl.conf` và symlink `/etc/sysctl.d/99-sysctl.conf` nếu thiếu (Debian 13 đã bỏ cả hai), để bước sysctl không lỗi và cấu hình được nạp lại sau reboot.
- Sửa bước cập nhật OS: bản gốc lọc theo tên suite (`noble-updates` → `noble`) nên không bao giờ nâng cấp gói nào. Nay lọc theo Label của repo trong `apt-get -s upgrade` (Ubuntu/Debian/Debian-Security/Debian Backports), vẫn bỏ qua repo bên thứ ba như Docker.
- Chỉ `clear` khi chạy trong terminal, để chạy được qua `ssh host 'cmd'` (không có TTY/`TERM`).

Mọi tối ưu khác (DNS, BBR, swap/sysctl theo RAM, Docker daemon.json, backup/restore) giữ nguyên như bản gốc.

## Sử dụng

```bash
curl -fsSL https://raw.githubusercontent.com/aikiata/vps-setup/main/system_optimization-v2.sh -o system_optimization-v2.sh
chmod +x system_optimization-v2.sh
sudo ./system_optimization-v2.sh
```

Xem thông tin hệ thống mà không chạy tối ưu:

```bash
sudo ./system_optimization-v2.sh --info
```

## Lưu ý

- Script khóa cứng `/etc/resolv.conf` (`chattr +i`, DNS `8.8.8.8`/`1.1.1.1`) và tắt `systemd-resolved`. Nếu sau này cài **Tailscale**, chạy `chattr -i /etc/resolv.conf` trước khi `tailscale up` để MagicDNS hoạt động, hoặc dùng `tailscale up --accept-dns=false` để giữ nguyên DNS đã khóa.
- Backup cấu hình gốc + script khôi phục được tạo tại `/opt/vps-setup-backup-<timestamp>/restore.sh` mỗi lần chạy.
