# Runing EasyConnect on Linux

# 1 Download & install EasyConnect

## 1.1 Download

[EasyConnect_x64_7_6_7_3.deb](http://download.sangfor.com.cn/download/product/sslvpn/pkg/linux_767/EasyConnect_x64_7_6_7_3.deb)

## 1.2 Install

Install the package with apt:

```bash
sudo apt install EasyConnect_x64_7_6_7_3.deb
```

# 2 Trouble shooting

The following error will prompt when running EasyConnect:

```bash
(EasyConnect:282648): Pango-ERROR **: 09:38:26.461: Harfbuzz version too old (1.3.1)
```

**Reason**: pango is too new, while harfbuzz is too old.
**Solution**: Downgrade pango as a walk around.

## 2.1 Download the following packages:

[libpango-1.0-0_1.42.4-8~deb10u1_amd64.deb](https://packages.debian.org/zh-cn/buster/amd64/libpango-1.0-0)
[libpangocairo-1.0-0_1.42.4-8~deb10u1_amd64.deb](https://packages.debian.org/zh-cn/buster/amd64/libpangocairo-1.0-0)
[libpangoft2-1.0-0_1.42.4-8~deb10u1_amd64.deb](https://packages.debian.org/zh-cn/buster/amd64/libpangoft2-1.0-0)

## 2.2 Unpack the above packages

```bash
dpkg -x libpango-1.0-0_1.42.4-8~deb10u1_amd64.deb ./tmp
dpkg -x libpangocairo-1.0-0_1.42.4-8~deb10u1_amd64.deb ./tmp
dpkg -x libpangoft2-1.0-0_1.42.4-8~deb10u1_amd64.deb ./tmp
```

The library files will be located in *./tmp/usr/lib/x86_64-linux-gnu/*.

## 2.3 Copy the library files into where *EasyCoonect* is installed:

```bash
sudo cp .tmp/usr/lib/x86_64-linux-gnu/* /usr/share/sangfor/EasyConnect/
```

Check the dependencies:

```bash
ldd /usr/share/sangfor/EasyConnect/EasyConnect | grep 'pango'
```

Denpendencies:

```bash
libpangocairo-1.0.so.0 => /usr/share/sangfor/EasyConnect/./libpangocairo-1.0.so.0 (0x00007f49e1472000)
libpango-1.0.so.0 => /usr/share/sangfor/EasyConnect/./libpango-1.0.so.0 (0x00007f49e12dc000)
libpangoft2-1.0.so.0 => /usr/share/sangfor/EasyConnect/./libpangoft2-1.0.so.0 (0x00007f49df5fc000)
```

After the above steps have been finished, *EasyConnect* will be able to run.

## References

[ubuntu22.04 安装 EasyConnect 并连接到内网](https://blog.csdn.net/baiyu33/article/details/130630836)
[Ubuntu 20.04(+)安装SSL VPN(EasyConnect)](https://zhuanlan.zhihu.com/p/346325399)
[Ubuntu 20.04下EasyConnect兼容性问题临时解决方案](https://www.cnblogs.com/cocode/p/12890684.html)