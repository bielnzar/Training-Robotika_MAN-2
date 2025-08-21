# 🤖 Modul Hari ke-1: Pengenalan Sensor & Komponen

Selamat datang, para calon inovator! 👋

Di modul ini, kita akan berpetualang ke dunia Internet of Things (IoT) dan Robotika. Di hari pertama ini, kita akan fokus untuk mengenal dan memberikan "indra" serta "indikator" pada calon robot kita. Kita akan belajar dari hal yang paling dasar, yaitu mengenal komponen, memahami cara kerjanya, hingga bisa membuat beberapa komponen bekerja sama.

Mari kita mulai petualangan ini selangkah demi selangkah!

## 🛠️ Alat dan Bahan

Sebelum mulai, pastikan kalian sudah menyiapkan semua komponen ini:

**Otak Robot:** Arduino Nano

**Mata Robot:**
1. Sensor Jarak Ultrasonik HC-SR04
2. 2x Sensor Garis Inframerah (IR)

**Indikator:** 3x Lampu LED (Merah, Kuning, Hijau)

**Lain-lain:**
1. Project Board / Breadboard
2. Kabel Jumper
3. Kabel USB

---

## Materi 1: Kerangka Program Arduino 🧠

Setiap program Arduino memiliki dua bagian utama yang wajib ada. Anggap saja seperti resep masakan yang punya bagian "Persiapan" dan "Cara Memasak".

### 1. `void setup()`
Fungsi ini berjalan **satu kali** saja, yaitu saat Arduino pertama kali dinyalakan atau saat tombol reset ditekan.

**Analogi:** Ini adalah tahap **persiapan**. Di sini kita menyiapkan semua "alat" yang akan kita gunakan. Misalnya, memberi tahu Arduino pin mana yang akan menjadi output (untuk menyalakan LED) dan pin mana yang menjadi input (untuk membaca sensor).

**Contoh Sintaks:**
```cpp
void setup() {
  // Kode persiapan ditaruh di sini
  pinMode(13, OUTPUT); // Contoh: Menyiapkan pin 13 sebagai output
  Serial.begin(9600);  // Contoh: Memulai komunikasi serial untuk debugging
}
```

### 2. `void loop()`
Setelah `setup()` selesai, Arduino akan menjalankan fungsi `loop()` **terus-menerus** tanpa henti, berulang-ulang dari awal hingga akhir, lalu kembali lagi ke awal.

**Analogi:** Ini adalah tahap **cara memasak**. Di sinilah semua aksi utama terjadi. Arduino akan terus mengulangi instruksi yang ada di dalam `loop()` ini. Misalnya, menyalakan LED, mematikannya, membaca sensor, dan sebagainya.

**Contoh Sintaks:**
```cpp
void loop() {
  // Kode utama yang akan diulang-ulang ditaruh di sini
  digitalWrite(13, HIGH); // Contoh: Menyalakan LED di pin 13
  delay(1000);            // Contoh: Tunggu 1 detik
  digitalWrite(13, LOW);  // Contoh: Mematikan LED di pin 13
  delay(1000);            // Contoh: Tunggu 1 detik lagi
}
```
Setiap program yang akan kita buat nanti pasti akan memiliki struktur `setup()` dan `loop()` ini.

---

## Materi 2: Menyalakan Lampu LED💡
Ini adalah langkah pertama paling dasar di dunia mikrokontroler. Menyalakan LED itu seperti kita bilang "Halo!" ke Arduino.

**Analogi:** Bayangkan Arduino itu adalah saklar listrik pintar. LED adalah lampunya. Kita akan memberi perintah ke "saklar pintar" ini untuk menyalakan dan mematikan lampu.

**Pin yang kita gunakan:**
*   LED Merah: Pin `D2`
*   LED Kuning: Pin `D3`
*   LED Hijau: Pin `D4`

### 2.1 Menyalakan Satu Lampu (Merah)
**Rangkaian:**
*   (+) LED Merah hubungkan ke Pin `D2` Arduino.
*   (-) LED Merah hubungkan ke Pin `GND` Arduino.

**Kode Program 1:**
```cpp
// Materi 2.1: Menyalakan Satu LED
void setup() {
  pinMode(2, OUTPUT); // Memberi tahu Arduino pin 2 adalah OUTPUT
}

void loop() {
  digitalWrite(2, HIGH); // Menyalakan LED
  delay(1000);         // Tunggu 1 detik
  digitalWrite(2, LOW);  // Mematikan LED
  delay(1000);         // Tunggu 1 detik
}
```

