# hackintosh-b360m-9400 自用EFI备份/OC/iMac19,1(核显+独显) 
![OS](https://pic1awn.oss-cn-shanghai.aliyuncs.com/img/20211121085133.png?tubed)
[Origin EFI From this link](https://github.com/GeQ1an/MSI-B360M-MORTAR-HACKINTOSH-OPENCORE-EFI "refer")  
### Summary：  
- SMBIOS的三码必须自定义，避免和别人重复导致appleid被锁
- 改USBinjectall为usbport，删除cpufriend，不需要hwp变频
- 修改oc boot menu参数  
- 此EFI小版本升级理论上不用更新oc和驱动，比如10.15.4升级10.15.5，之后升级10.15.5的安全更新，都正常。
### What's work
- 声卡（板载）/ 网卡（板载）
- 显卡 硬解 4K（HEVC + H.264） Videoproc硬解图和核显加速图见底部，核显最大跑到1.05
- WiFi（PCI-E 设备） / 蓝牙（PEI-E 载 USB 设备）
- 隔空投送 / 接力 / 随航
- FaceTime / iMessage
- 睿频 /原生电源管理
- 睡眠 / 键盘、鼠标唤醒  
- 感谢作者，EFI完美程度近99.999%

### 配置（不含显示器，主要配件合计3410元，不算独显2810元，挺香）
|   硬件 | 实例  | 价格 |
| ------------ | ------------ |------------ |
| CPU  | i5 9400带核显  | 1000 咸鱼 |
|  主板 |B360m迫击炮   | 500 咸鱼 |
| 显卡  | 蓝宝石Rx480 8G 满血版  | 600 咸鱼 |
| 固态  | 西数SN550 1T    | 770 咸鱼|
| 内存  | 枭鲸 16G x2 寨厂 貌似是玖和马甲 | 540 淘宝 |
| 显示器  | LG 4K Mac不上4K对不住眼睛 | 1500 咸鱼 |    

### update  in 20211120
最近发现无法睡眠，但是稳定后没升级过系统或者修改OC。第一反应是USB定制失效，重新定制，还是无效。结果pmset -g显示
```
sleep prevented by sharingd, configd, coreaudiod
```
🔍发现sharingd的确是会影响睡眠，想起近期开启过设置/共享里的网络分享开关，关闭后立即恢复睡眠。
>另，历史记录发现有sharingd，但是未开启共享、开启了handoff。  
>handoff自从某个版本--10.14.6开始一直是开启的，就是说handoff导致的sleep prevented by sharingd 应该是系统bug，后来修复了。

### update  in 2021053
- 关闭SIP，由于系统大版本不一样，键值不一样，现在大版本为11
```
在OpenCore中关闭SIP
NVRAM
增加或者修改
7C436110-AB2A-4BBB-A880-FE41995C9F82
csr-active-config 类型<Data>值： 67000000
改完成重启电脑=》重置NVRAM
```
- 检查sip状态
```
sudo csrutil status
System Integrity Protection status: unknown (Custom Configuration).

Configuration:
	Apple Internal: disabled //关键状态
	Kext Signing: disabled
	Filesystem Protections: disabled
	Debugging Restrictions: disabled
	DTrace Restrictions: disabled
	NVRAM Protections: disabled
	BaseSystem Verification: enabled

```


### update  in 20201118
*更新OC为0.6.3，10.15升级到11.0.1  更新OC注意事项*
- OC版本不同，config文件不一样，编辑时SMBIOS的三码勿忘修改
- OC版本不同，编辑器版本也不一致，不要用低版本编辑器修改高版本plist，此问题不注意再怎么修改都无法成功启动OC
- Kexts中的独有的驱动和相应的配置要更新
- Windows和Mac共用一个EFI（磁盘）的话，重置naram后，很有可能无法读取OC启动项。因为BIOS默认添加、识别Windows的boot启动项，手动删了重启电脑则又会添加，而一旦系统读取到Windows的启动项，就忽略OC的/Boot/BOOTx64.efi。
- 网上看到Nvram有记忆功能：本次启动是哪个系统，下次默认还是它。尝试办法：粗暴删除Windows的启动文件，只留OC文件，这样OC启动项就出现了，进入Mac。再用PE修复Windows的引导，注意，修复时把window引导写入另一块盘的EFI分区，这个盘可以是硬盘或者优盘，不要和Mac所在盘相同。重启电脑会发现OC也自动添加了Windows引导，而且排第一位，下面则是Mac几个启动项。
- 不建议从OC启动Windows，避免奇奇怪怪的问题，直接从BIOS启动项的Windowsboot进Windows
- 由于有两块盘的EFI，故Windows总是默认启动自己的问题可以在BIOS修改硬盘启动顺序来解决，即把oc放到第一
- B360优盘启动快捷键是F11

### update  in 20200601
- 10.15.5的睡眠唤醒重启+之前存在的handoff影响二次睡眠，此两个问题已解决：重置nvram，在win用easyuefi重新添加oc引导。奇怪的是pmset -g还是偶尔看到`sleep prevented by sharingd`。anyway，睡眠已完美。
- 升级10.15.5不需要更新oc和驱动。
- 怀疑原因：EFI文件不能随便替换，特别是clover和oc不能直接交叉替换。如果替换，需要重置nvram。同一份EFI，有的人总是有各种问题，但是格盘重新安装后就正常，应该也是同样的原因，已存nvram信息和引导不匹配。

### update  in 20200530（怀疑是nvram的锅） 
- 10.15.4升级10.15.5失败，别的没发现，但是睡眠唤醒变成重启，更新所有的驱动仍未解决，干脆回退到10.14.6。
- ~~发现10.14.6的睡眠很好，打开handoff也可以正常睡眠。~~  10.14.6打开handoff还是可能出现和10.15一样的睡眠失败情况
### update  in 20200522  睡眠bug-handoff（怀疑是nvram的锅） 
本来蓝牙直接接主板usb2.0后睡眠正常，但是偶然发现如果不小心唤醒了电脑，但是不登陆、放任不管或者在登录界面点击“取消”，那电脑不会重新睡眠，表现是屏幕黑但是主机风扇转、主板灯亮。  
pmset -g看到`sleep prevented by sharingd`，由于设置里的共享没开，那就只有handoff了，关闭后问题解决。

### update  in 20200519 睡眠bug-蓝牙内建
- 问题阐述：usb定制后发现睡眠时机箱和CPU散热风扇依旧转、主板灯亮，且显卡会不定时狂转几秒后停止，此问题无论是否开启小憩都会存在，而有人反映要开启小憩才能睡眠。
- 解决过程：1.`pmset -g assertions`和`log show --last 1h | grep -Ei "Wake Reason"`查看睡眠原因，pmset显示有蓝牙和parsec-fbf，后者显示`kernel: (AppleACPIPlatform) AppleACPIPlatformPower Wake reason: ?`，由于相信已经内建usb蓝牙，于是精力放在parsec-fbf和 Wake reason: ?上，未果。远偶然在远景看到一篇讲usb一分二导致不能睡眠的帖子，got it。
- **原因：蓝牙接在usb一分二hub上，尽管一分二已经内建。而蓝牙接在主板usb接口，睡眠（手动/自动）马上正常。不过，不开小憩的话，多次睡眠唤醒循环后自动睡眠偶尔会失败（屏幕黑但是主机风扇转、主板灯亮），开启小憩后还没遇到自动睡眠失败。10.15开启小憩注意会有RTC唤醒，要开启禁止RTC唤醒补丁**      
为什么用一分二usb？因为MSI B360M迫击炮只有一个9针usb2.0接口，而机箱面板有usb2.0，94360CD无线网卡的蓝牙也要接usb2.0。    
>睡眠即醒很大程度上跟USB的定制相关，一般一个好的USB定制就能解决睡眠即醒的问题。当然系统的更新，MacOS的也在做不断的调整，比如蓝牙不能在HUB下进行内建，比如雷电卡必须将4个端口全部内建才行，等等。甚至有些时候我们都不知道为什么黑果会睡不着，那有没有一个办法让黑果强制睡眠呢？答案是有的。经过我的摸索，有几种方法能达到强制睡眠的效果，只是方法不同而已，但主要围绕的还是0d/6d的数值来做一些工作。       
[睡眠扩展链接-3.10 睡眠即醒的相关问题](https://blog.xjn819.com/?p=543 "睡眠扩展链接-3.10 睡眠即醒的相关问题")  

![硬解](https://pic1awn.oss-cn-shanghai.aliyuncs.com/img/%E6%88%AA%E5%B1%8F2020-05-15%2017.43.33.png)  
![核显加速，最大到1.05](https://github.com/c1awn/hackintosh-b360m-9400-EFI/blob/master/Images/IGPU.png?raw=true)  
