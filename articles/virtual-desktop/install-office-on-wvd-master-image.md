---
title: 在主 VHD 映像中安装 Office - Azure
description: 如何在 Azure 的 Azure 虚拟桌面主映像上安装和自定义 Office。
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ca466110a3ac876df444aa380a4a2dde0384f1fa
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294121"
---
# <a name="install-office-on-a-master-vhd-image"></a>在主 VHD 映像中安装 Office

本文介绍如何在主虚拟硬盘 (VHD) 映像上安装 Microsoft 365 应用企业版、OneDrive 和其他常见应用程序，以便上传到 Azure。 如果你的用户需要访问特定业务线 (LOB) 应用程序，建议你在完成本文中的说明后安装这些应用程序。

本文假定你已创建了虚拟机 (VM)。 如果未创建，请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md#create-a-vm)

本文还假定你在 VM 上拥有已提升的访问权限，无论它是在 Azure 还是 Hyper-V 管理器中预配。 如果没有，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../role-based-access-control/elevate-access-global-admin.md)。

>[!NOTE]
>这些说明适用于可与组织的现有过程配合使用的特定于 Azure 虚拟桌面的配置。

## <a name="install-office-in-shared-computer-activation-mode"></a>在共享计算机激活模式下安装 Office

借助共享计算机激活，可将 Microsoft 365 应用企业版部署到组织中由多个用户访问的计算机。 有关共享计算机激活的详细信息，请参阅 [Microsoft 365 应用的共享计算机激活概述](/deployoffice/overview-shared-computer-activation)。

使用 [Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117)安装 Office。 Windows 10 企业版多会话仅支持以下版本的 Office：

   - Microsoft 365 企业应用版
   - 附带 Microsoft 365 商业高级版订阅的 Microsoft 365 应用企业版

Office 部署工具需要一个配置 XML 文件。 若要自定义下面的示例，请参阅 [Office 部署工具的配置选项](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)。

我们提供的此示例配置 XML 将执行以下操作：

   - 从每月企业频道安装 Office 并从每月企业频道提供更新。
   - 使用 x64 体系结构。
   - 禁用自动更新。
   - 删除 Office 的所有现有安装并迁移其设置。
   - 启用共享计算机激活。

>[!NOTE]
>在 Azure 虚拟桌面中，Visio 的模具搜索功能可能无法按预期方式运行。

下面是此示例配置 XML 不会执行的操作：

   - 安装 Skype for Business
   - 在每用户模式下安装 OneDrive。 若要了解详细信息，请参阅[在每台计算机模式下安装 OneDrive](#install-onedrive-in-per-machine-mode)。

>[!NOTE]
>共享计算机激活可通过组策略对象 (GPO) 或注册表设置进行设置。 GPO 位于 Computer Configuration\\Policies\\Administrative Templates\\Microsoft Office 2016 (Machine)\\Licensing Settings

Office 部署工具包含 setup.exe。 若要安装 Office，请在命令行中运行以下命令：

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>configuration.xml 示例

下面的 XML 示例将安装每月企业频道版本。

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Office 团队建议为 OfficeClientEdition 参数使用 64 位安装。

安装 Office 后，可更新默认 Office 行为。 单独运行以下命令或在批文件中运行，以更新行为。

```cmd
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>在每台计算机模式下安装 OneDrive

OneDrive 通常在每用户模式下安装。 在此环境中，应在每台计算机模式下安装。

下面介绍如何在每台计算机模式下安装 OneDrive：

1. 首先，创建一个用于暂存 OneDrive 安装程序的位置。 可以是本地磁盘文件夹或 [\\\\unc] (file://unc) 位置。

2. 使用以下链接将 OneDriveSetup.exe 下载到暂存位置：<https://aka.ms/OneDriveWVD-Installer>

3. 如果省略 \<ExcludeApp ID="OneDrive" /\> 以安装包含 OneDrive 的 Office，请运行以下命令，从提升的命令提示符中卸载所有现有的 OneDrive 每用户安装：

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 从提升的命令提示符中运行此命令以设置 AllUsersInstall 注册表值：

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 运行此命令以在每台计算机模式下安装 OneDrive：

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 运行此命令以将所有用户的 OneDrive 配置为在登录时启动：

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 运行以下命令以启用“以无提示方式配置用户帐户”。

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 运行以下命令以将 Windows 已知文件夹重定向并移至 OneDrive。

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft Teams 和 Skype for Business

Azure 虚拟桌面不支持 Skype for Business。

有关安装 Microsoft Teams 的帮助信息，请参阅[在 Azure 虚拟桌面上使用 Microsoft Teams](./teams-on-avd.md)。 

## <a name="next-steps"></a>后续步骤

现在，你已将 Office 添加到映像，接下来可以继续自定义你的主要 VHD 映像。 请参阅[准备和自定义主 VHD 映像](set-up-customize-master-image.md)。