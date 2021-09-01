---
title: 在 Azure Spring Cloud 中缩放应用程序 | Microsoft Docs
description: 了解如何在 Azure 门户中通过 Azure Spring Cloud 缩放应用程序
ms.service: spring-cloud
ms.topic: how-to
ms.author: karler
author: karlerickson
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: 22bae9cd5a626f6a90427bd7522f2b99fa08ca0c
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015360"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>在 Azure Spring Cloud 中缩放应用程序

本文适用于：✔️ Java ✔️ C#

本文档演示如何在 Azure 门户中使用 Azure Spring Cloud 仪表板缩放任何微服务应用程序。

上下缩放应用程序，方法是：修改其虚拟 CPU (vCPU) 数目和内存容量。 水平缩放应用程序，方法是：修改应用程序实例的数目。

完成后，你会了解如何对服务中的每个应用程序进行快速的手动更改。 缩放在数秒内生效，不需任何代码更改或重新部署。

## <a name="prerequisites"></a>先决条件

若要遵循这些过程进行操作，需要：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 部署的 Azure Spring Cloud 服务实例。  按[有关如何通过 Azure CLI 来部署应用的快速入门](./quickstart.md)中的说明开始操作。
* 至少已在该服务实例中创建一个应用程序。

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>导航到 Azure 门户中的“缩放”页

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 转到 Azure Spring Cloud 的“概览”页。

1. 选择包含服务的资源组。

1. 在页面左侧菜单中的“设置”下选择“应用”选项卡。 

1. 选择要缩放的应用程序。 在此示例中，选择名为 **account-service** 的应用程序。 然后会看到应用程序的“概览”页。

1. 转到页面左侧菜单中的“设置”下的“缩放”选项卡。  你应会看到以下部分中所示的缩放属性的选项。

## <a name="scale-your-application"></a>缩放应用程序

如果修改缩放属性，请记住以下注意事项：

* **CPU**：每个应用程序实例的最大 CPU 数是 4。 一个应用程序的总 CPU 数是此处设置的值乘以应用程序实例数。

* **内存/GB**：每个应用程序实例的最大内存容量是 8 GB。 一个应用程序的总内存量是此处设置的值乘以应用程序实例数。

* **应用实例计数**：在“标准”层中，最多可横向扩展到 20 个实例。 此值更改微服务应用程序的单独运行实例的数目。

确保选择“保存”，以应用缩放设置。

![Azure 门户中的缩放服务](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

数秒钟后，所做的更改会显示在“概览”页上，更多详细信息在“应用程序实例”选项卡中提供。 缩放不需任何代码更改或重新部署。

## <a name="upgrade-to-the-standard-tier"></a>升级到标准层

如果在使用基本层，且受其中一项或多项[限制](./quotas.md)约束，则可升级到标准层。 若要执行此操作，请先选择“标准层”列，然后选择“升级”按钮，转到“定价层”菜单。 

## <a name="next-steps"></a>后续步骤

本示例介绍了如何手动缩放 Azure Spring Cloud 应用程序。 若要了解如何通过设置警报来监视应用程序，请参阅[设置自动缩放](./how-to-setup-autoscale.md)。

> [!div class="nextstepaction"]
> [了解如何设置警报](./tutorial-alerts-action-groups.md)
