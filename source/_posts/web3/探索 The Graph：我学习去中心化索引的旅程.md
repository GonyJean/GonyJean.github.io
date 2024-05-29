---
title: 探索 The Graph
date: 2024-05-29 19:57:21
tags: [solidity, 合约, web3]
cover: [/images/theGraph.webp]
banner:
  type: img
  bgurl: [/images/theGraph.webp]
category: [web3]
single_column: true
---

# 探索 The Graph

在区块链和去中心化应用（dApps）的世界中，高效的数据检索是一个至关重要的组件。这是一个用于从区块链中索引和查询数据的去中心化协议。

## 什么是 The Graph？

The Graph 可以有效地索引和查询区块链数据。它允许开发者通过 GraphQL 进行查询，从而更方便地构建复杂的 dApp。The Graph 通过建立和部署子图（subgraph），使得数据能够以去中心化的方式进行检索。

## 准备工作

在开始之前，需要确保已经安装了以下工具：

- Node.js
- Yarn
- Docker
- Git

这些工具是开发和部署子图的基础。

## 第一步：创建子图

首先需要使用 Graph CLI 创建了一个新的子图项目。通过运行以下命令，初始化了一个新的子图：

```bash
graph init --from-contract <CONTRACT_ADDRESS> <GITHUB_USERNAME>/<SUBGRAPH_NAME> --network <NETWORK>
```

这个命令会根据指定的智能合约地址生成相应的子图配置文件和模板代码。

## 第二步：定义子图

在 `subgraph.yaml` 文件中，定义了子图的结构，包括需要索引的事件、实体和映射。这一步骤非常重要，因为它决定了子图如何从区块链中提取和存储数据。

## 第三步：编写映射代码

映射代码是子图的核心部分，它定义了如何处理从区块链中提取的数据。在 `src/mapping.ts` 文件中编写这些逻辑，用 TypeScript 实现数据转换和存储的过程。

## 第四步：部署子图

在本地测试完成后，将子图部署到 The Graph 的托管服务上。首先，需要创建一个新的子图并获取部署密钥。然后，通过运行以下命令将子图部署到 The Graph 网络：

```bash
graph auth --product hosted-service <ACCESS_TOKEN>
graph deploy --product hosted-service <GITHUB_USERNAME>/<SUBGRAPH_NAME>
```

## 第五步：查询子图

子图部署成功后，可以使用 GraphQL 进行数据查询。The Graph 提供了一个强大的查询接口，使得能够方便地检索和展示数据。

## 的hackathon项目子图
(https://thegraph.com/studio/subgraph/auctionclub0/playground)
## 参考资源

- [Building a Subgraph Workshop](https://github.com/dabit3/building-a-subgraph-workshop)
- [The Graph 官方文档](https://thegraph.com/docs/)



希望我的分享对你有所帮助，欢迎大家一同探索 The Graph 的无限可能！