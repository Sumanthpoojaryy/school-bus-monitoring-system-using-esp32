# school-bus-attendance-system-using-esp32
 **Aim:**
 Automate the tracking of student attendance ,Provide real-time attendance tracking of the school bus using RFID technology. 

**Objective:**
Implementing school bus monitoring and notification systems using GPS modules and RFID technology can serve several important objectives to enhance the safety and efficiency of school transportation. Here are some key objectives:

**Student Safety:**
Real-time Tracking: Use GPS modules to track the school bus in real-time, allowing parents, school administrators, and transportation authorities to know the exact location of the bus at any given time.
Emergency Response: In case of emergencies or unexpected incidents, the system can quickly provide the location of the bus, enabling swift and targeted emergency response.

**Attendance and Security:**
RFID Attendance: Integrate RFID technology to monitor students getting on and off the bus. Each student can have an RFID card, and the system will automatically log attendance when they enter or exit the bus.
Identity Verification: Ensure that only authorized students are allowed on the bus, enhancing security and preventing unauthorized access.
**sms sending**
Schedule Adherence: Monitor the bus's adherence to the planned schedule, providing real-time updates if there are delays or changes.
Parental Communication:
Real-time Notifications: Parents can receive notifications when the bus is approaching their child's stop, ensuring timely pickup and drop-off.
Alerts for Delays: Instant notifications can be sent to parents if there are delays or unexpected changes in the bus schedule, keeping them informed.
Incident Reporting: In case of accidents or incidents, the system can provide valuable data for investigations and analysis.

Implementing these objectives can contribute to a safer, more efficient, and well-monitored school bus transportation system.

**Feature:** 

**1. Student Safety:**
Real-time GPS tracking ensures the continuous monitoring of the school bus's location. This information serves as a critical tool for parents, school administrators, and transportation authorities to guarantee the safety of students during transit. In emergencies, the system provides rapid and precise location data, facilitating swift and targeted emergency responses.

**2. Attendance and Security:**
Integrating RFID technology for student identification enhances security and attendance tracking. Each student is assigned an RFID card, automating the logging of attendance as they board or disembark the bus. This not only streamlines administrative processes but also ensures that only authorized individuals have access to the school bus, bolstering overall security.

**3. Parental Communication:**
Real-time notifications, triggered by GPS and RFID data, keep parents informed about the bus's proximity to their child's stop. This ensures timely pickups and drop-offs and provides peace of mind to parents by keeping them abreast of any unexpected delays or changes in the bus schedule.

**4. Cost Efficiency:**
By tracking fuel consumption and optimizing routes, the system contributes to cost efficiency. Data analytics enable the optimization of bus allocation based on demand, ensuring the judicious use of resources and minimizing operational costs.

In conclusion, the integration of GPS modules and RFID technology into school bus monitoring and notification systems serves a multifaceted purpose. It goes beyond basic tracking to encompass student safety, attendance automation, route optimization, parental communication,cost efficiency. This comprehensive approach addresses the diverse needs of school transportation, creating a secure, efficient, and well-monitored system that benefits students, parents, school administrators, and transportation authorities alike.

**Components required:**
1)ESP32 board
2)bread board
3)GSM module
4)connecting wire
5)RFID card and reader

