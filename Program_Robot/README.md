# Modul Pembelajaran Pemrograman Robot

Selamat datang di modul pembelajaran pemrograman robot! Modul ini akan memandu Anda dalam memahami dasar-dasar pemrograman robot menggunakan platform Arduino.

## Konfigurasi Pin

Berikut adalah konfigurasi pin yang akan kita gunakan untuk mengontrol komponen-komponen robot.

### Motor Driver
| Fungsi            | Pin Arduino | Keterangan                                  |
| :---------------: | :---------: | :-----------------------------------------: |
| ENA (Speed Kanan) |     D10     | Pin PWM untuk kontrol kecepatan motor Kanan |
| IN1 (Arah Kanan)  |     D9      |      Pin digital untuk arah motor Kanan     |
| IN2 (Arah Kanan)  |     D8      |      Pin digital untuk arah motor Kanan     |
| IN3 (Arah Kiri)   |     D7      |       Pin digital untuk arah motor Kiri     |
| IN4 (Arah Kiri)   |     D6      |       Pin digital untuk arah motor Kiri     |
| ENB (Speed Kiri)  |     D5      |  Pin PWM untuk kontrol kecepatan motor Kiri  |

### Sensor Inframerah
| Posisi | Pin Arduino | Keterangan                               |
| :----: | :---------: | :---------------------------------------: |
| Kanan  |     D11     | Pin digital untuk input dari sensor kanan|
| Kiri   |     D12     | Pin digital untuk input dari sensor kiri |

### Sensor Ultrasonik
| Fungsi | Pin Arduino |             Keterangan              |
| :----: | :---------: | :---------------------------------: |
| Echo   |     A2      | Pin analog untuk menerima sinyal pantul |
| Trig   |     A3      |  Pin digital untuk mengirim sinyal suara  |

### LED Indikator
| Warna  | Pin Arduino | Keterangan                          |
| :----: | :---------: | :----------------------------------: |
| Merah  |     D2      | Pin digital untuk output ke LED Merah |
| Kuning |     D3      | Pin digital untuk output ke LED Kuning|
| Hijau  |     D4      | Pin digital untuk output ke LED Hijau |

---

## Penjelasan Pemilihan Pin: Digital vs Analog

Dalam mikrokontroler seperti Arduino, kita memiliki dua jenis pin utama: **Digital** dan **Analog**. Memahami perbedaan keduanya adalah kunci untuk merakit dan memprogram robot dengan benar.

### Pin Digital
Pin digital adalah pin yang hanya mengenal dua kondisi: **HIGH** (Tinggi/Menyala/5V) atau **LOW** (Rendah/Mati/0V). Pin ini cocok untuk:
- **Output Sederhana:** Menyalakan atau mematikan komponen seperti LED atau relay.
- **Input Sederhana:** Membaca sinyal dari tombol (ditekan atau tidak) atau sensor garis inframerah (mendeteksi garis hitam atau putih).

Pada konfigurasi kita:
- **LED (D2, D3, D4):** Kita hanya perlu menyalakan atau mematikan LED, jadi pin digital sudah cukup.
- **Input Driver Motor (IN1-IN4):** Pin ini menentukan arah putaran motor (misalnya, IN1 HIGH dan IN2 LOW untuk maju, sebaliknya untuk mundur). Karena hanya ada dua kondisi arah, pin digital sangat tepat.
- **Sensor Inframerah (D11, D12):** Sensor ini memberikan output HIGH atau LOW tergantung pada apakah ia mendeteksi permukaan reflektif (putih) atau tidak (hitam).
- **Trig Sensor Ultrasonik (A3):** Pin `Trig` hanya perlu diberi sinyal HIGH sesaat untuk memicu pengiriman gelombang suara. Meskipun kita menggunakan pin A3 (pin analog), pin analog pada Arduino juga bisa berfungsi sebagai pin digital.

#### Pulse Width Modulation (PWM)
Beberapa pin digital (ditandai dengan `~` di papan Arduino, seperti D3, D5, D6, D9, D10, D11) memiliki kemampuan khusus yang disebut **PWM (Pulse Width Modulation)**. PWM memungkinkan kita untuk mensimulasikan output analog. Alih-alih hanya HIGH atau LOW, kita bisa mengatur "kecerahan" LED atau "kecepatan" motor dengan mengirimkan pulsa sinyal HIGH dan LOW dengan sangat cepat.

Pada konfigurasi kita:
- **ENA (D10) dan ENB (D5):** Pin ini terhubung ke pin *Enable* pada driver motor. Dengan memberikan sinyal PWM ke pin ini, kita dapat mengatur kecepatan putaran motor. Nilai PWM berkisar dari 0 (motor berhenti) hingga 255 (kecepatan penuh).

### Pin Analog
Pin analog (biasanya diawali dengan huruf 'A', seperti A0-A5) dapat membaca rentang tegangan, bukan hanya HIGH atau LOW. Mereka mengubah tegangan input (biasanya antara 0V hingga 5V) menjadi nilai numerik (antara 0 hingga 1023). Pin ini sangat penting untuk membaca data dari sensor yang memberikan output bervariasi.

Pada konfigurasi kita:
- **Echo Sensor Ultrasonik (A2):** Pin `Echo` akan menghasilkan sinyal HIGH yang durasinya sebanding dengan jarak objek. Kita menggunakan fungsi `pulseIn()` yang akan mengukur durasi pulsa ini. Meskipun `pulseIn()` bisa bekerja di pin digital, penggunaan pin analog untuk input sensor seperti ini adalah praktik yang umum dan aman untuk memastikan pembacaan yang stabil. Pin analog didesain khusus untuk menerima sinyal dengan variasi tegangan atau durasi.

Dengan memahami fungsi masing-masing pin, kita dapat merancang robot yang efisien dan memanfaatkan kemampuan mikrokontroler secara maksimal.

---

## Materi 1: Menguji Arah Motor

Sebelum melangkah lebih jauh, sangat penting untuk memastikan bahwa kedua motor berputar ke arah yang benar (maju). Jika tidak, robot tidak akan bisa bergerak lurus. Kita akan menggunakan program sederhana di bawah ini untuk menguji setiap motor secara terpisah.

Buat file baru di Arduino IDE, salin kode di bawah, unggah ke Arduino Anda, dan buka **Serial Monitor** (Tools -> Serial Monitor) dengan baud rate 9600.

```cpp
// ==== PROGRAM UJI ARAH MOTOR ====

// --- Pin Motor Kanan ---
#define ENA 10
#define IN1 9
#define IN2 8

// --- Pin Motor Kiri ---
#define ENB 5
#define IN3 7
#define IN4 6

// Kecepatan sedang untuk pengujian
int kecepatanTes = 150; 

void setup() {
  // Buka komunikasi Serial untuk memberi tahu kita sedang menguji apa
  Serial.begin(9600);
  
  // Atur semua pin motor sebagai OUTPUT
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);

  Serial.println("--- Memulai Program Uji Arah Motor ---");
  delay(2000); // Tunggu 2 detik

  // --- UJI MOTOR KANAN ---
  Serial.println("Menguji Motor KANAN... Seharusnya berputar MAJU.");
  // Perintah MAJU untuk motor kanan
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  analogWrite(ENA, kecepatanTes);
  delay(2000); // Biarkan berputar selama 2 detik
  
  // Berhenti
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  analogWrite(ENA, 0);
  delay(2000); // Jeda 2 detik sebelum tes berikutnya

  // --- UJI MOTOR KIRI ---
  Serial.println("Menguji Motor KIRI... Seharusnya berputar MAJU.");
  // Perintah MAJU untuk motor kiri
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENB, kecepatanTes);
  delay(2000); // Biarkan berputar selama 2 detik

  // Berhenti
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
  analogWrite(ENB, 0);

  Serial.println("--- Tes Selesai ---");
}

void loop() {
  // Biarkan kosong, karena tes hanya perlu berjalan satu kali di setup()
}
```

