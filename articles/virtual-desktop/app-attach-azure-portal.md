---
title: Windows 虚拟桌面 .MSIX 应用附加门户预览-Azure
description: 如何使用 Azure 门户为 Windows 虚拟桌面设置 .MSIX 应用附加。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d8ed429003a9da7ae93fb93f4218cd66767dcd7b
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562270"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>设置 .MSIX 应用附加 Azure 门户

> [!IMPORTANT]
> .MSIX 应用附加当前为公共预览版。
> 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何在 Windows 虚拟桌面环境中设置 .MSIX 应用附加 (预览) 。

## <a name="requirements"></a>要求

>[!IMPORTANT]
>在开始之前，请确保填写并提交 [此表单](https://aka.ms/enablemsixappattach) ，以便在你的订阅中启用 .msix 应用附件。 如果没有已批准的请求，.MSIX 应用附加将不起作用。 在工作日内批准请求可能需要长达24小时。 请求被接受并完成后，会收到一封电子邮件。

下面是配置 .MSIX app attach 所需的内容：

- 正常运行的 Windows 虚拟桌面部署。 若要了解如何部署 Windows 虚拟桌面 (经典) ，请参阅 [在 Windows 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)。 若要了解如何使用 Azure 资源管理器集成部署 Windows 虚拟桌面，请参阅 [使用 Azure 门户创建主机池](./create-host-pools-azure-marketplace.md)。
- 至少具有一个活动会话主机的 Windows 虚拟桌面主机池。
- 此主机池必须在验证环境中。 
- .MSIX 打包工具。
- 扩展到已上载到文件共享中的 .MSIX 映像的 .MSIX 封装应用程序。
- Windows 虚拟桌面部署中将存储 .MSIX 包的文件共享。
- 你上载 .MSIX 映像的文件共享还必须可供主机池中 (Vm) 的所有虚拟机访问。 用户将需要只读权限才能访问该映像。

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>关闭 .MSIX 应用附加应用程序的自动更新

在开始之前，必须对 .MSIX 应用附加应用程序禁用自动更新。 若要禁用自动更新，需要在提升的命令提示符中运行以下命令：

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

禁用自动更新后，你必须启用 Hyper-v，因为你将使用 `Mount-VHD` 命令将 VHD 暂存并卸除到 "转储"。

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>此更改将需要你重新启动虚拟机。

## <a name="configure-the-msix-app-attach-management-interface"></a>配置 .MSIX 应用附加管理接口

接下来，需要下载并配置 Azure 门户的 .MSIX 应用附加管理接口。

若要设置管理界面：

1. [打开预览门户](https://preview.portal.azure.com/?feature.msixapplications=true#home)。
2. 如果您收到询问是否将该扩展视为可信的提示，请选择 " **允许**"。

      > [!div class="mx-imgBorder"]
      > ![不受信任的扩展窗口的屏幕截图。 "允许" 以红色突出显示。](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>将 .MSIX 映像添加到主机池

接下来，需要将 .MSIX 映像添加到主机池。

添加 .MSIX 映像：

1. 打开 Azure 门户。

2. 在搜索栏中输入 **Windows 虚拟桌面** ，然后选择服务名称。

      > [!div class="mx-imgBorder"]
      > ![用户的屏幕截图，从 Azure 门户的 "搜索栏" 下拉菜单中选择 "Windows 虚拟桌面"。 "Windows 虚拟桌面" 以红色突出显示。](media/find-and-select.png)

3. 选择计划在其中放置 .MSIX 应用的主机池。

4. 选择 " **.msix 包** " 以打开数据网格，其中包含当前已添加到主机池的所有 **.msix 包** 。

5. 选择 " **+ 添加** " 以打开 " **添加 .msix 包** " 选项卡。

6. 在 " **添加 .msix 包** " 选项卡中，输入以下值：

      - 对于 **.msix 映像路径**，请输入指向文件共享上的 .msix 映像的有效 UNC 路径。  (例如， `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` . ) 完成后，选择 " **添加** " 以询问 .msix 容器以检查路径是否有效。

      - 对于 **.msix package**，从下拉菜单中选择相关的 .msix 包名称。 仅当在 **.msix 图像路径** 中输入了有效的图像路径时才会填充此菜单。

      - 对于 **包应用程序**，请确保列表中包含你想要在 .msix 包中为用户提供的所有 .msix 应用程序。

      - 如果希望包在用户部署中具有更好的用户友好性，可选择输入 **显示名称** 。

      - 请确保 **版本** 的版本号正确。

      - 选择要使用的 **注册类型** 。 使用哪种方式取决于您的需求：

    - **按需注册** 会推迟 .msix 应用程序的完整注册，直到用户启动该应用程序。 这是我们建议使用的注册类型。

    - 仅当用户登录时，才会注册 **阻止登录**。 不建议这样做，因为这可能会导致用户登录时间较长。

7.  对于 " **状态**"，请选择首选状态。
    -  " **活动** " 状态允许用户与包交互。
    -  **非活动** 状态会导致 Windows 虚拟桌面忽略包，而不会将其传递给用户。

8. 完成后，选择“添加”。

## <a name="publish-msix-apps-to-an-app-group"></a>将 .MSIX 应用发布到应用组

接下来，需要将应用发布到包中。 需要为桌面和远程应用程序组执行此操作。

如果已经有 .MSIX 映像，请直接跳到将 [.msix 应用发布到应用组](#publish-msix-apps-to-an-app-group)。 如果要测试旧版应用程序，请按照[通过 VM 上的桌面安装程序创建 MSIX 包](/windows/msix/packaging-tool/create-app-package-msi-vm/)中的说明将旧版应用程序转换为 MSIX 包。

若要发布应用：

1. 在 Windows 虚拟桌面资源提供程序中，选择 " **应用程序组** " 选项卡。

2. 选择要将应用发布到的应用程序组。

   >[!NOTE]
   >.MSIX 应用程序可通过 .MSIX 应用附加到远程应用和桌面应用组进行传递

3. 进入应用组后，选择 " **应用程序** " 选项卡。 **应用程序** 网格将显示应用程序组中的所有现有应用程序。

4. 选择 " **+ 添加** " 以打开 " **添加应用程序** " 选项卡。

      > [!div class="mx-imgBorder"]
      > ![用户选择 "+ 添加" 以打开 "添加应用程序" 选项卡的屏幕截图](media/select-add.png)

5. 对于 " **应用程序源**"，请选择应用程序的源。
    - 如果使用的是桌面应用组，请选择 " **.msix 包**"。
      
      > [!div class="mx-imgBorder"]
      > ![从应用程序源下拉菜单中选择 .MSIX 包的客户屏幕截图。 .MSIX 包以红色突出显示。](media/select-source.png)
    
    - 如果使用的是远程应用组，请选择下列选项之一：
        
        - “开始”菜单
        - 应用路径
        - .MSIX 包

    - 对于 " **应用程序名称**"，请输入应用程序的描述性名称。

    你还可以配置以下可选功能：
   
    - 对于 " **显示名称**"，请输入用户将看到的包的新名称。

    - 对于 " **说明**"，请输入应用包的简短说明。

    - 如果使用的是远程应用组，还可以配置以下选项：

        - **图标路径**
        - **图标索引**
        - **在 web 源中显示**

6. 完成后，选择“保存”。

>[!NOTE]
>将用户分配到相同主机池中的远程应用组和桌面应用组时，桌面应用组将显示在源中。

## <a name="assign-a-user-to-an-app-group"></a>将用户分配到应用组

将 .MSIX 应用分配到应用组后，需要授予用户对其的访问权限。 可以通过向已发布的 .MSIX 应用程序添加用户或用户组，来分配访问权限。 按照 [使用 Azure 门户管理应用组](manage-app-groups.md) 中的说明将用户分配到应用组。

## <a name="change-msix-package-state"></a>更改 .MSIX 包状态

接下来，需要将 .MSIX 包状态更改为 " **活动** " 或 " **非活动**"，具体取决于要对包执行的操作。 活动包是你的用户在发布后可与之交互的包。 Windows 虚拟桌面忽略非活动包，因此用户无法与内部的应用进行交互。

### <a name="change-state-with-the-applications-list"></a>更改应用程序列表的状态

若要更改应用程序列表的包状态：

1. 前往主机池并选择 " **.msix 包**"。 应会在主机池中看到所有现有 .MSIX 包的列表。

2. 选择需要更改其状态的 .MSIX 包，并选择 " **更改状态**"。

### <a name="change-state-with-update-package"></a>更改包状态和更新包

使用更新包更改包状态：

1. 前往主机池并选择 " **.msix 包**"。 应会在主机池中看到所有现有 .MSIX 包的列表。

2. 从 ".MSIX 包" 列表中选择要更改其状态的包的名称。 这会打开 " **更新包** " 选项卡。

3. 切换 **状态** 切换为 " **非活动** " 或 " **活动**"，然后选择 " **保存"。**

## <a name="change-msix-package-registration-type"></a>更改 .MSIX 包注册类型

更改包的注册类型：

1. 选择 **.msix 包**。 应会在主机池中看到所有现有 .MSIX 包的列表。

2. 在 " **.msix 包" 网格** 中选择 "**包名称**" 这会打开边栏选项卡来更新包。

3. 根据需要通过按需 **/登录阻止** 按钮切换 **注册类型**，然后选择 "保存" **。**

## <a name="remove-an-msix-package"></a>删除 .MSIX 包

若要从主机池中删除 .MSIX 包，请执行以下操作：

1. 选择 **.msix 包**。  应会在主机池中看到所有现有 .MSIX 包的列表。

2. 选择要删除的包的名称右侧的省略号，然后选择 " **删除**"。

## <a name="remove-msix-apps"></a>删除 .MSIX 应用

从包中删除单独的 .MSIX 应用：

1. 前往主机池并选择 " **应用程序组**"。

2. 选择要从中删除 .MSIX 应用的应用程序组。

3. 打开 " **应用程序** " 选项卡。

4. 选择要删除的应用，然后选择 " **删除**"。

## <a name="next-steps"></a>后续步骤

请在 [Windows 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)上咨询有关此功能的社区问题。

还可以在 [Windows 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Windows 虚拟桌面的反馈。

下面是一些可能有用的其他文章：

- [.MSIX 应用附加术语表](app-attach-glossary.md)
- [.MSIX 应用附加常见问题解答](app-attach-faq.md)