### 2.2 Simulasi Lampu Lalu Lintas
**Rangkaian:**
*   LED Merah (+) -> Pin `D2`
*   LED Kuning (+) -> Pin `D3`
*   LED Hijau (+) -> Pin `D4`
*   (-) LED -> `GND`

**Kode Program 2:**
```cpp
// Materi 2.2: Simulasi Lampu Lalu Lintas
void setup() {
  pinMode(2, OUTPUT); // LED Merah
  pinMode(3, OUTPUT); // LED Kuning
  pinMode(4, OUTPUT); // LED Hijau
}

void loop() {
  // Lampu Hijau nyala
  digitalWrite(4, HIGH);
  digitalWrite(2, LOW);
  digitalWrite(3, LOW);
  delay(3000);

  // Lampu Kuning nyala
  digitalWrite(3, HIGH);
  digitalWrite(2, LOW);
  digitalWrite(4, LOW);
  delay(1000);

  // Lampu Merah nyala
  digitalWrite(2, HIGH);
  digitalWrite(3, LOW);
  digitalWrite(4, LOW);
  delay(3000);
}
```

> **💡 Info Tambahan: `const int` vs Angka Langsung (Magic Numbers)**
>
> Kamu mungkin bertanya-tanya, kenapa di beberapa kode kita melihat `const int ledPin = 2;` sementara di contoh lain kita langsung menulis angka `2`?
>
> 1.  **Menulis Angka Langsung (Contoh: `pinMode(2, OUTPUT)`)**
>     *   **Praktik Kurang Baik:** Cara ini disebut menggunakan *"Magic Numbers"*. Untuk program yang sangat sederhana, ini mungkin terlihat lebih cepat.
>     *   **Kelemahan:** Saat program membesar, angka `2` tidak memberi tahu kita apa fungsinya. Jika kita menggunakan pin 2 di banyak tempat dan suatu saat ingin memindahnya ke pin lain, kita harus mencari dan mengganti semua angka `2` secara manual, yang sangat merepotkan dan rawan kesalahan.
>
> 2.  **Menggunakan Variabel `const int` (Contoh: `const int ledMerah = 2;`)**
>     *   **Praktik Terbaik:** Ini adalah cara yang sangat disarankan.
>     *   **Keuntungan:**
>         *   **Keterbacaan:** Kode menjadi jauh lebih mudah dibaca. `ledMerah` jelas berarti pin untuk LED merah.
>         *   **Kemudahan Modifikasi:** Jika ingin mengganti pin, kamu cukup mengubah nilainya di satu tempat, yaitu di baris deklarasi variabel di awal program.
>         *   **Menghindari Kesalahan:** Mencegah salah ketik nomor pin di bagian lain dari kode.
>     *   **Apa itu `const`?** `const` adalah singkatan dari *constant*. Ini memberitahu Arduino bahwa nilai variabel ini (misalnya `ledMerah = 2`) bersifat konstan dan tidak akan berubah selama program berjalan. Ini adalah praktik yang baik untuk mendefinisikan pin.
>
> Singkatnya, selalu usahakan menggunakan `const int` untuk mendefinisikan pin agar kodemu lebih bersih, mudah dibaca, dan gampang dikelola.
---

## Materi 3: Mata Robot👀 Sensor Jarak Ultrasonik (HC-SR04)
Sensor ini berfungsi sebagai "mata" untuk robot kita agar bisa mendeteksi halangan di depannya.

**Analogi:** Cara kerja sensor ini mirip seperti kelelawar. Dia "berteriak" (pin Trig), lalu "mendengarkan" pantulan suaranya (pin Echo). Arduino akan mengukur waktu pantulan itu untuk menentukan jarak benda.

**Pin yang kita gunakan:**
*   Trig: `A3`
*   Echo: `A2`

**Rangkaian:**
*   Pin `VCC` sensor -> Pin `5V` Arduino
*   Pin `Trig` sensor -> Pin `A3` Arduino
*   Pin `Echo` sensor -> Pin `A2` Arduino
*   Pin `GND` sensor -> Pin `GND` Arduino

