# Ubuntu安装Windows字体(ttf)

## Step 1: 拷贝Windows字体

Windows字体目录：

```bash
C:\Windows\Fonts
```

拷贝需要的字体文件，比如*Times New Roman*，文件为'times.ttf'

## Step 2: 把字体文件拷贝至Ubuntu

Ubuntu字体目录:

```bash
/usr/share/fonts
```

在该目录下创建目录'winfonts'，用于存放Windows的字体：

```bash
sudo mkdir /usr/share/fonts/winfonts
```

把Windows字体文件(\*.ttf)拷贝至该目录下。

```bash
sudo cp times.ttf /usr/share/fonts/winfonts
```

## Step 3: 安装字体

进入'winfonts'目录，运行如下命令：

```bash
sudo mkfontscale
sudo mkfontdir
sudo fc-cache -fv
```

命令大意：
1. 创建字体的 fonts.scalecale 文件，用来控制字体的旋转和缩放
2. 创建字体的 fonts.dir 文件，用来控制字体粗斜体产生
3. 建立字体缓存信息，也就是让系统跟字体进行深入的交流 第一第二条好些时候可以不做
