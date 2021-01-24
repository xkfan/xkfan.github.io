# win7+ubuntu双系统恢复ubuntu引导
win7+ubuntu双系统重装win7后，开机启动界面的ubuntu引导不见了，恢复ubuntu引导的方法：

1. 准备ubuntu启动u盘，参考[制作usb启动盘](https://blog.csdn.net/u013553529/article/details/78307520)
2. 进入BIOS，在开机启动中设置为u盘启动为最高优先级
3. 插入u盘，启动机器
4. 进入ubuntu的安装界面，选择“试用ubuntu”
5. 进入ubuntu系统后，打开终端（快捷键Ctrl+Alt+T）
6. 在终端输入“sudo -i”，获取管理员权限
7. 在终端输入“fdisk -l”，查看盘符列表，得到类似以下信息：
```
   Device      Boot    Start   End    Sectors     Size    Id   Type
   /dev/sda1    *        xx     xx         xx       xx     7   HPFS/NTFS/exFAT
   /dev/sda2             xx     xx         xx       xx     7   HPFS/NTFS/exFAT
   /dev/sda3             xx     xx         xx       xx     7   HPFS/NTFS/exFAT
   /dev/sda4             xx     xx         xx       xx     5   Extended
   /dev/sda5             xx     xx         xx       xx    83   Linux
   /dev/sda6             xx     xx         xx       xx    83   Linux
```
8. 找到Id为83的盘符，即ubuntu系统所安装的分区/dev/sda5 （ubuntu系统安装在/dev/sda5，home目录安装在/dev/sda6）
9. 在终端输入“mount /dev/sda5 /mnt”，挂载ubuntu所在的分区
10. 在终端输入“grub-install --root-directory=/mnt /dev/sda”,若出现“Installation finished, No Error Reported”则表示成功恢复ubuntu引导
11. (optional)重启电脑，进入ubuntu系统，打开终端，输入“sudo update-grub”更新引导
