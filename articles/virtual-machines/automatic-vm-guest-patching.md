---
title: Azure VM 的自动 VM 来宾修补
description: 了解如何自动修补 Azure 中的虚拟机。
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/29/2021
ms.author: manayar
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0043935f46698f694aa76aec816c8bf72a9ee098
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698166"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>预览：Azure VM 的自动 VM 来宾修补

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

为 Azure VM 启用自动 VM 来宾修补有助于通过安全自动修补虚拟机来简化更新管理，使虚拟机保持安全合规。

自动 VM 来宾修补具有以下特征：
- 在 VM 上自动下载并应用已分类为“关键”或“安全”的补丁。 
- 在 VM 所在时区的非高峰期应用补丁。
- 由 Azure 管理补丁业务流程，遵循[可用性优先原则](#availability-first-updates)应用补丁。
- 监视虚拟机运行状况（通过平台运行状况信号来确定），以检测修补失败的情况。
- 适用于所有大小的 VM。

> [!IMPORTANT]
> 自动 VM 来宾修补目前为公共预览版。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-does-automatic-vm-guest-patching-work"></a>自动 VM 来宾修补的工作原理是什么？

如果在某个 VM 上启用了自动 VM 来宾修补，则会在该 VM 上自动下载并应用可用的关键补丁和安全补丁 。 在每个月发布新的补丁时，此过程将自动启动。 补丁评估和安装是自动的，该过程会根据需要重新启动 VM。

VM 每隔数天定期评估，并在任意 30 天周期内多次评估，以确定适用于该 VM 的补丁。 可在任何一天的 VM 非高峰期将补丁安装到 VM 上。 这种自动评估确保能够尽早发现任何缺失的补丁。

在发布每月补丁后的 30 天内，将按照下面所述的可用性优先业务流程安装补丁。 只会根据 VM 的时区在 VM 的非高峰期安装补丁。 VM 在非高峰期必须正在运行，才能自动安装补丁。 如果 VM 在定期评估期间已关闭，则会自动评估 VM，并在 VM 打开后的下一次定期评估（通常在几天之内）期间自动安装适用的补丁。

定义更新以及未分类为“关键”或“安全”的其他补丁不会通过自动 VM 来宾修补予以安装 。 若要安装属于其他分类的补丁或者要计划在你自己的自定义维护时段内安装补丁，可以使用[更新管理](./windows/tutorial-config-management.md#manage-windows-updates)。

### <a name="availability-first-updates"></a>可用性优先更新

Azure 将为启用了自动 VM 来宾修补功能的所有 VM 全局协调补丁安装过程。 此业务流程根据 Azure 提供的不同可用性级别遵循可用性优先原则。

对于正在更新的一组虚拟机，Azure 平台将协调更新：

跨区域：
- 按照分阶段的方式在整个 Azure 中全局协调每月更新，以防全局部署失败。
- 一个阶段可以涵盖一个或多个区域，仅当某个阶段中符合条件的 VM 成功更新时，更新才会进入下一阶段。
- 地理配对的区域不会并发更新，因此它们不能处于同一区域阶段。
- 更新是否成功是通过跟踪 VM 在更新后的运行状况来衡量的。 VM 运行状况是通过 VM 的平台运行状况指示器跟踪的。

在区域内部：
- 位于不同 Azure 可用性区域中的 VM 不会使用同一更新并发更新。
- 不在可用性集内的 VM 将按照尽力运行的原则进行批处理，以避免对订阅中的所有 VM 进行并发更新。

在可用性集内部：
- 公用可用性集中的所有 VM 不会并发更新。
-   公用可用性集中的 VM 在更新域边界内更新，跨多个更新域的 VM 不会并发更新。

给定 VM 的补丁安装日期在不同的月份都有可能不同，因为特定 VM 可能是在每月修补周期的不同批次中选取的。

### <a name="which-patches-are-installed"></a>安装哪些补丁？
安装的补丁取决于 VM 的推出阶段。 每个月都会启动新的全局推出，此时将为单个 VM 安装已针对该 VM 评估过的所有安全补丁和关键补丁。 将会协调所有 Azure 区域之间的分批推出（请参阅前面的“可用性优先修补”部分）。

要安装的具体补丁集因 VM 配置（包括 OS 类型和评估时间）而异。 如果当补丁业务流程在不同时间转到不同区域时可用的补丁较多或较少，则有可能会在不同区域中的两个相同 VM 上安装不同的补丁。 类似地，位于同一区域中的、但在不同时间评估（由于可用性区域或可用性集的批次不同）VM 上可能会安装不同的补丁，但这种情况更少见。

由于自动 VM 来宾修补不会配置补丁源，因此，配置为使用不同补丁源（例如公共存储库与专用存储库）的两个类似 VM 上安装的具体补丁集也可能不同。

对于按固定节奏发布补丁的 OS 类型，配置为使用公共 OS 存储库的 VM 预期会在一个月内的不同推出阶段收到相同的补丁集。 例如，配置为使用公共 Windows 更新存储库的 Windows VM。

由于每个月都会触发新的推出，如果 VM 在非高峰期处于开机状态，则 VM 在每个月至少会收到一个补丁推出。 这可以确保每个月都会使用最新可用的安全补丁和关键补丁来修补 VM。 若要确保安装的补丁集一致，可以配置 VM 以从你自己的专用存储库评估并下载补丁。

## <a name="supported-os-images"></a>支持的 OS 映像
预览版目前仅支持从特定 OS 平台映像创建的 VM。 预览版目前不支持自定义映像。

目前支持以下平台 SKU（我们会定期添加更多 SKU）：

| 发布者               | OS 产品/服务      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Canonical  | 0001-com-ubuntu-pro-bionic | pro-18_04-lts |
| Canonical  | 0001-com-ubuntu-server-focal | 20_04-lts |
| Canonical  | 0001-com-ubuntu-pro-focal | pro-20_04-lts |
| Redhat  | RHEL | 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7.8, 7_9, 7-RAW, 7-LVM |
| Redhat  | RHEL | 8, 8.1, 8.2, 8_3, 8_4, 8-LVM |
| Redhat  | RHEL-RAW | 8-raw |
| OpenLogic  | CentOS | 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7_8, 7_9, 7-LVM |
| OpenLogic  | CentOS | 8.0, 8_1, 8_2, 8_3, 8-lvm |
| SUSE  | sles-12-sp5 | gen1,gen2 |
| SUSE  | sles-15-sp2 | gen1,gen2 |
| MicrosoftWindowsServer  | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

> [!NOTE]
>自动 VM 来宾修补、按需修补程序评估和按需修补程序安装仅在从图像创建的 VM 上受支持，此类图像具有受支持操作系统图像列表中发布者、报价和 SKU 的确切组合。 不支持自定义图像或任何其他发布者、报价以及 SKU 组合。

## <a name="patch-orchestration-modes"></a>补丁业务流程模式
Azure 上的 VM 现在支持以下补丁业务流程模式：

AutomaticByPlatform：
- Linux 和 Windows VM 都支持此模式。
- 此模式为虚拟机启用自动 VM 来宾修补，后续的补丁安装将由 Azure 协调。
- 可用性优先修补需要此模式。
- 只有使用上述受支持 OS 平台映像创建的 VM 才支持此模式。
- 对于 Windows VM，设置此模式还会禁用 Windows 虚拟机上的本机自动更新，以避免重复。
- 若要在 Linux VM 上使用此模式，请在 VM 模板中设置属性 `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform`。
- 若要在 Windows VM 上使用此模式，请在 VM 模板中设置属性 `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform`。

AutomaticByOS：
- 只有 Windows VM 支持此模式。
- 此模式在 Windows 虚拟机上启用自动更新，将通过自动更新在 VM 上安装补丁。
- 此模式不支持可用性优先修补。
- 如果未为 Windows VM 指定其他修补模式，则默认会设置此模式。
- 若要在 Windows VM 上使用此模式，请设置属性 `osProfile.windowsConfiguration.enableAutomaticUpdates=true`，并在 VM 模板中设置属性 `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS`。

手动：
- 只有 Windows VM 支持此模式。
- 此模式会在 Windows 虚拟机上禁用自动更新。
- 此模式不支持可用性优先修补。
- 使用自定义修补解决方案时应设置此模式。
- 若要在 Windows VM 上使用此模式，请设置属性 `osProfile.windowsConfiguration.enableAutomaticUpdates=false`，并在 VM 模板中设置属性 `osProfile.windowsConfiguration.patchSettings.patchMode=Manual`。

ImageDefault：
- 只有 Linux VM 支持此模式。
- 此模式不支持可用性优先修补。
- 此模式遵循用于创建 VM 的映像中的默认修补配置。
- 如果未为 Linux VM 指定其他修补模式，则默认会设置此模式。
- 若要在 Linux VM 上使用此模式，请在 VM 模板中设置属性 `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault`。

> [!NOTE]
>对于 Windows VM，只能在首次创建 VM 时设置属性 `osProfile.windowsConfiguration.enableAutomaticUpdates`。 这会影响某些修补模式转换。 若 VM 具备 `osProfile.windowsConfiguration.enableAutomaticUpdates=false`，则支持在 AutomaticByPlatform 模式和手动模式之间进行切换。 若 VM 具备 `osProfile.windowsConfiguration.enableAutomaticUpdates=true`，则支持在 AutomaticByPlatform 模式和 AutomaticByOS 模式之间进行类似切换。 不支持在 AutomaticByOS 模式和手动模式之间进行切换。

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>启用自动 VM 来宾修补的要求

- 虚拟机上必须装有适用于 [Windows](./extensions/agent-windows.md) 或 [Linux](./extensions/agent-linux.md) 的 Azure VM 代理。
- 对于 Linux VM，Azure Linux 代理的版本必须是 2.2.53.1 或更高。 如果当前版本低于所需的版本，请[更新 Linux 代理](./extensions/update-linux-agent.md)。
- 对于 Windows VM，Windows 更新服务必须在虚拟机上运行。
- 虚拟机必须能够访问配置的更新终结点。 如果虚拟机配置为使用适用于 Linux 的专用存储库或适用于 Windows VM 的 Windows Server Update Services (WSUS)，则相关的更新终结点必须可访问。
- 使用计算 API 版本 2021-03-01 或更高版本来访问包括按需评估和按需修补在内的所有功能。
- 目前不支持自定义图像。

## <a name="enable-automatic-vm-guest-patching"></a>启用自动 VM 来宾修补
在由可支持的平台映像创建的任何 Windows 或 Linux VM 上，可启用自动 VM 来宾修补。 若要在 Windows VM 上启用自动 VM 来宾修补，请确保在 VM 模板定义中将属性 osProfile.windowsConfiguration.enableAutomaticUpdates 设置为 true 。 只能在创建 VM 时设置此属性。 此附加属性不适用于 Linux VM。

### <a name="rest-api-for-linux-vms"></a>适用于 Linux VM 的 REST API
以下示例说明如何启用自动 VM 来宾修补：

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>适用于 Windows VM 的 REST API
以下示例说明如何启用自动 VM 来宾修补：

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>适用于 Windows VM 的 Azure PowerShell
创建或更新 VM 时，使用 [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) cmdlet 启用自动 VM 来宾修补。

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>适用于 Windows VM 的 Azure CLI
创建新的 VM 时，使用 [az vm create](/cli/azure/vm#az_vm_create) 启用自动 VM 来宾修补。 以下示例为名为 myResourceGroup 的资源组中名为 myVM 的 VM 配置自动 VM 来宾修补 ：

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

若要修改现有的 VM，请使用 [az vm update](/cli/azure/vm#az_vm_update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>启用和评估

> [!NOTE]
>在 VM 上启用自动 VM 来宾更新可能需要三个多小时，因为启用过程是在 VM 的非高峰期完成的。 由于评估和补丁安装只会在非高峰期进行，因此，VM 在非高峰期仍然必须运行才能应用补丁。

为 VM 启用自动 VM 来宾修补后，将在 Linux VM 上安装 `Microsoft.CPlat.Core.LinuxPatchExtension` 类型的 VM 扩展，或者在 Windows VM 上安装 `Microsoft.CPlat.Core.WindowsPatchExtension` 类型的 VM 扩展。 无需手动安装或更新此扩展，因为此扩展由 Azure 平台作为自动 VM 来宾修补过程的一部分进行管理。

在 VM 上启用自动 VM 来宾更新可能需要三个多小时，因为启用过程是在 VM 的非高峰期完成的。 该扩展也是在 VM 的非高峰期安装和更新的。 如果在完成启用之前 VM 的非高峰期已结束，则启用过程将在下一个可用的非高峰期恢复。

自动更新在大多数情况下已禁用，补丁安装将通过今后的扩展安装来完成。 以下条件适用。
- 如果先前已通过 AutomaticByOS 修补模式在 Windows VM 上启用了自动 Windows 更新，则安装扩展时，将为 VM 禁用自动 Windows 更新。
- 对于 Ubuntu VM，当自动 VM 来宾修补完成启用时，会自动禁用默认的自动更新。
- 对于 RHEL，需在预览模式中手动禁用自动更新。 执行：

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

若要验证是否已完成自动 VM 来宾修补并已在 VM 上安装修补扩展，可以查看 VM 的实例视图。 如果启用过程已完成，则扩展已安装，并且 `patchStatus` 下会显示 VM 的评估结果。 可按如下所述的多种方式访问 VM 的实例视图。

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
结合 `-Status` 参数使用 [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet 来访问 VM 的实例视图。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell 目前仅提供有关修补扩展的信息。 不久之后，还可以通过 PowerShell 获取有关 `patchStatus` 的信息。

### <a name="azure-cli"></a>Azure CLI
使用 [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) 访问 VM 的实例视图。

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>了解 VM 的修补状态

实例视图响应的 `patchStatus` 节提供有关 VM 的最新评估以及上次补丁安装的详细信息。

可在 `availablePatchSummary` 节下查看 VM 的评估结果。 将定期为启用了自动 VM 来宾修补的 VM 执行评估。 评估后的可用补丁计数将在 `criticalAndSecurityPatchCount` 和 `otherPatchCount` 结果下提供。 自动 VM 来宾修补将安装属于“关键”和“安全”补丁分类的所有已评估补丁 。 将跳过任何其他已评估的补丁。

可在 `lastPatchInstallationSummary` 节下查看 VM 的补丁安装结果。 此节提供有关上次在 VM 上尝试的补丁安装的详细信息，包括已安装的、挂起的、失败的或已跳过的补丁数量。 只会在 VM 的非高峰期维护时段安装补丁。 在下一个非高峰维护时段，将自动重试挂起的和失败的补丁。

## <a name="disable-automatic-vm-guest-patching"></a>禁用自动 VM 来宾修补
可通过更改 VM 的[修补业务流程模式](#patch-orchestration-modes)来禁用自动 VM 来宾修补。

若要在 Linux VM 上禁用自动 VM 来宾修补，请将修补模式更改为 `ImageDefault`。

若要在 Windows VM 上启用自动 VM 来宾修补，属性 `osProfile.windowsConfiguration.enableAutomaticUpdates` 将确定可在 VM 上设置哪些修补模式，以及此属性只能在首次创建 VM 时进行设置。 这会影响某些修补模式转换：
- 若 VM 具备 `osProfile.windowsConfiguration.enableAutomaticUpdates=false`，通过将修补模式更改为 `Manual` 来禁用自动 VM 访问修补。
- 若 VM 显示`osProfile.windowsConfiguration.enableAutomaticUpdates=true`，可将修补模式更改为 `AutomaticByOS`　来禁用自动 VM 来宾修补。
- 不支持在 AutomaticByOS 模式和手动模式之间进行切换。

若要设置所需的修补模式，请使用本文上述[启用](#enable-automatic-vm-guest-patching)部分中的　API、PowerShell 和 CLI 使用情况示例。

## <a name="on-demand-patch-assessment"></a>按需补丁评估
如果已经为 VM 启用了自动 VM 来宾修补，则会在 VM 的非高峰期在 VM 上定期执行补丁评估。 此过程是自动完成的，可以按照本文档前面所述，通过 VM 的实例视图查看最新评估的结果。 此外，可以随时为 VM 触发按需补丁评估。 补丁评估可能需要几分钟才能完成，最新评估的状态将在 VM 的实例视图上更新。

> [!NOTE]
>按需补丁评估不会自动触发补丁安装。 如果你已启用自动 VM 来宾修补，则会遵循本文档前面所述的可用性优先修补过程，在 VM 的非高峰期安装 VM 的已评估补丁和适用补丁。

### <a name="rest-api"></a>REST API
使用 [Assess Patches](/rest/api/compute/virtual-machines/assess-patches) API 评估虚拟机的可用补丁。
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) cmdlet 评估虚拟机的可用补丁。

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
使用 [az vm assess-patches](/cli/azure/vm#az_vm_assess_patches) 评估虚拟机的可用补丁。

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="on-demand-patch-installation"></a>安装按需修补程序
若你已为 VM 启用了自动 VM 来宾修补，系统会于 VM 非高峰期，在 VM 上定期安装安全修补程序和关键补丁。 此过程是自动完成的，如本文档前面部分所述，你可通过 VM 实例视图查看最新安装结果。

此外，你还可随时为 VM 触发按需修补评估。 安装补丁可能需要几分钟才能完成，最新安装状态将在 VM 实例视图上更新。

你可以使用按需修补程序安装一种或多种修补程序的所有补丁。 你还可选择包含或排除 Linux 的特定包或 Windows 特定知识库 ID。 触发按需修补安装程序时，请确保在包含列表中至少指定一个补丁分类或至少一个补丁（Linux 特定包，Windows 知识库 ID）。

### <a name="rest-api"></a>REST API
使用 [Install Patches](/rest/api/compute/virtual-machines/install-patches) API 在虚拟机上安装补丁。

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/installPatches?api-version=2020-12-01`
```

Linux 请求正文示例：
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "linuxParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

Windows 请求正文示例：
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "windowsParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Invoke-AzVMInstallPatch](/powershell/module/az.compute/invoke-azvminstallpatch) cmdlet 在虚拟机上安装补丁。

在 Linux VM 上安装某些包的示例：
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Linux -ClassificationToIncludeForLinux "Security" -PackageNameMaskToInclude ["package123"] -PackageNameMaskToExclude ["package567"]
```

在 Windows VM 上安装所有关键补丁的示例：
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT2H" -RebootSetting "Never" -Windows   -ClassificationToIncludeForWindows Critical
```

在 Windows VM 上安装所有安全修补程序，同时包含和排除具有特定知识库 ID 的补丁，并排除需要重新启动的任何补丁，具体示例如下：
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Windows -ClassificationToIncludeForWindows "Security" -KBNumberToInclude ["KB1234567", "KB123567"] -KBNumberToExclude ["KB1234702", "KB1234802"] -ExcludeKBsRequiringReboot
```

### <a name="azure-cli"></a>Azure CLI
使用 [az vm install-patches](/cli/azure/vm#az_vm_install_patches)　在虚拟机上安装补丁。

在 Linux VM 上安装所有关键补丁的示例：
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-linux Critical
```

在 Windows VM 上安装所有关键补丁和安全修补程序，同时排除需要重新启动的任何补丁，示例如下：
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-win Critical Security --exclude-kbs-requiring-reboot true
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [详细了解如何创建和管理 Windows 虚拟机](./windows/tutorial-manage-vm.md)
