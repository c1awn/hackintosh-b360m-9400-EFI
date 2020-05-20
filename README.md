# hackintosh-b360m-9400 自用EFI备份
```
                                      mac
                                      ----------------------------------------------
                                      Model   : Hackintosh (SMBIOS: iMac19,1)
                 ###                  OS      : macOS Catalina 10.15.4 19E287
               ####                   Kernel  : Darwin 19.4.0
               ###                    Uptime  : 2 hours, 58 minutes
       #######    #######             Shell   : /bin/bash
     ######################           Time    : 
    #####################             CPU     : Intel Core i5-9400 2.90GHz x (6)
    ####################              Memory  : 2709MB(Avai) / 32768MB(Total)
    ####################              Disk    : 200GB(Avai) / 233GB(Total)
    #####################             IP Addr : Public /Intranet 192.168.2.143
     ######################           LCD     : LG
      ####################            Terminal: xterm-256color by Apple Terminal
        ################              Graphics: Radeon RX 480 (8 GB), 3840 x 2160 (2160p/4K UHD 1 - Ultra High Definition)
         ####     #####               Display : 3840 x 2160 (2160p/4K UHD 1 - Ultra High Definition)
```
### 配置
|   硬件 | 实例  |
| ------------ | ------------ |
| CPU  | i5 9400带核显  |
|  主板 |B360m迫击炮   |
| 显卡  | 蓝宝石Rx480  |
| 固态  | 西数SN550    |
| 内存  | 枭鲸 16G x2 寨厂 貌似是玖和马甲 |
| 显示器  | LG 4K Mac不上4K对不住眼睛 |    
   
[main refer](https://github.com/GeQ1an/MSI-B360M-MORTAR-HACKINTOSH-OPENCORE-EFI "refer")  
### update  in 20200519，睡眠bug已解决
- 问题阐述：usb定制后发现睡眠时机箱和CPU散热风扇依旧转、主板灯亮，且显卡会不定时狂转几秒后停止，此问题无论是否开启小憩都会存在，而有人反映要开启小憩才能睡眠。
- 解决过程：1.`pmset -g assertions`和`log show --last 1h | grep -Ei "Wake Reason"`查看睡眠原因，pmset显示有蓝牙和parsec-fbf，后者显示`kernel: (AppleACPIPlatform) AppleACPIPlatformPower Wake reason: ?`，由于相信已经内建usb蓝牙，于是精力放在parsec-fbf和 Wake reason: ?上，未果。远偶然在远景看到一篇讲usb一分二导致不能睡眠的帖子，got it。
- **原因：蓝牙接在usb一分二hub上，尽管一分二已经内建。而蓝牙接在主板usb接口，睡眠（手动/自动）马上正常。不过，不开小憩的话，多次睡眠唤醒循环后自动睡眠偶尔会失败（屏幕黑但是主机风扇转、主板灯亮），开启小憩后还没遇到自动睡眠失败。10.15开启小憩注意会有RTC唤醒，要开启禁止RTC唤醒补丁**      
为什么用一分二usb？因为MSI B360M迫击炮只有一个9针usb2.0接口，而机箱面板有usb2.0，94360CD无线网卡的蓝牙也要接usb2.0。    
>睡眠即醒很大程度上跟USB的定制相关，一般一个好的USB定制就能解决睡眠即醒的问题。当然系统的更新，MacOS的也在做不断的调整，比如蓝牙不能在HUB下进行内建，比如雷电卡必须将4个端口全部内建才行，等等。甚至有些时候我们都不知道为什么黑果会睡不着，那有没有一个办法让黑果强制睡眠呢？答案是有的。经过我的摸索，有几种方法能达到强制睡眠的效果，只是方法不同而已，但主要围绕的还是0d/6d的数值来做一些工作。       
[睡眠扩展链接-3.10 睡眠即醒的相关问题](https://blog.xjn819.com/?p=543 "睡眠扩展链接-3.10 睡眠即醒的相关问题")  


### Main change：  
- SMBIOS 必须自定义
- 改USBinjectall为usbport，删除cpufriend，不需要hwp变频
- 修改oc boot menu参数
- 测试Nvmefix.kext对温度的变化不明显，EFI里没加
### What's work
- 声卡（板载）/ 网卡（板载）
- 显卡 硬解 4K（HEVC + H.264） Videoproc显示UHD630硬解，而不是独显。通过观看4K视频、转码等发现主要是核显出力，CPU基本满载，独显很平淡。网上说核显作为主力对剪辑视频啥的速度有提升。
- WiFi（PCI-E 设备） / 蓝牙（PEI-E 载 USB 设备）
- 隔空投送 / 接力 / 随航
- FaceTime / iMessage
- 睿频 /原生电源管理
- 睡眠 / 键盘、鼠标唤醒
