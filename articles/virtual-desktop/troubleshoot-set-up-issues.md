---
title: Azure 虚拟桌面环境主机池创建 - Azure
description: 如何在设置 Azure 虚拟桌面环境期间排查和解决租户和主机池问题。
author: Heidilohr
ms.topic: troubleshooting
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 7e2c4c6a68bb8db4434a233e7b7c658074ed015a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804132"
---
# <a name="host-pool-creation"></a>创建主机池

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md)。

本文介绍了 Azure 虚拟桌面租户和相关会话主机池基础结构的初始设置过程中的问题。

## <a name="provide-feedback"></a>提供反馈

请访问 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum)，与产品团队和活跃的社区成员共同探讨 Azure 虚拟桌面服务。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>获取 Windows 10 企业版多会话映像

若要使用 Windows 10 企业版多会话映像，请转到 Azure 市场，选择“入门” > “Microsoft Windows 10”>[Windows 10 企业版多回话(版本 1809)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)。

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>使用 Azure 门户创建主机池时出现的问题

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>错误：访问服务时出现“创建免费帐户”

> [!div class="mx-imgBorder"]
> ![显示 Azure 门户的图像，其中显示“创建免费帐户”消息](media/create-new-account.png)

原因：用于登录 Azure 的帐户中没有活动订阅，或者该帐户无权查看订阅。

解决方法：使用至少具有参与者级别访问权限的帐户登录要在其中部署会话主机虚拟机 (VM) 的订阅。

### <a name="error-exceeding-quota-limit"></a>错误：“超过配额限制”

如果操作超过配额限制，则可以执行下列操作之一：

- 使用相同参数创建新主机池，但 VM 和 VM 核心数较少。

- 在浏览器中打开在 statusMessage 字段中看到的链接，以提交为指定 VM SKU 增加 Azure 订阅配额的请求。

### <a name="error-cant-see-user-assignments-in-app-groups"></a>错误：无法查看应用组中的用户分配。

原因：此错误通常发生在将订阅从 1 个 Azure Active Directory (AD) 租户移到另一个租户之后。 如果旧分配仍与旧 Azure AD 租户关联，则 Azure 门户会失去对它们的跟踪。

解决方法：需要将用户重新分配给应用组。

### <a name="i-only-see-us-when-setting-the-location-for-my-service-objects"></a>仅当为服务对象设置位置时才会看到美国

原因：就 Azure 虚拟桌面服务来说，Azure 目前不支持该区域。 若要了解我们支持的地理位置，请查看[数据位置](data-locations.md)。 如果 Azure 虚拟桌面支持该位置，但你在尝试选择位置时它仍未显示，则表示资源提供程序尚未进行更新。

解决方法：若要获取最新的区域列表，请重新注册资源提供程序：

1. 转到“订阅”，选择相关订阅。
2. 选择“资源提供程序”。
3. 选择“Microsoft.DesktopVirtualization”，然后从操作菜单中选择“重新注册” 。

重新注册资源提供程序时，你不会看到任何特定 UI 反馈或更新状态。 重新注册过程也不会干扰现有环境。

## <a name="azure-resource-manager-template-errors"></a>Azure 资源管理器模板错误

按照以下说明解决 Azure 资源管理器模板和 PowerShell DSC 的不成功部署。

1. 使用 [Azure 资源管理器的查看部署操作](../azure-resource-manager/templates/deployment-history.md)查看部署中的错误。
2. 如果部署中没有错误，请使用[查看活动日志以审核对资源的操作](../azure-monitor/essentials/activity-log.md)，来查看活动日志中的错误。
3. 确定错误后，请使用错误消息和[解决 Azure 资源管理器中的常见 Azure 部署错误](../azure-resource-manager/templates/common-deployment-errors.md)中的资源来解决此问题。
4. 删除在之前的部署过程中创建的任何资源，然后重试部署模板。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>错误：部署失败...\<hostname>/joindomain

> [!div class="mx-imgBorder"]
> ![部署失败的屏幕截图。](media/failure-joindomain.png)

原始错误示例：

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

原因 1：为将 VM 加入到域而提供的凭据不正确。

解决方法 1：如果 VM 未加入[会话主机 VM 配置](troubleshoot-vm-configuration.md)中的域，请参阅“凭据错误”。

原因 2：无法解析域名。

解决方法 2：请参阅[会话主机 VM 配置](troubleshoot-vm-configuration.md)中的[错误：无法解析域名](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve)。

原因 3：虚拟网络 (VNET) DNS 配置设置为“默认值”。

若要修复此错误，请执行以下操作：

