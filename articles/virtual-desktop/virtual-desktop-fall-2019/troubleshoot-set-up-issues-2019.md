---
title: Azure 虚拟桌面（经典）租户主机池创建 - Azure
description: 如何在安装 Azure 虚拟桌面（经典）租户环境期间排除和解决租户和主机池问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 61aea8002faa92c88081c78bc9ff05ec7e053094
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818509"
---
# <a name="tenant-and-host-pool-creation-in-azure-virtual-desktop-classic"></a>Azure 虚拟桌面（经典）中的租户和主机池创建

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[本文](../troubleshoot-set-up-issues.md)。

本文介绍了 Azure 虚拟桌面租户和相关会话主机池基础结构的初始设置过程中的问题。

## <a name="provide-feedback"></a>提供反馈

请访问 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Azure 虚拟桌面服务。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>获取 Windows 10 企业版多会话映像

若要使用 Windows 10 企业版多会话映像，请转到 Azure 市场，选择“入门” > “Microsoft Windows 10”>[适用于虚拟桌面的 Windows 10 企业版(版本 1809)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)。

> [!div class="mx-imgBorder"]
> ![选择适用于虚拟桌面的 Windows 10 企业版(版本 1809) 的屏幕截图。](../media/AzureMarketPlace.png)

## <a name="creating-azure-virtual-desktop-tenant&quot;></a>创建 Azure 虚拟桌面租户

此部分介绍了创建 Azure 虚拟桌面租户时可能存在的问题。

### <a name=&quot;error-aadsts650052-the-app-needs-access-to-a-service&quot;></a>错误：AADSTS650052 应用需要访问服务。

原始错误示例：

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

原因：未向 Azure Active Directory 实例中的 Azure 虚拟桌面授予同意。

