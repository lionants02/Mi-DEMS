- [Api Doc](#api-doc)
- [Note เอกสารเปิดสำหรับการเชื่อมต่อระบบ](#note-เอกสารเปิดสำหรับการเชื่อมต่อระบบ)
  - [สถานะสำคัญในระบบ Mi-DEMS](#สถานะสำคัญในระบบ-mi-dems)
  - [วิเคราะห์ข้อมูล](#วิเคราะห์ข้อมูล)
  - [ข้อมูลสำหรับใช้เปิดห้อง Mi-DEMS](#ข้อมูลสำหรับใช้เปิดห้อง-mi-dems)
    - [ช่วงการเกิดห้องประชุม](#ช่วงการเกิดห้องประชุม)

# Api Doc
[api doc](https://lionants02.github.io/Mi-DEMS/)

# Note เอกสารเปิดสำหรับการเชื่อมต่อระบบ

## สถานะสำคัญในระบบ Mi-DEMS
```mermaid
 flowchart LR
    p8["กลับถึงฐาน/พร้อมรับงานต่อ\nstampCode:7\nหากพร้อมรรับงานต่อจำเป็นต้องสแตมป์สถานะนี้"]:::red
    s("รับงาน\nstampCode:1")
    p1["ออกจากฐาน\nstampCode:2"]
    p2["ถึงจุดเกิดเหตุ/จุดรับผู้ป่วย\nstampCode:3"]
    p3["มีการรักษา\nstampCode:4"]
    p4["ไม่มีการรักษา"]
    p5["ออกจากจุดเกิดเหตุ/จุดรับ\nstampCode:5"]
    p6["ถึงโรงพยาบาล\nstampCode:6"]
    p7["ปิดงานก่อนถึง รพ."]

    p41["ผู้ป่วยปฏิเสท\nclosingReasonCode:5"]
    p42["ยกเลิก\nclosingReasonCode:6"]
    p43["ไม่พบเหตุ\nclosingReasonCode:7"]
    p44["เสียชีวิตก่อนชุดปฏิบัติการไปถึง\nclosingReasonCode:8"]

    s-->p1-->p2-->p3-->p5-->p6
    p2-.->p4
    p4-->p41
    p4-->p42
    p4-->p43
    p4-->p44

    s-.->p7
    p1-.->p7
    p2-.->p7
    p3-.->p7
    p5-.->p7

    p71["ส่งต่อชุดระดับสูงกว่า\nclosingReasonCode:1"]
    p72["ไม่นำส่ง/ขอลง\nclosingReasonCode:2"]
    p73["เสียชีวิตระหว่างนำส่ง\nclosingReasonCode:3"]
    p74["เสียชีวิต ณ จุดเกิดเหตุ\nclosingReasonCode:4"]
    
    p7-->p71
    p7-->p72
    p7-->p73
    p7-->p74

    classDef red stroke:green
    classDef red stroke-width:4px
    classDef red fill:#39FF00
    classDef red color:black
```

## วิเคราะห์ข้อมูล
ความสัมพันธ์ (Relationships) ของข้อมูลตารางหลักๆ
```mermaid
erDiagram
    ConferenceRoom["ห้องประชุม Mi-DEMS"]{
        string roomId PK
        string vehicleId FK,UK
    }
    Vehicle["พาหนะฉุกเฉิน"]{
        string vehicleId PK
        string vehicleLicenseNO "ทะเบียนพาหนะ"
        string transportType "ประเภท (FR,ALS)"
    }

    Patient["ผู้ป่วยฉุกเฉิน"]{
        string patientId PK "รหัสผู้ป่วย"
        string hospitalId FK
        string caseId "รหัสรับแจ้ง"
    }
    
    PatientTimeline["ไทม์ไลน์ผู้ป่วย"]{
        number id PK
        string patientId FK "รหัสผู้ป่วย"
        string operation_set_id FK "รหัสชุดปฏิบัติการ"
        string code
        string message
        timestamp create_at
    }

    Team["ทีมปฏิบัติงาน"]{
        string operation_set_id PK "รหัสชุดปฏิบัติการ"
        string roomId FK
        string vehicleId FK
    }
    VitalSign["อุปกรณ์สัญญาณชีพในรถ"]{
        number id PK
        string vehicleId FK
        string sn "Serial Number"
    }
    IPCam["ชุดกล้องในพาหนะ"]
    Report["รายงานเบิกจ่าย"]{
        string patientId PK,FK
        string operation_set_id PK,FK
        string hospitalId FK
        string reportId "autogen"
    }
    Hospital["โรงพยาบาล ER"]{
        string hospitalId PK
        string hospitalCode
        string hospitalName
    }
    Doctor["แพทย์อำนวยการ"]

    ConferenceRoom only one to one Vehicle :"one to one"
    ConferenceRoom many to many Patient: "many to many"
    ConferenceRoom only one to one Team: "one to one"

    Vehicle one to many VitalSign:"one to many"
    Vehicle one to one IPCam:"one to one"
    Vehicle one to one Team:"one to one"

    Team one to many Report: "one team to many report"
    Patient one to many Report: "one patient to many report"

    Hospital one to many Report: "one hospital to many report"
    Hospital one to many Patient: "one hospital to many patient"

```
## ข้อมูลสำหรับใช้เปิดห้อง Mi-DEMS

```mermaid
mindmap
  root((ห้องประชุมฉุกเฉิน))
    ผู้ประสบเหตุ
        ตำแหน่งที่เกิดเหตุ
        ชื่อ เพศ อายุ
        ระดับความรุ่นแรง
        วันเวลารับแจ้ง
        รหัสรับแจ้ง
    ข้อมูลพาหนะ
        ทีมที่ขึ้นพาหนะ
        ระดับทีม
    
```

### ช่วงการเกิดห้องประชุม
```mermaid
flowchart LR
    start("สร้างห้องประชุม")
    start-->p(("ปฏิบัติงาน"))
    p-->p2(("ปิดงาน"))
    p2-->e("ปิดห้องประชุม")
```