---
title: Azure 虚拟机的用户数据
description: 使用户可在预配时将脚本或其他元数据插入 Azure 虚拟机。
services: virtual-machines-linux
author: ningk
manager: rogardle
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/30/2021
ms.author: ningk
ms.reviewer: azmetadata
ms.openlocfilehash: d149ead5cd45b55c846852f92342e6bbc56ba3cf
ms.sourcegitcommit: 19dfdfa85e92c6a34933bdd54a7c94e8b00eacfd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109665297"
---
# <a name="user-data-for-azure-virtual-machine"></a>Azure 虚拟机的用户数据 

用户数据使你能够将自己的脚本或元数据传递给虚拟机。

## <a name="what-is-user-data"></a>什么是“用户数据”

用户数据是在预配时要插入 Azure 虚拟机的一组脚本或其他元数据。 虚拟机上的任何应用程序均可在预配后从 Azure 实例元数据服务 (IMDS) 访问用户数据。 

用户数据是新版[自定义数据](./custom-data.md)，可提供额外优势：

* 预配后可从 Azure 实例元数据服务 (IMDS) 检索用户数据。

* 用户数据具有永久性。 用户数据在 VM 的生存期内可用。

* 可在不停止或重启 VM 的情况下从 VM 外部更新用户数据。

* 可使用 $expand 选项通过 GET VM/VMSS API 查询用户数据。

 此外，如果预配时未添加用户数据，仍可在预配后添加。

**安全警告**

> [!WARNING]
> 用户数据不会加密，VM 上的任何进程均可查询这些数据。 不应在用户数据中存储机密信息。

确保获取最新的 Azure 资源管理器 API 以使用新的用户数据功能。 内容在传递到 API 之前应以 base64 编码。 大小不能超过 64 KB。

## <a name="create-user-data-for-azure-vmvmss"></a>为 Azure VM/VMSS 创建用户数据

**在创建新 VM 时添加用户数据**

借助[此 Azure 资源管理器模板](https://aka.ms/ImdsUserDataArmTemplate)，使用用户数据创建新 VM。
如果使用的是 REST API，则对于单个 VM，请使用 PUT 请求将“UserData”添加到“属性”部分以创建 VM。

```json
{
  "name": "testVM",
  "location": "West US",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_A1"
    },
    "storageProfile": {
      "osDisk": {
        "osType": "Windows",
        "name": "osDisk",
        "createOption": "Attach",
        "vhd": {
          "uri": "http://myaccount.blob.core.windows.net/container/directory/blob.vhd"
        }
      }
    },
    "userData": "c2FtcGxlIHVzZXJEYXRh",
    "networkProfile": { "networkInterfaces" : [ { "name" : "nic1" } ] },
  }
}
```

**新建虚拟机规模集时添加用户数据**

创建虚拟机规模集时，使用 REST API 通过 PUT 请求将“UserData”添加到“virtualMachineProfile”部分。
```json
{
  "location": "West US",
  "sku": {
    "name": "Standard_A1",
    "capacity": 1
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "userData": "VXNlckRhdGE=",
      "osProfile": {
        "computerNamePrefix": "TestVM",
        "adminUsername": "TestUserName",
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "timeZone": "Dateline Standard Time"
        }
      },
      "storageProfile": {
        "osDisk": {
          "createOption": "FromImage",
          "caching": "ReadOnly"
        },
        "imageReference": {
          "publisher": "publisher",
          "offer": "offer",
          "sku": "sku",
          "version": "1.2.3"
        }
      },
      "networkProfile": {"networkInterfaceConfigurations":[{"name":"nicconfig1","properties":{"ipConfigurations":[{"name":"ip1","properties":{"subnet":{"id":"vmssSubnet0"}}}]}}]},
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "https://crputest.blob.core.windows.net"
        }
      }
    },
    "provisioningState": 0,
    "overprovision": false,
    "uniqueId": "00000000-0000-0000-0000-000000000000"
  }
}
```


## <a name="retrieving-user-data"></a>检索用户数据

VM 内运行的应用程序可以从 IMDS 终结点检索用户数据。 有关详细信息，请参阅[此处的 IMDS 示例代码。](./linux/instance-metadata-service.md?tabs=linux#get-user-data)

客户可以使用 \$expand=userData 终结点（请求正文可留空）通过 REST API 检索现有的用户数据值。

单个 VM：

`GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachines/{VMName}?$expand=userData"`

虚拟机规模集：

`GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMSSName}?$expand=userData"`

虚拟机规模集 VM：

` GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMSSName}/virtualmachines/{vmss instance id}?$expand=userData" `

## <a name="updating-user-data"></a>更新用户数据

对于 Rest API，可通过普通 PUT 或 PATCH 请求来更新用户数据。 无需停止或重启 VM 即可更新用户数据。

`PUT
"/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/ virtualMachines/{VMName}
`

`PATCH
"/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/ virtualMachines/{VMName}
`

这些请求中的 VM.Properties 应包含所需的 UserData 字段，如下所示：

```json
"properties": {
        "hardwareProfile": {
          "vmSize": "Standard_D1_v2"
        },
        "storageProfile": {
          "imageReference": {
            "sku": "2016-Datacenter",
            "publisher": "MicrosoftWindowsServer",
            "version": "latest",
            "offer": "WindowsServer"
          },
          "osDisk": {
            "caching": "ReadWrite",
            "managedDisk": {
              "storageAccountType": "Standard_LRS"
            },
            "name": "vmOSdisk",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
              "properties": {
                "primary": true
              }
            }
          ]
        },
        "osProfile": {
          "adminUsername": "{your-username}",
          "computerName": "{vm-name}",
          "adminPassword": "{your-password}"
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
            "storageUri": "http://{existing-storage-account-name}.blob.core.windows.net",
            "enabled": true
          }
        },
        "userData": "U29tZSBDdXN0b20gRGF0YQ=="
      } 
```
> [!NOTE]
> 如果在这种情况下为“userData”传入空字符串，则将删除用户数据。

## <a name="user-data-and-custom-data"></a>用户数据和自定义数据

自定义数据将继续以现有方式工作。 请注意，无法从 IMDS 检索自定义数据。 

## <a name="adding-user-data-to-an-existing-vm"></a>将用户数据添加到现有 VM 

如果现有 VM/VMSS 中不包含用户数据，仍可使用更新命令将用户数据添加到此 VM，如[“更新用户数据”](#updating-user-data)部分所述。 确保升级为最新版的 Azure 资源管理器 API。

## <a name="next-steps"></a>后续步骤 
 
试用 [Azure 实例元数据服务](./linux/instance-metadata-service.md)，了解如何从其终结点获取 VM 实例元数据和用户数据。 
