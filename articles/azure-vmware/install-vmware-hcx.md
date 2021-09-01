---
title: 在 Azure VMware 解决方案中安装 VMware HCX
description: 在 Azure VMware 解决方案私有云中安装 VMware HCX。
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 2dec92fdee47c242a8e7e5b3df7befc586964910
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015624"
---
# <a name="install-and-activate-vmware-hcx-in-azure-vmware-solution"></a>在 Azure VMware 解决方案中安装并激活 VMware HCX

VMware HCX Advanced 及其关联的云管理器不再预先部署在 Azure VMware 解决方案中， 而是需要通过作为外接程序的 Azure 门户进行安装。 默认为 HCX Advanced，此后，如果需要 Enterprise 版中的功能，你仍可通过客户支持请求安装 VMware HCX Enterprise Edition。 你仍将下载 HCX 连接器 OVA，并在本地 vCenter 上部署虚拟设备。

HCX Advanced 最多支持三个站点连接（本地到云或云到云）。 如果需要三个以上的站点连接，请使用 HCX Enterprise Edition。 要激活 HCX Enterprise Edition（当前位于 Azure VMware 解决方案的公开预览版中），请打开支持请求以将其启用。 服务正式发布后，你将有 30 天的时间来确定你的后续步骤。 你还可以关闭或选择退出 HCX Enterprise Edition 服务，但将保留 HCX Advanced，因为它是节点成本的一部分。

在不重新部署的情况下，可以从 HCX Enterprise Edition 降级到 HCX Advanced。 首先，请确保已还原为 HCX Advanced 配置状态，且未使用 Enterprise 功能。 如果计划降级，请确保未计划任何迁移，未使用 RAV、MON 等功能，且站点配对为三个或更少。

