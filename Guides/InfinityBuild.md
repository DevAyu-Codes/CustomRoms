
# 📱 InfinityX ROM Building Guide for Motorola Cancunf

This guide will help you set up your environment and build **InfinityX** for the **Motorola Cancunf** device.

---

## 🧩 Step 1: Update System Packages and installing micro

```bash
sudo apt update && sudo apt upgrade -y
```
```bash
sudo apt install micro
```

---

## 📦 Step 2: Install Required Dependencies

```bash
sudo apt install -y \
    git gnupg flex bison build-essential zip curl zlib1g-dev \
    gcc-multilib g++-multilib libc6-dev-i386 libncurses-dev \
    x11proto-core-dev libx11-dev libgl1-mesa-dev libxml2-utils xsltproc unzip \
    fontconfig bc python3 python3-pip libssl-dev rsync \
    ccache jq wget nano lzop pngcrush schedtool \
    openjdk-17-jdk imagemagick python-is-python3 lsof lz4 libtinfo-dev
```

### 🔄 Install and setup Git LFS

```bash
sudo apt update
sudo apt install git-lfs
git lfs install
```

```bash
git config --global user.email "ayushkumar274549@gmail.com"
git config --global user.name "DevAyu-Codes"
```

---

## 🧰 Step 3: Set Up Repo Tool

```bash
mkdir -p ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
export PATH=~/bin:$PATH
echo 'export PATH=~/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

---

## 📁 Step 4: Create Source Directory

```bash
mkdir infinity
cd infinity
```

---

## 🌐 Step 5: Initialize and Sync InfinityX Source

```bash
repo init --no-repo-verify --git-lfs -u https://github.com/ProjectInfinity-X/manifest -b 15 -g default,-mips,-darwin,-notdefault
repo sync -c --no-clone-bundle --no-tags --optimized-fetch --prune --force-sync -j$(nproc --all)
```

---

## 🔄 Step 6: Pull Git LFS Files

```bash
repo forall -c 'git lfs pull'
```

---

## 📱 Step 7: Clone Device Trees and Vendor Repos

```bash
git clone https://github.com/PixelOS-Devices/device_motorola_cancunf device/motorola/cancunf
git clone https://github.com/PixelOS-Devices/device_motorola_cancunf-kernel device/motorola/cancunf-kernel
git clone https://github.com/PixelOS-Devices/vendor_motorola_cancunf vendor/motorola/cancunf
git clone https://github.com/yaap/vendor_motorola_cancunf-motcamera vendor/motorola/cancunf-motcamera
```

---

## 🧱 Step 8: Clone HALs and SEPolicy

```bash
git clone https://github.com/LineageOS/android_hardware_mediatek hardware/mediatek
git clone https://github.com/LineageOS/android_device_mediatek_sepolicy_vndr device/mediatek/sepolicy_vndr
```

---

## 🛠️ Step 9: Rename Device Makefile

```bash
mv device/motorola/cancunf/aosp_cancunf.mk device/motorola/cancunf/infinity_cancunf.mk
```

---

## 🧾 Step 10: Update Build Configuration

#### Replace all `aosp` references with `infinity`:
```bash
micro device/motorola/cancunf/AndroidProducts.mk
```

#### Replace all `aosp` references with `infinity` and add the flags mentioned below:
```bash
micro device/motorola/cancunf/infinity_cancunf.mk
```
```bash
# InfinityOS Custom Flags
INFINITY_MAINTAINER := "Ayu\u00A0Kashyap"

# Blur & GApps Config
TARGET_SUPPORTS_BLUR := false
WITH_GAPPS := true
TARGET_SHIPS_FULL_GAPPS := false
TARGET_SHIPS_GOOGLE_DIALER := true
USE_MOTO_CALCULATOR := true
```

---

## 🧾 Step 11: Update Specs Configuration

```bash
micro device/motorola/cancunf/device.mk
```
and add:
```bash
PRODUCT_SYSTEM_PROPERTIES += \
    ro.infinity.soc="MediaTek\u00A0Dimensity\u00A07020\u00A0(6nm)" \
    ro.infinity.battery="6000mAh\u00A0Li-Po\u00A0(non-removable)" \
    ro.infinity.display="6.5\u00A0FHD+\u00A0(2400x1080),\u00A0120Hz" \
    ro.infinity.camera="50MP\u00A0(f/1.8)\u00A0+\u00A08MP\u00A0(UW)\u00A0+\u00A016MP" \
    ro.product.marketname="Moto\u00A0G54\u00A05G"
```

---

## ✍️ Step 12: Modify TapGesture Package Name

```bash
micro device/motorola/cancunf/app/MotoActions/src/org/lineageos/settings/device/actions/TapGestureSettings.java
```

Replace all occurrences of `lineage` with your ROM's package name (e.g., `infinity`).

---

## 🧾 Step 13: Fix MobileFeliCaClient error

```bash
micro vendor/motorola/cancunf/Android.bp
```

Search for MobileFeliCaClient and remove `skip_preprocessed_apk_checks: true,`

---

## 🔑 Step 14: Setup Signing Keys

```bash
. build/envsetup.sh
croot && git clone https://github.com/ProjectInfinity-X/vendor_infinity-priv_keys-template vendor/infinity-priv/keys
cd vendor/infinity-priv/keys/
./keys.sh
```

---

## 🚀 Step 15: Build the ROM

```bash
cd ../../..
lunch infinity_cancunf-userdebug
mka bacon
```

## ⚡ Optional: Enable and Configure ccache

```bash
export USE_CCACHE=1
ccache -M 50G
```

---

## ✅ Notes

- Recommended: **Ubuntu 20.04+**, **32GB RAM or more**, **300GB+ storage**
- Final output will be in: `out/target/product/cancunf/`
- Resume build if interuppted:
  ```bash
  mka bacon
  ```

---

Happy Building! 🚀

