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

首先我們正在學習與實做的是 FPGA + Linux 的開發板，兩者之間各有它的好處那何不把他們合再一起。

在之前我碰過的板子是 Intel 的 FPGA 開發板，在舊環境中我們知道要用 Quartus 設計 FPGA，那在 xilinx 就只是換成 vivado 而已。

那在這之後我們可能要設計我們的軟核像是 Nios II 之類的用的就是 eclipse，在這裡換成 vitis。

那再加上 linux 對多核心管理呢，那就用 petalinux，第一次聽到可能覺得很神奇 xilinx 為了 linux 推出了自己的套件，其實不然， petalinux 就只是包了一層專屬於 xilinx 的 yocto 而已。（若是對 yocto 不熟的話建議可以去 Bootlin 看看）

</br>

小總結一下：
1. vivado：FPGA 主要編輯環境。
2. vitis：編寫軟核功能環境（Application）。
3. petalinux：設計與編譯 linux kernel 的環境。

# Vivado

Version : 2022.1

</br>

# Vitits

Version : 2022.1

</br>

# Petalinux

由於這是 Linux 開發，所以先切到別的[章節](Petalinux/Readme.md)做說明

</br>



