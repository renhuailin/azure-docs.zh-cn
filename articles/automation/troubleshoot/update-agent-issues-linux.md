---
title: 使用 Azure 自动化排查 Linux 更新代理问题
description: 本文介绍如何排查和解决在进行更新管理时出现的 Linux Windows 更新代理问题。
services: automation
ms.date: 01/25/2021
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 9fdfa1541e928491aecc039d51338f7ef1f1ef3d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181414"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>排查 Linux 更新代理问题

在进行更新管理时计算机未显示为已就绪（正常），这可能有多种原因。 你可以检查 Linux 混合 Runbook 辅助角色代理的运行状况，以确定潜在问题。 以下是计算机的三种就绪状态：

* 迁移就绪性：已部署混合 Runbook 辅助角色，并且上次访问它的时间距当前时间不到一小时。
* 已断开连接：已部署混合 Runbook 辅助角色，并且上次访问它的时间距当前时间超过一小时。
* 未配置：混合 Runbook 辅助角色找不到或尚未完成部署。

> [!NOTE]
> Azure 门户显示的内容和计算机的当前状态之间可能会些微延迟。

本文介绍如何从 Azure 门户为 Azure 计算机运行故障排除，以及如何为[离线场景](#troubleshoot-offline)下的非 Azure 计算机运行故障排除。

> [!NOTE]
> 如果配置了代理服务器，则故障排除脚本当前不会通过它路由流量。

## <a name="start-the-troubleshooter"></a>启动“故障排除”

对于 Azure 计算机，请选择门户中“更新代理准备”列下的“故障排除”链接，以打开“排除更新代理故障”页 。 对于非 Azure 计算机，该链接会转到本文。 若要对非 Azure 计算机进行故障排除，请参阅“脱机进行故障排除”部分中的说明。

![VM 列表页](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> 检查要求 VM 处于运行状态。 如果 VM 没有运行，则会显示“启动 VM”。

在“排除更新代理故障”页上，选择“运行检查”，启动故障排除。 故障排除将使用[运行命令](../../virtual-machines/linux/run-command.md)在计算机上运行脚本，以验证依赖项。 完成故障排除时，它会返回检查的结果。

![故障排除页](../media/update-agent-issues-linux/troubleshoot-page.png)

检查完成后，在窗口中将返回结果。 检查部分提供了每项检查所要查找的内容相关信息。

![更新代理检查页](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先决条件检查

### <a name="operating-system"></a>操作系统

操作系统检查将验证混合 Runbook 辅助角色是否正在运行以下操作系统之一。

|操作系统  |说明  |
|---------|---------|
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理必须具有访问更新存储库的权限。 基于分类的修补需要借助“yum”来返回 CentOS 当前没有的安全数据。         |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 LTS (x86/x64)      |Linux 代理必须具有访问更新存储库的权限。         |

## <a name="monitoring-agent-service-health-checks"></a>监视代理服务运行状况检查

### <a name="log-analytics-agent"></a>Log Analytics 代理

此检查可确保已安装适用于 Linux 的 Log Analytics 代理。 有关如何安装的说明，请参阅[安装适用于 Linux 的代理](../../azure-monitor/vm/monitor-virtual-machine.md#agents)。

### <a name="log-analytics-agent-status"></a>Log Analytics 代理状态

此检查可确保适用于 Linux 的 Log Analytics 代理正在运行。 如果该代理未在运行，则可以运行以下命令尝试重启该代理。 有关对该代理进行故障排除的详细信息，请参阅 [Linux - 排查混合 Runbook 辅助角色问题](hybrid-runbook-worker.md#linux)。

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>多宿主

此检查可确定代理是否向多个工作区报告。 更新管理不支持多宿主。

### <a name="hybrid-runbook-worker"></a>混合 Runbook 辅助角色

此检查可验证适用于 Linux 的 Log Analytics 代理是否拥有混合 Runbook 辅助角色包。 更新管理需要此包才能工作。 若要了解详细信息，请参阅[适用于 Linux 的 Log Analytics 代理未运行](hybrid-runbook-worker.md#oms-agent-not-running)。

更新管理将从操作终结点下载混合 Runbook 辅助角色包。 因此，如果混合 Runbook 辅助角色未运行，并且[操作终结点](#operations-endpoint)检查失败，则更新可能会失败。

### <a name="hybrid-runbook-worker-status"></a>混合 Runbook 辅助角色状态

此检查可确保混合 Runbook 辅助角色在计算机上运行。 如果混合 Runbook 辅助角色正常运行，则应存在以下示例中的进程。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>连接性检查

### <a name="general-internet-connectivity"></a>一般 Internet 连接

此检查可确保计算机拥有 Internet 访问权限。

### <a name="registration-endpoint"></a>注册终结点

此检查可确定混合 Runbook 辅助角色是否可以与 Log Analytics 工作区中的 Azure 自动化正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与注册终结点通信。 有关要打开的地址和端口的列表，请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="operations-endpoint"></a>操作终结点

此检查可确定 Log Analytics 代理是否可以与作业运行时数据服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与作业运行时数据服务通信。 有关要打开的地址和端口的列表，请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="log-analytics-endpoint-1"></a>Log Analytics 终结点 1

此检查会验证计算机是否可以访问 Log Analytics 代理所需的终结点。

### <a name="log-analytics-endpoint-2"></a>Log Analytics 终结点 2

此检查会验证计算机是否可以访问 Log Analytics 代理所需的终结点。

### <a name="log-analytics-endpoint-3"></a>Log Analytics 终结点 3

此检查会验证计算机是否可以访问 Log Analytics 代理所需的终结点。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>脱机进行故障排除

可以通过在本地运行脚本，在混合 Runbook 辅助角色上脱机使用故障排除。 可在 GitHub 中查看 Python 脚本 [UM_Linux_Troubleshooter_Offline.py](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py)。 以下示例显示了此脚本的输出示例：

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>后续步骤

[排查混合 Runbook 辅助角色问题](hybrid-runbook-worker.md)。