# One Sheet Miracle — Study Portal

เว็บนี้แบ่งเป็น 2 ส่วน:

```
/index.html                    ← หน้าประตู: login ด้วย Gmail + เลือกวิชา
/business-finance/index.html   ← เว็บเรียน Business Finance เดิมทั้งหมด (ย้ายมาไว้ใต้ path นี้)
/firestore.rules               ← กฎความปลอดภัยของฐานข้อมูล (ต้องนำไปวางใน Firebase Console)
```

คนที่ยังไม่ได้รับสิทธิ์จะเห็นปุ่ม "Business Finance" จางลงกดไม่ได้ พร้อมป้าย "รอการอนุมัติ" — คุณเป็นคนกดอนุมัติเองทีละคนผ่าน Firebase Console (ไม่มีใครอนุมัติตัวเองได้ แม้จะแก้โค้ดหน้าเว็บก็ตาม เพราะกฎถูกบังคับที่ฝั่งเซิร์ฟเวอร์ของ Google)

---

## สิ่งที่ต้องทำก่อน deploy ได้จริง (ทำครั้งเดียว)

ส่วนนี้ต้องทำเองเพราะต้องใช้บัญชี Google ของคุณโดยตรง (ไม่มีเครื่องมือไหนทำแทนได้จากภายนอก)

### 1. สร้างโปรเจกต์ Firebase (ฟรี)

