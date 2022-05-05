---
title: "Github Action 打包 Docker 镜像并自动部署"
categories: ["默认分类"]
tags: []
draft: false
slug: "912"
date: "2021-05-05 22:45:00"
---

根据 git tags 触发部署，用版本号生成镜像，上传至腾讯云私有镜像仓库。
服务器拉取最新版本部署上线。

```yaml
name: Docker Build And Push
on:
  push:
    tags:
      - 'v*'

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - name: Get version
        id: version
        run: echo ::set-output name=VERSION::${GITHUB_REF/refs\/tags\//}

      -
        name: Set up QEMU
        uses: docker/setup-qemu-action@v1
      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1
      -
        name: Login to DockerHub
        uses: docker/login-action@v1
        with:
          registry: ccr.ccs.tencentyun.com
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Build and push
        id: docker_build
        uses: docker/build-push-action@v2
        with:
          push: true
          tags: ${{ secrets.REGISTRY }}:latest,${{ secrets.REGISTRY }}:${{ steps.version.outputs.VERSION }}
      -
        name: Image digest
        run: echo ${{ steps.docker_build.outputs.digest }}

  pull-docker:
    needs: [ docker ]
    name: Pull Docker
    runs-on: ubuntu-latest
    steps:
      - name: Deploy
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.KEY }}
          script: |
            docker stop $(docker ps --filter ancestor=${{ secrets.REGISTRY }} -q)
            docker rm -f $(docker ps -a --filter ancestor=${{ secrets.REGISTRY }}:latest -q)
            docker rmi -f $(docker images  ${{ secrets.REGISTRY }}:latest -q)
            docker login --username=${{ secrets.DOCKERHUB_USERNAME }} --password ${{ secrets.DOCKERHUB_TOKEN }} ccr.ccs.tencentyun.com
            docker pull ${{ secrets.REGISTRY }}:latest
            docker run -d -p 8060:8060 ${{ secrets.REGISTRY }}:latest

```
