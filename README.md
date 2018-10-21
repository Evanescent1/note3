# note3

&ensp;&ensp;&ensp;&ensp;把库元素移动到root下
mv  /lib64/libc.so.6    /root/
通过救援模式进行修复  重置  ----快速的按esc键（生产中有可能按f1)---按光盘来引导---排错（troublohooting)---resue a centos system---进入页面后选1表示继续continue------之后会进入页面，但是它的根是光盘路径的根，前面一定要加上光盘的根把root下的文件移动回去
mv   /mnt/sysimage/root/libc.so.6    /mnt/sysimage/lib64/   按exit退出即可   mnt/sysimage  就表示根
救援模式就是把硬盘里的内容当成数据，因为系统已经出故障了，只能用光盘做个简化版的系统，执行一些简单的命令操作他们


rpm -ivh --test  包     测试安装
replacepkgs覆盖包    用rm命令删除包在rpm数据库里不认为是卸载可以用命令replacepkgs覆盖安装
replacefiles  覆盖文件  当两个包里的文件冲突的时候，用命令replacefiles覆盖 已存在的文件
每个数据包都有签名，命令nosignature不检查包的签名
每个文件的属性都有规定，命令nodigest可以不检查默认是检查的
包里的脚本都是自动运行       rpm -q   --scripts  检查包里是否带脚本
把一个包的文件删除怎么恢复    reset  重置
rpm -qf  /usr/bin/tree   找到包
cp   tree-1.6.0-10.e17.x86_64   /data   把包拷贝到data目录下
rpm2cpio      tree-1.6.0-10.e17.x86_64    把包转换成cpio流
rpm2cpio      tree-1.6.0-10.e17.x86_64  |cpio  -tv    看包里的文件
rpm2cpio      tree-1.6.0-10.e17.x86_64  |cpio   -id  ./usr/bin/tree把丢失文件解到data目录下
mv  usr/bin/tree  /usr/bin/   移动回去
这个方法有可能文件属性错误，需要验证
rpm -qpl   包文件完整路径   p可以在包没有安装前，rpm数据库还没有更新的时候看包里的内容
rpm -q  --whatprovides  bash  表示这个关键字有哪个包提供
 rpm -q  --whatrequires  bash  表示这个能力被那些包依赖的
 rpm -K  包文件   检查包的签名是否被篡改    直接查询包不行得导入公钥才行
rpm --import /run/media/root/CentOS\ 7\ x86_64/RPM-GPG-KEY-CentOS-7安装密钥
RPM-GPG-KEY-CentOS-7代表公钥
rpm -qa "gpg-pubkey*"  可以查看密钥  
rpm -qi gpg-pubkey-f4a80eb5-53a7ff4b可以查看密钥的内容
rpm -e gpg-pubkey-f4a80eb5-53a7ff4b卸载密钥（公钥）
需要在客户端/etc/yum.repos.d/目录下配置一个文件
2在/etc/yum.repos.d/创建一个文件文件vim  base.repo 文件
1[base]中括号给仓库起名
2baseurl=file:///run/media/root/CentOS\ 7\ x86_64/(写仓库的路径）因为是本地文件夹写file://
3#gpgcheck=0    yum会检查包是否合格  0表示不检查（绕过检查）忽略密钥的检查默认是1
3gpgkey=file:///run/media/root/CentOS\ 7\ x86_64/RPM-GPG-KEY-CentOS-7写上密钥路径，让系统自己检查           yum repolist查询配置文件是否装好
4客户端的配置就写好了，因为服务器写的是本机。仓库有。所以直接用命令yun install  包名  就直接安装       卸载包用yum   remove包名
MBR硬盘的第一个扇区是0扇区存放了划分分区表的信息（512字节）
0扇区分了446字节引导计算机系统的    64个字节分成了四个部分16个字节为一部分，每一个部分存放一个分区的信息    最后两个字节表示整个硬盘分区的结束
每部分的16个字节分成16份，第一个字节是8位，它表现为80的时候为活动分区  00表示此分区为非活动分区  n表示只查看硬盘的第512位字节        第二个字节是磁头数后续的两个字节柱面数      
文件系统就把我们的数据以文件的方式进行组织，只有创建了文件系统，才有文件的概念
日志是我们把数据的操作过程记录下来
如果没有日志，在修改文件的时候可能造成文件的丢失（例如停电）
第一步tar xf httpd-2.4.25.tar.bz2 下载包解包  进入当前目录下 httpd-2.4.25 
./configure  --prefix(指定安装目录）=/app/httpd    --sysconfdir(配置文件目录）=/etc/httpd24        启用功能直接加上  --enable-ssl(加密功能）开始安装 
安装过程中会缺各种包，用yum依次安装  包后面加上-devel
安装完成后用echo $?确认一下是否成功        du -sh 看文件大小
第二步 make   生成二进制程序     命令find  -perm  /111  搜索可执行文件
find -name 'httpd'  搜索文件名
第三步make install       设置PATH    echo 'PATH=/app/httpd/bin:$PATH' > /etc/profile.d/httpd.sh用单引号，如果双引号会把PATH解开    一定要放在前面，让系统先用它，优先级高
 开启  apachectl  start   运行    显示的网页页面在  /app/httpd/htdocs  在这个文件里可以修改网页显示的内容   ps aux看系统进程
如果是自己指定的路径，需要把man帮助给系统加进去，修改man帮助的配置文件，  vim/etc/man_db.conf        把man帮助的路径加进去
