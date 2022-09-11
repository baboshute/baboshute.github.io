# Vulnerability Wiki

**【免责声明】本仓库所涉及的技术、思路和工具仅供学习，任何人不得将其用于非法用途和盈利，否则后果自行承担。**

一个知识库，集成了Vulhub、Peiqi、EdgeSecurity、0sec、Wooyun等开源漏洞库，涵盖OA、CMS、开发框架、网络设备、开发语言、操作系统、Web应用、Web服务器、应用服务器等多种漏洞。

支持Docker部署。部署前请详细阅读网络安全法律法规、安全政策、国家标准、行业标准：

- 法律法规知识库：[Threekiii/Awesome-Laws](https://github.com/Threekiii/Awesome-Laws)

## 0x01 安装docsify

### 官方文档

- docsify：https://docsify.js.org/#/zh-cn/
- 全局安装`docsify-cli` 工具（依赖npm）：

```
npm i docsify-cli -g
```

### 安装插件

- 侧边栏收起：https://github.com/iPeng6/docsify-sidebar-collapse
- 复制到剪贴板：https://github.com/jperasmus/docsify-copy-code/blob/master/README.md
- 代码高亮：https://github.com/PrismJS/prism
- 分页导航：https://github.com/imyelo/docsify-pagination#readme

### 项目介绍

- docs-base：主项目入口，包括主要漏洞数据文件及wooyun、0sec导航页面。
- docs-0sec：0sec漏洞库入口，由于数据量太大，对主项目加载及检索速度产生影响，因此从主项目中切割成一个新的docker，另起一个新服务。**docs-0sec不再重点维护，相关issues不予回复。**
- wooyun：直接拉取镜像v7hinc/wooyun。

## 0x02 部署服务

克隆仓库：

```
git clone https://github.com/Threekiii/Vulnerability-Wiki.git
```

下载[docs-0sec release](https://github.com/Threekiii/Vulnerability-Wiki/releases/download/v1.0/docs-0sec.zip)，解压到对应的docs-0sec文件夹下。

### 方法一 docsify 本地部署

进入docs-base和docs-0sec下的docs目录，执行以下命令启动对应服务：

```
docsify serve docs-base
docsify serve docs-0sec
```

执行以下命令启动wooyun漏洞库：

```
docker pull v7hinc/wooyun
docker run --privileged=true --name wooyun -v ~/upload:/home/wwwroot/default/upload -p 3003:80 -dit v7hinc/wooyun:latest /bin/bash
```

访问对应的http://your-vps-ip:port即可获取服务。

### 方法二 docsify docker部署

参考docsify官方文档可以将目录挂载到Docker部署：

- https://docsify.js.org/#/zh-cn/deploy?id=docker

### 方法三 dockerfile（已废弃）

通过对应目录下的Dockerfile部署服务：

```
docker build -f Dockerfile -t threeki/vulnerability_wiki .

docker run -d -p 3000:3000 threeki/vulnerability_wiki:latest
```

在当前仓库目录下，使用以下方式进行更新，无需重新生成Docker镜像或容器：

```
# 拉取更新内容
git pull

# 拷贝进docker，container ID为容器ID
docker cp /docs <container ID>:/data
```

### 方法四 docker-compose

通过docker-compose.yml部署三个Docker：

```
# 克隆项目
git clone https://github.com/Threekiii/Vulnerability-Wiki.git

# 安装docker及docker-compose
apt install docker docker-compose

# 启动服务
docker-compose up -d

# 关闭服务
docker-compose down
```

对应服务及地址信息如下：

- docs-base：http://your-vps-ip:3001
- docs-0sec：http://your-vps-ip:3002
- wooyun：http://your-vps-ip:3003

## 0x03 ISSUES

1. 若docker-compose部署时出现cnpm相关错误，请更改对应Dockerfile，将cnpm改为npm，再次尝试。参考[issue](https://github.com/Threekiii/Vulnerability-Wiki/issues/5)：

   > 启动docker-compose会报错，提示node的版本过低，我最后更改了Dockerfile里面的配置才行
   >
   > ```
   > From node:16-alpine
   > RUN npm install npm -g --registry=[https://registry.npm.taobao.org](https://registry.npm.taobao.org/)
   > RUN npm i docsify-cli -g
   > ```
   >
   > debian环境，node改成16，cnpm改成npm

1. Vps部署可能出现编码问题，可参考以下命令解决：

```
convmv -f gb2312 -t UTF-8 --notest -r ./Vulnerability-Wiki/docs-base/docs --replace
```

3. 有时会出现首页全局搜索不到的问题，还未解决，但是不影响使用。进入细分目录搜索不存在该问题。docisfy是LocalStorage本地索引，`search.min.js`通过加载本地索引进行搜索。假设搜不到OA相关漏洞，点击OA对应链接，跳转后本地索引自动更新，就能搜索到了。

4. docs-0sec可能遇到以下问题（感谢[li-archer](https://github.com/li-archer)的反馈）：

   > 使用compose up模式构建的时候：新解压出来的0sec会需要使用一次 docker-compose up --build -d对0sec进行构建，不是的话会打开页面出现404报错。

   > 内存2g，cpu2核打开0sec 网页会提示内存报错

5. 乌云镜像没有图片的问题，需要参考https://github.com/V7hinc/wooyun_final 做目录挂载。

## 0x04 示例

2.0版本：

![image-20220610095555964](https://typora-notes-1308934770.cos.ap-beijing.myqcloud.com/202206100955067.png)

![image-20220610095621828](https://typora-notes-1308934770.cos.ap-beijing.myqcloud.com/202206100956951.png)

1.0版本：

![image-20220429163413683](https://typora-notes-1308934770.cos.ap-beijing.myqcloud.com/202204291634866.png)

## 0x05 声明

本项目收集漏洞均源于互联网，主要来自以下项目：

- Vulhub：https://github.com/vulhub/vulhub
- Peiqi：https://github.com/PeiQi0/PeiQi-WIKI-Book
- EdgeSecurity： https://github.com/EdgeSecurityTeam/Vulnerability
- 0sec：零组漏洞库。零组已于2021年停运。内容来源于互联网.docx文件，已全部转换为.html文件。
- Wooyun：乌云漏洞库。乌云已于2016年停运，镜像参考 https://github.com/V7hinc/wooyun_final

## 0x06 更新日志

- 2022.08.24 更新部分漏洞，增加ISSUES提示
- 2022.07.15 新增docs-0sec release
- 2022.06.20 2.0版本，重新划分漏洞分区，优化搜索策略
- 2022.05.25 补全peiqi漏洞库中所有漏洞
- 2022.05.13 引入wooyun漏洞库，部署方式改为docker-compose，废除Dockerfile部署方式

