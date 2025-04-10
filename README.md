# Useful Command

## Mục lục
- [Disable Password expire policy](#disable-password-expire-policy)
- [Enable offline cpu](#enable-offline-cpu)
- [Sudo no password](#sudo-no-password)
- [Disable selinux](#disable-selinux)
- [Upgrade kernel Ubuntu](#upgrade-kernel-ubuntu)
- [Docker delete all container](#delete-all-container)
- [Docker delete all images](#delete-all-images)
- [Reset Docker](#reset-docker-delete-all-container-image-volume-and-network)
- [MariaDB - Galera Cluster](#mariadb-galera-cluster)
- [Gitlab](#gitlab-list-the-versions-available-from-the-repository)
- [Iptables](#iptables)
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
### Upgrade kernel Ubuntu
```bash
wget https://raw.githubusercontent.com/pimlie/ubuntu-mainline-kernel.sh/master/ubuntu-mainline-kernel.sh
install ubuntu-mainline-kernel.sh /usr/local/bin/
ubuntu-mainline-kernel.sh -r
ubuntu-mainline-kernel.sh -i "version"
```
### Delete all container
```bash
docker ps -aq | xargs docker rm -f
```
### Delete all images
```bash
docker images -q | xargs docker rmi -f
```
### Reset Docker (delete all container, image, volume and network)
```bash
docker system prune -a --volumes
```

# Mariadb (Galera cluster)
- Check cluster status
```sql
SHOW GLOBAL STATUS LIKE 'wsrep_%';
SHOW GLOBAL STATUS LIKE 'wsrep_cluster_size';
SHOW GLOBAL STATUS LIKE 'wsrep_cluster_status';
SHOW GLOBAL STATUS LIKE 'wsrep_local_state_comment';
SHOW GLOBAL STATUS LIKE 'wsrep_ready';
SHOW GLOBAL STATUS LIKE 'wsrep_connected';
SHOW GLOBAL STATUS LIKE 'wsrep_local_state_uuid';
SHOW GLOBAL STATUS LIKE 'wsrep_last_committed';
```
- Check timout info
```sql
SHOW VARIABLES LIKE '%timeout%';
SHOW VARIABLES LIKE '%wait_timeout%';
SHOW VARIABLES LIKE '%interactive_timeout%';
```
- Check aborted info
```sql
SHOW GLOBAL STATUS LIKE 'Aborted_connects';
SHOW GLOBAL STATUS LIKE 'Aborted_clients';
```
- How to determine the latest SEQNO node
```sql
SHOW GLOBAL STATUS LIKE 'wsrep_last_committed';
```
Troubleshoot
---
### **[ERROR] mariadbd: Index for table 'table_name' is corrupt; try to repair it**
- Check the table to confirm the status
```sql
CHECK TABLE db_name.table_name EXTENDED;
```
- Use Repair Table (the safest):
```sql
REPAIR TABLE tio_site_main.chatroom_join_leave;
-- or
REPAIR TABLE tio_site_main.chatroom_join_leave EXTENDED;
```
- After repairing and checking:
```sql
CHECK TABLE db_name.table_name;
```
# Gitlab List the versions available from the repository
- Debian/Ubuntu:
```bash
apt-cache madison gitlab-ce
```
- CentOS/RHEL:
```bash
yum --showduplicates list gitlab-ce
```

# Iptables
- Check iptables status
```bash
for table in filter nat mangle raw security; do
    echo "====== Table: $table ======"
    iptables -t $table -L -v -n --line-numbers
    echo ""
done
```