解决方法：[按照本指南](./tenant-setup-azure-active-directory.md#grant-permissions-to-azure-virtual-desktop)授予同意。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>错误：用户无权查询管理服务

> [!div class="mx-imgBorder"]
> ![PowerShell 窗口的屏幕截图，其中用户无权查询管理服务。](../media/UserNotAuthorizedNewTenant.png)

原始错误示例：

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

原因：未在 Azure Active Directory 中为登录用户分配“TenantCreator”角色。

解决方法：按照[将 TenantCreator 应用程序角色分配到 Azure Active Directory 租户中的用户](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)中的说明操作。 按说明操作后，你将有一个分配到“TenantCreator”角色的用户。

> [!div class="mx-imgBorder"]
> ![已分配 TenantCreator 角色的屏幕截图。](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-azure-virtual-desktop-session-host-vms"></a>创建 Azure 虚拟桌面会话主机 VM

可以通过多种方式创建会话主机 VM，但 Azure 虚拟桌面团队仅支持与 [Azure 市场](https://azuremarketplace.microsoft.com/)产品/服务相关的 VM 预配问题。 有关详细信息，请参阅[使用 Azure 虚拟桌面的问题 - 预配主机池 Azure 市场产品/服务](#issues-using-azure-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)。

## <a name="issues-using-azure-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>使用 Azure 虚拟桌面的问题 – 预配主机池 Azure 市场产品/服务

Azure 虚拟桌面 – 预配主机池模板可从 Azure 市场获得。

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>错误：使用 GitHub 中的链接时，出现“创建免费帐户”消息

> [!div class="mx-imgBorder"]
> ![用于创建免费帐户的屏幕截图。](../media/be615904ace9832754f0669de28abd94.png)

原因 1：用于登录到 Azure 的帐户中没有活动订阅，或者使用的帐户无权查看订阅。

解决方法 1：使用至少具有参与者访问权限的帐户登录到将在其中部署会话主机 VM 的订阅。

原因 2：使用的订阅是 Microsoft 云服务提供商 (CSP) 租户的一部分。

解决方法 2：转到 GitHub 位置以创建并预配新的 Azure 虚拟桌面主机池，然后按照以下说明操作 ：

1. 右键单击“部署到 Azure”，然后选择“复制链接地址”。
2. 打开“记事本”并粘贴链接。
3. 在 # 字符之前，插入 CSP 最终客户租户名称。
4. 在浏览器中打开新链接，Azure 门户将加载模板。

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>错误：收到“模板部署无效”错误

> [!div class="mx-imgBorder"]
> ![“模板部署...无效”错误屏幕截图](../media/troubleshooting-marketplace-validation-error-generic.png)

在采取特定操作之前，需要检查活动日志以查看失败的部署验证的详细错误。

查看活动日志中的错误：

1. 退出当前的 Azure 市场部署产品/服务。
2. 在顶部搜索栏中，搜索并选择“活动日志”。
3. 找到状态为“失败”的名为“验证部署”的活动，并选择该活动。

   > [!div class="mx-imgBorder"]
   > ![单个**验证部署**活动且状态为**失败**的屏幕截图](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. 选择 JSON，然后向下滚动到屏幕底部，直到看到“statusMessage”字段。

   > [!div class="mx-imgBorder"]
   > ![失败活动的屏幕截图，其中 JSON 文本的 statusMessage 属性周围有一个红色框。](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

如果操作模板超过配额限制，则可以执行下列操作之一来解决此问题：

 - 使用首次使用的参数运行 Azure 市场，但这次使用的 VM 和 VM 内核数更少。
 - 在浏览器中打开在“statusMessage”字段中看到的链接，以提交为指定 VM SKU 增加 Azure 订阅配额的请求。

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure 资源管理器模板和 PowerShell Desired State Configuration (DSC) 错误

按照以下说明解决 Azure 资源管理器模板和 PowerShell DSC 的不成功部署。

1. 使用 [Azure 资源管理器的查看部署操作](../../azure-resource-manager/templates/deployment-history.md)查看部署中的错误。
2. 如果部署中没有错误，请使用[查看活动日志以审核对资源的操作](../../azure-monitor/essentials/activity-log.md)，来查看活动日志中的错误。
3. 确定错误后，请使用错误消息和[解决 Azure 资源管理器中的常见 Azure 部署错误](../../azure-resource-manager/templates/common-deployment-errors.md)中的资源来解决此问题。
4. 删除在之前的部署过程中创建的任何资源，然后重试部署模板。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>错误：部署失败...\<hostname>/joindomain

> [!div class="mx-imgBorder"]
> ![部署失败的屏幕截图。](../media/e72df4d5c05d390620e07f0d7328d50f.png)

原始错误示例：

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

原因 1：为将 VM 加入到域而提供的凭据不正确。

解决方法 1：如果 VM 未加入[会话主机 VM 配置](troubleshoot-vm-configuration-2019.md)中的域，请参阅“凭据错误”。

原因 2：无法解析域名。

解决方法 2：请参阅[会话主机 VM 配置](troubleshoot-vm-configuration-2019.md)中的[错误：无法解析域名](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve)。

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
> ![“部署失败”屏幕截图，其中终端预配状态为失败。](../media/7aaf15615309c18a984673be73ac969a.png)

原因 1：Azure 虚拟桌面环境出现暂时性错误。

原因 2：连接发生暂时性错误。

解决方法：使用 PowerShell 登录，确认 Azure 虚拟桌面环境正常运行。 在[使用 PowerShell 创建主机池](create-host-pools-powershell-2019.md)中手动完成 VM 注册。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>错误：不允许使用指定的管理员用户名

> [!div class="mx-imgBorder"]
> ![因指定的管理员不被允许而失败的部署的屏幕截图。](../media/f2b3d3700e9517463ef88fa41875bac9.png)

原始错误示例：

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

原因：提供的密码包含禁止的子字符串（admin、administrator、root）。

解决方法：更新用户名或使用其他用户。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>错误：VM 在处理扩展时报告了一项故障

> [!div class="mx-imgBorder"]
> ![已完成的资源操作的屏幕截图，其中“部署失败”中具有终端预配状态。](../media/49c4a1836a55d91cd65125cf227f411f.png)

原始错误示例：

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

原因：PowerShell DSC 扩展无法获取 VM 上的管理员访问权限。

解决方法：确认用户名和密码在虚拟机上具有管理访问权限，然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>错误：DeploymentFailed - PowerShell DSC 配置“FirstSessionHost”已完成但出现错误

> [!div class="mx-imgBorder"]
> ![因 PowerShell DSC 配置“FirstSessionHost”错误完成而失败的部署的屏幕截图。](../media/64870370bcbe1286906f34cf0a8646ab.png)

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

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>错误：用户无权查询管理服务

原始错误示例：

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

原因：指定的 Azure 虚拟桌面租户管理员没有有效的角色分配。

解决方法：创建 Azure 虚拟桌面租户的用户需要登录到 Azure 虚拟桌面 PowerShell，并为尝试的用户分配角色分配。 如果正在运行 GitHub Azure 资源管理器模板参数，请使用 PowerShell 命令执行以下说明：

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Azure Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>错误：用户需要 Azure AD 多重身份验证 (MFA)

> [!div class="mx-imgBorder"]
> ![因缺少多重身份验证 (MFA) 而失败的部署的屏幕截图](../media/MFARequiredError.png)

原始错误示例：

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

原因：指定的 Azure 虚拟桌面租户管理员需要进行 Azure AD 多重身份验证 (MFA) 才能登录。

解决方法：按照[教程：使用 PowerShell 创建服务主体和角色分配](create-service-principal-role-powershell.md)中的步骤创建服务主体，并为其分配一个适用于 Azure 虚拟桌面租户的角色。 在验证可以使用服务主体登录到 Azure 虚拟桌面后，重新运行 Azure 市场产品/服务或 GitHub Azure 资源管理器模板，具体取决于所使用的方法。 按照以下说明为方法输入正确的参数。

如果你运行的是 Azure 市场产品/服务，请提供以下参数的值，以正确地向 Azure 虚拟桌面进行身份验证：

- Azure 虚拟桌面租户 RDS 所有者：服务主体
- 应用程序 ID：创建的新服务主体的应用程序标识
- 密码/确认密码：为服务主体生成的密码机密
- Azure AD 租户 ID：创建的服务主体的 Azure AD 租户 ID

如果你运行的是 GitHub Azure 资源管理器模板，请提供以下参数的值，以正确地向 Azure 虚拟桌面进行身份验证：

- 租户管理员用户主体名称 (UPN) 或应用程序 ID：你创建的新服务主体的应用程序标识
- 租户管理员密码：为服务主体生成的密码机密
- IsServicePrincipal：true
- AadTenantId：创建的服务主体的 Azure AD 租户 ID

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>错误：在配置虚拟网络时无法使用 vmSubnet

原因：在 Azure 虚拟桌面市场模板中，UI 只显示至少拥有与模板中指定的 VM 总数相同的 IP 地址的子网。 子网中的实际可用 IP 地址数只需等同于正在部署的新 VM 的数目，但当前 UI 无法计算此值。

解决方法：你可以指定一个子网，其中至少有与不使用市场 UI 所添加的 VM 数目一样多的 IP 地址，可以通过在[重新部署现有部署](expand-existing-host-pool-2019.md#redeploy-from-azure)或[使用 GitHub 中的基础 ARM 模板进行部署](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool)时指定“existingSubnetName”参数中的子网名称来完成此操作。

## <a name="next-steps"></a>后续步骤

- 如需大致了解如何排查 Azure 虚拟桌面问题和跟踪升级，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview-2019.md)。
- 若要排查在 Azure 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration-2019.md)。
- 若要排查 Azure 虚拟桌面客户端连接问题，请参阅 [Azure 虚拟桌面服务连接](troubleshoot-service-connection-2019.md)。
- 若要排查远程桌面客户端的问题，请参阅[排查远程桌面客户端问题](../troubleshoot-client.md)
- 若要排查将 PowerShell 与 Azure 虚拟桌面结合使用时遇到的问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell-2019.md)。
- 若要详细了解该服务，请参阅 [Azure 虚拟桌面环境](environment-setup-2019.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../../azure-monitor/essentials/activity-log.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../../azure-resource-manager/templates/deployment-history.md)。