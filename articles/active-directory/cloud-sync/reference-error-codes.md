---
title: Azure AD Connect 云同步错误代码和说明
description: 云同步错误代码参考文章
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22e677e5a86f60627552161f7b2115c08695dbf0
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613029"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect 云同步错误代码和说明
下面是错误代码及其说明的列表


## <a name="error-codes"></a>错误代码

|错误代码|详细信息|方案|解决方法|
|-----|-----|-----|-----|
|TimeOut|错误消息：我们检测到在联系本地代理和同步配置时出现请求超时错误。 有关云同步代理的其他问题，请参阅故障排除指南。|请求超时。当前超时值为10分钟。|请参阅 [故障排除指南](how-to-troubleshoot.md)|
|HybridSynchronizationActiveDirectoryInternalServerError|错误消息：此时无法处理此请求。 如果此问题仍然存在，请与支持人员联系并提供以下作业标识符： AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926。 其他详细信息：处理 HTTP 请求时引发了异常。 |无法处理在 SCIM 请求中接收到搜索请求的参数。|有关详细信息，请参阅此异常的 "响应" 属性返回的 HTTP 响应。|
|HybridIdentityServiceNoAgentsAssigned|错误消息：我们找不到你尝试同步的域的活动代理。请检查是否已删除代理。 如果是这样，请重新安装代理。|没有代理正在运行。 可能已删除代理。 注册新代理。|"在这种情况下，不会在门户中看到任何分配给域的代理。|
|HybridIdentityServiceNoActiveAgents|错误消息：我们找不到你尝试同步的域的活动代理。请查看代理是否正在运行，方法是转到安装了代理的服务器，并检查 "服务" 下的 "Microsoft Azure AD 云同步代理" 是否正在运行。|"代理未在侦听此终结点终结点。 [代理位于不允许连接到服务总线的防火墙后面](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|错误消息：此时无法处理此请求。 如果此问题仍然存在，请与支持人员联系并提供以下作业标识符： AD2AADProvisioning. 3a2a0d8418f34f54a03da5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9。 其他详细信息：云同步设置似乎存在问题。 请在本地 AD 域上重新注册云同步代理，并从 Azure 门户重新启动配置。|必须设置资源名称，以便他知道要联系哪个代理。|请在本地 AD 域上重新注册云同步代理，并从 Azure 门户重新启动配置。|
|HybridIdentityServiceAgentSignalingError|错误消息：此时无法处理此请求。 如果此问题仍然存在，请与支持人员联系并提供以下作业标识符： AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62e8-42e3-b495-18d5272eb3f9。 其他详细信息：此时无法处理此请求。 如果此问题仍然存在，请与支持人员联系，其中包含配置) 的状态窗格 (的作业 ID。|服务总线无法将消息发送到代理。 可能是服务总线中断，或代理没有响应。|如果此问题仍然存在，请与支持人员联系，其中包含配置) 的状态窗格 (的作业 ID。|
|AzureDirectoryServiceServerBusy|错误消息：出现错误。 错误代码：81。 错误说明： Azure Active Directory 当前正忙。 将自动重试此操作。 如果此问题持续超过24小时，请联系技术支持。 跟踪 ID：8a4ab3b5-3664-4278-ab64-9cff37fd3f4f 服务器名称：|Azure Active Directory 当前正忙。|如果此问题持续超过24小时，请联系技术支持。|
|AzureActiveDirectoryInvalidCredential|错误消息：我们发现用于运行 Azure AD Connect 云同步的服务帐户出现问题。可以按照 [此处](https://go.microsoft.com/fwlink/?linkid=2150988)的说明修复云服务帐户。 如果错误仍然存在，请与支持人员联系，其中包含配置) 的状态窗格 (的作业 ID。 其他错误详细信息： CredentialsInvalid AADSTS50034： skydrive365.onmicrosoft.com 目录中不存在用户帐户 {EmailHidden}。 若要登录到此应用程序，必须将帐户添加到目录中。 跟踪 ID：14b63033-3bc9-4bd4-b871-5eb4b3500200 相关 ID：57d93ed1-be4d-483c-997c-a3b6f03deb00 时间戳： 2021-01-12 21：08：29Z |当租户中不存在同步服务帐户 ADToAADSyncServiceAccount 时，将引发此错误。 这可能是由于意外删除了帐户。|使用 [AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) 修复服务帐户。|
|AzureActiveDirectoryExpiredCredentials|错误消息：此时无法处理此请求。 如果此问题仍然存在，请与支持人员联系，其中包含配置) 的状态窗格 (的作业 ID。 其他错误详细信息： CredentialsExpired AADSTS50055：密码已过期。 跟踪 ID：989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 相关 ID： 1c69b196-1c3a-4381-9187-c84747807155 Timestamp： 2021-01-12 20：59：31Z | 响应状态代码不指示成功： 401 (未授权的) 。|AAD Sync 的服务帐户凭据已过期。|可以按照中的说明来修复云服务帐户 https://go.microsoft.com/fwlink/?linkid=2150988 。 如果错误仍然存在，请与支持人员联系，其中包含配置) 的状态窗格 (的作业 ID。  其他错误详细信息：已为已过期的 OAuth 令牌交换管理 Azure Active Directory 租户凭据。 "|
|AzureActiveDirectoryAuthenticationFailed|错误消息：此时无法处理此请求。 如果此问题仍然存在，请与支持人员联系并提供以下作业标识符： AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405d-a3b9-de87761dc3ac。 其他详细信息：此时无法处理此请求。 如果此问题仍然存在，请与支持人员联系，其中包含配置) 的状态窗格 (的作业 ID。 其他错误详细信息： UnexpectedError。|未知错误。|如果此问题仍然存在，请与支持人员联系，其中包含配置) 的状态窗格 (的作业 ID。|

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
