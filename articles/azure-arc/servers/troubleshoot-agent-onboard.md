---
title: 排查已启用 Azure Arc 的服务器代理连接问题
description: 本文介绍了如何在尝试连接到服务时，排查已启用 Azure Arc 的服务器的已连接计算机代理的问题并解决这些问题。
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 8547e66ee8915c356b345bceb3cc52bde63713b0
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114390160"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>排查已启用 Azure Arc 的服务器代理连接问题

本文提供有关如何排查在尝试为 Windows 或 Linux 配置已启用 Azure Arc 的服务器连接的计算机代理时可能出现的问题，以及如何解决这些问题。 文中包括配置与服务的连接时的交互式和大规模安装方法。 如需常规信息，请参阅[已启用 Arc 的服务器概述](./overview.md)。

## <a name="agent-error-codes"></a>代理错误代码

如果在配置已启用 Azure Arc 的服务器代理时发生错误，可查看下表确定可能的原因和帮助解决你的问题的建议步骤。 你将需要打印到控制台的 `AZCM0000`（“0000”可以是任意 4 位数）或脚本输出才能继续操作。

| 错误代码 | 可能的原因 | 建议的补救措施 |
|------------|----------------|-----------------------|
| AZCM0000 | 操作成功 | 空值 |
| AZCM0001 | 发生未知错误 | 请联系 Microsoft 支持部门获取进一步的帮助 |
| AZCM0011 | 用户已取消操作 (CTRL+C) | 重试上一命令 |
| AZCM0012 | 提供的访问令牌无效 | 获取新的访问令牌，然后重试 |
| AZCM0013 | 提供的标记无效 | 检查标记是否用双引号引起来且用逗号分隔，并且带空格的任何名称或值是否用单引号引起来：`--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | 云无效 | 指定支持的云：`AzureCloud` 或 `AzureUSGovernment` |
| AZCM0015 | 指定的 相关 ID 不是有效的 GUID | 为 `--correlation-id` 提供有效的 GUID |
| AZCM0016 | 缺少必需参数 | 查看输出，确定是否缺少哪些参数 |
| AZCM0017 | 资源名称无效 | 指定一个仅使用字母数字字符、连字符和/或下划线的名称。 名称不能以连字符和下划线结尾。 |
| AZCM0018 | 在没有管理权限的情况下执行了命令 | 在提升的命令提示符或控制台会话中使用管理员或根特权重试命令。 |
| AZCM0041 | 提供的凭据无效 | 对于设备登录，请验证指定的用户帐户是否有权访问将创建服务器资源的租户和订阅。 对于服务主体登录，请检查客户端 ID 和机密是否正确、机密的到期日期，以及服务主体是否来自将创建服务器资源的同一租户。 |
| AZCM0042 | 已启用 Arc 的服务器资源创建失败 | 验证指定的用户/服务主体是否具有访问权限来在指定的资源组中创建已启用 Arc 的服务器资源。 |
| AZCM0043 | 已启用 Arc 的服务器资源删除失败 | 验证指定的用户/服务主体是否具有访问权限来在指定的资源组中删除已启用 Arc 的服务器资源。 如果资源在 Azure 中不复存在，请使用 `--force-local-only` 标志继续操作。 |
| AZCM0044 | 已存在同名的资源 | 请为 `--resource-name` 参数指定其他名称，或者删除 Azure 中现有已启用 Arc 的服务器，然后重试。 |
| AZCM0061 | 无法访问代理服务 | 验证你是否正在提升的用户上下文（管理员/根）中运行命令，以及你的服务器上是否正在运行 HIMDS 服务。 |
| AZCM0062 | 连接服务器时出错 | 查看输出中的其他错误代码，获取更具体的信息。 如果在创建 Azure 资源后发生错误，你需要先从资源组中删除 Arc 服务器，然后再重试。 |
| AZCM0063 | 断开服务器连接时出错 | 查看输出中的其他错误代码，获取更具体的信息。 如果仍然遇到此错误，可删除 Azure 中的资源，然后在服务器上运行 `azcmagent disconnect --force-local-only` 来断开代理连接。 |
| AZCM0064 | 代理服务未响应 | 检查 `himds` 服务的状态，确保它正在运行。 如果该服务未在运行，请将其启动。 如果正在运行，请稍等片刻，然后再重试。 |
| AZCM0065 | 出现内部代理通信错误 | 请联系 Microsoft 支持部门获取帮助 |
| AZCM0066 | 代理 Web 服务未响应或不可用 | 请联系 Microsoft 支持部门获取帮助 |
| AZCM0067 | 代理已连接到 Azure | 请先按照[断开代理连接](manage-agent.md#unregister-machine)中的步骤进行操作，然后再重试。 |
| AZCM0068 | 断开服务器与 Azure 的连接时出现内部错误 | 请联系 Microsoft 支持部门获取帮助 |
| AZCM0081 | 下载 Azure Active Directory 托管标识证书时出错 | 如果尝试将服务器连接到 Azure 时出现此消息，则表示代理无法与 Azure Arc 服务通信。 请删除 Azure 中的资源，然后重试连接。 |
| AZCM0101 | 未成功分析命令 | 请运行 `azcmagent <command> --help` 来查看正确的命令语法 |
| AZCM0102 | 无法检索计算机主机名 | 请运行 `hostname` 以检查是否有任何系统级别的错误消息，然后联系 Microsoft 支持部门。 |
| AZCM0103 | 生成 RSA 密钥时出错 | 请联系 Microsoft 支持部门获取帮助 |
| AZCM0104 | 未能读取系统信息 | 验证用于运行 `azcmagent` 的标识在系统上是否具有管理员/根特权，然后重试。 |

## <a name="agent-verbose-log"></a>代理详细日志

在遵循本文后面所述的故障排除步骤之前，所需的最少信息为详细日志。 使用 verbose (-v) 参数时，它包含 azcmagent 工具命令的输出。 对于 Windows，日志文件将写入 `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log`；对于 Linux，日志文件将写入 `/var/opt/azcmagent/log/azcmagent.log`。

### <a name="windows"></a>Windows

下面是执行交互式安装时，使用已连接的计算机代理为 Windows 启用详细日志记录的命令示例。

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

下面是使用服务主体执行大规模安装时，使用已连接的计算机代理为 Windows 启用详细日志记录的命令示例。

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

下面是执行交互式安装时，使用已连接的计算机代理为 Linux 启用详细日志记录的命令示例。

>[!NOTE]
>若要运行 azcmagent，必须具有对 Linux 计算机的根访问权限。

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

下面是使用服务主体执行大规模安装时，使用已连接的计算机代理为 Linux 启用详细日志记录的命令示例。

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>服务的代理连接问题

下表列出了一些已知错误以及如何排除和解决这些问题的建议。

|消息 |错误 |可能的原因 |解决方案 |
|--------|------|---------------|---------|
|未能获取授权令牌设备流 |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |无法访问 `login.windows.net` 终结点 | 验证与终结点的连接。 |
|未能获取授权令牌设备流 |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |代理或防火墙正在阻止对 `login.windows.net` 终结点的访问。 | 验证与终结点的连接，并确保它未被防火墙或代理服务器阻止。 |
|未能获取授权令牌设备流  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | 已启用组策略对象“计算机配置\管理模板\系统\用户配置文件\在系统重启时删除指定天数之前的用户配置文件”。 | 验证 GPO 是否已启用并以受影响的计算机为目标。 有关更多详细信息，请参阅脚注 <sup>[1](#footnote1)</sup>。 |
|未能从 SPN 获取授权令牌 |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |代理或防火墙正在阻止对 `login.windows.net` 终结点的访问。 |验证与终结点的连接，并确保它未被防火墙或代理服务器阻止。 |
|未能从 SPN 获取授权令牌 |`Invalid client secret is provided` |服务主体机密错误或无效。 |验证服务主体机密。 |
| 未能从 SPN 获取授权令牌 |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |服务主体和/或租户 ID 不正确。 |验证服务主体和/或租户 ID。|
|获取 ARM 资源响应 |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |凭据和/或权限错误 |验证你或服务主体是“Azure Connected Machine 加入”角色的成员。 |
|未能 AzcmagentConnect ARM 资源 |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure 资源提供程序未注册。 |注册[资源提供程序](./agent-overview.md#register-azure-resource-providers)。 |
|未能 AzcmagentConnect ARM 资源 |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |代理服务器或防火墙正在阻止对 `management.azure.com` 终结点的访问。 |验证与终结点的连接，并确保它未被防火墙或代理服务器阻止。 |

<a name="footnote1"></a><sup>1</sup>如果此 GPO 已启用，并且应用于具有已连接的计算机代理的计算机，则它会删除与为 himds 服务指定的内置帐户关联的用户配置文件。 因此，它还会删除用于与缓存在本地证书存储中的服务进行通信（30 天）的身份验证证书。 在 30 天的限制之前，系统会尝试续订该证书。 若要解决此问题，请按照[注销计算机](manage-agent.md#unregister-machine)的步骤操作，然后将其重新注册到正在运行 `azcmagent connect` 的服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请尝试通过以下渠道之一获取更多支持：

* 通过 [Microsoft Q&A](/answers/topics/azure-arc.html) 获取 Azure 专家的解答。

* 连接到 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 Azure 支持人员会将你连接到 Azure 社区，从中可以获得解答、支持和专家建议。

* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。
