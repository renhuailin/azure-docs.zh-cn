---
title: Azure VM 的自动 VM 来宾修补
description: 了解如何自动修补 Azure 中的虚拟机。
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679167"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>预览：Azure VM 的自动 VM 来宾修补

为 Azure VM 启用自动 VM 来宾修补有助于通过安全自动修补虚拟机来简化更新管理，使虚拟机保持安全合规。

自动 VM 来宾修补具有以下特征：
- 在 VM 上自动下载并应用已分类为“关键”或“安全”的补丁。 
- 在 VM 所在时区的非高峰期应用补丁。
- 由 Azure 管理补丁业务流程，遵循[可用性优先原则](#availability-first-patching)应用补丁。
- 监视虚拟机运行状况（通过平台运行状况信号来确定），以检测修补失败的情况。
- 适用于所有大小的 VM。

> [!IMPORTANT]
> 自动 VM 来宾修补目前为公共预览版。 需要执行一个选用过程才能使用下述公共预览版功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-does-automatic-vm-guest-patching-work"></a>自动 VM 来宾修补的工作原理是什么？

如果在某个 VM 上启用了自动 VM 来宾修补，则会在该 VM 上自动下载并应用可用的关键补丁和安全补丁 。 在每个月发布新的补丁时，此过程将自动启动。 补丁评估和安装是自动的，该过程会根据需要重新启动 VM。

VM 每隔数天定期评估，并在任意 30 天周期内多次评估，以确定适用于该 VM 的补丁。 可在任何一天的 VM 非高峰期将补丁安装到 VM 上。 这种自动评估确保能够尽早发现任何缺失的补丁。

在发布每月补丁后的 30 天内，将按照下面所述的可用性优先业务流程安装补丁。 只会根据 VM 的时区在 VM 的非高峰期安装补丁。 VM 在非高峰期必须正在运行，才能自动安装补丁。 如果 VM 在定期评估期间已关闭，则会自动评估 VM，并在 VM 打开后的下一次定期评估（通常在几天之内）期间自动安装适用的补丁。

定义更新以及未分类为“关键”或“安全”的其他补丁不会通过自动 VM 来宾修补予以安装 。 若要安装属于其他分类的补丁或者要计划在你自己的自定义维护时段内安装补丁，可以使用[更新管理](./windows/tutorial-config-management.md#manage-windows-updates)。

### <a name="availability-first-patching"></a>可用性优先修补

Azure 将为启用了自动 VM 来宾修补功能的所有 VM 全局协调补丁安装过程。 此业务流程根据 Azure 提供的不同可用性级别遵循可用性优先原则。

对于正在更新的一组虚拟机，Azure 平台将协调更新：

跨区域：
- 按照分阶段的方式在整个 Azure 中全局协调每月更新，以防全局部署失败。
- 一个阶段可以涵盖一个或多个区域，仅当某个阶段中符合条件的 VM 成功更新时，更新才会进入下一阶段。
- 地理配对的区域不会并发更新，因此它们不能处于同一区域阶段。
- 更新是否成功是通过跟踪 VM 在更新后的运行状况来衡量的。 VM 运行状况是通过 VM 的平台运行状况指示器跟踪的。

在区域内部：
- 位于不同可用性区域中的 VM 不会并发更新。
- 不在可用性集中的 VM 将按照尽力而为的原则进行批处理，以避免对订阅中的所有 VM 进行并发更新。

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
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>补丁业务流程模式
Azure 上的 VM 现在支持以下补丁业务流程模式：

AutomaticByPlatform：
- Linux 和 Windows VM 都支持此模式。
- 此模式为虚拟机启用自动 VM 来宾修补，后续的补丁安装将由 Azure 协调。
- 可用性优先修补需要此模式。
- 只有使用上述受支持 OS 平台映像创建的 VM 才支持此模式。
- 对于 Windows VM，设置此模式还会禁用 Windows 虚拟机上的本机自动更新，以避免重复。
- 若要在 Linux VM 上使用此模式，请在 VM 模板中设置属性 `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform`。
- 若要在 Windows VM 上使用此模式，请设置属性 `osProfile.windowsConfiguration.enableAutomaticUpdates=true`，并在 VM 模板中设置属性 `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform`。

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
>对于 Windows VM，目前只能在首次创建 VM 时设置属性 `osProfile.windowsConfiguration.enableAutomaticUpdates`。 目前不支持从手动模式切换为自动模式，或者从自动模式切换为手动模式。 支持从 AutomaticByOS 模式切换为 AutomaticByPlatfom 模式。

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>启用自动 VM 来宾修补的要求

- 虚拟机上必须装有适用于 [Windows](./extensions/agent-windows.md) 或 [Linux](./extensions/agent-linux.md) 的 Azure VM 代理。
- 对于 Linux VM，Azure Linux 代理的版本必须是 2.2.53.1 或更高。 如果当前版本低于所需的版本，请[更新 Linux 代理](./extensions/update-linux-agent.md)。
- 对于 Windows VM，Windows 更新服务必须在虚拟机上运行。
- 虚拟机必须能够访问配置的更新终结点。 如果虚拟机配置为使用适用于 Linux 的专用存储库或适用于 Windows VM 的 Windows Server Update Services (WSUS)，则相关的更新终结点必须可访问。
- 使用计算 API 版本 2020-12-01 或更高版本。 计算 API 版本 2020-06-01 可用于 Windows VM，但其功能受限。

启用预览版功能需要为每个订阅一次性选用 InGuestAutoPatchVMPreview 和 InGuestPatchVMPreview 功能，如下一部分中所述 。

### <a name="rest-api"></a>REST API
以下示例说明如何为订阅启用预览版：

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为订阅启用预览版。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az feature register](/cli/azure/feature#az-feature-register) 为订阅启用预览版。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>启用自动 VM 来宾修补
若要在 Windows VM 上启用自动 VM 来宾修补，请确保在 VM 模板定义中将属性 osProfile.windowsConfiguration.enableAutomaticUpdates 设置为 true 。 只能在创建 VM 时设置此属性。 此附加属性不适用于 Linux VM。

### <a name="rest-api-for-linux-vms"></a>适用于 Linux VM 的 REST API
以下示例说明如何启用自动 VM 来宾修补：

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
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
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
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
创建新的 VM 时，使用 [az vm create](/cli/azure/vm#az-vm-create) 启用自动 VM 来宾修补。 以下示例为名为 myResourceGroup 的资源组中名为 myVM 的 VM 配置自动 VM 来宾修补 ：

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

若要修改现有的 VM，请使用 [az vm update](/cli/azure/vm#az-vm-update)

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
- 对于 RHEL，需要手动禁用自动更新（这是预览版中的一项限制）。 执行：

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
使用 [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) 访问 VM 的实例视图。

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>了解 VM 的修补状态

实例视图响应的 `patchStatus` 节提供有关 VM 的最新评估以及上次补丁安装的详细信息。

可在 `availablePatchSummary` 节下查看 VM 的评估结果。 将定期为启用了自动 VM 来宾修补的 VM 执行评估。 评估后的可用补丁计数将在 `criticalAndSecurityPatchCount` 和 `otherPatchCount` 结果下提供。 自动 VM 来宾修补将安装属于“关键”和“安全”补丁分类的所有已评估补丁 。 将跳过任何其他已评估的补丁。

可在 `lastPatchInstallationSummary` 节下查看 VM 的补丁安装结果。 此节提供有关上次在 VM 上尝试的补丁安装的详细信息，包括已安装的、挂起的、失败的或已跳过的补丁数量。 只会在 VM 的非高峰期维护时段安装补丁。 在下一个非高峰维护时段，将自动重试挂起的和失败的补丁。

## <a name="on-demand-patch-assessment"></a>按需补丁评估
如果已经为 VM 启用了自动 VM 来宾修补，则会在 VM 的非高峰期在 VM 上定期执行补丁评估。 此过程是自动完成的，可以按照本文档前面所述，通过 VM 的实例视图查看最新评估的结果。 此外，可以随时为 VM 触发按需补丁评估。 补丁评估可能需要几分钟才能完成，最新评估的状态将在 VM 的实例视图上更新。

启用预览版功能需要为每个订阅一次性选用 InGuestPatchVMPreview 功能。 此功能预览版不同于以前针对 InGuestAutoPatchVMPreview 执行的自动 VM 来宾修补功能注册。 启用额外的功能预览版是单独的一项附加要求。 可按照前面所述的适用于自动 VM 来宾修补的[预览版启用过程](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching)来启用按需补丁评估功能预览版。

> [!NOTE]
>按需补丁评估不会自动触发补丁安装。 如果你已启用自动 VM 来宾修补，则会遵循本文档前面所述的可用性优先修补过程，在 VM 的非高峰期安装 VM 的已评估补丁和适用补丁。

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) cmdlet 评估虚拟机的可用补丁。

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
使用 [az vm assess-patches](/cli/azure/vm#az-vm-assess-patches) 评估虚拟机的可用补丁。

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [详细了解如何创建和管理 Windows 虚拟机](./windows/tutorial-manage-vm.md)
