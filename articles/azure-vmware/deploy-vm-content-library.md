---
title: 创建内容库以在 Azure VMware 解决方案中部署 VM
description: 创建内容库以在 Azure VMware 解决方案私有云中部署 VM。
ms.topic: how-to
ms.date: 06/28/2021
ms.openlocfilehash: ed98d48037df6cfb50c3c94bb6a7187097f5b0e9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323689"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>创建内容库以在 Azure VMware 解决方案中部署 VM

内容库以库项的形式存储和管理内容。 单个库项由用于部署虚拟机 (VM) 的文件组成。

本文介绍如何在 vSphere 客户端创建内容库，然后使用内容库中的 ISO 映像部署 VM。

## <a name="prerequisites"></a>必备条件

学习本教程需要具备 NSX-T 段和托管式 DHCP 服务。  有关详细信息，请参阅[为 Azure VMware 解决方案配置 DHCP](configure-dhcp-azure-vmware-solution.md)。  

## <a name="create-a-content-library"></a>创建内容库

1. 在本地 vSphere 客户端中，选择“菜单” > “内容库”。

   :::image type="content" source="media/content-library/vsphere-menu-content-libraries.png" alt-text="显示 vSphere 客户端中的“内容库”菜单选项的屏幕截图。":::

1. 选择“添加”以创建新内容库。

   :::image type="content" source="media/content-library/create-new-content-library.png" alt-text="显示如何在 vSphere 中创建新内容库的屏幕截图。":::

1. 提供一个名称，确认 vCenter 服务器的 IP 地址，然后选择“下一步”。

   :::image type="content" source="media/content-library/new-content-library-step-1.png" alt-text="显示新内容库的名称和 vCenter Server IP 的屏幕截图。":::

1. 选择“本地内容库”，然后选择“下一步” 。

   :::image type="content" source="media/content-library/new-content-library-step-2.png" alt-text="显示为新内容库选择了本地内容库选项的屏幕截图。":::

1. 选择用于存储内容库的数据存储，然后选择“下一步”。

   :::image type="content" source="media/content-library/new-content-library-step-3.png" alt-text="显示选择了 vsanDatastore 存储位置的屏幕截图。":::

1. 查看内容库设置，然后选择“完成”。

   :::image type="content" source="media/content-library/new-content-library-step-4.png" alt-text="显示新内容库的设置的屏幕截图。":::

## <a name="upload-an-iso-image-to-the-content-library"></a>将 ISO 映像上传到内容库

现在内容库已创建完成，可以添加 ISO 映像来将 VM 部署到私有云群集。 

1. 在 vSphere 客户端中，选择“菜单” > >“内容库”。

1. 右键单击要用于新 ISO 的内容库，然后选择“导入项目”。

1. 通过以下一种方式从源导入一个库项，然后选择“导入”：
   1. 选择“URL”并提供用于下载 ISO 的 URL。

   1. 选择“本地文件”从本地系统中上传。

   > [!TIP]
   > （可选）可以自定义“目标”的名称和备注。

1. 打开库，然后选择“其他类型”选项卡以验证 ISO 已成功上传。


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>将 VM 部署到私有云群集

1. 在 vSphere 客户端中，选择“菜单” > “主机和群集”。

1. 在左侧窗格中，展开树结构，然后选择一个群集。

1. 选择“操作” > “新建虚拟机”。

1. 完成向导并修改所需的设置。

1. 选择“新建 CD/DVD 驱动器” > “客户端设备” > “内容库 ISO 文件”。

1. 选择上一部分中上传的 ISO，然后选择“确定”。

1. 选择“连接”复选框以在开机时装载此 ISO。

1. 选择“新建网络” > 选择下拉菜单 > “浏览”。

1. 选择“逻辑交换机(段)”，然后选择“确定” 。

1. 修改任何其他硬件设置，然后选择“下一步”。

1. 验证设置，然后选择“完成”。


## <a name="next-steps"></a>后续步骤

在 Azure VMware 解决方案中创建内容库以部署 VM 后，接下来你可能想要了解以下内容：

- [将 VM 工作负载迁移到私有云](configure-vmware-hcx.md)
- [在 Azure VMware 解决方案中集成 Azure 原生服务](integrate-azure-native-services.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
