# 1.个人博客项目介绍
这是我的第一个基于java+SpringBoot框架实现的项目，也是我的一个从零基础开始的后端项目，此项目重点使用了后端技术，前端技术主要使用了Amaze UI主题，通过使用其中的代码模板来完成前端代码的编写。
# 2.项目背景
  对于初学Springboot的朋友来说，最好的一个学习方式就是那一个功能俱全的项目来练练手，通过自己重构项目来发现其中的潜在难题，并且也能很好的在编码过程中总结和发现问题、解决问题。使用Springboot开发的博客系统，简单并且实用。
# 3.页面梳理
### 主页
1.博客汇总，以列表形式展示文章，并附上文章作者、发布日期、分类情况以及文章简要
2.能够以分类形式查看文章
3.能够以时间列表方式归档文章
4.可实现通过标签查找所有相关文章
5.我的个人介绍、联系方式
6.展示最新评论，最新留言
7.网站信息显示网站运行天数
### 网站后台管理页面
1.网站仪表盘，记录网站访客量情况
2.文章管理，分页展示文章信息，可对文章进行再编辑以及删除文章
3.点赞管理，支持全部标记为已读
4.分类管理，支持增加、删除、修改分类
5.反馈信息管理，可查看用户反馈信息
6.悄悄话管理，可看到用户发送的悄悄话
### 个人资料页面
1.个人资料，支持本地上传头像，资料等信息。
2.安全设置，支持通过手机号修改密码
3.评论管理，可操作当前用户评论
4.留言管理，可操作当前用户发表的留言
5.悄悄话，可向网站设计者发送悄悄话
### 发布文章页面
1.使用markdown编辑器，支持插入代码，插入图片，插入表格等功能
2.文章可选择分类和标签，以及转载文章支持链接原作者文章

# 4.个人博客后端技术方案
### 技术方案梳理
1. 开发环境配置
jdk版本：1.8
SpringBoot版本：2.0.0.RELEASE（直接使用IDEA创建SpringBoot项目）
项目构建：Maven
日志：使用SpringBoot集成log4j生成日志
数据库ORM：Mybatis1.3.2
SpringBoot分页插件pagehelper:1.2.3
数据库：mysql
缓存：Redis
前端模板：Thymeleaf 2.1.0RELEASE

2. 整体系统采用门户网站+后台管理+用户个人中心的方式搭建，门户网站展示博客内容以及博主介绍，后台管理用于编辑文章，查看反馈，管理评论留言。

3. 登录、注册、修改账号信息接口使用MD5加密

4. 自定义切面，拦截所有需要登录操作的controller接口


### 功能结构梳理：
#### 部分接口设计

##### BackControl所有页面跳转
|接口   |功能   |Path   |
| ------------ | ------------ | ------------ |
|index   |跳转首页   |/   |
|login   |跳转登录页   |/login   |
|toLogin   |登录前尝试保存上一个页面的url   |/toLogin   |
|register   |跳转注册页   |/register   |
|user   |跳转到用户页   |/user   |
|editor   |跳转到文章编辑页   |/editor   |
|show   |跳转到文章显示页   |/article/{articleId}   |
|archives   |跳转到归档页   |/archives   |
|categories   |跳转到分类页   |/categories   |
|tags  |跳转到标签页   |/tags   |
|superadmin   |跳转到超级管理员页   |/superadmin  |

##### ArchivesControl归档
|接口   |功能   |Path   |Request   |Response   |
| ------------ | ------------ | ------------ | ------------ | ------------ |
|findArchiveNameAndArticleNum   |获得所有归档日期以及每个归档日期的文章数目   |/findArchiveNameAndArticleNum   |null   |archiveName归档日期、archiveArticleNum归档日期对应文章数量   |
|getArchiveArticle   |分页获得该归档日期下的文章   |/getArchiveArticle   |archive归档日期、rows一页大小、pageNum页数   |articleJsonArray文章信息、pageInfo分页信息、articleNum文章数量、showMonth是否展示月份   |

