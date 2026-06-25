// ============================================================
//  PROCESSING GUI - PENGHITUNG PENGUNJUNG
//  Tabel 3.1: kondisi LED & Buzzer sesuai dokumen
//  Komunikasi Serial dengan Arduino UNO @ 9600 baud
// ============================================================
import processing.serial.*;

Serial port;

int     jumlah   = 0;
int     maxOrang = 10;
boolean locked   = false;
String  statusMsg = "Menghubungkan...";

// Warna
color C_BG      = color(15, 15, 25);
color C_PANEL   = color(25, 25, 42);
color C_GREEN   = color(50, 220, 100);
color C_YELLOW  = color(255, 200, 40);
color C_RED     = color(220, 55, 55);
color C_BLUE    = color(70, 150, 255);
color C_WHITE   = color(225, 225, 235);
color C_GRAY    = color(110, 110, 130);
color C_DIMGRAY = color(50, 50, 68);

// Tombol Lock
int btnLX, btnLY, btnLW = 230, btnLH = 62;
// Tombol Reset
int btnRX, btnRY, btnRW = 160, btnRH = 46;

// Animasi
float barAnim    = 0;
float pulseAlpha = 255;
int   pulseDir   = -4;
boolean ledKedipState = false;
int     ledKedipTimer = 0;

// LED indikator virtual (mirror dari Arduino)
boolean vHijau  = false;
boolean vKuning = false;
boolean vMerah  = false;
boolean vBuzzer = false;

void setup() {
  size(540, 660);
  smooth(4);

  btnLX = width/2 - btnLW/2;
  btnLY = 420;
  btnRX = width/2 - btnRW/2;
  btnRY = 510;

  println("Port tersedia:");
  printArray(Serial.list());

  try {
    port = new Serial(this, "COM4", 9600);
    port.bufferUntil('\n');
    statusMsg = "Terhubung: " + Serial.list()[0];
  } catch (Exception e) {
    statusMsg = "ERROR: Port tidak ditemukan!";
  }
}

void draw() {
  background(C_BG);

  // Animasi
  float target = (float)jumlah / maxOrang;
  barAnim += (target - barAnim) * 0.07;

  pulseAlpha += pulseDir;
  if (pulseAlpha <= 80 || pulseAlpha >= 255) pulseDir *= -1;
  pulseAlpha = constrain(pulseAlpha, 80, 255);

  // Update LED virtual sesuai Tabel 3.1
  hitungLEDVirtual();

  gambarHeader();
  gambarCounter();
  gambarProgressBar();
  gambarLEDPanel();
  gambarTombolLock();
  gambarTombolReset();
  gambarStatusBar();
}

// ============================================================
//  HITUNG STATUS LED VIRTUAL (mirror Tabel 3.1)
// ============================================================
void hitungLEDVirtual() {
  if (locked) {
    vHijau  = false;
    vBuzzer = false;
    if (jumlah > 7) {
      // Baris 5
      vKuning = true;
      vMerah  = ledKedipState;
    } else {
      // Baris 4
      vKuning = false;
      vMerah  = ledKedipState;
    }
    // Kedip timer
    if (millis() - ledKedipTimer > 400) {
      ledKedipTimer = millis();
      ledKedipState = !ledKedipState;
    }
  } else {
    if (jumlah >= maxOrang) {
      // Baris 2: PENUH
      vHijau  = true;
      vKuning = false;
      vMerah  = true;
      vBuzzer = true;  // ON TERUS
    } else if (jumlah > 7) {
      // Baris 3: warning
      vHijau  = true;
      vKuning = true;
      vMerah  = false;
      vBuzzer = false;
    } else {
      // Baris 1: normal
      vHijau  = true;
      vKuning = false;
      vMerah  = false;
      vBuzzer = false;
    }
  }
}

// ============================================================
//  HEADER
// ============================================================
void gambarHeader() {
  fill(C_PANEL);
  noStroke();
  rect(0, 0, width, 78);

  fill(C_WHITE);
  textAlign(CENTER, CENTER);
  textSize(19);
  text("PENGHITUNG PENGUNJUNG", width/2, 26);

  fill(C_GRAY);
  textSize(11);
  text("Sistem Monitoring Kapasitas Otomatis", width/2, 54);
}

