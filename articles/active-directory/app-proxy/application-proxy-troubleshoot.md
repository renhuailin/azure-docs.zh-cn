---
title: Azure Active Directory 应用程序代理疑难解答
description: 介绍如何对 Azure Active Directory 应用程序代理中的错误进行故障排除。
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
ms.openlocfilehash: 798f381ef067af174370fb21893c32386390449a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617276"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>应用程序代理问题和错误消息故障排除

对应用程序代理问题进行故障排除时，我们建议你首先查看故障排除流程，[调试应用程序代理连接器问题](./application-proxy-debug-connectors.md)，以确定应用程序代理连接器的配置是否正确。 如果仍然无法连接到应用程序，请按照[调试应用程序代理应用程序问题](./application-proxy-debug-apps.md)中的故障排除流程进行操作。

如果在访问已发布应用程序或发布应用程序时出现错误，请检查以下选项，查看 Microsoft Azure AD 应用程序代理是否正确工作：

* 打开 Windows 服务控制台。 验证“Microsoft AAD 应用程序代理连接器”服务是否已启用且正在运行。 另请查看应用程序代理服务属性页，如下图所示：  
  ![Microsoft AAD 应用程序代理连接器属性窗口屏幕截图](./media/application-proxy-troubleshoot/connectorproperties.png)
