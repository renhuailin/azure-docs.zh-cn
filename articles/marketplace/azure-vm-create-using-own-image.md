---
title: 使用自己的映像在 Azure 市场中创建 Azure 虚拟机产品/服务
description: 了解如何使用自己的映像将虚拟机产品/服务发布到 Azure 市场。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200462"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>如何使用自己的映像创建虚拟机

本文介绍如何创建和部署用户提供的虚拟机 (VM) 映像。

> [!NOTE]
> 在开始此过程之前，请先查看 Azure VM 产品/服务的[技术要求](marketplace-virtual-machines.md#technical-requirements)，包括虚拟硬盘 (VHD) 要求。

若要使用已批准的基础映像，请按照[基于已批准的基础映像创建 VM 映像](azure-vm-create-using-approved-base.md)中的说明操作。

## <a name="configure-the-vm"></a>配置 VM

本部分介绍如何对 Azure VM 进行大小调整、更新和通用化。 必须执行这些步骤才能准备好要在 Azure 市场部署的 VM。

### <a name="size-the-vhds"></a>调整 VHD 大小

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>安装最新的更新

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>执行其他安全检查

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>执行自定义配置和计划任务

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>通用化映像

Azure 市场中的所有映像必须可采用一般形式重复使用。 若要实现这种可重用性，必须将操作系统 VHD 通用化，此操作会从 VM 中删除所有特定于实例的标识符以及软件驱动程序。

## <a name="bring-your-image-into-azure"></a>将映像引入 Azure

可通过三种方式将映像引入 Azure：

1. 将 VHD 上传到共享映像库 (SIG)。
1. 将 VHD 上传到 Azure 存储帐户。
1. 从托管映像中提取 VHD（如果使用映像生成服务）。

以下三个部分将介绍这些选项。

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>选项 1：将 VHD 上传为共享映像库

1. 将 VHD 上传到存储帐户。
2. 在 Azure 门户上搜索“部署自定义模板”。
3. 选择“在编辑器中生成自己的模板”。
4. 复制以下 Azure 资源管理器 (ARM) 模板。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. 将该模板粘贴到编辑器中。

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="VM 的示例代码屏幕。":::

1. 选择“保存”。
1. 使用下表中的参数填写随后出现的屏幕中的字段。

| 参数 | 说明 |
| --- | --- |
| sourceStorageAccountResourceId | Blob VHD 所在的源存储帐户的资源 ID。<br><br>若要获取该资源 ID，请在 **Azure 门户** 上转到你的 **存储帐户**，转到“属性”，然后复制“资源 ID”值。  |
| sourceBlobUri | OS 磁盘 VHD Blob（必须在提供的存储帐户中）的 Blob URI。<br><br>若要获取 Blob URL，请在 **Azure 门户** 上转到你的 **存储帐户**，转到你的 **Blob**，然后复制“URL”值。 |
| sourceBlobDataDisk0Uri | 数据磁盘 VHD Blob（必须在提供的存储帐户中）的 Blob URI。 如果你没有数据磁盘，请从模板中删除此参数。<br><br>若要获取 Blob URL，请在 **Azure 门户** 上转到你的 **存储帐户**，转到你的 **Blob**，然后复制“URL”值。 |
| sourceBlobDataDisk1Uri | 其他数据磁盘 VHD Blob（必须在提供的存储帐户中）的 Blob URI。 如果你没有其他数据磁盘，请从模板中删除此参数。<br><br>若要获取 Blob URL，请在 **Azure 门户** 上转到你的 **存储帐户**，转到你的 **Blob**，然后复制“URL”值。 |
| galleryName | 共享映像库的名称 |
| galleryImageDefinitionName | 映像定义的名称 |
| galleryImageVersionName | 要创建的映像版本的名称，格式为：`<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="显示“自定义部署”窗口。":::

8. 选择“查看 + 创建”。 完成验证后，选择“创建”。

> [!TIP]
> 发布者帐户必须拥有“所有者”访问权限才能发布 SIG 映像。 如果需要，请遵循以下步骤授予访问权限：
>
> 1. 转到共享映像库 (SIG)。
> 2. 在左侧窗格中选择“访问控制”(IAM)。
> 3. 依次选择“添加”、“添加角色分配”。 
> 4. 对于“角色”，请选择“所有者”。 
> 5. 对于“将访问权限分配给”，请选择“用户、组或服务主体”。 
> 6. 输入映像发布者的 Azure 电子邮件。
> 7. 选择“保存”。<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="已显示“添加角色分配”窗口。":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>选项 2：将 VHD 上传到存储帐户

根据[准备要上传到 Azure 的 Windows VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 或者[创建并上传 Linux VHD](../virtual-machines/linux/create-upload-generic.md) 中所述，配置并准备要上传的 VM。

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>选项 3：从托管映像中提取 VHD（如果使用映像生成服务）

如果你正在使用 [Packer](https://www.packer.io/) 等映像生成服务，可能需要从映像中提取 VHD。 此提取操作没有捷径。 必须先创建一个 VM，然后从 VM 磁盘提取 VHD。

## <a name="create-the-vm-on-the-azure-portal"></a>在 Azure 门户中创建 VM

遵循以下步骤在 [Azure 门户](https://ms.portal.azure.com/)中创建基础 VM 映像。

1. 登录 [Azure 门户](https://ms.portal.azure.com/)。
2. 选择“虚拟机”。
3. 选择“+ 添加”以打开“创建虚拟机”屏幕 。
4. 从下拉列表中选择映像，或选择“浏览所有公共和专用映像”，以搜索或浏览所有可用的虚拟机映像。
5. 若要创建 Gen 2 VM，请转到“高级”选项卡并选择“Gen 2”选项  。

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="选择 Gen 1 或 Gen 2。":::

6. 选择要部署的 VM 的大小。

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="为所选映像选择建议的 VM 大小。":::

7. 提供创建 VM 所需的其他详细信息。
8. 选择“查看 + 创建”可查看选择。 在出现“已通过验证”消息时，选择“创建” 。

Azure 随即开始预配所指定的虚拟机。 通过选择左侧菜单中的“虚拟机”选项卡来跟踪预配进度。 在创建之后，虚拟机的状态会更改为“正在运行”。

## <a name="connect-to-your-vm"></a>连接到 VM

请参阅以下文档连接到 [Windows](../virtual-machines/windows/connect-logon.md) 或 [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM。

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>后续步骤

- [测试 VM 映像](azure-vm-image-test.md)，以确保它满足 Azure 市场发布要求。 此为可选项。
- 如果你不想测试 VM 映像，请登录到[合作伙伴中心](https://partner.microsoft.com/)并发布 SIG 映像（选项 #1）。
- 如果你已按照选项 #2 或 #3 进行操作，请[生成 SAS URI](azure-vm-get-sas-uri.md)。
- 如果在创建基于 Azure 的新 VHD 时遇到问题，请参阅 [Azure 市场 VM 常见问题解答](azure-vm-create-faq.md)。
