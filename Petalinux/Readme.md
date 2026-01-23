# Petalinux

### 簡介

Petalinux 是一個由 AMD 開發的嵌入式 Linux 開發套件，專為在 AMD 的 FPGA 晶片（如 Zynq、Versal）上開發 Linux 系統而設計。

它提供了一整套工具，簡化了為這些硬體平台建立、設定、編譯和部署客製化 Linux 系統的流程，包括支援 Yocto 專案、Linux 核心、開機載入程式和根檔案系統等組件。

</br>

對於熟悉 Yocto 的人可能可以很快上手，這份工具其實就是 xilinx 將 Yocto 再多包一層可以設定的工具並進行製作 img。

所以其實本身並不難，但基本上還是要先熟悉 Yocto。

</br>

## 使用

這邊先簡單介紹 Petalinux 實際上使用的流程。

### Step 1. 安裝

基本上跟著官網一步一步安裝即可。

須注意環境的建立，petalinux 基本上有自己的語法 `petalinux-config` 可以查看。

---

</br>

### Step 2. 建立專案

首先，建立一個基本的 linux kernel 專案想必需要一個底層 hardware，所以我們需要先利用 vivado 建立一個 BSP/DTS 等等，那這個底層就是 vivado export 的 xsa 檔。

有了基本的底層後我們就可以開始一步一步開始建立專案。

我們會需要有很大的記憶體空間（謝謝 yocto）; 除了基本的 BSP 之外我們需要有 u-boot 開機相關的文件，關於 uboot 可以去官網直接下載，這邊需要注意版本，若是版本沒有跟 petalinux 對上之後編譯會出錯。

### Step 2-1. 創建空專案

```bash
$ petalinux-create -t project -n petalinux --template zynqMP
```

各變數說明：
1. -t：type，創建的類型，這邊指創建`專案`
2. -n：name，創建項目的名稱
3. --template：創建的模板，xilinx 提供三種本專案使用的板子為 zynqMP

完成之後就可以看到創立的專案資料夾了。

### Step 2-2. 設定硬體

接下來有關設定該專案的命令都需要在該專案資料夾底下，`cd` 進去。

```bash
$ petalinux-config --get-hw-description /path/to/xsa
```

後面的 `/path/to/xsa` 直接指向 xsa 的資料夾即可。

### Step 2-3. 設定專案

在指定完成硬體後，會在終端中跳出一個 UI 介面，接下來就對該 UI 介面進行設定即可。

若是後續需要補設定直接輸入 `petalinux-config` 即可，不用再多次指向硬體。

</br>

接下列出需要設定的選項：

1. linux Components Selection：這裡可以選擇 u-boot 以及 kernel
   - 默認是從 github 上下載，但我們可以先從 xilinx 官網下載，若是需要修改的話需要從官網下載
   - 須注意版本差異！！！（2020.1 -> v5.4）
   - 選擇：
     - u-boot () 進去後，選擇 ext-local-src，之後會出現 External u-boot local source settings 讓我們選擇該路徑
     - linux kernel () 進去後，選擇 ext-local-src，之後會出現 External linux-kernel local source settings 讓我們選擇該路徑

2. Auto Config Settings 
   - kernel autoconfig 以及 u-boot autoconfig 需要勾選

以上兩項設定完成後保存退出即可。

---

</br>

### Step 3. 修改設備樹

進入 /petalinux/project-spec/meta-user/recipes-bsp/device-tree/files/ 裡面會有兩個檔案，選擇 system-user.dtsi 即可。

這邊可以加入對額外的設備的功能，像是 AXI 的擴充之類的。

當我們選擇好 petalinux 的 Hardware 時本身就會有對應的 dtsi 產出，這裡不是自己寫設備樹，而是額外增加的。

</br>

若是想單獨先檢查設備樹可以使用：

```bash
$ petalinux-build -c device-tree
```

但要先記得 `petalinux-config`

之後會產出 system.dtb 這是給機器看的，我們可以利用 device-tree-compiler 反編譯出來 dtsi。

```bash
$ sudo apt install -y device-tree-compiler

$ dtc -I dtb -O dts -o out.dts system.dtb
```

</br>

---

</br>

### Step 4. Yocto

緊張刺激的時候到了，yocto 本身是很龐大的編譯系統會有一推的下載文件湧入你的電腦，xilinx 有預先提供兩個下載包幫你解決這個問題。

關於下載包的選項可以上網找資源，我這邊是使用 yocto 預設的下載方式，只是需要改一下對應的 url：

```bash
https://petalinux.xilinx.com/sswreleases/rel-v2020/downloads/
```

個人很不推薦使用離線方式，畢竟有些東西還是有人堅持在維護的（謝謝 github 體系）。

之後使用 `petalinux-build` 耐心等待即可。

編譯成功後會在專案資料夾中出現 image/linux 這個資料夾裡面放置我們的編譯結果。

---

</br>

### Step 5. 打包

在 image/linux 資料夾中輸入：

```bash
$ petalinux-package --boot --fsbl --pmufw --u-boot --fpga --force
```

成功後會看到：images/linux/BOOT.BIN

須注意在 vivado export 中需要 include bitstream。

</br>

請先將 SD 卡分割好：

1. rootfs : 放置 root file system
	- images/linux/rootfs.tar.gz
	- 放入後記得解壓縮

2. boot : 放置開機用
	- images/linux/BOOT.BIN
	- images/linux/image.ub

對應的磁區會有不一樣的分割設定，上網找就好。

若是不知道怎麼用，請參考 DE10-Nano 的燒錄 SD Linux image 方式。

</br>

---

</br>

### Step 6. 開機

開機後其實還不會先進入 rootfs 的 user space ，會在 boot 中。

此時我們可以先利用 CLI 的輸入繼續開機，先測試是否東西都正確。

```bash
ZynqMP> mmc dev 0
ZynqMP> mmc rescan
ZynqMP> ls mmc 0:1

ZynqMP> load mmc 0:1 0x8000000 image.ub

ZynqMP> setenv bootargs 'console=ttyPS0,115200 root=/dev/mmcblk0p2 rw rootwait rootfstype=ext4'

ZynqMP> bootm 0x8000000
```

若是成功就會要求登入 root

</br>

### 小節

以上就是整個 petalinux 編譯出一個 yocto linux kernel 的使用方法。

</br>

</br>

# Petalinux 開發

基本上就跟 Yocto 開發一模一樣。

</br>

## 創建 meta-layer



</br>