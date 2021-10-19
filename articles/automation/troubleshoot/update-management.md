---
title: 排查 Azure 自动化更新管理问题
description: 本文介绍如何排查和解决 Azure 自动化更新管理的问题。
services: automation
ms.subservice: update-management
ms.date: 06/10/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bf9804b0881e02b1a4f58e5923c33840d06e37e2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706476"
---
# <a name="troubleshoot-update-management-issues"></a>排查“更新管理”问题

本文讨论在计算机上使用更新管理功能来访问和管理更新时可能遇到的问题。 对于混合 Runbook 辅助角色代理，可使用代理故障排除程序来帮助确定底层问题。 若要了解有关故障排除程序的详细信息，请参阅[排查 Windows 更新代理问题](update-agent-issues.md)和[排查 Linux 更新代理问题](update-agent-issues-linux.md)。 有关其他功能部署问题，请参阅[排查功能部署问题](onboarding.md)。

>[!NOTE]
>如果在 Windows 计算机上部署更新管理功能时遇到问题，请打开 Windows 事件查看器，查看本地计算机上“应用程序和服务日志”下的 Operations Manager 事件日志 。 查找事件 ID 为 4502 的事件和包含 `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` 的事件详细信息。

## <a name="scenario-windows-defender-update-always-show-as-missing"></a><a name="windows-defender-update-missing-status"></a>场景：Windows Defender 更新始终显示为缺失

### <a name="issue"></a>问题

Windows Defender 的定义更新 (KB2267602) 自安装后在评估中始终显示为缺失，在从 Windows 更新历史记录进行验证后会显示为最新状态。

### <a name="cause"></a>原因

定义更新在一天中发布多次。 因此，可看到 KB2267602 在一天内发布了多个版本，但它们具有不同的更新 ID 和版本。

更新管理评估每 11 小时运行一次。 在此示例中，上午 10:00 运行了某个评估，此时有 1.237.316.0 版本可用。 在 Log Analytics 工作区中搜索 Update 表时，定义更新 1.237.316.0 的“UpdateState”显示为“需要”。   如果计划的部署在几个小时后运行，假设下午 1:00 时版本 1.237.316.0 仍然可用或有可用的新版本，则会安装新版本，并反映在写入 UpdateRunProgress 表的记录中。 但在 Update 表中，运行下一次评估之前，它仍将版本 1.237.316.0 显示为“需要”。  再次运行评估时，可能没有可用的新定义更新，因此 Update 表不会将定义更新版本 1.237.316.0 显示为缺失，也不会将可用的新版本显示为“需要”。 由于定义更新的频率，日志搜索中可能会返回多个版本。 

### <a name="resolution"></a>解决方法

运行以下日志查询，确认是否正确报告了安装的定义更新。 此查询返回 Updates 表中 KB2267602 的生成时间、版本和更新 ID。 将 Computer 的值替换为计算机的完全限定名称。

```kusto
Update
| where TimeGenerated > ago(14h) and OSType != "Linux" and (Optional == false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((
    Heartbeat
    | where TimeGenerated > ago(12h) and OSType =~ "Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by Computer, SourceComputerId, UpdateID
| where UpdateState =~ "Needed" and Approved != false and Computer == "<computerName>"
| render table
```

返回的查询结果应类似于以下内容：

:::image type="content" source="./media/update-management/example-query-updates-table.png" alt-text="显示 Updates 表中的日志查询结果的示例。":::

运行以下日志查询，获取 UpdatesRunProgress 表中 KB2267602 的生成时间、版本和更新 ID。 此查询有助于我们了解它是从更新管理安装的，还是从 Microsoft 更新自动安装在计算机上的。 需要将 CorrelationId 的值替换为更新的 Runbook 作业 GUID（即 Patch-MicrosoftOMSComputer Runbook 作业中的 MasterJOBID 属性值），并将 SourceComputerId 的值替换为计算机的 GUID 。

```kusto
UpdateRunProgress
| where OSType!="Linux" and CorrelationId=="<master job id>" and SourceComputerId=="<source computer id>"
| summarize arg_max(TimeGenerated, Title, InstallationStatus) by UpdateId
| project TimeGenerated, id=UpdateId, displayName=Title, InstallationStatus
```

