# Xilinx

Xilinx 的軟體本身可以在不同的平台進行設計，官方都有給出不同的下載資源，

這邊一律建議全部都在 linux 還進底下進行。

安裝事宜就不贅述，網路資源很多！！

</br>

- OS : Ubuntu 22.04
- Main Board : AMD Zynq™ UltraScale+™ MPSoC ZCU104 評估套件

</br>

# 三種 IDE 與連結

剛接觸 xilinx 時一定會覺得很麻煩幹嘛分那麼多 IDE 那麼多介面，別急聽我娓娓道來 ～

首先我們正在學習與實做的是 FPGA + Linux + APP 的開發板，兩者之間各有它的好處那何不把他們合再一起。

</br>

讓我們先分清楚三者之間的用途：

1. Vivado 的工作是把「你想要的硬體系統」做出來。
2. Vitis 本質是「軟體開發 + 平台整合」，可以分三類情境：Bare-metal、FreeRTOS 與 Linux。
3. PetaLinux 是「把 Linux 為你的板子客製化」的整合工具。

</br>

那當我們要開始使用時該如何利用這三種不同的 IDE，以我的開發經驗來說，我習慣用以下的 wok-flow：

![xilinx ide work flow](/images/xilinx-workflow.png)

</br>

文字敘述一下：

先用 Vivado 將我們需要的硬體設計好，包括 PS 與 PL 端，甚至我們需要用到 IP 也一律先做好，再全數編譯完成後 ( Run Implementation ) 輸出 XSA 文件 ( 需要包含 Bitstream )，這就是我們的硬體描述文件了。

接下來，在正式進入 Linux 之前我們可以先用 Vitis 測試一下我們剛剛所設計好的硬體環境，通常如果有問題這邊就會有了。透過導入設計好的 XSA 文件，我們可以選擇需要的系統平台 ( Bare-metal、FreeRTOS 與 Linux，基本 Bare-metal 就夠了 )，在測試完之後就可以先大致確認這個 FPGA 硬體平台是 OK 的。

最後我們可以開始撰寫需要的 Linux 系統了，在搭建好 Petalinux 環境後，我們可以更改設備數或是像 u-boot 這種文件，誘惑是可以開始加入自己的 APP，最後編譯與打包我們的完整 image 給 SD 卡或是其他燒錄方式。

然而撰寫 Linux APP 的方式不只一種，我們可以透過 Petalinux 提供的 SDK 功能將平台的 Linux 底層資訊給 Vitis 做上層 User Space 的開發。


</br>

# Vivado

Version : 2022.2

</br>

# Vitits

Version : 2022.2

</br>

# Petalinux

Version : 2020.1

由於這是 Linux 開發，所以先切到別的[章節](Petalinux/Readme.md)做說明

</br>



