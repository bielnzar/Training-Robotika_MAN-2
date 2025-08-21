# ⚙️ Prepare Environment: Menyiapkan Komputer untuk Arduino
Selamat datang! Sebelum kita mulai merakit dan memprogram robot, ada satu langkah super penting yang harus kita lakukan: memastikan komputer kita siap dan bisa "berbicara" dengan Arduino.

Modul ini akan memandumu langkah demi langkah untuk menginstall semua software yang dibutuhkan dan melakukan tes koneksi pertama. Anggap saja ini adalah sesi pemanasan sebelum kita masuk ke pertandingan utama!

## Langkah 1: Instalasi Arduino IDE
Arduino IDE (Integrated Development Environment) adalah "bengkel" digital kita. Di sinilah kita akan menulis semua kode perintah untuk robot.

1. Buka browser internet dan kunjungi situs resmi Arduino di: [arduino.cc/en/software](https://www.arduino.cc/en/software).
2. Cari versi yang sesuai dengan sistem operasi komputermu (misalnya, **Windows Win 10 and newer, 64 bits**).
3. Klik link tersebut, lalu pilih "**Just Download**" atau "Contribute & Download".
4. Setelah file berhasil diunduh, buka dan install aplikasi tersebut. Ikuti saja petunjuk yang muncul di layar hingga proses instalasi selesai.

## Langkah 2: Instalasi Driver CH340 (Sangat Penting!)
Kebanyakan Arduino Nano (clone) yang kita gunakan saat ini memakai sebuah chip komunikasi khusus bernama CH340. Sayangnya, komputer kita tidak otomatis mengenali chip ini.

**Analogi:** Bayangkan Arduino Nano berbicara dalam bahasa "CH340". Komputer kita butuh aplikasi "penerjemah" (yaitu driver ini) agar bisa mengerti apa yang dikatakan oleh Arduino.

1. Unduh driver CH340. Kamu bisa menggunakan salah satu sumber terpercaya berikut:
    *   **Sumber Lokal (Direkomendasikan):** [Arduino Indonesia - Cara Install Driver CH340](https://www.arduinoindonesia.id/2020/08/cara-install-driver-ch340-ch341-di.html)
    *   **Sumber Internasional:** [SparkFun Electronics](https://learn.sparkfun.com/tutorials/how-to-install-ch340-drivers/all) (scroll ke bawah ke bagian "Documents")
2. Buka link unduhan, biasanya file akan berformat `.ZIP`. Ekstrak file tersebut.
3. Jalankan file instalasi (biasanya bernama `SETUP.EXE` atau `INSTALL.EXE`).
4. Klik tombol "**Install**" pada jendela yang muncul.
5. Setelah instalasi berhasil, **Restart komputermu**. Langkah ini wajib agar driver bisa berfungsi dengan sempurna.

## Langkah 3: Tes Koneksi Arduino
Sekarang, saatnya pembuktian! Mari kita cek apakah Arduino dan komputer sudah saling "kenal".

1.  Sambungkan Arduino Nano ke salah satu port USB di komputermu menggunakan kabel data. Perhatikan, lampu power (biasanya berwarna merah) di papan Arduino seharusnya akan menyala.
2.  Buka aplikasi Arduino IDE yang sudah kamu install.
3.  Di menu bar atas, lakukan konfigurasi berikut:
    *   `Tools > Board > Arduino AVR Boards > Arduino Nano`.
    *   `Tools > Processor > ATmega328P (Old Bootloader)`. (Jika nanti saat upload kode ada error, kamu bisa coba ganti ke `ATmega328P` saja).
    *   `Tools > Port`. Di sini, seharusnya akan muncul sebuah port baru, contohnya `COM3`, `COM4`, atau `COM5` (di Windows). Pilih port tersebut. Jika tidak ada port yang bisa dipilih, kemungkinan ada masalah dengan kabel data atau instalasi driver CH340 belum berhasil.
4.  **Waktunya Tes Upload!** Kita akan mengirim program paling dasar ke Arduino.
    *   Klik `File > Examples > 01.Basics > Blink`. Sebuah jendela kode baru akan terbuka.
    *   Kode "Blink" ini punya satu tugas sederhana: membuat lampu kecil (`LED_BUILTIN`) yang sudah ada di papan Arduino untuk berkedip.
    *   Klik tombol **Upload** (ikon panah ke kanan) yang ada di pojok kiri atas.
5.  Perhatikan bar status di bagian bawah. Arduino IDE akan meng-compile kode lalu mengirimkannya ke Arduino.
6.  Jika proses berhasil, kamu akan melihat pesan "**Done uploading.**".
7.  Lihat papan Arduino Nano-mu! Jika lampu kecil di atasnya (biasanya berwarna biru atau oranye) mulai berkedip setiap detik, **SELAMAT!** 🎉

Komputer dan Arduino-mu sudah berhasil terhubung. Kamu siap untuk melanjutkan ke modul berikutnya!
