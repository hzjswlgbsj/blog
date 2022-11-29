# 使用七牛云来做 typora 的图床

我算是 typora 的重度用户，但是我又需要将本地的一些文档或者博客文章发布到在线的博客。里面的图片是本地的，我每次都要自己手动再上传一次，这很不方便。最近突然发现七牛云有命令行工具 [qshell](https://github.com/qiniu/qshell)，而 `typora` 好像可以自己配置图片上传，我看了一下有一种方法就是 `命令` ，这下完美了。

### 1. 配置 qshell

首先在 [qshell](https://github.com/qiniu/qshell) 官网下载对应操作系统的包，我以 MacOs 为例。下载完了后解压直接得到一个可执行文件，将它复制到 `usr/local/bin` ，然后修改文件执行权限 `chmod +x qshell` 。

然后配置账户

```bash
qshell account <Your AccessKey> <Your SecretKey> <Your Name>
```

其中 `AccessKey` 和 `SecretKey` 是你的七牛云后台密钥管理面板里面对应的 `AK` 和 `SK` ，Name 就是你的七牛云账号，我的就是我的邮箱。然后按照如下示例进行测试：

上传本地文件`/Users/jemy/Documents/qiniu.mp4`到空间`if-pbl`里面。

```bash
 qshell rput if-pbl qiniu.mp4 /Users/jemy/Documents/qiniu.mp4
```

### 3. 写命令脚本

在 `/usr/local/bin` 文件夹 新建 `uploadToQiniu.sh` ,然后加入如下代码（注意修改配置）

```bash
#!/bin/bash
    set -e
    # 空间名称
    BUCKET="xxx"
    # qshell 的路径
    QSHELL="/usr/local/bin/qshell"
    # 你的域名
    DOMAIN="https://xxx.xxx.com"

    i=0
    for filepath in $@; do
      i=$((${i}+1))
      # 获取文件名， 并在文件名前面加当前时间做最后的文件名
      filename="$(date +'%Y%m%d%H%M%S')_${filepath##*/}"
      if [ -f "${filepath}" ]; then
        # 文件在就上传
        ${QSHELL} rput ${BUCKET} "${filename}" "${filepath}"
        if [ $? -eq 0 ]; then
          if [ ${i} -eq 1 ]; then
            echo "Upload Success:"
          fi

          echo "${DOMAIN}${PATH_PREFIX}/${filename}"
        else
          echo "upload ${filepath} failed!"
          exit 1;
       fi

     else
       echo "${filepath} does not exist"
       exit 1;
     fi
   done
```

以上代码中 shell 相关知识点：

```bash
$# 是传给脚本的参数个数
$0 是脚本本身的名字
$1 是传递给该shell脚本的第一个参数
$2 是传递给该shell脚本的第二个参数
$@ 是传给脚本的所有参数的列表

shell中使用符号 $? 来显示上一条命令执行的返回值，如果为0则代表执行成功，其他表示失败。

${filepath##*/} 提取文件名
${filepath##*.} 或 ${filepath#*.} 提取文件后缀
${filepath%/*} 提取目录
```

### 4. 修改 typora 的图片配置

按照如下选择配置

![](https://lib.sixtyden.com/20210331094143_typora%E5%9B%BE%E7%89%87%E9%85%8D%E7%BD%AE.jpg)

然后就可以点击左侧的 `验证图片上传选项` 了，会出现明明配置是正确的但是还是提示错误。你把他的错误信息复制出来，查看里面的图片地址是不是正确的，如果是能访问的就不用管他这个提示了。

### 相关连接

1. [七牛云密钥面板](https://portal.qiniu.com/user/key)
2. [qshell 分片上传文档](https://github.com/qiniu/qshell/blob/master/docs/rput.md)

（完）