**Mind Map**
![Mind Map]![image]![MindMap]![WhatsApp Image 2023-11-24 at 12 00 39 AM](https://github.com/Sumanthpoojaryy/school-bus-monitoring-system-using-esp32/assets/149647214/42f86366-fd75-44ac-b5c1-d4b223e0ad03)


**Flowchart**
![FlowChart]![FlowChart]![WhatsApp Image 2023-11-22 at 10 59 20 AM]![Picture8](https://github.com/Sumanthpoojaryy/school-bus-monitoring-system-using-esp32/assets/149647214/5d33932a-bb31-4579-a1fa-57847c08f0b3)


**Block Diagram**
![Block Diagram]!![Picture9](https://github.com/Sumanthpoojaryy/school-bus-monitoring-system-using-esp32/assets/149647214/2e5cca5f-ee68-4d6d-b300-43a14d4b1f97)

**Code**
/* terminal1 program
 
 ----------------------------------------------------------------------------- 
 * Pin layout should be as follows:
 * Signal     Pin              Pin               Pin
 *            Arduino Uno      Arduino Mega      MFRC522 board
 * ------------------------------------------------------------
 * Reset      9                5                 RST
 * SPI SS     10               53                SDA
 * SPI MOSI   11               51                MOSI
 * SPI MISO   12               50                MISO
 * SPI SCK    13               52                SCK
 * voltage 3.3v 
 */
 
 
 // gsm module is connected with pin number 7 and pin number 8 of the arduino. 

#include <SPI.h>
#include <MFRC522.h>
#include <LiquidCrystal.h>
#include <SoftwareSerial.h>
#include <Servo.h>
SoftwareSerial SIM900(2, 3); // gsm module connected here
String textForSMS;
SoftwareSerial SUART(0,1 ); //SRX = DPin-2; STX = DPin-3
#define SS_PIN 10
#define RST_PIN 9
MFRC522 mfrc522(SS_PIN, RST_PIN);  
Servo myServo;      // Create MFRC522 instance.
// lcd pins
#define rs A0
#define en A2 
#define d4 6 
#define d5 5  
#define d6 4 
#define d7 3 
// initialize the library with the numbers of the interface pins
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);
// parents numbers 
String f1001 = "+919019262751"; // student1 father cell phone number
String f1002 = "+917499992310"; 
String f1003 = "+917499992310"; 

void setup() {
  myServo.attach(8);
        Serial.begin(9600); 
         SUART.begin(9600);       // Nodemcu is connected over here
        randomSeed(analogRead(0));
        SIM900.begin(9600); // original 19200. while enter 9600 for sim900A
        SPI.begin();                // Init SPI bus
        mfrc522.PCD_Init();        // Init MFRC522 card
        //Serial.println("Scan a MIFARE Classic PICC to demonstrate Value Blocks.");
        
          // set up the LCD's number of columns and rows: 
  lcd.begin(16, 2);
  // Print a message to the LCD.
  lcd.print("AttendanceSystem");
  delay(3000); 
  lcd.clear(); 
  lcd.print("Swip Your Card"); 
  delay(1000); 
  
  
}

void loop() {
        // Prepare key - all keys are set to FFFFFFFFFFFFh at chip delivery from the factory.
        MFRC522::MIFARE_Key key;
        for (byte i = 0; i < 6; i++) {
                key.keyByte[i] = 0xFF;
        }
        // Look for new cards
        if ( ! mfrc522.PICC_IsNewCardPresent()) {
                return;
        }

        // Select one of the cards
        if ( ! mfrc522.PICC_ReadCardSerial()) {
                return;
        }
        // Now a card is selected. The UID and SAK is in mfrc522.uid.
        
        // Dump UID
        Serial.print("Card UID: ");
        for (byte i = 0; i < mfrc522.uid.size; i++) {
             //   Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
             //   Serial.print(mfrc522.uid.uidByte[i], DEC);
        } 
        Serial.println();

        // Dump PICC type
        byte piccType = mfrc522.PICC_GetType(mfrc522.uid.sak);
    //    Serial.print("PICC type: ");
//Serial.println(mfrc522.PICC_GetTypeName(piccType));
        if (        piccType != MFRC522::PICC_TYPE_MIFARE_MINI 
                &&        piccType != MFRC522::PICC_TYPE_MIFARE_1K
                &&        piccType != MFRC522::PICC_TYPE_MIFARE_4K) {
                //Serial.println("This sample only works with MIFARE Classic cards.");
                return;
        }
        // defining Cards here 
 
                // student1
        if( (mfrc522.uid.uidByte[0] == 03) && (mfrc522.uid.uidByte[1] == 137) && (mfrc522.uid.uidByte[2] == 94) && (mfrc522.uid.uidByte[3] == 47) ) // student1 card
        {
          lcd.clear();
          lcd.print("AttendanceMarked");
          delay(500); 
Serial.println("student 1");
    char myMsg[] = "student 1";
     Serial.println(myMsg); 
     SUART.println(myMsg); // Wait for 1 second
  myServo.write(180); // Move the servo to 90 degrees
        
         // for gsm   
          sendsms(" shravan is Present", f1001);
          delay(1000);   
        
         lcd.setCursor(0, 1);
         lcd.print("message sent"); 
         delay(2000); 
         
         lcd.clear(); 
          lcd.print("Swip Your Card");
          delay(1000); 
        }
        
                        // student2
        if( (mfrc522.uid.uidByte[0] == 83) && (mfrc522.uid.uidByte[1] == 23) && (mfrc522.uid.uidByte[2] == 180) && (mfrc522.uid.uidByte[3] == 80) ) // student2 card
        {
         lcd.clear();
          lcd.print("AttendanceMarked");
          delay(500); 
  Serial.println("student 2");
  char myMsg[] = "student 2";
     Serial.println(myMsg); 
     SUART.println(myMsg); // Wait for 1 second
  myServo.write(180); 
        
         // for gsm   
          sendsms("sumanthis Present", f1002);
          delay(1000);   
        
         lcd.setCursor(0, 1);
         lcd.print("message sent"); 
         delay(2000); 
         
         lcd.clear(); 
          lcd.print("Swip Your Card");
          delay(1000); 
        }
       

        else 
        Serial.println("unregistered user");

       
}


void sendsms(String message, String number)
{
String mnumber = "AT + CMGS = \""+number+"\""; 
   SIM900.print("AT+CMGF=1\r");                   
  delay(1000);
 SIM900.println(mnumber);  // recipient's mobile number, in international format
 
  delay(1000);
  SIM900.println(message);                         // message to send
  delay(1000);
  SIM900.println((char)26);                        // End AT command with a ^Z, ASCII code 26
  delay(1000); 
  SIM900.println();
  delay(100);                                     // give module time to send SMS
 // SIM900power();  
}










