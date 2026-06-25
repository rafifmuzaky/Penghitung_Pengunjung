// ============================================================
//  PENGHITUNG PENGUNJUNG OTOMATIS
//  Hardware : Arduino UNO
//  Komponen : 2x IR Sensor, 2x Servo SG90, 3x LED, 1x Buzzer
//  Serial   : 9600 baud → Processing GUI
//  Kapasitas: MAX 10 orang, WARNING mulai >7 orang
//  Kondisi LED sesuai Tabel 3.1
// ============================================================
#include <Servo.h>

// ---------- PIN MAPPING ----------
const int PIN_IR_MASUK   = 2;
const int PIN_IR_KELUAR  = 3;
const int PIN_LED_HIJAU  = 4;
const int PIN_LED_MERAH  = 5;
const int PIN_LED_KUNING = 6;
const int PIN_BUZZER     = 8;
const int PIN_SERVO1     = 9;   // portal masuk
const int PIN_SERVO2     = 10;  // portal keluar

// ---------- KONSTANTA ----------
const int MAX_KAPASITAS      = 10;
const int BATAS_WARNING      = 7;    // >7 → LED kuning menyala
const int SUDUT_BUKA         = 90;
const int SUDUT_TUTUP        = 0;
const int DURASI_SERVO       = 2000; // ms pintu terbuka
const int DURASI_BEEP        = 300;  // ms beep masuk/keluar
const unsigned long DEBOUNCE = 500;  // ms debounce sensor

// ---------- OBJEK & VARIABEL ----------
Servo servo1;
Servo servo2;

int  jumlahOrang    = 0;
bool sistemTerkunci = false;

bool servo1Aktif    = false;
bool servo2Aktif    = false;
bool beepAktif      = false;

unsigned long waktuServo1    = 0;
unsigned long waktuServo2    = 0;
unsigned long waktuBeep      = 0;
unsigned long waktuDebounce1 = 0;
unsigned long waktuDebounce2 = 0;
unsigned long waktuKirim     = 0;
unsigned long waktuKedip     = 0;

bool ir1Sebelum = HIGH;
bool ir2Sebelum = HIGH;
bool ledKedip   = false;

// ---------- SETUP ----------
void setup() {
  Serial.begin(9600);

  pinMode(PIN_IR_MASUK,   INPUT);
  pinMode(PIN_IR_KELUAR,  INPUT);
  pinMode(PIN_LED_HIJAU,  OUTPUT);
  pinMode(PIN_LED_MERAH,  OUTPUT);
  pinMode(PIN_LED_KUNING, OUTPUT);
  pinMode(PIN_BUZZER,     OUTPUT);

  servo1.attach(PIN_SERVO1);
  servo2.attach(PIN_SERVO2);
  servo1.write(SUDUT_TUTUP);
  servo2.write(SUDUT_TUTUP);

  updateLED();
  updateBuzzer();
  kirimStatus();
}

// ---------- LOOP ----------
void loop() {
  bacaSerial();
  unsigned long sekarang = millis();

  // ============================================================
  //  MODE TERKUNCI
  // ============================================================
  if (sistemTerkunci) {
    // Paksa servo tutup
    servo1.write(SUDUT_TUTUP);
    servo2.write(SUDUT_TUTUP);
    servo1Aktif = false;
    servo2Aktif = false;

    // LED kedip merah (mode terkunci)
    if (sekarang - waktuKedip >= 400) {
      waktuKedip = sekarang;
      ledKedip = !ledKedip;
      updateLEDTerkunci();
    }

    // Abaikan IR sensor saat terkunci
  }
  // ============================================================
  //  MODE NORMAL
  // ============================================================
  else {
    // Auto-tutup servo setelah DURASI_SERVO
    if (servo1Aktif && (sekarang - waktuServo1 >= DURASI_SERVO)) {
      servo1.write(SUDUT_TUTUP);
      servo1Aktif = false;
    }
    if (servo2Aktif && (sekarang - waktuServo2 >= DURASI_SERVO)) {
      servo2.write(SUDUT_TUTUP);
      servo2Aktif = false;
    }

    // ---- IR MASUK ----
    bool ir1 = digitalRead(PIN_IR_MASUK);
    if (ir1 == LOW && ir1Sebelum == HIGH &&
        (sekarang - waktuDebounce1 >= DEBOUNCE)) {
      waktuDebounce1 = sekarang;

      if (jumlahOrang < MAX_KAPASITAS) {
        jumlahOrang++;
        bukaServo1(sekarang);
        mulaiBeep(sekarang);   // beep singkat saat masuk
        updateLED();
        updateBuzzer();
        kirimStatus();
      }
      // Jika sudah penuh: tidak buka pintu, buzzer sudah ON terus
    }
    ir1Sebelum = ir1;

    // ---- IR KELUAR ----
    bool ir2 = digitalRead(PIN_IR_KELUAR);
    if (ir2 == LOW && ir2Sebelum == HIGH &&
        (sekarang - waktuDebounce2 >= DEBOUNCE)) {
      waktuDebounce2 = sekarang;

      if (jumlahOrang > 0) {
        jumlahOrang--;
        bukaServo2(sekarang);
        mulaiBeep(sekarang);   // beep singkat saat keluar
        updateLED();
        updateBuzzer();
        kirimStatus();
      }
    }
    ir2Sebelum = ir2;

    updateLED();
    updateBuzzer();
  }

  // ---- Matikan beep sementara (hanya saat tidak penuh) ----
  // Saat penuh buzzer ON terus, jadi jangan matikan
  if (beepAktif && jumlahOrang < MAX_KAPASITAS &&
      (sekarang - waktuBeep >= DURASI_BEEP)) {
    beepAktif = false;
    updateBuzzer(); // re-evaluasi buzzer setelah beep selesai
  }

  // Kirim status periodik tiap 500ms
  if (sekarang - waktuKirim >= 500) {
    waktuKirim = sekarang;
    kirimStatus();
  }
}

