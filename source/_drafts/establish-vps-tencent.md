title: 腾讯云服务器的搭建及ssh连接

tags:
 - linux

category: 服务器

date: 2017/02/03

---

## 简述
&nbsp;&nbsp;&nbsp;&nbsp;云服务器目前已经比较成熟了，对于学生腾讯云的学生计划和阿里云的云翼计划都是十分划算的东西，墙裂建议相关专业的同学开一台来用，作为ftp/云免流/博客/服务器都是极好的。就以腾讯云为例说说云服务器的搭建过程。

## 完成认证并购买服务器

### 完成身份认证和学生认证

&nbsp;&nbsp;&nbsp;&nbsp;建立腾讯云服务器首先需要完成身份的认证，登录及注册后点击[实名认证](https://console.qcloud.com/developer/auth?authType=personal) 完成身份的认证。同时，需要完成学生认证才能拿到学生优惠券。认证的链接可以在``用户中心->账户信息->认证信息``找到。

&nbsp;&nbsp;&nbsp;&nbsp;认证信息填写完成后一般在几分钟之内就可以通过。然后在[活动页面](https://www.qcloud.com/act/campus)领取优惠券就完成准备工作了。

### 购买服务器、选择套餐及完成初始设定

&nbsp;&nbsp;&nbsp;&nbsp;领取完优惠券以后就可以开始购买了，购买服务器根据需要按照指示选择即可，如果没有额外需求的话可以选择全部最低配置，包年包月，系统选择需要的系统即可（如果要搭建云免流的话建议选择CentOS 7 64位）。带宽不做调整，有需要可以适当调大一点，要保持1M以上的带宽保证有可访问的公网IP。
&nbsp;&nbsp;&nbsp;&nbsp;共有4个步骤：
    1. 选择地域与机型
    2. 选择镜像
    3. 选择存储与网络
    4. 设置信息 

&nbsp;&nbsp;&nbsp;&nbsp;需要注意的是在设置信息步骤中，建议选择密码登录方式而非密钥登录，因为这里的密钥登录有一些问题，并不能初始绑定到云服务器，而且此方式未初始root用户密码，也可能出现登录失败的情况。如果没有特殊需要的话，安全组建议选择默认安全组。

&nbsp;&nbsp;&nbsp;&nbsp;完成4个步骤以后就可以完成支付了，记得选择领取好的代金券进行支付。

## 添加ssh密钥

### 腾讯云上生成ssh密钥对的方法

&nbsp;&nbsp;&nbsp;&nbsp;腾讯云提供了一个图形界面的生成密钥的服务，进入管理中心点击SSH密钥，然后在页面中部选择创建密钥->创建新密钥对即可，下载好的私钥用于进行SSH连接。

### 使用git for windows生成ssh密钥并上传

&nbsp;&nbsp;&nbsp;&nbsp;使用cygwin/minGW/git for windows可以在windows下使用命令行工具创建SSH密钥对。生成密钥对的命令为``ssh-keygen``，其参数如下：
```shell
    usage:  ssh-keygen [-q] [-b bits] [-t dsa | ecdsa | ed25519 | rsa | rsa1]
                  [-N new_passphrase] [-C comment] [-f output_keyfile]
            ssh-keygen -p [-P old_passphrase] [-N new_passphrase] [-f keyfile]
            ssh-keygen -i [-m key_format] [-f input_keyfile]
            ssh-keygen -e [-m key_format] [-f input_keyfile]
            ssh-keygen -y [-f input_keyfile]
            ssh-keygen -c [-P passphrase] [-C comment] [-f keyfile]
            ssh-keygen -l [-v] [-E fingerprint_hash] [-f input_keyfile]
            ssh-keygen -B [-f input_keyfile]
            ssh-keygen -D pkcs11
            ssh-keygen -F hostname [-f known_hosts_file] [-l]
            ssh-keygen -H [-f known_hosts_file]
            ssh-keygen -R hostname [-f known_hosts_file]
            ssh-keygen -r hostname [-f input_keyfile] [-g]
            ssh-keygen -G output_file [-v] [-b bits] [-M memory] [-S start_point]
            ssh-keygen -T output_file -f input_file [-v] [-a rounds] [-J num_lines]
                        [-j start_line] [-K checkpt] [-W generator]
            ssh-keygen -s ca_key -I certificate_identity [-h] [-n principals]
                        [-O option] [-V validity_interval] [-z serial_number] file ...
            ssh-keygen -L [-f input_keyfile]
            ssh-keygen -A
            ssh-keygen -k -f krl_file [-u] [-s ca_public] [-z version_number]
                        file ...
            ssh-keygen -Q -f krl_file file ...
```

简单起见使用第一种格式即可，输入``ssh-keygen -t rsa -C "abc@e.com"``（abc@e.com为要输入的标签，作为标示此密钥对的一个特征，不影响加密过程），输入完成会是如下的界面：

```shell
    # Creates a new ssh key, using the provided email as a label

    # Generating public/private rsa key pair.

    # Enter file in which to save the key (/home/you/.ssh/id_rsa):

    # Enter passphrase (empty for no passphrase): [Type a passphrase]

    # Enter same passphrase again: [Type passphrase again]

    # Your identification has been saved in /home/you/.ssh/id_rsa.

    # Your public key has been saved in /home/you/.ssh/id_rsa.pub.

    # The key fingerprint is:

    # 01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db abc@e.com
```
可以直接回车到底。
一行一行解释，


有git使用需求的可以通过
```shell
    git confit --global --add user.name "abc" #abc 是你的git用户名

```

## 使用ssh连接服务器

### windows下连接

### Android下连接