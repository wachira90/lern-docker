# 📦 **Using Multiple Secrets in `docker-compose.yml`**

You can define and use multiple secrets very easily. Here's a full example:

```yaml
services:

  app:
    image: myapp:latest
    secrets:
      - db_password
      - api_key
      - oauth_token
    deploy:
      replicas: 1

secrets:
  db_password:
    file: ./db_password.txt

  api_key:
    file: ./api_key.txt

  oauth_token:
    file: ./oauth_token.txt
```

## 📌 What happens here:

* The secrets are defined in the `secrets` section.
* Each secret file is provided.
* The service lists all secrets under `secrets`.
* When the container starts, Docker will mount each secret under:

```
/run/secrets/db_password
/run/secrets/api_key
/run/secrets/oauth_token
```

Your app inside the container can read them using simple `cat` commands or read files from those paths in your code.

Example inside container:

```bash
cat /run/secrets/db_password
cat /run/secrets/api_key
cat /run/secrets/oauth_token
```

---

# 🚀 **Advanced Example with environment variables**

You can also read them dynamically via environment variables if your app supports reading from `*_FILE` style env vars:

```yaml
version: '3.7'

services:
  app:
    image: myapp:latest
    environment:
      DB_PASSWORD_FILE: /run/secrets/db_password
      API_KEY_FILE: /run/secrets/api_key
      OAUTH_TOKEN_FILE: /run/secrets/oauth_token
    secrets:
      - db_password
      - api_key
      - oauth_token

secrets:
  db_password:
    file: ./db_password.txt
  api_key:
    file: ./api_key.txt
  oauth_token:
    file: ./oauth_token.txt
```

In your app you can now read `DB_PASSWORD_FILE`, `API_KEY_FILE`, etc., and get the secret contents.

---

# 📌 Notes

* You can use **as many secrets as you want**.
* Secrets are **only accessible to services that declare them**.
* Each secret is a file in `/run/secrets/`.
* This only works when deploying with `docker stack deploy`, not with `docker-compose up`.

---

If you want, I can also show you:
✅ **Best practices for naming and managing many secrets**
✅ **How to update secrets cleanly without downtime**

Just say "Yes, show best practices too".


---

# 📦 **การใช้งานหลาย Secrets ใน `docker-compose.yml`**

คุณสามารถกำหนดและใช้งาน **Secrets หลายตัวพร้อมกัน** ได้ง่ายมาก ตัวอย่างดังนี้:

```yaml
version: '3.7'

services:
  app:
    image: myapp:latest
    secrets:
      - db_password
      - api_key
      - oauth_token
    deploy:
      replicas: 1

secrets:
  db_password:
    file: ./db_password.txt

  api_key:
    file: ./api_key.txt

  oauth_token:
    file: ./oauth_token.txt
```

## 📌 สิ่งที่เกิดขึ้น

* กำหนด secrets แต่ละตัวในส่วน `secrets` ของไฟล์ compose
* ระบุไฟล์ที่เก็บข้อมูลลับแต่ละตัว
* ใน service (app) ระบุ secrets ที่จะนำมาใช้งาน
* เมื่อ container เริ่มทำงาน Secrets เหล่านี้จะถูก mount เป็นไฟล์ที่ path:

```
/run/secrets/db_password
/run/secrets/api_key
/run/secrets/oauth_token
```

คุณสามารถอ่านค่า secrets ได้จากภายใน container ด้วยคำสั่ง:

```bash
cat /run/secrets/db_password
cat /run/secrets/api_key
cat /run/secrets/oauth_token
```

---

# 🚀 **ตัวอย่างเพิ่มเติมแบบใช้ environment variables**

ถ้า application ของคุณรองรับการอ่าน environment variables (เช่น `*_FILE`) คุณสามารถทำแบบนี้:

```yaml
version: '3.7'

services:
  app:
    image: myapp:latest
    environment:
      DB_PASSWORD_FILE: /run/secrets/db_password
      API_KEY_FILE: /run/secrets/api_key
      OAUTH_TOKEN_FILE: /run/secrets/oauth_token
    secrets:
      - db_password
      - api_key
      - oauth_token

secrets:
  db_password:
    file: ./db_password.txt
  api_key:
    file: ./api_key.txt
  oauth_token:
    file: ./oauth_token.txt
```

ในแอปของคุณสามารถอ่าน path เหล่านี้แล้วดึงค่า secret ได้เลย

---

# 📌 หมายเหตุสำคัญ

* สามารถใช้ secrets ได้ **ไม่จำกัดจำนวน** ตามต้องการ
* Secrets จะถูกมองเห็นเฉพาะ service ที่กำหนดไว้เท่านั้น
* แต่ละ secret จะถูก mount เป็นไฟล์ใน `/run/secrets/`
* การใช้งาน secrets ในรูปแบบนี้ **ต้องใช้ docker swarm mode** และ deploy ด้วยคำสั่ง:

```bash
docker stack deploy -c docker-compose.yml mystack
```

ถ้าใช้ `docker-compose up` แบบธรรมดา **จะไม่รองรับ secrets**

---

ถ้าต้องการ ผมยังสามารถอธิบายเพิ่มได้อีกว่า:
✅ **แนวทางปฏิบัติที่ดีเมื่อมีหลาย secrets (Best practices)**
✅ **วิธีอัปเดต secrets โดยไม่ต้องหยุดระบบ (Zero downtime)**

เพียงแค่พิมพ์ว่า "ใช่ ขอ best practice ด้วย" เดี๋ยวผมอธิบายต่อให้เลยครับ 🚀
