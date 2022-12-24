> 本方案基于方案于 [alicfeng/gogs-drone-docker](https://github.com/alicfeng/gogs-drone-docker.git) 仓库，由于 `Gogs` 版本比较老旧，故使用基于 `Gogs` 的开源社区版 `Gitea`

## 前言

`CI / CD`( 持续集成 / 持续部署  )方案是DevOps中不可或缺的流程之一，最近也了解了部分的相关的解决方案，最终选择了`Drone` + `Gitea`基于`docker`容器环境来构建`CI / CD`，本文将分享下如何构建此平台以及如何快速地使用到项目开发中。

**应该会有一个疑问？我为什么不选择主流的`GitLab` + `Jenkins` 两个最佳搭档来构建呢？**

- `GitLab`是使用`Ruby`编写的，`Jenkins`更是了不起，使用`Java`来编写的，项目整体比较膨大，同时它们对硬件、CPU等开销比较高
- `Drone`、`Gitea`皆是使用`Go`语言来编写构建，在整体的语言性能与内存开销算是有一定的优势，同时`Drone`支持`Github`、`GitLab`、`Gitea`以及`Bitbucket`，这点很不错！反手就是一个赞?
> GitLab + Jenkins该组合还是一个不错的选择，我并没有反对，为何呢？GitLab是一个非常成熟的git工具之一，同时Jenkins也是非常成熟的CICD组件，功能非常强大。
> 但是我还是要站在正义的一边，选择`Drone` + `Gitea`。O(∩_∩)O哈哈~



#### 说明

`Gitea`建议使用 1.15.7，latest 以及 1.17.4 版本存在问题（目前只测试了这两个版本，均无法正常使用），无法触发 webhook（2022年12月24日）


#### 环境

使用的前提，必须符合以下条件
- 系统安装了`Docker`，同时要安装了`Docker`编排工具`docker-compose`
- 主流的`x64`位系统，`Linux`、`Mac`、`Window`等
- 安装了`git`版本控制工具



#### 安装
安装非常简单，拉取`docker-compose.yml`编排文件，基于`Docker`环境自动构建即可！
```shell
git clone https://github.com/alicfeng/gogs-drone-docker.git
cp .env.simple .env # 这里需要手动配置相关的参数
cd gogs-drone-docker && docker-compose up -d
```
执行`docker ps`来看下容器的运行情况

![docker ps](https://github.com/klzdy123/gitea_drone_docker/blob/2.0/file/docker_ps.png)

#### 还有一步，为 Drone 授权
首先创建 `OAuth2` 应用，记得点击 `保存` 按钮

![create_oauth2](https://github.com/klzdy123/gitea_drone_docker/blob/2.0/file/create_oauth2.png)

![oauth_info](https://github.com/klzdy123/gitea_drone_docker/blob/2.0/file/oauth_info.png)

创建完成后，将 `CLIENT_ID`、`CLIENT_SECRET` 复制进 `.env` 文件中，将容器 `down` 掉后重新构建
```shell
docker-compose down && docker-compose up -d
```
访问 `Drone` 网站，点击跳转入以下界面，点击授权即可

![create_oauth2](https://github.com/klzdy123/gitea_drone_docker/blob/2.0/file/drone_%20authorization.png)

> 对应的配置文件可以根据项目的需求自由灵活改变，同时像我这样强迫症的人，我不喜欢使用`IP`来进行访问请求的以及`http`协议访问，我会使用`nginx`代理。[不详细说了](https://www.jianshu.com/p/5d36ccb5af88)

至此，我们已经完成了平台的构建工作了。我们来欣赏下~干杯~

有两个地方需要注意：
- `Drone`登录的账号需要在`Gogs`设置为管理员，他俩兄弟的账密是互通的
- `Gitea`的仓库会自动同步到`Drone`上，此时，需要在`Drone`开启钩子才能正常运行



#### 使用
好了，是时候来体验两把了，这里需要有一个前提了，O(∩_∩)O哈哈~，你需要了解它是如何运行的，根据什么来自动化构建的
每当分支的代码更新的时候，`Gitea`会动过钩子同步通知`Drone`，而`Drone`收到通知之后会发生一系列动作
 - 通过git插件`clone`分支代码到容器里面
 - 测试
 - 编译代码，构建可执行文件
 - 将项目和运行环境打包成镜像，发布到`Registry`
 - 部署至生产环境
 - 发送邮件等通知信息，这里还有很多插件，比如微信、钉钉、电报等

   构建的剧本是通过`.drone.yml`文件编排的，基于`Docker`镜像进行构建，很nice~下面简单体验下`Laravel`项目的即可！[github](https://github.com/alicfeng/gitea_drone_docker/tree/1.0/drone) 有`laravel`、`vue`等前后端编排的`yml`文件。

```yml
pipeline:
  build:
    image: motecshine/laravelphp71
    commands:
    - mv $(pwd)/.env.dev $(pwd)/.env
    - composer config repo.packagist composer https://packagist.phpcomposer.com
    - composer install --no-scripts --no-dev
    # others
```
![drone](https://github.com/klzdy123/gitea_drone_docker/blob/2.0/file/image3.png)

![drone](https://github.com/klzdy123/gitea_drone_docker/blob/2.0/file/image2.png)


**[价值源于技术，技术源于分享](https://github.com/alicfeng)**