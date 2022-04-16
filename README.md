准备：（为了找到手机开机图片的偏移量）
	方法一：
	# adb shell
	
	~ # find / -name splash
	find: /sys/kernel/slab/L2TP/IPv6: No such file or directory
	find: /sys/kernel/slab/L2TP/IP: No such file or directory
	find: /proc/6664: No such file or directory
	/dev/block/by-name/splash
	/dev/block/platform/soc/c0c4000.sdhci/by-name/splash
	
	~ # dd if=/dev/block/by-name/splash of=/sdcard/splash.img
	131072+0 records in
	131072+0 records out
	67108864 bytes (64.0MB) copied, 0.765826 seconds, 83.6MB/s
	
	~ # ls /sdcard/splash.img
	/sdcard/splash.img
	
	~ # exit
	
	root@ubuntu:/mnt/hgfs/Ubuntu/开机第一屏# adb pull /sdcard/splash.img .
	/sdcard/splash.img: 1 file pulled. 25.4 MB/s (67108864 bytes in 2.520s)
	
	root@ubuntu:/mnt/hgfs/Ubuntu/开机第一屏# binwalk splash.img
	DECIMAL       HEXADECIMAL     DESCRIPTION
	--------------------------------------------------------------------------------
	16384         0x4000          PC bitmap, Windows 3.x format,, 1080 x 2340 x 24
	3414178       0x3418A2        LANCOM firmware header, model: ">H=<F;9D;7D=6C@9CC<CC<CC<CB;BB;BB;BC<CC<CD=DE>EE>EE>ED=DD=DD=DD=DD=DE>EE>EE>EE>EE>EE>EE>EE>EE?DE?DE?DE?DD>CD>CD>CC=BB<AB<AA;@A;@", firmware version: "B@J?", RC68, build 76 ("IQEGPDEN")
	3456283       0x34BD1B        LANCOM firmware loader, model: "INFGLECHC?DA=CA=DB>DC<EA9C@8BA:BC<CC<CC<CB;BB;BB;BB;BB;BB;BC<CC<CC<CE>EF?FF?FF?FG@GG@GG@GHAHHAHIBIJCJJCJJCJHAGF@EE?DE?DD>CC=BC=B", loader version: "HJNH",
	4414334       0x435B7E        LANCOM firmware header, model: "JQEKQFKMFGIFDJFEJFEJFEJFEJFEJFEJFEJFEJFEJFEIEDHDCGCBHDCIECIECIEDIEDIEDIECIECHDCHCCHCBGD@FC=D@;B?:B?:C@;DA<DA=C@<B?;DA=GCBIDEICEH", firmware version: "CIOC", RC71, build 79 ("NTHNSGMQ")
	7598040       0x73EFD8        PC bitmap, Windows 3.x format,, 1080 x 2340 x 24
	15179694      0xE79FAE        PC bitmap, Windows 3.x format,, 1080 x 2340 x 24
	22761348      0x15B4F84       PC bitmap, Windows 3.x format,, 1080 x 2340 x 24
	34449584      0x20DA8B0       Boot section Start 0x10102121 End 0x10
	34449766      0x20DA966       Boot section Start 0x10212121 End 0x1010
	34449770      0x20DA96A       Boot section Start 0x1010 End 0x0
	34449940      0x20DAA14       Boot section Start 0x21424242 End 0x2121
	34449944      0x20DAA18       Boot section Start 0x2121 End 0x0
	34450050      0x20DAA82       Boot section Start 0x42424242 End 0x212121
	34450054      0x20DAA86       Boot section Start 0x212121 End 0x0
	34450058      0x20DAA8A       Boot section Start 0x0 End 0x0
	34517441      0x20EB1C1       Boot section Start 0x21214242 End 0x10101021
	34517445      0x20EB1C5       Boot section Start 0x10101021 End 0x0
	找到所有的“PC bitmap, Windows 3.x format,, 1080 x 2340 x 24”
	他们分别对应的“HEXADECIMAL”值（图片的开始位置分别是0x4000、0x73EFD8、0xE79FAE、0x15B4F84），将他们分别赋值给genlogo.py中的offset1~4
	
	方法二：
	下载你手机的刷机包，在里面找到splash.img
    
文件详解：
    01.bmp：开机第一屏图片
    02.bmp：系统损坏显示图
    03.bmp：fastboot界面图
    04.bmp：解bl锁后的开机第一屏
    genlogo.py：运行此脚本可以生成logo_new.img
    
使用方法：
    1.准备好上述四张图片,图片格式为 ".bmp",和脚本genlogo.py放在同一个目录
    2.执行命令 "python genlogo.py" ,将会生成 "logo_new.img"
    3.刷入系统 "fastboot flash splash logo_new.img"
    
提示：不同的手机也许有不同的文件名称、位置、生成方法。以上内容仅在红米 Note 7上进行过测试。
