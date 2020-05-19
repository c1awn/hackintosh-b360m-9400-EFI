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
| 内存  | 枭鲸 16G x2 寨厂 貌似是玖和马甲 |
| 显示器  | LG 4K Mac不上4K对不住眼睛 |    
   
[main refer](https://github.com/GeQ1an/MSI-B360M-MORTAR-HACKINTOSH-OPENCORE-EFI "refer")  
### update  in 20200519，睡眠bug已解决
- 问题阐述：usb定制后发现睡眠时机箱和CPU散热风扇依旧转，且显卡会不定时狂转几秒后停止，此问题无论是否开启小憩都会存在，而有人反映要开启小憩才能睡眠。
- 解决过程：1.`pmset -g assertions`和`log show --last 1h | grep -Ei "Wake Reason"`查看睡眠原因，pmset显示有蓝牙和parsec-fbf，后者显示`kernel: (AppleACPIPlatform) AppleACPIPlatformPower Wake reason: ?`，由于相信已经内建usb蓝牙，于是精力放在parsec-fbf和 Wake reason: ?上，未果。远偶然在远景看到一篇讲usb一分二导致不能睡眠的帖子，got it。
- **原因：蓝牙接在usb一分二hub上，尽管一分二已经内建。蓝牙接在主板usb接口，睡眠马上正常，无论小憩是否开启**      
为什么用一分二usb？因为MSI B360M迫击炮只有一个usb2.0接口，而机箱面板有usb2.0，94360CD无线网卡的蓝牙也要接usb2.0。


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