##### EditorControl编辑器控制
|接口   |功能   |Path   |Request   |Response   |
| ------------ | ------------ | ------------ | ------------ | ------------ |
|publishArticle   |发表博客   |/publishArticle   |principal 当前登录用户、article 文章信息、articleGrade 文章等级、request HttpServletRequest   |articleTitle文章标题、updateDate文章更新日期、author文章作者、articleUrl文章url   |
|canYouWrite   |验证是否有权限写博客   |/canYouWrite   |principal 当前登录用户   |JsonResult.success()状态码+状态信息   |
|uploadImage   |文章编辑本地上传图片   |/uploadImage   |file 图片信息   |resultMap状态信息+图片url   |


##### IndexControl主页信息控制部分接口

|接口   |功能   |Path   |Request   |Response   |
| ------------ | ------------ | ------------ | ------------ | ------------ |
|getVisitorNumByPageName   |增加访客量   |/getVisitorNumByPageName   |request HttpServletRequest、pageName网页名称   |totalVisitor总访问量、pageVisitor该页面访问量   |
|myArticles   |分页获得当前页文章   |/myArticles   |rows一页的大小、pageNum当前页   |thisPageInfo当前页文章内容   |
|newComment   |获得最新评论   |/newComment   |rows一页的大小、pageNum当前页   |jsonArray评论信息、pageJson分页信息   |
|getSiteInfo   |获得网站基本数据信息   |/getSiteInfo   |null   |articleNum文章数、tagsNum标签数、leaveWordNum留言数、commentNum评论数   |

##### UserControl个人主页控制部分接口
|接口   |功能   |Path   |Request   |Response   |
| ------------ | ------------ | ------------ | ------------ | ------------ |
|uploadHead   |上传头像   |/uploadHead   |request HttpServletRequest、principal 当前用户信息   |data获得头像url   |
|getUserPersonalInfo   |获得个人资料   |/getUserPersonalInfo   |principal 当前用户信息   |data获得用户个人信息   |
|getUserComment   |分页获得该用户曾今的所有评论   |/getUserComment   |rows 一页大小、pageNum 当前页、principal 当前用户   |data分页获得用户所有的评论   |
|sendPrivateWord   |发布悄悄话   |/sendPrivateWord   |privateWord 悄悄话内容、principal 当前用户   |data发送信息   |
|readAllMsg   |已读所有消息   |/readAllMsg   |msgType消息是评论消息还是留言消息  1-评论  2--留言、principal 当前用户   |JsonResult.success()状态码+状态信息   |
|getUserNews   |获得用户未读消息   |/readAllMsg   |principal 当前用户   |data获得redis中用户的未读消息   |


#### 数据库表设计
##### 用户表：user
| 名称  | 类型  |  长度 |  主键 | 非空  | 描述 
| ------------ | ------------ | ------------ | ------------ | ------------ | ------------
| id  | int  |  11 |  true |  true | 主键，自增 
| phone  | varchar  | 255  | false  | true  | 手机号 
| username  | varchar  | 255  |  false | true  |  用户名
| password  |  varchar |  255 |  false | true  | 密码 
| gender  | char  | 50  | false  |  true | 性别 
| trueName  | varchar  | 255  |  false | false  | 姓名 
| birthday  |  char | 100  |  false | false  | 生日 
| email  | varchar  | 255  | false  | false  | 邮箱 
| personalBrief  |  varchar | 255  | false  | false  |  个人简介
| avatarImgUrl  |  varchar |  255 | false  |  true | 头像url
| recentlyLanded  | varchar  |  255 |  false | false  |  最近登录时间

