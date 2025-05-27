# 📱 VoltageOS ROM Building Guide for Motorola Cancunf

This guide will help you set up your environment and build **VoltageOS** for the **Motorola Cancunf** device.

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
mkdir voltage
cd voltage
```

---

## 🌐 Step 5: Initialize and Sync VoltageOS Source

```bash
repo init -u https://github.com/VoltageOS/manifest.git -b 15-qpr2 --git-lfs
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
git clone https://github.com/LineageOS/android_device_lineage_sepolicy device/lineage/sepolicy
```

---

## 🧪 Step 9: Edit Maintainer Info

```bash
cd device/motorola/cancunf/rro_overlay/SettingsOverlayCancunf/res/values
touch voltage_strings.xml
micro voltage_strings.xml
```

### ✍️ Copy paste (Edit maintainer name);
```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- Copyright (C) 2022 VoltageOS
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
     http://www.apache.org/licenses/LICENSE-2.0
     
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
-->
<resources xmlns:xliff="urn:oasis:names:tc:xliff:document:1.2">

    <string name="voltage_maintainer">Ayu Kashyap</string>

</resources>
```

---

## 🧬 Step 10: Cherry-pick Frameworks Commit

```bash
cd ../../../../../../../frameworks/base
git remote add genesis https://github.com/GenesisOS/frameworks_base.git
git fetch genesis
git cherry-pick d073213e70610ca517db91bd035e1cbca605368c
```

---

## 🔑 Step 11: Setup Signing Keys

```bash
cd ../../vendor/voltage-priv/keys
./keys.sh
```

---

## ✍️ Step 12: Modify TapGesture Package Name

```bash
cd ../../..
micro device/motorola/cancunf/app/MotoActions/src/org/lineageos/settings/device/actions/TapGestureSettings.java
```

Replace all occurrences of `lineage` with your ROM's package name (e.g., `custom`).

---

## 🛠️ Step 13: Rename Device Makefile

```bash
mv device/motorola/cancunf/aosp_cancunf.mk device/motorola/cancunf/voltage_cancunf.mk
```

---

## 🧾 Step 14: Update Build Configuration

```bash
micro device/motorola/cancunf/voltage_cancunf.mk
```

Replace all `aosp` references with `voltage`.

```bash
micro device/motorola/cancunf/AndroidProducts.mk
```

Also replace all `aosp` references with `voltage`.

---

## 🚀 Step 15: Build the ROM

```bash
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
