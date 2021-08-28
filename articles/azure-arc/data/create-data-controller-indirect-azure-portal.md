---
title: 从 Azure 门户以间接模式创建 Azure Arc 数据控制器
description: 从 Azure 门户以间接模式创建 Azure Arc 数据控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: ce593d8f4e29ecbe0873381b2a1cf028f03e212b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745884"
---
# <a name="create-azure-arc-data-controller-from-azure-portal---indirect-connectivity-mode"></a>从 Azure 门户创建 Azure Arc 数据控制器 - 间接连接模式


## <a name="introduction"></a>简介

可以使用 Azure 门户以间接连接模式创建 Azure Arc 数据控制器。

Azure Arc 的许多创建体验都是从 Azure 门户中开始，即使要创建或管理的资源在 Azure 基础结构之外也是如此。 在这些情况下，尤其是当 Azure 与环境之间没有直接连接时，用户体验模式是使用 Azure 门户生成脚本，然后可以在环境中下载并执行该脚本，以建立与 Azure 的安全连接。 例如，已启用 Azure Arc 的服务器遵循这种用于[创建已启用 Arc 的服务器](../servers/onboard-portal.md)的模式。

使用已启用 Azure Arc 的数据服务的间接连接模式时，可以使用 Azure 门户为你生成笔记本，然后将其下载并针对 Kubernetes 群集在 Azure Data Studio 中运行它。 

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

使用直接连接模式时，可以直接从 Azure 门户预配数据控制器。 可阅读有关[连接模式](connectivity.md)的更多信息。

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>使用 Azure 门户创建 Azure Arc 数据控制器

按照以下步骤使用 Azure 门户和 Azure Data Studio 创建 Azure Arc 数据控制器。

1. 首先登录到 [Azure 门户市场](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)。  系统将过滤市场搜索结果，显示“Azure Arc 数据控制器”。
1. 如果第一步没有输入搜索条件。 请输入搜索结果，然后单击“Azure Arc 数据控制器”。
1. 从市场中选择“Azure 数据控制器”磁贴。
1. 单击“创建”按钮。
1. 选择间接连接模式。 详细了解“[连接模式和要求](./connectivity.md)”。 
1. 查看创建 Azure Arc 数据控制器的要求，并安装任何缺少的必备软件，如 Azure Data Studio 和 kubectl。
1. 单击“下一步：数据控制器详细信息”按钮。
1. 选择订阅、资源组和 Azure 位置，就像在 Azure 门户中创建任何其他资源一样。 在这种情况下，选择的 Azure 位置将是存储资源元数据的位置。  资源本身将创建在选择的任何基础结构上。 资源不需要在 Azure 基础结构上。
1. 输入数据控制器的名称。

1. 单击“在 Azure Studio 中打开”按钮。
1. 在下一个屏幕上，用户将看到所选内容的摘要和生成的笔记本。  可以单击“在 Azure Data Studio 中打开链接”按钮以在 Azure Data Studio 中打开生成的笔记本。
1. 在 Azure Data Studio 中打开笔记本，然后单击顶部的“全部运行”按钮。
1. 按照笔记本中的提示和说明完成数据控制器的创建。

## <a name="monitoring-the-creation-status"></a>监视创建状态

创建控制器需要几分钟才能完成。 可使用以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设你创建了一个名为“arc”的数据控制器和 Kubernetes 命名空间。  如果使用其他命名空间/数据控制器名称，则可以将“arc”替换为自己的名称。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可运行如下命令来检查任何特定 Pod 的创建状态。  这对于排查问题特别有用。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>排查创建问题

如果在创建过程中遇到任何问题，请参阅[故障排除指南](troubleshoot-guide.md)。