1. 打开 Azure 门户，转到“虚拟网络”选项卡。
2. 找到 VNET，然后选择“DNS 服务器”。
3. DNS 服务器菜单应出现在屏幕右侧。 在该菜单上，选择“自定义”。
4. 请确保“自定义”下列出的 DNS 服务器匹配域控制器或 Active Directory 域。 如果看不到 DNS 服务器，可以通过在“添加 DNS 服务器”字段中输入其值来添加它。

### <a name="error-your-deployment-failedunauthorized"></a>错误：部署失败...\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

原因：你使用的订阅类型无法访问客户尝试部署的区域中的所需功能。 例如，MSDN、免费或教育订阅可能显示此错误。

解决方法：更改订阅类型或区域，使其可以访问所需的功能。

### <a name="error-vmextensionprovisioningerror"></a>错误：VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![“部署失败”屏幕截图，其中终端预配状态为失败。](media/failure-vmextensionprovisioning.png)

原因 1：Azure 虚拟桌面环境出现暂时性错误。

原因 2：连接发生暂时性错误。

解决方法：使用 PowerShell 登录，确认 Azure 虚拟桌面环境正常运行。 在[使用 PowerShell 创建主机池](create-host-pools-powershell.md)中手动完成 VM 注册。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>错误：不允许使用指定的管理员用户名

> [!div class="mx-imgBorder"]
> ![因指定的管理员不被允许而失败的部署的屏幕截图。](media/failure-username.png)

原始错误示例：

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

原因：提供的密码包含禁止的子字符串（admin、administrator、root）。

解决方法：更新用户名或使用其他用户。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>错误：VM 在处理扩展时报告了一项故障

> [!div class="mx-imgBorder"]
> ![已完成的资源操作的屏幕截图，其中“部署失败”中具有终端预配状态。](media/failure-processing.png)

原始错误示例：

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

原因：PowerShell DSC 扩展无法获取 VM 上的管理员访问权限。

解决方法：确认用户名和密码在虚拟机上具有管理访问权限，然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>错误：DeploymentFailed - PowerShell DSC 配置“FirstSessionHost”已完成但出现错误

> [!div class="mx-imgBorder"]
> ![因 PowerShell DSC 配置“FirstSessionHost”错误完成而失败的部署的屏幕截图。](media/failure-dsc.png)

原始错误示例：

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

原因：PowerShell DSC 扩展无法获取 VM 上的管理员访问权限。

解决方法：确认提供的用户名和密码在虚拟机上具有管理访问权限，然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误：DeploymentFailed – InvalidResourceReference

原始错误示例：

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

原因：资源组名称的一部分用于由模板创建的某些资源。 由于名称与现有资源匹配，模板可以从不同的组中选择现有资源。

解决方法：运行 Azure 资源管理器模板来部署会话主机 VM 时，使订阅资源组名称的前两个字符惟一。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误：DeploymentFailed – InvalidResourceReference

原始错误示例：

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

原因：此错误是因为通过 Azure 资源管理器模板创建的 NIC 与 VNET 中的另一个 NIC 具有相同名称。

解决方法：使用其他主机前缀。

### <a name="error-deploymentfailed--error-downloading"></a>错误：DeploymentFailed – 错误下载

原始错误示例：

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

原因：此错误是由于静态路由、防火墙规则或 NSG 阻止下载关联到 Azure 资源管理器模板的 zip 文件导致的。

解决方法：删除阻止的静态路由、防火墙规则或 NSG。 （可选）在文本编辑器中打开 Azure 资源管理器模板 json 文件，获取 zip 文件的链接，并将资源下载到允许位置。

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>错误：删除 VM 后无法从主机池删除会话主机

原因：删除 VM 之前，需要先删除会话主机。

解决方法：将会话主机置于排出模式，从会话主机注销所有用户，然后删除主机。

## <a name="next-steps"></a>后续步骤

- 如需大致了解如何排查 Azure 虚拟桌面问题和跟踪升级，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要排查在 Azure 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要排查与 Azure 虚拟桌面代理或会话连接性相关的问题，请参阅[排查常见的 Azure 虚拟桌面代理问题](troubleshoot-agent.md)。
- 若要排查 Azure 虚拟桌面客户端连接问题，请参阅 [Azure 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要排查远程桌面客户端的问题，请参阅[排查远程桌面客户端问题](troubleshoot-client.md)
- 若要排查将 PowerShell 与 Azure 虚拟桌面结合使用时遇到的问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要详细了解该服务，请参阅 [Azure 虚拟桌面环境](environment-setup.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-monitor/essentials/activity-log.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
