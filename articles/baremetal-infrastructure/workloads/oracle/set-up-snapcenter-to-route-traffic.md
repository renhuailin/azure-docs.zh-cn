---
title: 设置 SnapCenter 以将流量从 Azure 路由到 Oracle BareMetal 服务器
description: 了解如何设置 NetApp SnapCenter 以将流量从 Azure 路由到 Oracle BareMetal 基础结构服务器。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: 9d8f83712822dcf556efe1098eadd1cfdb4867e6
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576223"
---
# <a name="set-up-netapp-snapcenter-to-route-traffic-from-azure-to-oracle-baremetal-servers"></a>设置 NetApp SnapCenter 以将流量从 Azure 路由到 Oracle BareMetal 服务器

本文将逐步介绍如何设置 NetApp SnapCenter 以将流量从 Azure 路由到 Oracle BareMetal 基础结构服务器。 

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> - **SnapCenter 服务器系统要求：** Azure Windows 2019 或更高版本，配备 4-vCPU、16 GB RAM 和至少 500 GB 托管高级 SSD 存储。
> - **ExpressRoute 网络要求：** Oracle BareMetal 的 SnapCenter 用户必须使用 Microsoft Operations 创建网络帐户，以实现个人存储虚拟机 (VM) 与 Azure 中的 SnapCenter VM 之间的通信。
> - **BareMetal 实例上的 Java 1.8：** SnapCenter 插件需要在 BareMetal 实例上安装 Java 1.8。

## <a name="steps-to-integrate-snapcenter"></a>集成 SnapCenter 的步骤

若要设置 NetApp SnapCenter 以将流量从 Azure 路由到 Oracle BareMetal 基础结构服务器，需完成以下步骤： 

1. 提出支持票证请求，以将用户生成的公钥告知 Microsoft 操作团队。 将 SnapCenter 用户设置为访问存储系统需要支持。 

2. 在 Azure 虚拟网络 (VNet) 中创建可访问 BareMetal 实例的 VM；此 VM 用于 SnapCenter。 

3. 下载并安装 SnapCenter。 

4. 备份和恢复操作。 

>[!NOTE]
> 这些步骤假定你已在 Azure 中的订阅和 Oracle HaaS 中的租户之间创建了 ExpressRoute 连接。

## <a name="create-a-support-ticket-for-user-role-storage-setup"></a>为用户角色存储设置创建支持票证

1. 打开 Azure 门户，导航到“订阅”页面。 选择 BareMetal 订阅。
2. 在 BareMetal 订阅页上，选择“资源组”。
3. 在某个区域中选择适当的资源组。
    
    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/select-resource-group.png" alt-text="屏幕截图显示了订阅页面上的资源组。":::

4. 选择与 Azure SAP HANA 存储对应的 SKU 条目。 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/select-sku.png" alt-text="资源组的屏幕截图，其中突出显示了 Azure SAP HANA 类型的 SKU。":::

5. 选择“新建支持请求”。

6. 在“基本信息”选项卡中，为票证提供以下信息：
    - **问题类型**：技术
    -   **订阅**：你的订阅
    -   **服务**：BareMetal 基础结构
    -   **资源**：你的资源组
    -   **摘要**：SnapCenter 访问设置
    -   **问题类型**：配置和设置
    -   **问题子类型**：设置 SnapCenter

7. 在支持票证的“说明”中的“详细信息”选项卡上，将 *.pem 文件的内容粘贴到文本框，以提供更多详细信息 ****  **** 。 还可以压缩并上传 *.pem 文件。 snapcenter.pem 将是 SnapCenter 用户的公钥。 按以下示例从一个 BareMetal 实例中创建 *.pem 文件。 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/pem.png" alt-text="屏幕截图显示了 .pem 文件的示例内容。":::

    >[!NOTE]
    >文件名称“snapcenter”是进行 REST API 调用所需的用户名。 因此，建议采用具有描述性的文件名。

8.  选择“查看 + 创建”以查看支持票证。

9.  提交公钥证书后，Microsoft 会为你的租户设置 SnapCenter 用户名以及存储虚拟机 (SVM) IP 地址。 Microsoft 将提供 SVM IP。

10. 收到 SVM IP 后，设置密码以访问由你控制的 SVM。

    下面是来自 HANA 大型实例或虚拟网络中的 VM 的 REST CALL 示例，该示例可以访问 HANA 大型实例环境，并将用于设置密码。
    
    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/sample-rest-call.png" alt-text="屏幕截图显示了示例 REST 调用。":::

11. 确保创建 *.pem 文件时使用的 BareMetal 实例上没有处于活动状态的代理变量。

     :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/ensure-no-proxy.png" alt-text="屏幕截图显示 http 代理未设置，以确保创建 *.pem 文件时使用的 BareMetal 实例上没有处于活动状态的代理变量。":::

12. 在不使用用户名/密码的情况下，启用的 REST 命令现在可以从客户端计算机执行命令。 测试连接： 

    无代理：

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/connection-test.png" alt-text="屏幕截图显示了在不使用用户名/密码的情况下测试连接和启用的 REST 命令。":::


       >[!NOTE]
       > 任一 curl 命令均可添加“--verbose”，以提供命令失败原因的详细信息。

## <a name="install-snapcenter"></a>安装 SnapCenter

1. 在预配的 Windows VM 上，浏览到 [NetApp 网站](https://mysupport.netapp.com/site/products/all/details/snapcenter/downloads-tab)。

2. 登录并下载 SnapCenter。 “下载”>“SnapCenter”>选择“版本 4.4”。

3. 安装 SnapCenter 4.4。 选择“**下一页**”。

    安装程序将检查 VM 先决条件。 请注意 VM 的大小，尤其是在较大的环境中。 即使重启可能挂起，也可以继续安装。

4. 配置 SnapCenter 的用户凭据。 默认情况下，它会填充启动该应用程序进行安装的 Windows 用户。 除非存在端口冲突，否则建议使用默认端口。

    安装向导将显示进度，并且需要一些时间才能完成。
 
5. 安装完成后，选择“完成”。  记下 SnapCenter Web 门户的 Web 地址。  也可以通过双击安装完成后桌面上显示的 SnapCenter 图标来对其进行访问。
 
## <a name="disable-enhanced-messaging-service-ems-messages-to-netapp-auto-support"></a>禁用向 NetApp 自动支持发送增强型消息传递服务 (EMS) 消息

默认情况下，EMS 数据收集处于启用状态，并且在安装日期后每七天运行一次。 可以随时禁用数据收集。

1. 从 PowerShell 命令行，建立与 SnapCenter 的会话：

   ```powershell-interactive
   Open-SmConnection
   ```

2. 使用凭据登录。

3. 禁用 EMS 数据收集： 

   ```powershell-interactive
   Disable-SmDataCollectionEms
   ```
   
## <a name="next-steps"></a>后续步骤

了解如何配置 SnapCenter：

> [!div class="nextstepaction"]
> [配置 SnapCenter](configure-snapcenter-oracle-baremetal.md)
