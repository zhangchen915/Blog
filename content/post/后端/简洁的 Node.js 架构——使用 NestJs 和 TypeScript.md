# NestJS 整洁架构

> 你的架构应该告诉读者系统，而不是你在系统中使用的框架——Robert C.Martin

这种架构试图将一些领先的现代建筑，如[六边形建筑](https://en.wikipedia.org/wiki/Hexagonal_architecture_(software))、[洋葱建筑](http://jeffreypalermo.com/blog/the-onion-architecture-part-1/)和[尖叫建筑](http://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html)整合到一个主要的建筑中。

![Node 清理架构图 http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html](https://miro.medium.com/proxy/0*x318bLrEpHGA5GxA.jpg)

Node 整洁架构图，图片由 Robert C. Martin 提供

此图取自 Robert C. Martin 的[官方文章](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)。我建议在深入研究 Node 实现之前阅读他的文章。这是有关此架构的最佳源知识。

关于这张图以及如何阅读它的几句话（如果您还不理解它，请不要担心，我们将深入探讨本文中的每一层）：

- 层：每个环代表应用程序中的一个隔离层。
- 依赖：依赖方向是由外而内。这意味着实体层是独立的，而框架层（Web、UI 等）依赖于所有其他层。
- 实体：包含构建我们应用程序的所有业务实体。
- 用例：这是我们集中逻辑的地方。每个用例编排特定业务用例的所有逻辑（例如向系统添加新客户）。
- 控制器和演示者：我们的控制器、演示者和网关是中间层。您可以将它们视为用例的入口和出口。
- 框架：这一层具有所有特定的实现。数据库、Web 框架、错误处理框架等。Robert C. Martin 描述了这一层：“*这一层是所有细节所在。网络是一个细节。数据库是一个细节。我们把这些东西放在外面，它们不会造成什么伤害。”*

此时，您可能在对自己说：“数据库在外层，数据库是细节？” 数据库应该是我的核心层。

我喜欢这种架构，因为它背后有一个聪明的动机：

这种架构不是专注于框架和工具，而是专注于应用程序的业务逻辑。它是独立于框架的（尽可能地）。

这意味着无论您使用哪个数据库、开发框架、UI 或外部服务，应用程序的实体和业务逻辑都将始终保持不变。

我们可以在不改变逻辑的情况下改变以上所有内容。这就是使测试使用此架构构建的应用程序变得如此容易的原因。如果您还不了解这一点，请不要担心，我们将逐步探索它。

在本文中，我们将通过示例应用程序的示例，慢慢解开架构的不同层。

与任何其他架构一样，有许多不同的方法来实现它，每种方法都有自己的考虑和权衡。

在本文中，我将解释如何在 Node 中使用 NestJs 实现这种架构。在此过程中，我将尝试解释不同的实现注意事项。

让我们仔细看看示例应用程序。

# 示例应用程序

我们的示例应用程序将代表一个简单的微服务，它支持图书存储库上的 CRUD 操作。

在本文中，我们将逐层实现服务API。[您可以在GitHub 存储库](https://github.com/royib/clean-architecture-nestJS)中找到所有代码。本文包含部分代码，但最好的方法（在我看来）是在阅读本文时探索代码。

在我们的实现中，我们将使用[NetsJS](https://nestjs.com/)。

> Nest 是一个用于构建高效、可扩展的 Node.js 服务器端应用程序的框架。它使用现代 JavaScript，使用 TypeScript（保持与纯 JavaScript 的兼容性）构建，并结合了 OOP（面向对象编程）、FP（函数式编程）和 FRP（函数式反应式编程）的元素。— [Nest 官方 npm 存储库](https://www.npmjs.com/package/@nestjs/core)

基本上，Node.js 也可以让你以任何你想要的方式构建服务器端应用程序，这在某些情况下是一件好事，但也可能是一个问题，因为每个团队以不同的方式构建他的应用程序，每个人都有自己的意见。你的项目没有统一性，如果你不知道自己在做什么，它很快就会变得一团糟。NestJs 是一个自以为是的 Node.js 框架，它提供了以下工具：

- 依赖注入
- 代码与模块的分离
- 控制器
- 中间件
- 过滤器
- 警卫

虽然这不是 NestJs 的文章，但我会在本文中尝试解释一些基本原理。

# 实体和用例层

> 该层中的代码包含特定于应用程序的业务规则。它封装并实现了系统的所有用例。这些用例协调进出实体的数据流，并指导这些实体使用其企业范围的业务规则来实现用例的目标。—*罗伯特·C·马丁*

在应用程序的核心，我们有两层：

- 实体层：包含构建我们应用程序的所有业务实体。
- 用例层：包含我们的应用程序支持的所有业务场景。

![img](https://miro.medium.com/max/1400/1*JkSjWvfGMsxV4eEyXcxUMg.png)

核心层

我们将从内到外或与依赖规则相反的方向遍历架构。

在内部，我们有独立的核心层。这些层包含业务实体和业务逻辑。框架在这些领域是罕见的生物，这些层应该会发生变化，主要是由于业务的变化。

当我们去到外层时，我们会发现更多的框架和更多的代码由于技术或效率的原因而随着时间的推移而变化。

实体是一个独立的层，用例仅依赖于它们。

# 实体

我们应用程序中的业务实体是：

## 作者

- ID
- 名
- 姓

## 类型

- 姓名

## 书

- ID
- 标题
- 作者
- 类型
- 发布日期

该层是独立的，这意味着您将从不同的层导入模块。

该层不会受到服务、路由或控制器等外部更改的影响，而且它

[您可以在src/core/entities](https://github.com/royib/clean-architecture-nestJS/tree/main/src/core/entities)文件夹下的示例应用程序中找到所有实体代码

<iframe src="https://medium.com/media/91188cb258099d7c29e45a9c8f98ff85" allowfullscreen="" frameborder="0" height="483" width="692" title="整洁巢示例应用程序实体层" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 483px; position: absolute;"></iframe>

# 用例

这是我们集中逻辑的地方。每个用例编排特定业务用例的所有逻辑。我们的应用程序 API 需要支持这些用例（我选择了其中的一个示例）：

- 获取所有书籍的列表。
- 获取单本书详细信息。
- 添加新书。
- 添加新作者。

## 添加图书用例

让我们检查并深入研究“添加新书”用例。用例的主要职责是：

- 业务规则验证。
- 检查该书在 DB 中不存在。
- 创建一个新的书籍对象。
- 在 DB 中坚持我们的新书。
- 更新图书馆CRM系统。

通过查看用例的职责，我们可以看到用例有两个依赖项：

- 数据库服务：用例需要持久化书籍的详细信息，并检查它在系统中不存在。例如，此功能可以实现为调用 SQL 或 MongoDB 的类。
- CRM 服务：用例需要将新书通知图书馆 CRM 应用程序。此功能可以实现为调用外部系统（可以是任何系统）的服务。

一种选择是在用例本身中要求数据库和 CRM 服务的具体实现（例如，直接调用 SQL SDK）。此选项将使我们的数据库和 CRM 服务的具体实现与我们的用例紧密耦合。

数据库/CRM 服务的任何更改（如 SDK 更改）都会导致我们的用例发生更改。这个选项将打破我们的干净架构假设，即用例表达业务流程并且框架（如数据库和外部服务）对它们不可见。

我们的用例只知道实体和业务逻辑。此外，测试用例逻辑将变得更加困难。

[好的，让我们假设用例对 SQL 或MongoDB](https://www.mongodb.com/)等具体数据库一无所知。它仍然需要与它们交互来执行任务（比如在数据库中持久化一本书），但是如果它不知道它们，它到底怎么能做到呢？

解决方案是在用例和外部世界之间建立一个网关。

这就是抽象和依赖注入发挥作用的地方。我们不是创建对特定数据库或特定金融系统的依赖，而是创建对抽象的依赖。但是，到底什么是抽象？

抽象是在不实现服务的情况下创建服务蓝图的方式。我们只定义我们需要的服务功能。

![img](https://miro.medium.com/max/1400/1*q1m_l6R_IfD5o_ML10EjmQ.png)

抽象依赖

通过抽象，我们将定义用例和框架之间的契约。基本上，合同是所需服务的功能签名。例如，CRM 服务需要提供一个“通知”函数，该函数获取一个 Book 对象作为参数，并返回一个带有布尔值的 Promise。

## 用例依赖

让我们定义我们的抽象，我们需要：

- 数据服务抽象
- CRM服务抽象

我们的数据服务抽象需要暴露 3 个存储库：

- 图书资料库
- 作者库
- 流派存储库

这些存储库中的每一个都需要为我们提供 CRUD 功能，例如：find、findById、Insert、Update、Delete 等……

![img](https://miro.medium.com/max/1400/1*mSpjRw74EIwlXI5TD6dCiA.png)

用例抽象

## 主要抽象

[您可以在src/core/abstracts](https://github.com/royib/clean-architecture-nestJS/tree/main/src/core/abstracts)的示例应用程序中找到所有抽象。

首先让我们看一下我们的数据服务抽象：

<iframe src="https://medium.com/media/1dd4b88a24da2db86accc308b99f7765" allowfullscreen="" frameborder="0" height="241" width="692" title="通用存储库摘要" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 241px; position: absolute;"></iframe>

<iframe src="https://medium.com/media/9257dba292c757a124f862bbfae65c1b" allowfullscreen="" frameborder="0" height="263" width="692" title="数据服务抽象" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 263px; position: absolute;"></iframe>

- **通用存储库**——每个实体存储库都需要支持基本的 crud 操作。我创建了一个通用存储库，它将作为所有实体存储库的抽象类，如果我们需要为每个存储库提供不同的功能，您可以单独定义它们。我只选择了基本的存储库功能，在您的实际应用程序中，您可以定义所有/每个存储库所需的功能。
- **数据服务**- 公开所有实体存储库。

你可以在我写的一篇关于这个主题的文章中阅读更多关于它的信息——[使用 NestJS 实现通用存储库模式](https://betterprogramming.pub/implementing-a-generic-repository-pattern-using-nestjs-fb4db1b61cce)

我们的 CRM 服务抽象如下：

<iframe src="https://medium.com/media/a37c3bcb37145b6b01f22a28ba847b49" allowfullscreen="" frameborder="0" height="153" width="692" title="crm 服务抽象" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 153px; position: absolute;"></iframe>

## 用例代码

<iframe src="https://medium.com/media/cbadcafb9afe37085fc025d1715f4115" allowfullscreen="" frameborder="0" height="637" width="692" title="添加书籍用例" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 637px; position: absolute;"></iframe>

- 我们的添加书用例仅依赖于抽象。在构造函数中，我们只注入 IDataServices 和 ICrmServices。
- 我们使用我们的服务而不知道它们的真正实现是什么。
- 外部服务实现的更改不会影响我们的用例业务逻辑。

谁将这些服务的具体实现注入到我们的用例构造函数中？敬请期待，您很快就会在框架部分找到

# 控制器和演示者

> *“*该层中的软件是一组适配器，可将数据从对用例和实体最方便的格式转换为对某些外部机构（如数据库或网络）最方便的格式。例如，正是这一层将完全包含 GUI 的 MVC 架构。演示者、视图和控制器都属于这里”——Robert C. Martin

在上一节中，我们讨论了我们的核心业务层以及它们如何仅依赖于它们定义的抽象。现在我们将讨论适配器，因此您不会在这里看到任何业务逻辑或框架。

![img](https://miro.medium.com/max/1400/1*xKNJsh-x3F1u93KBrjhpBA.png)

控制器

我们的控制器、演示者和网关是中间层。您可以将它们视为将我们的用例与外部世界粘合并以其他方式返回的适配器。

外面的世界是谁？

如果您来自 MVC 世界，您可能听说过控制器。在经典的 MVC 中，您有某种指向不同控制器的路由机制。控制器的工作是响应用户输入，验证它，做一些业务逻辑的东西，并且通常改变应用程序的状态。

另一方面，演示者从某种存储库接收数据，并为视图/api 层格式化数据。

# 控制器

在干净的架构中，控制器的工作是：

- 接收用户输入——某种[DTO](https://en.wikipedia.org/wiki/Data_transfer_object)。
- 验证用户输入清理。
- 将用户输入转换为用例期望的模型。例如，进行日期格式和字符串到整数的转换。
- 调用用例并将新模型传递给它。

控制器是一个适配器，我们不需要任何业务逻辑，只需要数据格式化逻辑。

# 主持人

演示者将从应用程序存储库中获取数据，然后为客户端构建格式化响应。其主要职责包括：

- 格式化字符串和日期。
- 添加演示数据，如标志。
- 准备要在 UI 中显示的数据。

在我们的 Node 实现中，我们将一起实现控制器和演示器，就像我们在 MVC 项目中所做的那样。我们将使用 NestJs 功能来实现我们的[控制器](https://docs.nestjs.com/controllers)。我们将使用[验证管道验证我们的用户输入，并使用](https://docs.nestjs.com/techniques/validation)[转换管道](https://docs.nestjs.com/pipes)将我们的 DTO 转换为业务对象。

首先让我们创建我们的[DTO book 对象](https://github.com/royib/clean-architecture-nestJS/blob/main/src/core/dtos/book.dto.ts)。我们将从我们的 api 消费者那里收到这个对象。

<iframe src="https://medium.com/media/ec1abfebe3fb92bda8f6a36c59566b9f" allowfullscreen="" frameborder="0" height="439" width="692" title="书-dto.ts" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 439px; position: absolute;"></iframe>

[NestsJS 在后台使用类验证器](https://docs.nestjs.com/techniques/validation)，因此我们可以使用[类验证器装饰器](https://github.com/typestack/class-validator)来验证我们的 DTO 对象。我们还可以添加自定义验证器，例如我们可以添加一个自定义验证器来检查这本书是否不存在。

这是将返回给我们的消费者的响应对象：

<iframe src="https://medium.com/media/4c73f37c16fda7dab6abfdc7a25f7bc9" allowfullscreen="" frameborder="0" height="197" width="692" title="干净的巢创建书response.ts" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 197px; position: absolute;"></iframe>

Book 控制器代码如下：

<iframe src="https://medium.com/media/ff05c2ac91e67e8802ce922512461a0f" allowfullscreen="" frameborder="0" height="681" width="692" title="整洁巢书控制器.ts" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 681px; position: absolute;"></iframe>

- NestJs 在 bookDto 对象的后台进行验证
- 我们正在使用`bookFactoryService`将我们的 DTO 转换为商业书籍对象
- 致电我们的用例服务
- 创建对消费者的响应

# 构架

![img](https://miro.medium.com/max/880/0*Y0GCjnpQUbvOrQhb)

Node 整洁架构——框架

> “这一层是所有细节所在。网络是一个细节。数据库是一个细节。我们把这些东西放在外面，它们不会造成什么伤害”——罗伯特·C·马丁

在上一节中，我们讨论了适配器层以及它们如何充当我们用例的入口和出口。

现在，我们来谈谈框架层，这一层包括我们所有的具体实现，例如数据库、监控、计费、错误处理等。

到目前为止，我们只讨论了实体、业务逻辑、适配器，并且我们在没有使用任何框架的情况下完成了所有这些，除了 NestJs 是我们的构建块框架。你可以说我们写了一个“纯”代码。

在我们的示例项目中，框架实现为：

- Web 应用程序框架由 NestJs（基于 express 构建）实现。
- 数据库服务是使用[mongoose](https://mongoosejs.com/)实现的。
- CRM 服务是一种简单的模拟服务。

## 数据服务实施

Mongo Generic Repository 的代码如下：

<iframe src="https://medium.com/media/c9f09074713197d18a72f604854015ec" allowfullscreen="" frameborder="0" height="659" width="692" title="干净的巢mongo-repository.ts" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 659px; position: absolute;"></iframe>

- 存储库使用 mongoose 实现了我们的抽象`IGenericRepository`类
- `T`表示一个 db 实体，每个实体都具有所有预期的功能

Mongo 数据服务代码如下：

<iframe src="https://medium.com/media/32d229feff0830d606a2e716e97be07d" allowfullscreen="" frameborder="0" height="923" width="692" title="干净的巢mongo-data-services.ts" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 923px; position: absolute;"></iframe>

- `MongoDataServices`实现`IDataServices`抽象类
- 它根据需要公开 3 个存储库，每个实体一个
- 您可以[查看 NestJs 文档](https://docs.nestjs.com/techniques/mongodb)以更好地了解 mongo 实现，这超出了本文的范围。

客户关系管理服务：

<iframe src="https://medium.com/media/e47b3f292bd7e92ae128f263db75743f" allowfullscreen="" frameborder="0" height="307" width="692" title="干净的巢-crm-services.ts" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 307px; position: absolute;"></iframe>

# 依赖注入

正如我们之前所讨论的，我们的用例依赖于合约而不是实现。这些合约需要在运行时通过依赖注入来满足。

如果你不熟悉依赖注入的概念，我鼓励你看看 Fun Fun Function 博客上的两个很好的视频，它们完美地解释了这个主题：

- [依赖注入基础](https://www.youtube.com/watch?v=0X1Ns2NRfks)
- [控制反转](https://www.youtube.com/watch?v=-kpEP4JeEdc)

幸运的是，NestJs 内置了[依赖注入](https://docs.nestjs.com/fundamentals/custom-providers)功能。我们需要做的就是将我们的依赖注入到我们的服务构造函数中，并且在运行时 NestJs 将负责注入它们的实例。

请注意，我们将注入抽象服务，并且在运行时 DI 引擎将创建正确实现的实例。

在[模块声明](https://github.com/royib/clean-architecture-nestJS/blob/main/src/frameworks/data-services/mongo/mongo-data-services.module.ts)中，我们告诉 NestJs 对于每个抽象我们想要哪个实现。例如，当我们请求时，`DataServices`我们实际上想要获取`MongoDataServices`.

美妙之处在于我们的服务对它一无所知`MongoDataServices`并且仍然在运行时使用它。

数据服务模块代码如下：

<iframe src="https://medium.com/media/f99a4f1927d408de7e8a798fc8ccfa6d" allowfullscreen="" frameborder="0" height="373" width="692" title="干净的巢数据服务模块.ts" class="mk ah ai cx ae" scrolling="auto" style="box-sizing: inherit; width: 692px; top: 0px; left: 0px; height: 373px; position: absolute;"></iframe>

```
MongoDataServices`每次有人请求时，NestJs 都会注入实例`IDataServices
```

例如，如果我们想用 sql 替换 mongo，我们需要做的就是：

- 创建使用 sql 并遵循数据服务抽象的新数据服务类和存储库。
- 在模块文件中，告诉 Nest.js 使用我们新的 sql 数据服务

# 概括

在本文中，我们演示了如何构建健壮的逐层服务结构，将我们的核心业务逻辑与框架解耦。

我们可以轻松地用 Sql 替换我们的数据库或迁移到新的 CRM 系统，所有这些都无需触及我们的业务逻辑。

我们还可以通过仅接触框架层来对我们其中一个框架中的 SDK 更改做出反应。由于层的松散耦合架构，测试也变得容易。

在复杂的项目中，保持所有层的干净和整洁是困难的，有时甚至是乏味的。它总是关于架构中的权衡，我们时不时地需要妥协并打破我们的界限以获得另一个好处。

我相信，如果我们努力遵守这些规则，我们将在未来获得巨大的收益。
