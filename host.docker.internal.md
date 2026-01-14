# การใช้งาน `--add-host=host.docker.internal:host-gateway` พร้อมตัวอย่างใช้งาน

---

## 1. Docker คืออะไร (สั้นมาก)

Docker คือเครื่องมือที่ใช้รันแอปพลิเคชันใน **container**
ซึ่ง container จะมี environment แยกจากเครื่องเรา (host) เช่น

* มี network ของตัวเอง
* localhost ใน container ≠ localhost ของเครื่องเรา

จุดนี้แหละครับที่คำสั่ง `--add-host` เข้ามาช่วย

---

## 2. ปัญหาที่เจอบ่อย: Container อยากเรียก Host

สมมติว่า:

* คุณรัน Docker container
* แต่ Database / API รันอยู่บนเครื่อง host (เครื่องเราเอง)

ภายใน container:

```text
localhost  = ตัว container เอง
```

ดังนั้นถ้า container เรียก:

```bash
curl http://localhost:8080
```

❌ จะไม่เจอ service บน host

---

## 3. `host.docker.internal` คืออะไร

`host.docker.internal`
เป็น hostname ที่ Docker ใช้แทน **IP ของเครื่อง host**

* บน **Docker Desktop (Mac/Windows)** → มีให้ใช้เลย
* บน **Linux** → ต้อง map เองด้วย `--add-host`

---

## 4. อธิบายคำสั่งนี้ทีละส่วน

```bash
--add-host=host.docker.internal:host-gateway
```

| ส่วน                   | ความหมาย                                             |
| ---------------------- | ---------------------------------------------------- |
| `--add-host`           | เพิ่ม hostname ลงใน `/etc/hosts` ของ container       |
| `host.docker.internal` | ชื่อ host ที่เราจะใช้เรียก                           |
| `host-gateway`         | IP gateway ของ host (Docker จะ resolve ให้อัตโนมัติ) |

ผลลัพธ์คือใน container จะมีบรรทัดนี้:

```text
<host-ip> host.docker.internal
```

---

## 5. ตัวอย่างที่ 1: curl จาก container ไปหา host

สมมติว่า host เปิด API ที่พอร์ต `8080`

### รัน container

```bash
docker run --rm \
  --add-host=host.docker.internal:host-gateway \
  curlimages/curl \
  curl http://host.docker.internal:8080
```

✅ container จะเรียก API บน host ได้ทันที

---

## 6. ตัวอย่างที่ 2: ใช้กับ Application จริง (เช่น Python)

สมมติ app ใน container ต้องต่อ DB ที่รันบน host

### docker run

```bash
docker run -d \
  --name myapp \
  --add-host=host.docker.internal:host-gateway \
  -e DB_HOST=host.docker.internal \
  myapp:latest
```

### ในโค้ด

```python
db_host = os.getenv("DB_HOST")
```

---

## 7. ตัวอย่างที่ 3: ใช้กับ docker-compose

เหมาะมากสำหรับ Dev / Local environment

```yaml
version: "3.9"
services:
  app:
    image: myapp:latest
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

แล้วใน container:

```bash
ping host.docker.internal
```

---

## 8. ใช้เมื่อไรดี?

✅ เหมาะกับ:

* Dev / Local test
* Container ต้องเรียก service บน host
* Linux ที่ไม่มี `host.docker.internal` มาให้

❌ ไม่เหมาะกับ:

* Production (ควรใช้ network หรือ service discovery)
* Kubernetes (มีวิธีอื่นที่เหมาะกว่า)

---

## 9. สรุปสั้น ๆ

* `--add-host=host.docker.internal:host-gateway`
* ทำให้ container คุยกับ host ได้
* แก้ปัญหา `localhost` คนละโลก
* ใช้บ่อยมากใน DevOps / Local dev 🔥

ถ้าคุณอยากได้:

* ตัวอย่างกับ **Database (MySQL/Postgres)**
* Diagram network Docker
* เปรียบเทียบกับ `--network=host`

บอกผมได้เลยครับ 👍
