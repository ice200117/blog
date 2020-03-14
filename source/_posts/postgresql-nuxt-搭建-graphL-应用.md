---
title: postgresql+nuxt 搭建 graphL 应用
date: 2019-08-07 17:28:47
tags:
---

# [服务端](https://docs.hasura.io/1.0/graphql/manual/deployment/docker/index.html)

## 获取docker 运行脚本
$ wget https://raw.githubusercontent.com/hasura/graphql-engine/master/install-manifests/docker-run/docker-run.sh

## 启动 docker-run.sh 脚本

docker run -d -p 8080:8080  -e HASURA_GRAPHQL_DATABASE_URL=postgres://postgres:postgres@localhost:5433/myapp_development   -e HASURA_GRAPHQL_ENABLE_CONSOLE=true hasura/graphql-engine:latest

# [前端](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/nuxtjs-postgres-graphql)

按照文档修改成自己后台的地址和表、字段名称，访问首页可以看到自己的数据了
