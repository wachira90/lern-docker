# 📌 **log ของ Docker**

โดยค่าเริ่มต้น Docker จะใช้ **log driver เป็น `json-file`** ซึ่งจะเก็บ log ลงไฟล์ JSON โดยไม่มีการจำกัดขนาดหรือหมุนไฟล์ (rotation) ทำให้ไฟล์ log อาจโตขึ้นเรื่อย ๆ และ **กินพื้นที่ดิสก์เยอะมาก** ถ้าไม่ตั้งค่าจัดการให้ดี‎ ([Khuong Dev][1])

---

## 🛠️ วิธีจำกัดและจัดการ log ของ Docker

### 1. 🌀 ตั้งค่าการหมุนและจำกัดขนาด log แบบ **ทั่วทั้งระบบ Docker**

แก้ไขไฟล์ `daemon.json` ของ Docker เพื่อกำหนดนโยบาย log rotation:

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

📌 ความหมาย:

* `max-size: 10m` → จำกัดขนาดไฟล์ log แต่ละไฟล์ไม่เกิน 10 MB
* `max-file: 3` → เก็บไฟล์หมุนไว้ไม่เกิน 3 ไฟล์

รวมแล้วจะเก็บ log ได้ประมาณ 30 MB ต่อ container ก่อนจะลบไฟล์เก่าสุดอัตโนมัติ‎ ([Khuong Dev][1])

จากนั้น **restart Docker** เพื่อให้ค่ามีผล:

```bash
sudo systemctl restart docker
```

---

### 2. 🧱 ตั้งค่าการจำกัด log เมื่อรันแต่ละ container

ถ้าคุณต้องการตั้งค่าขณะรัน container แต่ละตัว ก็สามารถเพิ่ม option แบบนี้ได้:

```bash
docker run \
  --log-driver=json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  your-image
```

จะได้ผลเหมือนกับการตั้งค่าทั่วระบบ แต่เฉพาะ container นั้น ๆ เท่านั้น‎ ([Khuong Dev][1])

---

### 3. 🔎 หา log ของ container และลบ/ลดขนาดเมื่อมันโตเกินไป

* ตรวจดู path ของ log file:

```bash
docker inspect <container-id> --format='{{.LogPath}}'
```

* ลดขนาด log (truncate) แบบไม่ต้อง restart container:

```bash
sudo truncate -s 0 /path/to/container-id-json.log
```

* หรือจะลบ log ของทุก container พร้อมกัน:

```bash
sudo find /var/lib/docker/containers/ -name "*-json.log" -exec truncate -s 0 {} \;
```

ทั้ง 2 วิธีจะ **ล้าง log เพื่อลดการใช้พื้นที่ทันที**‎ ([Khuong Dev][1])

---

### 📌 เคล็ดลับเพิ่มเติม (จากบทความ)

* สามารถใช้คำสั่งหาไฟล์ log ที่ใหญ่ที่สุดเพื่อดูว่าควรจัดการกับตัวไหนก่อน:

```bash
sudo find /var/lib/docker/containers/ -name "*.log" -exec du -h {} + | sort -hr | head -20
```

ไฟล์ไหนใหญ่จะถูก list ขึ้นมาก่อนให้คุณจัดการได้ง่ายขึ้น‎ ([Khuong Dev][1])

---

### 🧠 สรุปใจความหลัก

✅ Docker log ถ้าไม่จัดการ จะโตไม่จำกัดและกินเนื้อที่ดิสก์
✅ ต้องตั้งค่า log rotation ด้วย `max-size` และ `max-file`
✅ ทำได้ทั้งแบบ global ใน `daemon.json` และ per-container
✅ สามารถลบ/truncate log เมื่อมันโตเกินไปได้เอง

---

ถ้าคุณอยากให้สรุปเป็น **ขั้นตอนพร้อมคำสั่งตัวอย่าง (แบบ script)** หรือ **ตัวอย่างใน docker-compose.yml** ก็สามารถบอกได้เลยครับ 😊

[1]: https://dev.ngockhuong.com/posts/how-to-limit-and-manage-docker-container-logs/?utm_source=chatgpt.com "How to Limit and Manage Docker Container Logs | Khuong Dev"
