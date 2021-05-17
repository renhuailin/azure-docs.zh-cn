---
title: 调试应用程序代理应用程序 - Azure Active Directory
description: 调试 Azure Active Directory (Azure AD) 应用程序代理应用程序的问题。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 058ff3bc61794f74d2068bd99b93df007495bedf
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108186048"
---
# <a name="debug-application-proxy-application-issues"></a>调试应用程序代理应用程序问题 

本文内容有助于排查 Azure Active Directory (Azure AD) 应用程序代理应用程序的问题。 如果正在使用应用程序代理服务远程访问本地 Web 应用程序，但连接到应用程序时遇到问题，请使用此流程图调试应用程序问题。 

## <a name="before-you-begin"></a>准备阶段

排查应用程序代理问题时，建议从连接器开始。 首先，请遵循[调试应用程序代理连接器问题](application-proxy-debug-connectors.md)中的故障排除流程，确保正确配置应用程序代理连接器。 如果仍有问题，请返回到本文，对应用程序进行故障排除。  

有关应用程序代理的详细信息，请参阅：

- [通过应用程序代理远程访问本地应用程序](application-proxy.md)
- [应用程序代理连接器](application-proxy-connectors.md)
- [安装并注册连接器](application-proxy-add-on-premises-application.md)
- [应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>应用程序问题的流程图

此流程图逐步讲解了如何调试在连接到应用程序时遇到的某些更常见问题。 有关每个步骤的详细信息，请参阅流程图后面的表。

![显示应用程序调试步骤的流程图](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

| 步骤 | 操作 | 描述 |
|---------|---------|---------|
|1 | 打开浏览器，访问应用，然后输入你的凭据 | 尝试使用你的凭据登录到应用，并检查与用户相关的任何错误，如[无法访问此公司应用](application-proxy-sign-in-bad-gateway-timeout-error.md)。 |
|2 | 验证应用的用户分配 | 确保你的用户帐户有权从公司网络内部访问该应用，然后按照[测试应用程序](application-proxy-add-on-premises-application.md#test-the-application)中的步骤来测试登录应用的操作。 如果登录问题仍然存在，请参阅[如何排查登录错误](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。  |
|3 | 打开浏览器并尝试访问应用 | 如果立即出现错误，请检查是否正确配置了应用程序代理。 有关特定错误消息的详细信息，请参阅[排查应用程序代理问题和错误消息](application-proxy-troubleshoot.md)。  |
|4 | 检查自定义域设置或排查错误 | 如果页面完全没有显示，请查看[使用自定义域](application-proxy-configure-custom-domain.md)，确保已正确配置自定义域。<br></br>如果页面未加载并显示错误消息，请参阅[排查应用程序代理问题和错误消息](application-proxy-troubleshoot.md)，以解决该错误。 <br></br>如果显示错误消息所用的时间超过 20 秒，则可能存在连接问题。 请参阅[调试应用程序代理连接器](application-proxy-debug-connectors.md)故障排除一文。  |
|5 | 如果问题仍然存在，请转到连接器调试 | 代理与连接器之间或连接器与后端之间可能存在连接问题。 请参阅[调试应用程序代理连接器](application-proxy-debug-connectors.md)故障排除一文。 |
|6 | 发布所有资源，检查浏览器开发人员工具并修复链接 | 请确保发布路径包含应用程序所需的所有映像、脚本和样式表。 有关详细信息，请参阅[将本地应用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 <br></br>使用浏览器的开发人员工具（Internet Explorer 或 Microsoft Edge 中的 F12 工具），并根据[应用程序页面未正确显示](application-proxy-page-appearance-broken-problem.md)中所述检查发布问题。 <br></br>查看[页面上的链接不起作用](application-proxy-page-links-broken-problem.md)中用于解决断开的链接的选项。 |
|7 | 检查网络延迟 | 如果页面加载速度缓慢，请在[有关降低延迟的注意事项](application-proxy-network-topology.md#considerations-for-reducing-latency)中了解如何最大程度地降低网络延迟。 | 
|8 | 请参阅更多故障排除帮助 | 如果问题仍然存在，请在[应用程序代理故障排除文档](application-proxy-troubleshoot.md)中查找更多故障排除文章。 |

## <a name="next-steps"></a>后续步骤


* [使用连接器组在单独的网络和位置上发布应用程序。](application-proxy-connector-groups.md)
* [使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)
* [对应用程序代理和连接器错误进行故障排除](application-proxy-troubleshoot.md)
* [如何以无提示方式安装 Azure AD 应用程序代理连接器](application-proxy-register-connector-powershell.md)
