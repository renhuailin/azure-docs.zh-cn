---
title: Azure 虚拟桌面 MSIX 应用附加门户 - Azure
description: 如何使用 Azure 门户设置 Azure 虚拟桌面的 MSIX 应用附加。
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ec462d04402f20d801c43cebd7f4561eb6177f1c
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707117"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>通过 Azure 门户设置 MSIX 应用附加

本文将引导你了解如何在 Azure 虚拟桌面环境中设置 MSIX 应用附加。

## <a name="requirements"></a>要求

配置 MSIX 应用附加需要以下各项：

- 正常运行的 Azure 虚拟桌面部署。 要了解如何部署 Azure 虚拟桌面（经典），请参阅[在 Azure 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)。 要了解如何使用 Azure 资源管理器集成部署 Azure 虚拟桌面，请参阅[使用 Azure 门户创建主机池](./create-host-pools-azure-marketplace.md)。
- 至少具有一个活动会话主机的 Azure 虚拟桌面主机池。
- MSIX 打包工具。
- 展开到已上传到文件共享中的 MSIX 映像的 MSIX 打包应用程序。
- Azure 虚拟桌面部署中将存储 MSIX 包的文件共享。
- 上传了 MSIX 映像的文件共享还必须可由主机池中的所有虚拟机 (VM) 访问。 用户需要只读权限才能访问该映像。
- 如果证书不受公开信任，请按照[安装证书](app-attach.md#install-certificates)中的说明进行操作。

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>关闭 MSIX 应用附加应用程序的自动更新

在开始之前，必须禁用 MSIX 应用附加应用程序的自动更新。 若要禁用自动更新，需要在提升的命令提示符中运行以下命令：

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

## <a name="configure-the-msix-app-attach-management-interface"></a>配置 MSIX 应用附加管理接口

接下来，需要下载并配置 Azure 门户的 MSIX 应用附加管理接口。

若要设置管理接口：

1. [打开 Azure 门户](https://portal.azure.com)。
2. 如果收到询问是否将该扩展视为可信的提示，请选择“允许”。

      > [!div class="mx-imgBorder"]
      > ![不受信任的扩展窗口的屏幕截图。 “允许”以红色突出显示。](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>将 MSIX 映像添加到主机池

接下来，需要将 MSIX 映像添加到主机池。

若要添加 MSIX 映像，请执行以下操作：

1. 打开 Azure 门户。

2. 在搜索栏中输入“Azure 虚拟桌面”，然后选择服务名称。

3. 选择计划在其中放置 MSIX 应用的主机池。

4. 选择“MSIX 包”以打开数据网格，其中包含当前已添加到主机池的所有 MSIX 包。

5. 选择“+ 添加”以打开“添加 MSIX 包”选项卡。

6. 在“添加 MSIX 包”选项卡中，输入以下值：

      - 对于“MSIX 映像路径”，请输入指向文件共享上的 MSIX 映像的有效 UNC 路径。 （例如：`\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd`。）完成后，选择“添加”以询问 MSIX 容器，以检查路径是否有效。

      - 对于 MSIX 包，从下拉菜单中选择相关的 MSIX 包名称。 仅当在“MSIX 映像路径”中输入了有效的映像路径时才会填充此菜单。

      - 对于包应用程序，请确保列表中包含需要在 MSIX 包中为用户提供的所有 MSIX 应用程序。

      - （可选）如果希望包在用户部署中更易用，可输入“显示名称”。

      - 请确保“版本”的版本号正确。

      - 选择要使用的“注册类型”。 使用哪一种类型取决于你的需求：

    - “按需注册”会推迟 MSIX 应用程序的完整注册，直到用户启动该应用程序。 这是我们建议使用的注册类型。

    - 仅当用户登录时，才会注册“登录阻止”。 不建议使用此类型，因为这可能会导致用户登录时间较长。

7.  对于“状态”，请选择你喜欢的状态。
    -  “活动”状态允许用户与包交互。
    -  “非活动”状态会导致 Azure 虚拟桌面忽略包，而不会将其传递给用户。

8. 完成后，选择“添加”。

## <a name="publish-msix-apps-to-an-app-group"></a>将 MSIX 应用发布到应用组

接下来，你需要将应用发布到包中。 需要同时为桌面和远程应用应用程序组执行此操作。

如果已经有 MSIX 映像，请直接跳至[将 MSIX 应用发布到应用组](#publish-msix-apps-to-an-app-group)。 如果要测试旧版应用程序，请按照[通过 VM 上的桌面安装程序创建 MSIX 包](/windows/msix/packaging-tool/create-app-package-msi-vm/)中的说明将旧版应用程序转换为 MSIX 包。

若要发布应用：

1. 在 Azure 虚拟桌面资源提供程序中，选择“应用程序组”选项卡。

2. 选择要将应用发布到的应用程序组。

   >[!NOTE]
   >可以将 MSIX 应用程序与 MSIX 应用附加一起交付给远程应用组和桌面应用组

3. 进入应用组后，选择“应用程序”标签。“应用程序”网格将显示应用组内的所有现有应用。

4. 选择“+ 添加”以打开“添加应用程序”选项卡 。

      > [!div class="mx-imgBorder"]
      > ![用户选择“+ 添加”以打开“添加应用程序”选项卡的屏幕截图](media/select-add.png)

5. 对于“应用程序源”，请选择应用程序的源。
    - 如果使用的是桌面应用组，请选择“MSIX 包”。
      
      > [!div class="mx-imgBorder"]
      > ![客户从应用程序源下拉菜单中选择 MSIX 包的屏幕截图。 MSIX 包以红色突出显示。](media/select-source.png)
    
    - 如果使用的是远程应用组，请选择下列选项之一：
        
        - “开始”菜单
        - 应用路径
        - MSIX 包

    - 对于“应用程序名称”，请输入应用程序的描述性名称。

    还可以配置以下可选功能：
   
    - 对于“显示名称”，请为包输入用户将看到的新名称。

    - 在“描述”中，输入应用包的短描述。

    - 如果使用的是远程应用组，还可以配置以下选项：

        - 图标路径
        - 图标索引
        - 在 Web 源中显示

6. 完成后，选择“保存”。

>[!NOTE]
>将用户从相同主机池分配到远程应用组和桌面应用组时，桌面应用组将显示在源中。

## <a name="assign-a-user-to-an-app-group"></a>将用户分配到应用组

将 MSIX 应用分配到应用组后，需要授予用户对其的访问权限。 可以通过将用户或用户组添加到具有已发布的 MSIX 应用程序的应用程序组来分配访问权限。 按照[使用 Azure 门户管理应用组](manage-app-groups.md)中的说明将用户分配到应用组。

>[!NOTE]
>当在公共预览版期间测试远程应用时，MSIX 应用附加远程应用可能会从源中消失。 应用不会显示，因为在评估环境中使用的主机池正由生产环境中的 RD Broker 提供服务。 由于生产环境中的 RD Broker 未注册 MSIX 应用附加远程应用的存在，因此这些应用将不会出现在源中。

## <a name="change-msix-package-state"></a>更改 MSIX 包状态

接下来，你需要根据要对包执行的操作将 MSIX 包状态更改为“活动”或“非活动” 。 活动包是在发布后用户可与之交互的包。 Azure 虚拟桌面将忽略非活动包，因此用户无法与内部的应用进行交互。

### <a name="change-state-with-the-applications-list"></a>通过应用程序列表更改状态

若要通过应用程序列表更改包状态：

1. 前往主机池并选择“MSIX 包”。 应会在主机池中看到所有现有 MSIX 包的列表。

2. 选择需要更改其状态的 MSIX 包，并选择“更改状态”。

### <a name="change-state-with-update-package"></a>使用更新包更改状态

若要使用更新包更改包状态：

1. 前往主机池并选择“MSIX 包”。 应会在主机池中看到所有现有 MSIX 包的列表。

2. 从 MSIX 包列表中选择要更改其状态的包的名称。 此时会打开“更新包”选项卡。

3. 将“状态”开关切换为“非活动”或“活动”  ，然后选择“保存”。

## <a name="change-msix-package-registration-type"></a>更改 MSIX 包注册类型

若要更改 MSIX 包注册类型：

1. 选择“MSIX 包”。 应会在主机池中看到所有现有 MSIX 包的列表。

2. 在“MSIX 包网格”中选择“包名称”，这会打开更新包的边栏选项卡。

3. 根据需要，通过“按需/登录阻止”按钮切换“注册类型”，然后选择“保存”  。

## <a name="remove-an-msix-package"></a>删除 MSIX 包

若要从主机池中删除 MSIX 包：

1. 选择“MSIX 包”。  应会在主机池中看到所有现有 MSIX 包的列表。

2. 选择要删除的包的名称右侧的省略号，然后选择“删除”。

## <a name="remove-msix-apps"></a>删除 MSIX 应用

若要从包中删除单独的 MSIX 应用：

1. 前往主机池并选择“应用程序组”。

2. 选择要从中删除 MSIX 应用的应用程序组。

3. 打开“应用程序”选项卡。

4. 选择要删除的应用，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

请在 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)询问有关此功能的问题。

还可以在 [Azure 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Azure 虚拟桌面的反馈。

下面是一些可能有用的其他文章：

- [MSIX 应用附加术语表](app-attach-glossary.md)
- [MSIX 应用附加常见问题解答](app-attach-faq.yml)