### Apa yang Harus Diperhatikan:
1.  Lihat Serial Monitor. Program akan memberitahu motor mana yang sedang diuji.
2.  Perhatikan roda kanan. Apakah ia berputar ke arah **maju**?
3.  Perhatikan roda kiri. Apakah ia juga berputar ke arah **maju**?

**Jika ada motor yang berputar mundur:**
-   Misalnya, jika motor kiri berputar mundur saat diuji, Anda hanya perlu menukar logika pin `IN3` dan `IN4` di dalam kode. Cari bagian "Perintah MAJU untuk motor kiri" dan ubah dari `digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);` menjadi `digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);`.
-   Lakukan hal yang sama untuk motor kanan jika diperlukan (tukar logika `IN1` dan `IN2`).

Setelah kedua motor dipastikan berputar maju, kita bisa melanjutkan ke langkah berikutnya.

Selanjutnya, kita akan mulai menulis kode berdasarkan konfigurasi pin ini.

### Cara Kerja Driver Motor: Logika HIGH dan LOW
Anda mungkin bertanya, mengapa untuk bergerak "maju" kita perlu satu pin `HIGH` dan satu pin `LOW`? Jawabannya terletak pada cara kerja komponen di dalam driver motor yang disebut **H-Bridge**.

Bayangkan H-Bridge seperti empat saklar elektronik yang mengendalikan arah aliran listrik ke motor.

-   **Maju (Contoh: `IN1` HIGH, `IN2` LOW):** Ini seperti menutup saklar A dan D. Arus listrik mengalir dari satu sisi ke sisi lain, membuat motor berputar ke satu arah (misalnya, searah jarum jam).
-   **Mundur (Contoh: `IN1` LOW, `IN2` HIGH):** Ini akan menutup saklar B dan C. Arus listrik sekarang mengalir ke arah yang berlawanan, sehingga motor berputar ke arah yang berlawanan (berlawanan arah jarum jam).
-   **Berhenti/Rem (Contoh: `IN1` LOW, `IN2` LOW):** Ini menutup saklar di sisi bawah (C dan D) atau atas (A dan B, jika `HIGH`/`HIGH`). Ini menyebabkan pengereman dinamis (dynamic braking), di mana motor berhenti dengan cepat, bukan hanya meluncur bebas.
-   **Meluncur Bebas (Coasting):** Jika pin `ENA`/`ENB` diatur ke `LOW` (atau `analogWrite` ke 0), semua saklar terbuka dan motor tidak mendapat daya sama sekali, sehingga bisa berputar bebas.

Jadi, kombinasi `HIGH` dan `LOW` pada pin `IN` adalah instruksi untuk H-Bridge tentang bagaimana cara mengalirkan listrik, yang pada akhirnya menentukan arah putaran motor.

---

## Materi 2: Membuat Fungsi Gerak Dasar (Maju & Mundur)

Setelah memastikan arah motor sudah benar, saatnya kita membuat program yang lebih terstruktur. Daripada menulis perintah `digitalWrite` dan `analogWrite` berulang-ulang, kita akan membungkusnya ke dalam **fungsi**.

Keuntungan menggunakan fungsi:
- **Lebih Rapi:** Kode utama (`void loop`) menjadi lebih bersih dan mudah dibaca.
- **Mudah Digunakan:** Untuk membuat robot maju, kita cukup panggil `maju();`.
- **Mudah Diperbaiki:** Jika ada kesalahan pada gerakan maju, kita hanya perlu memperbaikinya di satu tempat, yaitu di dalam `void maju()`.

Berikut adalah program untuk membuat robot bergerak maju, berhenti, mundur, lalu berhenti lagi, secara berulang. Program ini juga akan menyalakan LED sebagai indikator status robot.

```cpp
// ==== PENGATURAN PIN (SESUAI RAKITAN ANDA) ====
// --- Motor Kanan ---
#define ENA 10
#define IN1 9
#define IN2 8

// --- Motor Kiri ---
#define ENB 5
#define IN3 7
#define IN4 6

// --- LED Indikator ---
#define LED_MERAH 2
#define LED_KUNING 3
#define LED_HIJAU 4

// Variabel untuk kecepatan motor (0-255)
int kecepatan = 150; // Silakan diubah sesuai kebutuhan

void setup() {
  // Atur semua pin motor sebagai OUTPUT
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  
  // Atur semua pin LED sebagai OUTPUT
  pinMode(LED_MERAH, OUTPUT);
  pinMode(LED_KUNING, OUTPUT);
  pinMode(LED_HIJAU, OUTPUT);
  
  // Memulai dengan kondisi berhenti
  berhenti(); 
}

void loop() {
  // 1. Bergerak Maju selama 3 detik
  maju();
  delay(3000);
  
  // Berhenti sejenak
  berhenti();
  delay(1000);

  // 2. Bergerak Mundur selama 3 detik
  mundur();
  delay(3000);

  // Berhenti sejenak sebelum mengulang
  berhenti();
  delay(1000);
}

// ==== FUNGSI-FUNGSI KONTROL GERAK ====

void maju() {
  // Indikator: LED Hijau ON
  digitalWrite(LED_HIJAU, HIGH);
  digitalWrite(LED_KUNING, LOW);
  digitalWrite(LED_MERAH, LOW);
  
  // Atur arah motor maju
  // Pastikan logika HIGH/LOW ini sesuai dengan hasil tes di Langkah 1
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  
  // Atur kecepatan
  analogWrite(ENA, kecepatan);
  analogWrite(ENB, kecepatan);
}

void mundur() {
  // Indikator: LED Kuning ON
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, HIGH);
  digitalWrite(LED_MERAH, LOW);

  // Atur arah motor mundur (kebalikan dari maju)
  digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);
  
  // Atur kecepatan
  analogWrite(ENA, kecepatan);
  analogWrite(ENB, kecepatan);
}

void berhenti() {
  // Indikator: LED Merah ON
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, LOW);
  digitalWrite(LED_MERAH, HIGH);

  // Matikan semua motor (gunakan mode rem/brake)
  digitalWrite(IN1, LOW); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, LOW);
  analogWrite(ENA, 0);
  analogWrite(ENB, 0);
}
```

Unggah kode di atas ke Arduino Anda. Robot seharusnya akan bergerak maju selama 3 detik, berhenti 1 detik, mundur 3 detik, berhenti 1 detik, dan terus mengulang pola tersebut. Perhatikan juga lampu LED yang menyala sesuai statusnya.

---

## Materi 3: Gerakan Berputar-Putar

Selanjutnya kita akan menambahkan kemampuan untuk membuat robot berputar-putar. Gerakan ini penting agar robot dapat mengubah arahnya tanpa harus bergerak maju atau mundur terlebih dahulu.

Prinsip dasar untuk membuat robot berputar adalah dengan menggerakkan motor di kedua sisi ke arah yang berlawanan:
- **Berputar ke Kanan**: Motor kanan berputar mundur, motor kiri berputar maju.
- **Berputar ke Kiri**: Motor kanan berputar maju, motor kiri berputar mundur.

Dengan cara ini, robot akan berputar di tempat tanpa berpindah posisi terlalu jauh.

