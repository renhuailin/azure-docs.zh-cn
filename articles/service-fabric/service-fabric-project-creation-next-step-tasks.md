---
title: 创建 Service Fabric 项目的后续步骤
description: 了解刚才在 Visual Studio 中创建的应用程序项目。  了解如何使用教程生成服务以及有关开发 Service Fabric 服务的详细信息。
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 59c8eb0737d2cef1c4b1df34d673b74944fef4e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97760429"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Service Fabric 应用程序和后续步骤
已创建 Azure Service Fabric 应用程序。 本文包含许多资源、你可能感兴趣的更多信息以及可能的[后续步骤](#next-steps)。

新用户可能会发现[教程、演练和示例](#get-started-with-tutorials-walk-throughs-and-samples)有所帮助。 检查[所创建应用程序项目的结构](#the-application-project)也很有用。 还包括 Service Fabric [编程模型](#learn-more-about-the-programming-models)、[服务通信](#learn-about-service-communication)、[应用程序安全](#learn-about-configuring-application-security)和[应用程序生命周期](#learn-about-the-application-lifecycle)的说明。

较有经验的用户可能会发现 Service Fabric [最佳做法](#learn-about-best-practices)部分对了解如何以最大效率利用平台和构造应用程序非常有用。

如有疑问或反馈，或想要报告问题，请参阅[相应部分](#have-questions-or-feedback--need-to-report-an-issue)。

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>快速入门教程、演练和示例
准备好开始了吗？  

按照 .NET 应用程序教程进行操作。 了解如何使用 ASP.NET Core 前端和监控状态的后端[构建应用](service-fabric-tutorial-create-dotnet-app.md)，如何为群集[部署应用程序](service-fabric-tutorial-deploy-app-to-party-cluster.md)、[配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) 以及如何[设置监视和诊断](service-fabric-tutorial-monitoring-aspnet.md)。

或尝试以下某个演练并创建第一个...
- [基于 Windows 的 C# Reliable Services 服务](service-fabric-reliable-services-quick-start.md) 
- [基于 Windows 的 C# Reliable Actors 服务](service-fabric-reliable-actors-get-started.md) 
- [基于 Windows 的来宾可执行服务](quickstart-guest-app.md) 
- [Windows 容器应用程序](service-fabric-get-started-containers.md) 

还可以尝试[示例应用程序](/samples/browse/?products=azure)。

## <a name="the-application-project"></a>应用程序项目
每个新应用程序都包含一个应用程序项目。 根据选择的服务类型，可能有一个或两个附加的项目。

应用程序项目包括：

* 对构成应用程序的服务的引用集。
* 三个发布配置文件（1-本地节点、5-本地节点和云），可用于维护首选项以适应不同的环境，诸如与群集终结点相关的首选项，以及是否按默认执行升级部署。
* 三个应用程序参数文件（同上），可用于维护环境特定的应用程序配置，例如，要为服务创建的分区数目。 了解如何[为多个环境配置应用程序](service-fabric-manage-multiple-environment-app-configuration.md)。
* 可使用部署脚本从命令行部署应用程序，或者通过自动持续集成和部署管道来部署应用程序。 深入了解如何[使用 PowerShell 部署应用程序](service-fabric-deploy-remove-applications.md)。
* 用于描述应用程序的应用程序清单。 可以在 ApplicationPackageRoot 文件夹下查找清单。 深入了解[应用程序和服务清单](service-fabric-application-model.md)。

## <a name="learn-more-about-the-programming-models"></a>了解有关编程模型的详细信息
Service Fabric 提供了多种方法来编写和管理服务。  以下是[无状态与有状态 Reliable Services](service-fabric-reliable-services-introduction.md)、[Reliable Actors](service-fabric-reliable-actors-introduction.md)、[容器](service-fabric-containers-overview.md)、[来宾可执行文件](service-fabric-guest-executables-introduction.md)和[无状态与有状态 ASP.NET Core 服务](service-fabric-reliable-services-communication-aspnetcore.md)的概述和概念信息。

## <a name="learn-about-service-communication"></a>了解服务通信相关信息
Service Fabric 应用程序由不同的服务组成，其中每个服务执行专门任务。 这些服务之间可以相互通信，并且存在连接到服务并与之通信的群集外客户端应用程序。 了解如何在 Service Fabric 中[设置与服务进行的通信以及服务之间的通信](service-fabric-connect-and-communicate-with-services.md)。 

## <a name="learn-about-configuring-application-security"></a>了解配置应用程序安全性的相关信息
可以保护群集中以不同用户帐户运行的应用程序。 使用用户帐户进行部署时，Service Fabric 还有助于保护应用程序所使用的资源，例如文件、目录和证书。 这样，即使是在共享托管环境中，运行应用程序会更加安全。  了解如何[配置应用程序的安全策略](service-fabric-application-runas-security.md)。

应用程序可能包含敏感信息，例如存储连接字符串、密码或其他不应以明文形式处理的值。 了解如何[管理应用程序中的机密](service-fabric-application-secret-management.md)。

## <a name="learn-about-the-application-lifecycle"></a>了解应用程序生命周期的相关信息
与其他平台一样，Service Fabric 应用程序通常将经历以下几个阶段：设计、开发、测试、部署、升级、维护和删除。 [本文](service-fabric-application-lifecycle.md)提供了有关 API 的概述，并且介绍了在 Service Fabric 应用程序生命周期的各个阶段，它们如何被不同角色所使用。

## <a name="learn-about-best-practices"></a>了解最佳做法
Service Fabric 有许多介绍[最佳做法](./service-fabric-best-practices-overview.md)的文章。 利用此信息来帮助确保群集和应用程序尽可能正常地运行。
涵盖的主题包括：
* [安全性](./service-fabric-best-practices-security.md)
* [网络](./service-fabric-best-practices-networking.md)
* [计算规划和缩放](./service-fabric-best-practices-capacity-scaling.md)
* [基础结构即代码](./service-fabric-best-practices-infrastructure-as-code.md)
* [监视和诊断](./service-fabric-best-practices-monitoring.md)
* [应用程序设计](./service-fabric-best-practices-applications.md)

还包括一个[生产就绪情况清单](./service-fabric-production-readiness-checklist.md)，其中以一种易于使用的格式集成了所有最佳做法信息。

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>有问题或反馈？  需要报告问题？
请浏览[常见问题](service-fabric-common-questions.md)，了解有关 Service Fabric 的功能及用法方面的解答。

[疑难解答指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)有助于诊断和解决 Service Fabric 群集中的常见问题。

[支持选项](service-fabric-support.md)列出了 StackOverflow 和 MSDN 的相关论坛和选项，可通过论坛提问并通过选项报告问题、获得支持和提交产品反馈。


## <a name="next-steps"></a>后续步骤
- [在 Azure 中创建 Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)。
- 使用 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 可视化群集（包括已部署的应用程序和物理布局）。
- [对服务进行版本控制和升级](service-fabric-application-upgrade-tutorial.md)