##### 文章表：article
| 名称  | 类型  |  长度 |  主键 | 非空  | 描述 
| ------------ | ------------ | ------------ | ------------ | ------------ | ------------
| id  | int  |  11 |  true |  true | 主键，自增 
| articleId  | bigint  | 20  | false  | true  | 文章id 
| author  | varchar  | 255  |  false | true  |  作者
| originalAuthor  |  varchar |  255 |  false | true  | 文章原作者 
| articleTitle  | varchar  | 255  | false  |  true | 文章标题 
| articleContent  | longtext  | 0  |  false | true  | 文章内容 
| articleTags  |  varchar | 255  |  false | true  | 文章标签 
| articleType  | varchar  | 255  | false  | true  | 文章类型 
| articleCategories  |  varchar | 255  | false  | true  |  文章分类
| publishDate  |  varchar |  255 | false  |  true | 发布文章日期
| updateDate  | varchar  |  255 |  false | true  |  更新文章日期
| articleUrl  | varchar  |  255 |  false | true  |  文章url
| articleTabloid  | 0  |  255 |  false | true  |  文章摘要
| likes  | int  |  11 |  false | true  |  文章喜欢数
| lastArticleId  | bigint  |  20 |  false | false  |  上一篇文章id
| nextArticleId  | bigint  |  20 |  false | false  |  下一篇文章id

##### 评论记录表：comment_record
| 名称  | 类型  |  长度 |  主键 | 非空  | 描述 
| ------------ | ------------ | ------------ | ------------ | ------------ | ------------
| id  | bigint  |  20 |  true |  true | 主键，自增 
| pId  | bigint  | 20  | false  | true  | 父id 
| articleId  | bigint  | 20  |  false | true  |  文章id
| originalAuthor  |  varchar |  255 |  false | true  | 文章原作者 
| answererId  | int  | 11  | false  |  true | 评论者id 
| respondentId  | int  | 11  |  false | true  | 被评论者id 
| commentDate  |  varchar | 100  |  false | true  | 评论日期 
| likes  | int  | 11  | false  | true  | 评论点赞数 
| commentContent  |  text | 0  | false  | true  |  评论内容



### 开发流程
1. controller层中编写前端接口，接收前端参数
2. service层中编写所需业务接口，将数据封装之后，供controller层调用
3. 实现service层中的接口，并注入mapper层中的sql接口
4. 采用Mybatis的JavaConfig方式编写Sql语句。由于并没有使用Mybatis的逆向功能，需要自己手写所有sql语句和model实体类
5. 关于事务的实现，在启动类中开启事务，并在service层需要实现事务的业务接口上使用@Transactional注解，还是十分方便的

### 其他功能
1. 使用lazyload插件实现页面图片懒加载
2. 后台实时记录当天访客量，便于了解博客日常访问量
3. 分析访问量最多的数据，主要在于文章访问部分，将文章放入redis缓存。每次编辑完文章后，更新缓存
4. 使用阿里云互联网中间件的业务实时监控服务，对于网站性能的了解以及优化有很大的帮助

### 网站建设
1. 服务器选用的是阿里云轻量型应用服务器，系统选择的是centos7
2. 域名是阿里云上购买的.cn的域名（jinyuhang.cn）

