# CAM-IMX462Mono

Sony IMX462 Starvis monochrome camera module — driver and usage resources.

---

## 1. Hardware

- [Product Page](https://www.inno-maker.com/product/cam-mipi462mono/)

---

## 2. Raspberry Pi Usage

For Raspberry Pi setup, installation, and usage, please refer to the dedicated repository:

**[INNO-MAKER/CAM-MIPI327RAW-and-CAM-MIPI462RAW](https://github.com/INNO-MAKER/CAM-MIPI327RAW-and-CAM-MIPI462RAW)**

That repository covers:
- Driver installation for Raspberry Pi (Bookworm / Trixie)
- `config.txt` overlay configuration
- libcamera / rpicam preview and capture commands
- HCG mode switching
- Tuning file usage

For HCG support on Raspberry Pi, see also the `HCG_Raspberry/` folder in this repository.

---

## 3. Jetson Orin Nano Usage

Prebuilt binary driver for **NVIDIA Jetson Orin Nano** (L4T R36.4.x / JetPack 6, kernel `5.15.148-tegra`).

> **Supported board crystal: 74.25 MHz → 1080p60.**

Driver package location: [`jetson-orin-nano-driver/5.15.148/`](jetson-orin-nano-driver/5.15.148/)

### 3.1 Package Contents

| File | Description |
| :--- | :--- |
| `binary/imx462.ko` | Prebuilt kernel module |
| `overlays/tegra234-imx462-cam0.dtbo` | Device tree overlay for CAM0 |
| `overlays/tegra234-imx462-cam1.dtbo` | Device tree overlay for CAM1 |
| `overlays/tegra234-imx462-dual.dtbo` | Device tree overlay for dual CAM0+CAM1 |
| `isp/camera_overrides.imx462_tuned.isp` | Calibrated Argus ISP tuning profile |
| `scripts/install_binary.sh` | One-step installer (never edits extlinux.conf) |
| `scripts/preview_argus.sh` | Live preview — color Argus pipeline |
| `scripts/preview_argus_mono.sh` | Live preview — Mono pipeline |
| `scripts/capture_argus_image.sh` | Capture JPEG via Argus |
| `scripts/capture_argus_mono_image.sh` | Capture Mono image via Argus |
| `scripts/capture_v4l2_image.sh` | Capture RAW image via V4L2 |
| `USER_MANUAL.md` | Full usage guide |

### 3.2 Installation

**Step 1: Extract and install**

```bash
tar -xzf imx462_tegra_binary_working_20260630_v1.1.tar.gz
cd imx462_tegra_binary_working_20260630_v1.1
sudo bash scripts/install_binary.sh
```

**Step 2: Activate overlay**

```bash
# List available overlays:
sudo python3 /opt/nvidia/jetson-io/config-by-hardware.py -l

# Single camera on CAM0:
sudo python3 /opt/nvidia/jetson-io/config-by-hardware.py -n '2=Camera IMX462 CAM0'

# Single camera on CAM1:
sudo python3 /opt/nvidia/jetson-io/config-by-hardware.py -n '2=Camera IMX462 CAM1'

# Dual CAM0+CAM1:
sudo python3 /opt/nvidia/jetson-io/config-by-hardware.py -n '2=Camera IMX462 Dual (CAM0+CAM1)'
```

**Step 3: Reboot**

```bash
sudo reboot
```

### 3.3 Quick Verification

```bash
ls /dev/video0 /dev/video1
v4l2-ctl -d /dev/video0 --set-fmt-video=width=1920,height=1080,pixelformat=RG10 \
  --stream-mmap --stream-count=60
```

### 3.4 HCG / LCG Mode Switching

```bash
# Read current mode (0 = LCG, 1 = HCG):
cat /sys/module/imx462/parameters/hcg_mode

# Switch to HCG (low-light, lower read noise):
echo 1 | sudo tee /sys/module/imx462/parameters/hcg_mode

# Switch back to LCG (higher dynamic range):
echo 0 | sudo tee /sys/module/imx462/parameters/hcg_mode
```

### 3.5 Preview

```bash
# Color Argus preview:
bash scripts/preview_argus.sh

# Mono pipeline preview:
bash scripts/preview_argus_mono.sh
```

For full usage including capture commands, camera↔node mapping, ISP configuration, and troubleshooting, see **USER_MANUAL.md** inside the package.

---

## 4. FAQ

### Camera not detected on Raspberry Pi 5 / CM5

For color version, refer to [CAM-MIPI327RAW-and-CAM-MIPI462RAW](https://github.com/INNO-MAKER/CAM-MIPI327RAW-and-CAM-MIPI462RAW) for the latest driver and tuning file for Raspberry Pi 5.

### `modprobe imx462` → `invalid module format` on Jetson

The prebuilt `.ko` was built for kernel `5.15.148-tegra`. Check your running kernel with `uname -r`. If it does not match, contact support for a matching build.