Berikut adalah program yang menambahkan dua fungsi baru, yaitu `putarKanan()` dan `putarKiri()`:

```cpp
// ==== PENGATURAN PIN (SESUAI RAKITAN ANDA) ====
// --- Motor Kanan ---
#define ENA 10
#define IN1 9
#define IN2 8

// --- Motor Kiri ---
#define ENB 5
#define IN3 7
#define IN4 6

// --- LED Indikator ---
#define LED_MERAH 2
#define LED_KUNING 3
#define LED_HIJAU 4

// Variabel untuk kecepatan motor (0-255)
int kecepatan = 100; // Silakan diubah sesuai kebutuhan

void setup() {
  // Atur semua pin motor sebagai OUTPUT
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  
  // Atur semua pin LED sebagai OUTPUT
  pinMode(LED_MERAH, OUTPUT);
  pinMode(LED_KUNING, OUTPUT);
  pinMode(LED_HIJAU, OUTPUT);
  
  // Memulai dengan kondisi berhenti
  berhenti(); 
}

void loop() {
  // 1. Maju selama 3 detik
  maju();
  delay(3000);
  berhenti();
  delay(1000);

  // 2. Mundur selama 3 detik
  mundur();
  delay(3000);
  berhenti();
  delay(1000);

  // 3. Putar Kanan selama 3 detik
  putarKanan();
  delay(3000);
  berhenti();
  delay(1000);

  // 4. Putar Kiri selama 3 detik
  putarKiri();
  delay(3000);
  berhenti();
  delay(1000);
}

// ==== FUNGSI-FUNGSI KONTROL GERAK ====

void maju() {
  digitalWrite(LED_HIJAU, HIGH);
  digitalWrite(LED_KUNING, LOW);
  digitalWrite(LED_MERAH, LOW);
  
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatan);
  analogWrite(ENB, kecepatan);
}

void mundur() {
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, HIGH);
  digitalWrite(LED_MERAH, LOW);

  digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);
  analogWrite(ENA, kecepatan);
  analogWrite(ENB, kecepatan);
}

void putarKanan() {
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, HIGH);
  digitalWrite(LED_MERAH, LOW);

  // Motor Kanan Mundur, Motor Kiri Maju
  digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatan);
  analogWrite(ENB, kecepatan);
}

void putarKiri() {
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, HIGH);
  digitalWrite(LED_MERAH, LOW);

  // Motor Kanan Maju, Motor Kiri Mundur
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);
  analogWrite(ENA, kecepatan);
  analogWrite(ENB, kecepatan);
}

void berhenti() {
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, LOW);
  digitalWrite(LED_MERAH, HIGH);

  digitalWrite(IN1, LOW); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, LOW);
  analogWrite(ENA, 0);
  analogWrite(ENB, 0);
}
```

Perhatikan bahwa kita menggunakan kecepatan 100 (lebih rendah dari sebelumnya), karena gerakan berputar tidak perlu secepat maju/mundur. Kecepatan yang lebih rendah memberikan kontrol yang lebih baik saat berputar.

Unggah kode di atas ke Arduino Anda. Robot seharusnya akan mengeksekusi serangkaian gerakan: maju, berhenti, mundur, berhenti, putar kanan, berhenti, putar kiri, berhenti, lalu mengulang pola tersebut.

### Tips:
- Jika robot berputar terlalu cepat, turunkan nilai variabel `kecepatan`.
- Anda juga bisa membuat fungsi khusus dengan kecepatan berbeda untuk berputar, misalnya dengan membuat fungsi `putarKananPelan()` yang menggunakan kecepatan lebih rendah khusus untuk berputar.

---

## Materi 4: Robot Penghindar Halangan (Obstacle Avoider)

Sekarang kita akan masuk ke materi selanjutnya. Kita akan menggabungkan yang telah kita pelajari (kontrol motor) dengan input dari sensor ultrasonik. Robot ini akan dapat bergerak maju, dan ketika mendeteksi halangan di depannya, ia akan berhenti, mencari jalan lain, lalu melanjutkan perjalanannya.

### Konsep Logika
1.  **Ukur Jarak**: Terus-menerus ukur jarak objek di depan robot menggunakan sensor ultrasonik.
2.  **Bandingkan Jarak**: Bandingkan jarak yang terukur dengan `jarakAman` yang sudah kita tentukan.
3.  **Ambil Keputusan**:
    *   **Jika Jarak > `jarakAman`**: Jalanan aman. Robot terus bergerak maju.
    *   **Jika Jarak <= `jarakAman`**: Ada halangan! Robot harus melakukan manuver menghindar.
4.  **Manuver Menghindar**:
    *   Berhenti sejenak.
    *   Berputar ke satu arah (misalnya, ke kanan).
    *   Saat berputar, terus ukur jarak.
    *   Ketika jarak sudah kembali aman (Jarak > `jarakAman`), berarti robot telah menemukan jalan yang kosong.
    *   Berhenti berputar.
    *   Lanjutkan bergerak maju.

Berikut adalah kode lengkap untuk robot penghindar halangan:

