# Configure Orange Pi 3 as a Local Storage 

## A. Setting up auto-mount partitions
We need to take note the `UUID`,`type` within this command of desired partitions:
```
$ lsblk -o NAME,FSTYPE,UUID
NAME         FSTYPE UUID
sda                 
└─sda1       ext4   af9e4319-395b-47bc-a408-823b32ed5fff
sdb                 
└─sdb1       xfs    f2768dc7-6009-468d-b19b-635470dc32e3
mmcblk2             
└─mmcblk2p1  ext4   1511502b-9f88-4e08-932a-78606ab7fbfe
mmcblk2boot0        
mmcblk2boot1        
zram0               
zram1
```


We create mount points in `/mnt` (e.g. `/mnt/hdd1tb`)
```
$ mkdir /mnt/hdd1tb
```
Supposing that we want to mount the partition `sdb1` in mount-point `/mnt/hdd1tb`, we add the last lines in the file `/etc/fstab` via `sudo nano /etc/fstab` (adaptively change your `UUID`, `mount-point`, `type`) as follows:

```
# <file system>                                 <mount point>   <type>  <options>                                                       <dump>  <pass>
tmpfs                                           /tmp            tmpfs   defaults,nosuid                                                 0       0
UUID=1511502b-9f88-4e08-932a-78606ab7fbfe       /               ext4    defaults,noatime,commit=600,errors=remount-ro,x-gvfs-hide       0       1

UUID=f2768dc7-6009-468d-b19b-635470dc32e3       /mnt/hdd1tb     xfs     defaults,sync,auto,nosuid,rw,nouser                             0       0
```

We need to activate the mount:
```
$ sudo mount -a
```
We need to reboot the pi to check the automount works or not (?!)
```
$ sudo reboot
```

## B. Setting Samba to share folder across devices
We install the samba for **file sharing only**.
```
$ sudo apt-get install samba
```
We confirm the installation via checking the version of samba
```
$ samba -V
```

We backup the default config of samba
```
$ sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
```

Supposing that we want to share the directory `/mnt/hdd1tb`, we edit the file `smb.conf` (with `sudo nano /etc/samba/smb.conf`)

```
[global]
workgroup = WORKGROUP
server string = Samba Server %v
netbios name = orangepi
security = user
dns proxy = no

[hdd1tb]
  path = /mnt/hdd1tb
  browseable = yes
  writable = yes
  valid users = @users
  force group = users
  create mask = 0777
  directory mask = 0777
```
We need to add *dedicated users* for smb service. For example, we add a user `pi` for smb service:
```
$ sudo smbpasswd -a pi
```

We restart the `smb` service
```
$ sudo systemctl restart smbd.service
$ /etc/init.d/smbd restart
```

## C. Install & Configure deluge

### 1. Install deluge
Như thông lệ, trước mỗi cài đặt gì mới chúng ta nên thực hiện update và upgrade repositories. Các bạn chạy lệnh sau
```
$ sudo apt-get update  
$ sudo apt-get upgrade
```

Sau khi update, bạn thực hiện cài đặt Deluge lên Raspberry Pi của mình
```
$ sudo apt-get install deluged  
$ sudo apt-get install deluge-console
```
Khi có thông báo xác nhận cài đặt, bạn bấm Y rồi Enter để quá trình cài đặt được thực hiện. Sau khi cài đặt xong, bạn cần chạy các lệnh sau
```
$ deluged  
$ sudo pkill deluged
```
Lệnh đầu tiên sẽ khởi động Deluge cho bạn (đồng thời khởi tạo file cấu hình mặc định) và lệnh thứ hai thì … tắt nó đi bởi bạn cần phải thiết lập một chút mới có thể sử dụng được. 

### 2. Configure deluge
Sau khi đã tắt, bạn thực hiện sử file config của Deluge. Trước hết, chúng ta tiến hành backup file config này để có thể rollback lại nếu cần, sau đó chạy lệnh sau để sửa
```
$ cp ~/.config/deluge/auth ~/.config/deluge/auth.old  
$ nano ~/.config/deluge/auth
```
Trình Nano Editor mở ra, các bạn thêm dòng sau vào file config
```
user:password:level
```
Trong đó user là user bạn muốn chạy với Deluge, password là mật khẩu bạn thiết lập, level là cấp độ phân quyền cho Deluge (cái này mình cũng không hiểu lắm, nhưng được khuyến cáo là để level 10 tức là full quyền truy cập). Ví dụ hoàn thiện cho thiết lập này như sau:

`pi:raspberry:10`

Sau khi sửa xong nội dung trên, bạn lưu lại file với lệnh Ctrl + X và chạy các lệnh sau để tiến hành khởi động phần mềm và thiết lập ban đầu
```
$ deluged  
$ deluge-console
```
Nếu bạn chạy lệnh deluge-console mà có thông báo lỗi thì hãy gõ lệnh exit và chạy lại lệnh deluged để đảm bảo Deluge đã được khởi động.

Sau khi vào được console của Deluge, bạn chạy tiếp 2 lệnh sau rồi tiến hành exit khỏi console:
```
$ config -s allow_remote True  
$ config allow_remote  
$ exit
```









## APPENDIX
We check available disks, which were plugged in, with following command
```
$ lsblk
NAME         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda            8:0    0   4.6T  0 disk 
└─sda1         8:1    0   4.6T  0 part /mnt/hdd5tb
sdb            8:16   0 931.5G  0 disk 
└─sdb1         8:17   0 931.5G  0 part /mnt/hdd1tb
mmcblk2      179:0    0   7.3G  0 disk 
└─mmcblk2p1  179:1    0   7.2G  0 part /
mmcblk2boot0 179:32   0     4M  1 disk 
mmcblk2boot1 179:64   0     4M  1 disk 
zram0        252:0    0 994.8M  0 disk [SWAP]
zram1        252:1    0    50M  0 disk /var/log
```

```
$ lsblk -o NAME,FSTYPE,UUID
NAME         FSTYPE UUID
sda                 
└─sda1       ext4   af9e4319-395b-47bc-a408-823b32ed5fff
sdb                 
└─sdb1       xfs    f2768dc7-6009-468d-b19b-635470dc32e3
mmcblk2             
└─mmcblk2p1  ext4   1511502b-9f88-4e08-932a-78606ab7fbfe
mmcblk2boot0        
mmcblk2boot1        
zram0               
zram1
```

```
$ sudo fdisk -l
Disk /dev/mmcblk2: 7.3 GiB, 7818182656 bytes, 15269888 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xdbc45241

Device         Boot Start      End  Sectors  Size Id Type
/dev/mmcblk2p1       8192 15106047 15097856  7.2G 83 Linux


Disk /dev/sdb: 931.5 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: Generic DISK01  
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: C7A1573D-2C74-4EB2-9E20-A5EDB822E516

Device     Start        End    Sectors   Size Type
/dev/sdb1   2048 1953523711 1953521664 931.5G Linux filesystem


Disk /dev/sda: 4.6 TiB, 5000981078016 bytes, 9767541168 sectors
Disk model: Generic DISK00  
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: D88D090F-5F66-4415-A37E-EFF46578B71D

Device     Start        End    Sectors  Size Type
/dev/sda1   2048 9767540735 9767538688  4.6T Linux filesystem


Disk /dev/zram0: 994.8 MiB, 1043091456 bytes, 254661 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes


Disk /dev/zram1: 50 MiB, 52428800 bytes, 12800 sectors
Units: sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes

```
