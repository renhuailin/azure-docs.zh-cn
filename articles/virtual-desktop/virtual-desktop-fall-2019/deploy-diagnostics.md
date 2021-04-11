---
title: 部署适用于 Windows 虚拟桌面（经典）的诊断工具 - Azure
description: 如何部署适用于 Windows 虚拟桌面（经典）的诊断 UX 工具。
author: Heidilohr
ms.topic: how-to
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bdba36e2ff4721bd024369788c7dc04f066e9a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565574"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>部署 Windows 虚拟桌面（经典）诊断工具

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。

Windows 虚拟桌面诊断工具可为你实现以下目的：

- 查找单个用户在一周内的诊断活动（管理、连接或源）。
- 从 Log Analytics 工作区收集连接活动的会话主机信息。
- 查看特定主机中的虚拟机 (VM) 性能详细信息。
- 查看哪些用户登录到了会话主机。
- 向特定会话主机上的活动用户发送消息。
- 将用户登出会话主机。

## <a name="prerequisites"></a>先决条件

你需要创建 Azure Active Directory 应用注册和 Log Analytics 工作区，然后才能为该工具部署 Azure 资源管理器模板。 你或管理员需要以下权限才能执行此操作：

- Azure 订阅的所有者
- 在 Azure 订阅中创建资源的权限
- 创建 Azure AD 应用的权限
- RDS 所有者或参与者权限

在开始之前，还需要安装以下两个 PowerShell 模块：

- [Azure PowerShell 模块](/powershell/azure/install-az-ps)
- [Azure AD 模块](/powershell/azure/active-directory/install-adv2)

请确保在登录时已准备好订阅 ID。

完成所有操作后，可以创建 Azure AD 应用注册。

## <a name="create-an-azure-active-directory-app-registration"></a>创建 Azure Active Directory 应用注册

本部分将说明如何使用 PowerShell 创建包含服务主体的 Azure Active Directory 应用并获取其 API 权限。

>[!NOTE]
>API 权限是指添加到 Azure Active Directory 应用程序的 Windows 虚拟桌面、Log Analytics 和 Microsoft Graph API 权限。

1. 以管理员身份打开 PowerShell。
2. 使用对要用于诊断工具的 Azure 订阅拥有“所有者”或“参与者”权限的帐户登录到 Azure：
   ```powershell
   Login-AzAccount
   ```
3. 用同一帐户登录到 Azure AD：
   ```powershell
   Connect-AzureAD
   ```
4. 转到 [RDS-Templates GitHub 存储库](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)，并在 PowerShell 中运行 CreateADAppRegistrationforDiagnostics.ps1 脚本。
5.  当脚本要求你为应用命名时，请输入唯一的应用名称。


脚本成功运行后，它应在输出中显示以下内容：

-  一条消息，确认你的应用现在有一个服务主体角色分配。
-  部署诊断工具时需要的客户端 ID 和客户端密钥。

完成应用注册后，接下来即可配置 Log Analytics 工作区。

## <a name="configure-your-log-analytics-workspace"></a>配置 Log Analytics 工作区

