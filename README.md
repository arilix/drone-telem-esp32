# Drone Telemetry ESP32

Kumpulan firmware binary **DroneBridge ESP32 v2.2.0 (stable)** untuk
membangun bridge telemetry antara flight controller dan Ground Control
Station (GCS) melalui Wi-Fi. Repository ini juga menyertakan paket
`arduino-esp32` versi 3.2.0 yang digunakan untuk tool dan board support.

## Isi repository

| Direktori | Keterangan |
| --- | --- |
| [`DroneBridge_ESP32_v2_2_0_stable/`](./DroneBridge_ESP32_v2_2_0_stable) | Firmware siap flash untuk beberapa keluarga ESP32 |
| [`esp32-3.2.0/`](./esp32-3.2.0) | Board support package Arduino-ESP32 3.2.0 |
| [`DroneBridge_ESP32_v2_2_0_stable/db_params.csv`](./DroneBridge_ESP32_v2_2_0_stable/db_params.csv) | Parameter konfigurasi default |
| [`DroneBridge_ESP32_v2_2_0_stable/flashing_instructions.txt`](./DroneBridge_ESP32_v2_2_0_stable/flashing_instructions.txt) | Instruksi flashing upstream |

### Varian firmware

Firmware tersedia untuk:

- ESP32
- ESP32-S2
- ESP32-S3
- ESP32-C3
- ESP32-C6

Beberapa board memiliki varian tambahan:

- `*_official`: layout untuk board resmi DroneBridge
- `*_noUARTConsole`: tanpa console UART
- `*_USBSerial`: menggunakan USB serial; khususnya berguna untuk ESP32-C3

Gunakan varian yang sesuai dengan chip dan layout board. Jangan mencampur
binary antar keluarga chip.

## Kebutuhan

- Board ESP32 yang kompatibel
- Kabel USB atau adapter serial sesuai board
- Python 3
- `esptool` terbaru:

```bash
python -m pip install esptool
```

## Flashing firmware

1. Masuk ke direktori varian firmware yang dipilih, misalnya:

   ```bash
   cd DroneBridge_ESP32_v2_2_0_stable/esp32c3
   ```

2. Hapus flash lama:

   ```bash
   python -m esptool --port PORT erase_flash
   ```

3. Flash seluruh image. Ganti `PORT` dengan port serial board, misalnya
   `/dev/ttyUSB0` atau `COM4`:

   ```bash
   python -m esptool \
     --chip esp32c3 \
     --port PORT \
     --baud 115200 \
     --before default_reset \
     --after hard_reset \
     write_flash \
     --flash_mode dio \
     --flash_size 2MB \
     --flash_freq 80m \
     0x0 bootloader.bin \
     0x8000 partition-table.bin \
     0x10000 db_esp32.bin \
     0x190000 www.bin
   ```

Perintah dan offset dapat berbeda untuk tiap keluarga chip. Lihat
[`flashing_instructions.txt`](./DroneBridge_ESP32_v2_2_0_stable/flashing_instructions.txt)
untuk perintah lengkap ESP32, ESP32-S2, ESP32-S3, ESP32-C3, dan ESP32-C6.

> Beberapa folder varian hanya berisi image tertentu karena mengikuti
> layout board masing-masing. Pastikan semua file yang disebut perintah
> flashing tersedia di folder varian sebelum menjalankan perintah.

## Setelah flashing

1. Nyalakan ulang board.
2. Hubungkan laptop atau ponsel ke access point:

   - SSID: `DroneBridge for ESP32`
   - Password: `dronebridge`

3. Buka web interface di:

   - <http://192.168.2.1/>
   - <http://dronebridge.local/>

URL hostname bergantung pada dukungan mDNS di perangkat client. Jika
hostname tidak dapat diakses, gunakan alamat IP.

## Konfigurasi default

Konfigurasi awal tersedia di
[`db_params.csv`](./DroneBridge_ESP32_v2_2_0_stable/db_params.csv). Nilai
penting yang digunakan firmware ini antara lain:

| Parameter | Nilai |
| --- | --- |
| Access point IP | `192.168.2.1` |
| Wi-Fi channel | `6` |
| Hostname | `DroneBridgeESP32` |
| Baud rate serial | `57600` |
| Ukuran paket | `576` byte |
| Protocol | `4` |

Segera ganti password access point sebelum digunakan pada lingkungan
operasional.

## Catatan

- Repository ini berisi firmware binary; source aplikasi DroneBridge tidak
  disertakan.
- Pastikan board menggunakan tegangan dan wiring serial yang benar sebelum
  menghubungkan flight controller.
- Proses flashing menghapus isi flash. Simpan konfigurasi penting terlebih
  dahulu.
- Gunakan firmware hanya pada perangkat dan frekuensi radio yang sesuai
  dengan peraturan setempat.

## Referensi

- [DroneBridge](https://github.com/DroneBridge/ESP32)
- [Arduino core for ESP32](https://github.com/espressif/arduino-esp32)