// ============================================================
//  COUNTER
// ============================================================
void gambarCounter() {
  fill(C_PANEL);
  noStroke();
  rect(55, 96, width-110, 148, 14);

  // Warna angka
  color cAngka;
  if (jumlah >= maxOrang && !locked) {
    cAngka = color(red(C_RED), green(C_RED), blue(C_RED), pulseAlpha);
  } else if (locked) {
    cAngka = color(red(C_RED), green(C_RED), blue(C_RED), pulseAlpha);
  } else if (jumlah > 7) {
    cAngka = C_YELLOW;
  } else {
    cAngka = C_GREEN;
  }

  fill(cAngka);
  textAlign(CENTER, CENTER);
  textSize(76);
  text(jumlah, width/2 - 28, 178);

  fill(C_GRAY);
  textSize(46);
  text("/", width/2 + 12, 175);

  fill(C_GRAY);
  textSize(34);
  text(maxOrang, width/2 + 62, 176);

  fill(C_GRAY);
  textSize(11);
  text("orang di dalam", width/2, 228);
}

// ============================================================
//  PROGRESS BAR
// ============================================================
void gambarProgressBar() {
  int bx = 55, by = 265, bw = width-110, bh = 26;

  fill(C_DIMGRAY);
  noStroke();
  rect(bx, by, bw, bh, 13);

  color cBar;
  if (jumlah >= maxOrang && !locked) {
    cBar = color(red(C_RED), green(C_RED), blue(C_RED), (int)pulseAlpha);
  } else if (locked) {
    cBar = color(red(C_RED), green(C_RED), blue(C_RED), (int)pulseAlpha);
  } else if (jumlah > 7) {
    cBar = C_YELLOW;
  } else {
    cBar = C_GREEN;
  }

  float isi = constrain(barAnim, 0, 1) * bw;
  if (isi > 0) {
    fill(cBar);
    rect(bx, by, isi, bh, 13);
  }

  fill(C_WHITE);
  textSize(11);
  textAlign(CENTER, CENTER);
  text((int)(barAnim * 100) + "%", width/2, by + bh/2);

  // Marker warning di >7 = 70%
  stroke(C_YELLOW);
  strokeWeight(2);
  float mx = bx + bw * 0.7;
  line(mx, by - 4, mx, by + bh + 4);
  noStroke();
  fill(C_YELLOW);
  textSize(9);
  textAlign(CENTER, TOP);
  text("WARNING", mx, by + bh + 5);

  // Status kondisi
  String kondisi;
  color  cK;
  if (locked) {
    kondisi = "● SISTEM TERKUNCI";
    cK = color(red(C_RED), green(C_RED), blue(C_RED), (int)pulseAlpha);
  } else if (jumlah >= maxOrang) {
    kondisi = "● PENUH — BUZZER ON";
    cK = color(red(C_RED), green(C_RED), blue(C_RED), (int)pulseAlpha);
  } else if (jumlah > 7) {
    kondisi = "● HAMPIR PENUH — PERINGATAN";
    cK = C_YELLOW;
  } else {
    kondisi = "● NORMAL — TERSEDIA";
    cK = C_GREEN;
  }

  fill(cK);
  textSize(13);
  textAlign(CENTER, CENTER);
  text(kondisi, width/2, 322);

  fill(C_GRAY);
  textSize(11);
  text("Sisa: " + (maxOrang - jumlah) + " orang", width/2, 344);
}

// ============================================================
//  PANEL LED VIRTUAL (sesuai Tabel 3.1)
// ============================================================
void gambarLEDPanel() {
  int py = 368, ph = 42;
  fill(C_PANEL);
  noStroke();
  rect(55, py, width-110, ph, 10);

  // Label LED
  String[] labels = {"HIJAU", "KUNING", "MERAH", "BUZZER"};
  boolean[] states = {vHijau, vKuning, vMerah, vBuzzer};
  color[] colors   = {C_GREEN, C_YELLOW, C_RED, color(255, 120, 50)};

  int total = labels.length;
  int cellW = (width - 110) / total;

  for (int i = 0; i < total; i++) {
    int cx = 55 + i * cellW + cellW / 2;
    int cy = py + ph / 2;

    // LED bulat
    float alpha = states[i] ? 255 : 60;
    color cLed = color(red(colors[i]), green(colors[i]), blue(colors[i]), alpha);

    // Glow jika aktif
    if (states[i]) {
      noStroke();
      fill(red(colors[i]), green(colors[i]), blue(colors[i]), 40);
      ellipse(cx - 22, cy, 20, 20);
    }

    fill(cLed);
    noStroke();
    ellipse(cx - 22, cy, 14, 14);

    // Teks
    fill(states[i] ? colors[i] : C_GRAY);
    textSize(9);
    textAlign(LEFT, CENTER);
    text(labels[i], cx - 13, cy);
  }
}

