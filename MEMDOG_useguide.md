	#MEMDOG的安装使用
##MEI错误注入工具
１．克隆仓库

		git clone:https://github.com/wangxiaoq/MEI.git
２．进入MEI目录
		
        cd MEI
３．编译

		make
４．安装

		make install
５．进入tools目录

		cd tools
６．编译

		make
７．生成内存错误
		
        ./gen-single-inject-file.py  5
两种错误生成方式：单比特gen-single-inject-file.py/多比特gen-multi-inject-file.py
数字代表生成错误数

８．查看注入错误

		注入内存错误
        sudo ./memerr-inject ./single-inject-file.5cd
        sudo cat /sys/kernel/debug/MEI/inject_errors
        
９．删除注入的错误

		sudo ./del-inject 180458027（数字代表内存错误的物理地址）
        
##MEMDOG内存检测器
１．下载4.4版本的内核
进入kernel.org
		
        https://www.kernel.org/pub/linux/kernel/v4.x/
找到linux-4.4.tar.xz并下载

２．解压
		
        tar -xvf linux-4.4.tar.xz
３．获取safe-mem

４．打补丁（到底哪一个？）
		
        cd linux-4.4
        patch -p1 < ../safe-mem/safemem-migratetype.4.4.patch
５．编译内核和内核模块

		make menuconfig（开启safemem，保存后退出）
        make (-j8)
        sudo make modules_install
        sudo make install
６．重启，选择linux4.4内核版本

７．安装MEI
		出现报错（read_byte...）执行
		
        cp Module.symvers ../safe-mem/module/
８．注入错误
参照前文的内容

		cd safe-mem/modules
        make 
        make install

９．开启MEMDOG
		
        sudo su
		cd /sys/kernel/debug/appsafemem/
        
查看当前可选择的算法
		
        cat available_tester
当前支持的算法为linear_tester  和　linearcext_tester
选择算法

		echo linear_tester > current_tester
这里选择了线性检测算法
开启MEMDOG

		echo yes > use_appsafemem
        
１０．查看检测出来的错误
这里为了提高内存使用效率，可以开启尽量多的应用程序，便于提高检测效率

		dmesg
注意：这里检测出来的是页框地址，需要乘以4096与之前注入的内存错误地址对应。ME