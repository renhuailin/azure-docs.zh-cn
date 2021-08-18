---
title: Azure Red Hat OpenShift 故障排除
description: 排查和解决 Azure Red Hat OpenShift 的常见问题
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 1b68a84b599f8dab071b54f566721ecd7118294e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090594"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 故障排除

本文详细介绍创建或管理 Microsoft Azure Red Hat OpenShift 群集时遇到的一些常见问题。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>重新尝试创建失败的群集

如果使用 CLI 命令 `az` 创建 Azure Red Hat OpenShift 群集的操作失败，则重试创建操作将继续失败。
使用 `az openshift delete` 删除失败的群集，然后创建一个全新的群集。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>隐藏的 Azure Red Hat OpenShift 群集资源组

目前，通过 Azure CLI（`az openshift create` 命令）自动创建的 `Microsoft.ContainerService/openShiftManagedClusters` 资源在 Azure 门户中是隐藏的。 在“资源组”视图中，选中“显示隐藏的类型”来查看该资源组 。

![门户中“隐藏的类型”复选框的屏幕截图](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>创建群集时出现“找不到已注册的资源提供程序”错误

如果创建群集时出现 `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` 错误，则表明你使用的是预览版，现在需要[购买 Azure 虚拟机预留实例](https://aka.ms/openshift/buy)，才能使用正式发布的产品。 使用预留项，可通过预先支付完全托管的 Azure 服务费用来减少支出。 请查看[什么是 Azure 预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)，详细了解预留及其节省成本的方式。

## <a name="next-steps"></a>后续步骤

- 有关 OpenShift 故障排除的详细信息，请访问 [Red Hat OpenShift 帮助中心](https://help.openshift.com/)。

- 查看[有关 Azure Red Hat OpenShift 的常见问题](openshift-faq.yml)的解答。