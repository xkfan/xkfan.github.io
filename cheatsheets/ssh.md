# SSH


## 1 免密登录

### 1.1 客户端生成公私钥

本地客户端生成公私钥(一路回车默认即可)

```bash
ssh-keygen
```

"ssh-keygen"会在用户目录下创建两个秘钥文件:

```bash
ls ~/.ssh
```

```
id_rsa (私钥)
id_rsa.pub (公钥)
```

### 1.2 上传公钥到服务器

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub username@hostmachine
```

## 2 登录选项

###

```bash 2.1 防止超时
ssh -o serveraliveinterval=60 username@host
```
