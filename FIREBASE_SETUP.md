# ระบบ HI/CI แบบออนไลน์ — คู่มือตั้งค่า Firebase

ระบบทดสอบอยู่ที่: https://sornua2026.github.io/dengue-huaiso/online.html
เว็บเดิม index.html ยังคงอยู่โดยไม่เปลี่ยนแปลง

## ขั้นตอน A: เพิ่มโดเมนเว็บไซต์ที่อนุญาต

Firebase Console → Authentication → Settings → Authorized domains
เพิ่ม: sornua2026.github.io

## ขั้นตอน B: สร้างบัญชีผู้ดูแล

Firebase Console → Authentication → Users → Add user
กรอกอีเมลที่ใช้งานได้และตั้งรหัสผ่านที่รัดกุม
คัดลอก UID จากรายการ Users (ไม่ใช่รหัสผ่าน)

Cloud Firestore → Data → Start collection
Collection ID: users
Document ID: UID ที่คัดลอกมาจาก Authentication (ต้องตรงกัน)
สร้างฟิลด์ดังนี้:
- role (string) = admin
- enabled (boolean) = true
- displayName (string) = ผู้ดูแล รพ.สต.
- village (string) = all

อย่าลืมว่าบัญชีผู้ดูแลคนแรกต้องสร้างเอกสาร users/{UID} ใน Firestore Console
ก่อนเผยแพร่กฎ Firestore หรือก่อนเริ่มเข้าระบบได้ตามความเหมาะสม
การสร้างเอกสารใน Console ทำได้ด้วยสิทธิผู้ดูแลโครงการ ไม่ใช่สิทธิไคลเอนต์ของเว็บไซต์

## ขั้นตอน C: กำหนด Security Rules

Firebase Console → Firestore Database → Rules
นำเนื้อหาไฟล์ firestore.rules ใน Repository นี้ไปแทนกฎเดิม แล้วกด Publish
อย่าใช้ allow read, write: if true หรือ if request.auth != null กับทุกข้อมูล
กฎนี้ตรวจ role และ village จากเอกสาร users/{UID} และตรวจสอบความถูกต้องข้อมูลด้วย

## ขั้นตอน D: ล็อกอินที่เว็บไซต์ทดลอง

เปิด https://sornua2026.github.io/dengue-huaiso/online.html
ใช้อีเมลและรหัสผ่านจาก Authentication
ระบบจะตรวจสิทธิใน collection users
เมื่อเข้าระบบสำเร็จ ผู้ดูแลจะเห็นทุกหมู่บ้าน และมีปุ่มนำเข้าข้อมูลฐานปี 2569
หากยังไม่ได้ตั้งค่า Rules / users profile จะเข้าระบบข้อมูลไม่ได้

## ขั้นตอน E: สร้างบัญชี อสม. แต่ละหมู่บ้าน

Firebase Authentication → Users → Add user (หนึ่งบัญชีต่อผู้ใช้)
คัดลอก UID ของแต่ละบัญชี แล้วสร้างเอกสารใน collection users:

Document ID = UID ผู้ใช้งาน
- role (string) = vhw
- enabled (boolean) = true
- displayName (string) = ชื่อสำหรับแสดง (ไม่จำเป็นต้องใส่ชื่อจริง)
- village (string) = 1 หรือ 2 หรือ 12 หรือ 16 หรือ 18 หรือ 19

ผู้ใช้ vhw อ่าน/แก้ไขผลสำรวจได้เฉพาะหมู่ที่กำหนด
ผู้ใช้ admin อ่าน/แก้ไขข้อมูลทุกหมู่และจัดการข้อมูลผู้ป่วยแบบสรุป

## การนำเข้าข้อมูลต้นปี 2569

หลังเข้าระบบด้วยบัญชี admin ให้ไปที่ "ตั้งค่า"
เลือก "นำเข้าข้อมูลพื้นฐาน 6 หมู่บ้าน"
ระบบจะเพิ่มเฉพาะเอกสารที่ยังไม่มีอยู่แล้ว
***ตรวจสอบวันที่สำรวจ 21 กันยายน 2569 ก่อนใช้งานจริง***
การนำเข้าไม่เพิ่มบ้านเลขที่หรือข้อมูลส่วนบุคคล

## หมายเหตุด้านความปลอดภัย

- ไฟล์ตั้งค่า Firebase สำหรับเว็บ (apiKey และ projectId) เป็นข้อมูลระบุโปรเจกต์ ไม่ใช่รหัสผู้ดูแล
- ห้ามใส่ Firebase service account private key, รหัสผ่าน หรือ OTP ใน Repository
- โครงการ Firestore ใช้ Security Rules ควบคุมสิทธิ แม้เปิดเว็บไซต์แบบ Public
- ไม่แนะนำให้เผยแพร่บ้านเลขที่หรือตัวระบุบุคคลในรายงานสาธารณะ
- เว็บไซต์ทดลองใช้บัญชีล็อกอินและ session persistence; ควรออกจากระบบเมื่อใช้เครื่องร่วม
- ต้องตรวจสอบดัชนี HI/CI เทียบกับวิธีสำรวจเดียวกันก่อนสรุปความเสี่ยง
- ระบบไม่สามารถยืนยัน/ทำนายการระบาดจากค่า HI/CI เพียงอย่างเดียว

## คู่มือดูแลระบบระยะยาว

อ่านคู่มือผู้ดูแลระบบภาษาไทยได้ที่ [ADMIN_GUIDE_TH.md](./ADMIN_GUIDE_TH.md) ครอบคลุมการรักษาสิทธิ์เจ้าของ Firebase/GitHub การจัดการบัญชี อสม. การสำรองข้อมูล และการส่งมอบงาน

**การสำรองข้อมูล:** หน้าออนไลน์ `online.html` มีปุ่ม “ดาวน์โหลดสำรองข้อมูลครบรวมภาพ” สำหรับแอดมิน โดยส่งออกข้อมูล surveys, cases, surveyPhotos และ userProfiles ในไฟล์ JSON ทั้งนี้ไฟล์สำรองไม่รวมรหัสผ่านหรือบัญชี Firebase Authentication และยังไม่มีระบบนำเข้า/กู้คืนอัตโนมัติ
