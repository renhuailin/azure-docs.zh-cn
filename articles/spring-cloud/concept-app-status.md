---
title: Azure Spring Cloud 中的应用状态
description: 了解 Azure Spring Cloud 中的应用状态类别
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 2ab7e8b548df93c5b28a3265e71ff383765bcd0d
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015448"
---
# <a name="app-status-in-azure-spring-cloud"></a>Azure Spring Cloud 中的应用状态

本文适用于：✔️ Java ✔️ C#

Azure Spring Cloud UI 提供有关正在运行的应用程序状态信息。  订阅中的每个资源组都有一个“应用”选项，其中显示了应用程序类型的常规状态。  对于每种应用程序类型，均显示“应用程序实例”。

## <a name="apps-status"></a>应用状态

若要查看应用程序类型的常规状态，请在资源组的左侧导航窗格中选择“应用”。 结果显示已部署应用的状态：

* **预配状态** 显示部署的预配状态
* **运行实例** 显示正在运行的应用实例数/所需的应用实例数。 如果应停止应用，此列将显示“已停止”。
* **已注册实例** 显示已向 eureka 注册的应用实例数/所需的应用实例数。 如果应停止应用，此列将显示“已停止”。

![应用状态](media/spring-cloud-concept-app-status/apps-ui-status.png)

**部署状态将报告为以下某个值：**

| 枚举 | 定义 |
|:--:|:----------------:|
| 正在运行 | 应运行部署。 |
| 已停止 | 应停止部署。 |

**只能从 CLI 访问预配状态。将报告为以下某个值：**

| 枚举 | 定义 |
|:--:|:----------------:|
| Creating | 正在创建资源。 |
| 更新 | 正在更新资源。 |
| 成功 | 已成功提供资源并部署二进制文件。 |
| 失败 | 未能实现 Succeeded 目标。 |
| 正在删除 | 正在删除此资源。 这会阻止操作，并且资源在此状态下不可用。 |

## <a name="app-instances-status"></a>应用实例状态

若要查看已部署应用的特定实例的状态，请在“应用”UI 中选择应用的“名称”。  将会显示以下结果：

* **状态**：实例是否正在运行或其状态如何
* **DiscoveryStatus**：Eureka 服务器中应用实例的注册状态

![应用实例状态](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**实例状态将报告为以下某个值：**

| 枚举 | 定义 |
|:--:|:----------------:|
| 正在启动 | 该二进制文件已成功部署到给定的实例。 启动 jar 文件的实例可能会失败，因为 jar 无法正常运行。 |
| 正在运行 | 实例正常运行。 |
| 失败 | 应用实例多次重试后未能启动用户的二进制文件。 |
| 正在终止 | 应用实例正在关闭。 |

**实例的发现状态将报告为以下某个值：**

| 枚举 | 定义 |
|:--:|:----------------:|
| UP | 已将应用实例注册到 Eureka 并准备好接收流量 |
| OUT_OF_SERVICE | 应用实例已注册到 Eureka 并且能够接收流量。 但会有意关闭流量。 |
| DOWN | 应用实例未注册到 Eureka 或已注册但无法接收流量。 |

## <a name="see-also"></a>请参阅

* [准备要部署到 Azure Spring Cloud 中的 Spring 或 Steeltoe 应用程序](how-to-prepare-app-deployment.md)
