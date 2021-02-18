---
title: 升级 Azure Red Hat OpenShift 群集
description: 了解如何升级运行 OpenShift 4 的 Azure Red Hat OpenShift 群集
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro、openshift、az aro、red hat、cli
ms.openlocfilehash: 2c2c8607374493aa4e1135bbef0156b9c0b66fa6
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634969"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>升级 Azure Red Hat OpenShift (ARO) 群集

ARO 群集生命周期的一部分涉及到定期升级到最新的 OpenShift 版本。 必须应用最新的安全版本，或者通过升级来获取最新功能。 本文介绍如何使用 OpenShift Web 控制台升级 OpenShift 群集中的所有组件。

## <a name="before-you-begin"></a>在开始之前

本文要求你正在运行 Azure CLI 版本2.0.65。 运行 `az --version` 即可查找当前版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

本文假设你有权访问现有的 Azure Red Hat OpenShift 群集作为具有权限的用户 `admin` 。

## <a name="check-for-available-aro-cluster-upgrades"></a>检查是否有可用的 ARO 群集升级

在 OpenShift web 控制台中，选择 "**管理**  >  **群集设置**" 并打开 "**详细信息**" 选项卡。

如果群集的 **更新状态** 反映了 **可用的更新**，则可以更新群集。

## <a name="upgrade-your-aro-cluster"></a>升级 ARO 群集

在上一步中，从 web 控制台将 **通道** 设置为要更新到的版本的正确通道，如 `stable-4.5` 。

选择要更新到的版本，然后选择 " **更新**"。 你会看到更新状态更改为： `Update to <product-version> in progress` 。 可以通过观察操作员和节点的进度栏来查看群集更新的进度。

## <a name="next-steps"></a>后续步骤
- [了解如何使用 OC CLI 升级 ARO 群集](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- 可以在客户门户的 [勘误表部分](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) 找到有关可用 OpenShift 容器平台提议和更新的信息。
  
