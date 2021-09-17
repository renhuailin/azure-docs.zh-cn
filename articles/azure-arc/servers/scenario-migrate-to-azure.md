---
title: 将已启用 Azure Arc 的服务器迁移到 Azure
description: 了解如何将本地运行的已启用 Azure Arc 的服务器或其他云环境迁移到 Azure。
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 5433c859389722884df525ab7ac885ae013f9e59
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768272"
---
# <a name="migrate-your-on-premises-or-other-cloud-arc-enabled-server-to-azure"></a>将本地或其他已启用 Arc 的云服务器迁移到 Azure

本文旨在帮助你进行计划，以便成功地将本地服务器或由已启用 Azure Arc 的服务器托管的虚拟机迁移到 Azure。 通过执行这些步骤，将可从已启用 Arc 的服务器（基于已安装的受支持 VM 扩展）和 Azure 服务（基于其 Arc 服务器资源标识）转换管理。

在执行这些步骤前，请查看 Azure Migrate [准备本地计算机以迁移到 Azure](../../migrate/prepare-for-migration.md) 一文，了解使用 Azure Migrate 的准备要求。

本文内容：

* 清点已启用 Azure Arc 的服务器支持的已安装 VM 扩展。
* 从已启用 Arc 的服务器中卸载所有 VM 扩展。
* 标识已配置为使用已启用 Arc 的服务器托管标识进行身份验证的 Azure 服务，并准备在迁移后更新这些服务以使用 Azure VM 标识。
* 查看授予已启用 Arc 的服务器资源的 Azure 基于角色的访问控制 (Azure RBAC) 访问权限，以便维护在将资源迁移到 Azure VM 后有权访问该资源的人员。 
* 从 Azure 中删除已启用 Arc 的服务器资源标识，并删除已启用 Arc 的服务器代理。
* 安装 Azure 来宾代理。
* 将服务器或 VM 迁移到 Azure。

## <a name="step-1-inventory-and-remove-vm-extensions"></a>步骤 1：清点和删除 VM 扩展

若要清点已启用 Arc 的服务器上安装的 VM 扩展，可以使用 Azure CLI 或 Azure PowerShell 将其列出。

借助 Azure PowerShell，将 [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) 命令与 `-MachineName` 和 `-ResourceGroupName` 参数一起使用。

借助 Azure CLI，将 [az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) 命令与 `--machine-name` 和 `--resource-group` 参数一起使用。 默认情况下，Azure CLI 命令的输出采用 JSON（JavaScript 对象表示法）格式。 若要更改列表或表的默认输出，请使用 [az configure --output](/cli/azure/reference-index)。 还可以向任何命令添加 `--output` 来对输出格式进行一次性更改。

确定部署了哪些 VM 扩展后，可以使用 [Azure 门户](manage-vm-extensions-portal.md)、[Azure PowerShell](manage-vm-extensions-powershell.md) 或 [Azure CLI](manage-vm-extensions-cli.md) 将扩展删除。 如果 Log Analytics VM 扩展或依赖项代理 VM 扩展是使用 Azure Policy 和 [VM 见解计划](../../azure-monitor/vm/vminsights-enable-policy.md)部署的，则必须[创建一个排除项](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)，以防止在迁移完成前在已启用 Arc 的服务器上重新评估和部署扩展。

## <a name="step-2-review-access-rights"></a>步骤 2：查看访问权限 

