# 🐋 Lab 1: Docker & Docker Compose

[← กลับหน้าหลัก](README.md)

---

## 🎯 วัตถุประสงค์การเรียนรู้

หลังจากทำการทดลองนี้แล้ว นักศึกษาจะสามารถ:

- [ ] อธิบายแนวคิดพื้นฐานของ Docker และ Container Technology ได้
- [ ] เขียน `Dockerfile` สำหรับ Node.js Backend และ Nginx Frontend ได้
- [ ] เขียน `docker-compose.yml` เพื่อรัน Multi-Service Application ได้
- [ ] ใช้คำสั่ง Docker พื้นฐานในการ Build, Run, Debug Container ได้
- [ ] อธิบาย Docker Network และ Volume ได้
- [ ] ตรวจสอบและแก้ปัญหา Container ด้วย `docker logs` และ `docker exec` ได้
### การเรียนรู้เพื่อฝึกฝนตนเองให้มีความรู้ ความชำนาญ สามารถวิเคราะห์ปัญหาได้ อย่าพึ่งพาแค่ AI โดยที่ไม่มีความรู้พื้นฐาน
### ใช้ AI อย่างชาญฉลาด เพื่อเป็นผู้ช่วยในการเรียนรู้ของตนเอง ทุกคนสามารถใช้ AI ได้ แต่คนที่ มีความรู้พื้นฐาน + AI จะกลายเป็นคนที่อยู่รอดในอนาคต
---

## 📖 สารบัญ

