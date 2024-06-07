- [Note เอกสารเปิดสำหรับการเชื่อมต่อระบบ](#note-เอกสารเปิดสำหรับการเชื่อมต่อระบบ)
  - [วิเคราะห์ข้อมูล](#วิเคราะห์ข้อมูล)
  - [ข้อมูลสำหรับใช้เปิดห้อง Mi-DEMS](#ข้อมูลสำหรับใช้เปิดห้อง-mi-dems)
    - [ช่วงการเกิดห้องประชุม](#ช่วงการเกิดห้องประชุม)

[api doc](https://lionants02.github.io/Mi-DEMS/)


# Note เอกสารเปิดสำหรับการเชื่อมต่อระบบ

## วิเคราะห์ข้อมูล
ความสัมพันธ์ (Relationships) ของข้อมูล
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