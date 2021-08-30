---
title: 排查 Azure Migrate 设备问题
description: 获取相关帮助信息来排查 Azure Migrate 设备可能会发生的问题。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 405976fdf418c02172c3ad8129d206c3313380e2
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183568"
---
# <a name="troubleshoot-the-azure-migrate-appliance"></a>排查 Azure Migrate 设备问题

本文将帮助你排查在部署 [Azure Migrate](migrate-services-overview.md) 设备以及使用设备发现本地服务器时遇到的问题。

## <a name="whats-supported"></a>支持的功能

[查看](migrate-appliance.md)设备支持要求。

## <a name="invalid-ovf-manifest-entry-during-appliance-set-up"></a>在设备设置期间出现“OVF 清单条目无效”

**错误**

使用 OVA 模板设置设备时，出现错误“提供的清单文件无效: OVF 清单条目无效”。

**修正**

1. 通过检查哈希值，验证是否已正确下载 Azure Migrate 设备 OVA 文件。 [了解详细信息](./tutorial-discover-vmware.md)。 如果哈希值不匹配，请重新下载 OVA 文件并重新尝试部署。
2. 如果部署仍然失败，并且是使用 VMware vSphere 客户端部署 OVF 文件，请尝试通过 vSphere Web 客户端进行部署。 如果部署仍失败，请尝试使用其它 Web 浏览器。
3. 如果使用的是 vSphere Web 客户端并尝试在 vCenter Server 6.5 或 6.7 上部署，请尝试直接在 ESXi 主机上部署 OVA：
   - 使用 Web 客户端（ https://<主机 IP 地址>/ui）直接连接 ESXi 主机（而不是 vCenter Server）。
   - 在“主页” > “清单”中，选择“文件” > “部署 OVF 模板”。    浏览到 OVA，并完成部署。
4. 如果部署仍然失败，请联系 Azure Migrate 支持部门。

## <a name="connectivity-check-failing-during-set-up-prerequisites"></a>在“设置必备组件”期间连接检查失败

**错误**

在设备上执行连接检查时出现错误。

**修正**

