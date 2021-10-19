---
title: 在自动化中部署基于代理的 Linux 混合 Runbook 辅助角色
description: 本文介绍如何安装基于代理的混合 Runbook 辅助角色，以便在本地数据中心或云环境中基于 Linux 的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 09/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6702fb96b616c59b539ce72f8da0bfec2feeec97
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707060"
---
# <a name="deploy-an-agent-based-linux-hybrid-runbook-worker-in-automation"></a>在自动化中部署基于代理的 Linux 混合 Runbook 辅助角色

通过 Azure 自动化的用户混合 Runbook 辅助角色功能，可以直接在 Azure 或非 Azure 计算机上运行 runbook，包括在[启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)上注册的服务器。 在托管角色的计算机或服务器中，可以直接运行 Runbook，并对环境中的资源运行 Runbook，从而管理这些本地资源。

Linux 混合 Runbook 辅助角色以特殊用户身份执行 Runbook，该用户身份可进行权限提升，以运行需要提升权限的命令。 Azure 自动化将存储并管理 Runbook，然后将其传送到一台或多台选定的计算机。 本文介绍了如何在 Linux 计算机上安装混合 Runbook 辅助角色、删除辅助角色以及删除混合 Runbook 辅助角色组。 对于用户混合 Runbook 辅助角色，另请参阅[在自动化中部署基于扩展的 Windows 或 Linux 用户混合 Runbook 辅助角色](./extension-based-hybrid-runbook-worker-install.md)

成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保你具备以下内容。

### <a name="a-log-analytics-workspace"></a>Log Analytics 工作区

混合 Runbook 辅助角色依赖于 Azure Monitor Log Analytics 工作区来安装和配置角色。 你可通过 [Azure 资源管理器](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)、[PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json) 或在 [Azure 门户](../azure-monitor/logs/quick-create-workspace.md)中创建该工作区。

如果没有 Azure Monitor Log Analytics 工作区，请在创建工作区前查看 [Azure Monitor 日志设计指南](../azure-monitor/logs/design-logs-deployment.md)。

### <a name="log-analytics-agent"></a>Log Analytics 代理

