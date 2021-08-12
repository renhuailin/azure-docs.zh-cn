---
title: 在 Azure 虚拟桌面的 Windows 10 VM 上安装语言包 - Azure
description: 如何在 Azure 虚拟桌面中安装适用于 Windows 10 多会话 VM 的语言包。
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 63565af9775779d89c44384e0bd45c8d01e4147c
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006538"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>将语言包添加到 Windows 10 多会话映像

Azure 虚拟桌面是一种用户可以随时随地进行部署的服务。 这就是用户最好可以自定义其 Windows 10 企业版多会话映像显示的语言的原因。

可以通过两种方式来满足用户的语言需求：

- 使用每种语言的自定义映像生成专用的主机池。
- 让用户在同一主机池中具有不同语言和本地化要求，但可以自定义其映像，以确保他们可以选择所需的任何语言。

后一种方法更高效且更具成本效益。 不过，你可以自行决定最适合自己需求的方法。 本文将介绍如何自定义映像语言。

## <a name="prerequisites"></a>必备条件

你需要执行以下操作来自定义 Windows 10 企业版多会话映像以添加多种语言：

- 安装了 Windows 10 企业版多会话版本 1903 或更高版本的 Azure 虚拟机 (VM)

- 语言 ISO、按需功能 (FOD) 磁盘 1 和映像所使用的操作系统版本的收件箱应用 ISO。 可以在此处下载：
     
     - 语言 ISO：
        - [Windows 10 版本 1903 或 1909 语言包 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [2004、20H2 或 21H1 版本的 Windows 10 语言包 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - FOD 磁盘 1 ISO：
        - [Windows 10 版本 1903 或 1909 FOD 磁盘 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [2004、20H2 或 21H1 版本的 Windows 10 FOD 磁盘 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - 收件箱应用 ISO：
        - [Windows 10 版本 1903 或 1909 收件箱应用 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10 版本 2004 收件箱应用 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10 版本 20H2 收件箱应用 ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
        - [21H1 版本的 Windows 10 收件箱应用 ISO](https://software-download.microsoft.com/download/sg/19041.928.210407-2138.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - 如果使用本地体验包 (LXP) ISO 文件来本地化映像，则还需要下载适当的 LXP ISO 以获得最佳语言体验
        - 如果使用的是 Windows 10 版本 1903 或 1909：
          - [Windows 10 版本 1903 或 1909 LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - 如果使用的是 2004、20H2 或 21H1 版本的 Windows 10，请参阅[“在 Windows 10 中添加语言：已知问题”](/windows-hardware/manufacture/desktop/language-packs-known-issue)中的信息，了解以下哪一个 LXP ISO 适合你：
          - [2004、20H2 或 21H1 版本的 Windows 10 **9B** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [2004、20H2 或 21H1 版本的 Windows 10 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [2004、20H2 或 21H1 版本的 Windows 10 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [2004、20H2 或 21H1 版本的 Windows 10 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [2004、20H2 或 21H1 版本的 Windows 10 **1C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [2004、20H2 或 21H1 版本的 Windows 10 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)
          - [2004、20H2 或 21H1 版本的 Windows 10 **4B** LXP ISO](https://software-download.microsoft.com/download/sg/LanguageExperiencePack.2104B.iso)
          - [2004、20H2 或 21H1 版本的 Windows 10 **5C** LXP ISO](https://software-download.microsoft.com/download/sg/LanguageExperiencePack.2105C.iso)

- Azure 文件共享或 Windows 文件服务器虚拟机上的文件共享

>[!NOTE]
>必须可以从你计划用于创建自定义映像的 Azure VM 访问文件共享（存储库）。

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>为语言包和按需功能创建内容存储库

若要为语言包和 FOD 创建内容存储库，以及为收件箱应用包创建存储库，请执行以下操作：

1. 在 Azure VM 上，从[先决条件](#prerequisites)中的链接下载适用于 Windows 10 企业版多会话版本 1903/1909 和 2004 映像的 Windows 10 多语言 ISO、FOD 和收件箱应用。

2. 打开 ISO 文件，并装载到 VM 上。

3. 请转到语言包 ISO 并复制 LocalExperiencePacks 和 x64\\langpacks 文件夹中的内容，然后将内容粘贴到文件共享中。

4. 请访问 FOD ISO 文件，复制其所有内容，并将其粘贴到文件共享中。
5. 转到收件箱应用 ISO 上的 amd64fre 文件夹，并复制已准备好的收件箱应用的存储库中的内容。

     >[!NOTE]
     > 如果使用的是有限存储，只需复制你知道用户需要的语言的文件。 你可以通过查看文件名中的语言代码来区分这些文件。 例如，法语文件的名称中包含代码“fr-FR”。 有关所有可用语言的完整语言代码列表，请参阅[适用于 Windows 的可用语言包](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)。

     >[!IMPORTANT]
     > 某些语言需要附属包中包含的遵循其他命名约定的其他字体。 例如，日语字体文件名包括“Jpan”。
     >
     > [!div class="mx-imgBorder"]
     > ![日语语言包的示例，其文件名中包含“Jpan”语言标记。](media/language-pack-example.png)

6. 设置对语言内容存储库共享的权限，以便你拥有对用于生成自定义映像的 VM 的读取访问权限。

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually&quot;></a>手动创建自定义 Windows 10 企业版多会话映像

若要手动创建自定义 Windows 10 企业版多会话映像，请执行以下操作：

1. 部署 Azure VM，然后转到 Azure 库，并选择要使用的 Windows 10 企业版多会话的当前版本。
2. 部署 VM 后，请使用 RDP 以本地管理员身份连接到该 VM。
3. 请确保 VM 具有所有最新的 Windows 更新。 下载更新，根据需要重启 VM。
4. 连接到语言包、FOD 和收件箱应用文件共享存储库，并将其安装到某个驱动器号（例如驱动器 E）。

## <a name=&quot;create-a-custom-windows-10-enterprise-multi-session-image-automatically&quot;></a>自动创建自定义 Windows 10 企业版多会话映像

如果你希望通过自动化过程安装语言，可以在 PowerShell 中设置脚本。 你可以使用以下脚本示例，安装西班牙语（西班牙）、法语（法国）和中文（中国）语言包和适用于 Windows 10 企业版多会话版本 2004 的附属包。 该脚本将语言界面包和所有必要的附属包集成到映像中。 但是，你还可以修改此脚本以安装其他语言。 只需确保从已提升权限的 PowerShell 会话中运行该脚本，否则它将不起作用。

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath &quot;\Microsoft\Windows\AppxDeploymentClient\" -TaskName &quot;Pre-staged app cleanup&quot;

##Set Language Pack Content Stores##
[string]$LIPContent = &quot;E:&quot;

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add(&quot;es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

运行该脚本可能需要一段时间，具体取决于需要安装的语言数量。

脚本运行完毕后，请转到“开始” > “设置” > “时间和语言” > “语言”，确保正确安装了语言包。 如果存在相应的语言文件，即表示一切都已设置完毕。

向 Windows 映像添加其他语言后，还需要更新收件箱应用以支持添加的语言。 这可以通过使用收件箱应用 ISO 中的内容刷新预安装的应用来完成。
若要在 VM 无法访问 Internet 的环境中执行此刷新，可使用以下 PowerShell 脚本模板自动执行此过程，并且仅更新已安装的收件箱应用版本。

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string] $AppsContent = "F:\"

##Update installed Inbox Store Apps##
foreach ($App in (Get-AppxProvisionedPackage -Online)) {
    $AppPath = $AppsContent + $App.DisplayName + '_' + $App.PublisherId
    Write-Host "Handling $AppPath"
    $licFile = Get-Item $AppPath*.xml
    if ($licFile.Count) {
        $lic = $true
        $licFilePath = $licFile.FullName
    } else {
        $lic = $false
    }
    $appxFile = Get-Item $AppPath*.appx*
    if ($appxFile.Count) {
        $appxFilePath = $appxFile.FullName
        if ($lic) {
            Add-AppxProvisionedPackage -Online -PackagePath $appxFilePath -LicensePath $licFilePath 
        } else {
            Add-AppxProvisionedPackage -Online -PackagePath $appxFilePath -skiplicense
        }
    }
}

```

>[!IMPORTANT]
>ISO 中包含的收件箱应用不是预安装的 Windows 应用的最新版本。 若要获取所有应用的最新版本，需要使用 Windows 应用商店应用更新应用，并在安装了其他语言之后手动搜索更新。

完成后，请确保断开共享的连接。

## <a name="finish-customizing-your-image"></a>完成映像自定义

安装语言包之后，可以安装要添加到自定义映像的任何其他软件。

完成映像自定义后，需要运行系统准备工具 (sysprep)。

若要运行 Sysprep，请执行以下操作：

1. 打开提升的命令提示符并运行以下命令，以通用化映像：  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. 按照[在 Azure 中创建通用化 VM 的托管映像](../virtual-machines/windows/capture-image-resource.md)中的说明，停止 VM，然后将其捕获到托管映像中。

3. 你现在可以使用自定义映像来部署 Azure 虚拟桌面主机池。 若要了解如何部署主机池，请参阅[教程：使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)。

## <a name="enable-languages-in-windows-settings-app"></a>在 Windows 设置应用中启用语言

最后，在部署主机池之后，你需要将该语言添加到每个用户的语言列表中，以便他们可以在“设置”菜单中选择其首选语言。

若要确保你的用户可以选择你安装的语言，请以用户身份登录，然后运行以下 PowerShell cmdlet 以将安装的语言包添加到“语言”菜单。 你还可以将此脚本设置为自动化任务或在用户登录到会话时激活的登录脚本。

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

用户更改其语言设置后，需要注销其 Azure 虚拟桌面会话，并再次登录以使更改生效。 

## <a name="next-steps"></a>后续步骤

如果想要了解语言包的已知问题，请参阅[在 Windows 10 版本 1803 及更高版本中添加语言包：已知问题](/windows-hardware/manufacture/desktop/language-packs-known-issue)。

如果对 Windows 10 企业版多会话有任何其他问题，请查看[常见问题解答](windows-10-multisession-faq.yml)。
