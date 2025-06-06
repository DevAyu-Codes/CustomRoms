# 📱 Alphadroid ROM Building Guide for Motorola Cancunf

This guide will help you set up your environment and build **Alphadroid** for the **Motorola Cancunf** device.

---

## 🧩 Step 1: Update System Packages and installing micro

```bash
sudo apt update && sudo apt upgrade -y
```
```bash
sudo apt install micro
```

---
VoltageOS
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
mkdir alpha
cd alpha
```

---

## 🌐 Step 5: Initialize and Sync Alphadroid Source

```bash
repo init -u https://github.com/alphadroid-project/manifest -b alpha-15.2 --git-lfs
repo sync
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

rm -rf device/lineage/sepolicy

git clone https://github.com/LineageOS/android_device_lineage_sepolicy device/lineage/sepolicy
```

---

## 🧬 Step 9: Cherry-pick Frameworks Commit

```bash
cd frameworks/base
git remote add genesis https://github.com/GenesisOS/frameworks_base.git
git fetch genesis
git cherry-pick d073213e70610ca517db91bd035e1cbca605368c
```

Manually fix the merge issue by removing the old code and then:
```bash
git add packages/SystemUI/src/com/android/systemui/statusbar/phone/CentralSurfacesImpl.java
git cherry-pick --continue
```

---

## ✍️ Step 10: Modify TapGesture Package Name

```bash
cd ../..
micro device/motorola/cancunf/app/MotoActions/src/org/lineageos/settings/device/actions/TapGestureSettings.java
```

Replace all occurrences of `com.android.internal.lineage` with your ROM's package name (e.g., `lineageos`).

---

## 🛠️ Step 11: Rename Device Makefile

```bash
mv device/motorola/cancunf/aosp_cancunf.mk device/motorola/cancunf/alphadroid_cancunf.mk
```

---

## 🧾 Step 12: Update Build Configuration

```bash
micro device/motorola/cancunf/alphadroid_cancunf.mk
```

Replace all `aosp` references with `alphadroid` and add this code:
```bash
# Device config
TARGET_HAS_UDFPS := false
TARGET_ENABLE_BLUR := false
TARGET_EXCLUDES_AUDIOFX := true
TARGET_FACE_UNLOCK_SUPPORTED := true

# Build config

# TARGET_BUILD_PACKAGE options:
# 1 - vanilla (default)
# 2 - microg
# 3 - gapps
TARGET_BUILD_PACKAGE := 3

# Launcher
TARGET_INCLUDE_LAWNCHAIR := false

# (valid only for GAPPS builds)
TARGET_INCLUDE_PIXEL_LAUNCHER := true
TARGET_SUPPORTS_QUICK_TAP := true
TARGET_SUPPORTS_CALL_RECORDING := true
TARGET_INCLUDE_STOCK_ARCORE := false
TARGET_INCLUDE_LIVE_WALLPAPERS := false
TARGET_SUPPORTS_GOOGLE_RECORDER := false

# Debugging
TARGET_INCLUDE_MATLOG := false
WITH_ADB_INSECURE := true

# Extras
TARGET_INCLUDE_RIMUSIC := false

# Maintainer
ALPHA_BUILD_TYPE := Unofficial
ALPHA_MAINTAINER := "Ayu\u00A0Kashyap"
```

```bash
micro device/motorola/cancunf/AndroidProducts.mk
```

Also replace all `aosp` references with `alphadroid`.

---

## 🔑 Step 13: Setup Signing Keys

```bash
git clone https://github.com/alphadroid-project/vendor_alpha-priv_keys vendor/alpha-priv/keys
cd vendor/alpha-priv/keys
./keys.sh
```

---

## 🚀 Step 14: Build the ROM

```bash
cd ../../..
. build/envsetup.sh
brunch cancunf
```

---

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
  brunch cancunf
  ```

---

Happy Building! 🚀