// ============================================================
//  FUNGSI UPDATE LED — sesuai Tabel 3.1
// ============================================================
void updateLED() {
  if (sistemTerkunci) return; // ditangani updateLEDTerkunci()

  bool hijau  = false;
  bool kuning = false;
  bool merah  = false;

  if (jumlahOrang >= MAX_KAPASITAS) {
    // Baris 2: jumlah = 10 → Hijau ON, Kuning OFF, Merah ON
    hijau  = true;
    kuning = false;
    merah  = true;
  } else if (jumlahOrang > BATAS_WARNING) {
    // Baris 3: >7 <10 → Hijau ON, Kuning ON, Merah OFF
    hijau  = true;
    kuning = true;
    merah  = false;
  } else {
    // Baris 1: <10 (normal) → Hijau ON, Kuning OFF, Merah OFF
    hijau  = true;
    kuning = false;
    merah  = false;
  }

  digitalWrite(PIN_LED_HIJAU,  hijau  ? HIGH : LOW);
  digitalWrite(PIN_LED_KUNING, kuning ? HIGH : LOW);
  digitalWrite(PIN_LED_MERAH,  merah  ? HIGH : LOW);
}

// LED saat sistem terkunci (baris 4 & 5 tabel)
void updateLEDTerkunci() {
  bool hijau  = false;
  bool kuning = false;
  bool merah  = false;

  if (jumlahOrang > BATAS_WARNING) {
    // Baris 5: >7 <10 locked → Hijau OFF, Kuning ON, Merah ON (kedip)
    kuning = true;
    merah  = true;
  } else {
    // Baris 4: <10 locked → Hijau OFF, Kuning OFF, Merah ON (kedip)
    merah  = true;
  }

  digitalWrite(PIN_LED_HIJAU,  hijau  ? HIGH : LOW);
  digitalWrite(PIN_LED_KUNING, kuning ? HIGH : LOW);
  digitalWrite(PIN_LED_MERAH,  merah  ? HIGH : LOW);
}

// ============================================================
//  FUNGSI UPDATE BUZZER
// ============================================================
void updateBuzzer() {
  if (sistemTerkunci) {
    // Tabel baris 4 & 5: saat terkunci buzzer OFF
    if (!beepAktif) {
      digitalWrite(PIN_BUZZER, LOW);
    }
    return;
  }

  if (jumlahOrang >= MAX_KAPASITAS) {
    // Baris 2: PENUH → buzzer ON TERUS (bukan beep)
    digitalWrite(PIN_BUZZER, HIGH);
  } else if (beepAktif) {
    // Beep masuk/keluar sedang aktif
    digitalWrite(PIN_BUZZER, HIGH);
  } else {
    // Normal → buzzer OFF
    digitalWrite(PIN_BUZZER, LOW);
  }
}

// ============================================================
//  FUNGSI HELPER
// ============================================================
void bacaSerial() {
  if (Serial.available() > 0) {
    char cmd = Serial.read();
    if (cmd == 'L') {
      sistemTerkunci = true;
      digitalWrite(PIN_BUZZER, LOW); // matikan buzzer saat dikunci
      kirimStatus();
    } else if (cmd == 'U') {
      sistemTerkunci = false;
      updateLED();
      updateBuzzer();
      kirimStatus();
    } else if (cmd == 'R') {
      jumlahOrang = 0;
      beepAktif   = false;
      updateLED();
      updateBuzzer();
      kirimStatus();
    }
  }
}

void bukaServo1(unsigned long t) {
  servo1.write(SUDUT_BUKA);
  servo1Aktif = true;
  waktuServo1 = t;
}

void bukaServo2(unsigned long t) {
  servo2.write(SUDUT_BUKA);
  servo2Aktif = true;
  waktuServo2 = t;
}

void mulaiBeep(unsigned long t) {
  // Hanya set flag beep; buzzer aktual diatur updateBuzzer()
  // Tidak perlu beep jika sudah penuh (buzzer sudah ON terus)
  if (jumlahOrang < MAX_KAPASITAS) {
    beepAktif = true;
    waktuBeep = t;
  }
}

void kirimStatus() {
  // Format: "COUNT,MAX,LOCKED\n"
  Serial.print(jumlahOrang);
  Serial.print(",");
  Serial.print(MAX_KAPASITAS);
  Serial.print(",");
  Serial.println(sistemTerkunci ? 1 : 0);
}
