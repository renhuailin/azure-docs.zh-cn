---
title: 使用 Azure CLI 创建和加密 Windows VM
description: 本快速入门介绍如何使用 Azure CLI 创建和加密 Windows 虚拟机
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6db23229cf0f3521c5855226ac96ad7d52c0fc87
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693432"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>快速入门：使用 Azure CLI 创建和加密 Windows VM

适用于：:heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门演示如何使用 Azure CLI 创建和加密 Windows Server 2016 虚拟机 (VM)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0.30 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group#az_group_create)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：  

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 以下示例创建一个名为 myVM 的 VM。 此示例使用 azureuser 作为管理用户名，使用 myPassword12 作为密码。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

创建 VM 和支持资源需要几分钟时间。 以下示例输出表明 VM 创建操作已成功。

```console
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>创建为加密密钥配置的密钥保管库

Azure 磁盘加密将其加密密钥存储在 Azure 密钥保管库中。 使用 [az keyvault create](/cli/azure/keyvault#az_keyvault_create) 创建密钥保管库。 要使密钥保管库能够存储加密密钥，请使用 --enabled-for-disk-encryption 参数。
> [!Important]
> 每个密钥保管库必须具有唯一的名称。 以下示例创建名为“myKV”的密钥保管库，但你必须将其命名为不同的名称。

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>加密虚拟机

使用 [az vm encryption](/cli/azure/vm/encryption) 加密 VM，为 --disk-encryption-keyvault 参数提供唯一的密钥保管库名称。

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

可以使用 [az vm show](/cli/azure/vm/encryption#az_vm_encryption_show) 验证 VM 是否已启用加密

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

将在返回的输出中看到以下内容：

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和密钥保管库，可以使用 [az group delete](/cli/azure/group) 命令将其删除。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个虚拟机，创建了一个启用加密密钥的密钥保管库，并对 VM 进行了加密。  请继续学习下一篇文章，详细了解 IaaS VM 的 Azure 磁盘加密先决条件。

> [!div class="nextstepaction"]
> [Azure 磁盘加密概述](disk-encryption-overview.md)
