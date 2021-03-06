---
title: 关于 Graphs 的思考
layout: ../_core/DocsLayout
category: 最佳实践
permalink: /learn/thinking-in-graphs/
next: /learn/serving-over-http/
---

## 一切皆是图 [\*](https://en.wikipedia.org/wiki/Turtles_all_the_way_down)
> 使用 GraphQL，你可以将你所有的业务建模为图

图是将很多真实世界现象变成模型的强大工具，因为它们和我们自然的心智模型和基本过程的口头描述很相似。通过 GraphQL，你会把自己的业务领域通过定义 schema 建模成一张图；在你的 schema 里，你定义不同类型的节点以及它们之间是如何连接的。在客户端这边，这创建了一种类似于面向对象编程的模式：引用其他类型的类型。在服务器端，由于 GraphQL 定义了接口，你可以在任何后端自由的使用它（无论新旧！）。

## 共同语言
> 命名是构建直观接口中一个困难但重要的部分

考虑下把你的 GraphQL schema 作为一种给你的团队和用户的沟通的共同语言。为了建立一个好的 schema，你必须检查你用来描述业务的日常语言。举个例子，让我们尝试用简单的文字描述一个电子邮件应用程序：

* 一个用户可以有多个邮箱账号
* 每个电子邮件帐户都有地址、收件箱、草稿箱、删除的邮件和发送的邮件
* 每封邮件都有发送人、接收日期、主题和正文
* 没有收件人地址，用户无法发送电子邮件

命名是构建直观的接口中一个困难但重要的部分，所以花时间仔细地考虑对于你的问题域和用户什么事有意义的。您的团队应该对这些业务领域的规则形成共同的理解和共识，因为您需要为 GraphQL schema 中的节点和关系选择直观，耐用的名称。试着去想象一些你想执行的查询：

获取我所有帐户的收件箱里未读邮件的数量
```graphql
{
  accounts {
    inbox {
      unreadEmailCount
    }
  }
}
```

获取主账户的前二十封草稿邮件的“预览信息”
```graphql
{
  mainAccount {
    drafts(first: 20) {
      ...previewInfo
    }
  }
}

fragment previewInfo on Email {
  subject
  bodyPreviewSentence
}
```

## 业务逻辑层
> 你的业务逻辑层应作为执行业务域规则的唯一正确来源

你应该在哪里定义真正的业务逻辑？你应该在哪里验证，检查用户权限？就是在专门的业务逻辑层里。你的业务逻辑层应作为执行业务域规则的唯一正确来源。

![业务逻辑层图](/img/diagrams/business_layer.png)

在上图中，系统中的所有入口点（REST、GraphQL和RPC）都将使用相同的验证、授权和错误处理规则进行处理。

### 使用旧有的数据
> 希望构建一个描述客户端如何使用数据的 GraphQL schema，而不是镜像旧有的数据库 schema。

有时候，你会发现自己正在使用不能完全反映客户端消费数据的旧有的数据源。在这种情况下，更倾向于构建一个描述客户端如何使用数据的 GraphQL  schema，而不是镜像旧有的数据库 schema。

构建一个表达“是什么”而不是“怎么做”的 GraphQL schema。然后，您可以改进执行的具体细节，而不会破坏与旧客户端的接口。

## 一次一步
> 更频繁地进行验证和获得反馈

不要试图一次就做一个模型来构建整个业务域。 而是一次只构建一个场景所需的部分 schema。渐渐地拓展 schema，你要更频繁地进行验证和获得反馈，以便寻找到构建的正确解决方案。
