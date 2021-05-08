---
title: 如何从 Azure 应用程序网关入口控制器 Helm 迁移到 AGIC 加载项
description: 本文介绍如何从通过 Helm 部署的 AGIC 迁移到作为 AKS 加载项部署的 AGIC
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050828"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>从 AGIC Helm 迁移到 AGIC 加载项 

如果已通过 Helm 部署了 AGIC，但想要迁移到作为 AKS 加载项部署的 AGIC，以下步骤将帮助指导你完成迁移过程。 

## <a name="prerequisites"></a>先决条件 
在开始迁移过程之前，请注意以下事项。 
  - 是否在使用 [AGIC 加载项当前不支持](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)的 AGIC Helm 功能？
  - 是否在对每个 AKS 群集使用多个 AGIC Helm 部署？ 
  - 是否在使用多个 AGIC Helm 部署来针对一个应用程序网关？ 

如果你对上述任何问题的回答均为“是”，则 AGIC 加载项还不支持你的用例，在这种情况下，最好继续使用 AGIC Helm。 否则，请在非工作时间继续执行下面的迁移过程。 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>查找 AGIC Helm 当前针对的应用程序网关资源 ID 
导航到 AGIC Helm 部署所针对的应用程序网关。 复制并保存该应用程序网关的资源 ID。 稍后的步骤中将需要资源 ID。 可以在门户中在应用程序网关的属性选项卡下或通过 Azure CLI 找到资源 ID。 对于资源组“myResourceGroup”中名为“myApplicationGateway”的网关，以下示例将应用程序网关资源 ID 保存到“appgwId”。  

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>从 AKS 群集中删除 AGIC Helm
通过 Azure CLI 从群集中删除 AGIC Helm 部署。 首先需要删除 AGIC Helm 部署，然后才能启用 AGIC AKS 加载项。 请注意，在删除 AGIC Helm 部署的时间与启用 AGIC 加载项的时间之间在 AKS 群集中发生的任何更改都不会反映在应用程序网关上，因此，该迁移过程应在工作时间之外进行，以最大程度地减少影响。 应用程序网关将继续使用 AGIC 应用的最后一个配置，因此现有路由规则不会受到影响。 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>使用现有应用程序网关启用 AGIC 加载项 
现在可以在 AKS 群集中启用 AGIC 加载项，以通过 Azure CLI 或门户定位现有应用程序网关。 运行以下 Azure CLI 命令，在 AKS 群集中启用 AGIC 加载项。 该示例使用前面步骤中保存的应用程序网关资源 ID“appgwId”，在名为“myResourceGroup”的资源组中，在名为“myCluster”的群集中启用加载项。   


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

或者，可以使用此[链接](https://portal.azure.com/?feature.aksagic=true)在门户中导航到 AKS 群集，并在群集的“网络”选项卡中启用 AGIC 加载项。 当选择加载项应针对的应用程序网关时，从下拉菜单中选择现有的应用程序网关。 

![应用程序网关入口控制器门户](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>后续步骤
- [**应用程序网关入口控制器故障排除**](ingress-controller-troubleshoot.md)：AGIC 的故障排除指南 
- [**应用程序网关入口控制器注释**](ingress-controller-annotations.md)：AGIC 上的注释列表 