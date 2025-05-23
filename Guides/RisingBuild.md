
# 📱 RisingOS-Revived ROM Building Guide for Motorola Cancunf

This guide will help you set up your environment and build **RisingOS Revived** for the **Motorola Cancunf** device.

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
mkdir rising
cd rising
```

---

## 🌐 Step 5: Initialize and Sync RisingOS Revived Source

```bash
repo init -u https://github.com/RisingOS-Revived/android -b qpr2 --git-lfs
repo sync -c --no-clone-bundle --optimized-fetch --prune --force-sync -j$(nproc --all)
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
mv device/motorola/cancunf/aosp_cancunf.mk device/motorola/cancunf/lineage_cancunf.mk
```

---

## 🧾 Step 10: Update Build Configuration

#### Replace all `aosp` references with `lineage`:
```bash
micro device/motorola/cancunf/AndroidProducts.mk
```

#### Replace all `aosp` references with `lineage` and add the flags mentioned below:
```bash
micro device/motorola/cancunf/lineage_cancunf.mk
```
```bash
# Lunch banner maintainer variable
RISING_MAINTAINER="Ayu Kashyap"

# Chipset/Maintainer properties (ro.rising.chipset/ro.rising.maintainer) 
# Set RISING_MAINTAINER for version control 
# (Optional if builder is setting properties via init_<device>.cpp)
PRODUCT_BUILD_PROP_OVERRIDES += \
    RisingChipset="MediaTek Dimensity 7020" \
    RisingMaintainer="Ayu Kashyap"

RISING_MAINTAINER := "Ayu Kashyap"

# Disable/enable blur support, false by default
TARGET_ENABLE_BLUR := true

# Whether to ship aperture camera, false by default
PRODUCT_NO_CAMERA := false

# Whether to ship lawnchair launcher, false by default
TARGET_PREBUILT_LAWNCHAIR_LAUNCHER := false

# GMS build flags, false by default
# ship with GMS packages, replaces default AOSP packages with Google manufactured packages.
WITH_GMS := true

# These flags needs WITH_GMS set to true
# Whether to ship pixel launcher and set it as default launcher, false by default
TARGET_DEFAULT_PIXEL_LAUNCHER := true 

# Whether to ship prebuilt Google Dialer and Messages, false by default
TARGET_INCLUDE_GOOGLE_DIALER := true
```

---

## 🧬 Step 11: Cherry-pick HardwareManager fix

```bash
git -C device/motorola/cancunf remote add 2by2 https://github.com/2by2-Project-Devices/android_device_motorola_cancunf.git
git -C device/motorola/cancunf fetch 2by2
git -C device/motorola/cancunf cherry-pick 42403f68003b2ed3de8d508042c4d7696b2f4f60
```

---

## 🧬 Step 12: Fix for vendor_camera_prop missing

Paste `vendor_restricted_prop(vendor_camera_prop);` after `#Camera`

```bash
micro device/motorola/cancunf/sepolicy/vendor/property.te
```

---

## ✍️ Step 13: Fixing ATCI dependencies
```bash
micro device/motorola/cancunf/Android.bp
```
and add:
```bash
prebuilt_hidl_interfaces {
    name: "hidl_atcid_interface",
    interfaces: [
        "vendor.mediatek.hardware.atci@1.0::IAtcid",
    ],
}
```

---

## 🚀 Step 14: Build the ROM

```bash
. build/envsetup.sh
riseup cancunf userdebug 
```
Generating keys:
```bash
gk -s
```
Building rom:
```bash
rise b
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
  rise b
  ```

---

Happy Building! 🚀