使用 [Azure PowerShell](../../role-based-access-control/role-assignments-list-powershell.md#list-role-assignments-for-a-resource) 和其他 PowerShell 代码列出已启用 Arc 的服务器资源的角色分配，可以将结果导出为 CSV 或其他格式。 

如果对已启用 Arc 的服务器上运行的应用程序或进程使用托管标识，则需要确保 Azure VM 分配有托管标识。 若要查看托管标识的角色分配，可以使用 Azure PowerShell `Get-AzADServicePrincipal` cmdlet。 有关详细信息，请参阅[列出托管标识的角色分配](../../role-based-access-control/role-assignments-list-powershell.md#list-role-assignments-for-a-managed-identity)。 

使用 Azure Policy 审核或配置计算机或服务器中的设置时，还会使用系统托管标识。 对于已启用 Arc 的服务器，将包括来宾配置代理服务，并执行审核设置验证。 迁移后，请参阅 [Azure 虚拟机的部署要求](../../governance/policy/concepts/guest-configuration.md#deploy-requirements-for-azure-virtual-machines)，了解如何通过手动方式或通过来宾配置扩展策略来配置 Azure VM。

使用托管标识访问的任何资源更新角色分配，以允许新的 Azure VM 标识向这些服务进行身份验证。 请参阅以下资源，了解[如何将 Azure 资源的托管标识用于 Azure 虚拟机 (VM)](../../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md)。

## <a name="step-3-disconnect-from-azure-arc-and-uninstall-agent"></a>步骤 3：从 Azure Arc 断开连接并卸载代理

使用以下方法之一删除 Azure 中已启用 Arc 的服务器的资源 ID：

   * 在计算机或者服务器上运行 `azcmagent disconnect` 命令。

   * 在 Azure 门户中已启用 Arc 的所选已注册服务器上，选择顶部栏中的“删除”。

   * 使用 [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) 或 [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource)。 对于 `ResourceType` 参数，请使用 `Microsoft.HybridCompute/machines`。

然后，按照[删除代理](manage-agent.md#remove-the-agent)步骤来删除已启用 Azure Arc 的服务器的 Windows 或 Linux 代理。

## <a name="step-4-install-the-azure-guest-agent"></a>步骤 4：安装 Azure 来宾代理

从本地迁移到 Azure 的 VM 未安装 Linux 或 Windows Azure 来宾代理。 在这些情况下，必须手动安装 VM 代理。 有关如何安装 VM 代理的详细信息，请参阅 [Azure 虚拟机 Windows 代理概述](../../virtual-machines/extensions/agent-windows.md)或 [Azure 虚拟机 Linux 代理概述](../../virtual-machines/extensions/agent-linux.md)。

## <a name="step-5-migrate-server-or-machine-to-azure"></a>步骤 5：将服务器或计算机迁移到 Azure

在继续使用 Azure Migrate 进行迁移之前，请查看[准备本地计算机以迁移到 Azure](../../migrate/prepare-for-migration.md) 一文，了解使用 Azure Migrate 的必需要求。 若要完成到 Azure 的迁移，请基于环境查看 Azure Migrate [迁移选项](../../migrate/prepare-for-migration.md#next-steps)。

## <a name="step-6-deploy-azure-vm-extensions"></a>步骤 6：部署 Azure VM 扩展

迁移并完成所有迁移后配置步骤后，就可以基于最初安装在已启用 Arc 的服务器上的 VM 扩展来部署 Azure VM 扩展了。 查看 [Azure 虚拟机扩展和功能](../../virtual-machines/extensions/overview.md)，以帮助规划扩展部署。 

若要在具有来宾配置策略定义的计算机中继续使用审核设置，请参阅[启用来宾配置](../../governance/policy/concepts/guest-configuration.md#enable-guest-configuration)。

如果 Log Analytics VM 扩展或依赖项代理 VM 扩展是使用 Azure Policy 和 [VM 见解计划](../../azure-monitor/vm/vminsights-enable-policy.md)部署的，请删除之前创建的[排除项](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)。 若要使用 Azure Policy 启用 Azure 虚拟机，请参阅[使用 Azure Policy 大规模部署 Azure Monitor](../../azure-monitor/deploy-scale.md#vm-insights)。 

## <a name="next-steps"></a>后续步骤

有关故障排除信息，请参阅 [Connected Machine 代理故障排除](troubleshoot-agent-onboard.md)指南。