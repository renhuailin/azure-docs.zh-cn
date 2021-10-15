---
title: 在 Azure 虚拟桌面的 Windows 11 企业版虚拟机上安装语言包 - Azure
description: 在 Azure 虚拟桌面的 Windows 11 企业版虚拟机上安装语言包的方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 365d2821ce0fec8fdf901ad4dc663e8325267803
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536620"
---
# <a name="add-languages-to-a-windows-11-enterprise-image"></a>将语言添加到 Windows 11 企业版映像

确保组织内全球各地的用户都可使用 Azure 虚拟桌面部署，这一点很重要。 这便是可为虚拟机自定义自己使用的 Windows 11 企业版映像，使其拥有不同语言包的原因。 从 Windows 11 开始，非管理员用户帐户现在可添加显示语言及其对应的语言功能。 此功能意味着无需为个人主机池中的用户预安装语言包。 对于共用主机池中的用户，我们仍建议添加计划添加到自定义映像的语言。 对于 Windows 11 企业版的单会话和多会话版本，可以使用本文中的说明。

当组织包含多种不同语言的用户时，你拥有以下两种选项：

- 使用每种语言的自定义映像创建专用的主机池。
- 在同一主机池中添加多个具有不同语言的用户。

就资源和成本而言，第二个选项的效率更高，但需要执行一些额外的步骤。 幸运的是，本文将逐步介绍如何构建可适应用户使用各种语言和本地化需求的映像。

## <a name="requirements"></a>要求

需要首先准备以下内容，才能将语言添加到 Windows 11 企业版虚拟机：

