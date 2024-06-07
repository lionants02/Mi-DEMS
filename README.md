- [Note](#note)
  - [วิเคราะห์ข้อมูล](#วิเคราะห์ข้อมูล)
  - [ข้อมูลสำหรับใช้เปิดห้อง Mi-DEMS](#ข้อมูลสำหรับใช้เปิดห้อง-mi-dems)
    - [ช่วงการเกิดห้องประชุม](#ช่วงการเกิดห้องประชุม)

[api doc](https://lionants02.github.io/Mi-DEMS/)


# Note

## วิเคราะห์ข้อมูล
ความสัมพันธ์ (Relationships) ของข้อมูล
```mermaid
erDiagram
    ConferenceRoom["ห้องประชุม Mi-DEMS"]{
        string roomId PK
        string vehicleId FK
    }
    Vehicle["พาหนะฉุกเฉิน"]{
        string vehicleId PK
        string vehicleLicenseNO "ทะเบียนพาหนะ"
        string transportType "ประเภท (FR,ALS)"
    }

    Patient["ผู้ป่วยฉุกเฉิน"]{
        string caseId PK
    }

    Team["ทีมปฏิบัติงาน"]{
        string teamId PK
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
        string caseId PK,FK
        string teamId PK,FK
        string reportId "autogen"
    }
    ER
    Doctor["แพทย์"]

    ConferenceRoom only one to one Vehicle :"one to one"
    ConferenceRoom many to many Patient: "many to many"
    ConferenceRoom only one to one Team: "one to one"

    Vehicle one to many VitalSign:"one to many"
    Vehicle one to one IPCam:"one to one"
    Vehicle one to one Team:"one to one"

    Team one to many Report: "one team to many report"
    Patient one to many Report: "one case to many report"


```
## ข้อมูลสำหรับใช้เปิดห้อง Mi-DEMS

```mermaid
mindmap
  root((ห้องประชุมฉุกเฉิน))
    รายการผู้ประสบเหตุ
        ใครบ้าง
        ตำแหน่งที่เกิดเหตุ
        ชื่อ เพศ อายุ
        ระดับความรุ่นแรง
        วันเวลารับแจ้ง
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