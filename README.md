# Visitor Counter System

An IoT-based Visitor Counter System built using **Arduino Uno** and **Processing IDE** to automatically monitor the number of people inside a room in real time. The system uses infrared sensors to detect visitor movement, controls door access with a servo motor, and provides visual and audio indicators based on room occupancy.

---

## 📖 About the Project

The purpose of this project is to develop an automatic visitor counting system that can accurately detect people entering and leaving a room. The collected data is processed by an Arduino Uno and displayed through a graphical user interface (GUI) built with Processing IDE.

The system also provides room capacity monitoring using three LED indicators and a buzzer, making it suitable for classrooms, laboratories, offices, libraries, and other indoor public facilities.

---

## ✨ Features

- Automatic visitor counting
- Real-time visitor monitoring
- Entry and exit detection using infrared sensors
- Graphical User Interface (GUI)
- Manual door lock control
- Automatic servo door control
- LED room status indicator
- Audio notification using buzzer
- Counter reset function

---

## 🛠 Hardware

- Arduino Uno R3
- 2 × Infrared Sensors
- Servo Motor SG90
- Green LED
- Yellow LED
- Red LED
- Active Buzzer
- 220Ω Resistors
- PCB
- Jumper Wires

---

## 💻 Software

- Arduino IDE
- Processing IDE

---

## ⚙️ System Operation

1. Initialize all hardware components.
2. Read data from the infrared sensors.
3. Detect whether a visitor enters or exits the room.
4. Update the visitor count.
5. Display the visitor count on the GUI.
6. Update LED indicators according to room capacity.
7. Activate the buzzer when the room reaches maximum capacity.
8. Control the servo motor based on the system condition.

---

## 🚦 Room Capacity Indicator

| Visitor Count | Room Status | Green LED | Yellow LED | Red LED | Buzzer |
|---------------|------------|-----------|------------|----------|---------|
| ≤ 7 | Available | ON | OFF | OFF | OFF |
| 8 – 9 | Almost Full | OFF | ON | OFF | OFF |
| 10 | Full | OFF | OFF | ON | ON |

---

## 📂 Repository Structure

```text
Visitor-Counter-System/
│
├── Arduino/
│   └── VisitorCounter.ino
│
├── Processing/
│   └── VisitorCounterGUI.pde
│
└── README.md
```

---

## 📑 Project Documentation

The complete project documentation is available here:

📄 **Project Report**

> *https://drive.google.com/file/d/1Nhkr1sqGsCPVhyi6MNL609RB2q0AyFvw/view?usp=drivesdk*

---

## 🎥 Demonstration Video

Watch the complete demonstration of the project:

▶️*https://drive.google.com/file/d/13tOIeBStrh4Ng6t4nu_oH4AYyaTzRa-4/view?usp=drivesdk*

---

## 👨‍💻 Author

**Muhammad Rafif Muzaky**

Universitas Gunadarma

---

## 📄 License

This project is intended for educational and research purposes.
