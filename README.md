# 🔧 ESP-Hosted Firmware

Pre-built ESP-Hosted firmware binaries for Arduino boards.

## 📦 Supported Targets

| Target | ESP Chip | Patch Set | ESP-Hosted Release | Firmware Version | ESP-IDF |
|--------|----------|-----------|--------------------|------------------|---------|
| portenta_c33 | ESP32-C3 | `latest` | `release/fg-1.0.0.0.0` | v1.0.0.0.3 | v5.4 |
| portenta_c33 | ESP32-C3 | `legacy` | `fe0b105` | v0.0.5 | v5.1 |

The last field of the firmware version counts the patches applied on top of
the upstream release, so a patched build never reports itself as stock. It is
stamped into `esp_fw_version.h` at build time, not maintained by hand. The
legacy release predates that header, so its version is pinned in the workflow
matrix and does not track its patch count.

### CI/CD

The GitHub Actions workflow automatically builds firmware for all targets on:
- Push to `main` branch
- Pull requests to `main`
- Tag pushes (creates a release with binaries)

## 📁 Structure

```
├── tools/
│   └── combine.py
└── <target>/
    └── <patch-set>/
        ├── patches/
        │   └── *.patch
        └── sdkconfig.defaults.<chip>
```

## 🚀 Flashing

```bash
espflash write-bin 0x0 <target>-<version>.bin
```

Or with esptool:

```bash
esptool.py --chip esp32c3 -p /dev/ttyACM0 -b 230400 \
  --before=default_reset --after=hard_reset --no-stub \
  write_flash --flash_mode dio --flash_freq 80m 0x0 <target>-<version>.bin
```

## 📄 License

The patches under `<target>/<patch-set>/patches/` are modifications to
[esp-hosted](https://github.com/espressif/esp-hosted). The files they touch live
under `esp_hosted_fg/esp`, which upstream licenses as Apache-2.0, so the patches
and the firmware built from them are Apache-2.0. See `LICENSE`, which is also
published with the release binaries.

The released image is produced by ESP-IDF and also contains the IDF bootloader
and Espressif's pre-compiled WiFi and Bluetooth libraries, which carry their own
licenses. See the [ESP-IDF
license](https://github.com/espressif/esp-idf/blob/master/LICENSE) for those.
