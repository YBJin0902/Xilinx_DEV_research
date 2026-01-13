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

```dts
/include/ "system-conf.dtsi"
&amba {
	xlnk {
		compatible = "xlnx,xlnk-1.0";
	};
};
/{
   reserved-memory {
		#address-cells = <2>;
		#size-cells = <2>;
		ranges;
		reserved: buffer@0x10000000 {
			 no-map;
			 reg = <0x10000000 0x0DF9E000>;
		};
	};

	reserved-driver@0 {
		compatible = "xlnx,reserved-memory";
		memory-region = <&reserved>;
	};
};
```

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
$ peatlinux-package --boot --u-boot --fpga --force
```

須注意在 vivado export 中需要 include bitstream。

</br>

打包成功後會出現三個文件：boot.scr、BOOT.BIN、image.ub。

1. 若是利用 SD 卡先進行測試的話，複製到 SD 卡中即可。
   - 須注意 SD 卡需要格式化成 FAT 格式。

2. 可以利用 vitis 繼續往下開發

</br>

### 小節

以上就是整個 petalinux 編譯出一個 yocto linux kernel 的使用方法。

接下來就是進入 vitis 撰寫我們的 APP 即可。

</br>