* 打开事件查看器，并在 **应用程序和服务日志** > **Microsoft** > **AadApplicationProxy** > **Connector** > **Admin** 中查找应用程序代理连接器事件。
* 如果需要，可通过[打开应用程序代理连接器会话日志](application-proxy-connectors.md#under-the-hood)获取更详细的日志。

## <a name="the-page-is-not-rendered-correctly"></a>页面未正确呈现
即使没有收到特定的错误消息，应用程序也可能存在绘制或运行不正常的问题。 如果发布了文章路径，但应用程序需要存在于该路径外的内容时，可能发生此情况。

例如，如果发布路径 `https://yourapp/app`，但应用程序调用 `https://yourapp/media` 中的图像，则这些图像无法呈现。 确保使用包含所有相关内容所需的最高级路径发布应用程序。 在此示例中，它是 `http://yourapp/`。

## <a name="connector-errors"></a>连接器错误

如果注册在连接器向导安装期间失败，有两种方法可查看失败原因。 查看“应用程序和服务日志\Microsoft\AadApplicationProxy\Connector\Admin”下的事件日志，或运行以下 Windows PowerShell 命令：

```powershell
Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1
```

从事件日志找到连接器错误后，使用此常见错误表解决问题：

| 错误 | 建议的步骤 |
| ----- | ----------------- |
| 连接器注册已失败：确保已在 Azure 管理门户中启用应用程序代理，并且已正确输入 Active Directory 用户名和密码。 错误：“发生了一个或多个错误。” | 如果关闭了注册窗口但没有登录到 Azure AD，请再次运行连接器向导并注册连接器。 <br><br> 如果注册窗口打开后立即关闭，无法进行登录，则可能看到此错误。 当系统上存在网络错误时，可能出现此错误。 确保可从浏览器连接到公共网站，并且端口以[应用程序代理先决条件](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)中所指定的方式打开。 |
| 注册窗口中出现明确错误。 无法继续 | 如果看到此错误，然后窗口关闭，这意味着输入的用户名或密码错误。 重试。 |
| 连接器注册已失败：确保已在 Azure 管理门户中启用应用程序代理，并且已正确输入 Active Directory 用户名和密码。 错误：AADSTS50059: 未在请求中找到或所提供的任何凭据均未暗示任何租户识别信息，并且服务主体 URI 的搜索已失败。 | 正尝试使用 Microsoft 帐户登录，而不是使用作为正尝试访问的目录组织 ID 一部分的域进行登录。 确保管理员是租户域的相同域名的一部分，例如，如果 Azure AD 域为 contoso.com，则管理员应为 admin@contoso.com。 |
| 无法检索运行 PowerShell 脚本的当前执行策略。 | 如果连接器安装失败，请检查确保 PowerShell 执行策略未禁用。 <br><br>1. 打开组策略编辑器。<br>2. 依次转到“计算机配置” > “管理模板” > “Windows 组件” > “Windows PowerShell”，并双击“打开脚本执行”    。<br>3. 可将执行策略设置为“未配置”或“已启用” 。 如果设置为“已启用”，请确保在“选项”下将“执行策略”设置为“允许本地脚本和远程签名脚本”或“允许所有脚本”。 |
| 连接器无法下载配置。 | 用于身份验证的连接器客户端证书已过期。 如果将连接器安装在代理后，则可能发生此情况。 在此情况下，连接器无法访问 Internet，并且将无法向远程用户提供应用程序。 在 Windows PowerShell 中使用 `Register-AppProxyConnector` cmdlet 手动续订信任。 如果连接器在代理后，则必须向连接器帐户“网络服务”和“本地系统”授予 Internet 访问权限。 可通过向它们授予代理访问权限或将它们设置为绕过代理来实现此目的。 |
| 连接器注册已失败：要注册连接器，请确保是 Active Directory 的应用程序管理员。 错误：“注册请求被拒绝。” | 尝试登录时所使用的别名不是此域上的管理员。 始终为拥有用户域的目录安装连接器。 确保尝试登录时所使用的管理员帐户至少具有 Azure AD 租户的应用程序管理员权限。 |
| 由于网络问题，连接器无法连接到该服务。 连接器尝试访问以下 URL。 | 连接器无法连接到应用程序代理云服务。 如果有阻止连接的防火墙规则，则可能会发生这种情况。 确保允许访问[应用程序代理先决条件](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)中列出的正确端口和 URL。 |

## <a name="kerberos-errors"></a>Kerberos 错误

此表包含 Kerberos 设置和配置中较常见的错误以及相关解决建议。

| 错误 | 建议的步骤 |
| ----- | ----------------- |
| 无法检索运行 PowerShell 脚本的当前执行策略。 | 如果连接器安装已失败，请检查确保 PowerShell 执行策略未禁用。<br><br>1. 打开组策略编辑器。<br>2. 依次转到“计算机配置” > “管理模板” > “Windows 组件” > “Windows PowerShell”，并双击“打开脚本执行”    。<br>3. 可将执行策略设置为“未配置”或“已启用” 。 如果设置为“已启用”，请确保在“选项”下将“执行策略”设置为“允许本地脚本和远程签名脚本”或“允许所有脚本”。 |
| 12008 - Azure AD 超出了对后端服务器的 Kerberos 身份验证尝试的最大允许次数。 | 此错误可能表示 Azure AD 和后端应用程序服务器之间的配置不正确，或者两台计算机上的时间和日期配置存在问题。 后端服务器拒绝了由 Azure AD 创建的 Kerberos 票证。 验证 Azure AD 和后端应用程序服务器是否正确配置。 确保 Azure AD 和后端应用程序服务器上的时间和日期配置已同步。 |
| 13016 - Azure AD 无法代表用户检索 Kerberos 票证，因为边缘令牌或访问 cookie 中没有 UPN。 | STS 配置出现问题。 在 STS 中修复 UPN 声明配置。 |
| 13019 - Azure AD 由于以下常规 API 错误而无法代表用户检索 Kerberos 票证。 | 此事件可能表示 Azure AD 和域控制器服务器之间的配置不正确，或者两台计算机上的时间和日期配置存在问题。 域控制器拒绝了由 Azure AD 创建的 Kerberos 票证。 验证 Azure AD 和后端应用程序服务器是否正确配置，尤其是 SPN 配置。 确保 Azure AD 加入与域控制器相同的域，以确保域控制器与 Azure AD 建立信任。 确保 Azure AD 和域控制器上的时间和日期配置已同步。 |
| 13020 - Azure AD 无法代表用户检索 Kerberos 票证，因为后端服务器 SPN 未定义。 | 此事件可能表示 Azure AD 和域控制器服务器之间的配置不正确，或者两台计算机上的时间和日期配置存在问题。 域控制器拒绝了由 Azure AD 创建的 Kerberos 票证。 验证 Azure AD 和后端应用程序服务器是否正确配置，尤其是 SPN 配置。 确保 Azure AD 加入与域控制器相同的域，以确保域控制器与 Azure AD 建立信任。 确保 Azure AD 和域控制器上的时间和日期配置已同步。 |
| 13022 - Azure AD 无法对用户进行身份验证，因为后端服务器以 HTTP 401 错误响应 Kerberos 身份验证尝试。 | 此事件可能表示 Azure AD 和后端应用程序服务器之间的配置不正确，或者两台计算机上的时间和日期配置存在问题。 后端服务器拒绝了由 Azure AD 创建的 Kerberos 票证。 验证 Azure AD 和后端应用程序服务器是否正确配置。 确保 Azure AD 和后端应用程序服务器上的时间和日期配置已同步。 有关详细信息，请参阅[排查应用程序代理的 Kerberos 约束委派配置问题](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)。  |

## <a name="end-user-errors"></a>最终用户错误

此列表包括最终用户尝试访问应用失败时可能遇到的错误。 

| 错误 | 建议的步骤 |
| ----- | ----------------- |
| 网站无法显示页面。 | 如果应用程序是 IWA 应用程序，用户可能在尝试访问已发布的应用时得到此错误。 为此应用程序定义的 SPN 可能不正确。 对于 IWA 应用，确保为此应用程序配置的 SPN 正确。 |
| 网站无法显示页面。 | 如果应用程序是 OWA 应用程序，用户可能在尝试访问已发布的应用时得到此错误。 这可能由以下原因之一导致：<br><li>为此应用程序定义的 SPN 不正确。 确保针对此应用程序配置的 SPN 正确。</li><li>尝试访问应用程序的用户正在使用 Microsoft 帐户（而不是正确的公司帐户）登录，或者该用户是来宾用户。 确保用户使用与已发布应用程序的域匹配的公司帐户登录。 Microsoft 帐户用户和来宾无法访问 IWA 应用程序。</li><li>尝试访问应用程序的用户未针对本地端的此应用程序正确定义。 确保此用户在本地计算机上具有针对此后端应用程序定义的正确权限。 |
| 无法访问此公司应用。 无权访问此应用程序。 授权失败。 确保向用户分配此应用程序的访问权限。 | 如果用户使用 Microsoft 帐户而不是公司帐户登录，则在尝试访问已发布的应用时可能会收到此错误。 来宾用户也可能得到此错误。 Microsoft 帐户用户和来宾无法访问 IWA 应用程序。 确保用户使用与已发布应用程序的域匹配的公司帐户登录。<br><br>可能未针对此应用程序分配用户。 转到“应用程序”选项卡，然后在“用户和组”下将此用户或用户组分配到此应用程序。 |
| 当前无法访问此公司应用。 请稍后重试…连接器已超时。 | 如果用户未在本地端针对此应用程序正确定义，则在尝试访问已发布的应用时可能会收到此错误。 确保用户在本地计算机上具有针对此后端应用程序定义的正确权限。 |
| 无法访问此公司应用。 无权访问此应用程序。 授权失败。 确保用户具有 Azure Active Directory Premium 许可证。 | 如果订阅方管理员未向用户显式分配 Premium 许可证，则用户在尝试访问你发布的应用时可能会收到此错误。 转到订阅方的 Active Directory“许可证”选项卡并确保向此用户或用户组分配 Premium 许可证。 |
| 找不到具有指定主机名的服务器。 | 如果应用程序的自定义域未正确配置，则用户在尝试访问已发布的应用时可能会收到此错误。 请确保已上传域的证书，并按照[使用 Azure AD 应用程序代理中的自定义域](./application-proxy-configure-custom-domain.md)中的步骤正确配置 DNS 记录 |
|禁止：无法访问该企业应用或无法对用户授权。 请确保用户已在本地 AD 中定义，并且有权访问本地 AD 中的应用。 | 这可能是访问授权信息的问题，请参阅[某些应用程序和 API 需要访问帐户对象上的授权信息]( https://support.microsoft.com/help/331951/some-applications-and-apis-require-access-to-authorization-information)。 简单来说，要解决此问题，应将应用代理连接器计算机帐户添加到“Windows 授权访问组”内置域组。 |

## <a name="see-also"></a>另请参阅
* [启用 Azure Active Directory 的应用程序代理](application-proxy-add-on-premises-application.md)
* [使用应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)
* [启用单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
* [启用条件访问](./application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png