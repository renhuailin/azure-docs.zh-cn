---
title: Azure 云服务（外延支持）的角色实例启动失败
description: 排查 Azure 云服务（外延支持）的角色实例启动失败的问题。
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 9c6fdee8dbb28e86e5084c9c0cfca97f5ac5ea05
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287151"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>排查无法启动的 Azure 云服务（外延支持）角色的问题

下面是与无法启动的 Azure 云服务（外延支持）角色相关的一些常见问题和解决方法。

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>云服务操作失败并出现 RoleInstanceStartupTimeoutError

Azure 云服务（外延支持）中的一个或多个角色实例可能启动缓慢，或者它们可能被回收，角色实例失败。 显示角色应用程序错误 `RoleInstanceStartupTimeoutError`。

角色应用程序包含可能导致角色回收的两个部分：“启动任务”和“角色代码（RoleEntryPoint 实现）”。 

如果角色停止，平台即服务 (PaaS) 代理将重新启动角色。

你可以使用 PowerShell 或 Azure 门户获取角色实例的当前状态和详细信息以诊断错误：

* PowerShell：使用 [Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) cmdlet 获取有关角色实例的运行时状态的信息：

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* Azure 门户：在门户中，转到云服务实例。 若要查看状态详细信息，请选择“角色和实例”，然后选择角色实例。

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="显示 Azure 门户中的角色启动失败的屏幕截图。":::

## <a name="missing-dlls-or-dependencies"></a>缺少 DLL 或依赖项

DLL 或程序集缺失可能导致角色未响应以及角色在各种状态之间循环。

下面是 DLL 或程序集缺失的一些症状：

* 角色实例的状态在“正在初始化”、“忙”、“正在停止”之间循环。
* 角色实例已转为“就绪”状态，但在转到 Web 应用程序时未显示相应页面。


在 Web 角色中部署且缺少 DLL 的网站可能会显示以下服务器运行时错误：

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="在角色启动失败后显示运行时错误的屏幕截图。":::

### <a name="resolve-missing-dlls-and-assemblies"></a>解决 DLL 和程序集缺失的问题

若要解决 DLL 和程序集缺失的问题，请执行以下操作：

1. 在 Visual Studio 中打开解决方案。
2. 在“解决方案资源管理器”中，打开 References 文件夹。
3. 选择错误中标识的程序集。
4. 在“属性”中，将“复制本地”属性设置为“True”。
5. 重新部署云服务。

验证并确认错误不再显示后，重新部署服务。 设置部署时，不要选中“为 .NET 4 角色启用 IntelliTrace”复选框。

## <a name="diagnose-role-instance-errors"></a>诊断角色实例错误

从以下选项中进行选择，以诊断角色实例的问题。

### <a name="turn-off-custom-errors"></a>关闭自定义错误

若要查看完整的错误信息，请在 Web 角色的 web.config 文件中，将自定义错误模式设置为 `off`，然后重新部署服务：

1. 在 Visual Studio 中打开解决方案。
2. 在解决方案资源管理器中，打开 web.config 文件。
3. 在 `system.web` 部分中，添加以下代码：

   ```xml
   <customErrors mode="Off" />
   ```

4. 保存文件。
5. 重新打包并重新部署服务。

重新部署服务后，错误消息包括缺失的程序集或 DLL 的名称。

### <a name="use-remote-desktop"></a>使用远程桌面

使用远程桌面来访问角色并查看完整的错误信息：

1. [为 Azure 云服务（外延支持）添加远程桌面扩展](enable-rdp.md)。
2. 在 Azure 门户中，当云服务实例显示“就绪”状态时，使用远程桌面登录到云服务。 有关详细信息，请参阅[使用远程桌面连接到角色实例](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled)。
3. 使用你用于设置远程桌面的凭据登录到虚拟机。
4. 打开命令提示符窗口。
5. 在命令提示符处，输入 ipconfig。 记下 IPv4 地址的返回值。
6. 打开 Microsoft Internet Explorer。
7. 在地址栏中，输入 IPv4 地址，后跟斜杠和 Web 应用程序默认文件的名称。 例如，`http://<IPv4 address>/default.aspx`。

如果现在转到该网站，你将看到包含详细信息的错误消息。 下面是一个示例：

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="显示错误消息示例的屏幕截图。":::
  
### <a name="use-the-compute-emulator"></a>使用计算模拟器

可以使用 Azure 计算模拟器来诊断并解决缺失依赖项和出现 web.config 错误的问题。 为了在使用此方法诊断问题时获得最佳结果，可以使用包含 Windows 的干净安装的计算机或虚拟机。

若要使用 Azure 计算模拟器诊断问题，请执行以下操作：

1. 安装 [Azure SDK](https://azure.microsoft.com/downloads/)。
2. 在开发计算机上生成云服务项目。
3. 在文件资源管理器的云服务项目中，转到 bin\debug 文件夹。
4. 将 .csx 文件夹和 .cscfg 文件复制到用来调试问题的计算机。
5. 在干净的计算机上打开 Azure SDK 命令提示符窗口。
6. 在命令提示符下，输入 csrun.exe /devstore:start。
7. 然后，输入 run csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser。
8. 角色启动后，Internet Explorer 会显示详细的错误信息。

如果需要更多诊断，可以使用标准的 Windows 故障排除工具。

### <a name="use-intellitrace"></a>使用 IntelliTrace

对于使用 .NET Framework 4 的辅助角色和 Web 角色，可以使用 [IntelliTrace](/visualstudio/debugger/intellitrace)。 Visual Studio Enterprise 中提供了 IntelliTrace。

若要部署已启用 IntelliTrace 的云服务，请执行以下操作：

1. 确认已安装 Azure SDK 1.3 或更高版本。
2. 在 Visual Studio 中部署解决方案。 设置部署时，选中“为 .NET 4 角色启用 IntelliTrace”复选框。
3. 角色实例启动后，打开“服务器资源管理器”。
4. 展开“Azure\云服务”节点。
5. 展开部署以列出角色实例。 右键单击某个角色实例。
6. 选择“查看 IntelliTrace 日志”。
7. 在“IntelliTrace 摘要”中，转到“异常数据”。
8. 展开“异常数据”并查找 `System.IO.FileNotFoundException` 错误：

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="角色启动失败的异常数据的屏幕截图。" lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure PaaS 计算机诊断数据排查云服务角色问题](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)。