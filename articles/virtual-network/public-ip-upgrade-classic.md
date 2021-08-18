---
title: 将经典保留 IP 地址迁移到公共 IP 地址
titleSuffix: Azure Virtual Network
description: 本文介绍如何将经典部署模型保留 IP 升级到 Azure 资源管理器公共 IP 地址。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 05355db2d5227db3bf695b059df638e8bc07f4bd
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438959"
---
# <a name="migrate-a-classic-reserved-ip-address-to-a-public-ip-address"></a>将经典保留 IP 地址迁移到公共 IP 地址

若要利用 Azure 资源管理器中的新功能，可以将现有的公共静态 IP 地址（即保留 IP）从经典部署模型迁移到 Azure 资源管理器。  迁移的公共 IP 将是基本 SKU 类型。 

本文介绍如何将经典保留 IP 升级到基本公共 IP 地址。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 已在 Azure 订阅中注册迁移功能。 有关详细信息，请参阅[使用 PowerShell 迁移到资源管理器](../virtual-machines/migration-classic-resource-manager-ps.md)。
* 一个经典部署模型保留 IP 地址。
* 已安装用于 PowerShell 的 Azure PowerShell 服务管理模块。 有关详细信息，请参阅[安装 Azure PowerShell 服务管理模块](/powershell/azure/servicemanagement/install-azure-ps)。
* 已根据 Azure CLI 说明安装 Azure 经典 CLI。 有关详细信息，请参阅[安装 Azure 经典 CLI](/cli/azure/install-classic-cli)。

## <a name="azure-powershell-service-management-module"></a>Azure PowerShell 服务管理模块

在本部分，你将使用 Azure PowerShell 服务管理模块将经典保留 IP 迁移到 Azure 资源管理器静态公共 IP。

> [!NOTE]
> 必须从该保留 IP 地址关联到的所有云服务中删除该 IP 地址。

```azurepowershell-interactive
$validate = Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Validate
$validate.ValidationMessages

```

上一命令显示任何阻止迁移的警告和错误。 如果验证成功，则可以继续执行以下步骤来准备并提交迁移 ：

```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Prepare
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Commit
```
将使用迁移的保留 IP 的名称在 Azure 资源管理器中创建一个新资源组。 在上面的示例中，该资源组为 myReservedIP-Migrated。

## <a name="azure-classic-cli"></a>Azure 经典 CLI

在本部分，你将使用 Azure 经典 CLI 将经典保留 IP 迁移到 Azure 资源管理器静态公共 IP。

> [!NOTE]
> 必须从该保留 IP 地址关联到的所有云服务中删除该 IP 地址。

```azurecli-interactive
azure network reserved-ip validate migration myReservedIP

```
上一命令显示任何阻止迁移的警告和错误。 如果验证成功，则可以继续执行以下步骤来准备并提交迁移 ：

```azurecli-interactive
azure network reserved-ip prepare-migration myReservedIP
azure network reserved-ip commit-migration myReservedIP
```
将使用迁移的保留 IP 的名称在 Azure 资源管理器中创建一个新资源组。 在上面的示例中，该资源组为 myReservedIP-Migrated。

## <a name="next-steps"></a>后续步骤


有关 Azure 中的公共 IP 地址的详细信息，请参阅：

- [Azure 中的公共 IP 地址](public-ip-addresses.md)
- [创建公共 IP - Azure 门户](create-public-ip-portal.md)

