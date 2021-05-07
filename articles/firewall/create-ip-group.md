---
title: 在 Azure 防火墙中创建 IP 组
description: 可以通过 IP 组对 Azure 防火墙规则的 IP 地址进行分组和管理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394520"
---
# <a name="create-ip-groups"></a>创建 IP 组

可以通过 IP 组对 Azure 防火墙规则的 IP 地址进行分组和管理。 IP 组可以有单个或多个 IP 地址、或一个或多个 IP 地址范围。

## <a name="create-an-ip-group---azure-portal"></a>创建 IP 组 - Azure 门户

使用 Azure 门户创建 IP 组：

1. 在 Azure 门户主页上，选择“创建资源”。
1. 在搜索框中，输入“IP 组”，然后选择“IP 组”。
1. 选择“创建”。
1. 选择订阅。
1. 选择一个资源组或新建一个资源组。
1. 为 IP 组输入一个唯一名称，然后选择区域。
1. 在完成时选择“下一步:IP 地址”。
1. 键入一个 IP 地址、多个 IP 地址或 IP 地址范围。

   可以通过两种方式输入 IP 地址：
   - 可以手动输入操作
   - 也可以从文件导入

   若要从文件导入，请选择“从文件导入”。 可以将文件拖到框中，也可以选择“浏览文件”。 如有必要，可以查看和编辑已上传的 IP 地址。

   键入 IP 地址时，门户会对其进行验证，以检查重叠、重复和格式设置问题。

1. 完成后，选择“查看 + 创建”。
1. 选择“创建”。

## <a name="create-an-ip-group---azure-powershell"></a>创建 IP 组 - Azure PowerShell

此示例使用 Azure PowerShell 创建包含地址前缀和 IP 地址的 IP 组：

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>创建 IP 组 - Azure CLI

此示例使用 Azure CLI 创建包含地址前缀和 IP 地址的 IP 组：

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>后续步骤

- [了解 IP 组详细信息](ip-groups.md)