建议使用以下性能计数器配置 Log Analytics 工作区，以便在远程会话中获得最佳用户体验。 有关具有建议的阈值的建议计数器列表，请参阅 [Windows 性能计数器阈值](deploy-diagnostics.md#windows-performance-counter-thresholds)。

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>使用 PowerShell 创建 Azure Log Analytics 工作区

可以运行 PowerShell 脚本来创建 Log Analytics 工作区，并配置建议的 Windows 性能计数器，以监视用户体验和应用性能。

>[!NOTE]
>如果创建现有的 Log Analytics 工作区时未使用所需的 PowerShell 脚本，请跳到[在 Azure 门户中验证脚本结果](#validate-the-script-results-in-the-azure-portal)。

运行 PowerShell 脚本：

1.  以管理员身份打开 PowerShell。
2.  请参阅 [RDS-Templates GitHub 存储库](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)，并在 PowerShell 中运行 CreateLogAnalyticsWorkspaceforDiagnostics.ps1 脚本。
3. 输入以下参数值：

    - 对于 ResourceGroupName，请输入资源组的名称。
    - 对于 LogAnalyticsWorkspaceName，请为 Log Analytics 工作区输入唯一的名称。
    - 对于“位置”，请输入要使用的 Azure 区域。
    - 输入“Azure 订阅 ID”，你可以在“订阅”下的 Azure 门户中找到该 ID 。

4. 输入具有委托的管理员访问权限的用户的凭据。
5. 使用同一名用户的凭据登录到 Azure 门户。
6. 记下或记住 LogAnalyticsWorkspace ID 以供稍后使用。
7. 如果使用 PowerShell 脚本设置了 Log Analytics 工作区，则应已配置好性能计数器，你可以直接跳到[在 Azure 门户中验证脚本结果](#validate-the-script-results-in-the-azure-portal)。 否则，请继续下一部分。

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>在现有 Log Analytics 工作区中配置 Windows 性能计数器

本部分内容对想要使用上一部分中未使用 PowerShell 脚本创建的现有 Azure Log Analytics 工作区的用户适用。 如果尚未使用该脚本，则必须手动配置建议的 Windows 性能计数器。

下面介绍如何手动配置建议的性能计数器：

1. 打开 Internet 浏览器，然后使用管理帐户登录到 [Azure 门户](https://portal.azure.com/)。
2. 接下来，转到 Log Analytics 工作区，查看已配置的 Windows 性能计数器。
3. 在“设置”部分，选择“高级设置” 。
4. 然后，导航到“数据” > “Windows 性能计数器”，并添加以下计数器 ：

    -   LogicalDisk(\*)\\%Free Space
    -   LogicalDisk(C:)\\Avg.磁盘队列长度
    -   Memory(\*)\\Available Mbytes
    -   Processor Information(\*)\\Processor Time
    -   User Input Delay per Session(\*)\\Max Input Delay

有关性能计数器的详细信息，请参阅 [Azure Monitor 中的 Windows 和 Linux 性能数据源](../../azure-monitor/agents/data-sources-performance-counters.md)。

>[!NOTE]
>你配置的任何其他计数器都不会显示在诊断工具本身中。 若要使它显示在诊断工具中，需要配置该工具的配置文件。 有关如何使用高级管理完成此操作的说明，请日后查看 GitHub。

## <a name="validate-the-script-results-in-the-azure-portal"></a>在 Azure 门户中验证脚本结果

在继续部署诊断工具之前，建议验证 Azure Active Directory 应用程序是否具有 API 权限，以及 Log Analytics 工作区是否具有预先配置的 Windows 性能计数器。

### <a name="review-your-app-registration"></a>查看应用注册

确保应用注册具有 API 权限：

1. 打开浏览器，然后使用管理帐户连接到 [Azure 门户](https://portal.azure.com/)。
2. 转到 Azure Active Directory。
3. 转到“应用注册”，并选择“所有应用程序” 。
4. 使用在[创建 Azure Active Directory 应用注册](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)的步骤 5 中输入的相同应用名称查找 Azure AD 应用注册。

### <a name="review-your-log-analytics-workspace"></a>查看 Log Analytics 工作区

确保 Log Analytics 工作区具有预先配置的 Windows 性能计数器：

1. 在 [Azure 门户](https://portal.azure.com/)中转到“Log Analytics 工作区”，以查看配置的 Windows 性能计数器。
2. 在“设置”下，选择“高级设置” 。
3. 然后，转到“数据” > “Windows 性能计数器” 。
4. 确保已预先配置以下计数器：

   - LogicalDisk(\*)\\%Free Space：显示磁盘上总可用空间的可用空间量（以百分比表示）。
   - LogicalDisk(C:)\\Avg.Disk Queue Length：C 盘的磁盘转移请求的长度。 该值超过 2 的时间应是一小会儿。
   - Memory(\*)\\Available Mbytes：系统的可用内存（以兆字节为单位）。
   - Processor Information(\*)\\Processor Time：处理器执行非空闲线程所用的运行时间的百分比。
   - User Input Delay per Session(\*)\\Max Input Delay

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>连接到 Log Analytics 工作区中的 VM

若要查看 VM 的运行状况，需要启用 Log Analytics 连接。 请按照以下步骤连接 VM：

1. 打开浏览器，然后使用管理帐户登录到 [Azure 门户](https://portal.azure.com/)。
2. 转到 Log Analytics 工作区。
3. 在左侧面板上的“工作区数据源”下，选择“虚拟机”。
4. 选择要连接到的 VM 的名称。
5. 选择“连接”  。

## <a name="deploy-the-diagnostics-tool"></a>部署诊断工具

为诊断工具部署 Azure 资源管理模板：

1.  转到 [GitHub Azure RDS 模板页](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy)。
2.  将模板部署到 Azure 并按照模板中的说明进行操作。 请确保你可以使用以下信息：

    -   Client-Id
    -   Client-Secret
    -   Log Analytics 工作区 ID

3.  提供输入参数后，接受“条款和条件”，并选择“购买”。

部署完成将需要 2 至 3 分钟的时间。 成功部署后，请前往资源组，并确保其中包含 Web 应用和应用服务计划资源。

之后，需要设置重定向 URI。

### <a name="set-the-redirect-uri"></a>设置重定向 URI

设置重定向 URI：

1.  在 [Azure 门户](https://portal.azure.com/)中，转到“应用服务”并找到所创建的应用程序。
2.  在“概述”页上，复制在此处找到的 URL。
3.  导航到“应用注册”，并选择要部署的应用。
4.  在左侧面板的“管理”部分下，选择“身份验证”。
5.  在“重定向 URI”文本框中输入所需的重定向 URI，并选择菜单左上角的“保存” 。
6. 在“类型”下的下拉菜单中选择“Web”。
7. 输入“应用概述”页中的 URL，并将“/security/signin-callback”添加到该 URL 的末尾。 例如：`https://<yourappname>.azurewebsites.net/security/signin-callback`。

   > [!div class="mx-imgBorder"]
   > ![重定向 URI 页面](../media/redirect-uri-page.png)

8. 现在，请转到你的 Azure 资源，选择具有模板中提供的名称的 Azure 应用服务资源，然后导航到与它关联的 URL。 （例如，如果你在模板中使用的应用名称为 `contosoapp45`，则关联的 URL 为 <http://contoso.azurewebsites.net>）。
9. 使用相应的 Azure Active Directory 用户帐户登录。
10.   选择“接受”。

## <a name="distribute-the-diagnostics-tool"></a>分发诊断工具

在使诊断工具可供用户使用之前，请确保他们具有以下权限：

- 用户需要具有 Log Analytics 的读取访问权限。 有关详细信息，请参阅 [Azure Monitor 的角色、权限和安全入门](../../azure-monitor/roles-permissions-security.md)。
-  用户还需要具有对 Windows 虚拟桌面租户的读取访问权限（RDS 读取者角色）。 有关详细信息，请参阅 [Windows 虚拟桌面中的委托访问](delegated-access-virtual-desktop-2019.md)。

还需要为用户指定以下信息：

- 应用的 URL
- 他们可以访问的租户组中的单个租户的名称。

## <a name="use-the-diagnostics-tool"></a>使用诊断工具

使用从组织中收到的信息登录到帐户后，为要查询其活动的用户准备好 UPN。 搜索将在指定活动类型下显示上一周内发生的所有活动。

### <a name="how-to-read-activity-search-results"></a>如何读取活动搜索结果

活动按时间戳排序，最新活动排在最前面。 如果结果返回错误，请首先检查是否是服务错误。 对于服务错误，请创建支持工单并包含活动信息，以帮助我们调试问题。 所有其他错误类型通常由用户或管理员来解决。 有关最常见的错误情况及其解决方法的列表，请参阅[识别和诊断问题](diagnostics-role-service-2019.md#common-error-scenarios)。

>[!NOTE]
>服务错误在链接文档中称为“外部错误”。 更新 PowerShell 参考时，我们将对此内容进行更改。

连接活动可能包含多个错误。 你可以展开活动类型，查看用户遇到的任何其他错误。 选择错误代码的名称可打开一个对话框，查看其详细信息。

### <a name="investigate-the-session-host"></a>调查会话主机

在搜索结果中，找到并选择你要获取其相关信息的会话主机。

可以分析会话主机运行状况：

- 根据预定义的阈值，可以检索 Log Analytics 查询的会话主机运行状况信息。
- 如果没有任何活动或会话主机未连接到 Log Analytics，则信息将不可用。

你还可在会话主机上与用户进行交互：

- 你可以注销已登录的用户或向他们发送消息。
- 默认情况下，将选择最初搜索的用户，但也可以选择其他用户来向其发送消息或同时注销多个用户。

### <a name="windows-performance-counter-thresholds"></a>Windows 性能计数器阈值

- LogicalDisk(\*)\\%Free Space：

    - 显示逻辑磁盘上可用的总可用空间的百分比。
    - 阈值：小于 20% 会被标记为不正常。

- LogicalDisk(C:)\\Avg.Disk Queue Length：

    - 表示存储系统条件。
    - 阈值：大于 5 会被标记为不正常。

- Memory(\*)\\Available Mbytes：

    - 系统的可用内存。
    - 阈值：小于 500 兆字节会被标记为不正常。

- Processor Information(\*)\\Processor Time：

    - 阈值：大于 80% 会被标记为不正常。

- [User Input Delay per Session(\*)\\Max Input Delay](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)：

    - 阈值：大于 2000 ms 会被标记为不正常。

## <a name="next-steps"></a>后续步骤

- 如需了解如何监视活动日志，请参阅[将诊断与 Log Analytics 配合使用](diagnostics-log-analytics-2019.md)。
- 如需了解常见错误情况及其修复方法，请参阅[识别和诊断问题](diagnostics-role-service-2019.md)。