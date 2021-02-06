---
title: 本地 Azure AD 密码保护常见问题解答
description: 查看 Azure AD 本地 Active Directory 域服务环境中的密码保护的常见问题
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625121"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD 本地密码保护常见问题

本部分提供有关 Azure AD 密码保护的许多常见问题的解答。

## <a name="general-questions"></a>一般问题

**问：用户如何选择安全密码应提供哪些指导？**

在以下链接中可以找到 Microsoft 当前为此主题提供的指导：

[Microsoft 密码指导](https://www.microsoft.com/research/publication/password-guidance)

**问：在非公有云中是否支持本地 Azure AD 密码保护？**

公有云和 Arlington 云中支持本地 Azure AD 密码保护。 尚未为其他云中的可用性发布日期。

Azure AD 门户允许修改本地特定的 "Windows Server Active Directory 的密码保护" 配置，即使在不支持的云中也是如此。此类更改将持久保存，但不会生效。 不支持在不受支持的云中注册本地代理或林，任何此类注册尝试都将始终失败。

**问：我如何向本地用户的子集应用 Azure AD 密码保护权益？**

不支持。 部署并启用后，Azure AD 密码保护不会区分对待 - 所有用户都会获得均等的安全权益。

**问：密码更改和密码设置 (或重置) 之间的区别是什么？**

密码更改是指用户在证明其了解旧密码后选择新密码。 例如，密码更改会在用户登录到 Windows 时执行，然后系统会提示选择一个新密码。

如果管理员使用新密码将帐户替换为密码（例如，使用 Active Directory 用户和计算机管理工具），则密码设置 (有时称为密码重置) 。 此操作需要 (通常是域管理员) 的高级权限，并且执行该操作的人员通常不知道旧密码。 技术支持方案通常会执行密码集，例如，在协助用户忘记密码时。 首次使用密码创建新用户帐户时，还会看到密码设置事件。

不管密码是否已更改或已设置，密码验证策略的行为都是相同的。 Azure AD 密码保护 DC 代理服务会记录不同的事件，以通知你是否已完成密码更改或设置操作。  请参阅 [Azure AD 密码保护监视和日志记录](./howto-password-ban-bad-on-premises-monitor.md)。

**问：在安装后 Azure AD 密码保护是否验证现有密码？**

无 Azure AD 密码保护只能在密码更改或设置操作期间对明文密码强制执行密码策略。 Active Directory 接受密码后，只保留该密码的身份验证协议特定的哈希。 明文密码永远不会被保留，因此 Azure AD 密码保护无法验证现有密码。

初始部署 Azure AD 密码保护之后，所有用户和帐户最终将开始使用 Azure AD 密码保护验证密码，因为其现有密码会在一段时间后过期。 如果需要，此过程可以通过一次性手动过期的用户帐户密码来加速。

除非手动过期，否则配置为 "密码永不过期" 的帐户永远不会被迫更改其密码。

**问：为什么在尝试使用 "Active Directory 用户和计算机管理" 管理单元尝试设置弱密码时记录重复的密码拒绝事件？**

"Active Directory 用户和计算机管理" 管理单元将首先尝试使用 Kerberos 协议设置新密码。 失败时，管理单元将再次尝试使用旧版 (SAM RPC) 协议设置密码 (所使用的特定协议并不重要) 。 如果 Azure AD 密码保护将新密码视为弱密码，则此管理单元行为将导致记录两组密码重置拒绝事件。

**问：为什么使用空用户名记录 Azure AD 密码保护密码验证事件？**

Active Directory 支持测试密码以查看是否通过了域的当前密码复杂性要求（例如，使用 [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api）的功能。 以这种方式验证密码时，测试还包括通过密码筛选器基于 dll 的产品（如 Azure AD 密码保护）进行的验证-但传递到给定密码筛选器 dll 的用户名将为空。 在此方案中，Azure AD 密码保护仍将使用当前生效的密码策略来验证密码，并将发出事件日志消息来捕获结果，但是事件日志消息将包含空的用户名字段。

**问：是否支持与其他基于密码筛选器的产品并行安装 Azure AD 密码保护？**

是的。 支持多个已注册的密码筛选器 dll 是一项 Windows 核心功能，并不特定于 Azure AD 密码保护。 在接受密码之前，所有已注册的密码筛选器 dll 必须同意。

**问：如何在 Active Directory 环境中部署和配置 Azure AD 密码保护，而无需使用 Azure？**

不支持。 Azure AD 密码保护是旨在扩展到本地 Active Directory 环境中的一项 Azure 功能。

**问：如何在 Active Directory 级别修改策略的内容？**

不支持。 仅可使用 Azure AD 门户管理策略。 另请参阅前面的问题。

**问：为何需要使用 DFSR 进行 sysvol 复制？**

FRS（DFSR 以前的技术）存在很多已知问题，在较新版本的 Windows Server Active Directory 中完全不受支持。 Azure AD 密码保护的零测试将在配置了 FRS 的域上完成。

有关详细信息，请参阅以下文章：

[将 sysvol 复制迁移到 DFSR 的用例](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[FRS 即将终结](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

如果你的域尚未使用 DFSR，则必须先将其迁移到使用 DFSR，然后才能安装 Azure AD 密码保护。 有关详细信息，请参阅以下链接：

[SYSVOL 复制迁移指南： FRS 到 DFS 复制](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD 密码保护 DC 代理软件当前将安装在仍在使用 FRS 进行 sysvol 复制的域中的域控制器上，但该软件在此环境中将无法正常运行。 其他负面影响包括未能复制的单个文件，sysvol 还原过程会成功，但不会以静默方式复制所有文件。 应该尽快迁移你的域以使用 DFSR，这两种方法都适用于 DFSR 固有的优势，还会取消阻止 Azure AD 密码保护的部署。 在仍使用 FRS 的域中运行时，软件的未来版本将自动禁用。

**问：在域 sysvol 共享上，该功能需要多少磁盘空间？**

准确的空间用量根据多种因素而异，例如，Microsoft 全局受禁密码列表和租户自定义密码列表中的受禁令牌数目和长度，以及加密开销。 这些列表的内容将来可能会不断扩充。 考虑到这一点，合理的预期是该功能至少需要占用域 sysvol 共享中的 5 MB 空间。

**问：安装或升级 DC 代理软件后为何需要重新启动？**

此项要求是核心 Windows 行为造成的。

**问：是否有任何方法可将 DC 代理配置为使用特定的代理服务器？**

不能。 由于代理服务器是无状态的，具体使用哪种代理服务器并不重要。

**问：是否可以与其他服务（如 Azure AD Connect）并行部署 Azure AD 密码保护代理服务？**

是的。 Azure AD 密码保护代理服务和 Azure AD Connect 永远不会直接相互冲突。

遗憾的是，在 Azure AD 密码保护代理软件安装的 Microsoft Azure AD 连接代理更新程序服务的版本与 [Azure Active Directory 应用程序代理](../manage-apps/application-proxy.md) 软件安装的服务版本之间发现不兼容性。 这种不兼容性可能会导致代理更新服务无法联系 Azure 获取软件更新。 不建议在同一台计算机上安装 Azure AD 密码保护代理和 Azure Active Directory 应用程序代理。

**问： DC 代理和代理的安装和注册顺序是什么？**

支持对代理安装、DC 代理安装、林注册和代理注册进行排序。

**问：我是否应该关心域控制器上的性能点击是否超过了部署此功能的情况？**

在现有的正常 Active Directory 部署中，Azure AD 密码保护 DC 代理服务应该不会显著影响域控制器的性能。

对于大部分 Active Directory 部署而言，密码更改操作在任意给定的域控制器上只占总体工作负荷的一小部分。 例如，假设某个 Active Directory 域包含 10000 个用户帐户，某个 MaxPasswordAge 策略设置为 30 天。 一般情况下，此域每天会发生 10000/30 = 大约 333 次密码更改操作，即使是在单个域控制器中，此操作次数也是微不足道的。 考虑一种更糟糕的潜在情况：假设在单个 DC 上执行的大约 333 次密码更改是在一小时内完成的。 例如，当许多员工在星期一上午上班时，就可能会发生这种此情况。 即使出现这种情况，每 60 分钟的密码更改次数也只有大约 333 次，即每分钟 6 次，同样，这并不是一个很高的负载。

但是，如果当前域控制器已在性能限制级别运行（例如，达到了 CPU、磁盘空间、磁盘 I/O 等的上限），则我们建议添加更多的域控制器或扩展可用磁盘空间，然后再部署此功能。 另请参阅前面有关 sysvol 磁盘空间用量的问题。

**问：我只想在域中的几个 Dc 上测试 Azure AD 密码保护。是否可以强制更改用户密码以使用这些特定 Dc？**

不能。 当用户更改其密码时，Windows 客户端 OS 会控制要使用的域控制器。 基于诸如 Active Directory 站点和子网分配、特定于环境的网络配置等因素来选择域控制器。Azure AD 密码保护不会控制这些因素，并且不会影响选择哪个域控制器来更改用户的密码。

在一定程度上实现此目标的方法之一是在给定 Active Directory 站点中的所有域控制器上部署 Azure AD 密码保护。 这种方法能够合理覆盖分配到该站点的 Windows 客户端，因此，也会合理覆盖登录到这些客户端并更改其密码的用户。

**问：如果我只在主域控制器上安装 Azure AD 密码保护 DC 代理服务 (PDC) ，则该域中的所有其他域控制器是否也受到保护？**

不能。 如果在给定的非 PDC 域控制器上更改用户密码时，则明文密码永远不会发送到 PDC（这种想法是常见的错误认知）。 一旦在给定的 DC 上接受新密码，该 DC 将使用该密码来为该密码创建各种特定于身份验证协议的哈希，然后在目录中保存这些哈希。 不会保存明文密码。 然后，更新的哈希将复制到 PDC。 在某些情况下，用户密码可以直接在 PDC 上更改，同样，这取决于网络拓扑和 Active Directory 站点设计等多种因素。 （请参阅前面的问题。）

总而言之，在 PDC 上部署 Azure AD 密码保护 DC 代理服务需要对整个跨中的功能实现 100% 的安全覆盖。 仅在 PDC 上部署该功能不能为域中的其他任何 DC 提供 Azure AD 密码保护安全优势。

**问：为什么在本地 Active Directory 环境中安装代理后，自定义智能锁定仍不起作用？**

仅 Azure AD 支持自定义智能锁定。 即使安装了代理，对 Azure AD 门户中的自定义智能锁定设置的更改也不会影响本地 Active Directory 环境。

**问： System Center Operations Manager 管理包是否可用于 Azure AD 密码保护？**

不能。

**问：为什么在将策略配置为处于审核模式时，为什么 Azure AD 仍拒绝弱密码？**

仅本地 Active Directory 环境支持审核模式。 当计算密码时，Azure AD 隐式始终处于 "强制" 模式。

**问：我的用户在 Azure AD 密码保护拒绝密码时，会看到传统的 Windows 错误消息。是否可以自定义此错误消息，以便用户知道实际发生的情况？**

不能。 当域控制器拒绝密码时，用户所见到的错误消息由客户端计算机控制，而不是由域控制器控制。 不管密码是否被默认 Active Directory 密码策略或基于密码筛选器的解决方案（如 Azure AD 密码保护）拒绝，都会发生此行为。

## <a name="password-testing-procedures"></a>密码测试过程

您可能想要对各种密码执行一些基本测试，以便验证软件的正确操作并更好地了解 [密码评估算法](concept-password-ban-bad.md#how-are-passwords-evaluated)。 本部分概述了用于生成可重复结果的测试的方法。

为什么需要执行此类步骤？ 有几个因素会使本地 Active Directory 环境中对密码进行受控、可重复的测试：

* 密码策略在 Azure 中进行配置和保留，并且策略的副本会定期通过使用轮询机制)  (的本地 DC 代理进行同步。 此轮询周期固有的延迟可能会导致混淆。 例如，如果在 Azure 中配置策略，但忘记将其同步到 DC 代理，则测试可能不会产生预期的结果。 轮询间隔当前硬编码为每小时一次，但在策略更改之间等待一小时对于交互式测试方案来说是不理想的。
* 将新的密码策略同步到域控制器后，会在复制到其他域控制器时出现更多延迟。 如果对尚未接收到最新版本策略的域控制器测试密码更改，则这些延迟可能会导致意外结果。
* 通过用户界面测试密码更改会使你难以相信你的结果。 例如，很容易错误地在用户界面中键入无效密码，尤其是因为大多数密码用户界面都隐藏用户输入 (例如，例如 Windows Ctrl-Alt-Delete-> 更改密码 UI) 。
* 在测试已加入域的客户端中的密码更改时，不能严格控制使用哪个域控制器。 Windows 客户端操作系统根据各种因素（如 Active Directory 站点和子网分配、特定于环境的网络配置等）选择域控制器。

若要避免这些问题，请在登录域控制器时，基于对密码重置的命令行测试执行以下步骤。

> [!WARNING]
> 这些过程只应在测试环境中使用，因为在 DC 代理服务停止时，将接受所有传入密码更改和重置，而无需验证，同时还会避免在登录域控制器时出现固有的风险。

以下步骤假定你已在至少一个域控制器上安装了 DC 代理，至少安装了一个代理，并同时注册了代理和林。

1. 使用域管理员凭据登录到域控制器， (或其他凭据，这些凭据具有安装了 DC 代理软件并重新启动的、具有创建测试用户帐户和重置密码) 的权限。
1. 打开事件查看器，导航到 [DC 代理管理事件日志](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log)。
1. 打开提升的命令提示符窗口。
1. 为执行密码测试创建测试帐户

   有多种方法可以创建用户帐户，但此处提供了一个命令行选项，可让你在重复的测试周期中轻松地执行此操作：

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   出于下面讨论的目的，假设我们创建了一个名为 "ContosoUser" 的测试帐户，例如：

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. 打开 web 浏览器 (你可能需要使用单独的设备而不是域控制器) ，登录 [Azure 门户](https://portal.azure.com)，并浏览到 Azure Active Directory > > > Security Authentication 密码保护方法。
1. 根据需要为要执行的测试修改 Azure AD 密码保护策略。  例如，你可以决定配置强制或审核模式，或者可以决定在自定义禁止密码列表中修改禁止项的列表。
1. 通过停止并重新启动 DC 代理服务来同步新策略。

   可以通过多种方式来完成此步骤。 一种方法是使用 "服务管理" 管理控制台，方法是右键单击 Azure AD 密码保护 DC 代理服务，然后选择 "重新启动"。 另一种方法可以从命令提示符窗口执行，如下所示：

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. 检查事件查看器以验证是否已下载新策略。

   每次停止并启动 DC 代理服务时，应会看到连续关闭的 2 30006 事件。 第一个30006事件将反映在 sysvol 共享的磁盘上缓存的策略。 如果) 存在，则第二个30006事件 (是否有更新的租户策略日期，如果是，则将反映从 Azure 下载的策略。 租户策略日期值当前被编码为显示从 Azure 下载策略的大致时间戳。
   
   如果第二个30006事件未出现，则应在继续之前解决该问题。
   
   30006事件与以下示例类似：
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   例如，在 "强制" 和 "审核" 模式之间进行更改时，将会修改 AuditOnly 标志， (上述策略（AuditOnly = 0）处于强制模式) ;对自定义禁止密码列表所做的更改不会直接反映在 (以上的30006事件中，出于安全原因，这些更改不会记录到任何位置) 。 此类更改后，成功从 Azure 下载策略还将包括已修改的自定义禁止密码列表。

1. 尝试在测试用户帐户上重置新密码来运行测试。

   可在命令提示符窗口中执行此步骤，如下所示：

   ```text
   net.exe user ContosoUser <password>
   ```

   运行该命令后，可以通过在事件查看器中查看来获取有关该命令的结果的详细信息。 [DC Agent 管理事件日志](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log)主题中介绍了密码验证结果事件;你将使用此类事件来验证测试结果以及 net.exe 命令的交互式输出。

   我们来试一试示例：尝试设置 Microsoft 全局列表禁止的密码 (请注意，该列表 [未记录](concept-password-ban-bad.md#global-banned-password-list) ，但我们可以根据已知的禁止字词) 进行测试。 此示例假设你已将策略配置为处于强制模式下，并已将零个字词添加到自定义禁止密码列表。

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   按照文档，由于我们的测试是密码重置操作，你应该看到 ContosoUser 用户的10017和30005事件。

   10017事件应类似于以下示例：

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   30005事件应类似于以下示例：

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   这很有趣，接下来请尝试其他示例！ 这次，当策略处于审核模式时，我们将尝试设置自定义禁止列表所禁止的密码。 此示例假设你已完成以下步骤：将策略配置为处于审核模式，将 "lachrymose" 一词添加到 "自定义禁止密码" 列表，并按如上所述循环使用 DC 代理服务将生成的新策略同步到域控制器。

   好的，设置 "禁止密码" 的变体：

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   请记住，这一次成功是因为策略处于审核模式。 你应看到 ContosoUser 用户的10025和30007事件。

   10025事件应类似于以下示例：
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   30007事件应类似于以下示例：

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. 继续测试所选的各种密码，并使用前面步骤中所述的过程检查事件查看器中的结果。 如果需要更改 Azure 门户中的策略，请不要忘记将新策略同步到 DC 代理，如前文所述。

我们介绍了一些过程，使你能够对 Azure AD 密码保护的密码验证行为进行受控测试。 直接在域控制器上从命令行重置用户密码可能是执行此类测试的一种奇怪方法，但如前所述，它旨在产生可重复的结果。 在测试各种密码时，请记住 [密码评估算法](concept-password-ban-bad.md#how-are-passwords-evaluated) ，因为它可能有助于解释你不需要的结果。

> [!WARNING]
> 完成所有测试后，请不要忘记删除为测试目的创建的任何用户帐户！

## <a name="additional-content"></a>更多内容

以下链接不是核心 Azure AD 密码保护文档的一部分，但可能有关该功能的其他信息的有用来源。

[Azure AD 密码保护现已正式发布！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[电子邮件仿冒 Protection 指南–第15部分：在本地实现 Microsoft Azure AD 密码保护服务 (！ ) ](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)（Azure AD 密码保护和智能锁定现已推出公共预览版！）

## <a name="microsoft-premierunified-support-training-available"></a>可用的 Microsoft Premier\Unified 支持培训

如果你想要详细了解 Azure AD 密码保护并将其部署在自己的环境中，可以利用面向已签署 Premier 或 Unified 合同的客户的 Microsoft 前瞻服务。 此服务称为 Azure Active Directory：密码保护。 请联系技术客户经理获取详细信息。

## <a name="next-steps"></a>后续步骤

如果本文未解答你遇到的本地 Azure AD 密码保护问题，请在下方提交反馈 - 谢谢！

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)