返回的查询结果应类似于以下内容：

:::image type="content" source="./media/update-management/example-query-updaterunprogress-table.png" alt-text="显示 UpdatesRunProgress 表中的日志查询结果的示例。":::

如果 Updates 表中的日志查询结果的 TimeGenerated 值早于计算机上的更新安装的时间戳（即 TimeGenerated 值），或早于 UpdateRunProgress 表中的日志查询结果中的相应值，则等待下一次评估   。 然后，再次对 Updates 表运行日志查询。 不会显示 KB2267602 的更新，或者显示新版本。 但是，即使在最新评估之后，如果 Updates 表中的相同版本显示为“需要”，但该版本已安装，则应打开 Azure 支持事件 。

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>场景：Linux 更新显示为挂起，已安装的更新并不相同

### <a name="issue"></a>问题

对于 Linux 计算机，“更新管理”会在“安全性”和“其他”分类下显示可用的特定更新。 但是，在计算机上运行更新计划来安装特定更新（例如，仅安装与“安全性”分类匹配的更新）时，安装的更新将不同于之前显示的与该分类匹配的更新，或者将是那些更新的子集。

### <a name="cause"></a>原因

在评估 Linux 计算机的挂起的 OS 更新后，更新管理会使用 Linux 发行版供应商提供的[开放漏洞和评估语言](https://oval.mitre.org/) (OVAL) 文件进行分类。 基于 OVAL 文件的 Linux 更新分类为“安全性”或“其他”，其中指明了用于解决安全问题或漏洞的更新 。 但是，当运行更新计划时，它会使用适当的包管理器（例如 YUM、APT 或 ZYPPER）在 Linux 计算机上执行，以便进行安装。 Linux 发行版的包管理器可以通过不同的机制对更新进行分类，其结果可能不同于更新管理从 OVAL 文件获得的结果。

### <a name="resolution"></a>解决方法

你可以根据发行版的包管理器，手动检查 Linux 计算机、适用的更新及其分类。 若要了解哪些更新被包管理器归类为“安全性”，请运行以下命令。

对于 YUM，以下命令返回由 Red Hat 归类为“安全性”的非零数量的更新列表。 请注意，对于 CentOS，它始终返回一个空列表，而不进行安全性分类。

```bash
sudo yum -q --security check-update
```

对于 ZYPPER，以下命令返回由 SUSE 归类为“安全性”的非零数量的更新列表。

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

对于 APT，以下命令返回由 Canonical for Ubuntu Linux 发行版归类为“安全性”的非零数量的更新列表。

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

然后，你可以根据此列表运行命令 `grep ^Inst` 来获取所有挂起的安全更新。

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>场景：收到“无法启用更新解决方案”错误

### <a name="issue"></a>问题

尝试在自动化帐户中启用更新管理时，收到以下错误：

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>原因

出现该错误的原因可能如下：

* 可能是未正确配置 Log Analytics 代理的网络防火墙要求。 这种情况可能会导致代理在解析 DNS URL 时失败。

* 未正确配置更新管理目标，并且计算机未按预期接收更新。

* 你可能还会注意到，计算机在“合规性”下显示了状态 `Non-compliant`。 同时，代理桌面分析将代理报告为 `Disconnected`。

### <a name="resolution"></a>解决方法

* 根据操作系统，运行适用于 [Windows](update-agent-issues.md#troubleshoot-offline) 或 [Linux](update-agent-issues-linux.md#troubleshoot-offline) 的故障排除程序。

* 转到[网络配置](../automation-hybrid-runbook-worker.md#network-planning)，了解需要允许哪些地址和端口才能使更新管理功能正常工作。  

* 检查作用域配置问题。 [作用域配置](../update-management/scope-configuration.md)决定为更新管理配置哪些计算机。 如果计算机显示在工作区中但未显示在“更新管理”中，则必须将范围配置设置为将该计算机作为目标。 若要了解作用域配置，请参阅[在工作区中启用计算机](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)。

* 按照[从本地 Windows 计算机删除混合 Runbook 辅助角色](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker)或[从本地 Linux 计算机删除混合 Runbook 辅助角色](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)中的步骤删除辅助角色配置。

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>方案：被取代的更新在“更新管理”中显示为缺失

### <a name="issue"></a>问题

旧更新即使已被取代，在自动化帐户中仍会显示为缺失。 被取代的更新是指不必安装的更新，因为推出的后续更新可纠正相同的漏洞。 为了支持取代旧更新的更新，“更新管理”会忽略被取代的更新，并使其“不适用”。 若需了解相关问题的信息，请参阅[更新被取代](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="cause"></a>原因

被取代的更新在 Windows Server Update Services (WSUS) 中不是“已拒绝”，因此无法将其视为“不适用”。

### <a name="resolution"></a>解决方法

当被取代的更新完全不适用时，应在 WSUS 中将该更新的批准状态更改为 `Declined`。 若要更改所有更新的审批状态，请执行以下操作：

1. 在自动化帐户中，选择“更新管理”来查看计算机的状态。 请参阅[查看更新评估](../update-management/view-update-assessments.md)。

2. 检查被取代的更新，确保其 100% 不适用。

3. 在计算机向其报告的 WSUS 服务器上，[拒绝更新](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates)。

4. 选择“计算机”，然后在“合规性”列中，强制执行重新扫描，以检查合规性 。 请参阅[管理 VM 的更新](../update-management/manage-updates-for-vm.md)。

5. 对于其他被取代的更新，请重复上述步骤。

6. 对于 Windows Server Update Services (WSUS)，请清除所有被取代的更新以使用 WSUS [清理向导](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)刷新基础结构。

7. 定期重复此过程以更正显示问题，并最大程度地减少用于更新管理的磁盘空间量。

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>场景：更新管理下的门户中未显示计算机

### <a name="issue"></a>问题

计算机出现以下问题：

* 计算机在 VM 的更新管理视图中显示为 `Not configured`。

* Azure 自动化帐户的“更新管理”视图中缺失计算机。

* 在“合规性”下，计算机显示为 `Not assessed`。 但 Azure Monitor 日志中会显示混合 Runbook 辅助角色的检测信号数据，而不会显示更新管理的检测信号数据。

### <a name="cause"></a>原因

导致此问题的原因可能是本地配置问题或作用域配置不当。 可能的特定原因如下：

* 可能需要重新注册并重新安装混合 Runbook 辅助角色。

* 可能在工作区中定义的配额已满，导致无法继续存储数据。

### <a name="resolution"></a>解决方法

1. 根据操作系统，运行适用于 [Windows](update-agent-issues.md#troubleshoot-offline) 或 [Linux](update-agent-issues-linux.md#troubleshoot-offline) 的故障排除程序。

2. 请确保你的计算机向正确的工作区报告。 有关如何进行这方面验证的指导，请参阅[验证代理与 Azure Monitor 的连接](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor)。 此外，请确保此工作区已链接到 Azure 自动化帐户。 若要进行验证，请转到自动化帐户，选择“相关资源”下的“链接的工作区” 。

3. 确保链接到自动化帐户的 Log Analytics 工作区中显示计算机。 在 Log Analytics 工作区中运行以下查询。

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    如果查询结果中未显示计算机，则表示该计算机最近尚未签入。 可能存在本地配置问题，因此应该[重新安装代理](../../azure-monitor/agents/agent-windows.md)。

    如果你的计算机在查询结果中列出，请在“解决方案”属性下验证是否列出了“更新”。 这验证它是否已在“更新管理”中注册。 如果未注册，请检查是否存在范围配置问题。 [作用域配置](../update-management/scope-configuration.md)决定为更新管理配置哪些计算机。 若要为目标计算机配置范围，请参阅[在工作区中启用计算机](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)。

4. 在工作区中运行此查询。

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   如果结果为 `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`，则表示工作区中定义的配额已满，这导致无法保存数据。 在工作区中，转到“使用情况和预估成本”下的“数据量管理”，然后更改或删除配额 。

5. 如果问题仍未解决，请遵循[部署 Windows 混合 Runbook 辅助角色](../automation-windows-hrw-install.md)中的步骤来为 Windows 重新安装混合辅助角色。 对于 Linux，请按照[部署 Linux 混合 Runbook 辅助角色](../automation-linux-hrw-install.md)中的步骤操作。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>场景：无法为订阅注册自动化资源提供程序

### <a name="issue"></a>问题

在自动化帐户中部署功能时，发生以下错误：

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>原因

未在订阅中注册自动化资源提供程序。

### <a name="resolution"></a>解决方法

若要注册自动化资源提供程序，请在 Azure 门户中执行以下步骤。

1. 在门户底部的 Azure 服务列表中，选择“所有服务”，然后在“常规”服务组中选择“订阅” 。

2. 选择订阅。

3. 在“设置”下，选择“资源提供程序” 。

4. 在资源提供程序列表中，验证是否注册了 Microsoft.Automation 资源提供程序。

5. 如果未列出该提供程序，请按照[解决资源提供程序注册错误](../../azure-resource-manager/templates/error-register-resource-provider.md)中的步骤操作，注册 Microsoft.Automation 提供程序。

## <a name="scenario-scheduled-update-did-not-patch-some-machines"></a><a name="scheduled-update-missed-machines"></a>场景：计划的更新未修补一些计算机

### <a name="issue"></a>问题

更新预览中包含的计算机不会全部显示在要在计划的运行期间进行修补的计算机的列表中，或者动态组的所选作用域的 VM 没有显示在门户的更新预览列表中。

更新预览列表包含通过 [Azure Resource Graph](../../governance/resource-graph/overview.md) 查询针对所选作用域检索的所有计算机。 作用域筛选为安装了系统混合 Runbook 辅助角色的计算机以及你具有访问权限的计算机。

### <a name="cause"></a>原因

导致此问题的原因可能是以下之一：

* 没有为注册的自动化资源提供程序配置动态查询作用域中定义的订阅。

* 执行计划时，计算机不可用或缺少适当的标记。

* 对所选作用域不具有正确的访问权限。

* Azure Resource Graph 查询不检索预期的计算机。

* 计算机未安装系统混合 Runbook 辅助角色。

### <a name="resolution"></a>解决方法

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>未为注册的自动化资源提供程序配置订阅

如果未为自动化资源提供程序配置订阅，则无法查询或获取该订阅中关于计算机的信息。 使用以下步骤验证订阅的注册情况。

1. 在 [Azure 门户](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中，访问 Azure 服务列表。

2. 在“常规”服务组中，依次选择“所有服务”和“订阅” 。

3. 查找部署作用域中定义的订阅。

4. 在“设置”下，选择“资源提供程序” 。

5. 验证是否注册了 Microsoft.Automation 资源提供程序。

6. 如果未列出该提供程序，请按照[解决资源提供程序注册错误](../../azure-resource-manager/templates/error-register-resource-provider.md)中的步骤操作，注册 Microsoft.Automation 提供程序。

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>执行计划时，计算机不可用或标记不当

如果为自动化资源提供程序配置了订阅，但在运行更新计划时，指定的[动态组](../update-management/configure-groups.md)缺失了某些计算机，请执行以下操作。

1. 在 Azure 门户中，打开自动化帐户，然后选择“更新管理”。

2. 检查[更新管理历史记录](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment)，以确定运行更新部署的确切时间。

3. 对于可能是更新管理所缺失的计算机，请使用 Azure Resource Graph (ARG) [查找计算机更改](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details)。

4. 搜索运行更新部署之前的某个时间段（不要太短，例如一天）内的更改。

5. 检查搜索结果，确定此时段内是否有任何针对计算机的系统更改，例如删除或更新更改。 这些更改可能会改变计算机状态或标记，导致部署更新时计算机在计算机列表中不会被选中。

6. 根据需要调整计算机和资源设置以纠正计算机状态或标记问题。

7. 重新运行更新计划，以确保具有指定动态组的部署包括所有计算机。

#### <a name="incorrect-access-on-selected-scopes"></a>对所选作用域的访问权限不正确

Azure 门户仅显示你在给定作用域内具有写入访问权限的计算机。 如果你在某个范围内没有适当的访问权限，请参阅[教程：使用 Azure 门户授予用户对 Azure 资源的访问权限](../../role-based-access-control/quickstart-assign-role-user-portal.md)。

#### <a name="resource-graph-query-doesnt-return-expected-machines"></a>Resource Graph 查询未返回预期的计算机

按照以下步骤操作，查看查询是否正常工作。

1. 运行 Azure Resource Graph 查询，格式如下方 Azure 门户的 Resource Graph 资源管理器边栏选项卡中所示。 如果你不熟悉 Azure Resource Graph，请参阅此[快速入门](../../governance/resource-graph/first-query-portal.md)，了解如何使用 Resource Graph 资源管理器。 此查询模拟在更新管理中创建动态组时所选的筛选器。 请参阅[将动态组与更新管理配合使用](../update-management/configure-groups.md)。

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   以下是示例：

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. 查看查询结果中是否列出了你要查找的计算机。

3. 如果未列出所需计算机，动态组中所选的筛选器可能存在问题。 根据需要调整组配置。

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>未在计算机上安装的混合 Runbook 辅助角色

Azure Resource Graph 查询结果中确实显示了计算机，但动态组预览中仍未显示。 在这种情况下，可能不会将计算机指定为系统混合 Runbook 辅助角色，因此无法运行 Azure 自动化和更新管理作业。 若要确保将所需计算机设置为系统混合 Runbook 辅助角色，请执行以下操作：

1. 在 Azure 门户中，转到自动化帐户，找到某个未正确显示的计算机。

2. 在“流程自动化”下选择“混合辅助角色组” 。

3. 选择“系统混合辅助角色组”选项卡。

4. 验证是否为该计算机显示了混合辅助角色。

5. 如果未将计算机设置为系统混合 Runbook 辅助角色，请使用以下方式之一查看启用方法：

   - 从一台或多台 Azure 和非 Azure 计算机（包括已启用 Azure Arc 的服务器）的[自动化帐户](../update-management/enable-from-automation-account.md)。

   - 使用 Enable-AutomationSolution [runbook](../update-management/enable-from-runbook.md) 自动加入 Azure VM。

   - 从 Azure 门户中的“虚拟机”页为[所选 Azure VM](../update-management/enable-from-vm.md) 启用。 此方案适用于 Linux 和 Windows VM。

   - 可以从 Azure 门户中的“虚拟机”页选择启用[多个 Azure VM](../update-management/enable-from-portal.md)。

   用于启用的方法基于计算机的运行环境。

6. 针对预览中未显示的所有计算机，重复上述步骤。

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>场景：已启用更新管理组件，但 VM 仍显示为正在配置

### <a name="issue"></a>问题

在部署开始 15 分钟后继续在 VM 上看到以下消息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

出现该错误的原因可能如下：

* 与自动化帐户的通信被阻止。

* 同一计算机名称具有不同的源计算机 ID。 当在不同的资源组中都创建一个具有特定计算机名称的 VM 并且这些 VM 向订阅中的同一 Logistics Agent 工作区报告时，会发生这种情况。

* 正在部署的 VM 映像可能来自于某个克隆计算机，该计算机未在安装了用于 Windows 的 Log Analytics 代理的情况下使用系统准备 (sysprep) 进行配置。

### <a name="resolution"></a>解决方法

为帮助确定 VM 的确切问题，请在链接到自动化帐户的 Log Analytics 工作区中运行以下查询。

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>与自动化帐户的通信被阻止

转到[网络规划](../update-management/plan-deployment.md#ports)，了解必须允许哪些地址和端口才能使更新管理正常工作。

#### <a name="duplicate-computer-name"></a>重复的计算机名称

重命名 VM，以确保其名称在环境中的唯一性。

#### <a name="deployed-image-from-cloned-machine"></a>从克隆计算机部署了映像

如果使用的是克隆映像，则不同的计算机名具有相同的源计算机 ID。 在这种情况下：

1. 在 Log Analytics 工作区中，从已保存的范围配置 `MicrosoftDefaultScopeConfig-Updates`（若显示）的搜索中删除 VM。 已保存的搜索位于工作区的“常规”下。

2. 运行以下 cmdlet。

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. 运行 `Restart-Service HealthService` 重新启动运行状况服务。 此操作将重新创建密钥并生成新的 UUID。

4. 如果这种方法不起作用，请先对映像运行 sysprep，然后安装适用于 Windows 的 Log Analytics 代理。

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>场景：在为另一个 Azure 租户中的计算机创建更新部署时收到链接订阅错误

### <a name="issue"></a>问题

尝试为另一个 Azure 租户中的计算机创建更新部署时遇到以下错误：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

当创建的更新部署包含另一个租户中的 Azure VM 时会发生此错误。

### <a name="resolution"></a>解决方法

使用以下解决方法来安排这些项。 可以将 [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) cmdlet 与 `ForUpdateConfiguration` 参数一起使用来创建计划。 然后，使用 [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) cmdlet，并将另一个租户中的计算机传递给 `NonAzureComputer` 参数。 以下示例介绍如何执行此操作：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>场景：原因不明的重启

### <a name="issue"></a>问题

即使已将“重启控制”选项设置为“永不重启”，计算机仍将在安装更新后重新启动 。

### <a name="cause"></a>原因

多个注册表项都可以修改 Windows 更新，其中任何一个都可以修改重启行为。

### <a name="resolution"></a>解决方法

查看[通过编辑注册表来配置自动更新](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)和[用于管理重启的注册表项](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)下列出的注册表项，确保计算机配置正确。

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>场景：计算机在更新部署中显示为“无法启动”

### <a name="issue"></a>问题

计算机显示 `Failed to start` 或 `Failed` 状态。 查看计算机的特定详细信息时，看到以下错误：

```error
For one or more machines in schedule, UM job run resulted in either Failed or Failed to start state. Guide available at https://aka.ms/UMSucrFailed.
```

### <a name="cause"></a>原因

存在以下任一原因时，可能出现此错误：

* 计算机不再存在。
* 计算机已关闭且无法访问。
* 计算机存在网络连接问题，因此无法访问计算机上的混合辅助角色。
* 对 Log Analytics 代理的某项更新更改了源计算机 ID。
* 如果在自动化帐户中达到了 200 个并发作业的限制，则更新运行会受到限制。 每个部署均视为一项作业，更新部署中的每台计算机均计为一个作业。 自动化帐户中当前运行的其他任何自动化作业或更新部署均计入并发作业，受其数量限制的约束。

### <a name="resolution"></a>解决方法

可使用 REST API 以编程方式检索更多详细信息。 请查看[软件更新配置计算机运行](/rest/api/automation/softwareupdateconfigurationmachineruns)，了解如何检索更新配置计算机运行的列表，或者如何通过 ID 检索单个软件更新配置计算机运行。

如果适用，请为更新部署使用[动态组](../update-management/configure-groups.md)。 此外，可以执行以下步骤。

1. 验证计算机或服务器是否满足[要求](../update-management/operating-system-requirements.md)。
2. 使用混合 Runbook 辅助角色代理故障排除程序验证与混合 Runbook 辅助角色的连接。 若要了解有关故障排除程序的详细信息，请参阅[排查更新代理问题](update-agent-issues.md)。

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>场景：在没有部署的情况下安装更新

### <a name="issue"></a>问题

在更新管理中注册 Windows 计算机时，你会看到在没有部署的情况下安装的更新。

### <a name="cause"></a>原因

在 Windows 上，更新一旦可用就会自动安装。 如果未计划将更新部署到计算机，则此行为可能会导致混淆。

### <a name="resolution"></a>解决方法

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` 注册表项默认设置为 4：`auto download and install`。

对于更新管理客户端，建议将此项设置为 3：`auto download but do not auto install`。

有关详细信息，请参阅[配置自动更新](/windows/deployment/update/waas-wu-settings#configure-automatic-updates)。

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>场景：计算机已注册到其他帐户

### <a name="issue"></a>问题

看到以下错误消息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

计算机已部署到其他进行更新管理的工作区。

### <a name="resolution"></a>解决方法

1. 按[计算机不显示在门户中的更新管理下](#nologs)中的步骤操作，确保计算机向正确的工作区报告。
2. 通过[删除混合 runbook 组](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)对计算机上的项目进行清理，然后重试。

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>场景：计算机无法与服务进行通信

### <a name="issue"></a>问题

收到以下错误消息之一：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>原因

可能是因为代理、网关或防火墙阻止了网络通信。

### <a name="resolution"></a>解决方法

检查网络并确保允许适当的端口和地址。 有关更新管理和混合 Runbook 辅助角色所需的端口和地址列表，请参阅[网络要求](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>场景：无法创建自签名证书

### <a name="issue"></a>问题

收到以下错误消息之一：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

混合 Runbook 辅助角色无法生成自签名证书。

### <a name="resolution"></a>解决方法

请验证系统帐户是否具有对文件夹 C:\ProgramData\Microsoft\Crypto\RSA 的读取权限，然后重试。

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>场景：计划的更新失败，并出现 MaintenanceWindowExceeded 错误

### <a name="issue"></a>问题

更新的默认维护时段为 120 分钟。 最多可将维护时段增至 6 小时，即 360 分钟。 可能会收到错误消息 `For one or more machines in schedule, UM job run resulted in Maintenance Window Exceeded state. Guide available at https://aka.ms/UMSucrMwExceeded.`

### <a name="resolution"></a>解决方法

若要了解更新成功启动后在运行期间发生此错误的原因，请[检查运行中受影响的计算机的作业输出](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment)。 可以从计算机查找特定的错误消息，可以对这些错误消息进行调查并对其采取操作。  

可使用 REST API 以编程方式检索更多详细信息。 请查看[软件更新配置计算机运行](/rest/api/automation/softwareupdateconfigurationmachineruns)，了解如何检索更新配置计算机运行的列表，或者如何通过 ID 检索单个软件更新配置计算机运行。

编辑任何失败的计划更新部署，并增加维护时段。

有关维护时段的详细信息，请参阅[安装更新](../update-management/deploy-updates.md#schedule-an-update-deployment)。

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>场景：计算机显示“未评估”，并显示 HRESULT 异常

### <a name="issue"></a>问题

* 计算机在“合规性”下显示 `Not assessed`，并且能看到下面显示一条异常消息。
* 你会在门户中看到 HRESULT 错误代码。

### <a name="cause"></a>原因

未正确配置更新代理（Windows 上的 Windows 更新代理；Linux 分发的包管理器）。 更新管理依赖于计算机的更新代理来提供所需的更新、修补程序的状态，以及所部署的修补程序的结果。 如果没有该信息，则更新管理无法正确报告所需的或已安装的修补程序。

### <a name="resolution"></a>解决方法

尝试在计算机上本地执行更新。 如果此操作失败，则通常表示存在更新代理配置错误。

此问题通常是由网络配置和防火墙问题导致的。 执行以下检查来更正问题。

* 对于 Linux，请查看相应文档，确保可以访问包存储库的网络终结点。

* 对于 Windows，请检查[无法从 intranet 终结点 (WSUS/SCCM) 下载更新](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)中所列的代理配置。

  * 如果为 Windows 更新配置了计算机，请确保可以访问[与 HTTP/代理相关的问题](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中所述的终结点。
  * 如果为 Windows Server Update Services (WSUS) 配置了计算机，请确保可以访问由 [WUServer 注册表项](/windows/deployment/update/waas-wu-settings)配置的 WSUS 服务器。

如果看到 HRESULT，双击显示为红色的异常，查看完整的异常消息。 查看下表，了解可能采取的解决方案或推荐操作。

|异常  |解决方法或操作  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 搜索 [Windows 更新错误代码列表](https://support.microsoft.com/help/938205/windows-update-error-code-list)中的相关错误代码，以查找有关异常原因的其他详细信息。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 这些表示是网络连接问题。 请确保你的计算机具有与更新管理的网络连接。 请参阅[网络规划](../update-management/plan-deployment.md#ports)部分，了解所需的端口和地址的列表。        |
|`0x8024001E`| 由于服务或系统正关闭，未能完成更新操作。|
|`0x8024002E`| 已禁用 Windows 更新服务。|
|`0x8024402C`     | 如果使用 WSUS 服务器，请确保注册表项 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 下 `WUServer` 和 `WUStatusServer` 的注册表值指定的是正确的 WSUS 服务器。        |
|`0x80072EE2`|网络连接问题或与已配置的 WSUS 服务器通信的问题。 检查 WSUS 设置并确保可以从客户端访问服务。|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 请确保 Windows 更新服务 (wuauserv) 正在运行，并且未禁用。        |
|`0x80070005`| 出现拒绝访问错误的原因可能是以下之一：<br> 感染的计算机<br> 未正确配置 Windows 更新设置<br> %WinDir%\SoftwareDistribution 文件夹的文件权限错误<br> 系统驱动器 (C:) 的磁盘空间不足。
|任何其他一般异常     | 在 Internet 上搜索可能的解决方案，并与本地 IT 支持人员合作。         |

查看 %Windir%\Windowsupdate.log 文件还可以帮助确定可能的原因。 有关如何阅读日志的详细信息，请参阅[如何阅读 Windowsupdate.log 文件](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)。

你还可以下载并运行 [Windows 更新故障排除程序](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)，以检查计算机上的 Windows 更新是否存在任何问题。

> [!NOTE]
> 根据 [Windows 更新故障排除程序](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)文档，该程序不仅适用于 Windows 客户端，还适用于 Windows Server。

## <a name="scenario-update-run-returns-failed-status-linux"></a>方案：更新运行返回状态“失败”(Linux)

### <a name="issue"></a>问题

启动更新运行后，在运行期间遇到错误。

### <a name="cause"></a>原因

可能的原因：

* 包管理器运行状况不正常。
* 错误地配置了更新代理（适用于 Windows 的 WUA，适用于 Linux 的发行版特定包管理器）。
* 特定包干扰基于云的修补。
* 无法访问计算机。
* 更新具有未解析的依赖关系。

### <a name="resolution"></a>解决方法

如果更新运行成功启动后又失败，请[检查运行中受影响的计算机的作业输出](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment)。 可以从计算机查找特定的错误消息，可以对这些错误消息进行调查并对其采取操作。 更新管理要求包管理器正常运行才能成功进行更新部署。

如果看到特定修补程序、包或更新后作业随即失败，则可以尝试在下一次更新部署中[排除](../update-management/deploy-updates.md#schedule-an-update-deployment)这些项。 若要从 Windows 更新收集日志信息，请参阅 [Windows 更新日志文件](/windows/deployment/update/windows-update-logs)。

如果无法解决某个修补问题，请在下次更新部署启动之前创建 /var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log 文件的副本，并保留它以用于故障排除。

## <a name="patches-arent-installed"></a>未安装修补程序

### <a name="machines-dont-install-updates"></a>计算机未安装更新

请尝试直接在计算机上运行更新。 如果计算机无法应用更新，请查阅[故障排除指南中的潜在错误列表](#hresult)。

如果更新在本地运行，请尝试按照[从更新管理中删除 VM](../update-management/remove-vms.md) 中的指南在计算机上删除并重新安装代理。

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>我知道有可用更新，但更新并未在计算机上显示为可用

如果将计算机配置为从 WSUS 或 Microsoft Endpoint Configuration Manager 获取更新，但 WSUS 和 Configuration Manager 并未批准相应的更新，则往往会发生这种情况。

可以检查是否已针对 WSUS 和 SCCM 配置计算机，方法是将 `UseWUServer` 注册表项交叉引用到[本文“通过编辑注册表配置自动更新”部分](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)的注册表项。

如果 WSUS 中未批准更新，则不会安装更新。 可以通过运行以下查询在 Log Analytics 中检查未批准的更新。

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>更新显示为已安装，但我在计算机上找不到它们

更新通常会被其他更新替代。 有关详细信息，请参阅 Windows 更新疑难解答指南中的[“更新被替代”](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)。

### <a name="installing-updates-by-classification-on-linux"></a>按 Linux 上的分类安装更新

按分类（“关键更新和安全更新”）将更新部署到 Linux 有重要的注意事项，尤其是对 CentOS 来说。 这些[限制记录在“更新管理”概览页上](../update-management/overview.md#update-classifications)。

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 始终缺失

KB2267602 是 [Windows Defender 定义更新](https://www.microsoft.com/wdsi/definitions)。 它每天更新一次。

## <a name="next-steps"></a>后续步骤

如果看不到你的问题，或者无法解决你的问题，请尝试以下通道之一以获取其他支持。

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 连接到 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。
* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
