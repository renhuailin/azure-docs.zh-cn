---
title: 排查在 Azure Stack Edge Pro GPU 中预配虚拟机的问题 | Microsoft Docs
description: 介绍如何排查在 Azure Stack Edge Pro GPU 中预配新虚拟机时出现的问题。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: e04d3a0a5b7c48117af1f597606658a878a3a6ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740768"
---
# <a name="troubleshoot-vm-deployment-in-azure-stack-edge-pro-gpu"></a>排查在 Azure Stack Edge Pro GPU 中部署 VM 的问题

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何排查在 Azure Stack Edge Pro GPU 设备上部署虚拟机时出现的常见错误。 本文提供相关指导，助你调查导致 VM 预配超时的最常见问题，以及在创建网路接口和 VM 过程中出现的各种问题。

若要诊断 VM 预配失败，请查看故障虚拟机的来宾日志。 有关收集 VM 来宾日志并将其包含在支持包中的步骤，请参阅[在 Azure Stack Edge Pro 上收集 VM 的来宾日志](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)。

如需相关指导以在部署 VM 之前排查会导致 VM 映像上传失败的问题，请参阅[排查在 Azure Stack Edge Pro GPU 中上传虚拟机映像的问题](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md)。


## <a name="vm-provisioning-timeout"></a>VM 预配超时

本部分介绍如何排查导致 VM 预配超时的最常见原因。

当 VM 预配超时时，你会看到以下错误： 

![Azure Stack Edge 中的 VM 预配超时时在 Azure 门户中显示的错误的屏幕截图。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-provisioning-timeout-01.png) 

