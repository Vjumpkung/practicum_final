# ไมโครคอนโทรลเลอร์และการพัฒนาเฟิร์มแวร์

## สถาปัตยกรรม AVR

- สถาปัตยกรรมแบบ 8 บิต RISC
- ถูกพัฒนาขึ้น นโดยบริษัท Atmel ในปี 1996

## กระบวนการพัฒนาเฟิร์มแวร์

- สร้าง Source File ด้วย `nano,vim,emacs` หรืออื่นๆ
- complied ด้วย `avr-gcc`
- สกัดรหัสเฟิร์มแวร์ด้วย `avr-objcopy`
- upload firmware ด้วย `avrdude`

## Setup Raspberry Pi

- `ssh pi@ชื่ออุปกรณ์.local`

## ติดตั้งซอฟต์แวร์ที่จำเป็นและแก้ไขสิทธิการเข้าถึงอุปกรณ์ USB
```
sudo apt update
sudo apt install gcc-avr avr-libc binutils-avr avrdude
```

### แก้ไขสิทธิการเข้าถึงบอร์ด MCU (Microcontroller Unit) ผ่านพอร์ต USB โดยสร้างไฟล์ชื่อ 45-objdev.rules ในไดเรคตอรี /etc/udev/rules.d/

```
sudo nano /etc/udev/rules.d/45-objdev.rules
```

### สั่งให้บริการ udev เริ่มต้นทำงานใหม่

```
sudo service udev restart
```