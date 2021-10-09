---
title: 排查 Azure Migrate 设备问题
description: 获取相关帮助信息来排查 Azure Migrate 设备可能会发生的问题。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: d6d87d7503a68091e72f1aa84703966ab28f32b5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654511"
---
# <a name="troubleshoot-the-azure-migrate-appliance"></a>排查 Azure Migrate 设备问题

本文有助于排查在部署 [Azure Migrate](migrate-services-overview.md) 设备以及使用设备发现本地服务器时遇到的问题。

## <a name="whats-supported"></a>支持的功能

[查看](migrate-appliance.md)设备支持要求。

## <a name="invalid-ovf-manifest-entry-error-occurs-during-appliance-setup"></a>设备安装过程中出现“OVF 清单条目无效”错误

使用 OVA 模板设置设备时，出现“提供的清单文件无效：OVF 清单条目无效”错误。

### <a name="remediation"></a>补救

1. 通过检查哈希值，验证是否已正确下载 Azure Migrate 设备 OVA 文件。 [了解详细信息](./tutorial-discover-vmware.md)。 如果哈希值不匹配，请重新下载 OVA 文件并重新尝试部署。
1. 如果部署仍然失败，并且使用的是 VMware vSphere 客户端部署 OVF 文件，则尝试通过 vSphere Web 客户端进行部署。 如果部署仍失败，请尝试使用其它 Web 浏览器。
1. 如果使用的是 vSphere Web 客户端并尝试在 vCenter Server 6.5 或 6.7 上部署，请尝试直接在 ESXi 主机上部署 OVA：
   - 使用 Web 客户端（ https://<主机 IP 地址>/ui）直接连接 ESXi 主机（而不是 vCenter Server）。
   - 在“主页” > “清单”中，选择“文件” > “部署 OVF 模板”。    浏览到 OVA，并完成部署。
1. 如果部署仍然失败，请联系 Azure Migrate 支持部门。

## <a name="connectivity-check-fails-during-the-prerequisites-setup"></a>在先决条件设置期间连接性检查失败

在设备上执行连接性检查时出现错误。

### <a name="remediation"></a>补救

