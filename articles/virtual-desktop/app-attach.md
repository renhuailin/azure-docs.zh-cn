---
title: 配置 Azure 虚拟桌面 MSIX 应用附加 PowerShell 脚本 - Azure
description: 如何为 Azure 虚拟桌面的 MSIX 应用附加创建 PowerShell 脚本。
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6b54afdb77bd1fc3a958b959dad4fcb030e1fe2f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228779"
---
# <a name="create-powershell-scripts-for-msix-app-attach"></a>为 MSIX 应用附加创建 PowerShell 脚本

本主题介绍如何为 MSIX 应用附加设置 PowerShell 脚本。

## <a name="install-certificates"></a>安装证书

必须在主机池中的所有会话主机上安装证书，该主机池将托管来自 MSIX 应用附加包的应用。

如果你的应用使用的证书不是受公共信任的或是自签名的，请按以下方法安装证书：

1. 右键单击该包，选择“属性”。
2. 在出现的窗口中，选择“数字签名”选项卡。选项卡上的列表中应该只有一个项。选择该项以突出显示该项，然后选择“详细信息”。
3. 出现“数字签名详细信息”窗口时，选择“常规”选项卡，然后依次选择“查看证书”、“安装证书”  。
4. 当安装程序打开时，选择“本地计算机”作为存储位置，然后选择“下一步” 。
5. 如果安装程序询问你是否允许应用对设备进行更改，请选择“是”。
6. 选择“将所有证书放入以下存储区”，然后选择“浏览” 。
7. 当“选择证书存储”窗口出现时，选择“受信任的人员”，然后选择“确定” 。
8. 依次选择“下一步”、“完成” 。

## <a name="enable-microsoft-hyper-v"></a>启用 Microsoft Hyper-V

必须启用 Microsoft Hyper-V，因为需要使用 `Mount-VHD` 命令进行暂存，使用 `Dismount-VHD` 进行转储。

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>此更改将需要你重新启动虚拟机。

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>为 MSIX 应用附加准备 PowerShell 脚本

MSIX 应用附加有四个不同的阶段，必须按以下顺序执行：

1. 阶段
2. 注册
3. 取消注册
4. 转储

每个阶段都会创建一个 PowerShell 脚本。 可在[此处](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)查看适用于每个阶段的示例脚本。

### <a name="stage-powershell-script"></a>暂存 PowerShell 脚本

在更新 PowerShell 脚本之前，请确保在 VHD 中具有卷的卷 GUID。 获取卷 GUID：

1.  打开在运行脚本的 VM 中 VHD 所在的网络共享。

2.  右键单击 VHD 并选择“装载”。 这会将 VHD 装载到驱动器号。

3.  装载 VHD 后，“文件资源管理器”窗口将打开。 找到父文件夹并更新“$parentFolder”变量

    >[!NOTE]
    >若找不到父文件夹，则表示 MSIX 未正确展开。 重复上一部分中的步骤，然后重试。

4.  打开父文件夹。 如果正确展开，将看到与包同名的文件夹。 更新“$packageName”变量以匹配此文件夹的名称。

    例如，`VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe` 。

5.  打开命令提示并输入“mountvol”。 此命令将显示卷及其 GUID 的列表。 复制驱动器号与在步骤 2 中装载 VHD 的驱动器匹配的卷的 GUID。

    例如，在此 mountvol 命令的输出示例中，如果将 VHD 安装到驱动器 C，则需要复制上面的值 `C:\`：

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  用刚才复制的卷 GUID 更新“$volumeGuid”变量。

7. 打开管理员 PowerShell 提示符并使用应用于环境的变量更新以下 PowerShell 脚本。

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName 
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>注册 PowerShell 脚本

若要运行注册表脚本，请运行以下 PowerShell cmdlet，将其中占位符值替换为应用于环境的值。

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>取消注册 PowerShell 脚本

对于此脚本，将“$packageName”的占位符替换为要测试的包的名称。

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>转储 PowerShell 脚本

对于此脚本，将“$packageName”的占位符替换为要测试的包的名称。 在生产部署中，最好是在关闭时运行它。

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

>[!NOTE]
>即使在执行 destage 脚本后 $volumeGuid 点仍然存在，也可以关闭设备。

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>为 MSIX 应用附加代理设置模拟脚本

创建脚本之后，用户可以手动运行它们，或者将其设置为自动运行的启动、登录、注销和关闭脚本。 若要了解有关这些类型脚本的详细信息，请参阅[在组策略中使用启动、关闭、登录和注销脚本](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)。

每个自动脚本都运行应用附加脚本的一个阶段：

- 启动脚本运行暂存脚本。
- 登录脚本运行注册脚本。
- 注销脚本运行取消注册脚本。
- 关闭脚本运行转储脚本。

>[!NOTE]
>可以使用暂存脚本运行任务计划程序。 若要运行脚本，请将任务触发器设置为“当计算机启动时”，然后启用“以最高权限运行”。

## <a name="use-packages-offline"></a>脱机使用包

如果通过网络中或未连接到 Internet 的设备使用[适用于企业的 Microsoft Store](https://businessstore.microsoft.com/) 或[适用于教育的 Microsoft Store](https://educationstore.microsoft.com/) 中的包，则需要从 Microsoft Store 获取包许可证并将其安装到设备上，才能成功运行该应用。 如果设备处于联机状态并且可以连接到适用于企业的 Microsoft Store，就会自动下载所需的许可证，但如果处于脱机状态，则需要手动设置许可证。

若要安装许可证文件，需要使用 PowerShell 脚本来调用 WMI Bridge 提供程序中的 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 类。

下面介绍如何设置脱机使用的许可证：

1. 从适用于企业的 Microsoft Store 下载应用包、许可证和必需的框架。 你需要编码和未编码的许可证文件。 可在[此处](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)找到详细的下载说明。
2. 在步骤 3 的脚本中更新以下变量：
      1. `$contentID` 是未编码的许可证文件 (.xml) 中的 ContentID 值。 可以在所选的文本编辑器中打开许可证文件。
      2. `$licenseBlob` 是已编码的许可证文件 (.bin) 中许可证 blob 的整个字符串。 可以在所选的文本编辑器中打开已编码的许可证文件。
3. 在管理员 PowerShell 提示符中运行以下脚本。 建议在[暂存脚本](#stage-powershell-script)的末尾执行许可证安装，该脚本也需要在管理员提示符中运行。

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>后续步骤

当前不支持此功能，但可以在 [Azure 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 向社区提问。

还可以在 [Azure 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Azure 虚拟桌面的反馈。
