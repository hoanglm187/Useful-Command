# Useful Command

## Mục lục
- [Disable Password expire policy](#disable-password-expire-policy)
- [Enable offline cpu](#enable-offline-cpu)
- [Sudo no password](#sudo-no-password)
- [Disable selinux](#disable-selinux)

### Disable Password expire policy
```bash
sudo chage -I -1 -m 0 -M 99999 -E -1 <username>
```
- ***-I -1: Vô hiệu hoá thời gian chờ tài khoản hoạt động***
- ***-m 0: Đặt số ngày tối thiểu trước khi đổi mật khẩu là 0***
- ***-M 99999: Đặt số ngày tối đa để đổi mật khẩu là 99999 (tương đương không bao giờ hết hạn)***
- ***-E -1: Vô hiệu hoá ngày hết hạn tài khoản***

### Enable offline cpu
```bash
for cpu in /sys/devices/system/cpu/cpu*/online; do
    if [ "$(cat $cpu)" -eq "0" ]; then
        echo 1 | sudo tee $cpu
    fi
done
```

### Sudo no password
```bash
echo '<username> ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
```

### Disable selinux
```bash
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/selinux/config
```