1. 请确保可以从设备连接到所需的 [URL](./migrate-appliance.md#url-access)。
1. 检查是否有代理或防火墙阻止对这些 URL 的访问。 如果要求创建了允许列表，请确保列表中包含了所有 URL。
1. 如果在本地配置了代理服务器，请在同一步骤中选择“设置代理”，输入正确的代理详细信息。 如果代理需要授权凭据，请输入它们。
1. 请确保以前未使用服务器设置过[复制设备](./migrate-replication-appliance.md)或者未在服务器上安装移动服务代理。

## <a name="connectivity-check-fails-for-the-akams-url-during-the-prerequisites-setup"></a>在先决条件设置期间针对 aka.ms URL 的连接性检查失败

在设备上针对 aka.ms URL 的连接性检查出现错误。

### <a name="remediation"></a>补救

1. 确保已连接到 Internet 并已将 URL-aka.ms/* 添加到了允许列表，以便下载最新版本的服务。
1. 检查是否有代理或防火墙阻止对此 URL 的访问。 确保在配置管理器的先决条件步骤中提供了正确的代理详细信息。
1. 返回设备配置管理器，然后重新运行先决条件，以启动自动更新。
1. 如果重试不管用，可从[本网站](https://aka.ms/latestapplianceservices)下载 latestcomponents.json 文件，以检查发生失败的服务是否是最新版本。 从该文件中的下载链接手动更新它们。

 如果你已为专用终结点连接启用了设备，并且不希望允许通过 Internet 访问此 URL，则可[禁用自动更新](./migrate-appliance.md#turn-off-auto-update)，因为此服务需要 aka.ms 链接。

 >[!Note]
 >如果禁用自动更新服务，则设备上运行的服务将不会自动获取最新更新。 若要解决此问题，请[手动更新设备服务](./migrate-appliance.md#manually-update-an-older-version)。

## <a name="auto-update-check-fails-during-the-prerequisites-setup"></a>在先决条件设置期间自动更新检查失败

设备上的自动更新检查出现错误。

### <a name="remediation"></a>补救

1. 确保为[所需 URL](./migrate-appliance.md#url-access) 创建了允许列表，并且没有任何代理或防火墙设置阻止这些 URL。
1. 如果任何设备组件的更新失败，请重新运行先决条件或[手动更新设备服务](./migrate-appliance.md#manually-update-an-older-version)。

## <a name="time-sync-check-fails-during-the-prerequisites-setup"></a>在先决条件设置期间，时间同步检查失败

有关时间同步的错误指示服务器时钟可能与当前时间不同步，且时间差超过五分钟。

### <a name="remediation"></a>补救

- 在控制面板中检查日期和时间设置，确保设备服务器时间与 Internet 时间同步。
- 也可以执行以下步骤来更改设备服务器上的时钟时间，使之与当前时间匹配：
     1. 在服务器上打开管理员命令提示符。
     1. 若要检查时区，请运行 w32tm /tz。
     1. 若要同步时间，请运行 w32tm /resync。

## <a name="vddk-check-fails-during-the-prerequisites-setup-on-the-vmware-appliance"></a>在 VMware 设备上设置先决条件期间，VDDK 检查失败

虚拟磁盘开发工具包 (VDDK) 检查失败，因为在设备上找不到所需的 VDDK。 此问题会导致正在进行的复制失败。

### <a name="remediation"></a>补救

1. 确保已下载 VDDK 6.7，并已将其文件复制到设备服务器上的 C:\Program Files\VMware\VMware Virtual Disk Development Kit。
1. 确保设备上没有其他软件或应用程序在使用其他版本的 VDDK。

## <a name="project-key-related-error-occurs-during-appliance-registration"></a>在设备注册期间出现与项目密钥相关的错误

 尝试使用从项目复制的 Azure Migrate 项目密钥注册设备时出现问题。

### <a name="remediation"></a>补救

1. 确保你已从项目中复制了正确的密钥。 在项目中的“Azure Migrate: 发现与评估”卡上，选择“发现” 。 然后选择步骤 1 中的“管理现有设备”。 从下拉菜单中选择你以前为其生成密钥的设备名称。 复制相应的密钥。
1. 确保将密钥粘贴到具有正确云类型（公有/美国政府）和设备类型（VMware/Hyper-V/物理或其他）的设备 。 查看设备配置管理器顶部的信息，确认云和方案类型。

## <a name="failed-to-connect-to-the-azure-migrate-project-error-occurs-during-appliance-registration"></a>在设备注册期间出现“未连接到 Azure Migrate 项目”错误

使用 Azure 用户帐户成功登录后，设备注册步骤失败，并显示消息：“未连接到 Azure Migrate 项目。 请查看错误详情并单击‘重试’”，执行补救步骤。

当用于从设备配置管理器登录的 Azure 用户帐户与用于在门户中生成 Azure Migrate 项目密钥的用户帐户不同时，就会发生此问题。

### <a name="remediation"></a>补救

可以使用两个选项：

- 若要完成设备注册，请使用在门户中生成 Azure Migrate 项目密钥的同一 Azure 用户帐户。
- 还可以将所需的角色和[权限](./tutorial-discover-vmware.md#prepare-an-azure-user-account)分配给其他用于设备注册的 Azure 用户帐户。

## <a name="azure-active-directory-aad-operation-failed-with-status-forbidden-error-occurs-during-appliance-registration"></a>在设备注册期间出现“Azure Active Directory (AAD) 操作失败，且状态为禁止”错误

由于 Azure Active Directory 权限不足，无法完成注册，并出现“Azure Active Directory (AAD) 操作失败，且状态为禁止”错误。

### <a name="remediation"></a>补救

确保你具有所需的[权限](./tutorial-discover-vmware.md#prepare-an-azure-user-account)，以在 Azure 中创建和管理 Azure Active Directory 应用程序。 你应具有“应用程序开发人员”角色或在租户级别支持“用户可以注册应用程序”的用户角色。

## <a name="forbidden-to-access-key-vault-error-occurs-during-appliance-registration"></a>在设备注册期间出现“禁止访问密钥保管库”错误

由于发生错误“{KeyVaultErrorMessage}”，所以“{KeyVaultName}”的 Azure 密钥保管库创建或更新操作失败。

当用于注册设备的 Azure 用户帐户与用于在门户中生成 Azure Migrate 项目密钥的帐户（即在创建密钥保管库时）不同时，通常会发生这种情况。

### <a name="remediation"></a>补救

1. 确保设备上当前登录的用户帐户对错误消息中提到的密钥保管库拥有所需的权限。 用户帐户需拥有[此网站](./tutorial-discover-vmware.md#prepare-an-azure-user-account)中提及的权限。
1. 转到该密钥保管库，确保用户帐户具有访问策略，且该“密钥保管库访问策略”下分配了所有的“密钥”、“机密”和“证书”权限   。 [了解详细信息](../key-vault/general/assign-access-policy-portal.md)。
1. 如果已经为专用终结点连接启用了设备，请确保设备托管在创建密钥保管库的同一虚拟网络中，或连接到通过专用链接创建密钥保管库的 Azure 虚拟网络。 确保可从设备解析密钥保管库专用链接。 转到“Azure Migrate：发现和评估” > “属性”，找到资源的专用终结点详细信息，例如在创建 Azure Migrate 密钥期间创建的密钥保管库 。 [了解详细信息](./troubleshoot-network-connectivity.md)。
1. 如果你拥有所需的权限并已建立连接，请稍后在设备上重试注册。

## <a name="unable-to-connect-to-vcenter-server-during-validation"></a>在验证期间无法连接到 vCenter Server

如果出现此连接错误，可能无法连接到 vCenter Server Servername.com:9443。 错误详情表明，在可接受消息的 `https://\*servername*.com:9443/sdk` 没有终结点在侦听。

### <a name="remediation"></a>补救

- 检查是否正在运行最新版本的设备。 如果不是，请将设备升级到[最新版本](./migrate-appliance.md)。
- 如果使用最新版本后仍出现此问题，则设备可能无法解析指定的 vCenter Server 名称，或者指定的端口可能是错误的。 默认情况下，如果端口未指定，收集器会尝试连接到端口号 443。

    1. 在设备中对 Servername.com 执行 ping 操作。
    1. 如果步骤 1 失败，请尝试使用 IP 地址连接到 vCenter Server。
    1. 确定连接到 vCenter Server 的端口号是否正确。
    1. 验证 vCenter Server 是否已启动并正在运行。

## <a name="server-credentials-domain-fails-validation-on-the-vmware-appliance"></a>VMware 设备上的服务器凭据（域）验证失败

在 VMware 设备上添加的用于执行软件清单和无代理依赖项分析的域凭据出现“验证失败”的情况。

### <a name="remediation"></a>补救

1. 检查是否提供了正确的域名和凭据。
1. 确保可从设备访问域以验证凭据。 设备可能存在视线问题，或者从设备服务器可能无法解析域名。
1. 选择“编辑”更新域名或凭据。 选择“重新验证凭据”，稍后可再次验证凭据。

## <a name="access-is-denied-error-occurs-when-you-connect-to-hyper-v-hosts-or-clusters-during-validation"></a>在验证期间连接到 Hyper-V 主机或群集时出现“拒绝访问”错误

由于出现错误“拒绝访问”，所以无法验证添加的 Hyper-V 主机或群集。

### <a name="remediation"></a>补救

1. 确保满足所有 [Hyper-V 主机的先决条件](./migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)。 
1. 请在[本网站](./tutorial-discover-hyper-v.md#prepare-hyper-v-hosts)上查看步骤，了解如何使用预配的 PowerShell 脚本准备 Hyper-V 主机或手动准备。

## <a name="the-server-does-not-support-ws-management-identify-operations-error-occurs-during-validation"></a>在验证期间出现“服务器不支持 WS-Management 标识操作”错误

由于出现错误“服务器不支持 WS-Management 标识操作，所以无法在设备上验证 Hyper-V 群集。 请跳过请求的 TestConnection 部分，然后重试”，无法验证设备上的 Hyper-V 群集。

### <a name="remediation"></a>补救

如果在设备上提供了代理配置，则通常会出现此错误。 即使提供了节点的 FQDN，设备也会使用群集节点的短名称连接到群集。 将群集节点的短名称添加到设备上的绕过代理列表，即可解决该问题，并成功验证 Hyper-V 群集。

## <a name="cant-connect-to-host-or-cluster-error-occurs-during-validation-on-a-hyper-v-appliance"></a>在一个拥有 Hyper-V 的设备的验证期间出现“无法连接到主机或群集”错误

因为无法解析服务器名称，所以出现错误“无法连接到主机或群集”。 可能出现错误 50004：“无法连接到主机或群集，因为无法解析服务器名称。WinRM 错误代码：0x803381B9”。

出现此问题，通常是由于添加了无法通过 DNS 解析的主机的 IP 地址。 可能还会在群集的主机中看到此错误。 这表明设备可以连接到群集，但群集返回的主机名不是 FQDN。

### <a name="remediation"></a>补救

若要解决此错误，请添加 IP 地址和主机名的映射来更新设备上的 hosts 文件。
1. 以管理员身份打开记事本。
1. 打开 C:\Windows\System32\Drivers\etc\hosts 文件。
1. 在一行中添加 IP 地址和主机名。 对于出现此错误的每个主机或群集重复此操作。
1. 保存然后关闭 hosts 文件。
1. 使用设备管理应用检查设备是否可以连接到主机。 30 分钟后，应该可在 Azure 门户中看到这些主机的最新信息。

## <a name="unable-to-connect-to-server-error-occurs-during-validation-of-physical-servers"></a>在验证物理服务器期间出现“无法连接到服务器”错误

### <a name="remediation"></a>补救

- 确保设备已连接到目标服务器。
- 如果它是 Linux 服务器，请通过以下步骤确保启用基于密码的身份验证：
    1. 登录 Linux 服务器，并使用命令“vi /etc/ssh/sshd_config”打开 SSH 配置文件。
    1. 将“PasswordAuthentication”选项设置为“是”。 保存文件。
    1. 运行“service sshd restart”来重启 SSH 服务。
- 如果它是 Windows 服务器，请确保端口 5985 处于打开状态，允许远程 WMI 调用。
- 如果你正在发现 GCP Linux 服务器并正在使用根用户，请使用以下命令更改 root 登录的默认设置：
    1. 登录 Linux 服务器，并使用命令“vi /etc/ssh/sshd_config”打开 SSH 配置文件。
    1. 将“PermitRootLogin”选项设置为“是”。
    1. 运行“service sshd restart”来重启 SSH 服务。

## <a name="failed-to-fetch-bios-guid-error-occurs-for-the-server-during-validation"></a>在验证期间服务器出现“BIOS GUID 提取失败”错误

设备上的物理服务器验证失败，并显示错误消息“BIOS GUID 提取失败”。

### <a name="remediation"></a>补救

**Linux 服务器：**

连接到验证失败的目标服务器。 运行以下命令，查看它是否返回服务器的 BIOS GUID：

````
cat /sys/class/dmi/id/product_uuid
dmidecode | grep -i uuid | awk '{print $2}'
````
还可以通过以下命令与目标 Linux 服务器建立 SSH 连接，然后在设备服务器上的命令提示符中运行上述命令：
````
ssh <username>@<servername>
````

**Windows 服务器：**

在设备服务器上的 PowerShell 中，针对验证失败的目标服务器运行以下代码，查看它是否返回服务器的 BIOS GUID：

````
[CmdletBinding()]
Param(
  [Parameter(Mandatory=$True,Position=1)]
   [string]$Hostname
)
$HostNS = "root\cimv2"
$error.Clear()

$Cred = Get-Credential
$Session = New-CimSession -Credential $Cred -ComputerName $Hostname

if ($Session -eq $null -or $Session.TestConnection() -eq $false)
{
    Write-Host "Connection failed with $Hostname due to $error"
    exit -1
}

Write-Host "Connection established with $Hostname"
#Get-WmiObject -Query "select uuid from Win32_ComputerSystemProduct" 

$HostIntance = $Session.QueryInstances($HostNS, "WQL", "Select UUID from Win32_ComputerSystemProduct")
$HostIntance | fl *
````

运行上述代码时，需提供目标服务器的主机名。 它可以是 IP 地址/FQDN/主机名。 然后，系统会提示你提供用于连接该服务器的凭据。

## <a name="no-suitable-authentication-method-found-error-occurs-for-the-server-during-validation"></a>在验证期间，服务器出现“找不到合适的身份验证方法”错误

在尝试通过物理设备验证 Linux 服务器时，出现错误“找不到合适的身份验证方法”。

### <a name="remediation"></a>补救

通过以下步骤确保在 Linux 服务器上启用基于密码的身份验证：

1. 登录到 Linux 服务器。 使用命令“vi /etc/ssh/sshd_config”打开 SSH 配置文件。
1. 将“PasswordAuthentication”选项设置为“是” 。 保存文件。
1. 运行“service sshd restart”来重启 SSH 服务。

## <a name="access-is-denied-error-occurs-when-you-connect-to-physical-servers-during-validation"></a>在验证期间连接到物理服务器时出现“拒绝访问”错误

出现错误“WS-Management 服务无法处理请求”。 尝试通过物理设备验证 Windows 服务器时出现“WMI 服务返回访问被拒绝错误“。

### <a name="remediation"></a>补救

- 如果出现此错误，请确保已将设备配置管理器上提供的用户帐户（域/本地）添加到以下组：“远程管理用户”、“性能监视器用户”和“性能日志用户”。
- 如果远程管理用户组不存在，请将用户帐户添加到 WinRMRemoteWMIUsers_ 组。
- 还可以在目标服务器的命令提示符中运行以下命令，检查在服务器上是否启用了 WS-Management 协议：`winrm qc`
- 如果仍然出现该问题，请确保用户帐户有权访问 WMI 控制面板中的 CIMV2 命名空间和子命名空间。 可按照以下步骤设置访问权限：

    1. 转到设备上验证失败的服务器。
    1. 在“开始”菜单中搜索并选择“运行” 。 在“运行”对话框的“打开”文本框中，键入“wmimgmt.msc”，然后选择“进入”   。
    1. 此时会打开 wmimgmt 控制台，然后可在左侧窗格中找到“WMI 控件(本地)”。 右键单击它，然后从菜单中选择“属性”。
    1. 在“WMI 控件(本地)属性”对话框中，选择“安全性”选项卡 。
    1. 在“安全性”选项卡上，展开命名空间树中的“Root”文件夹，并选择“cimv2”命名空间  。
    1. 选择“安全性”，然后打开“ROOT\cimv2 的安全性”对话框 。
    1. 在“组或用户名”部分下，选择“添加”以打开“选择用户、计算机、服务帐户或组”对话框  。
    1. 搜索用户帐户并将其选中，然后选择“确定”返回到“ROOT\cimv2 的安全性”对话框 。
    1. 在“组或用户名”部分中，选择刚刚添加的用户帐户。 检查是否允许以下权限：<br/>
       - 启用帐户 <br/>
       - 远程启用
    1. 选择“应用”以启用该用户帐户的权限设置。

- 这些步骤也同样适用于非域/工作组服务器的本地用户帐户。 在某些情况下，[UAC](/windows/win32/wmisdk/user-account-control-and-wmi) 筛选可能会阻止某些 WMI 属性（因为命令是以标准用户的身份运行的），因此你可以使用本地管理员帐户或禁用 UAC，使得本地用户帐户不会被筛选，从而让自己成为拥有完全权限的管理员。
- 不建议通过更改控制远程 UAC 的注册表项来禁用远程 UAC，但在工作组中可能需要这样做。 该注册表项为 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system\LocalAccountTokenFilterPolicy。 如果此项的值为零 (0)，则会启用远程 UAC 访问令牌筛选。 如果值为 1，则会禁用远程 UAC。

## <a name="appliance-is-disconnected"></a>设备已断开连接

尝试从门户在几个 VMware 服务器上启用复制时，出现“设备已断开连接”错误消息。

如果设备处于关闭状态，或者设备上的 DRA 服务无法与 Azure 通信，则会发生这种错误。

### <a name="remediation"></a>补救

 1. 请转到设备配置管理器并重新运行先决条件，以查看“查看设备服务”下 DRA 服务的状态。 
 1. 如果该服务未运行，请在命令提示符下使用以下命令来停止再重启该服务：

    ````
    net stop dra
    net start dra
    ````

## <a name="next-steps"></a>后续步骤

为 [VMware](how-to-set-up-appliance-vmware.md)、[Hyper-V](how-to-set-up-appliance-hyper-v.md) 或[物理服务器](how-to-set-up-appliance-physical.md)设置设备。
