# 📝 **Write-up: ติดตั้ง XFCE + XRDP บน Proxmox LXC เพื่อใช้กับ Guacamole**

คู่มือนี้ใช้ได้กับ **Ubuntu 22.04 / 24.04 LXC** บน Proxmox และเชื่อมเข้า Desktop ผ่าน **Guacamole (RDP)**

----------

# ✅ **1) เปิดฟีเจอร์ใน LXC ตอนสร้าง**

ใน Proxmox → เมื่อสร้าง LXC ให้เปิด:

`Features: ✔ Nesting
✔ Fuse
✔ Keyctl` 

ถ้าสร้างแล้ว ให้แก้ไฟล์:

`nano /etc/pve/lxc/CTID.conf` 

เพิ่ม:

`features: nesting=1,fuse=1,keyctl=1` 

แล้ว:

`pct restart CTID` 

----------

# ✅ **2) อัปเดตระบบและติดตั้ง XFCE Desktop**

`apt update  && apt upgrade -y
apt install xfce4 xfce4-goodies -y
apt install lightdm -y` 

เลือก LightDM

----------

# ✅ **3) ติดตั้ง XRDP**

`apt install xrdp -y` 

เพิ่มสิทธิ์สำคัญ:

`adduser xrdp ssl-cert` 

----------

# ✅ **4) ติดตั้ง Xorg ที่ XRDP ต้องใช้**

`apt install xorgxrdp xserver-xorg-core xserver-xorg -y` 

เปิดสิทธิ์ใช้ Xorg:

`echo  "allowed_users=anybody" > /etc/X11/Xwrapper.config` 

----------

# ✅ **5) แก้ startwm.sh เพื่อบังคับใช้ XFCE**

`nano /etc/xrdp/startwm.sh` 

ลบทั้งหมดแล้วใส่:

`#!/bin/sh  
if [ -r /etc/profile ]; then 
. /etc/profile 
fi  
unset DBUS_SESSION_BUS_ADDRESS 
unset XDG_RUNTIME_DIR
startxfce4` 

ตั้งสิทธิ์:

`chmod +x /etc/xrdp/startwm.sh` 

----------

# ✅ **6) เปิด DBus สำหรับ LXC**

`apt install dbus-x11 -y` 

----------

# ✅ **7) ตั้งค่า session ให้ user ที่จะ login (สำคัญที่สุด)**

สมมติ user ชื่อ `ncwjj`:

`su - ncwjj echo xfce4-session > ~/.xsession echo  "xfce4-session" > ~/.xinitrc chmod +x ~/.xsession ~/.xinitrc exit` 

----------

# ✅ **8) Restart XRDP**

`systemctl restart xrdp
systemctl restart xrdp-sesman` 

เช็คสถานะ:

`systemctl status xrdp` 

----------

# ✅ **9) ตั้งค่า Guacamole**

สร้าง Connection ใหม่:

-   **Protocol:** RDP
    
-   **Hostname:** IP ของ LXC
    
-   **Port:** 3389
    
-   **Username:** xxxx
    
-   **Password:** (ของคุณ)
    
-   **Security Mode:** RDP
    

ปรับ Performance:

-   Disable wallpaper
    
-   Disable animations
    
-   Disable effects
    

Save แล้ว Connect
