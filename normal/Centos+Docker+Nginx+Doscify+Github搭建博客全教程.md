# Centos+Docker+Nginx+Doscify+Github 搭建博客全教程

以前我是自己用 vue 写了一个完整的博客，整个花里胡哨的，还要自己维护数据库，一旦自己维护了就有迁移成本，还有备份等痛点。博客应该注重内容的书写而不是整一堆毫无用处的页面，已经难以维护的项目。

因此我打算直接使用 docker 来管理部署，迁移成本极低，只需要一个 dockerfile 就能搞定，而 github 就是最好的版本管理工具和备份工具，书写 `markdown`更专注与内容，至于 `.md`解析成静态文件我选择了 `docsify`还有很多选项，比如 `Vuepress`、`Hexo`等等，无所谓的。

结合 github 的 hooks 我们还能实现自动更新，岂不是方便快捷！

## 准备

- 一台 centos 服务器
- 一个 github 账号
- 一个 github blog 项目
- 保证有 node 环境
- ssl 证书（可选）
- 备案成功的域名（可选）

## 服务器

可以去购买云服务器，我是搞了一个 3 年的腾讯云服务器，双十一活动贼爽，你可以搞个学生账号。自己先配置好 ssh 免密登录，不会的可以参考我写的 [教程](https://www.yuque.com/sixty/eg6zk0/clydnv3c7n32q6ra) ，配置好后建议使用 vscode 安装 `remote ssh`这个插件，他可以让你直接在 vscode 里面修改服务器文件，真的很方便，你不用再命令行各种 vim。

## Docker

先给出 [centos 的官方文档](https://docs.docker.com/engine/install/centos/) 供你参考，我建议你跟着我的来。

1. 先更新 yum 包

```bash
yum -y update
```

> 注意
> `yum -y update`：升级所有包同时也升级软件和系统内核；
> `yum -y upgrade`：只升级所有包，不升级软件和系统内核

2. 如果之前安装过，执行卸载

```bash
yum remove docker docker-common docker-selinux docker-engine
```

3. 查看可安装版本列表

```bash
yum list docker-ce --showduplicates | sort -r
```

4. 选择最新版本安装

```bash
yum -y install docker-ce-18.06.0.ce
```

5. 启动 docker

```bash
sudo systemctl start docker
```

6. 使用 hello world 来验证

```bash
sudo docker run hello-world
```

这个操作是从 [docker hub](https://hub.docker.com/u/library) 里面拉取 hello-world 镜像，完成后可以查看当前的镜像列表

```bash
docker image ls
```

可以看到有 hello-world 这个镜像，运行它

```bash
docker container run hello-world
```

如果运行成功了，你可以在控制台看到如下输出

```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

到这里 docker 就已经安装完成，如果还想进一步了解，可以自行查文档，关于 dockfile 这边先不做介绍，如果需要制作自己的 docker 镜像的话可以看看参考中的 [docker 入门教程](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)。

用 docker 部署 Docsify 将在 Nginx 章节介绍

## 安装 Docsify

安装 Docsify 非常简单，你完全可以直接安装官方文档的 [快速开始](https://docsify.js.org/#/zh-cn/quickstart) 来完成，如果你懒得打开，影响你连贯性阅读和操作的话，你可以继续跟着下文操作

### 安装脚手架工具

全局安装 docsify-cli 工具，可以方便地创建及在本地预览生成的文档。

```bash
npm i docsify-cli -g
```

### 初始化项目

你先在你的服务器上建好存放博客文章的文件夹，我是放在了`/www/blog`，你可以参考，后面也是用这个目录来演示

```bash
docsify init /www/blog
```

### 写测试文章

初始化成功后，可以看到`/www/blog`目录下创建的几个文件

- `index.html`入口文件
- `README.md` 会做为主页内容渲染
- `.nojekyll`用于阻止 GitHub Pages 忽略掉下划线开头的文件

### 预览

通过 `docsify serve`可以启动一个本地服务器，可以方便地实时预览效果。

```bash
docsify serve docs
```

成功后会提示

```bash
Serving /www/blog now.
Listening at http://localhost:39475
```

**注意**：你如果现在是在云服务器上直接操作的话是没有 localhost 的，需要换成你的 ip，`39475`是端口，默认是 3000，如果被占用他会自动开一个端口。

如果你能访问成功那么安装过程就完成了，用 docker 部署 Docsify 将在 Nginx 章节介绍。

> 对默认的 Docsify 进行改造，布局、自定义等操作我在 [定制自己心仪的 Docsify](定制自己心仪的 Docsify) 中详细介绍

## 配置 Nginx

1. 拉取一下最新的 Nginx

```bash
docker pull nginx:latest
```

2. 准备工作

需要有一个地方来保存 Nginx 配置文件。

特别提醒：如果你的服务器上已经安装了 Nginx，我建议你要停掉

```bash
nginx -s stop
```

> 注意，此操作会影响到你其他部署在这台服务器上的项目

docker 里面的 Nginx 有可能会跟本机上的 Nginx 产生问题，比如我明明已经 stop 了 docker 的容器了，但是它里面的 Nginx 配置文件依然是生效的，部署出来的网站也依然是在线的，我把服务器本省的 Nginx 服务停掉，再做一遍 docker 启动关闭才正常。

如果你服务器上有其他服务需要 Nginx 的话，我建议:

- 要么其他服务也容器化
- 要么放弃使用 docker 来维护你的 Docsify 文档部署

接下来继续下面的流程

其实只需要执行如下命令即可在开启一个带有 Nginx 的 docker 容器

```bash
docker container run \
-d \
-p 127.0.0.2:8080:80 \
--rm \
--name mynginx \
--volume /www/blog:/usr/share/nginx/html \
nginx
```

逐行解释

- 第一行：`docker container run \` 表示开启一个容器，接着后面的都是参数了
- 第二行：`-d \` 表示一直在后台运行容器
- 第三行：`-p 127.0.0.2:8080:80` 对端口进行映射，将本地 `8080`端口映射到容器的 `80`端口
- 第四行：`--rm \` 表示本次运行是一次性的，运行后就会被删除
- 第五行：`--name mynginx \` 本次运行的容器的名称，使用 `docker container ls` 或 `docker container ps` 可以看到运行的容器列表
- 第六行：`--volume /www/blog:/usr/share/nginx/html \`，`--volume`用于挂载本机目录到容器目录（新版本可以直接使用-v），本句命令指的是将本机的 `/www/blog` 命名卷挂载到容器的的 `/usr/share/nginx/html`位置，访问 `/usr/share/nginx/html` 会自动去访问本机的 `/www/blog`。
- 第七行：`nginx`表示启动一个 Nginx 服务，

运行完上面的命令后你在浏览器访问: `http:localhost:8080`（如果你是在服务器上运行的将 localhost 换成你的 ip）就可以看到 Nginx 的首页。

现在我们来拷贝一份 Nginx 的配置文件进行修改（注意：此时保持上面的 mynginx 容器处于运行，拷贝完后可以停止）。我是在 `/www/blog`目录下运行，也方便管理，这个 Nginx 只用于博客镜像的使用不污染服务器本身。

```bash
docker container cp mynginx:/etc/nginx .
```

上面命令的含义是，把 `mynginx` 容器的 `/etc/nginx` 拷贝到当前目录，**不要漏掉最后那个点**。

执行完成后，当前目录应该多出一个 `nginx` 子目录。然后，把这个子目录改名为 `conf`。

```bash
mv nginx conf
```

现在可以把容器终止了。

```bash
docker container stop mynginx
```

OK！现在我们来映射 Docsify 的目录

```bash
docker container run \
-d \
-p 8080:80 \
--name mynginx \
-v /www/blog:/usr/share/nginx/html \
nginx
```

我们前面已经用 Docsify 初始化到了 `/www/blog` 目录了，这时候目录下已经有 `index.html` 了，所以执行完上面的命令后你访问 `http:localhost:8080` 应该可以看到 Docsify 的首页了。

趁热打铁，我们在 Nginx 配置文件中加入博客的配置，这里是一份我的配置文件，我在里面加了详细注释应该不需要过多解释。

我早就申请过域名和 ssl 证书了，我就直接配置 https 和域名一步到位了，如果你没有域名可以去各个平台申请， ssl 证书在腾讯云和阿里云上都可以申请和购买，当然你也可以直接搞自签名证书，非常简单你可以参考[DigitalOcean 的教程](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-on-centos-7)，确保你机器上去你安装了 [OpenSSL](https://www.openssl.org/source/)。

```nginx
# www.sixtyden.com （欢迎访问，也欢迎给我的github blog（https://github.com/hzjswlgbsj/blog）来个start）
server {
  # 监听 443
  listen 443 ssl;
  # 域名
  server_name www.sixtyden.com;
  # 开启 ssl
  ssl on;
	# 证书地址，这里要注意应该写容器上的地址而不是本机！！！
  ssl_certificate   /etc/nginx/WwwSixtydenCom.pem;
  # 证书密钥地址，这里要注意应该写容器上的地址而不是本机！！！
  ssl_certificate_key  /etc/nginx/WwwSixtydenCom.key;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
  ssl_prefer_server_ciphers on;
  # 这里先只配置了根目录，如果有静态文件或者其他需要可以增加 location
  location / {
    index index.html index.htm;
    # 这里要注意应该写容器上的地址而不是本机！！！
    root /usr/share/nginx/html;
  }
}
```

> 特别提醒: nginx 的 server 模块是要放到 http 模块下的！也就是说上面的配置应该复制到配置文件的 http 模块里面。

来到最后一步，修改容器启动命令

```bash
docker container run \
-d \
-p 80:80 \
-p 443:443 \
--name mynginx \
-v /www/blog:/usr/share/nginx/html \
-v /www/blog/conf/nginx.conf:/etc/nginx/nginx.conf:ro \
-v /etc/nginx/ssl/sixtyden.com/WwwSixtydenCom.pem:/etc/nginx/WwwSixtydenCom.pem \
-v /etc/nginx/ssl/sixtyden.com/WwwSixtydenCom.key:/etc/nginx/WwwSixtydenCom.key \
nginx
```

这里映射了两个端口 `80` 和 `443`, `80` 端口是默认访问端口这样用户直接访问域名就可以访问到博客的主页了，`443`端口用于 `https` 服务.

你可以看端口到这里的写法有点变化之前的写法是这样：`-p 127.0.0.2:8080:80 \`，其实是一样的哦， `-p 80:80 \` 指的是映射容器的 `80` 端口到本机的 `80`端口，省略 ip 会默认为当前服务的 ip。

注意看那几个 `-v` ,第一个不用说是挂载我们的博客根目录到容器，第二个是挂载 Nginx 的配置文件，而第三个和第四个则是挂载 ssl 的密钥地址，还记得在 Nginx 配置文件中我们写的就是被挂载的对应的容器的地址。

执行完这最后的命令你就可以访问你的域名或者 ip 了。

我知道，怪事可能发生了，你执行完上面的代码后没有任何报错，但是就是访问域名毛都没有，这有很多可能，我提供一下检查思路。

首先你要看一下这个容器是否真的启动了

```bash
docker container ps
# 或者
docker container ls
```

你应该在列表中看到启动的容器，比如这里就是 `mynginx`

如果能看到，那你检查一下应该是端口问题，如果不是端口问题，你可以将上面的命令尝试一行一行的删除来排除，比如执行前端最简单的命令，然后慢慢增加，比如先增加端口看有没有问题，没有问题了再增加 Nginx 配置

我在 Nginx 的配置这一步是出问题的，好好检查一下挂载目录

如果还是找不到问题，最后给你杀手锏，看日志

```bash
docker logs mynginx
```

## 配置 Github

网上有很多种方式来利用 Github 做博客，最多的就是开启 GitHub Pages，都无须自己部署，买域名，但是访问个构建速度都很慢，所以有人用 [vercel](https://vercel.com/)，vercel 是一个出色的静态网站构建工具，它在全球都拥有 CDN 节点，因此比 Github 官方自带的 github pages 更加稳定，访问速度更快。

而我想利用 Github 的 [webhook](https://docs.github.com/cn/developers/webhooks-and-events/webhooks/about-webhooks) 进行自动更新，GitHub 当然为我们准备了钩子让我们能利用它做更多事情。

Webhook 允许生成或设置集成，例如 [GitHub Apps](https://docs.github.com/cn/developers/apps/building-github-apps) 或 [OAuth Apps](https://docs.github.com/cn/developers/apps/building-oauth-apps)，以订阅 GitHub.com 上的某些事件。 当触发其中某个事件时，我们将向 web 挂钩的配置 URL 发送 HTTP POST 有效负载。 Web 钩子可用于更新外部议题跟踪器、触发 CI 构建、更新备份镜像，甚至部署到生产服务器。

我其实只做了一件最简单的事情，就是 `git pull`，我希望当我向 blog 项目 push 更新的时候，我的服务器能自动帮我拉取代码到我的 `/www/blog` 目录

要做到这件事，需要准备：

- 在服务器上配置 git 账户，确保能正常拉代码
- Webhook 需要回调一个脚本，我这里使用 node 结合 pm2 快速就能跑一个 node 服务
- 在 GitHub 仓库的 Settings - webhooks 设置中创建一个新的 webhook

### 配置 Git 账户

先看看是否已经安装了 Git

```bash
git --version
```

如果没有安装，执行

```bash
yum install git
```

配置帐户和密码

```bash
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

然后再看看账户列表

```bash
git config --list
```

配置 SSH 用于拉取远程仓库

```bash
ssh-keygen -t rsa -C "your_email@youremail.com"
```

查看一下

```bash
cat /root/.ssh/id_rsa.pub
```

配置 GitHub
在你的 Github 右上角头像下拉中打开 `Settings` 找到 `SSH and GPG keys` 然后在 `SSH keys` 点击 `New SSH keys` 将刚才的 `id_rsa.pub` 内容粘贴到 `key` 输入框中点击确定。

现在你可以在 `/www/blog` 目录中拉取博客项目的代码。

### 配置 Webhook

在 GitHub 中找到你的博客项目，然后点击 `Settings`，在右侧找到 `Webhook`，添加你的 webhook，如图所示：
![image.png](https://lib.sixtyden.com/webhook.png)
Payload URL 填写 `http://your_ip:8888`，我使用了 `8888` 端口，Content type 选择 application/json，这个 Secret 一定要填写，为了安全起见，也请你阅读一下文档，GitHub 会根据这个字符串加密，然后在返回头信息里面返回一个 `x-hub-signature`（现在建议使用 x-hub-signature-256 了）。在后面的代码里面我们要验证这个。

此时 GitHub 会提示你填写的这个回调地址是错误的，不用管他，毕竟代码还没写， node 服务也还没起来。

OK，准备工作就做好了，接下来开始写拉代码的 node 小代码。

### 创建 Webhook 项目

这里使用 node 来说做 GitHub 的 webhook 回调服务。
创建 `webhook`文件夹用来放 供 Github webhook 回调的 node 服务，这个 `webhook`文件夹其实随便你放到哪里，反正能启动起来就行，我这里依然放到了 `/www/blog` 目录，方便管理。

启动 node 服务的方式有很多，你甚至可以直接 `node /www/blog/webhook/app.js` 但是这也太原始了吧，我采用 [pm2](https://pm2.keymetrics.io/docs/usage/quick-start/)，来管理，因为我再服务器上同时开了很多个 node 服务，如果你没有安装 PM2 请提前装好，这个很简单照着官网做就行了。

接下来编写 webhook ，先看一下 `/www/blog` 的文件结构吧

```bash
- blog          // github 上的blog仓库保存地址
  - ...         // 文章
- conf          // Nginx 配置文件夹
	- nginx.conf  // docker 中的 Nginx 配置文件
  - ...
- webhook       // webhook 相关代码
  - app.js      // 入口
	- server.js   // webhook 捕获后的处理代码
	- index.html  // webhook 的node服务自动成功提示页（可删除）
- Dockerfile
- index.html    // 目前Docsify默认生成的首页
- README.md     // readme
```

#### app.js

```javascript
var server = require("./server");

server.start();
```

#### server.js

```javascript
const secret = "46a5dc72550e02ce2743bb7061e4b33bc195c541";
const repo = "/www/blog/blog";

const http = require("http");
const crypto = require("crypto");
const exec = require("child_process").exec;
const fs = require("fs");

exports.start = function () {
  http
    .createServer(function (request, response) {
      // 脚本
      request.on("data", function (chunk) {
        console.log("成功得到github回调响应", chunk);
        let sig =
          "sha1=" +
          crypto
            .createHmac("sha1", secret)
            .update(chunk.toString())
            .digest("hex");
        if (request.headers["x-hub-signature"] == sig) {
          exec("cd " + repo + " && git pull");
        }
        response.end();
      });

      // 显示成功页面
      fs.readFile("./index.html", "utf-8", function (err, data) {
        //读取内容
        if (err) throw err;
        response.writeHead(200, { "Content-Type": "text/html" }); //注意这里
        response.write(data);
        response.end();
      });
    })
    .listen(8888);
};
```

#### index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <title>index</title>
    <meta content="IE=8" http-equiv="X-UA-Compatible" />
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
  </head>

  <body>
    <h2>看到这个页面就表示 GitHub 的 Webhook 已经成功启动！</h2>
  </body>
</html>
```

现在来到 `/www/blog/webhook` 执行 PM2

```bash
cd /www/blog/webhook && pm2 start app.js
```

看到如下图代表成了
![image.png](https://lib.sixtyden.com/pm2_view.png)
此时你可以在浏览器中访问 `http://your_ip:8888/` 端口你可以自己去代码里面修改，如果看到 `index.html`中的恭喜页就代表一切就绪

现在，你去博客的仓库中再次去查看 Webhook，在 Recent Deliveries 中已经可以看到回调了，如果没有成功你需要检查一下你的 node 服务。
![image.png](https://lib.sixtyden.com/webhook_rencent.png)
现在去修改仓库的代码，然后 push ，你就能发现服务器上面的 `/www/blog/blog` 文件夹里面的文件自动更新了。

至此，整个搭建过程就全部完成了，之后我会继续出一篇如何美化和组织 Docsify 博客。

如果你还有问题可以在 GitHub 我的博客中提交 issue 我看到后会立即帮助你，如果觉得有帮助的话，欢迎 start 我的博客。

## 参考

1. [centos7 安装 Docker 详细步骤（无坑版教程）](https://cloud.tencent.com/developer/article/1701451)
2. [Nginx 容器教程](https://www.ruanyifeng.com/blog/2018/02/nginx-docker.html)
3. [Docker 入门教程](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
4. [Docsify 快速开始](https://docsify.js.org/#/zh-cn/quickstart)
5. [Docker 从入门到实践](https://yeasy.gitbook.io/docker_practice/)
6. [使用 GitHub Webhook 实现静态网站自动化部署](https://jimmysong.io/blog/github-webhook-website-auto-deploy/)
7. [GitHub Webhook](https://docs.github.com/en/developers/webhooks-and-events/webhooks/about-webhooks)
8. [PM2](https://pm2.keymetrics.io/docs/usage/quick-start/)