1. ไปที่ [console.firebase.google.com](https://console.firebase.google.com) → **Add project** → ตั้งชื่ออะไรก็ได้ เช่น `business-finance-portal`
2. ไม่ต้องเปิด Google Analytics ก็ได้ (ไม่จำเป็น)

### 2. เปิดใช้ Google Sign-In

1. ในเมนูซ้าย → **Build → Authentication** → **Get started**
2. แท็บ **Sign-in method** → เลือก **Google** → เปิดใช้งาน (Enable) → เลือกอีเมลติดต่อโปรเจกต์ → **Save**

### 3. สร้างฐานข้อมูล Firestore

1. เมนูซ้าย → **Build → Firestore Database** → **Create database**
2. เลือกโหมด **Production mode** (สำคัญ — ต้องไม่ใช่ Test mode)
3. เลือก location ที่ใกล้ (เช่น `asia-southeast1`) → Enable

### 4. วางกฎความปลอดภัย

1. ในหน้า Firestore Database → แท็บ **Rules**
2. ลบของเดิมทั้งหมด แล้ววางเนื้อหาทั้งหมดจากไฟล์ `firestore.rules` ที่แนบมาในโปรเจกต์นี้แทน
3. กด **Publish**

### 5. เอา config มาใส่ในเว็บ

1. เมนูซ้าย (รูปเฟือง) → **Project settings** → เลื่อนลงหา **Your apps** → กดไอคอน **</>** (Web app)
2. ตั้งชื่อ nickname อะไรก็ได้ → **Register app** (ไม่ต้องติ๊ก Firebase Hosting)
3. จะได้โค้ดก้อนหนึ่งหน้าตาแบบนี้:
   ```js
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "business-finance-portal.firebaseapp.com",
     projectId: "business-finance-portal",
     storageBucket: "business-finance-portal.appspot.com",
     messagingSenderId: "123456789",
     appId: "1:123456789:web:abcdef"
   };
   ```
4. เปิดไฟล์ `index.html` (หน้าประตู) ในโปรเจกต์นี้ → หา `var firebaseConfig = {...}` (อยู่เกือบล่างสุดของไฟล์) → แทนที่ด้วยค่าจริงที่ได้มา

### 6. เพิ่มโดเมน Cloudflare Pages เข้า Authorized domains (สำคัญมาก — ถ้าข้ามขั้นตอนนี้ปุ่ม Google Sign-In จะ error)

หลัง deploy ขึ้น Cloudflare Pages แล้ว (ขั้นตอนถัดไป) จะได้โดเมนแบบ `xxxxx.pages.dev`:

1. กลับไปที่ Firebase Console → **Authentication** → แท็บ **Settings** → **Authorized domains**
2. กด **Add domain** → ใส่โดเมน `.pages.dev` ที่ได้มา (ไม่ต้องใส่ `https://`)

---

## นำโปรเจกต์นี้ขึ้น GitHub repo `Business-Finance` ของคุณ

**หมายเหตุ:** เซสชันนี้เชื่อมต่อ GitHub ผ่านระบบที่ผูกกับ repo ที่ตั้งค่าไว้ล่วงหน้าเท่านั้น ผมไม่มีสิทธิ์ push เข้า repo `Business-Finance` ที่คุณเพิ่งสร้างโดยตรงจากในนี้ได้ — ต้องให้คุณ push เองครับ (ใช้เวลาไม่ถึง 2 นาที) เลือกวิธีใดวิธีหนึ่ง:

### วิธี A — ผ่าน terminal/command line (เร็วที่สุด)

ดาวน์โหลดไฟล์ทั้งหมดที่ผมส่งให้ (ไฟล์ zip) แตกไฟล์ แล้วรันคำสั่งนี้ในโฟลเดอร์ที่แตกออกมา:

```bash
git init
git add .
git commit -m "Add study portal with Google login"
git branch -M main
git remote add origin https://github.com/Solopreneur-ATK/Business-Finance.git
git push -u origin main
```

(ถ้า repo มีไฟล์อยู่แล้วเช่น README ที่ GitHub สร้างให้ตอนสร้าง repo อาจต้องรัน `git pull origin main --allow-unrelated-histories` ก่อน push)

### วิธี B — อัปโหลดผ่านเว็บ GitHub (ไม่ต้องใช้ terminal)

1. เข้า repo `Business-Finance` บนเว็บ GitHub → กด **Add file → Upload files**
2. ลากไฟล์/โฟลเดอร์ทั้งหมดจาก zip ที่แตกแล้ว (`index.html`, โฟลเดอร์ `business-finance/`, `firestore.rules`, `README.md`) เข้าไปในหน้าอัปโหลด
3. เลื่อนลงกด **Commit changes**

---

## Deploy บน Cloudflare Pages

1. ไปที่ [dash.cloudflare.com](https://dash.cloudflare.com) → **Workers & Pages** → **Create** → แท็บ **Pages** → **Connect to Git**
2. เชื่อมบัญชี GitHub แล้วเลือก repo `Business-Finance`
3. ตั้งค่า build:
   - **Framework preset:** None
   - **Build command:** ว่างไว้ (ไม่ต้องใส่ — ไม่มีขั้นตอน build ไฟล์เป็น HTML/CSS/JS ล้วนอยู่แล้ว)
   - **Build output directory:** `/` (root ของ repo)
4. กด **Save and Deploy** → รอสักครู่จะได้ลิงก์ เช่น `business-finance.pages.dev`
5. **สำคัญ:** กลับไปทำขั้นตอน "6. เพิ่มโดเมน Cloudflare Pages เข้า Authorized domains" ด้านบนด้วยโดเมนที่ได้ตรงนี้ ไม่งั้น Google Sign-In จะ error

จากนี้ทุกครั้งที่ push โค้ดใหม่ขึ้น GitHub (ไม่ว่าจะเป็นการอัปเดตเว็บ Business Finance หรือเพิ่มวิชาใหม่) Cloudflare Pages จะ deploy ให้อัตโนมัติ

---

## วิธีอนุมัติสิทธิ์ให้เพื่อน (ทำทุกครั้งที่มีคนใหม่ขอเข้า)

1. Firebase Console → **Firestore Database** → **Data**
2. เข้า collection `users` → หาเอกสารของอีเมลที่ต้องการอนุมัติ (ดูจากฟิลด์ `email`)
3. เปิดฟิลด์ `courses` → แก้ค่า `business-finance` จาก `"pending"` เป็น `"approved"` → กด update
4. เพื่อนคนนั้น refresh หน้าเว็บ ปุ่ม Business Finance จะสว่างขึ้นและกดเข้าได้ทันที

**หมายเหตุ:** เอกสารของผู้ใช้จะถูกสร้างในฐานข้อมูลก็ต่อเมื่อเขา sign in อย่างน้อย 1 ครั้งแล้ว — ถ้ายังไม่เห็นชื่อใน Firestore แปลว่าเขายังไม่เคย sign in

---

## เพิ่มวิชาใหม่ในอนาคต

1. อัปโหลดเว็บวิชาใหม่ไว้ในโฟลเดอร์ใหม่ เช่น `/excel-for-business/index.html`
2. เปิดไฟล์ `index.html` (หน้าประตู) → หา `var COURSES = [...]` → เพิ่มบรรทัดใหม่ตามแบบที่ comment ไว้ในไฟล์:
   ```js
   { id: "excel-for-business", name: "Excel for Business", code: "2602359", icon: "📊", path: "/excel-for-business/" }
   ```
3. ไม่ต้องแก้ `firestore.rules` เพิ่ม — กฎที่วางไว้รองรับวิชาใหม่โดยอัตโนมัติ (ผู้ใช้ใหม่จะเริ่มเป็น "pending" ทุกวิชาที่มีอยู่ในตอนนั้น)
