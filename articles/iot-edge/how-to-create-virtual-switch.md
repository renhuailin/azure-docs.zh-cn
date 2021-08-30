---
title: 创建 Azure IoT Edge for Linux on Windows 虚拟交换机 | Microsoft Docs
description: 用于创建 Azure IoT Edge for Linux on Windows 虚拟交换机的安装项
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 5db37717356cd06b257867615623e24a1c36a335
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778533"
---
# <a name="azure-iot-edge-for-linux-on-windows-virtual-switch-creation"></a>Azure IoT Edge for Linux on Windows 虚拟交换机的创建
Azure IoT Edge for Linux on Windows 使用主计算机上的虚拟交换机与虚拟机通信。 Windows 桌面版附带一个可使用的默认交换机，Windows Server 则没有。 你需要创建一个虚拟交换机，然后才能将 IoT Edge for Linux on Windows 部署到 Windows Server 设备。 此外，还可根据需要使用本指南创建自定义虚拟交换机。 

本文介绍如何按照以下步骤在 Windows 设备上创建虚拟交换机，来安装 IoT Edge for Linux on Windows：
- 创建虚拟交换机
- 创建 NAT 表
- 安装和设置 DHCP 服务器

## <a name="prerequisites"></a>先决条件
- 一台 Windows 设备。 如需了解支持的 Windows 版本，请参阅[操作系统](support.md#operating-systems)。
- Windows 设备上安装了 Hyper-V 角色。 要详细了解如何启用 Hyper-V，请参阅[在 Windows 设备上安装和预配 Azure IoT Edge for Linux](./how-to-install-iot-edge-on-windows.md?tabs=powershell#prerequisites)。

## <a name="create-virtual-switch"></a>创建虚拟交换机 
本部分中的以下步骤是用于创建虚拟交换机的通用指南。 确保虚拟交换机的配置与你的网络环境相符。

1. 在提升的会话中打开 PowerShell。

2. 检查 Windows 主机上的虚拟交换机，确保没有可用的虚拟交换机。 有关完整详细信息，请查看 [Get-VMSwitch (Hyper-V)](/powershell/module/hyper-v/get-vmswitch)。 

   ```powershell
   Get-VMSwitch
   ```

   如果已创建名为“默认交换机”的虚拟交换机，并且无需自定义虚拟交换机，则不用遵循本指南的后续步骤就能安装 IoT Edge for Linux on Windows。

3. 创建新的虚拟机 (VM) 交换机，并为其提供名称和类型（“内部”或“专用”） 。 若要创建外部虚拟交换机，请指定 NetAdapterInterfaceDescription 或 NetAdapterName 参数，这些参数可将虚拟交换机的类型隐式设置为“外部”   。 请查看 [New-VMSwitch (Hyper-V)](/powershell/module/hyper-v/new-vmswitch) 和[为 Hyper-V 虚拟机创建虚拟交换机](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines)，获取完整详细信息和进一步的说明。
   ```powershell
   New-VMSwitch -Name "{switchName}" -SwitchType {switchType}
   ```

4. 获取所创建的交换机的接口索引。 请查看 [Get-NetAdapter (NetAdapter)](/powershell/module/netadapter/get-netadapter)，获取完整的详细信息。 
   ```powershell
   (Get-NetAdapter -Name '*{switchName}*').ifIndex
   ```

5. 使用上一步中的接口索引获取所创建的交换机网络适配器的 IP 地址八位位组。 请查看 [Get-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/get-netipaddress)，获取完整的详细信息。 
   ```powershell
   Get-NetIPAddress -AddressFamily IPv4  -InterfaceIndex {ifIndex}
   ```

6. 使用前面步骤中的 IP 地址系列和接口索引，创建并设置新的网关 IP 地址。  例如，如果虚拟网络交换机适配器的 IPv4 地址为 xxx.xxx.xxx.yyy，可以将 gatewayIp 设置为 xxx.xxx.xxx.1。 请查看 [New-NetIPAddress (NetTCPIP)](/powershell/module/nettcpip/new-netipaddress)，获取完整的详细信息。
   ```powershell
   New-NetIPAddress -IPAddress {gatewayIp} -PrefixLength 24 -InterfaceIndex {ifIndex}
   ```

7. 创建一个网络地址转换 (NAT) 对象，用于将内部网络地址转换为外部网络。 使用前面步骤中的相同 IPv4 系列地址。 例如，如果虚拟网络交换机适配器的 IPv4 地址为 xxx.xxx.xxx.yyy，可以将 natIp 设置为 xxx.xxx.xxx.0。 请查看 [New-NetNat (NetNat)](/powershell/module/netnat/new-netnat)，获取完整的详细信息。 
   ```powershell
   New-NetNat -Name "{switchName}" -InternalIPInterfaceAddressPrefix "{natIp}/24"
   ```

## <a name="create-dhcp-server"></a>创建 DHCP 服务器 

>[!WARNING]
>要在公司网络环境中部署 DHCP 服务器，可能需要授权。 检查虚拟交换机配置是否符合公司网络策略。 有关详细信息，请参阅[使用 Windows PowerShell 部署 DHCP](/windows-server/networking/technologies/dhcp/dhcp-deploy-wps) 指南。 

1. 检查是否在设备中安装了 DHCP 服务器功能。 查看“安装状态”列。
   ```powershell
   Get-WindowsFeature -Name 'DHCP'
   ```

2. 如果未安装，请使用以下命令安装服务器。
   ```powershell
   Install-WindowsFeature -Name 'DHCP' -IncludeManagementTools
   ```

3. 将 DHCP 服务器添加到默认的本地安全组，然后重启服务器。
   ```powershell
   netsh dhcp add securitygroups
   Restart-Service dhcpserver
   ```

4. 配置 DHCP 服务器范围。 请查看 [Add-DhcpServerv4Scope (DhcpServer)](/powershell/module/dhcpserver/add-dhcpserverv4scope)，获取完整的详细信息。  IP 的 DHCP 服务器范围由 startIp 和 endIp 决定 。 例如，如果要让 100 个地址可用，并遵循步骤 5 中虚拟网络交换机适配器的 xxx.xxx.xxx.yyy IPv4 地址，则 startIp = xxx.xxx.xxx.100、endIp = xxx.xxx.xxx.200、subnetMask = 255.255.255.0。
   ```powershell
   Add-DhcpServerV4Scope -Name "AzureIoTEdgeScope" -StartRange {startIp} -EndRange {endIp} -SubnetMask {subnetMask} -State Active
   ```

5. 最后，将 NAT 对象和 gatewayIp 分配给 DHCP 服务器，并重启服务器以加载配置。
   ```powershell
   Set-DhcpServerV4OptionValue -ScopeID {natIp} -Router {gatewayIp}
   Restart-service dhcpserver
   ```

## <a name="next-steps"></a>后续步骤
按照[在 Windows 设备上安装并预配 Azure IoT Edge for Linux](how-to-install-iot-edge-on-windows.md) 中的步骤为设备安装 IoT Edge for Linux on Windows。