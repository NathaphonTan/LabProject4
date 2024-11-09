# LabProject4
# แนวทางการทำงาน ESP32 project cook book
## 1. ระบุตัวอย่างที่ใช้ ว่าเอามาจากตัวอย่างไหน
ตัวอย่างที่ใช้จะเป็นตัวอย่างที่เกี่ยวกับการใช้เซ็นเซอร์ MPU6050 หรือ GY-87 เพื่อตรวจจับการเคลื่อนไหว
## 2. ระบุว่า จะแก้ไขตรงไหนบ้าง เพื่ออะไร
1. การตรวจจับการล้ม: จะต้องฟังก์ชันตรวจจับการล้ม โดยการคำนวณค่าของเซ็นเซอร์ accelerometer และ gyroscope เพื่อตรวจสอบการเปลี่ยนแปลงของมุมเมื่อมีการล้ม
2. แสดงผล: ใช้ Serial Monitor  ในการแสดงสถานะการล้ม เพื่อแสดงสถานะการล้ม
## 3. แสดงขั้นตอนการทำ project
**ขั้นตอนที่ 1 ติดตั้งไลบรารี**
1. เปิด Arduino IDE และไปที่ Sketch > Include Library > Manage Libraries
2. ค้นหา MPU6050 และติดตั้งไลบรารี
3. ค้นหา Wire และติดตั้งไลบรารีที่จำเป็นสำหรับการเชื่อมต่อ I2C

**ขั้นตอนที่ 2 การเชื่อมต่อ GY-87 กับ ESP32**

|GY87 Pin |ESP32 Pin | 
| -------- | -------- | 
| VCC | 3.3V| 
| GND | GND | 
|SDA	|GPIO 21 (หรือ GPIO อื่นๆ ที่ตั้งค่าเป็น SDA ในโค้ด)|
|SCL|	GPIO 22 (หรือ GPIO อื่นๆ ที่ตั้งค่าเป็น SCL ในโค้ด)|

**ขั้นตอนที่ 3 เขียนโค้ด**
  ```cpp
#include <Wire.h>
#include <MPU6050.h>

MPU6050 mpu;

void setup() {
  Serial.begin(115200);  
  Wire.begin();
  mpu.initialize();  
  
  if (!mpu.testConnection()) {
    Serial.println("Connection failed");
    while (1);  
  }
  Serial.println("MPU6050 connected successfully");
}

void loop() {
  int16_t ax, ay, az;
  mpu.getAcceleration(&ax, &ay, &az);
  
  float acceleration = sqrt(ax*ax + ay*ay + az*az);
  
  // ตรวจจับการล้ม
  if (acceleration > 15000) {  
    Serial.println("Fallen");  
  } else {
    Serial.println("Standing");  
  }
  
  delay(500);  
}


  ```
**ขั้นตอนที่ 4 อัปโหลดโค้ด**
อัปโหลดโค้ดไปยัง ESP32: หลังจากเชื่อมต่อกับ ESP32 แล้ว อัปโหลดโค้ดลงไปผ่าน Arduino IDE

## 4. แสดงผลการทำ project
เมื่อเชื่อมต่อ GY-87 กับ ESP32 และอัปโหลดโค้ดไปยังบอร์ดแล้ว ระบบจะตรวจจับการล้มเมื่อค่าการเร่งจากเซ็นเซอร์มีค่ามากกว่าที่กำหนดและจะแสดงผลใน Serial Monitor ว่ามีการล้มเกิดขึ้นหรือไม่

![1](https://github.com/user-attachments/assets/c71362fe-dbf9-462f-b192-78149b091645)

## 5. สรุปผลการทำ project 
โปรเจคนี้ใช้เซ็นเซอร์ GY-87 เพื่อตรวจจับการล้ม โดยตรวจสอบการเปลี่ยนแปลงของค่า acceleration ที่เกิดจากการเคลื่อนไหว เมื่อค่าถึงระดับที่กำหนด ระบบจะตรวจพบการล้มและแสดงผลใน Serial Monitor
