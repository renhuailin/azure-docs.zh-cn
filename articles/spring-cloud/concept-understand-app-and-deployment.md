---
title: Azure Spring Cloud 中的应用和部署
description: 本主题介绍 Azure Spring Cloud 中应用程序和部署之间的区别。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: b5a48a562781e68d7b653d55eb63d8a9edc71228
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860818"
---
# <a name="app-and-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud 中的应用和部署

本文适用于：✔️ Java ✔️ C#

“应用”和“部署”是 Azure Spring Cloud 的资源模型中的两个关键概念。 在 Azure Spring Cloud 中，“应用”是一个商业应用或一个微服务的抽象。  部署为“应用”的一个版本的代码或二进制文件在“部署”中运行。  应用在 Azure Spring Cloud 服务实例（简称“服务实例”）中运行，如下所示。

 ![应用和部署](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

单个 Azure 订阅中可以有多个服务实例，但在构成商业应用或微服务的所有应用都驻留在单个服务实例中时，Azure Spring Cloud 服务最易于使用。

Azure Spring Cloud 标准层允许一个应用有一个生产部署和一个过渡部署，这样就可以轻松地在其上进行蓝/绿部署。

## <a name="app"></a>应用

以下特性/属性是在应用级别定义的。

| 功能 | 说明 |
|:--|:----------------|
| 公用</br>终结点 | 用于访问应用的 URL |
| 自定义</br>Domain | 保护自定义域的 CNAME 记录 |
| 服务</br>绑定 | 与其他 Azure 服务的现成连接 |
| Managed</br>标识 | 借助 Azure Active Directory 的托管标识，应用可以轻松访问其他受 Azure AD 保护的资源（如 Azure Key Vault） |
| 永久</br>存储 | 使数据能够在应用重启后保持不变的设置 |

## <a name="deployment"></a>部署

以下特性/属性是在部署级别定义的，会在切换生产/过渡部署时进行交换。

| 功能 | 说明 |
|:--|:----------------|
| CPU | 每个应用实例的 vCore 数 |
| 内存 | 每个应用实例的内存容量 (Gb)|
| 实例</br>计数 | 手动或自动设置的应用实例数 |
| 自动缩放 | 根据预定义的规则和计划自动缩放实例计数 |
| JVM</br>选项 | 设置 JVM 选项  |
| 环境</br>变量 | 设置环境变量 |
| 运行时</br>版本 | Java 8/Java 11|

## <a name="restrictions"></a>限制

* **应用必须有一个生产部署**：删除生产部署的操作会被 API 阻止。 删除生产部署之前，应将其切换到过渡部署。
* **一个应用最多可以有两个部署**：创建两个以上的部署的操作会被 API 阻止。 将新的二进制文件部署到现有的生产部署或过渡部署。
* **部署管理在基本层中不可用**：将标准层用于蓝-绿部署功能。

## <a name="see-also"></a>请参阅

* [在 Azure Spring Cloud 中设置过渡环境](./how-to-staging-environment.md)