>[!TIP]
>你也可以通过该门户[卸载 HCX Advanced](#uninstall-hcx-advanced)。 卸载 HCX Advanced 时，请确保没有任何正在进行的活动迁移。 删除 HCX Advanced 将向 HCX 虚拟设备所占用的私有云返还资源。

在此操作说明中，你将：

* 通过 Azure 门户安装 HCX Advanced
* 下载并部署 VMware HCX 连接器 OVA
* 使用许可证密钥激活 HCX Advanced
* 卸载 HCX Advanced

完成后，请按照末尾建议的后续步骤继续执行本入门指南的其他步骤。

## <a name="prerequisites"></a>先决条件

- [HCX 安装准备](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-A631101E-8564-4173-8442-1D294B731CEB.html)

- [VMware 博客系列 - 云迁移](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)


## <a name="install-vmware-hcx-advanced"></a>安装 VMware HCX Advanced

1. 在 Azure VMware 解决方案私有云中，选择“管理” > “外接程序”。

1. 选择“开始”，进入“HCX Workload Mobility”。

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-get-started.png" alt-text="屏幕截图：显示“HCX Workload Mobility”的“开始”按钮。":::

1. 勾选“我同意上述条款和条件”复选框，然后选择“安装”。

   安装 HCX Advanced 并配置云管理器大约需要 35 分钟。 安装完成后，HCX 本地连接器站点配对所需的 HCX Manager URL 和 HCX 密钥将显示在“使用 HCX 迁移”选项卡上。

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-using-hcx-tab.png" alt-text="屏幕截图：显示“连接性”下的“使用 HCX 迁移”选项卡。":::


## <a name="download-and-deploy-the-vmware-hcx-connector-ova"></a>下载并部署 VMware HCX 连接器 OVA 

在此步骤中，你将下载 VMware HCX 连接器 OVA 文件，然后将 VMware HCX 连接器部署到你的本地 vCenter。

1. 打开浏览器窗口，使用 cloudadmin\@vsphere.local 用户凭据通过 `https://x.x.x.9` 端口 443 登录到 Azure VMware 解决方案 HCX 管理器

1. 在“管理” > “系统更新”下，选择“请求下载链接”。 如果该复选框灰显，请等待几秒钟，以便其生成链接。

1. 下载或接收要在本地 vCenter 上部署的 VMware HCX 连接器 OVA 文件的链接。

1. 在本地 vCenter，选择 [OVF 模板](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)将 VMware HCX 连接器配置到本地 vCenter。

1. 导航到下载的 OVA 文件并选择该文件，然后选择“打开”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="浏览到 OVF 模板的屏幕截图。" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::

1. 选择名称和位置，然后选择需要在其中部署 VMware HCX 连接器的资源或群集。 接下来，查看详细信息和所需的资源，并选择“下一步”。

1. 查看许可条款，选择所需的存储和网络，然后选择“下一步”。

1. 选择在计划状态期间定义的 [VMware HCX 管理网络段](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments)。 然后，选择“下一步”。  

1. 在“自定义模板”中，输入所有必需信息，然后选择“下一步” 。

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="用于自定义模板的框的屏幕截图。" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. 进行验证，然后选择“完成”以部署 VMware HCX 连接器 OVA。

   >[!IMPORTANT]
   >你将需要手动打开虚拟设备。  开机后，请等待 10-15 分钟，然后继续下一步。


## <a name="activate-vmware-hcx"></a>激活 VMware HCX

在本地部署 VMware HCX 连接器 OVA 并启动设备后，即可进行激活。 首先，你需要从 Azure VMware 解决方案门户获取许可证密钥，然后在 VMware HCX Manager 中将其激活。 最后，你需要一个可用于每个已部署的本地 HCX 连接器的密钥。

1. 在 Azure VMware 解决方案私有云中，选择“管理” > “外接程序” > “使用 HCX 迁移”。 然后复制“激活密钥”。

   :::image type="content" source="media/tutorial-vmware-hcx/hcx-activation-key.png" alt-text="屏幕截图：显示激活密钥。":::   

1. 使用管理员凭据登录到位于 `https://HCXManagerIP:9443` 的本地 VMware HCX Manager。  确保在 VMware HCX 管理器 IP 地址中包括 `9443` 端口号。

   >[!TIP]
   >在 VMware HCX 管理器 OVA 文件部署过程中，你已定义管理员用户密码。

1. 在“许可”中，在“HCX 高级密钥”中输入自己的密钥，然后选择“激活”  。

    >[!IMPORTANT]
    >VMware HCX 管理器必须开放 Internet 访问权限或配置一个代理。

1. 在“数据中心位置”中，提供在本地安装 VMware HCX 管理器的最近位置。 然后选择“继续”。 

1. 在“系统名称”中，修改名称或接受默认名称，然后选择“继续” 。

1. 选择“是，继续”。

1. 在“连接 vCenter”中，提供你的 vCenter 服务器的 FQDN 或 IP 地址和相应的凭据，然后选择“继续”。

   >[!TIP]
   >vCenter 服务器是在数据中心部署 VMware HCX 连接器的位置。

1. 在“配置 SSO/PSC”中，提供你的 Platform Services Controller 的 FQDN 或 IP 地址，然后选择“继续”。 

   >[!NOTE]
   >通常，它与 vCenter FQDN 或 IP 地址相同。

1. 验证输入的信息是否正确，然后选择“重启”。

   >[!NOTE]
   >重启后，会有一段延迟，然后系统才会提示你执行下一步。

服务重启后，你会在出现的屏幕上看到 vCenter 显示为绿色。 VCenter 和 SSO 必须都具有相应的配置参数，它们应该与上一个屏幕相同。

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="具有绿色 vCenter 状态的仪表板的屏幕截图。" lightbox="media/tutorial-vmware-hcx/activation-done.png":::


## <a name="uninstall-hcx-advanced"></a>卸载 HCX Advanced

你可以通过门户卸载 HCX Advanced，此操作将删除现有的配对和软件。 

>[!NOTE]
>卸载可能需要约 30 分钟时间，才能将资源返还到 HCX 虚拟设备所占用的私有云。 

1. 请确保没有任何正在进行的活动迁移。

1. 确保不再需要 L2 扩展，或将网络“延伸”到目标。 

1. 对于使用 MON 的工作负载，请确保删除了默认网关。 否则，可能会导致工作负载无法通信或工作。  

1. 在 Azure VMware 解决方案私有云中，选择“管理” > “外接程序”。

1. 选择“开始”，进入“HCX Workload Mobility”，然后选择“卸载”。
   
1. 输入“是”以确认卸载。

此时，HCX Advanced 不再具有 vCenter 插件，如果需要，你也可以随时重新安装它。


## <a name="next-steps"></a>后续步骤

继续学习下一教程，配置 VMware HCX 连接器。  配置 VMware HCX 连接器后，你将拥有一个用于创建虚拟机 (VM) 和迁移的生产就绪环境。 


>[!div class="nextstepaction"]
>[在 Azure VMware 解决方案中配置 VMware HCX](configure-vmware-hcx.md)
