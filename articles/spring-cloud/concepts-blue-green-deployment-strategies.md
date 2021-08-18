---
title: Azure Spring Cloud 中的蓝绿部署策略
description: 本主题介绍 Azure Spring Cloud 中的蓝绿部署的两种方法。
author: karlerickson
ms.author: yebronsh
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/12/2021
ms.custom: devx-track-java
ms.openlocfilehash: bce33a40933e5c40b5eab0948880d44f4b23edcd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728554"
---
# <a name="blue-green-deployment-strategies-in-azure-spring-cloud"></a>Azure Spring Cloud 中的蓝绿部署策略

本文介绍 Azure Spring Cloud 中的蓝绿部署支持。

Azure Spring Cloud (标准层和更高层) 允许每个应用有两个部署，其中只有一个用于接收生产流量。 此模式通常称为蓝绿部署。 Azure Spring Cloud 对蓝绿部署的支持，以及 [持续交付 (CD)](/devops/deliver/what-is-continuous-delivery) 管道和严格的自动测试，实现了敏捷且高度确信的应用程序部署。

## <a name="alternating-deployments"></a>交替部署

若要使用 Azure Spring Cloud 中实施蓝绿部署，最简单的方法是创建两个固定部署，并始终部署到未接收生产流量的部署。 使用 [Azure Pipelines 的 Azure Spring Cloud 任务](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)，只需将 `UseStagingDeployment` 标志设置为 `true`，即可以此方式部署。

下面介绍了交替部署方法的实践工作原理：

假设应用程序有两个部署：`deployment1` 和 `deployment2`。 当前，将 `deployment1` 设置为生产部署，并运行应用程序的版本 `v3`。

这使 `deployment2` 成为过渡部署。 因此，当持续交付 (CD) 管道准备运行时，它会将应用的下一个版本（版本 `v4`）部署到过渡部署 `deployment2` 上。

![两个部署：deployment1 接收生产流量](media/spring-cloud-blue-green-patterns/alternating-deployments-1.png)

`v4` 在 `deployment2` 上启动后，可以通过专用测试终结点对其运行自动测试和手动测试，以确保 `v4` 满足所有期望。

![V4 现在部署在 deployment2 上并经受测试](media/spring-cloud-blue-green-patterns/alternating-deployments-2.png)

确信 `v4` 时，可以将 `deployment2` 设置为生产部署，使其接收所有生产流量。 `v3` 将继续在 `deployment1` 上运行，以防发现需要回滚的严重问题。

![deployment2 上的 V4 现在接收生产流量](media/spring-cloud-blue-green-patterns/alternating-deployments-3.png)

现在，`deployment1` 是过渡部署 因此，部署管道的下一次运行将部署到 `deployment1` 上。

![V5 部署在 deployment1 上](media/spring-cloud-blue-green-patterns/alternating-deployments-4.png)

现在可以在 `deployment1` 的专用测试终结点上测试 `V5`。

![V5在 deployment1 上受测试](media/spring-cloud-blue-green-patterns/alternating-deployments-5.png)

最后，在 `v5` 满足所有期望后，再次将 `deployment1` 设置为生产部署， 以便 `v5` 接收所有生产流量。

![V5 在 deployment1 上接收流量](media/spring-cloud-blue-green-patterns/alternating-deployments-6.png)

### <a name="tradeoffs-of-the-alternating-deployments-approach"></a>交替部署方法的权衡

交替部署方法简单快捷，因为它不需要创建新部署。 但是，它确实存在几个缺点，如以下各节中所述。

#### <a name="persistent-staging-deployment"></a>永久性过渡部署

过渡部署始终保持运行，因而消耗 Azure Spring Cloud 实例的资源。 这实际上使 Azure Spring Cloud 上每个应用程序的资源需求增加一倍。

#### <a name="the-approval-race-condition"></a>审批争用条件

假设在以上应用程序中，发布管道需要手动审批，然后应用程序的每个新版本才能接收生产流量。 这造成了以下风险：当一个版本 (`v6`) 在过渡部署上等待手动审批时，部署管道会再次运行，并用更新的版本 (`v7`) 覆盖该版本。 然后，当 `v6` 获得批准时，部署 `v6` 的管道将过渡部署设置为生产。 但现在，部署在该部署上并接收流量将是未获得批准的 `v7`，而不是已批准的 `v6`。

![审批争用条件](media/spring-cloud-blue-green-patterns/alternating-deployments-race-condition.png)

您可以通过确保某个版本的部署流在所有以前版本的部署流均已完成或中止之后才能开始，来防止争用条件。 防止审批争用条件的另一种方法是使用下述命名部署方法。

## <a name="named-deployments"></a>命名部署

在命名部署方法中，将为每个要部署的应用程序的新版本创建一个新的部署。 应用程序在其定制部署上经过测试后，该部署将设置为生产部署。 包含以前版本的部署可以保持足够长的时间，直到确信不需要回滚。

在下图中，版本 `v5` 正在部署 `deployment-v5` 上运行。 部署名称现在包含版本，因为部署是为此版本专门创建的。 开始时没有其他部署。 现在，若要部署版本 `v6`，部署管道会创建一个新部署 `deployment-v6`，并在其上部署应用版本 `v6`。

![在命名部署上部署新版本](media/spring-cloud-blue-green-patterns/named-deployment-1.png)

没有并行部署另一版本的风险。 首先，Azure Spring Cloud 不允许在两个部署已存在的情况下创建第三个部署。 其次，即使可以有两个以上的部署，每个部署都由它所包含的应用程序的版本进行标识。 因此，协调 `v6` 的部署的管道仅尝试将 `deployment-v6` 设置为生产部署。

![新版本接收生产流量命名部署](media/spring-cloud-blue-green-patterns/named-deployment-2.png)

为新版本创建的部署收到生产流量后，将需要删除包含以前版本的部署，以便为将来的部署留出空间。 可能希望推迟几分钟或几小时的时间，以便可以在新版本中发现严重问题时回滚到以前的版本。

![回退期后，删除以前的部署](media/spring-cloud-blue-green-patterns/named-deployment-3.png)

### <a name="tradeoffs-of-the-named-deployments-approach"></a>命名部署方法的权衡

命名部署方法具有以下优点：

* 防止审批争用条件。
* 它通过删除不使用的过渡部署来减少资源消耗。

但是，也存在一些缺点，如下一节所述。

#### <a name="deployment-pipeline-failures"></a>部署管道失败

从部署开始到过渡部署被删除期间，运行部署管道的其他任何尝试都将失败。 管道将尝试创建新的部署，这将导致错误，因为每个 Azure Spring Cloud 应用程序只允许两个部署。

因此，部署业务流程必须能够稍后重试失败的部署过程，或能够确保每个版本的部署流会保持排队状态，直到所有以前版本的流全部完成。

## <a name="next-steps"></a>后续步骤

* [自动化到 Azure Spring Cloud 的应用程序部署](./how-to-cicd.md)