// ============================================================
//  TOMBOL LOCK / UNLOCK
// ============================================================
void gambarTombolLock() {
  boolean hover = (mouseX >= btnLX && mouseX <= btnLX + btnLW &&
                   mouseY >= btnLY && mouseY <= btnLY + btnLH);

  color cBtn = locked
    ? color(180, 45, 45)
    : color(35, 155, 80);
  if (hover) cBtn = color(red(cBtn)+30, green(cBtn)+30, blue(cBtn)+30);

  fill(cBtn);
  noStroke();
  rect(btnLX, btnLY, btnLW, btnLH, 12);

  fill(C_WHITE);
  textAlign(CENTER, CENTER);
  textSize(15);
  text(locked ? "UNLOCK SISTEM" : "LOCK SISTEM", btnLX + btnLW/2, btnLY + btnLH/2 - 6);

  fill(color(255, 255, 255, 150));
  textSize(10);
  text(locked ? "Klik untuk buka akses" : "Klik untuk kunci akses",
       btnLX + btnLW/2, btnLY + btnLH - 12);
}

// ============================================================
//  TOMBOL RESET
// ============================================================
void gambarTombolReset() {
  boolean hover = (mouseX >= btnRX && mouseX <= btnRX + btnRW &&
                   mouseY >= btnRY && mouseY <= btnRY + btnRH);

  fill(hover ? color(80, 92, 130) : color(55, 65, 100));
  noStroke();
  rect(btnRX, btnRY, btnRW, btnRH, 10);

  fill(C_WHITE);
  textAlign(CENTER, CENTER);
  textSize(13);
  text("RESET COUNTER", btnRX + btnRW/2, btnRY + btnRH/2);
}

// ============================================================
//  STATUS BAR BAWAH
// ============================================================
void gambarStatusBar() {
  fill(color(10, 10, 18));
  noStroke();
  rect(0, height - 34, width, 34);

  fill(C_GRAY);
  textAlign(LEFT, CENTER);
  textSize(10);
  text("  " + statusMsg, 0, height - 17);

  fill(C_BLUE);
  textAlign(RIGHT, CENTER);
  text("9600 baud  ", width, height - 17);
}

// ============================================================
//  MOUSE
// ============================================================
void mousePressed() {
  // Tombol Lock/Unlock
  if (mouseX >= btnLX && mouseX <= btnLX + btnLW &&
      mouseY >= btnLY && mouseY <= btnLY + btnLH) {
    if (port != null) {
      if (locked) {
        port.write('U');
        statusMsg = "Perintah: UNLOCK dikirim";
      } else {
        port.write('L');
        statusMsg = "Perintah: LOCK dikirim";
      }
    }
  }

  // Tombol Reset
  if (mouseX >= btnRX && mouseX <= btnRX + btnRW &&
      mouseY >= btnRY && mouseY <= btnRY + btnRH) {
    if (port != null) {
      port.write('R');
      statusMsg = "Perintah: RESET dikirim";
    }
  }
}

// ============================================================
//  TERIMA DATA SERIAL
// ============================================================
void serialEvent(Serial p) {
  String data = trim(p.readStringUntil('\n'));
  if (data == null || data.length() == 0) return;

  String[] parts = split(data, ',');
  if (parts.length == 3) {
    try {
      jumlah   = int(parts[0]);
      maxOrang = int(parts[1]);
      locked   = (int(parts[2]) == 1);
      statusMsg = "Data: " + jumlah + "/" + maxOrang +
                  " | " + (locked ? "TERKUNCI" : "AKTIF") +
                  " | Buzzer: " + (vBuzzer ? "ON" : "OFF");
    } catch (Exception e) {
      statusMsg = "Parse error: " + data;
    }
  }
}