```cpp
// ==== PENGATURAN PIN ====
// --- Motor Kanan ---
#define ENA 10
#define IN1 9
#define IN2 8

// --- Motor Kiri ---
#define ENB 5
#define IN3 7
#define IN4 6

// --- Sensor Ultrasonik ---
#define TRIG_PIN A3
#define ECHO_PIN A2

// --- LED Indikator ---
#define LED_MERAH 2
#define LED_KUNING 3
#define LED_HIJAU 4

// ==== PENGATURAN LOGIKA ROBOT ====
int kecepatanMaju = 80;    // Kecepatan normal robot (lebih pelan agar lebih akurat)
int kecepatanPutar = 90;   // Kecepatan saat berputar mencari jalan
int jarakAman = 30;        // Jarak minimum (dalam cm) sebelum robot bereaksi

void setup() {
  Serial.begin(9600); // Untuk menampilkan jarak di Serial Monitor

  // Atur pin motor sebagai OUTPUT
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  
  // Atur pin LED sebagai OUTPUT
  pinMode(LED_MERAH, OUTPUT);
  pinMode(LED_KUNING, OUTPUT);
  pinMode(LED_HIJAU, OUTPUT);

  // Atur pin sensor Ultrasonik
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  Serial.println("Robot Penghindar Halangan Siap!");
  delay(2000); // Tunggu 2 detik sebelum mulai
}

void loop() {
  // 1. Ukur jarak objek di depan
  long jarak = ukurJarakCm();

  // 2. Tampilkan jarak di Serial Monitor untuk debugging
  // Ini sangat membantu untuk memastikan sensor bekerja dengan benar
  Serial.print("Jarak di depan: ");
  Serial.print(jarak);
  Serial.println(" cm");

  // 3. Logika utama: Cek apakah ada halangan
  if (jarak <= jarakAman && jarak > 0) { // Cek jika jarak kurang dari batas aman dan bukan pembacaan error (0)
    
    // --- MANUVER MENGHINDAR ---
    Serial.println("HALANGAN TERDETEKSI! Mencari jalan...");
    
    // a. Berhenti dulu
    berhenti();
    delay(500); // Jeda sejenak

    // b. Mulai putar kanan dan terus cek sampai jalan kosong
    // Kita gunakan loop 'while' di sini. Robot akan 'terjebak' dalam loop ini
    // selama jalan di depannya masih terhalang.
    while (ukurJarakCm() <= jarakAman) {
      putarKanan();
      // Tidak perlu delay, agar terus berputar sambil mengecek secara responsif
    }

    // c. Setelah menemukan jalan kosong, berhenti sejenak sebelum maju
    berhenti();
    delay(500);

  } else {
    // Jika tidak ada halangan, terus maju
    maju();
  }
}

// ==== FUNGSI PENGUKUR JARAK ====
// Fungsi ini bertugas untuk mengukur jarak menggunakan sensor ultrasonik
// dan mengembalikannya dalam satuan sentimeter.
long ukurJarakCm() {
  // 1. Kirim pulse trigger untuk memulai pengukuran
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  // 2. Baca durasi pulse echo yang kembali
  long durasi = pulseIn(ECHO_PIN, HIGH);

  // 3. Hitung jarak berdasarkan durasi
  // Rumus: Jarak = (Durasi * Kecepatan Suara) / 2
  // Kecepatan suara = 343 m/s = 0.0343 cm/microsecond
  // Kita bagi 2 karena durasi yang diukur adalah waktu bolak-balik (sensor -> objek -> sensor)
  return durasi * 0.0343 / 2;
}


// ==== FUNGSI-FUNGSI KONTROL GERAK (Sedikit dimodifikasi) ====
void maju() {
  // LED Hijau: Sedang maju
  digitalWrite(LED_HIJAU, HIGH);
  digitalWrite(LED_KUNING, LOW);
  digitalWrite(LED_MERAH, LOW);
  
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatanMaju);
  analogWrite(ENB, kecepatanMaju);
}

void mundur() {
  // LED Kuning: Sedang mundur
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, HIGH);
  digitalWrite(LED_MERAH, LOW);

  digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);
  analogWrite(ENA, kecepatanMaju);
  analogWrite(ENB, kecepatanMaju);
}

void putarKanan() {
  // LED Kuning: Sedang berputar/mencari jalan
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, HIGH);
  digitalWrite(LED_MERAH, LOW);

  // Motor Kanan Mundur, Motor Kiri Maju
  digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatanPutar);
  analogWrite(ENB, kecepatanPutar);
}

void putarKiri() {
  // LED Kuning: Sedang berputar/mencari jalan
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, HIGH);
  digitalWrite(LED_MERAH, LOW);

  // Motor Kanan Maju, Motor Kiri Mundur
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);
  analogWrite(ENA, kecepatanPutar);
  analogWrite(ENB, kecepatanPutar);
}

void berhenti() {
  // LED Merah: Berhenti / Ada Halangan
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, LOW);
  digitalWrite(LED_MERAH, HIGH);

  digitalWrite(IN1, LOW); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, LOW);
  analogWrite(ENA, 0);
  analogWrite(ENB, 0);
}
```

Unggah kode ini dan letakkan robot Anda di lantai. Coba letakkan tangan atau buku di depannya. Robot seharusnya berhenti, berputar untuk mencari jalan lain, dan kemudian melanjutkan perjalanannya. Selamat, Anda telah membuat robot otonom pertama Anda!

---

## Materi 5: Robot Pengikut Garis (Line Follower)

Setelah berhasil membuat robot menghindari halangan, sekarang kita akan mencoba tantangan yang berbeda: membuat robot untuk mengikuti sebuah garis hitam di permukaan putih.

### Komponen Kunci: Sensor Inframerah (IR)
Sensor inframerah adalah komponen utama untuk proyek ini. Cara kerjanya sederhana:
1.  **Pemancar (IR LED):** Mengirimkan cahaya inframerah (tidak terlihat oleh mata manusia) ke bawah.
2.  **Penerima (Photodiode):** Mendeteksi pantulan cahaya inframerah tersebut.

Logikanya adalah:
-   **Permukaan Putih:** Memantulkan banyak cahaya. Penerima akan mendeteksi pantulan ini dan memberikan sinyal **LOW** (Rendah/0).
-   **Permukaan Hitam:** Menyerap sebagian besar cahaya. Sangat sedikit cahaya yang dipantulkan kembali. Penerima tidak mendeteksi pantulan dan memberikan sinyal **HIGH** (Tinggi/1).

Dengan menempatkan dua sensor, satu di sisi kiri dan satu di sisi kanan, kita bisa menentukan posisi robot relatif terhadap garis.

### Logika Dasar Line Follower
Kita akan memprogram robot berdasarkan 4 kemungkinan kondisi dari kedua sensor:

1.  **Kiri LOW, Kanan LOW:** Kedua sensor berada di atas permukaan putih. Artinya, garis hitam ada di antara kedua sensor. Robot harus bergerak **lurus maju**.
2.  **Kiri HIGH, Kanan LOW:** Sensor kiri mendeteksi garis hitam, sensor kanan di atas putih. Artinya, robot terlalu ke kanan dan harus **berbelok ke kiri** untuk kembali ke jalur.
3.  **Kiri LOW, Kanan HIGH:** Sensor kanan mendeteksi garis hitam, sensor kiri di atas putih. Artinya, robot terlalu ke kiri dan harus **berbelok ke kanan** untuk kembali ke jalur.
4.  **Kiri HIGH, Kanan HIGH:** Kedua sensor mendeteksi garis hitam. Ini bisa berarti garis persimpangan atau akhir dari lintasan. Untuk amannya, kita program robot untuk **berhenti**.

Berikut adalah kode lengkap untuk robot pengikut garis:

```cpp
// ==== PENGATURAN PIN LENGKAP ====
// --- Motor Kanan ---
#define ENA 10
#define IN1 9
#define IN2 8

// --- Motor Kiri ---
#define ENB 5
#define IN3 7
#define IN4 6

// --- Sensor Infrared ---
#define IR_KANAN 11
#define IR_KIRI  12

// --- LED Indikator ---
#define LED_MERAH 2
#define LED_KUNING 3
#define LED_HIJAU 4

// Variabel untuk kecepatan
int kecepatanMaju = 100;
int kecepatanBelok = 80;

void setup() {
  Serial.begin(9600); // Untuk debugging nilai sensor

  // Atur pin motor sebagai OUTPUT
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  
  // Atur pin LED sebagai OUTPUT
  pinMode(LED_MERAH, OUTPUT);
  pinMode(LED_KUNING, OUTPUT);
  pinMode(LED_HIJAU, OUTPUT);

  // Atur pin sensor IR sebagai INPUT
  pinMode(IR_KANAN, INPUT);
  pinMode(IR_KIRI, INPUT);
  
  Serial.println("Robot Line Follower Siap!");
  Serial.println("Letakkan di atas garis dan nyalakan...");
  berhenti();
  delay(2000); // Beri waktu 2 detik sebelum mulai
}

void loop() {
  // 1. Baca nilai dari kedua sensor
  int nilaiKiri = digitalRead(IR_KIRI);   // Baca sensor kiri (D12)
  int nilaiKanan = digitalRead(IR_KANAN); // Baca sensor kanan (D11)

  // 2. Cetak nilai sensor ke Serial Monitor untuk debugging
  // Ini sangat membantu untuk memastikan sensor bekerja dengan benar
  Serial.print("Kiri: ");
  Serial.print(nilaiKiri); // 0 = Putih, 1 = Hitam
  Serial.print(" | Kanan: ");
  Serial.println(nilaiKanan); // 0 = Putih, 1 = Hitam

  // 3. Terapkan logika Line Follower
  // Kondisi 1: Keduanya di atas putih (lurus)
  if (nilaiKiri == LOW && nilaiKanan == LOW) {
    maju();
  } 
  // Kondisi 2: Sensor kiri di atas hitam (belok kiri)
  else if (nilaiKiri == HIGH && nilaiKanan == LOW) {
    putarKiri();
  }
  // Kondisi 3: Sensor kanan di atas hitam (belok kanan)
  else if (nilaiKiri == LOW && nilaiKanan == HIGH) {
    putarKanan();
  }
  // Kondisi 4: Keduanya di atas hitam (berhenti)
  else { // (nilaiKiri == HIGH && nilaiKanan == HIGH)
    berhenti();
  }
}

// ==== FUNGSI-FUNGSI KONTROL GERAK ====

void maju() {
  digitalWrite(LED_HIJAU, HIGH);
  digitalWrite(LED_KUNING, LOW);
  digitalWrite(LED_MERAH, LOW);
  
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatanMaju);
  analogWrite(ENB, kecepatanMaju);
}

void putarKanan() {
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, HIGH);
  digitalWrite(LED_MERAH, LOW);

  // Motor Kanan Mundur, Motor Kiri Maju untuk berputar di tempat
  digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatanBelok);
  analogWrite(ENB, kecepatanBelok);
}

void putarKiri() {
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, HIGH);
  digitalWrite(LED_MERAH, LOW);

  // Motor Kanan Maju, Motor Kiri Mundur untuk berputar di tempat
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);
  analogWrite(ENA, kecepatanBelok);
  analogWrite(ENB, kecepatanBelok);
}

void berhenti() {
  digitalWrite(LED_HIJAU, LOW);
  digitalWrite(LED_KUNING, LOW);
  digitalWrite(LED_MERAH, HIGH);

  digitalWrite(IN1, LOW); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, LOW);
  analogWrite(ENA, 0);
  analogWrite(ENB, 0);
}
```

