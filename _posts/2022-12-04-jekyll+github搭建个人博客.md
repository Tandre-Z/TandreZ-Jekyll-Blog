---
title: jekyll+github搭建个人博客
description: 也许“每一个程序员心中都有一个极客梦”，本文章记录了我博客搭建及利用Jekyll和GitHub搭建我的博客的全流程
categories:
- test/test
tags:
---
> 也许“每一个程序员心中都有一个极客梦”

#### 我的博客搭建经历

第一次搭建博客是利用腾讯云免费提供的半年的服务器，花了很长时间使用wordpress最终上线了，但还是放弃了。

后来服务器到期，使用旧电脑自建服务器，通过内网穿透的方法再次尝试搭建，虽然也成功上线了，可旧电脑需要保持开机状态，加之换地方等过于麻烦也放弃了。

之后比较多的是使用csdn和个人公众号，但仍想有一个自己的网站，偶然的机会发现jekyll+GitHub pages便可以轻松搭建博客，也不需要服务器，所以毅然决然踏上了这条路。

#### 需要准备的东西

1. 一个GitHub账号（程序员必备）
2. Jekyll本地开发环境
3. 一个markdown的编辑工具（可选）

个人是因为github自带有jekyll引擎，使用时只需要编写markdown文件上传便可以，不需要每次写完后转成html再上传，相对方便很多才选择了jekyll。除此之外也可以使用hexo，相对使用上麻烦了些，但有中文文档碰到问题可以更容易解决。

#### 搭建流程

##### jekyll环境搭建

###### 安装ruby

1. 下载安装Ruby和Devit(x64),下载地址：<https://rubyinstaller.org/downloads/> 这里需要安装带有Devkit的，devkit是window系统下必须的，有了它就可以不用自己去编译c/c++的源代码。
2. 安装时基本一路next就可以了，但需要注意需要勾选添加到系统变量，最后会有一个命令窗口，输入3将两个组件一起安装就可以了
3. 安装完成后，win+r打开“运行”，输入cmd打开命名窗口，输入```ruby -v```如果出现了ruby的版本号则说明已经安装好了。

###### 安装Rubygems

它相当于ruby的包管理器，下载地址：<https://rubygems.org/pages/download> 下载.zip版本直接解压缩运行就可以了，也可以到安装目录位置，按住shift右键打开终端，输入```ruby setup.rb```，等待安装完成后输入```gem -v```如果出现gem的版本号，则说明安装完成
![rubygem](https://github.com/Tandre-Z/Tandre-Z.github.io/blob/main/assets/images/M_BlogImg/20221204/rubygem.png?raw=true)

###### 安装Jekyll

cmd打开命令行工具，输入```gem install jekyll```，等待安装完成后，输入```jekyll -v``` ，正常出现版本号即说明安装完成

##### 下载Jekyll主题

jekyll官方提供了很多的主题可以使用：<http://jekyllthemes.org/>
我使用的是<https://github.com/simpleyyt/jekyll-theme-next> 下载压缩包后解压缩，或者去它的GitHub仓库clone到自己的github然后再克隆到本地。

##### 安装依赖，开启本地服务

有些模板可能需要安装包的依赖，在下载好的模板的根目录下先执行```gem install bundler```命令，来安装gem的依赖包，然后运行```jekyll server```，如果有提示run bundle install，则再执行```bundle install```,之后重复上边执行```jekyll server```

``` bash
gem install bundler
bundle install
jekyll server
```

如果问题无法解决，可以尝试在安装依赖前删除gemfile.lock文件再安装，两者的区别在于不删除时则会根据该文件进行安装所需的依赖包，删除后则自动根据模板需要进行安装

有时候可能会报错提示你run bundle exec jekyll server，只需要按要求执行```bundle exec jekyll exec``` 一般第一次运行jekyll本地服务需要这样执行，后面就只需要```jekyll s```或者```jekyll server```便可以打开Jekyll 本地服务。

如果你像我一样ruby版本过高，可能还会有缺少webrick的错误提示,只需要执行```bundle add webrick```再开启本地服务就可以了。具体可以看这里的说明：<https://jekyllrb.com/docs/>

```bash
bundle add webrick
bundle exec jekyll exec
```

开启了本地服务后,如下图会有一个地址 http:/127.0.0.1：4000，在浏览器输入该地址便可以看到成功部署本地服务器的博客，如果这里没有出现地址，则说明仍有问题，可以根据他的输出，查找出现的问题在哪里并加以解决，通常是依赖包没有安装完全或者端口被占用。正常打开本地服务的示例如下：
![loaclserver](https://github.com/Tandre-Z/Tandre-Z.github.io/blob/main/assets/images/M_BlogImg/20221204/localserver.png?raw=true)  

##### GitHub pages

关于GitHub的基础操作有很多相关教程，官网也提供了很详细的说明文档，可以再[这里](https://pages.github.com/)查看GitHub pages的官方介绍。
以下主要从博客搭建是需要设置的地方进行说明：

1. 如果前面不是clone的别人的仓库则可以进入自己的GitHub新建一个仓库，把它命名为 username.github.io，示例如下图，如果是clone的仓库也可以如下图打开仓库的设置页面更改仓库名。这是一个特殊的仓库，username表示自己github的用户名，后面访问自己的博客也将使用该地址。
   ![name.png](https://github.com/Tandre-Z/Tandre-Z.github.io/blob/main/assets/images/M_BlogImg/20221204/name.png?raw=true)

2. 将模板上传到自己的仓库，仓库的层级关系应该是如下这个样子，其中的_post文件夹将是我们放我们自己写的博客的位置
   ![仓库样式](https://github.com/Tandre-Z/Tandre-Z.github.io/blob/main/assets/images/M_BlogImg/20221204/githubstyle.png?raw=true)  

3. 打开创建的这个仓库，找到设置页面的pages页面，设置好分支路径，就可以通过页面上展示的那个链接访问自己的博客了
![pages](https://github.com/Tandre-Z/Tandre-Z.github.io/blob/main/assets/images/M_BlogImg/20221204/pages.png?raw=true)

#### 博客设置与编写

如果是使用的模板，一般模板的github上会有相关的使用说明可以查阅。下面将几个主要用到的地方。

1. _config.yml文件是一个jekyll的配置文件，可以在里面更改网站的标题名字描述等，具体的一般模板的github上会有介绍。
2. _posts文件夹里面是用来放我们写的博客的地方，博客需要是.md文件，命名需要为20xx-xx-xx-blogName.md,jekyll的具体结构可以参考jekyll的官方文档：<https://jekyllrb.com/docs/pages/>

关于markdown的编写，作为程序员，我比较用得惯的是vscode，它自带有git插件，将整个文件夹添加入vscode后可以直接推送到github，非常方便。另外安装Markdown All in One,Markdown Paste,Markdown Preview Enhanced三个插件后，编写markdown也会方便很多。

除vscode外也有很多其他的markdown工具如印象笔记、苹果的atom等，对于项目的提交，也可以单纯使用git或者github desktop，后者的界面很容易上手，git的常用命令使用习惯后相对也很快捷。
