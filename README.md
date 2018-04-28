# -centOS7

## Question:
主要是分区的时候总会提示：For a UEFI installation, you must include an EFI System Partition on a GPT-formatted disk, mounted at /boot/efi.

## Answer:
跟硬盘的 分区表格式是 GPT还是 MBP无关。当然，老硬盘可能MBP兼容性更好，但我是新买的SSD，所以没有影响。

百度结果：
[原地址](https://www.douban.com/note/583385349/)

---
昨天心血来潮准备在老爷机上装个centos7双系统，没想到折腾到了今天才弄完。
先是下的DVDiso版用不了还是用poweriso制作启动U盘的时候制作不好，反正这个4G多的DVD标准版我是用不了，于是下了个minimal版本，只有600多兆，欢喜欢喜~
其中还要看U盘的盘符，改掉运行提示的某句为linux dd quiet。我的盘符是sda1
接下来安装还蛮顺利，但是出现了For a UEFI installation, you must include an EFI System Partition on a GPT-formatted disk, mounted at /boot/efi.
在预装win10的基础上用u盘安装centos7双系统，分区时出现这个问题，如果是好几年的老机子，不支持uefi，建议将u盘的安装文件中的efi文件夹删掉后再安装。这个依据是根据CSDN中得来，大神们说得蛮清楚http://bbs.csdn.net/topics/391041119
++++++++++++++++++++++++++++++++
第一位：这是因为空闲盘终存在.efi文件，需要格式化空闲盘在安装。方法可以采用新建分区，然后再删除的方法，过程中就格式化分区了。另外一定要建立/boot/efi分区。
第二位：你是用UEFI模式进入的系统安装界面，而你的硬盘分区是MBR格式的，而非GPT格式的。
第三位：建议使用非UEFI模式安装。
第四位：开机启动选择U盘进入，不用选择UFFI哪个选项，有两个选项的。
第五位：将主板的UEFI设置为legacy 试试。
第六位：将u盘安装文件里的EFI文件夹整个删掉就好了。
因为我的自己不支持uefi，所以我就参考第六位的做法直接把安装文件的efi文件夹删掉。
++++++++++++++++++++++++++++++++
到最后一步提示重启，重启之后记得把优先从U盘启动换回原来的。这里安装的时候可以看最新版的鸟叔私房菜，都已经更新到centos7了，鸟叔真勤奋。
接下来由于是迷你版本，所以需要手动安装桌面，先安装X window system,然后再安装genome或者kde，我选择kde。接着用startx启动桌面。
接下来要安装中文支持
1、查看可用的中文安装包

''' 
#  yum list kde*chinese
'''

2、安装中文包
'''
# yum install kde-l10n-Chinese.noarch
'''
3、修改界面
在 系统设置=>语系，添加使用简体中文即可。需要重启电脑
安装中文字体可以参考这个网址http://www.centoscn.com/image-text/setup/2015/0402/5072.html
附在后面了，免得文章不见了（见附1）
以下针对kde：
安装中文输入法，用ibus挺好的，用fcitx的话，ficitx-configtool还要找源的……
安装完ibus记得安装im-chooser，运行im-chooser，选择ibus，然后右下角会出现一个小键盘（？）图标，右键点击reference，在里面选择Chinese的拼音输入法。记得这几步多重启……
然后重启后发现win10引导消失，参考这一篇http://blog.csdn.net/ergouge/article/details/49836913
神一样的文章，比其他人的方法好用多了。也附在后边了（附2）。

用了centos7+kde的感想，真是好卡……是不是我哪里没装好……

——————————————————————
附1
3.1 执行CentOS7 最小安装

去官网下载CentOS-7.0-1406-x86_64-Minimal.iso，然后刻录光盘，安装之。安装完成后执行yum update更新系统。然后，执行
'''
# yum install epel-release
'''
安装额外包yum源（extra package for Enterprise Linux）。

3.2 安装X Window system

执行yum groupinstall "X Window system"，安装X。安装完成后执行sudo systemctl isolate graphical.target检验安装成功。

3.3 安装Xfce4

执行 yum groupinstall xfce4安装Xfce4桌面环境。如果需要，可选安装xfce4的其他模块。 
执行sudo systemctl isolate graphical.target,进入Xfce。

3.4 安装中文字体和中文输入法

在CentOS6可以使用yum groupinstall "Chinese support"一次性安装，但是CentOS7下，不能这么安装，没有这个安装组，所以需要单独安装每个包。我只安装了一个楷体字体：yum install cjkuni-ukai-fonts。 
【增加字体】 
把Windows系统下C:\Windows\Fonts目录下需要的字体文件复制到CentOS的/usr/share/fonts/chinese/目录下，然后执行如下命令即可安装这些字体：
'''
cd /usr/share/fonts/chinese/ # 切换到新字体目录
mkfontscale # 在当前目录下生成fonts.scale文件
mkfontdir   # 在当前目录下生成fonts.dir文件
fc-cache -fv # 重新建立字体缓存
'''
关于输入法，有两个概念也区分。“输入法平台”和“输入法”，输入法平台是为具体的输入法提供实现接口，并与X环境实现对接。目前广泛采用的输入法平台有:fictx，scim，ibus。最新的是ibus，也是gnome的默认设置。这里我们就选择ibus平台。 
主要需要安装如下包：

ibus， 这个包里有ibus-daemon这个平台服务器程序和ibus这个配置助手。
ibus-libpinyin， 这个是ibus平台下具体的拼音输入法。
ibus-gtk2/3，这个是ibus在GTK环境下的UI，托盘显示。
im-chooser,这个是输入法平台选择助手程序。
gtk2/3-immodule-xim，这个是输入法候选字显示UI。我就是因为这个包没有安装导致始终无法显示输入法字选择窗口，为此折腾了1天！！！
执行im-chooser，选择输入法平台和输入法。然后重新登录系统。

3.5 安装firefox

执行yum install firefox，然后在Xfce中设置为默认浏览器。启动并浏览百度网站，看中文显示是否正常，看输入法是否正常。

3.6 安装开发环境

这个不同的人有不同的需要，我本人是vim+gcc。
——————————————————————————————————————
附2
网上参考的解决方法：

(应该可以吧，我还是选择了保守的方法)
'''
vim  /boot/grub2/grub.cfg
'''
在### END /etc/grub.d/00_header ###之后添加：
 '''
 ### BEGIN /etc/grub.d/30_os-prober ###
menuentry 'Windows 10 (loader) (on /dev/sda1)' --class windows --class os $menuentry_id_option 'osprober-chain-140E68540E6830C2' {
insmod part_msdos
insmod ntfs
set root='hd0,msdos1'
chainloader +1
}
 ### END /etc/grub.d/30_os-prober ###
'''
我的解决办法：

1.添加源 
>$ yum localinstall --nogpgcheck http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm
2.安装ntfs-3g(为了使centos能识别出windows所在的分区。) 
>$ yum install fuse ntfs-3g -y 
3.系统自动找到windows 8 系统与xp系统的引导项，并加入到了grub.cfg菜单中。这样就避免了手功改动文件的危险，并且所有系统都能正常启动。 
>$ grub2-mkconfig -o /boot/grub2/grub.cfg 
4.重启 
>$ reboot 
5.然后就好了，出现了win8的启动引导提示（不知道为什么是win8，难道win8和win10底层标识是一样的？）