### Tips dan Kalibrasi
- **Buka Serial Monitor:** Saat pertama kali mencoba, buka Serial Monitor di Arduino IDE. Amati nilai yang dikeluarkan sensor saat Anda meletakkannya di atas permukaan putih dan hitam. Pastikan logikanya sesuai (Putih = 0, Hitam = 1). Jika terbalik, Anda hanya perlu membalik logika `HIGH` dan `LOW` di dalam `if` statement.
- **Atur Kecepatan:** `kecepatanMaju` dan `kecepatanBelok` mungkin perlu disesuaikan. Jika robot sering keluar jalur, coba kurangi kecepatannya. Jika gerakannya patah-patah, coba naikkan sedikit `kecepatanBelok`.
- **Posisi Sensor:** Jarak antara kedua sensor sangat mempengaruhi performa. Jika terlalu rapat, robot akan bergetar hebat. Jika terlalu lebar, robot mungkin akan kehilangan garis di tikungan tajam.

---

## Materi 6: Menggabungkan Line Follower dan Obstacle Avoidance (Versi Sederhana)

Inilah tantangan sesungguhnya: membuat robot yang tidak hanya bisa mengikuti garis, tetapi juga cukup pintar untuk menghindari halangan yang tiba-tiba muncul di jalurnya, lalu kembali menemukan garis untuk melanjutkan tugasnya.

### Konsep Utama
Logika utamanya sederhana dan berjalan di `void loop()`:
1.  **Prioritas Utama: Cek Halangan.** Di setiap awal loop, robot akan selalu memeriksa jarak di depannya menggunakan sensor ultrasonik.
2.  **Jika Ada Halangan:** Robot akan mengabaikan sensor garis dan masuk ke mode `hindariHalangan()`.
3.  **Jika Tidak Ada Halangan:** Robot akan menjalankan mode `ikutiGaris()` seperti biasa.

### Manuver Menghindar yang Terprogram (Hard-coded)
Pada versi ini, manuver untuk menghindari halangan dibuat sangat sederhana dan kaku (hard-coded). Robot akan melakukan serangkaian gerakan yang sudah ditentukan durasinya untuk "mengitari" halangan:
1.  Berhenti.
2.  Putar kanan 90 derajat.
3.  Maju lurus ke samping.
4.  Putar kiri 90 derajat (menghadap ke depan lagi).
5.  Maju lurus untuk melewati halangan.
6.  Putar kiri 90 derajat (menghadap kembali ke arah garis).
7.  Setelah manuver selesai, robot masuk ke mode `modeCariGaris()`.

### Mencari Garis Kembali
Setelah berhasil melewati halangan, robot tidak tahu di mana garis berada. Fungsi `modeCariGaris()` akan:
1.  Bergerak maju perlahan sambil terus memeriksa sensor IR.
2.  Jika garis ditemukan saat maju, robot akan menengahkan posisinya dan kembali ke mode normal.
3.  Jika setelah maju beberapa saat garis tidak ditemukan, robot akan berhenti, berputar sedikit, lalu mengulangi proses maju perlahan.
4.  Proses ini diulang beberapa kali hingga garis ditemukan atau robot menyerah.

Berikut adalah kode lengkap untuk menggabungkan kedua fungsi tersebut:

