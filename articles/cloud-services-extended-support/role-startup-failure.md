---
title: Azure 云服务（外延支持）的角色实例启动失败
description: 排查 Azure 云服务（外延支持）的角色实例启动失败的问题
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382349"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>排查无法启动的 Azure 云服务（外延支持）角色的问题
下面是与无法启动的 Azure 云服务（外延支持）角色相关的一些常见问题和解决方法。

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>云服务操作失败并出现 RoleInstanceStartupTimeoutError
云服务（外延支持）的一个或多个角色实例在规定的时间内无法启动。 这些角色实例可能一直在启动或处于正在回收状态，并且角色实例可能失败并出现 RoleInstanceStartupTimeoutError。这是一个角色应用程序错误。 角色应用程序包含两个主要部分：“启动任务”和“角色代码（实现 RoleEntryPoint）”，两者都可能导致角色回收。 如果角色崩溃，PaaS 代理始终会重新启动该角色。 

若要在出错时获取角色实例的当前状态和详细信息，请使用：

* PowerShell：使用 [Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) cmdlet 检索有关云服务中角色实例的运行时状态的信息。
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Azure 门户：转到你的云服务并选择“角色和实例”选项卡。单击角色实例以获取其状态详细信息 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="插图显示门户上的角色启动失败。":::
   
下面是与无法启动的，或者在正在初始化、繁忙和正在停止状态之间循环切换状态的 Azure 云服务（外延支持）角色相关的一些常见问题和解决方法。

## <a name="missing-dlls-or-dependencies"></a>缺少 DLL 或依赖项
DLL 或程序集缺失可能导致出现不响应的角色以及在“正在初始化”、“忙”和“正在停止”状态之间循环的角色。
DLL 或程序集缺失的症状可能为：

* 角色实例的状态在“**正在初始化**”、“**忙**”、“**正在停止**”之间循环。
* 角色实例已转为“**就绪**”状态，但在导航到 Web 应用程序时未显示相应页面。

若要调查这些问题，可采用几种推荐的方法。

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>在 Web 角色中诊断缺失 DLL 的问题
如果导航到在 Web 角色中部署的网站，且浏览器显示类似于下面的服务器错误，可能指示 DLL 缺失。

:::image type="content" source="media/role-startup-failure-2.png" alt-text="插图显示角色启动失败时出现的运行时错误":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>通过关闭自定义错误来诊断问题
可通过配置 Web 角色的 web.config，将自定义错误模式设置为“关闭”并重新部署服务，来查看更完整的错误信息。
若要在不使用远程桌面的情况下查看更完整的错误，请执行以下操作：
1.  在 Microsoft Visual Studio 中打开解决方案。
2.  在“解决方案资源管理器”中，找到 web.config 文件并打开它。
3.  在 web.config 文件中，找到 system.web 部分并添加以下行：
 ```xml
<customErrors mode="Off" />
```
4.  保存文件。
5.  重新打包并重新部署服务。
重新部署服务后，会看到错误消息，其中包含缺失的程序集或 DLL 的名称。

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>通过远程查看错误来诊断问题
可使用远程桌面来访问角色并远程查看更完整的错误信息。 通过以下步骤使用远程桌面来查看错误：
1.  为云服务（外延支持）启用远程桌面扩展。 有关详细信息，请参阅[使用 Azure 门户将远程桌面扩展应用到云服务（外延支持）](enable-rdp.md)
2.  在 Azure 门户中，在实例显示“就绪”状态后，远程连接到该实例。 有关对云服务（外延支持）使用远程桌面的详细信息，请参阅[使用远程桌面连接到角色实例](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled)
3.  使用在远程桌面配置期间指定的凭据登录到虚拟机。
4.  打开命令窗口。
5.  键入 IPconfig。
6.  记下 IPv4 地址值。
7.  打开 Internet Explorer。
8.  键入 Web 应用程序的地址和名称。 例如 http://<IPV4 Address>/default.aspx。
现在，导航到网站将返回更明确的错误消息：
* '/' 应用程序中出现服务器错误。
* 说明：执行当前 Web 请求期间，出现未处理的异常。 请检查堆栈跟踪信息，以了解有关该错误以及代码中导致错误的出处的详细信息。
* 异常详细信息：System.IO.FIleNotFoundException：未能加载文件或程序集“Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35”或它的某一个依赖项。 系统找不到指定的文件。
例如：

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="插图显示角色启动失败时出现的异常":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>使用计算模拟器诊断问题
可以使用 Azure 计算模拟器来诊断并解决缺失依赖项和出现 web.config 错误的问题。
为了在使用此诊断方法时获得最佳结果，应使用包含 Windows 的干净安装的计算机或虚拟机。 
1.  安装 [Azure SDK](https://azure.microsoft.com/downloads/) 
2.  在开发计算机上生成云服务项目。
3.  在 Windows 资源管理器中，导航到云服务项目的 bin\debug 文件夹。
4.  将 .csx 文件夹和 .cscfg 文件复制到用来调试问题的计算机。
5.  在干净的计算机上，打开 Azure SDK 命令提示符窗口并键入 csrun.exe /devstore:start。
6.  在命令提示符下，键入 run csrun <.csx 文件夹的路径> <.cscfg 文件的路径> /launchBrowser。
7.  角色启动后，会在 Internet Explorer 中看到详细的错误信息。 还可使用标准的 Windows 故障排除工具来进一步诊断问题。

## <a name="diagnose-issues-by-using-intellitrace"></a>使用 IntelliTrace 诊断问题
对于使用 .NET Framework 4 的辅助角色和 Web 角色，可以使用 Microsoft Visual Studio Enterprise 中提供的 [IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace)。
请按照以下步骤操作来部署启用了 IntelliTrace 的服务：
1.  确认已安装 Azure SDK 1.3 或更高版本。
2.  使用 Visual Studio 部署解决方案。 在部署期间，请选中“为 .NET 4 角色启用 IntelliTrace”复选框。
3.  实例启动后，打开“服务器资源管理器”。
4.  展开 Azure\Cloud Services 节点并查找部署。
5.  展开部署，直至看到角色实例。 右键单击其中一个实例。
6.  选择“查看 IntelliTrace 日志”。 这将打开“IntelliTrace 摘要”。
7.  查找摘要的异常部分。 如果存在异常，则会将该部分标记为“异常数据”。
8.  展开“异常数据”并查找类似如下所示的 System.IO.FileNotFoundException 错误：

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="插图显示角色启动失败时出现的异常数据" lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>解决缺失 DLL 和程序集的问题
若要纠正丢失 DLL 和程序集错误，请按照以下步骤进行操作：
1.  在 Visual Studio 中打开解决方案。
2.  在“解决方案资源管理器”中，打开 References 文件夹。
3.  单击错误中标识的程序集。
4.  在“属性”窗格中，找到“复制本地属性”并将值设置为 True。
5.  重新部署云服务。
确认所有错误均已更正后，可以在不选中“为 .NET 4 角色启用 IntelliTrace”复选框的情况下部署服务。

## <a name="next-steps"></a>后续步骤 
- 若要了解如何使用 Azure PaaS 计算机诊断数据对云服务角色问题进行故障排除，请参阅 [Kevin Williamson 博客系列](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)。