**Kode Program 3:**
```cpp
// Materi 3: Membaca Sensor Jarak HC-SR04
const int trigPin = A3;
const int echoPin = A2;
long duration;
int distance;

void setup() {
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
}

void loop() {
  // Mengirim pulsa suara
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  
  // Menerima pantulan dan menghitung jarak
  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;
  
  // Menampilkan hasil di Serial Monitor
  Serial.print("Jarak: ");
  Serial.print(distance);
  Serial.println(" cm");
  delay(500);
}
```
**Cara Melihat Hasil:** Upload kode, lalu klik ikon kaca pembesar di pojok kanan atas Arduino IDE (Serial Monitor).

> **💡 Info Tambahan: `Serial.print` vs `Serial.println`**
>
> Saat melihat kode sensor, kamu akan menemukan dua perintah yang mirip: `Serial.print()` dan `Serial.println()`. Apa bedanya?
>
> *   **`Serial.print(data)`**: Perintah ini akan mengirimkan data (teks, angka, dll.) ke Serial Monitor, tapi **kursor tetap berada di baris yang sama**. Jika kamu memanggil `Serial.print()` lagi, teks berikutnya akan muncul persis di sebelahnya.
>
> *   **`Serial.println(data)`**: Perintah ini melakukan hal yang sama, yaitu mengirim data, tetapi setelah selesai, ia akan **memindahkan kursor ke baris baru** (seperti menekan tombol "Enter"). `ln` adalah singkatan dari *line*.
>
> **Contoh Praktis:**
>
> Kode:
> ```cpp
> Serial.print("Jarak: ");
> Serial.print(25);
> Serial.println(" cm");
> ```
>
> Hasil di Serial Monitor:
> ```
> Jarak: 25 cm
> ```
>
> Jika kita menggunakan `Serial.print` untuk semuanya, hasilnya akan menjadi satu baris panjang yang sulit dibaca. Jika kita menggunakan `Serial.println` untuk semuanya, setiap bagian akan berada di baris baru. Kombinasi keduanya memungkinkan kita membuat output yang rapi dan terstruktur.

---

## Materi 4: Mata Garis! 〰️ Sensor Inframerah (IR)
Sensor ini adalah mata kedua robot kita, khusus untuk "melihat" garis hitam di permukaan putih.

**Analogi:** Sensor ini seperti senter dan detektor cahaya. Jika cahaya inframerah yang dipancarkan dipantulkan (permukaan putih), nilainya `LOW`. Jika diserap (permukaan hitam), nilainya `HIGH`.

**Pin yang kita gunakan:**
*   Sensor Kiri (Out): `A1`
*   Sensor Kanan (Out): `A0`

**Rangkaian:**
*   Sensor Kiri: `VCC` -> `5V`, `GND` -> `GND`, `OUT` -> `A1`
*   Sensor Kanan: `VCC` -> `5V`, `GND` -> `GND`, `OUT` -> `A0`

**Kode Program 4:**
```cpp
// Materi 4: Membaca Sensor Garis Inframerah
const int irKiri = A1;
const int irKanan = A0;

void setup() {
  Serial.begin(9600);
  pinMode(irKiri, INPUT);
  pinMode(irKanan, INPUT);
}

void loop() {
  int nilaiKiri = digitalRead(irKiri);
  int nilaiKanan = digitalRead(irKanan);

  Serial.print("Kiri: ");
  Serial.print(nilaiKiri);
  Serial.print(" | Kanan: ");
  Serial.println(nilaiKanan);
  // Nilai 1 (HIGH) = Garis Hitam, Nilai 0 (LOW) = Putih
  delay(500);
}
```

---

## Materi 5: Kolaborasi Sensor🤝
Sekarang kita akan coba gabungkan kemampuan beberapa komponen yang sudah kita pelajari.

### 5.1 Kolaborasi LED & Ultrasonik (Alarm Parkir)
Kita akan membuat simulasi alarm parkir. LED akan memberikan indikasi visual berdasarkan jarak benda di depan sensor.

**Analogi:** Persis seperti sensor parkir di mobil. Semakin dekat halangan, lampu peringatan akan menyala.
*   **Jauh (> 20cm):** Lampu Hijau menyala (Aman).
*   **Sedang (10-20cm):** Lampu Kuning menyala (Hati-hati).
*   **Dekat (< 10cm):** Lampu Merah menyala (Bahaya!).

