---
title: "Github Action 前后端项目模板"
categories: ["默认分类"]
tags: ["CI/CD"]
draft: false
slug: "907"
date: "2021-04-12 09:58:00"
---

![android-github-actions-setup-image-35b6a79fea4a7289acb6796cd4ad05b4.png](https://img.bi-bo.cn/2021/05/4231068411.png)
GitHub Actions 用起来非常简单，只要在你的仓库根目录建立.github/workflows文件夹，将你的工作流配置(YAML 文件)放到这个目录下，就能启用 GitHub Actions 服务

### 前端项目部署到服务器
```yaml
name: Deploy
on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@master

      - name: use Node.js 14
        uses: actions/setup-node@v1
        with:
          node-version: 14

      - name: npm ci and build
        run: |
          npm ci
          npm run build
        env:
          NODE_ENV: development
          CI: true

      - name: Prepare SSH
        run: >
          cd ~ && mkdir .ssh &&
          touch ~/.ssh/known_hosts &&
          ssh-keyscan -H "$IP" >>~/.ssh/known_hosts
        env:
          IP: ${{ secrets.HOST }}

      - name: Deploy to Server
        uses: easingthemes/ssh-deploy@v2.1.6
        env:
          REMOTE_HOST: ${{ secrets.HOST }}
          SSH_PRIVATE_KEY: ${{ secrets.TOKEN }}
          ARGS: "-avzr --delete"
          SOURCE: "build/"
          REMOTE_USER: "ubuntu"
          TARGET: "/var/www/scholar.run"
		  
```

### 后端 Docker 镜像
GitHub Actions Docker镜像编译和推送到云镜像仓储

Github：https://github.com/risfeng/docker-image-build-push-action

Github Marketplace：https://github.com/marketplace/actions/docker-images-build-and-push


```yaml
name: Docker Build And Push To Aliyun Hub

on:
  push:
    branches:
      - master

jobs:
  build:
    name: Build Spring Boot
    runs-on: ubuntu-latest
    steps:
      - name: Git Checkout Code
        uses: actions/checkout@v1
        id: git_checkout

      - name: Set up JDK 12.0
        uses: actions/setup-java@v1
        with:
          java-version: 12.0

      - name: Build with Gradle
        run: ./gradlew build

      - name: Build Docker Image
        id: buildAndPushImage
        uses: risfeng/docker-image-build-push-action@v1.0
        with:
          registry_url: 'registry.us-east-1.aliyuncs.com'
          namespaces: 'adotcode'
          repository_name: 'adc.ms.eureka.usa'
          user_name: ${{ secrets.ALIYUN_IMAGES_HUB_USER_NAME }}
          password: ${{ secrets.ALIYUN_IMAGES_HUB_TOKEN }}
          image_version: 'v1.0'
          docker_file: '.'
      - name: Get pre step result output image_pull_url
        run: echo "The time was ${{ steps.buildAndPushImage.outputs.image_pull_url }}"
```
其中 `registry_url、namespaces、repository_name、user_name、password` 为自己云镜像仓库设置，`${{ secrets.ALIYUN_IMAGES_HUB_USER_NAME }} ${{ secrets.ALIYUN_IMAGES_HUB_TOKEN }}`是调用Github仓库settings配置的云镜像仓库的登录用户名和密码，防止密码硬编码被泄漏，配置路径：Github代码仓库-->[settings]->[Secrets]中添加对应的Key。

## 输入参数说明

以下参数均可参见云私有镜像仓库，如：[阿里云私有镜像仓库](https://cr.console.aliyun.com/)

| 参数            | 是否必传 | 描述                                                   |
| --------------- | -------- | ------------------------------------------------------ |
| registry_url    | 是       | 仓库地址，eg: `registry.us-east-1.aliyuncs.com`        |
| namespaces      | 是       | 命名空间                                               |
| repository_name | 是       | 镜像仓库名称                                           |
| user_name       | 是       | 云登录账户                                             |
| password        | 是       | 登录个人容器镜像服务设置                               |
| image_version   | 是       | 生成镜像的版本，可以写死，也可以通过上下文自行动态赋值 |
| docker_file     | 否       | 构建镜像的Dockerfile目录，默认当前目录（.）            |

## 输出参数说明

脚本执行完成后会输出镜像pull地址，便于后续直接docker pull 使用

| 参数           | 是否必传 | 描述                                                         |
| -------------- | -------- | ------------------------------------------------------------ |
| image_pull_url | 是       | 镜像上传成功后返回的pull地址，eg: `registry.us-east-1.aliyuncs.com/ns/adc.ms.erika:v1.0` 使用示例: `docker pull ${{ steps.<steps.id>.outputs.image_pull_url }}` |

#### 参考文章

https://frostming.com/2020/04-26/github-actions-deploy/#%E4%BD%BF%E7%94%A8-github-actions-%E8%87%AA%E5%8A%A8%E5%8C%96
https://github.com/risfeng/docker-image-build-push-action