### 部署上线流程
1. 配置远程服务器环境。注意jdk、mysql、redis（springboot内嵌有tomcat）版本要和本地一致
2. springboot项目在本地打成jar包
使用本地IDE中的服务端代码打成jar包（IDEA的package）
在项目target目录下查看打出来的jar包（在pom文件中已将其重命名为myblog.jar）
可在本地尝试运行：
```
java -jar myblog.jar
```
SpringBoot内置了tomcat，只要服务器上配置的jdk版本是1.8以上的，就不需要内置tomcat了
3. 将项目部署到远程服务器
4. 远程服务器防火墙的安全设置（端口放开）；
5. 在远程服务器上对Redis服务操作：
（1）进入Redis安装目录src文件夹下：
```
cd /usr/local/redis-4.0.9/src
```
（2）后台启动Redis：
```
nohup ./redis-server ../redis.conf &
```
（3）访问Redis：
在src目录下：
```
./redis-cli
```
有密码的话：
```
./redis-cli -a ###
```
（4）查看redis服务状态：
```
ps -ef | grep redis
```
（5）关闭Redis服务：
```
./redis-cli shutdown
```
（6）本地使用RedisDesktopManager对远程服务器上的redis服务操作
6.在远程服务器上对mysql服务操作
（1）进入mysql安装目录bin文件夹下
```
cd /usr/local/mysql/bin
```
（2）后台启动mysql
```
./mysqld_safe &
```
（3）关闭mysql：
bin目录下：
```
./mysqladmin -uroot -p shutdown
```
7.运行项目
（1）运行前先查看jar包是否被占用：
```
ps -ef | grep myblog.jar
```
（2）若有占用先
```
kill -9 ****（进程号）
```
（3）新建run-jar.sh文件并输入：
```
#！/bin/sh
nohup java -jar myblog.jar &（使jar包一直处于后台运行）
```
（4）启动项目
```
./run-jar.sh
```
（5）浏览器输入www.jinyuhang.cn检查是否正常


### SpringBoot注解使用：
- @Autowired：自动注入
- @Controller ：控制层注解，在对应的方法上，视图解析器可以解析return 的jsp,html页面，并且跳转到相应页面。
- @ResponseBody：若返回json等内容到页面，需要加该注解
- @RestController：相当于@Controller+@ResponseBody两个注解的结合，返回json数据不需要在方法前面加@ResponseBody注解了，但使用@RestController这个注解，就不能返回jsp,html页面，视图解析器无法解析jsp,html页面。
- @Transactional：在service层需要实现事务的业务接口上使用@Transactional注解来开启事务
- @RequestMapping： 默认method是get,post方式都支持
- @GetMapping：接收get请求
- @RequestParam：将请求参数绑定到控制器的方法参数上（是springmvc中接收普通参数的注解）
- @Slf4j：启用日志注解
- @PermissionCheck：自定义的注解，实现权限验证，定义三种权限：普通用户1、vip用户2、超级管理员用户3
- @PathVariable:可以将URL中占位符参数{xxx}绑定到处理器类的方法形参中@PathVariable(“xxx“)
- @AuthenticationPrincipal：Springsecurity中获取当前用户信息

### 开启事务的业务：
- 修改文章，通过id删除文章
- 保存评论
- 保存反馈信息
- 保存留言信息，保存留言回复信息
- 发布悄悄话，回复悄悄话

# 5.遇到的问题及解决方案

- 要实现在一个页面进行权限验证，如果验证不成功会跳转到登录界面，并且登录成功后还要返回到之前界面，这里由于对SpringSecurity内部原理的不了解，所以我这里采用的方法是利用请求头和响应头存储url，并在登录成功后的页面出跳转到响应头中存储的url处
- 上传头像处使用上传头像至阿里云的OSS对象存储中，由于上传总是失败没有返回上传成功后的图片url地址，找了半天原因发现是阿里云API的外网域名没有设置正确，本应是"oss-cn-beijing.aliyuncs.com"，错认为是"www.jinyuhang.cn"，将这个错误改正后还是拿不到图片的URL地址，所以干脆设置OSS的Bucket为公共读权限，然后当上传成功后手动拼接图片url并存入数据库。
- 项目中最大的难点还是莫过于页面css、js的设计，但是使用了后极大的解决了这个问题，只需修改少量css、js就能实现自己所需要的样式，特此感谢[妹子UI](http://amazeui.shopxo.net/ "妹UI")、张海洋的博客：https://github.com/zhyocean
- 短信验证码的发送到现在还是存在bug，我使用的短信验证码接收的平台是阿里云的短信服务，从阿里云短信服务处获取ACCESS_KEY、申请了签名rain以及模板，可能是没有导入正确的jar包，也可能是代码逻辑哪里有问题，这个也是之后要解决的问题。（目前的用户都是手动向数据库里添加的）