**Kode Program 5:**
```cpp
// Materi 5.1: Kolaborasi LED & Sensor Jarak
// Pin LED
const int ledMerah = 2;
const int ledKuning = 3;
const int ledHijau = 4;

// Pin Sensor Ultrasonik
const int trigPin = A3;
const int echoPin = A2;

long duration;
int distance;

void setup() {
  pinMode(ledMerah, OUTPUT);
  pinMode(ledKuning, OUTPUT);
  pinMode(ledHijau, OUTPUT);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Serial.begin(9600);
}

void loop() {
  // Baca jarak
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;

  Serial.print("Jarak: ");
  Serial.println(distance);

  // Logika LED berdasarkan jarak
  if (distance < 10) {
    digitalWrite(ledMerah, HIGH);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledHijau, LOW);
  } 
  else if (distance >= 10 && distance <= 20) {
    digitalWrite(ledMerah, LOW);
    digitalWrite(ledKuning, HIGH);
    digitalWrite(ledHijau, LOW);
  } 
  else {
    digitalWrite(ledMerah, LOW);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledHijau, HIGH);
  }
  delay(200);

}
```

### 5.2 Kolaborasi Ketiganya (Sistem Peringatan Cerdas)
Kita gabungkan semua komponen hari ini. LED akan menjadi indikator status dari sensor jarak dan sensor garis.

**Logika:**
1.  Jika ada halangan dekat (< 15cm), sensor garis diabaikan, **Lampu Merah** menyala.
2.  Jika tidak ada halangan, tapi salah satu sensor IR mendeteksi garis hitam, **Lampu Kuning** menyala.
3.  Jika aman (tidak ada halangan dan tidak ada garis), **Lampu Hijau** menyala.

**Kode Program 6:**
```cpp
// Materi 5.2: Kolaborasi LED, Sensor Jarak, dan Sensor Garis
// Pin LED
const int ledMerah = 2;
const int ledKuning = 3;
const int ledHijau = 4;

// Pin Sensor Ultrasonik
const int trigPin = A3;
const int echoPin = A2;

// Pin Sensor IR
const int irKiri = A1;
const int irKanan = A0;

void setup() {
  pinMode(ledMerah, OUTPUT);
  pinMode(ledKuning, OUTPUT);
  pinMode(ledHijau, OUTPUT);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(irKiri, INPUT);
  pinMode(irKanan, INPUT);
}

void loop() {
  // 1. Baca semua sensor
  // Pertama, kirim gelombang ultrasonik
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  
  // Kedua, baca nilai dari semua sensor
  long duration = pulseIn(echoPin, HIGH);
  int distance = duration * 0.034 / 2;
  int nilaiKiri = digitalRead(irKiri);
  int nilaiKanan = digitalRead(irKanan);

  // 2. Logika Prioritas
  if (distance < 15 && distance > 0) { // Prioritas 1: Halangan
    digitalWrite(ledMerah, HIGH);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledHijau, LOW);
  } 
  else if (nilaiKiri == 1 || nilaiKanan == 1) { // Prioritas 2: Garis (Asumsi 1 = Hitam)
    digitalWrite(ledMerah, LOW);
    digitalWrite(ledKuning, HIGH);
    digitalWrite(ledHijau, LOW);
  } 
  else { // Kondisi Aman
    digitalWrite(ledMerah, LOW);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledHijau, HIGH);
  }
  delay(100);
}
```

---

## Penutup Hari Pertama & Langkah Selanjutnya
Selamat! 🎉 Kalian telah berhasil menyelesaikan semua materi dasar untuk hari pertama. Kalian sudah bisa:

*   Mengontrol lampu LED.
*   Mengukur jarak dengan sensor ultrasonik.
*   Mendeteksi garis dengan sensor inframerah.
*   Menggabungkan beberapa sensor untuk membuat logika yang lebih cerdas.

Kalian sudah berhasil membuat "indra" dan "sistem peringatan" untuk robot.

Besok, kita akan masuk ke **Modul Hari ke-2**, yaitu memberikan "kaki" pada robot kita dengan belajar mengendalikan Motor Driver dan menggabungkan semua yang kita pelajari hari ini untuk membuat robot yang bisa bergerak!

See yaa! Selamat beristirahat dan sampai jumpa besok:)