```cpp
#define ENA 10
#define IN1 9
#define IN2 8
#define ENB 5
#define IN3 7
#define IN4 6
#define TRIG_PIN A3
#define ECHO_PIN A2
#define PIN_IR_KANAN 11
#define PIN_IR_KIRI  12
#define PIN_LED_MERAH 2
#define PIN_LED_KUNING 3
#define PIN_LED_HIJAU 4

// ==== PENGATURAN LOGIKA & KECEPATAN ====
int kecepatanMajuFollower = 100;
int kecepatanBelokFollower = 80;
int kecepatanMajuObstacle = 80;
int kecepatanPutarObstacle = 80;
int jarakAman = 20;
int kecepatanCariGaris = 60; 

// --- Durasi Manuver Menghindar ---
const int WAKTU_PUTAR_90_DERAJAT = 700;
const int WAKTU_MAJU_SAMPING = 600;
const int WAKTU_MAJU_LEWATI_HALANGAN = 600;
const int WAKTU_PUTAR_CARI = 700;

// --- Durasi Pencarian Garis ---
const int LANGKAH_MAKSIMAL_CARI = 20;
const int WAKTU_PER_POTONGAN = 10;          // DISARANKAN: 10ms agar gerakan lebih stabil
const int JUMLAH_POTONGAN_MAJU = 200;        // Lakukan 200 potongan (10ms * 200 = 2000ms = 2.0 detik)

// Mendefinisikan state (status) yang bisa dimiliki robot
enum RobotState {
  MENGIKUTI_GARIS,
  MENGHINDARI_HALANGAN,
  MENCARI_GARIS,
  MENENGAHKAN_POSISI
};

// Variabel global untuk menyimpan state saat ini
RobotState currentState = MENGIKUTI_GARIS;

// Variabel untuk manajemen state dan waktu
unsigned long stateStartTime = 0; // Waktu kapan state saat ini dimulai
int manuverStep = 0;              // Langkah ke berapa dalam sebuah manuver
bool belokKiriTerakhir = true;    // Untuk bolak-balik arah saat mencari garis

void setup() {
  Serial.begin(9600);

  pinMode(ENA, OUTPUT); pinMode(IN1, OUTPUT); pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT); pinMode(IN3, OUTPUT); pinMode(IN4, OUTPUT);
  pinMode(PIN_LED_MERAH, OUTPUT); pinMode(PIN_LED_KUNING, OUTPUT); pinMode(PIN_LED_HIJAU, OUTPUT);
  pinMode(PIN_IR_KANAN, INPUT); pinMode(PIN_IR_KIRI, INPUT);
  pinMode(TRIG_PIN, OUTPUT); pinMode(ECHO_PIN, INPUT);
  
  Serial.println("Robot Cerdas (State Machine) Siap!");
  berhenti();
  delay(500);
}

void loop() {
  // Pengecekan halangan menjadi prioritas utama untuk mengubah state.
  // Hanya bisa diinterupsi jika sedang mengikuti garis.
  long jarak = ukurJarakCm();
  if (jarak <= JARAK_AMAN_CM && jarak > 0 && currentState == MENGIKUTI_GARIS) {
    // Transisi state ke MENGHINDARI_HALANGAN
    currentState = MENGHINDARI_HALANGAN;
    manuverStep = 0;           // Reset langkah manuver
    stateStartTime = millis(); // Catat waktu mulai manuver
    berhenti();
    Serial.println("State -> MENGHINDARI_HALANGAN");
  }

  // Jalankan fungsi yang sesuai dengan state saat ini
  switch (currentState) {
    case MENGIKUTI_GARIS:
      ikutiGaris();
      break;
    case MENGHINDARI_HALANGAN:
      hindariHalangan_nonBlocking();
      break;
    case MENCARI_GARIS:
      cariGaris_nonBlocking();
      break;
    case MENENGAHKAN_POSISI:
      tengahkanPosisi_nonBlocking();
      break;
  }
}

void ikutiGaris() {
  // Fungsi ini tetap sama, karena ia reaktif dan tidak butuh delay
  bool sensorKiri = (digitalRead(PIN_IR_KIRI) == GARIS_HITAM);
  bool sensorKanan = (digitalRead(PIN_IR_KANAN) == GARIS_HITAM);

  if (!sensorKiri && !sensorKanan) { // Keduanya di putih
    maju(KECEPATAN_MAJU_NORMAL);
  } 
  else if (sensorKiri && !sensorKanan) { // Kiri di hitam, belok kiri
    putarKiri(KECEPATAN_BELOK);
  } 
  else if (!sensorKiri && sensorKanan) { // Kanan di hitam, belok kanan
    putarKanan(KECEPATAN_BELOK);
  } 
  else { // Keduanya di hitam
    berhenti();
  }
}

void hindariHalangan_nonBlocking() {
  unsigned long currentTime = millis();

  // Manuver ini dibagi menjadi beberapa langkah (step)
  switch (manuverStep) {
    case 0: // Jeda awal
      if (currentTime - stateStartTime >= WAKTU_JEDA_ANTAR_MANUVER) { manuverStep++; stateStartTime = currentTime; }
      else { berhenti(); }
      break;
    case 1: // Putar Kanan
      if (currentTime - stateStartTime < WAKTU_PUTAR_90_DERAJAT) { putarKanan(KECEPATAN_PUTAR_HINDAR); } 
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 2: // Maju Samping
      if (currentTime - stateStartTime < WAKTU_MAJU_SAMPING) { maju(KECEPATAN_MAJU_NORMAL); }
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 3: // Putar Kiri
      if (currentTime - stateStartTime < WAKTU_PUTAR_90_DERAJAT) { putarKiri(KECEPATAN_PUTAR_HINDAR); }
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 4: // Maju Lewati
      if (currentTime - stateStartTime < WAKTU_MAJU_LEWATI) { maju(KECEPATAN_MAJU_NORMAL); }
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 5: // Putar Kiri lagi
      if (currentTime - stateStartTime < WAKTU_PUTAR_90_DERAJAT) { putarKiri(KECEPATAN_PUTAR_HINDAR); }
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 6: // Selesai, transisi ke state cari garis
      currentState = MENCARI_GARIS;
      Serial.println("State -> MENCARI_GARIS");
      break;
  }
}

void cariGaris_nonBlocking() {
  unsigned long currentTime = millis();

  // Prioritas utama: jika garis terdeteksi, langsung transisi state
  if (digitalRead(PIN_IR_KIRI) == GARIS_HITAM || digitalRead(PIN_IR_KANAN) == GARIS_HITAM) {
    Serial.println("Garis ditemukan!");
    currentState = MENENGAHKAN_POSISI;
    stateStartTime = millis();
    berhenti();
    Serial.println("State -> MENENGAHKAN_POSISI");
    return; // Keluar dari fungsi agar tidak menjalankan kode di bawah
  }
  
  // Logika untuk maju-putar-maju-putar
  if (currentTime - stateStartTime >= WAKTU_MAJU_CARI + WAKTU_PUTAR_CARI) {
    stateStartTime = currentTime; // Reset timer untuk siklus baru
    belokKiriTerakhir = !belokKiriTerakhir; // Balik arah putaran setiap siklus
  }
  
  if (currentTime - stateStartTime < WAKTU_MAJU_CARI) {
    maju(KECEPATAN_CARI);
  } 
  else {
    if (belokKiriTerakhir) { putarKiri(KECEPATAN_CARI); } 
    else { putarKanan(KECEPATAN_CARI); }
  }
}

void tengahkanPosisi_nonBlocking() {
  bool sensorKiri = (digitalRead(PIN_IR_KIRI) == GARIS_HITAM);
  bool sensorKanan = (digitalRead(PIN_IR_KANAN) == GARIS_HITAM);

  if (sensorKiri || sensorKanan) {
    // Jika salah satu atau kedua sensor masih di garis hitam, terus berputar
    if (sensorKiri) {
      putarKiri(KECEPATAN_CARI);
    } else { // sensorKanan pasti true di sini
      putarKanan(KECEPATAN_CARI);
    }
  }
  else {
    // Keduanya sudah di putih, berarti sudah pas di tengah. Selesai.
    berhenti();
    Serial.println("Posisi sudah di tengah.");
    currentState = MENGIKUTI_GARIS; // Transisi kembali ke state normal
    Serial.println("State -> MENGIKUTI_GARIS");
  }
}

// --- Fungsi Utilitas dan Kontrol Motor Dasar ---
long ukurJarakCm() {
  digitalWrite(TRIG_PIN, LOW); delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH); delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  long durasi = pulseIn(ECHO_PIN, HIGH, 25000); 
  return durasi * 0.034 / 2;
}

void maju(int kecepatan) {
  digitalWrite(PIN_LED_HIJAU, HIGH); digitalWrite(PIN_LED_KUNING, LOW); digitalWrite(PIN_LED_MERAH, LOW);
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatan); analogWrite(ENB, kecepatan);
}

void putarKanan(int kecepatan) {
  digitalWrite(PIN_LED_HIJAU, LOW); digitalWrite(PIN_LED_KUNING, HIGH); digitalWrite(PIN_LED_MERAH, LOW);
  digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatan); analogWrite(ENB, kecepatan);
}

void putarKiri(int kecepatan) {
  digitalWrite(PIN_LED_HIJAU, LOW); digitalWrite(PIN_LED_KUNING, HIGH); digitalWrite(PIN_LED_MERAH, LOW);
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);
  analogWrite(ENA, kecepatan); analogWrite(ENB, kecepatan);
}

void berhenti() {
  digitalWrite(PIN_LED_HIJAU, LOW); digitalWrite(PIN_LED_KUNING, LOW); digitalWrite(PIN_LED_MERAH, HIGH);
  digitalWrite(IN1, LOW); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, LOW);
  analogWrite(ENA, 0); analogWrite(ENB, 0);
}
```