1. 请确保可以从设备连接到所需的 [URL](./migrate-appliance.md#url-access)。
1. 检查是否存在代理或防火墙阻止对这些 URL 的访问。 如果需要创建允许列表，请确保包括所有 URL。
1. 如果在本地配置了代理服务器，请确保在同一步骤中选择“设置代理”，以正确提供代理详细信息。 请确保提供代理所需的授权凭据。
1. 请确保以前未使用服务器设置[复制设备](./migrate-replication-appliance.md)，也未在服务器上安装移动服务代理。

## <a name="connectivity-check-failing-for-akams-url-during-set-up-prerequisites"></a>在“设置必备组件”期间针对 aka.ms URL 的连接检查失败

**错误**

在设备上针对 aka.ms URL 执行连接检查时出现错误。

**修正**

1. 确保已连接到 Internet 并已将 URL-aka.ms/* 加入允许列表，以便能够下载最新版本的服务。
2. 检查是否有代理/防火墙阻止了对此 URL 的访问。 确保在配置管理器的先决条件步骤中正确提供了代理详细信息。
3. 可以返回设备配置管理器，然后重新运行先决条件检查以启动自动更新。
3. 如果重试不起作用，可从[此处](https://aka.ms/latestapplianceservices)下载 latestcomponents.json 文件以检查发生失败的服务的最新版本，并通过该文件中的下载链接手动更新这些服务。

 如果你已经为设备启用了专用终结点连接，并且不希望允许通过 Internet 访问此 URL，则可以[禁用自动更新](./migrate-appliance.md#turn-off-auto-update)，因为此服务需要 aka.ms 链接。

 >[!Note]
 >如果禁用自动更新服务，则设备上运行的服务不会自动获取最新更新。 若要解决此问题，请[手动更新设备服务](./migrate-appliance.md#manually-update-an-older-version)。

## <a name="auto-update-check-failing-during-set-up-prerequisites"></a>在“设置必备组件”期间自动更新检查失败

**错误**

在设备上执行自动更新检查时出现错误。

**修正**

1. 确保为[所需 URL](./migrate-appliance.md#url-access) 创建了允许列表，并且没有任何代理或防火墙设置阻止这些 URL。
1. 如果任何设备组件的更新失败，请重新运行先决条件或[手动更新设备服务](./migrate-appliance.md#manually-update-an-older-version)。

## <a name="time-sync-check-failing-during-set-up-prerequisites"></a>在“设置必备组件”期间时间同步检查失败

**错误**

有关时间同步的错误指示服务器时钟可能与当前时间不同步，且时间差超过五分钟。

**修正**

- 在控制面板中检查日期和时间设置，确保设备服务器时间与 Internet 时间同步。
- 也可以执行以下步骤来更改设备服务器上的时钟时间，使之与当前时间匹配：
     1. 在服务器上打开管理员命令提示符。
     2. 若要检查时区，请运行 w32tm /tz。
     3. 若要同步时间，请运行 w32tm /resync。

## <a name="vddk-check-failing-during-set-up-prerequisites-on-vmware-appliance"></a>在 VMware 设备上“设置必备组件”期间 VDDK 检查失败

**错误**

由于设备找不到必须在其上安装的 VDDK 工具包，VDDK 检查失败。 这可能会导致进行中的复制失败。

**修正**

1. 确保已下载 VDDK 工具包 6.7，并已将其文件复制到设备服务器上的 C:\Program Files\VMware\VMware Virtual Disk Development Kit。
2. 确保没有任何其他软件或应用程序正在使用该设备上的其他 VDDK 工具包版本。

## <a name="getting-project-key-related-error-during-appliance-registration"></a>在设备注册期间出现与项目密钥相关的错误

**错误** 

尝试使用从项目复制的 Azure Migrate 项目密钥注册设备时出现问题。

**修正**

1. 确保从项目中复制了正确的密钥：在项目中的“Azure Migrate: 发现和评估”卡上选择“发现”，然后选择步骤 1 中的“管理现有设备”  。 从下拉菜单中选择设备名称（前面已为其生成了密钥），然后复制相应的密钥。
2. 确保将密钥粘贴到具有正确云类型（公有/美国政府）和设备类型（VMware/Hyper-V/物理或其他）的设备 。 查看设备配置管理器顶部的信息，确认云和方案类型。

## <a name="failed-to-connect-to-the-azure-migrate-project-during-appliance-registration"></a>在设备注册期间出现“未能连接到 Azure Migrate 项目”

**错误**

使用 Azure 用户帐户成功登录后，设备注册步骤失败并出现消息“未能连接到 Azure Migrate 项目。请查看错误详细信息，并在单击‘重试’后执行修正步骤”。

如果用于从设备配置管理器登录的 Azure 用户帐户与用于在门户中生成 Azure Migrate 项目密钥的用户帐户不同，就会发生此问题。

**修正**
1. 若要完成设备注册，请使用在门户中生成 Azure Migrate 项目密钥的同一 Azure 用户帐户，或者
1. 将所需的角色和[权限](./tutorial-discover-vmware.md#prepare-an-azure-user-account)分配给用于设备注册的其他 Azure 用户帐户

## <a name="azure-active-directory-aad-operation-failed-with-status-forbidden-during-appliance-registration"></a>在设备注册期间出现“Azure Active Directory (AAD) 操作失败，状态为‘禁止’”

**错误**

由于 AAD 权限不足，无法完成注册并出现错误“Azure Active Directory (AAD) 操作失败，状态为‘禁止’”。

**修正**

确保具有在 Azure 中创建和管理 AAD 应用程序所需的[权限](./tutorial-discover-vmware.md#prepare-an-azure-user-account)。 你应具有“应用程序开发人员”角色或在租户级别支持“用户可以注册应用程序”的用户角色 。

## <a name="forbidden-to-access-key-vault-during-appliance-registration"></a>在设备注册期间出现“禁止访问密钥保管库”

**错误**

由于发生错误“{KeyVaultErrorMessage}”，无法对“{KeyVaultName}”执行 Azure 密钥保管库创建或更新操作。

当用于注册设备的 Azure 用户帐户与用于在门户中生成 Azure Migrate 项目密钥（即在创建密钥保管库时）的帐户不同时，通常会发生这种情况。

**修正**

1. 确保设备上的当前已登录用户帐户对错误消息中提到的密钥保管库拥有所需的权限。 用户帐户需要拥有[此处](./tutorial-discover-vmware.md#prepare-an-azure-user-account)所述的权限。
2. 转到该密钥保管库，确保你的用户帐户具有访问策略，并拥有根据密钥保管库访问策略分配的所有密钥、机密和证书权限。 [了解详细信息](../key-vault/general/assign-access-policy-portal.md)
3. 如果已经为设备启用了专用终结点连接，请确保设备托管在创建密钥保管库的同一 VNet 中，或者已通过专用链接连接到 Azure VNet（创建密钥保管库的位置）。 确保可从设备解析密钥保管库专用链接。 转到“Azure Migrate: 发现和评估”> “属性”，找到资源的专用终结点详细信息，例如在创建 Azure Migrate 密钥期间创建的密钥保管库   。 [了解详细信息](./troubleshoot-network-connectivity.md)
4. 如果你拥有所需的权限并已建立连接，请稍后在设备上重试注册。

## <a name="unable-to-connect-to-vcenter-server-during-validation"></a>在验证期间无法连接到 vCenter Server

**错误**

如果出现此连接错误，可能无法连接到 vCenter Server Servername.com:9443。 错误详细信息指示 `https://\*servername*.com:9443/sdk` 处没有可接受消息的终结点在侦听。

**修正**

- 检查是否正在运行最新版本的设备。 如果不是，请将设备升级到[最新版本](./migrate-appliance.md)。
- 如果使用最新版本后仍出现此问题，则设备可能无法解析指定的 vCenter Server 名称，或者指定的端口可能是错误的。 默认情况下，如果端口未指定，收集器会尝试连接到端口号 443。

    1. 在设备中对 Servername.com 执行 ping 操作。
    2. 如果步骤 1 失败，请尝试使用 IP 地址连接到 vCenter Server。
    3. 确定可连接到 vCenter Server 的正确端口号。
    4. 验证 vCenter Server 是否已启动并正在运行。

## <a name="server-credentials-domain-failing-validation-on-vmware-appliance"></a>VMware 设备上的服务器凭据（域）通不过验证

**错误**

在 VMware 设备上添加的用于执行软件盘存和无代理依赖项分析的域凭据出现“验证失败”。

**修正**

1. 检查是否提供了正确的域名和凭据
1. 确保可从设备访问域以验证凭据。 设备可能存在视线问题，或是可能无法从设备服务器解析域名。
1. 可以选择“编辑”以更新域名或凭据，然后选择“重新验证凭据”以在一段时间后再次验证凭据 

## <a name="access-is-denied-when-connecting-to-hyper-v-hosts-or-clusters-during-validation"></a>在验证期间连接到 Hyper-V 主机或群集时出现“拒绝访问”

**错误**

由于出现“拒绝访问”错误，无法验证添加的 Hyper-V 主机/群集。

**修正**

1. 确保满足 [Hyper-V 主机的所有先决条件](./migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)。 
1. 请查看[此处](./tutorial-discover-hyper-v.md#prepare-hyper-v-hosts)的步骤，了解如何使用预配的 PowerShell 脚本或手动准备 Hyper-V 主机。

## <a name="the-server-does-not-support-ws-management-identify-operations-during-validation"></a>在验证期间出现“服务器不支持 WS-Management 标识操作”

**错误**

由于出现错误“服务器不支持 WS-Management 标识操作。 请跳过请求的 TestConnection 部分，然后重试”，无法验证设备上的 Hyper-V 群集。

**修正**

如果在设备上提供了代理配置，则通常会出现此错误。 设备使用群集节点的短名称连接到群集，即使你提供了节点的 FQDN 也是如此。 将群集节点的短名称添加到设备上的绕过代理列表即可解决该问题并成功验证 Hyper-V 群集。

## <a name="cant-connect-to-host-or-cluster-during-validation-on-hyper-v-appliance"></a>在 Hyper-V 设备上进行验证期间出现“无法连接到主机或群集”

**错误**

如果设备的 Azure DNS 服务无法解析你提供的群集或主机名， 可能出现错误 50004：“无法连接到主机或群集，因为无法解析服务器名称。WinRM 错误代码：0x803381B9”。

如果添加了无法通过 DNS 解析的主机的 IP 地址，通常就会发生这种情况。 可能还会在群集的主机中看到此错误。 这表明设备可以连接到群集，但群集返回的主机名不是 FQDN。

**修正**

若要解决此错误，请添加 IP 地址和主机名的映射来更新设备上的 hosts 文件：
1. 以管理员身份打开记事本。
1. 打开 C:\Windows\System32\Drivers\etc\hosts 文件。
1. 在一行中添加 IP 地址和主机名。 对于出现此错误的每个主机或群集重复此操作。
1. 保存然后关闭 hosts 文件。
1. 使用设备管理应用检查设备是否可以连接到主机。 30 分钟后，应该可在 Azure 门户中看到这些主机的最新信息。

## <a name="unable-to-connect-to-server-during-validation-of-physical-servers"></a>在验证物理服务器期间出现“无法连接到服务器”

**修正**

- 确保设备已连接到目标服务器。
- 如果它是 Linux 服务器，请确保通过以下步骤启用基于密码的身份验证：
    1. 登录 Linux 服务器，并使用命令“vi /etc/ssh/sshd_config”打开 SSH 配置文件
    2. 将“PasswordAuthentication”选项设置为“是”。 保存文件。
    3. 运行“service sshd restart”来重启 SSH 服务
- 如果它是 Windows 服务器，请确保端口 5985 处于打开状态以允许远程 WMI 调用。
- 如果你正在发现 GCP Linux 服务器并使用 root 用户，请使用以下命令更改默认 root 登录设置
    1. 登录 Linux 服务器，并使用命令“vi /etc/ssh/sshd_config”打开 SSH 配置文件
    2. 将“PermitRootLogin”选项设置为“是”。
    3. 运行“service sshd restart”来重启 SSH 服务

## <a name="failed-to-fetch-bios-guid-for-server-during-validation"></a>在验证期间服务器出现“无法提取 BIOS GUID”

**错误**

在设备上验证物理服务器失败并出现错误消息“无法提取 BIOS GUID”。

**修正**

Linux 服务器：连接到验证失败的目标服务器，并运行以下命令来确定是否返回服务器的 BIOS GUID：
````
cat /sys/class/dmi/id/product_uuid
dmidecode | grep -i uuid | awk '{print $2}'
````
还可以在通过以下命令与目标 Linux 服务器建立 SSH 连接后，在设备服务器上的命令提示符下运行上述命令：
````
ssh <username>@<servername>
````

Windows服务器：在设备服务器上的 PowerShell 中，针对验证失败的目标服务器运行以下代码，以确定是否返回服务器的 BIOS GUID：
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

执行上述代码时，需要提供目标服务器的主机名（可以是 IP 地址/FQDN/主机名）。 然后，系统会提示你提供用于连接该服务器的凭据。

## <a name="no-suitable-authentication-method-found-for-server-during-validation"></a>在验证期间服务器出现“找不到适当的身份验证方法”

**错误**

尝试通过物理设备验证 Linux 服务器时出现此错误：“找不到适当的身份验证方法”。

**修正**

确保通过以下步骤在 Linux 服务器上启用基于密码的身份验证：

1. 登录 Linux 服务器，并使用命令“vi /etc/ssh/sshd_config”打开 SSH 配置文件
2. 将“PasswordAuthentication”选项设置为“是”。 保存文件。
3. 运行“service sshd restart”来重启 SSH 服务

## <a name="access-is-denied-when-connecting-to-physical-servers-during-validation"></a>在验证期间连接到物理服务器时出现“拒绝访问”

**错误**

尝试通过物理设备验证 Windows 服务器时出现此错误：“WS-Management 服务无法处理请求。 WMI 服务返回了拒绝访问错误。”

**修正**

- 如果出现此错误，请确保已将设备配置管理器中提供的用户帐户（域/本地）添加到以下组：“远程管理用户”、“性能监视器用户”和“性能日志用户”。
- 如果“远程管理用户”组不存在，请将用户帐户添加到以下组：WinRMRemoteWMIUsers_。
- 还可以在目标服务器的命令提示符下运行以下命令，检查是否在服务器上启用了 WS-Management 协议。
    
    ```` winrm qc ````
- 如果仍然出现该问题，请确保用户帐户有权访问 WMI 控制面板中的 CIMV2 命名空间和子命名空间。 可按照以下步骤设置访问权限：
    1.  转到设备上验证失败的服务器
    2.  在“开始”菜单中搜索并选择“运行”。 在“运行”对话框的“打开:”文本字段中，键入“wmimgmt.msc”并按 Enter。
    3.  此时会打开 wmimgmt.msc 控制台，可在左侧面板中找到“WMI 控件(本地)”。 右键单击此项并从菜单中选择“属性”。
    4.  在“WMI 控件(本地)属性”对话框中，单击“安全”选项卡。
    5.  在“安全”选项卡上，展开命名空间树中的“Root”文件夹，并选择“cimv2”命名空间。
    6.  单击“安全”按钮打开“ROOT\cimv2 的安全性”对话框。
    7.  在“组或用户名”部分下，单击“添加”按钮打开“选择用户、计算机、服务帐户或组”对话框。
    8.  搜索该用户帐户并将其选中，然后单击“确定”按钮返回“ROOT\cimv2 的安全性”对话框。
    9.  在“组或用户名”部分中，选择刚刚添加的用户帐户并检查是否允许以下权限：<br/>
    启用帐户 <br/>
    远程启用
    10. 单击“应用”以启用对该用户帐户设置的权限。

- 相同的步骤也适用于非域/工作组服务器的本地用户帐户，但在某些情况下，[UAC](/windows/win32/wmisdk/user-account-control-and-wmi) 筛选可能会阻止某些 WMI 属性（因为命令是以标准用户的身份运行的），因此，你可以使用本地管理员帐户或禁用 UAC，以便不筛选本地用户帐户，而是让自己成为拥有完全权限的管理员。
- 不建议通过更改控制远程 UAC 的注册表项来禁用远程 UAC，但在工作组中可能需要这样做。 该注册表项为 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system\LocalAccountTokenFilterPolicy。 如果此项的值为零 (0)，则会启用远程 UAC 访问令牌筛选。 如果值为 1，则会禁用远程 UAC。

## <a name="appliance-is-disconnected"></a>设备已断开连接

**错误**

尝试通过门户在几个 VMware 服务器上启用复制时，出现“设备已断开连接”错误消息。

如果设备处于关闭状态，或者设备上的 DRA 服务无法与 Azure 通信，则会发生这种情况。

**修正**

 1. 请转到设备配置管理器并重新运行先决条件，以查看“查看设备服务”下 DRA 服务的状态。 
 1. 如果该服务未运行，请在命令提示符下使用以下命令来停止再重启该服务：

    ````
    net stop dra
    net start dra
    ````

## <a name="next-steps"></a>后续步骤

为 [VMware](how-to-set-up-appliance-vmware.md)、[Hyper-V](how-to-set-up-appliance-hyper-v.md) 或[物理服务器](how-to-set-up-appliance-physical.md)设置设备。