以下问题是导致 VM 预配超时的主要原因：
- 分配给 VM 的 IP 地址已在使用中。 [了解详细信息](#vm-provisioning-timeout)
- 用于部署 VM 的 VM 映像未经正确准备。 [了解详细信息](#vm-image-not-prepared-correctly)
- 无法从来宾 VM 访问默认网关和 DNS 服务器。 [了解详细信息](#gateway-dns-server-couldnt-be-reached-from-guest-vm)
- 在安装 `cloud init` 的过程中，`cloud init` 未运行或运行时出现问题。 （仅限 Linux VM）[了解详细信息](#cloud-init-issues-linux-vms)
- 对于使用自定义 VM 映像部署的 Linux VM，/etc/waagent.conf 文件中的预配标志不正确。 （仅限 Linux VM）[了解详细信息](#provisioning-flags-set-incorrectly-linux-vms)

### <a name="ip-assigned-to-the-vm-is-already-in-use"></a>分配给 VM 的 IP 已在使用中

错误说明：分配给 VM 的静态 IP 地址已在使用中，VM 预配失败。 如果部署 VM 的子网已在使用分配的 IP 地址，即会发生此错误。 通过 Azure 门户部署 VM 时，该过程会检查设备中现有的 IP 地址，但无法检查其他亦可能存在于子网中的服务或虚拟机的 IP 地址。 

建议解决方案：使用未在使用中的静态 IP 地址，或使用 DHCP 服务器提供的动态 IP 地址。

检查 IP 地址是否重复：

- 在使用同一网络的任何设备上运行以下 `ping` 和 Test-NetConnection (`tnc`) 命令：

  ```
  ping <IP address>
  tnc <IP address>
  tnc <IP address> -CommonTCPPort “RDP”
  ```

如果收到响应，则表示你分配给新 VM 的 IP 地址已在使用中。

### <a name="vm-image-not-prepared-correctly"></a>VM 映像未经正确准备

错误说明：若要准备将在 Azure Stack Edge Pro GPU 设备上使用的 VM 映像，请务必遵循特定工作流。 你必须在 Azure 中创建第 1 代虚拟机，自定义 VM，通用化 VHD，然后下载该虚拟机的 OS VHD。 准备使用的映像必须是第 1 代固定（类型）VHD，并且文件名扩展名为“vhd”。

有关要求的概述，请参阅[为 Azure Stack Edge Pro GPU 设备创建自定义 VM 映像](azure-stack-edge-gpu-create-virtual-machine-image.md)。 有关解决 VM 映像问题的指导，请参阅[排查在 Azure Stack Edge Pro GPU 中上传虚拟机映像的问题](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md)。  

建议解决方案：完成用于准备 VM 映像的工作流。 如需指导，请参阅以下文章之一：

* [适用于 Windows 和 Linux VM 的自定义 VM 映像工作流](azure-stack-edge-gpu-create-virtual-machine-image.md)
* [从 Windows VHD 准备通用化映像](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
* [使用 ISO 准备通用化映像](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
* [使用专用映像部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)


### <a name="gateway-dns-server-couldnt-be-reached-from-guest-vm"></a>无法从来宾 VM 访问网关和 DNS 服务器

错误说明：如果在 VM 部署期间无法访问默认网关和 DNS 服务器，则 VM 预配将超时，VM 部署将失败。

建议解决方案：验证是否可以从 VM 访问默认网关和 DNS 服务器。 然后再次部署 VM。

若要验证可以从 VM 访问默认网关和 DNS 服务器，请执行以下步骤：
1. [连接到 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm)。
2. 运行以下命令：

   ```
   ping <default gateway IP address>
   ping <DNS server IP address>
   ```

   若要查找默认网关和 DNS 服务器的 IP 地址，请转到设备的本地 UI。 选择相关端口，并查看网络设置。

   ![Azure Stack Edge 设备的“网络”页的屏幕截图，其中显示了端口 2 的网络设置。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gateway-dns-server-settings-01.png) 


### <a name="cloud-init-issues-linux-vms"></a>`cloud init` 问题 (Linux VM)

错误说明：`cloud init` 未运行，或者运行 `cloud init` 时出现问题。 `cloud-init` 可用于在首次启动 Linux VM 时自定义该 VM。 有关详细信息，请参阅 [Azure 中虚拟机的 cloud-init 支持](../virtual-machines/linux/using-cloud-init.md)。

建议解决方案：查找运行 `cloud init` 时发生的问题：
1. [连接到 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm)。
1. 检查以下日志文件中出现的 `cloud init` 错误：

   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent/log 

若要检查导致 `cloud init` 运行失败的最常见问题，请执行以下步骤：

1. 确保 VM 映像是基于 `cloud init` 创建。 运行以下命令：

   `cloud-init --version`

   该命令应返回 cloud init 版本号。 如果映像并非基于 `cloud init` 创建，则该命令将不会返回版本信息。

   若需帮助以获取 `cloud init` 选项，请运行以下命令：

   `cloud-init --help` 

2. 确保已将数据源设置为 Azure 的 `cloud init` 实例能够成功运行。 

   当数据源被设置为 Azure 时，cloud init 日志中的条目将如下所示。

   ![“数据源”设置为“Azure”的 VM 映像的 cloud-init 日志条目图示。 标识文本已突出显示。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/cloud-init-log-entry-01.png)

   若数据源未设置为 Azure，则可能需要修改 `cloud init` 脚本。 有关详细信息，请参阅[深入了解 cloud-init](../virtual-machines/linux/cloud-init-deep-dive.md)。


### <a name="provisioning-flags-set-incorrectly-linux-vms"></a>预配标志设置有误 (Linux VM)

错误说明：若要在 Azure 中成功部署 Linux VM，则必须在映像上禁用预配，且必须启用 `cloud init` 以进行预配。 并且，为标准 VM 映像正确配置设置这些值的预配标志。 如果使用自定义 VM 映像，则需要确保其正确无误。 

建议解决方案：确保 */etc/waagent.conf* 文件中的预配标志具有以下值：<!--Move details to "Create a custom VM image" when the 2 active PRs against that article have been merged. Not before Friday release.-->

   | 功能                      | 必需的值                |
   |---------------------------------|-------------------------------|
   | 启用设置             | `Provisioning.Enabled=n`      |
   | 依靠 cloud-init 进行预配 | `Provisioning.UseCloudInit=y` |


##  <a name="network-interface-creation-issues"></a>网络接口创建问题

本部分提供相关指导，助你排查在 VM 部署过程中导致网络接口创建失败的问题。


### <a name="nic-creation-timeout"></a>NIC 创建超时

错误说明：未在允许的超时期间内，在 VM 上成功创建网络接口。 此故障可能是由环境中的 DHCP 服务器问题所导致。 

若要验证是否已成功创建网络接口，请执行以下步骤：

1. 在 Azure 门户中，转到设备的 Azure Stack Edge 资源（前往“Edge 服务” > “虚拟机”）。 然后选择“部署”，并导航到 VM 部署。 

1. 如果未成功创建网络接口，你将看到以下错误。

   ![在 Azure Stack Edge 设备上部署 VM 期间创建网络接口失败时，Azure 门户中显示的错误的屏幕截图。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/nic-creation-failed-01.png)

建议的解决方案：再次创建 VM，并为其分配静态 IP 地址。


## <a name="vm-creation-issues"></a>VM 创建问题

本部分介绍创建 VM 期间发生的常见问题。

### <a name="not-enough-memory-to-create-the-vm"></a>内存不足，无法创建 VM

错误说明：若因内存不足而导致 VM 创建失败时，你将看到以下错误。
 
![在 Azure Stack Edge 设备上创建 VM 失败时，Azure 门户中显示的错误的屏幕截图。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-creation-failed-01.png)

建议的解决方案：检查设备上可用的内存，并根据 VM 大小做出相应选择。 有关详细信息，请参阅 [Azure Stack Edge 上支持的虚拟机大小](azure-stack-edge-gpu-virtual-machine-sizes.md)。

可用于部署 VM 的内存受以下多重因素约束：

- 设备上的可用内存容量。 有关详细信息，请参阅 [Azure Stack Edge Pro GPU 技术规范](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications) 和 [Azure Stack Edge Mini R 技术规范](azure-stack-edge-mini-r-technical-specifications-compliance.md#compute-memory)，了解相关计算和内存规范。

- 若启用 Kubernetes，则 Kubernetes 群集上的 Kubernetes 和应用程序需要占用计算内存。
- Hyper-V 中每个虚拟机的开销。

建议解决方案：

- 使用需要较少内存的 VM 大小。
- 在部署新 VM 之前，关闭门户中未使用的所有 VM。
- 删除不再使用的所有 VM。


### <a name="insufficient-number-of-gpus-to-create-gpu-vm"></a>GPU 数量不足，无法创建 GPU VM

若尝试在已启用 Kubernetes 的 GPU 设备上部署 VM，你将无法使用 GPU，VM 预配亦会失败并出现以下错误：

![因 Azure Stack Edge 设备上没有可用的 GPU 而导致 GPU VM 创建失败时，在 Azure 门户中显示的错误的屏幕截图。](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gpu-vm-creation-failed-01.png)

可能的原因：如果在创建 VM 之前已经启用 Kubernetes，则 Kubernetes 将会使用所有可用的 GPU，从而导致你无法创建与 GPU 同等大小的 VM。 你能够创建的与 GPU 同等大小的 VM 数同于可用的 GPU 数。 而你的 Azure Stack Edge 设备可以配备 1 或 2 个 GPU。

建议解决方案：如需了解已配置 Kubernetes 的 1 GPU 或 2 GPU 设备上的 VM 部署选项，请参阅 [GPU VM 和 Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes)。


## <a name="next-steps"></a>后续步骤

- [收集一个包含故障 VM 的来宾日志的支持包](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)<!--Does a failed VM have a guest log? Does it have GPU and memory metrics?-->
- [排查 GPU 扩展安装失败的问题](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
- [排查 Azure 资源管理器的问题](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)