### Penjelasan Kode dan Kalibrasi
- **`void loop()`**: Sangat bersih dan efisien. Hanya ada `if-else` untuk memilih antara `hindariHalangan()` atau `ikutiGaris()`. Ini menunjukkan prioritas yang jelas: keamanan (menghindari tabrakan) adalah nomor satu.
- **`hindariHalangan_nonBlocking()`**: Fungsi ini seperti sebuah "skrip" gerakan. Ia menjalankan serangkaian perintah maju, putar, dan berhenti dengan durasi (`delay`) yang sudah ditentukan. **PENTING:** Nilai `WAKTU_PUTAR_90_DERAJAT`, `WAKTU_MAJU_SAMPING`, dll., sangat bergantung pada kecepatan motor, voltase baterai, dan permukaan lantai. Anda **harus** mengkalibrasi nilai-nilai ini agar robot bisa bermanuver dengan mulus.
- **`cariGaris_nonBlocking()`**: Ini adalah logika pencarian yang cukup tangguh. Ia mencoba maju, lalu berputar, berulang kali. Jika robot Anda kesulitan menemukan garis, Anda bisa mengubah `LANGKAH_MAKSIMAL_CARI` atau `WAKTU_PUTAR_CARI`.
- **`tengahkanPosisi_nonBlocking()`**: Setelah garis ditemukan, fungsi ini memastikan robot berada di posisi yang benar sebelum kembali ke mode `ikutiGaris`. Ia akan memutar sedikit sampai kedua sensor tidak lagi melihat garis hitam, menempatkan robot tepat di tengah.
- **Fungsi Gerak dengan Parameter**: Perhatikan fungsi `maju()`, `putarKiri()`, dan `putarKanan()` sekarang menerima parameter `int kecepatan`. Ini membuat kode lebih fleksibel karena kita bisa memanggil fungsi yang sama dengan kecepatan yang berbeda-beda tergantung situasinya (misal: lebih lambat saat mencari garis, lebih cepat saat mengikuti garis lurus).

---

## Materi 7: Robot Cerdas dengan State Machine (Versi Lanjutan)

Selamat datang di materi puncak! Di sini kita akan membangun ulang logika dari materi sebelumnya menggunakan pendekatan yang jauh lebih canggih, fleksibel, dan kuat: sebuah **State Machine** (Mesin Status) dengan **kode non-blocking**. Ini adalah cara yang digunakan dalam sistem robotika dan embedded yang lebih serius.

### Masalah dengan Pendekatan `delay()`
Pada versi sebelumnya, kita menggunakan `delay()` untuk mengatur durasi setiap gerakan. `delay()` memiliki kelemahan besar: ia **memblokir** mikrokontroler. Saat `delay(1000)` berjalan, Arduino tidak bisa melakukan apa-apa lagi selama 1 detik—tidak bisa membaca sensor, tidak bisa merespons perintah, ia benar-benar "tertidur". Ini membuat robot tidak responsif.

### Solusi: State Machine dan `millis()`
**1. State Machine:**
Bayangkan robot hanya bisa berada dalam satu "mode" atau "status" (`state`) pada satu waktu. Misalnya:
- `MENGIKUTI_GARIS`: Mode normal.
- `MENGHINDARI_HALANGAN`: Mode saat ada rintangan.
- `MENCARI_GARIS`: Mode setelah menghindari rintangan.
- `MENENGAHKAN_POSISI`: Mode untuk mensejajarkan diri di garis.

Robot akan berpindah dari satu state ke state lain berdasarkan input sensor. Pendekatan ini membuat logika program sangat terstruktur dan mudah dikelola.

**2. Kode Non-Blocking dengan `millis()`:**
`millis()` adalah fungsi internal Arduino yang mengembalikan jumlah milidetik sejak program dimulai. Kita bisa menggunakannya untuk mengukur waktu tanpa menghentikan program.

Logikanya seperti ini:
- Catat waktu mulai (`stateStartTime = millis();`).
- Di dalam `loop()`, terus periksa `if (millis() - stateStartTime >= durasiYangDiinginkan)`.
- Jika kondisi terpenuhi, artinya waktu yang diinginkan telah berlalu. Lakukan aksi berikutnya dan catat waktu mulai yang baru.

Dengan cara ini, `loop()` terus berjalan ratusan kali per detik, memungkinkan robot untuk terus membaca sensor dan merespons dengan cepat bahkan saat sedang "menunggu".

### Kode Lengkap Versi State Machine