-   已安装 Windows 11 企业版的 Azure 虚拟机
-   语言和可选功能 (LoF) ISO。 可在 [Windows 11 语言和可选功能 LoF ISO](https://software-download.microsoft.com/download/sg/22000.1.210604-1628.co_release_amd64fre_CLIENT_LOF_PACKAGES_OEM.iso) 中下载 ISO
-   Azure 文件共享或 Windows 文件服务器虚拟机上的文件共享

>[!NOTE]
>必须可以从即将用于创建自定义映像的 Azure 虚拟机访问文件共享存储库。

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>为语言包和按需功能创建内容存储库

要创建用于向虚拟机添加语言和功能的内容存储库，请执行以下操作：

1. 在 Azure 中打开要添加语言的目标虚拟机。

2. 在虚拟机上打开并装载按[要求](#requirements)下载的 ISO 文件。

3. 在文件共享上创建文件夹。

4. 将 ISO 中“LanguagesAndOptionalFeatures”文件夹内的所有内容复制到创建的文件夹中。

     >[!NOTE]
     > 如果可使用的存储空间有限，则可使用已装载的“语言和可选功能”ISO 作为存储库。 要了解如何创建存储库，请参阅[生成自定义 FOD 和语言包存储库](/windows-hardware/manufacture/desktop/languages-overview#build-a-custom-fod-and-language-pack-repository)。

     >[!IMPORTANT]
     > 某些语言需要附属包中包含的遵循其他命名约定的其他字体。 例如，日语字体文件名包括“Jpan”。
     >
     > [!div class="mx-imgBorder"]
     > ![日语语言包的示例，其文件名中包含“Jpan”语言标记。](media/language-pack-example.png)

5. 设置对语言内容存储库共享的权限，以便你拥有对用于生成自定义映像的 VM 的读取访问权限。

## <a name="create-a-custom-windows-11-enterprise-image-manually"></a>手动创建自定义 Windows 11 企业版映像

可通过以下步骤创建自定义映像：

1. 部署 Azure VM，然后转到 Azure 库，并选择要使用的 Windows 11 企业版的当前版本。

2. 部署 VM 后，请使用 RDP 以本地管理员身份连接到该 VM。

3. 连接到已在[为语言包和按需功能创建内容存储库](#create-a-content-repository-for-language-packages-and-features-on-demand)步骤中创建的文件共享存储库，并将其装载到驱动器盘符（例如，驱动器 E）。

4. 在已提升权限的 PowerShell 会话中运行以下 PowerShell 脚本，以在 Windows 11 Enterprise 上安装语言包和附属包：
   
   ```powershell
   ########################################################
   ## Add Languages to running Windows Image for Capture##
   ########################################################
   ##Disable Language Pack Cleanup##
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\MUI\" -TaskName "LPRemove"
   Disable-ScheduledTask -TaskPath "\Microsoft\Windows\LanguageComponentsInstaller" -TaskName "Uninstallation"
   reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Control Panel\International" /v "BlockCleanupOfUnusedPreinstalledLangPacks" /t REG_DWORD /d 1 /f

   ##Set Language Pack Content Stores##
   $LIPContent = "E:"

   ##Set Path of CSV File##
   $CSVFile = "Windows-10-1809-FOD-to-LP-Mapping-Table.csv"
   $filePath = (Get-Location).Path + "/$CSVFile"

   ##Import Necesarry CSV File##
   $FODList = Import-Csv -Path $filePath -Delimiter ";"

   ##Set Language (Target)##
   $targetLanguage = "es-es"

   $sourceLanguage = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Source Lang' -ne $targetLanguage} | Select-Object -Property 'Source Lang' -Unique).'Source Lang'
   if(!($sourceLanguage)){
       $sourceLanguage = $targetLanguage
   }

   $langGroup = (($FODList | Where-Object {$_.'Target Lang' -eq $targetLanguage}) | Where-Object {$_.'Lang Group:' -ne ""} | Select-Object -Property 'Lang Group:' -Unique).'Lang Group:'

   ##List of additional features to be installed##
   $additionalFODList = @(
       "$LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~~.cab", 
       "$LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-SnippingTool-FoD-Package~31bf3856ad364e35~amd64~$sourceLanguage~.cab",
       "$LIPContent\Microsoft-Windows-Lip-Language_x64_$sourceLanguage.cab" ##only if applicable##
   )
   
   $additionalCapabilityList = @(
    "Language.Basic~~~$sourceLanguage~0.0.1.0",
    "Language.Handwriting~~~$sourceLanguage~0.0.1.0",
    "Language.OCR~~~$sourceLanguage~0.0.1.0",
    "Language.Speech~~~$sourceLanguage~0.0.1.0",
    "Language.TextToSpeech~~~$sourceLanguage~0.0.1.0"
    )

    ##Install all FODs or fonts from the CSV file###
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Client-Language-Pack_x64_$sourceLanguage.cab
    Dism /Online /Add-Package /PackagePath:$LIPContent\Microsoft-Windows-Lip-Language-Pack_x64_$sourceLanguage.cab
    foreach($capability in $additionalCapabilityList){
       Dism /Online /Add-Capability /CapabilityName:$capability /Source:$LIPContent
    }

    foreach($feature in $additionalFODList){
    Dism /Online /Add-Package /PackagePath:$feature
    }

    if($langGroup){
    Dism /Online /Add-Capability /CapabilityName:Language.Fonts.$langGroup~~~und-$langGroup~0.0.1.0 
    }

    ##Add installed language to language list##
    $LanguageList = Get-WinUserLanguageList
    $LanguageList.Add("$targetlanguage")
    Set-WinUserLanguageList $LanguageList -force
    ```

    >[!NOTE]
    >本示例脚本使用的是西班牙语 (es-Es) 语言代码。 要自动安装其他语言相应的文件，请将“$targetLanguage”参数更改为正确的语言代码。 相关语言代码，请参阅[适用于 Windows 的可用语言包](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)。

    该脚本可能需要一段时间才能运行结束，具体取决于需要安装的语言数量。 初始设置完成后，还可以通过使用不同的“$targetLanguage”参数再次运行脚本来安装其他语言。

5. 要自动选择相应的安装文件，请将[可用的 Windows 10 1809 语言和功能按需表](https://download.microsoft.com/download/7/6/0/7600F9DC-C296-4CF8-B92A-2D85BAFBD5D2/Windows-10-1809-FOD-to-LP-Mapping-Table.xlsx )另存为 CSV 文件，然后将其保存到 PowerShell 脚本所在的同一文件夹中。

6. 脚本运行完毕后，请转到“开始” > “设置” > “时间和语言” > “语言”，确保正确安装了语言包。 如果存在相应的语言文件，即表示一切都已设置完毕。

7. 最后，如果在安装语言时虚拟机已连接到 Internet，则需要执行清除过程，以删除所有不必要的语言体验包。 要清理文件，请运行以下命令：

    ```powershell
    ##Cleanup to prepare sysprep##
    Remove-AppxPackage -Package Microsoft.LanguageExperiencePackes-ES_22000.8.13.0_neutral__8wekyb3d8bbwe

    Remove-AppxPackage -Package Microsoft.OneDriveSync_22000.8.13.0_neutral__8wekyb3d8bbwe
    ```

    要清理不同的语言包，请将“es-ES”替换为其他语言代码。

8. 完成清理后，请断开共享。 

## <a name="finish-customizing-your-image"></a>完成映像自定义

安装语言包之后，可以安装要添加到自定义映像的任何其他软件。

完成映像自定义后，需要运行系统准备工具 (sysprep)。

若要运行 Sysprep，请执行以下操作：

1. 打开提升的命令提示符并运行以下命令，以通用化映像：  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. 如果遇到任何问题，请检查 C 盘中的 SetupErr.log 文件（路径为：Windows > System32 > Sysprep > Panther）。 完成此操作后，请按照 [Microsoft Store 应用程序出现 Sysprep 错误](/troubleshoot/windows-client/deployment/sysprep-fails-remove-or-update-store-apps)的说明，排查设置故障。

3. 如果设置成功，按照[在 Azure 中创建通用化虚拟机的托管映像](../virtual-machines/windows/capture-image-resource.md)中的说明，停止虚拟机，然后在托管映像中将其捕获。

4. 你现在可以使用自定义映像来部署 Azure 虚拟桌面主机池。 若要了解如何部署主机池，请参阅[教程：使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)。

>[!NOTE]
>用户更改现实与语言后，需要注销其 Azure 虚拟桌面会话，然后重新登录。 用户必须在“开始”菜单中注销。

## <a name="next-steps"></a>后续步骤

参阅[将语言包添加到 Windows 10 多会话映像](language-packs.md)，了解如何安装适用于 Windows 10 多会话虚拟机的语言包。

要了解相关已知问题列表，请参阅[在 Windows 10 中添加语言：已知问题](/windows-hardware/manufacture/desktop/language-packs-known-issue)。
