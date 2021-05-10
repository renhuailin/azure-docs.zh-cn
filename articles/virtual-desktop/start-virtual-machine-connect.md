---
title: 连接时启动虚拟机 - Azure
description: 如何配置连接时启动虚拟机功能。
author: Heidilohr
ms.topic: how-to
ms.date: 04/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 05500ded7512b54446d153e37233e4889b3107ff
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107949181"
---
# <a name="start-virtual-machine-on-connect-preview"></a>连接时启动虚拟机（预览版）

> [!IMPORTANT]
> 连接时启动 VM 功能目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

通过“连接时启动虚拟机 (VM)”功能（预览版），你可以让最终用户只在需要 VM 时才将其打开，从而节省成本。 然后，你可以在不需要 VM 时将其关闭。

>[!NOTE]
>Windows 虚拟桌面（经典）不支持此功能。

## <a name="requirements-and-limitations"></a>要求和限制

只能为个人主机池启用连接时启动 VM 功能。 若要详细了解个人主机池，请参阅 [Windows 虚拟桌面环境](environment-setup.md#host-pools)。

以下远程桌面客户端支持“连接时启动 VM”功能：

- [Web 客户端](connect-web.md)
- [Windows 客户端（1.2748 或更高版本）](connect-windows-7-10.md)
- [Android 客户端（版本 10.0.10 或更高版本）](connect-android.md)
- [macOS 客户端（版本 10.6.4 或更高版本）](connect-macos.md)

可以在[技术社区论坛](https://aka.ms/wvdtc)中查看有关更新和客户端支持的公告。

Azure 政府云目前不支持连接时启动 VM。

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>为连接时启动 VM 创建自定义角色

需要先为 VM 分配自定义 RBAC（基于角色的访问控制）角色，然后才能配置连接时启动 VM 功能。 此角色允许 Windows 虚拟桌面管理你的订阅中的 VM。 你还可以使用此角色来打开 VM、检查其状态，以及报告诊断信息。 如果你想要详细了解每个角色的作用，请查看 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

若要使用 Azure 门户为连接时启动 VM 分配自定义角色，请执行以下操作：

1. 打开 Azure 门户并转到“订阅”。

2. 转到“访问控制(IAM)”，选择“添加自定义角色” 。

    > [!div class="mx-imgBorder"]
    > ![“访问控制(IAM)”中“添加”按钮对应的下拉菜单的屏幕截图。 “添加自定义角色”以红色突出显示。](media/add-custom-role.png)

3. 接下来，为自定义角色命名并添加说明。 建议将其命名为“连接时启动 VM”。

4. 在“权限”选项卡上，将以下权限添加到要向其分配角色的订阅： 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. 完成后，选择“确定”。

然后，需要分配该角色以向 Windows 虚拟桌面授予访问权限。

若要分配自定义角色，请执行以下操作：

1. 在“访问控制(IAM)”选项卡中，选择“添加角色分配” 。

2. 选择刚刚创建的角色。

3. 在搜索栏中，输入并选择“Windows 虚拟桌面”。

      >[!NOTE]
      >如果已部署 Windows 虚拟桌面（经典），则你可能会看到两个应用。 将角色分配到这两个应用。
      >
      > [!div class="mx-imgBorder"]
      > ![“访问控制(IAM)”选项卡的屏幕截图。在搜索栏中，“Windows 虚拟桌面”和“Windows 虚拟桌面(经典)”都以红色突出显示。](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>使用 JSON 文件模板创建自定义角色

如果你正在使用 JSON 文件创建自定义角色，以下示例显示了可以使用的基本模板。 请确保将订阅 ID 值替换为要将角色分配到的订阅 ID。

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>配置连接时启动 VM 功能

现在你已为订阅分配了角色，接下来可以配置连接时启动 VM 功能了！

### <a name="deployment-considerations"></a>部署注意事项 

连接时启动 VM 是一项主机池设置。 如果你希望只有选定的一组用户可以使用此功能，请确保只向你要添加的用户分配所需角色。

>[!IMPORTANT]
> 只能在现有的主机池中配置此功能。 创建新的主机池时，此功能不可用。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

若要使用 Azure 门户配置连接时启动 VM 功能：

1. 打开浏览器，并转到 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中转到 Windows 虚拟桌面。

3. 选择“主机池”，然后查找包含你向其分配了角色的个人桌面的主机池。

   >[!NOTE]
   > 配置有此功能的主机池必须具有直接分配了角色的个人桌面。 如果主机池中的桌面配置不正确，配置过程将不起作用。

4. 在主机池中，选择“属性”。 在“连接时启动 VM”下选择“是”，然后选择“保存”以立即应用设置  。

    > [!div class="mx-imgBorder"]
    > ![“属性”窗口的屏幕快照。 “连接时启动 VM”选项以红色突出显示。](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>使用 PowerShell

若要使用 PowerShell 配置此设置，需确保具有要配置的资源组和主机池的名称。 此外，需要安装 [Azure PowerShell 模块（2.1.0 或更高版本）](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0)。

使用 PowerShell 配置连接时启动 VM：

1. 打开 PowerShell 命令窗口。

2. 运行以下 cmdlet 启用连接时启动 VM：

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. 运行以下 cmdlet 禁用连接时启动 VM：

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>用户体验

在典型会话中，用户连接到已解除分配的 VM 所需的时间会增加，因为再次打开 VM 需要一定的时间，这与打开物理计算机非常类似。 远程桌面客户端有一个指示器，可让用户知道他们在连接时正在打开电脑。

## <a name="troubleshooting"></a>故障排除

如果该功能出现任何问题，我们建议使用 Windows 虚拟桌面[诊断功能](diagnostics-log-analytics.md)检查问题。 如果收到错误消息，请确保特别注意消息内容，并将错误名称复制到某个位置以方便参考。

还可以使用 [Azure Monitor for Windows Virtual Desktop](azure-monitor.md) 获取有关如何解决问题的建议。

## <a name="next-steps"></a>后续步骤

如果你遇到了故障排除文档或诊断功能无法解决的任何问题，请查看[连接时启动 VM 常见问题解答](start-virtual-machine-connect-faq.md)。