混合 Runbook 辅助角色需要受支持的 Linux 操作系统的 [Log Analytics 代理](../azure-monitor/agents/log-analytics-agent.md)。 对于在 Azure 外部托管的服务器或计算机，可以使用[已启用 Azure Arc 的服务器](../azure-arc/servers/overview.md)安装 Log Analytics 代理。 代理与特定的服务帐户一起安装，这些帐户执行需要根权限的命令。 有关详细信息，请参阅[服务帐户](./automation-hrw-run-runbooks.md#service-accounts)。

### <a name="supported-linux-operating-systems"></a>受支持的 Linux 操作系统

混合 Runbook 辅助角色功能支持以下发行版。 所有操作系统都假定为 x64。 任何操作系统均不支持 x86。

* Amazon Linux 2012.09 - 2015.09
* CentOS Linux 5、6、7 和 8
* Oracle Linux 6、7 和 8
* Red Hat Enterprise Linux Server 5、6、7 和 8
* Debian GNU/Linux 6、7 和 8
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS、18.04 和 20.04 LTS
* SUSE Linux Enterprise Server 12、15 和 15.1（SUSE 未发布编号为 13 或 14 的版本）

> [!IMPORTANT]
> 在启用依赖于系统混合 Runbook 辅助角色的更新管理功能之前，请在[此处](update-management/operating-system-requirements.md)确认它支持的分发。

### <a name="minimum-requirements"></a>最低要求

Linux 系统和用户混合 Runbook 辅助角色的最低要求如下：

* 双核
* 4 GB RAM
* 端口 443（出站）

| **必需的程序包** | **说明** | **最低版本**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 库| 2.5-12 |
|Openssl| OpenSSL 库 | 1.0（支持 TLS 1.1 和 TLS 1.2）|
|Curl | cURL Web 客户端 | 7.15.5|
|Python-ctype | 适用于 Python 的外部函数库| 需要 Python 2.x 或 Python 3.x |
|PAM | 可插入验证模块|

| **可选包** | **说明** | **最低版本**|
|--------------------- | --------------------- | -------------------|
| PowerShell Core | 若要运行 PowerShell runbook，需要安装 PowerShell Core。 请参阅[在 Linux 上安装 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) 了解如何安装。 | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>将计算机添加到混合 Runbook 辅助角色组

可将辅助角色计算机添加到其中一个自动化帐户中的混合 Runbook 辅助角色组。 对于托管系统混合 Runbook 辅助角色（由更新管理进行管理）的计算机，可以将其添加到混合 Runbook 辅助角色组。 但必须对更新管理和混合 Runbook 辅助角色组成员身份使用同一自动化帐户。

> [!NOTE]
> Azure 自动化[更新管理](./update-management/overview.md)会自动在启用了更新管理的 Azure 或非 Azure 计算机上安装系统混合 Runbook 辅助角色。 但是，此辅助角色未注册到自动化帐户中的任何混合 Runbook 辅助角色组。 若要在这些计算机上运行 Runbook，需将其添加到混合 Runbook 辅助角色组。 按照[安装 Linux 混合 Runbook 辅助角色](#install-a-linux-hybrid-runbook-worker)部分下的步骤 4 操作，将其添加到组中。

## <a name="supported-linux-hardening"></a>支持的 Linux 强化

尚不支持以下各项：

* CIS

## <a name="supported-runbook-types"></a>支持的 runbook 类型

Linux 混合 Runbook 辅助角色支持 Azure 自动化中有限的一组 Runbook 类型，下表对它们进行了介绍。

|Runbook 类型 | 支持 |
|-------------|-----------|
|Python 3（预览）|是，仅对这些发行版是必需的：SUSE LES 15、RHEL 8 和 CentOS 8|
|Python 2 |是，适用于不需要 Python 3<sup>1</sup> 的任何发行版 |
|PowerShell |Yes<sup>2</sup> |
|PowerShell 工作流 |否 |
|图形 |否 |
|图形 PowerShell 工作流 |否 |

<sup>1</sup>请参阅[支持的 Linux 操作系统](#supported-linux-operating-systems)。

<sup>2</sup>PowerShell Runbook 要求在 Linux 计算机上安装 PowerShell Core。 请参阅[在 Linux 上安装 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) 了解如何安装。

### <a name="network-configuration"></a>网络配置

有关混合 Runbook 辅助角色的网络要求，请参阅[配置网络](automation-hybrid-runbook-worker.md#network-planning)。

## <a name="install-a-linux-hybrid-runbook-worker"></a>安装 Linux 混合 Runbook 辅助角色

有两种方法可以部署混合 Runbook 辅助角色。 你既可以从 Azure 门户中的 Runbook 库导入并运行 runbook，也可以手动运行一系列 PowerShell 命令。

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>从 Runbook 库导入 Runbook

若要详细了解导入过程，请查看[使用 Azure 门户从 GitHub 导入 runbook](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)。 待导入的 runbook 的名称为“创建自动化 Linux HybridWorker”。

runbook 使用以下参数。

| 参数 | 状态 | 说明 |
| ------- | ----- | ----------- |
| `Location` | 必需 | Log Analytics 工作区的位置。 |
| `ResourceGroupName` | 必需 | 自动化帐户的资源组。 |
| `AccountName` | 必需 | 要在其中注册混合 Runbook 辅助角色的自动化帐户的名称。 |
| `CreateLA` | 必需 | 如果为 true，则使用 `WorkspaceName` 的值创建 Log Analytics 工作区。 如果为 false，则 `WorkspaceName` 的值必须引用现有工作区。 |
| `LAlocation` | 可选 | 将在其中创建 Log Analytics 工作区的位置或现有 Log Analytics 工作区所在的位置。 |
| `WorkspaceName` | 可选 | 待创建或待使用的 Log Analytics 工作区的名称。 |
| `CreateVM` | 必需 | 如果为 true，则使用 `VMName` 的值作为新 VM 的名称。 如果为 false，则使用 `VMName` 查找并注册现有 VM。 |
| `VMName` | 可选 | 创建或注册的虚拟机的名称取决于 `CreateVM` 的值。 |
| `VMImage` | 可选 | 待创建的 VM 映像的名称。 |
| `VMlocation` | 可选 | 创建或注册的 VM 位置。 如果未指定此位置，则使用 `LAlocation` 值。 |
| `RegisterHW` | 必需 | 如果为 true，则将 VM 注册为混合辅助角色。 |
| `WorkerGroupName` | 必需 | 混合辅助角色组的名称。 |

### <a name="manually-run-powershell-commands"></a>手动运行 PowerShell 命令

若要安装和配置 Linux 混合 Runbook 辅助角色，请执行以下步骤。

1. 通过在提升的 PowerShell 命令提示符中或在 [Azure 门户](https://portal.azure.com)的 Cloud Shell 中运行以下命令，在 Log Analytics 工作区中启用 Azure 自动化解决方案：

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. 将 Log Analytics 代理部署到目标计算机。

    - 对于 Azure VM，请使用[适用于 Linux 的虚拟机扩展](../virtual-machines/extensions/oms-linux.md)安装适用于 Linux 的 Log Analytics 代理。 该扩展在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Log Analytics 工作区中。 可以使用一个 Azure 资源管理器模板、Azure CLI 或 Azure Policy 分配[为 Linux 或 Windows VM 内置策略定义部署 Log Analytics 代理 ](../governance/policy/samples/built-in-policies.md#monitoring)。 安装代理后，可将计算机添加到自动化帐户中的混合 Runbook 辅助角色组。

    - 对于非 Azure 计算机，可以使用[已启用 Azure Arc 的服务器](../azure-arc/servers/overview.md)安装 Log Analytics 代理。 已启用 Azure Arc 的服务器支持使用以下方法部署 Log Analytics 代理：

      - 使用 VM 扩展框架。

        利用已启用 Azure Arc 的服务器中的这项功能，可以将 Log Analytics 代理 VM 扩展部署到非 Azure Windows 和/或 Linux 服务器。 在由已启用 Azure Arc 的服务器管理的混合计算机或服务器上，可以使用以下方法来管理 VM 扩展：

        - [Azure 门户](../azure-arc/servers/manage-vm-extensions-portal.md)
        - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
        - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
        - Azure [资源管理器模板](../azure-arc/servers/manage-vm-extensions-template.md)

      - 使用 Azure Policy。

        在使用此方法时，请使用 Azure Policy [将 Log Analytics 代理部署到 Linux 或 Windows Azure Arc 计算机](../governance/policy/samples/built-in-policies.md#monitoring)内置策略定义来审核已启用 Arc 的服务器是否已安装 Log Analytics 代理。 如果该代理未安装，则使用修正任务来自动部署该代理。 如果你计划通过用于 VM 的 Azure Monitor 来监视计算机，请改为使用[启用用于 VM 的 Azure Monitor](../governance/policy/samples/built-in-initiatives.md#monitoring) 计划来安装和配置 Log Analytics 代理。

      建议使用 Azure 策略安装适用于 Windows 或 Linux 的 Log Analytics 代理。

    > [!NOTE]
    > 为了使用 Desired State Configuration (DSC) 管理支持混合 Runbook 辅助角色的计算机配置，必须将计算机添加为 DSC 节点。

    > [!NOTE]
    > 安装 Linux 混合辅助角色期间，必须存在具有相应 sudo 权限的 [nxautomation 帐户](automation-runbook-execution.md#log-analytics-agent-for-linux)。 如果尝试安装辅助角色，但该帐户不存在或没有相应权限，则安装会失败。

3. 验证代理是否向工作区报告。

    适用于 Linux 的 Log Analytics 代理会将计算机连接到 Azure Monitor Log Analytics 工作区。 在计算机上安装代理并将其连接到工作区时，代理会自动下载混合 Runbook 辅助角色所需的组件。

    几分钟后，如果代理已成功连接到 Log Analytics 工作区，则可以运行以下查询，验证是否正在向工作区发送检测信号数据。

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    在搜索结果中，应会看到计算机的检测信号记录，它们指示计算机已连接到服务并将该结果报告给服务。 默认情况下，每个代理都会将一个检测信号记录转发到其分配的工作区。

4. 运行以下命令，以将计算机添加到混合 Runbook 辅助角色组，并为参数 `-w`、`-k`、`-g` 和 `-e` 指定值。

    你可以从自动化帐户中的“密钥”页获取参数 `-k` 和 `-e` 所需的信息。 从页面左侧的“帐户设置”部分下，选择“密钥” 。

    ![“管理密钥”页](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * 对于 `-e` 参数，复制 URL 的值。

    * 对于 `-k` 参数，复制主访问密钥的值。

    * 对于 `-g` 参数，请指定新的 Linux 混合 Runbook 辅助角色应加入的混合 Runbook 辅助角色组的名称。 如果自动化帐户中已存在该组，则会将当前计算机添加到其中。 如果该组不存在，则会使用该名称创建它。

    * 对于 `-w` 参数，请指定 Log Analytics 工作区 ID。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. 完成脚本后，验证部署。 在自动化帐户的“混合 Runbook 辅助角色组”页的“使用混合 Runbook 辅助角色组”选项卡下，会显示新组或现有组以及成员数量 。 如果这是现有的组，则成员数会递增。 可从该页上的列表中选择此组，从左侧菜单中选择“混合辅助角色”。 在“混合辅助角色”页上，可以查看列出组的每个成员。

    > [!NOTE]
    > 如果要对 Azure VM 使用用于 Linux 的 Log Analytics 虚拟机扩展，建议将 `autoUpgradeMinorVersion` 设置为 `false`，因为自动升级版本可能会导致混合 Runbook 辅助角色出问题。 若要了解如何手动升级扩展，请参阅 [Azure CLI 部署](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)。

## <a name="turn-off-signature-validation"></a>关闭签名验证

默认情况下，Linux 混合 Runbook 辅助角色需要签名验证。 如果针对辅助角色运行未签名的 Runbook，将看到 `Signature validation failed` 错误。 若要禁用签名验证，请运行以下命令。 将第二个参数替换为 Log Analytics 工作区 ID。

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>删除混合 Runbook 辅助角色

可在混合 Runbook 辅助角色上使用命令 `ls /var/opt/microsoft/omsagent` 获取工作区 ID。 将创建一个使用工作区 ID 命名的文件夹。

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 此脚本不会从计算机中删除适用于 Linux 的 Log Analytics 代理。 它只会删除混合 Runbook 辅助角色的功能和配置。

## <a name="remove-a-hybrid-worker-group"></a>删除混合辅助角色组

若要删除 Linux 计算机的混合 Runbook 辅助角色组，请按照删除 Windows 混合辅助角色组的相同步骤进行操作。 请参阅[删除混合辅助角色组](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[排查混合 Runbook 辅助角色问题 - Linux](troubleshoot/hybrid-runbook-worker.md#linux)。