- [ภาคทฤษฎี](#-ภาคทฤษฎี)
  - [1.1 Docker คืออะไร](#11-docker-คืออะไร)
  - [1.2 แนวคิดสำคัญ](#12-แนวคิดสำคัญ)
  - [1.3 โครงสร้าง Dockerfile](#13-โครงสร้าง-dockerfile)
  - [1.4 Docker Compose](#14-docker-compose)
  - [1.5 Docker Volume](#15-docker-volume)
  - [1.6 สถาปัตยกรรมของ Booking App](#16-สถาปัตยกรรมของ-booking-app)
  - [1.7 คำสั่งพื้นฐาน](#17-คำสั่ง-docker-พื้นฐาน)
- [ภาคปฏิบัติ](#-ภาคปฏิบัติ)
  - [ขั้นตอนที่ 1: เตรียมสภาพแวดล้อม](#-ขั้นตอนที่-1-เตรียมสภาพแวดล้อม)
  - [ขั้นตอนที่ 2: Docker พื้นฐาน](#-ขั้นตอนที่-2-ทดลองใช้คำสั่ง-docker-พื้นฐาน)
  - [ขั้นตอนที่ 3: Dockerfile Backend](#-ขั้นตอนที่-3-เขียน-dockerfile-สำหรับ-backend)
  - [ขั้นตอนที่ 4: Dockerfile Frontend](#-ขั้นตอนที่-4-เขียน-dockerfile-สำหรับ-frontend)
  - [ขั้นตอนที่ 5: Docker Compose](#-ขั้นตอนที่-5-สร้าง-docker-compose)
  - [ขั้นตอนที่ 6: รันและทดสอบระบบ](#-ขั้นตอนที่-6-รันและทดสอบระบบ)

- [สรุปและการประเมิน](#-สรุปและการประเมิน)
- [งานเพิ่มเติม](#-งานเพิ่มเติม-challenge)

---

## 📚 ภาคทฤษฎี

### 1.1 Docker คืออะไร

**Docker** คือแพลตฟอร์ม Open-source สำหรับสร้าง ส่ง และรัน Application ใน **Container**

> **Container** = หน่วยซอฟต์แวร์ที่รวมโค้ด + Dependencies + Configuration ทั้งหมดเข้าด้วยกัน ทำให้ Application ทำงานได้เหมือนกันในทุกสภาพแวดล้อม

#### Container vs Virtual Machine

![docker-architecture](images/docker-architecture.png)

| คุณสมบัติ | Container | Virtual Machine |
|----------|-----------|----------------|
| **ขนาด** | เบา (MBs) | หนัก (GBs) |
| **เวลาเริ่มต้น** | วินาที | นาที |
| **การใช้ OS** | Share Host Kernel | OS แยกของตัวเอง |
| **Isolation** | Process Level | Hardware Level |
| **Portability** | สูงมาก | ปานกลาง |
| **Performance** | ใกล้เคียง Native | Overhead มากกว่า |

---

### 1.2 แนวคิดสำคัญ

```
Docker Ecosystem
├── 📦 Image      → Blueprint / Template สำหรับสร้าง Container
├── 🏃 Container  → Instance ที่รันจาก Image
├── 📄 Dockerfile → ไฟล์คำสั่งสำหรับสร้าง Image
├── 🌐 Docker Hub → Registry กลางสำหรับเก็บ/แชร์ Image
├── 💾 Volume     → พื้นที่เก็บข้อมูลถาวรนอก Container
└── 🔗 Network    → Virtual Network สำหรับการสื่อสารระหว่าง Container
```

#### Image Layer System
![docker-architecture](images/image-layer.png)

---

### 1.3 โครงสร้าง Dockerfile

| Instruction | ความหมาย | ตัวอย่าง |
|-------------|---------|---------|
| `FROM` | กำหนด Base Image | `FROM node:20-alpine` |
| `WORKDIR` | กำหนด Working Directory | `WORKDIR /app` |
| `COPY` | คัดลอกไฟล์จาก Host | `COPY package*.json ./` |
| `RUN` | รันคำสั่งระหว่าง Build | `RUN npm install` |
| `ENV` | กำหนด Environment Variable | `ENV PORT=5000` |
| `EXPOSE` | ระบุ Port ที่ Container ใช้ | `EXPOSE 5000` |
| `CMD` | คำสั่งเริ่มต้นเมื่อ Container รัน | `CMD ["node", "server.js"]` |
| `ENTRYPOINT` | Executable หลักของ Container | `ENTRYPOINT ["nginx"]` |
| `ARG` | Build-time Variable | `ARG NODE_ENV=production` |
| `VOLUME` | กำหนด Mount Point | `VOLUME ["/data"]` |

---

### 1.4 Docker Compose

**Docker Compose** คือเครื่องมือสำหรับนิยามและรัน **Multi-Container Application** โดยใช้ไฟล์ `docker-compose.yml`

#### โครงสร้างหลักของ docker-compose.yml

```yaml

services:          # กำหนดแต่ละ Container
  web:
    build: .       # Build จาก Dockerfile
    ports:
      - "3000:80"  # Host:Container Port Mapping
    environment:   # Environment Variables
      - NODE_ENV=production
    volumes:       # Mount Volumes
      - ./data:/app/data
    depends_on:    # ลำดับการเริ่มต้น
      - db
    networks:      # เชื่อมต่อ Network
      - app-net

  db:
    image: postgres:17-alpine   # ตัวอย่าง Image จาก Registry (ใบงานนี้ใช้ SQLite แทน)
    volumes:
      - db_data:/data/db

networks:          # กำหนด Virtual Networks
  app-net:
    driver: bridge

volumes:           # กำหนด Named Volumes
  db_data:
```

#### คำสั่ง Docker Compose ที่สำคัญ

```
docker compose up -d        → เริ่ม Service ทั้งหมด (Background)
docker compose down         → หยุดและลบ Container ทั้งหมด
docker compose down -v      → หยุด + ลบ Volume ด้วย
docker compose build        → Build Images ทั้งหมด
docker compose ps           → ดูสถานะ Service
docker compose logs -f      → ดู Log แบบ Real-time
docker compose exec <svc> sh → เข้าไปใน Container
docker compose restart <svc> → Restart Service เฉพาะ
docker compose config       → ตรวจสอบ Syntax ของ YAML
```

---

### 1.5 Docker Volume

**Docker Volume** คือกลไกที่ Docker ใช้ในการจัดการพื้นที่เก็บข้อมูลแบบ **Persistent** ซึ่งมีอายุยืนยาวกว่า Container และอยู่นอกขอบเขตของ Container Filesystem

> ⚠️ **ปัญหาหลักของ Container**: ข้อมูลที่เขียนใน Container Layer จะ **หายทันที** เมื่อ Container ถูกลบ  
> ✅ **แก้ด้วย Volume**: เก็บข้อมูลไว้นอก Container — ลบ Container กี่ครั้งก็ยังมีข้อมูล

---

#### ประเภทของ Docker Storage

```
Docker Storage Options
│
├── 📦 Volumes          ← Docker จัดการให้ (แนะนำ)
│   ├── Named Volume    ← มีชื่อ, ใช้ร่วมกันได้ระหว่าง Container
│   └── Anonymous Volume← ไม่มีชื่อ, ผูกกับ Container เดียว
│
├── 🗂️  Bind Mounts      ← Mount โฟลเดอร์จาก Host โดยตรง
│   └── /host/path:/container/path
│
└── 🧠 tmpfs Mounts     ← เก็บใน RAM (ข้อมูลหายเมื่อหยุด)
    └── ใช้สำหรับข้อมูลชั่วคราวที่ sensitive
```

---

#### เปรียบเทียบ 3 ประเภท

| คุณสมบัติ | Named Volume | Bind Mount | tmpfs |
|----------|-------------|-----------|-------|
| **ตำแหน่งเก็บข้อมูล** | Docker Area (`/var/lib/docker/volumes/`) | ใดก็ได้บน Host | RAM |
| **ข้อมูลคงอยู่หลัง rm** | ✅ ใช่ | ✅ ใช่ | ❌ ไม่ |
| **แชร์ระหว่าง Container** | ✅ ง่าย | ✅ ได้ | ❌ ไม่ |
| **Performance** | ดีมาก | ดี (Linux) | ดีที่สุด |
| **ใช้สำหรับ** | Database, ข้อมูล App | Source Code (Dev), Config | Session, Cache |
| **Backup ง่าย** | ✅ ใช่ | ✅ ใช่ | ❌ ไม่ |
| **ทำงานข้าม OS** | ✅ ทุก OS | ⚠️ path ต่างกัน | ✅ ทุก OS |

---

#### Volume Lifecycle
![docker-architecture](images/volume-life-cycle.png)

---

#### Named Volume: ทำงานอย่างไร
![docker-architecture](images/name-valume.png)

---

#### Bind Mount: ทำงานอย่างไร
![docker-architecture](images/bind-mount-2.png)

---

#### คำสั่ง Volume ที่สำคัญ

```bash
# ── Create ─────────────────────────────────────────────────
docker volume create my-volume           # สร้าง Named Volume

# ── List & Inspect ─────────────────────────────────────────
docker volume ls                         # แสดง Volume ทั้งหมด
docker volume ls --filter dangling=true  # Volume ที่ไม่มี Container ใช้
docker volume inspect my-volume          # ดูรายละเอียด (path, driver, etc.)

# ── Use in docker run ──────────────────────────────────────
docker run -v my-volume:/app/data image           # Named Volume
docker run -v $(pwd)/data:/app/data image         # Bind Mount
docker run -v /app/data image                     # Anonymous Volume
docker run --mount type=volume,src=my-volume,dst=/app/data image  # --mount syntax

# ── Remove ─────────────────────────────────────────────────
docker volume rm my-volume               # ลบ Volume (ต้อง rm container ก่อน)
docker volume prune                      # ลบ Volume ที่ไม่ได้ใช้ทั้งหมด
docker compose down -v                   # หยุด + ลบ Container + Named Volumes
```

---

#### Volume ใน Docker Compose

```yaml
services:
  backend:
    volumes:
      # รูปแบบที่ 1: Named Volume (แนะนำสำหรับ Database)
      - sqlite_data:/app/data

      # รูปแบบที่ 2: Bind Mount (สำหรับ Development)
      - ./backend:/app

      # รูปแบบที่ 3: Anonymous Volume (ไม่แนะนำ)
      - /app/node_modules

      # รูปแบบที่ 4: Read-only Bind Mount
      - ./config:/app/config:ro

volumes:
  sqlite_data:            # กำหนด Named Volume ระดับ top-level
    driver: local         # ใช้ local storage (default)
    name: booking-sqlite-data   # ชื่อที่แสดงใน docker volume ls
```

---

#### Volume กับ SQLite ใน Booking App
![docker-architecture](images/volume-sqlite.png)

---

#### Volume Backup & Restore

```bash
# ── Backup: Copy จาก Volume ไปยัง tar file ─────────────────
docker run --rm   -v booking-sqlite-data:/source:ro   -v $(pwd):/backup   alpine   tar czf /backup/booking-backup-$(date +%Y%m%d).tar.gz -C /source .

# ── Restore: Copy จาก tar file กลับเข้า Volume ────────────
docker run --rm   -v booking-sqlite-data:/target   -v $(pwd):/backup   alpine   tar xzf /backup/booking-backup-20250801.tar.gz -C /target
```

---

### 1.6 สถาปัตยกรรมของ Booking App

```
booking-app-demo-2025/
├── frontend/               ← Static HTML/CSS/JS (Nginx)
│   ├── index.html
│   ├── css/
│   └── js/
├── backend/                ← REST API (Node.js + Express)
│   ├── server.js
│   ├── models/
│   ├── routes/
│   └── package.json
├── newman/                 ← API Test Collection (Postman)
└── tests/robot/            ← E2E Tests (Robot Framework)
```

#### Data Flow
![docker-architecture](images/booking-app-architecture.png)

> 💡 **SQLite ต่างจาก MongoDB/PostgreSQL** ตรงที่ไม่ต้องรัน Database Server แยก
> ข้อมูลเก็บอยู่ใน **ไฟล์เดียว** (`booking.db`) ภายใน Backend Container

| Service | Image | Port | หน้าที่ |
|---------|-------|------|--------|
| `frontend` | `nginx:alpine` (Custom) | `3000→80` | Serve HTML + Proxy /api/ |
| `backend` | `node:20-alpine` (Custom) | `5000→5000` | REST API + SQLite Database |

---

### 1.7 คำสั่ง Docker พื้นฐาน

```
── Image Management ───────────────────────────────────
docker pull <image>              ดึง Image จาก Registry
docker images                    แสดง Image ทั้งหมด
docker build -t <name> <path>    Build Image จาก Dockerfile
docker rmi <image>               ลบ Image
docker image prune               ลบ Dangling Images

── Container Lifecycle ────────────────────────────────
docker run [flags] <image>       สร้างและรัน Container
docker start <id/name>           เริ่ม Container ที่หยุดอยู่
docker stop <id/name>            หยุด Container
docker restart <id/name>         Restart Container
docker rm <id/name>              ลบ Container (ต้อง Stop ก่อน)
docker rm -f <id/name>           Force ลบ Container

── Inspection & Debugging ─────────────────────────────
docker ps                        แสดง Container ที่รันอยู่
docker ps -a                     แสดงทุก Container
docker logs <id/name>            ดู Log
docker logs -f <id/name>         ดู Log แบบ Real-time
docker exec -it <id> bash/sh     เข้าไปใน Shell ของ Container
docker inspect <id>              ดูรายละเอียดทั้งหมด
docker stats                     ดูการใช้ Resource

── System ─────────────────────────────────────────────
docker system df                 ดูพื้นที่ที่ Docker ใช้
docker system prune              ลบสิ่งที่ไม่ใช้ทั้งหมด
docker network ls                แสดง Network ทั้งหมด
docker volume ls                 แสดง Volume ทั้งหมด
```

---

## 🔬 ภาคปฏิบัติ

> ⚠️ **สำคัญ**: ทำตามลำดับขั้นตอนโดยไม่ข้ามขั้น และบันทึกผลลัพธ์ในช่องที่กำหนดทุกขั้น

---

### 🔵 ขั้นตอนที่ 1: เตรียมสภาพแวดล้อม

**วัตถุประสงค์**: ตรวจสอบ Docker, Git และ Clone Repository

#### 1.0 Terminal ที่ใช้ในใบงาน

ใบงานนี้ใช้คำสั่ง Shell สำหรับ **macOS Terminal** และ **Windows Git Bash** ซึ่งมี syntax เหมือนกันทุกคำสั่ง

> 🪟 **Windows**: ติดตั้ง **Git for Windows** แล้วใช้ **Git Bash** ได้เลย  
> ดาวน์โหลดที่ https://git-scm.com — ทุกคำสั่งในใบงานนี้ทำงานได้ทันทีโดยไม่ต้องแก้ไข

---

#### 1.1 ตรวจสอบการติดตั้ง
**พิมพ์ และรันครั้งละ 1 คำสั่งด้วยตนเอง**
```bash
docker --version
docker compose version
git --version
docker info | head -20
```

<details>
<summary>✅ ผลลัพธ์ที่ควรได้รับ</summary>

```
Docker version 27.x.x, build xxxxxxx    ← v24.0+ ขึ้นไปทั้งหมดใช้ได้
Docker Compose version v2.29.x          ← v2.25+ จะแสดง warning ถ้ามี version:
git version 2.43.0
```
</details>

> **📝 บันทึกผล**: 
```
บันทึกรูปภาพผลการตรวจสอบ version
```![alt text](image.png)
---

#### 1.2 Clone Repository

```bash
# Clone project จาก GitHub
# กรณีมี Folder นี้ที่สามารถรันได้อยู่แล้ว สามารถใช้ Folder เดิมได้ โดยไม่ต้อง Clone ใหม่
git clone https://github.com/surachai-p/booking-app-demo-2025.git

# เข้าไปยัง Directory
cd booking-app-demo-2025

# ดูโครงสร้างไฟล์
ls -la
```

<details>
<summary>✅ โครงสร้างที่ถูกต้อง</summary>

```
drwxr-xr-x  backend/
drwxr-xr-x  frontend/
drwxr-xr-x  newman/
drwxr-xr-x  node_modules/
drwxr-xr-x  reports/
drwxr-xr-x  tests/
-rw-r--r--  package.json
-rw-r--r--  README.md
```
</details>



#### 1.3 สร้าง .env File

สร้างไฟล์ `.env` ที่ Root Directory พร้อมเนื้อหาดังนี้:

```bash
# ── Database (SQLite) ─────────────────────────────────────────
# Path ภายใน Container ที่เก็บ database file
DB_PATH=/app/data/booking.db

# ── Backend ───────────────────────────────────────────────────
NODE_ENV=production
BACKEND_PORT=5000
HOST_BACKEND_PORT=5000

# ── Frontend ──────────────────────────────────────────────────
FRONTEND_PORT=3000
HOST_FRONTEND_PORT=3000
```

```bash
# เพิ่มใน .gitignore (ทุก OS)
echo ".env" >> .gitignore

# ตรวจสอบ
cat .env
```

> ⚠️ **ห้าม Commit `.env` ขึ้น GitHub** เพราะมีข้อมูลที่เป็นความลับ

**แก้ไขไฟล์​ server.js ในส่วนการกำหนด port ถ้าข้อมูลมีการแก้ไขแล้ว ให้ข้ามขั้นตอนนี้**

```bash
const PORT=process.env.BACKEND_PORT || 5000;
//const PORT       = 5000;
```
**หมายเลข Port ที่มีในตำแหน่งอื่น ๆ ให้แทนด้วย ${PORT}**
---


### 🔵 ขั้นตอนที่ 2: ทดลองใช้คำสั่ง Docker พื้นฐาน

**วัตถุประสงค์**: ฝึกใช้คำสั่ง Docker สำหรับการจัดการ Container และ Image

---

#### 2.1 Pull และรัน Nginx Container


```bash
# ดึง nginx:alpine image จาก Docker Hub
docker pull nginx:alpine

# ดู Image ที่ดาวน์โหลดมา
docker images

# รัน Container
# -d = Detached (Background)
# --name = กำหนดชื่อ Container
# -p = Map Port (Host:Container)
# กรณีที่ port 8080 ถูกใช้งานอยู่ สามารถเปลี่ยนเป็น port อื่นได้ เช่น 8081
# กรณีที่ต้องการยกเลิกหรือ kill process ที่ใช้งาน port สามารถใช้คำสั่ง npx kill-port 8080
docker run -d --name my-nginx -p 8080:80 nginx:alpine

# ตรวจสอบ Container ที่กำลังรัน
docker ps
```
![docker maping port](images/docker-map-port.png)

<details>
<summary>✅ ผลลัพธ์ที่คาดหวัง</summary>

```
CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS        PORTS
a1b2c3d4e5f6   nginx:alpine   "/docker-entrypoint.…"   2 seconds ago  Up 2 seconds  0.0.0.0:8080->80/tcp
```
</details>

**บันทึกผลการทดลอง**
```
![alt text](image.png)
```

เปิด Browser ไปที่ **http://localhost:8080** ควรเห็นหน้า `Welcome to nginx!`

**อธิบาย แต่ละส่วนของคำสั่ง docker run -d --name my-nginx -p 8080:80 nginx:alpine**
```text
อธิบายคำสั่งที่นี่

```



---

#### 2.2 ตรวจสอบ Container

```bash
# ดู Log ของ Nginx
docker logs my-nginx

# ดู Log แบบ Follow เพื่อติดตามดู log(กด Ctrl+C เพื่อออก)
docker logs -f my-nginx

# เข้าไปใน Shell ของ Container
docker exec -it my-nginx sh

# คำสั่งภายใน Container (หลัง exec):
ls /usr/share/nginx/html      # ดูไฟล์ที่ Nginx Serve
cat /usr/share/nginx/html/index.html   # ดูเนื้อหา HTML
nginx -v                       # ดู Nginx version
exit                           # ออกจาก Container Shell
```

<details>
<summary>💡 อธิบายคำสั่ง exec</summary>

```
docker exec -it my-nginx sh
            │  │
            │  └── -t: Allocate a pseudo-TTY (terminal)
            └───── -i: Keep STDIN open (Interactive)

เทียบเท่ากับการ SSH เข้าไปใน Container
ทุกอย่างที่ทำใน Shell นี้อยู่ภายใน Container เท่านั้น
```
![docker exec](images/docker-exec-it.png)

**บันทึกผลการรันคำสั่ง docker exec -it และ ls /usr/share/nginx/html**

   ```bash
   บันทึกรูปภาพที่นี่
   ```
</details>

---

#### 2.3 ดูรายละเอียดและ Inspect

```bash
# ดูรายละเอียดทั้งหมดของ Container (JSON format)
docker inspect my-nginx

# ดูเฉพาะ IP Address
docker inspect my-nginx --format='{{.NetworkSettings.IPAddress}}'

# ดูการใช้ Resource (CPU, Memory, Network)
docker stats my-nginx
# กด Ctrl+C เพื่อออก
```

> **📝 บันทึกผล**: Container ID คือ: `............` และ IP Address คือ: `............`

---

#### 2.4 Lifecycle: Stop, Start, Remove

```bash
# หยุด Container
docker stop my-nginx

# ดูสถานะ (ควรแสดง Exited)
docker ps -a

# เริ่ม Container ใหม่
docker start my-nginx

# ดูสถานะอีกครั้ง (ควรแสดง Up)
docker ps

# หยุดและลบ Container
docker stop my-nginx
docker rm my-nginx

# ตรวจสอบ (ไม่ควรมี my-nginx แล้ว)
docker ps -a
```

---

#### 2.5 ทดลอง Node.js Container

```bash
# รัน Node.js Container แบบ Interactive
# --rm = ลบ Container อัตโนมัติเมื่อออกจาก container ด้วยคำสั่ง exit
docker run -it --rm node:20-alpine sh

# ภายใน Container:
node --version
npm --version
node -e "console.log('Hello from Docker! Running in:', process.env.HOSTNAME)"
exit
```
<details>
<summary>💡 ความหมายของ flags ใน docker run</summary>

| Flag | ความหมาย |
|------|---------|
| `-d` | Detached — รันใน Background |
| `-it` | Interactive + TTY — รันแบบ Interactive |
| `--rm` | ลบ Container อัตโนมัติเมื่อหยุด |
| `--name` | กำหนดชื่อ Container |
| `-p host port:container port` | Map Port จาก Host ไปยัง Container |
| `-e KEY=VALUE` | กำหนด Environment Variable |
| `-v host path:container path` | Mount Volume |
| `--network` | กำหนด Network |
</details>

**บันทึกรูปผลการทดลอง**
```text
บันทึกรูปผลการทดลองที่นี่
```
---

### ❓ คำถามท้ายขั้นตอนที่ 2

1. `docker ps` กับ `docker ps -a` ต่างกันอย่างไร?

   > _คำตอบ_: ........................................................................

2. ขนาดของ `nginx:alpine` image คือเท่าไร?

   > _คำตอบ_: ........................................................................

3. flag `--rm` ใน `docker run` มีประโยชน์ในกรณีใด?

   > _คำตอบ_: ........................................................................

4. `docker exec -it` ต่างจาก `docker run -it` อย่างไร?

   > _คำตอบ_: ........................................................................

5. ต้องการดูว่าในเครื่องมี image อะไรอยู่บ้าง จะเขียนคำสั่งอย่างไร
   ```bash
   เขียนคำสั่งที่นี่
   ```
6. ต้องการลบ image ชื่อ nginx:alpine จะต้องเขียนคำสั่งอย่างไร หากมีหลายขั้นตอนที่ต้องเขียน ให้เขียนลำดับคำสั่งให้ถูกต้อง
   ```bash
   เขียนคำสั่งที่นี่
   ```
7. เมื่อลบ image แล้วให้ทำการ pull image กลับมาเหมือนเดิม พร้อมทำการรัน container
   ```bash
   เขียนคำสั่งที่นี่
   ```
---

### 🔵 ขั้นตอนที่ 3: เขียน Dockerfile สำหรับ Backend

**วัตถุประสงค์**: สร้าง Docker Image สำหรับ Node.js Express API

---

#### 3.1 ศึกษา Backend Code

```bash
# ดู Entry Point ของ Application
cat backend/server.js

# ดู Dependencies ทั้งหมด
cat backend/package.json

# ดู API Routes
ls backend
```

<details>
<summary>💡 สิ่งที่ต้องรู้ก่อนเขียน Dockerfile</summary>

จาก `package.json` สังเกตว่า:
- **start script** คืออะไร → ใช้ใน `CMD` จากตัวอย่างคือ `node server.js`
- **node version** ที่ต้องการ → ใช้ใน `FROM` จากตัวอย่างคือ `1.0.0`
- **port** ที่ใช้ → ใช้ใน `EXPOSE`
- **better-sqlite3** ต้องการ compile native module → ต้องเพิ่ม build tools ใน Alpine
</details>

---

#### 3.2 สร้าง `.dockerignore`

สร้างไฟล์ `backend/.dockerignore` พร้อมเนื้อหาดังนี้:

```bash
cat > backend/.dockerignore << 'EOF'
node_modules
npm-debug.log
.env
.env.local
.env.*.local
.git
.gitignore
*.md
coverage/
*.test.js
*.spec.js
EOF
```

```bash
# ตรวจสอบ
cat backend/.dockerignore
```

> **หมายเหตุ**: `.dockerignore` ทำงานเหมือน `.gitignore` แต่สำหรับ Docker Build Context

---

#### 3.3 เขียน Dockerfile สำหรับ Backend

สร้างไฟล์ `backend/Dockerfile` ที่มีเนื้อหาดังนี้:

```bash
# ─────────────────────────────────────────────────────────────
# Stage: Production Environment
# Base Image: node:20-alpine (Active LTS — node:18 EOL เม.ย. 2025)
# ─────────────────────────────────────────────────────────────
FROM node:20-alpine

# ─── Build Tools สำหรับ better-sqlite3 ────────────────────
# better-sqlite3 เป็น Native Module ต้องการ C++ compiler
# python3 + make + g++ ใช้ตอน Build เท่านั้น
RUN apk add --no-cache python3 make g++

# กำหนด Working Directory ภายใน Container
WORKDIR /app

# ─── Install Dependencies ──────────────────────────────────
# คัดลอก package files ก่อน (เพื่อใช้ Layer Cache)
# ถ้า package.json ไม่เปลี่ยน → npm install จะใช้ Cache
COPY package*.json ./

# ติดตั้ง Production Dependencies เท่านั้น
RUN npm install --omit=dev

# ─── Copy Source Code ─────────────────────────────────────
# คัดลอก Source Code (ทำทีหลังเพื่อ Cache Optimization)
COPY . .

# ─── Database Directory ───────────────────────────────────
# สร้างโฟลเดอร์สำหรับเก็บ SQLite database file
RUN mkdir -p /app/data

# ─── Configuration ────────────────────────────────────────
# รวม ENV ไว้บรรทัดเดียว = ลด Layer 1 ชั้น (Best Practice)
ENV NODE_ENV=production PORT=5000 DB_PATH=/app/data/booking.db

# ระบุ Port ที่ Application ฟัง
EXPOSE 5000

# ─── Start Application ────────────────────────────────────
# CMD จะถูกแทนที่ได้เมื่อรัน Container
CMD ["node", "server.js"]
```

```bash
# ตรวจสอบเนื้อหาไฟล์
cat backend/Dockerfile
```

<details>
<summary>💡 ทำไมต้อง COPY package*.json ./ ก่อน?</summary>

```
# แบบไม่ดี (ทุกครั้งที่แก้โค้ด จะ npm install ใหม่)
COPY . .
RUN npm install

# แบบดี (ใช้ Cache ได้)
COPY package*.json ./    ← Layer นี้ Cache ได้ (เปลี่ยนเฉพาะเมื่อ package.json เปลี่ยน)
RUN npm install          ← ใช้ Cache ถ้า Layer ก่อนหน้าไม่เปลี่ยน
COPY . .                 ← Layer นี้เปลี่ยนบ่อย แต่ npm install ไม่ต้องรันใหม่
```

> **📌 หมายเหตุ SQLite**: `better-sqlite3` ต้องการ `python3 make g++` ใน Alpine
> ใน Production ควรใช้ Multi-stage Build เพื่อไม่ให้ build tools ติดไปใน Image สุดท้าย
</details>

**Multi-stage Build คืออะไร?**
```
ตอบคำถามที่นี่
```
---

#### 3.4 Build Backend Image

```bash
# Build Image จาก Dockerfile ใน ./backend
# -t คือ Tag ในรูปแบบ name:version
docker build -t booking-backend:1.0 ./backend

# ดูกระบวนการ Build แบบ Detail
docker build -t booking-backend:1.0 ./backend --progress=plain 2>&1 | head -50

# ตรวจสอบ Image ที่สร้างได้
docker images | grep booking

# ดูรายละเอียด Image (Layer History)
docker history booking-backend:1.0
```

<details>
<summary>✅ ผลลัพธ์ที่คาดหวัง</summary>

```
REPOSITORY         TAG    IMAGE ID       CREATED         SIZE
booking-backend    1.0    abc123def456   5 seconds ago   ~200MB
```
</details>

> **📝 บันทึกผล**: ขนาดของ booking-backend:1.0 คือ: `............` MB 

---

#### 3.5 ทดสอบรัน Backend Container

```bash
# สร้าง Network เพื่อใช้งานร่วมกันระหว่าง backend และ frontend โดยตั้งชื่อ network ว่า booking-network
docker network create booking-network

# การระบุ port  -p 5000:5000 หาก server.js ระบุ port ในการรันคือ 3000 คำสั่งต้องแก้ไขเป็น -p 5000:3000 เพื่อ map จาก port 5000 ของ host ไปยัง port 3000 ของ container backend

docker run -d --name backend --network booking-network -p 5000:5000 -e DB_PATH=/app/data/booking.db -v $(pwd)/test-data:/app/data booking-backend:1.0
```
**คำถามการทดลอง**
**อธิบายคำสั่งในส่วน -e DB_PATH=/app/data/booking.db -v $(pwd)/test-data:/app/data**
```
อธิบายคำสั่งที่นี่
```

```bash
# ดู Log เพื่อตรวจสอบการเริ่มต้น
docker logs backend

# ทดสอบ API (SQLite สร้างไฟล์ .db อัตโนมัติ ไม่ต้องติดตั้ง Server)
curl http://localhost:5000

# ลบ Container ทดสอบ
docker stop backend
docker rm backend
```
#### เปิด web browser แล้วทดสอบเข้าที่ http://localhost:5000/api-docs

**บันทึกผลการทดลอง**
```
บันทึกรูปผลการรัน curl http://localhost:5000
```
---

### ❓ คำถามท้ายขั้นตอนที่ 3

1. `booking-backend:1.0` คืออะไร?

   > _คำตอบ_: ........................................................................

2. ถ้าต้องการให้ ทดสอบ API ด้วย `curl http://localhost` ต้องทำอย่างไร?

   > _คำตอบ_: ........................................................................

---

### 🔵 ขั้นตอนที่ 4: เขียน Dockerfile สำหรับ Frontend

**วัตถุประสงค์**: สร้าง Docker Image สำหรับ Static Website ด้วย Nginx

---

#### 4.1 ศึกษา Frontend Structure

```bash
# ดูโครงสร้าง Frontend
ls -la frontend/
cat frontend/index.html | head -30
```

> Frontend เป็น Static Website (HTML + CSS + JS) ไม่ต้อง Build → ใช้ Nginx Serve โดยตรง

---

#### 4.2 สร้าง Nginx Configuration

สร้างไฟล์ `frontend/nginx.conf` พร้อมเนื้อหาดังนี้:
**เลข Port 5000 คือ Host port ที่จะ map ไปยัง Port ของ backend container กรณีที่ Container run ที่ port อื่นให้เปลี่ยนเลข 5000 เป็นเลข Port ของ backend**
```bash
server {
    listen 80;
    server_name localhost;

    # Root Directory สำหรับ Static Files
    root /usr/share/nginx/html;
    index index.html;

    # Handle Client-side Routing (SPA)
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Proxy API requests ไปยัง Backend Container
    # "backend" คือชื่อ Service ใน docker-compose.yml
    # เลข Port 5000 คือ Host port ที่จะ map ไปยัง Port ของ backend container กรณีที่ Container run ที่ port อื่นให้เปลี่ยนเลข 5000 เป็นเลข Port ของ backend container
    location /api/ {
        proxy_pass         http://backend:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection 'upgrade';
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Real-IP $remote_addr;
    }

    # Cache Static Assets (1 ปี)
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Gzip Compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript;
}
```

```bash
# ตรวจสอบ
cat frontend/nginx.conf
```

<details>
<summary>💡 ทำไม proxy_pass ในส่วนของการระบุ proxy_pass   http://backend:5000/api/; ใช้ชื่อ backend แทนการใช้ localhost?</summary>

ใน Docker Network, Container สามารถค้นหากันได้ด้วย **Service Name** (ในกรณีนี้ ไฟล์ docker-compose.yml ต้องกำหนดชื่อ service ของส่วน API Server ว่า **backend**) 

```
frontend container → DNS lookup "backend" → backend container IP
```

เหมือนกับการใช้ Hostname ชื่อ backend แทน IP Address ซึ่งเปลี่ยนแปลงได้
</details>

---

#### 4.3 เขียน Dockerfile สำหรับ Frontend

สร้างไฟล์ `frontend/Dockerfile` พร้อมเนื้อหาดังนี้:

```bash
# Stage 1: Build (ขั้นตอนปรุงอาหาร)
FROM node:20-alpine AS build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production (ขั้นตอนเสิร์ฟ)
FROM nginx:stable-alpine
# ก๊อปปี้เฉพาะไฟล์ที่ Build เสร็จแล้ว (อยู่ในโฟลเดอร์ dist) ไปที่ Nginx
COPY --from=build-stage /app/dist /usr/share/nginx/html

# (อย่าลืมก๊อปปี้ nginx.conf ของคุณไปด้วย)
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

```bash
# ตรวจสอบ
cat frontend/Dockerfile
```

<details>
<summary>💡 ทำไม CMD ต้องมี daemon off?</summary>

Docker Container จะหยุดทำงานเมื่อ Process หลัก (PID 1) สิ้นสุด

```
# Nginx ปกติ (Background daemon) → Process หลักจบทันที → Container หยุด ❌
CMD ["nginx"]

# Nginx Foreground → Process หลักทำงานต่อเนื่อง → Container ยังรัน ✅
CMD ["nginx", "-g", "daemon off;"]
```
</details>

---

#### 4.4 Build Frontend Image
**ก่อนรันคำสั่ง ให้อยู่ที่ไดเรคทอรี่ booking-app-demo-2025**
```bash
# Build Frontend Image
docker build -t booking-frontend:1.0 ./frontend

# เปรียบเทียบขนาด
docker images | grep booking

# ทดสอบรัน Frontend 
# ก่อนการรันให้ตรวจสอบว่า Port 3000 บนเครื่องไม่มีแอปพลิเคชั่นอื่นใช้งานอยู่
docker run -d --name booking-frontend-test -p 3000:80 --network booking-network booking-frontend:1.0

# เปิด Browser ไปที่ http://localhost:3000
curl -I http://localhost:3000

# ดู Configuration ของ Nginx
docker exec booking-frontend-test cat /etc/nginx/conf.d/nginx.conf

# ลบ Container ทดสอบ
docker stop booking-frontend-test && docker rm booking-frontend-test
```

> **📝 บันทึกผล**: booking-frontend:1.0 ขนาด `............` MB | booking-backend:1.0 ขนาด `............` MB

---

### ❓ คำถามท้ายขั้นตอนที่ 4

1. ขนาดของ `booking-frontend:1.0` เทียบกับ `booking-backend:1.0` ต่างกันอย่างไร? เพราะเหตุใด?

   > _คำตอบ_: ........................................................................

2. `location /api/` ใน nginx.conf ทำหน้าที่อะไร?

   > _คำตอบ_: ........................................................................

3. ทำไม Frontend Container ถึงใช้ชื่อ `backend` ในการ Proxy ได้?

   > _คำตอบ_: ........................................................................

---

### 🔵 ขั้นตอนที่ 5: สร้าง Docker Compose

**วัตถุประสงค์**: เชื่อม Frontend กับ Backend และกำหนด SQLite Volume ให้ถูกต้อง 

---

#### 5.1 สร้าง docker-compose.yml

สร้างไฟล์ `docker-compose.yml` ที่ Root Directory พร้อมเนื้อหาดังนี้:

```bash
# ══════════════════════════════════════════════════════
#  Compose Specification (ไม่ต้องระบุ version แล้ว)
#  https://docs.docker.com/compose/compose-file/
# ══════════════════════════════════════════════════════

# ══════════════════════════════════════════════════════
#  Services (SQLite ไม่ต้อง Database Container แยก!)
# ══════════════════════════════════════════════════════
services:

  # ─── Backend API + SQLite ─────────────────────────────
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: booking-backend
    restart: unless-stopped
    environment:
      NODE_ENV: ${NODE_ENV}
      BACKEND_PORT: ${BACKEND_PORT}
      DB_PATH: ${DB_PATH}          # Path ของ SQLite file ใน Container
    ports:
      - "${HOST_BACKEND_PORT}:${BACKEND_PORT}"
    volumes:
      # Mount Volume เพื่อให้ booking.db อยู่รอดเมื่อ Container ถูก restart
      - sqlite_data:/app/data
    networks:
      - booking-net
    # หมายเหตุ: ไม่มี depends_on เพราะ SQLite ไม่มี Database Server
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://localhost:${BACKEND_PORT}/api/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s

  # ─── Frontend Web ─────────────────────────────────────
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: booking-frontend
    restart: unless-stopped
    ports:
      - "${HOST_FRONTEND_PORT}:${FRONTEND_PORT}"
    depends_on:
      backend:
        condition: service_healthy
    networks:
      - booking-net

# ══════════════════════════════════════════════════════
#  Networks
# ══════════════════════════════════════════════════════
networks:
  booking-net:
    driver: bridge
    name: booking-network

# ══════════════════════════════════════════════════════
#  Volumes — เก็บ SQLite .db file ให้อยู่รอดข้าม restart
# ══════════════════════════════════════════════════════
volumes:
  sqlite_data:
    driver: local
    name: booking-sqlite-data
```

```bash
# ตรวจสอบ
cat docker-compose.yml
```

---

#### 5.2 ตรวจสอบ Configuration

```bash
# Validate docker-compose.yml syntax
docker compose config

# ดูรายการ Service ทั้งหมด
docker compose config --services
```

<details>
<summary>✅ ผลลัพธ์ที่ถูกต้อง</summary>

```
# docker compose config --services ควรแสดง:
backend
frontend
```

</details>

---

### ❓ คำถามท้ายขั้นตอนที่ 5

1. docker-compose.yml คืออะไร มีประโยชน์อย่างไร?

   > _คำตอบ_: ........................................................................

2. `restart: unless-stopped` หมายความว่าอย่างไร?

   > _คำตอบ_: ........................................................................

3. Named Volume `sqlite_data` ต่างจาก Bind Mount อย่างไร? และทำไมต้องใช้กับ SQLite?

   > _คำตอบ_: ........................................................................

---

### 🔵 ขั้นตอนที่ 6: รันและทดสอบระบบ

**วัตถุประสงค์**: เริ่มต้น Monitor และ Debug Application ทั้งหมด

---
**ก่อนการรันทดสอบ ให้ลบ container backend และ frontend ที่สร้างในการทดลองก่อนหน้าออกก่อน**
```
docker rm -f backend frontend
docker network rm booking-network
```

#### 6.1 Build และเริ่ม Services

```bash
# Build Images ทุก Service พร้อมกัน
docker compose build

# Build + เริ่มทุก Service (Detached)
docker compose up -d --build

# ดูสถานะ Service
docker compose ps
```

<details>
<summary>✅ ผลลัพธ์ที่คาดหวัง</summary>

```
NAME                STATUS          PORTS
booking-backend     Up (healthy)    0.0.0.0:5000->5000/tcp
booking-frontend    Up              0.0.0.0:3000->80/tcp
```
</details>

> ⚠️ ครั้งแรกจะใช้เวลานานเพราะต้อง Build `booking-backend` (compile `better-sqlite3`)


---

#### 6.2 Monitor Log

```bash
# ดู Log ทุก Service พร้อมกัน (Ctrl+C เพื่อออก)
docker compose logs -f

# ดู Log เฉพาะ Service
docker compose logs -f backend
docker compose logs -f frontend

# ดู Log 50 บรรทัดล่าสุด
docker compose logs --tail=50 backend
```

<details>
<summary>💡 สิ่งที่ควรเห็นใน Log</summary>

**Backend Log** ที่สำเร็จ:
```
booking-backend  | Server running on port 5000
booking-backend  | SQLite database initialized: /app/data/booking.db
booking-backend  | Database tables created successfully
```

**Frontend Log** ที่สำเร็จ:
```
booking-frontend  | /docker-entrypoint.sh: Configuration complete; ready for start up
booking-frontend  | ... nginx: worker process
```
</details>

---

#### 6.3 ทดสอบ Application

```bash
# ── Test 1: Frontend ─────────────────────────────────────────
curl -I http://localhost:3000
# ควรได้ HTTP/1.1 200 OK

# ── Test 2: Backend Health Check ─────────────────────────────
curl http://localhost:5000/api/health

# ── Test 3: GET All Bookings ──────────────────────────────────
curl http://localhost:5000/api/bookings

# ── Test 6: เปิด Browser ─────────────────────────────────────
# http://localhost:3000
```

```bash
# ── Test 4: POST สร้าง Booking ใหม่ ──────────────────────────
curl -X POST http://localhost:5000/api/bookings -H "Content-Type: application/json" -d '{
    "fullname": "นายทดสอบ ระบบ",
    "email":"test@gmail.com",
    "phone":"0123456789",
    "room": "101",
    "roomtype":"delux",
    "guests":2,
    "checkin": "2025-08-01",
    "checkout": "2025-08-03"
  }'
```

**บันทึกรูปผลการทดลอง**
```
บันทึกรูปที่นี่
```

---

#### 6.4 Debug: ตรวจสอบ Container Network

```bash
# เข้าไปใน Backend Container
docker compose exec backend sh

# ภายใน Backend Container:
# ดู Environment Variables (ทำงานภายใน Alpine Container ไม่ใช่ Windows Shell)
env | grep -E "DB_PATH|PORT|NODE"

# ตรวจสอบว่า SQLite file ถูกสร้างแล้ว
ls -lh /app/data/

# ดูขนาดของ Database file (คำสั่งเหล่านี้รันภายใน Alpine Container)
du -sh /app/data/booking.db

exit
```
---

#### 6.5 ดู Resource Usage

```bash
# ดูการใช้ CPU/Memory ของทุก Container
docker stats

# ดู Network ที่สร้างขึ้น
docker network ls | grep booking

# ดูรายละเอียด Network
docker network inspect booking-network

# ดู IP Address ของแต่ละ Container
docker network inspect booking-network \
  --format='{{range .Containers}}{{.Name}}: {{.IPv4Address}}{{"\n"}}{{end}}'
```

> **📝 บันทึกผลการทดลอง**:
> - IP ของ frontend: `............`
> - IP ของ backend: `............`


---

#### 6.6 Restart และ Scale

```bash
# Restart เฉพาะ Backend
docker compose restart backend

# หยุดเฉพาะ Frontend ชั่วคราว
docker compose stop frontend

# เริ่ม Frontend ใหม่
docker compose start frontend

# ดู Events ทั้งหมด
docker compose events --json 2>/dev/null | head -20
```

---

### ❓ คำถามท้ายขั้นตอนที่ 6

1. `docker compose ps` แสดงสถานะ Service อย่างไร?

   > _คำตอบ_: ........................................................................

2. IP Address ของแต่ละ Container ในผลจาก `docker network inspect` คืออะไร?

   > _คำตอบ_: ........................................................................

3. SQLite database file (`booking.db`) ถูกสร้างใน Path ใดภายใน Container?

   > _คำตอบ_: ........................................................................

---
