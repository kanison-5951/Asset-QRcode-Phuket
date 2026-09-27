# Asset-QRcode-Phuket
ระบบบริหารจัดการครุภัณฑ์ผ่าน QR Code — Supabase Edition
ไฟล์ `index-supabase.html` เป็นเวอร์ชันที่เปลี่ยน Backend จาก Google Sheets + Google Apps Script เป็น Supabase โดยคงหน้าตาและ workflow หลักของ `index (7).html` ไว้
ไฟล์ในชุดนี้
`index-supabase.html` — หน้าเว็บสำหรับ GitHub Pages
`supabase_schema.sql` — SQL สร้างตาราง, Index, RLS และ Storage
1. สร้าง Supabase Project
สร้าง Project ใหม่ใน Supabase แล้วเปิด SQL Editor
รันไฟล์ `supabase_schema.sql` ทั้งไฟล์
2. สร้าง Admin User
ไปที่ Authentication → Users แล้วสร้างผู้ใช้แบบ Email + Password เช่น
Email: อีเมลของผู้ดูแลระบบ
Password: ตั้งรหัสผ่านของคุณเอง
อย่าใช้ `admin1234` ในระบบจริง
3. ตั้งค่าเว็บ
เปิด `index-supabase.html` แล้วกด ตั้งค่า Supabase บริเวณด้านล่างหน้าเว็บ
ใส่:
Project URL
Publishable/Anon Key
หรือใส่ค่าตรงนี้ใน JavaScript เพื่อให้เว็บใหม่ทุกเครื่องเชื่อมต่อให้อัตโนมัติ:
```js
const DEFAULT_SUPABASE_URL = 'https://YOUR-PROJECT.supabase.co';
const DEFAULT_SUPABASE_ANON_KEY = 'YOUR-PUBLISHABLE-OR-ANON-KEY';
```
ใช้เฉพาะ Publishable/Anon Key ใน browser ห้ามนำ `service_role` key ไปใส่ใน HTML
4. GitHub Pages
ตั้งชื่อไฟล์เป็น `index.html` แล้วอัปโหลดแทนไฟล์เดิมใน repository ของคุณ จากนั้นเปิด GitHub Pages ตามปกติ
5. การย้ายข้อมูลจาก Google Sheets
Export ชีตเดิมเป็น CSV แล้วนำเข้าที่ Supabase Table Editor → `assets`
Mapping:
Google Sheets	Supabase
ID	id
Category	category
Name	name
Code	code
Detail	detail
Budget	budget
Amount	amount
ReceivedDate	received_date
DikaNo	dika_no
Location	location
ImageUrl	image_url
ก่อน import `assets` ต้องมีชื่อหมวดหมู่เหล่านั้นอยู่ใน `categories` เพราะตาราง assets ใช้ foreign key กับ categories
6. รูปภาพ
ระบบใหม่ไม่เก็บรูปภาพเป็น Base64 ในฐานข้อมูลอีกแล้ว
รูปใหม่จะถูกอัปโหลดไปที่ Storage bucket:
`asset-images`
และใน `assets.image_url` จะเก็บเฉพาะ URL
7. ทำไมเวอร์ชันนี้เร็วกว่า
เวอร์ชันเดิมโหลดข้อมูลทั้งหมดจาก Google Apps Script แล้วให้ browser ค้นหาและแบ่งหน้าเอง
เวอร์ชันใหม่ใช้:
PostgreSQL
Database indexes
Server-side search
Server-side pagination 20 รายการ/ครั้ง
Direct Supabase Data API
Supabase Storage สำหรับรูปภาพ
ดังนั้นถ้ามี 10,000 รายการ browser จะไม่ต้องรับข้อมูลทั้ง 10,000 รายการทุกครั้ง
8. Security
RLS ถูกเปิดใช้กับ `assets` และ `categories`
ผู้ใช้ทั่วไป: อ่านข้อมูลได้
ผู้ใช้ authenticated: เพิ่ม/แก้ไข/ลบได้
Storage: รูปภาพอ่านได้สาธารณะ แต่ upload/update/delete ต้อง authenticated
ถ้าระบบมีผู้ใช้หลายระดับในอนาคต ควรเพิ่ม role/RBAC เพื่อให้เฉพาะผู้ดูแลระบบมีสิทธิ์แก้ไขข้อมูล
