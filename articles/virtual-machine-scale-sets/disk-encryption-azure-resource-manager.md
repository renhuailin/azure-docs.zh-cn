---
title: 使用 Azure 资源管理器模板创建并加密虚拟机规模集
description: 本快速入门介绍如何使用 Azure 资源管理器模板来创建和加密虚拟机规模集
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: c6f572ad472bcafe0ca1ff301f2d32a34a924424
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291810"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>使用 Azure 资源管理器加密虚拟机规模集

可以使用 Azure 资源管理器模板加密或解密 Linux 虚拟机规模集。

## <a name="deploying-templates"></a>部署模板

首先，选择适合你的方案的模板。

- [在运行的 Linux 虚拟机规模集上启用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-linux)

- [在运行的 Windows 虚拟机规模集上启用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-windows)

  - [使用 jumpbox 部署 Linux VM 的虚拟机规模集，并在 Linux 虚拟机规模集上启用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-linux-jumpbox)

  - [使用 jumpbox 部署 Windows VM 的虚拟机规模集，并在 Windows 虚拟机规模集上启用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-windows-jumpbox)

- [在运行的 Linux 虚拟机规模集上禁用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-linux)

- [在运行的 Windows 虚拟机规模集上禁用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-windows)

然后执行以下步骤：

1. 单击“部署到 Azure”  。
2. 填写必填字段，然后同意条款和条件。
3. 单击“购买”以部署模板。

## <a name="next-steps"></a>后续步骤

- [适用于虚拟机规模集的 Azure 磁盘加密](disk-encryption-overview.md)
- [使用 Azure CLI 加密虚拟机规模集](disk-encryption-cli.md)
- [使用 Azure PowerShell 加密虚拟机规模集](disk-encryption-powershell.md)
- [为 Azure 磁盘加密创建和配置密钥保管库](disk-encryption-key-vault.md)
- [将 Azure 磁盘加密与虚拟机规模集扩展排序配合使用](disk-encryption-extension-sequencing.md)