```cpp
// ==== PENGATURAN PIN DAN KONSTANTA ====
#define ENA 10
#define IN1 9
#define IN2 8
#define ENB 5
#define IN3 7
#define IN4 6
#define TRIG_PIN A3
#define ECHO_PIN A2
#define PIN_IR_KANAN 11
#define PIN_IR_KIRI  12
#define PIN_LED_MERAH 2
#define PIN_LED_KUNING 3
#define PIN_LED_HIJAU 4

// Definisi untuk pembacaan sensor IR agar lebih mudah dibaca
#define GARIS_HITAM HIGH
#define DASAR_PUTIH LOW

// --- Pengaturan Kecepatan ---
const int KECEPATAN_MAJU_NORMAL = 120;
const int KECEPATAN_BELOK = 100;
const int KECEPATAN_CARI = 80;
const int KECEPATAN_PUTAR_HINDAR = 100;

// --- Pengaturan Logika ---
const int JARAK_AMAN_CM = 20;

// --- Durasi Manuver (untuk millis()) ---
const int WAKTU_PUTAR_90_DERAJAT = 650; 
const int WAKTU_MAJU_SAMPING = 500;
const int WAKTU_MAJU_LEWATI = 700;
const int WAKTU_JEDA_ANTAR_MANUVER = 200;

// --- Durasi Pencarian Garis ---
const int WAKTU_MAJU_CARI = 200;    // Maju sedikit di setiap langkah cari
const int WAKTU_PUTAR_CARI = 40;   // Putar sedikit untuk mencari

// Mendefinisikan state (status) yang bisa dimiliki robot
enum RobotState {
  MENGIKUTI_GARIS,
  MENGHINDARI_HALANGAN,
  MENCARI_GARIS,
  MENENGAHKAN_POSISI
};

// Variabel global untuk menyimpan state saat ini
RobotState currentState = MENGIKUTI_GARIS;

// Variabel untuk manajemen state dan waktu
unsigned long stateStartTime = 0; // Waktu kapan state saat ini dimulai
int manuverStep = 0;              // Langkah ke berapa dalam sebuah manuver
bool belokKiriTerakhir = true;    // Untuk bolak-balik arah saat mencari garis

void setup() {
  Serial.begin(9600);

  pinMode(ENA, OUTPUT); pinMode(IN1, OUTPUT); pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT); pinMode(IN3, OUTPUT); pinMode(IN4, OUTPUT);
  pinMode(PIN_LED_MERAH, OUTPUT); pinMode(PIN_LED_KUNING, OUTPUT); pinMode(PIN_LED_HIJAU, OUTPUT);
  pinMode(PIN_IR_KANAN, INPUT); pinMode(PIN_IR_KIRI, INPUT);
  pinMode(TRIG_PIN, OUTPUT); pinMode(ECHO_PIN, INPUT);
  
  Serial.println("Robot Cerdas (State Machine) Siap!");
  berhenti();
  delay(500);
}

void loop() {
  // Pengecekan halangan menjadi prioritas utama untuk mengubah state.
  // Hanya bisa diinterupsi jika sedang mengikuti garis.
  long jarak = ukurJarakCm();
  if (jarak <= JARAK_AMAN_CM && jarak > 0 && currentState == MENGIKUTI_GARIS) {
    // Transisi state ke MENGHINDARI_HALANGAN
    currentState = MENGHINDARI_HALANGAN;
    manuverStep = 0;           // Reset langkah manuver
    stateStartTime = millis(); // Catat waktu mulai manuver
    berhenti();
    Serial.println("State -> MENGHINDARI_HALANGAN");
  }

  // Jalankan fungsi yang sesuai dengan state saat ini
  switch (currentState) {
    case MENGIKUTI_GARIS:
      ikutiGaris();
      break;
    case MENGHINDARI_HALANGAN:
      hindariHalangan_nonBlocking();
      break;
    case MENCARI_GARIS:
      cariGaris_nonBlocking();
      break;
    case MENENGAHKAN_POSISI:
      tengahkanPosisi_nonBlocking();
      break;
  }
}

void ikutiGaris() {
  // Fungsi ini tetap sama, karena ia reaktif dan tidak butuh delay
  bool sensorKiri = (digitalRead(PIN_IR_KIRI) == GARIS_HITAM);
  bool sensorKanan = (digitalRead(PIN_IR_KANAN) == GARIS_HITAM);

  if (!sensorKiri && !sensorKanan) { // Keduanya di putih
    maju(KECEPATAN_MAJU_NORMAL);
  } 
  else if (sensorKiri && !sensorKanan) { // Kiri di hitam, belok kiri
    putarKiri(KECEPATAN_BELOK);
  } 
  else if (!sensorKiri && sensorKanan) { // Kanan di hitam, belok kanan
    putarKanan(KECEPATAN_BELOK);
  } 
  else { // Keduanya di hitam
    berhenti();
  }
}

void hindariHalangan_nonBlocking() {
  unsigned long currentTime = millis();

  // Manuver ini dibagi menjadi beberapa langkah (step)
  switch (manuverStep) {
    case 0: // Jeda awal
      if (currentTime - stateStartTime >= WAKTU_JEDA_ANTAR_MANUVER) { manuverStep++; stateStartTime = currentTime; }
      else { berhenti(); }
      break;
    case 1: // Putar Kanan
      if (currentTime - stateStartTime < WAKTU_PUTAR_90_DERAJAT) { putarKanan(KECEPATAN_PUTAR_HINDAR); } 
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 2: // Maju Samping
      if (currentTime - stateStartTime < WAKTU_MAJU_SAMPING) { maju(KECEPATAN_MAJU_NORMAL); }
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 3: // Putar Kiri
      if (currentTime - stateStartTime < WAKTU_PUTAR_90_DERAJAT) { putarKiri(KECEPATAN_PUTAR_HINDAR); }
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 4: // Maju Lewati
      if (currentTime - stateStartTime < WAKTU_MAJU_LEWATI) { maju(KECEPATAN_MAJU_NORMAL); }
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 5: // Putar Kiri lagi
      if (currentTime - stateStartTime < WAKTU_PUTAR_90_DERAJAT) { putarKiri(KECEPATAN_PUTAR_HINDAR); }
      else { manuverStep++; stateStartTime = currentTime; berhenti(); }
      break;
    case 6: // Selesai, transisi ke state cari garis
      currentState = MENCARI_GARIS;
      Serial.println("State -> MENCARI_GARIS");
      break;
  }
}

void cariGaris_nonBlocking() {
  unsigned long currentTime = millis();

  // Prioritas utama: jika garis terdeteksi, langsung transisi state
  if (digitalRead(PIN_IR_KIRI) == GARIS_HITAM || digitalRead(PIN_IR_KANAN) == GARIS_HITAM) {
    Serial.println("Garis ditemukan!");
    currentState = MENENGAHKAN_POSISI;
    stateStartTime = millis();
    berhenti();
    Serial.println("State -> MENENGAHKAN_POSISI");
    return; // Keluar dari fungsi agar tidak menjalankan kode di bawah
  }
  
  // Logika untuk maju-putar-maju-putar
  if (currentTime - stateStartTime >= WAKTU_MAJU_CARI + WAKTU_PUTAR_CARI) {
    stateStartTime = currentTime; // Reset timer untuk siklus baru
    belokKiriTerakhir = !belokKiriTerakhir; // Balik arah putaran setiap siklus
  }
  
  if (currentTime - stateStartTime < WAKTU_MAJU_CARI) {
    maju(KECEPATAN_CARI);
  } 
  else {
    if (belokKiriTerakhir) { putarKiri(KECEPATAN_CARI); } 
    else { putarKanan(KECEPATAN_CARI); }
  }
}

void tengahkanPosisi_nonBlocking() {
  bool sensorKiri = (digitalRead(PIN_IR_KIRI) == GARIS_HITAM);
  bool sensorKanan = (digitalRead(PIN_IR_KANAN) == GARIS_HITAM);

  if (sensorKiri || sensorKanan) {
    // Jika salah satu atau kedua sensor masih di garis hitam, terus berputar
    if (sensorKiri) {
      putarKiri(KECEPATAN_CARI);
    } else { // sensorKanan pasti true di sini
      putarKanan(KECEPATAN_CARI);
    }
  }
  else {
    // Keduanya sudah di putih, berarti sudah pas di tengah. Selesai.
    berhenti();
    Serial.println("Posisi sudah di tengah.");
    currentState = MENGIKUTI_GARIS; // Transisi kembali ke state normal
    Serial.println("State -> MENGIKUTI_GARIS");
  }
}

// --- Fungsi Utilitas dan Kontrol Motor Dasar ---
long ukurJarakCm() {
  digitalWrite(TRIG_PIN, LOW); delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH); delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  long durasi = pulseIn(ECHO_PIN, HIGH, 25000); 
  return durasi * 0.034 / 2;
}

void maju(int kecepatan) {
  digitalWrite(PIN_LED_HIJAU, HIGH); digitalWrite(PIN_LED_KUNING, LOW); digitalWrite(PIN_LED_MERAH, LOW);
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatan); analogWrite(ENB, kecepatan);
}

void putarKanan(int kecepatan) {
  digitalWrite(PIN_LED_HIJAU, LOW); digitalWrite(PIN_LED_KUNING, HIGH); digitalWrite(PIN_LED_MERAH, LOW);
  digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH); digitalWrite(IN4, LOW);
  analogWrite(ENA, kecepatan); analogWrite(ENB, kecepatan);
}

void putarKiri(int kecepatan) {
  digitalWrite(PIN_LED_HIJAU, LOW); digitalWrite(PIN_LED_KUNING, HIGH); digitalWrite(PIN_LED_MERAH, LOW);
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);
  analogWrite(ENA, kecepatan); analogWrite(ENB, kecepatan);
}

void berhenti() {
  digitalWrite(PIN_LED_HIJAU, LOW); digitalWrite(PIN_LED_KUNING, LOW); digitalWrite(PIN_LED_MERAH, HIGH);
  digitalWrite(IN1, LOW); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, LOW);
  analogWrite(ENA, 0); analogWrite(ENB, 0);
}
```
### Keunggulan Pendekatan Ini
- **Responsif:** Robot dapat mendeteksi halangan atau menemukan garis *saat* sedang bermanuver, karena `loop()` tidak pernah diblokir.
- **Terstruktur:** Kode menjadi sangat rapi. Setiap mode memiliki fungsinya sendiri. Jika Anda ingin mengubah cara robot mencari garis, Anda hanya perlu menyentuh fungsi `cariGaris_nonBlocking()`.
- **Mudah Dikembangkan:** Ingin menambahkan state baru, misalnya `MODE_BATERAI_LEMAH`? Cukup tambahkan ke `enum`, buat fungsinya, dan atur logika transisinya.

Selamat! Anda telah menyelesaikan seluruh modul, dari kontrol motor dasar hingga state machine yang kompleks. Anda sekarang memiliki fondasi yang sangat kuat untuk mengembangkan proyek robotika yang lebih canggih.

