---
title: 排查与 Azure Migrate 设备部署和发现相关的问题
description: 获取有关设备部署和服务器发现的帮助。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: dceac550bb10d9365106f11b2089b031c4e7d451
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112679091"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>排查 Azure Migrate 设备和发现问题

本文将帮助你排查在部署 [Azure Migrate](migrate-services-overview.md) 设备以及使用设备发现本地服务器时遇到的问题。

## <a name="whats-supported"></a>支持的功能

[查看](migrate-appliance.md)设备支持要求。

## <a name="invalid-ovf-manifest-entry"></a>“OVF 清单条目无效”

如果收到错误“提供的清单文件无效：OVF 清单条目无效”，请执行以下操作：

1. 通过检查哈希值，验证是否已正确下载 Azure Migrate 设备 OVA 文件。 [了解详细信息](./tutorial-discover-vmware.md)。 如果哈希值不匹配，请重新下载 OVA 文件并重新尝试部署。
2. 如果部署仍然失败，并且是使用 VMware vSphere 客户端部署 OVF 文件，请尝试通过 vSphere Web 客户端进行部署。 如果部署仍失败，请尝试使用其它 Web 浏览器。
3. 如果使用的是 vSphere Web 客户端并尝试在 vCenter Server 6.5 或 6.7 上部署，请尝试直接在 ESXi 主机上部署 OVA：
   - 使用 Web 客户端（ https://<主机 IP 地址>/ui）直接连接 ESXi 主机（而不是 vCenter Server）。
   - 在“主页” > “清单”中，选择“文件” > “部署 OVF 模板”。    浏览到 OVA，并完成部署。
4. 如果部署仍然失败，请联系 Azure Migrate 支持部门。

## <a name="cant-connect-to-the-internet"></a>无法连接到 Internet

如果设备服务器位于代理后方，则会发生这种情况。

- 请确保提供代理所需的授权凭据。
- 如果使用基于 URL 的防火墙代理来控制出站连接，请将[以下 URL](migrate-appliance.md#url-access) 加入允许列表。
- 如果使用拦截代理连接到 Internet，请使用[这些步骤](./migrate-appliance.md)将代理证书导入设备。

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>无法从设备 Web 应用登录 Azure

如果使用不正确的 Azure 帐户登录 Azure，则会显示错误“抱歉，登录时遇到问题”。 此错误可能因以下原因所致：

- 如果使用政府云门户的用户帐户凭据登录公有云的设备 Web 应用程序，会出现此错误。
- 如果使用私有云门户的用户帐户凭据登录政府云的设备 Web 应用程序，会出现此错误。

请确保使用正确的凭据。

## <a name="datetime-synchronization-error"></a>日期/时间同步错误

日期和时间同步错误 (802) 指示服务器时钟可能与当前时间不同步，且时间差超过五分钟。 更改收集器服务器上的时钟时间以匹配当前时间：

1. 在服务器上打开管理员命令提示符。
2. 若要检查时区，请运行 w32tm /tz。
3. 若要同步时间，请运行 w32tm /resync。

## <a name="unabletoconnecttoserver"></a>“UnableToConnectToServer”

如果出现此连接错误，可能无法连接到 vCenter Server Servername.com:9443。 错误详细信息指示 `https://\*servername*.com:9443/sdk` 处没有可接受消息的终结点在侦听。

- 检查是否正在运行最新版本的设备。 如果不是，请将设备升级到[最新版本](./migrate-appliance.md)。
- 如果使用最新版本后仍出现此问题，则设备可能无法解析指定的 vCenter Server 名称，或者指定的端口可能是错误的。 默认情况下，如果端口未指定，收集器会尝试连接到端口号 443。

    1. 在设备中对 Servername.com 执行 ping 操作。
    2. 如果步骤 1 失败，请尝试使用 IP 地址连接到 vCenter Server。
    3. 确定可连接到 vCenter Server 的正确端口号。
    4. 验证 vCenter Server 是否已启动并正在运行。

## <a name="error-6005260039-appliance-might-not-be-registered"></a>错误 60052/60039：设备可能未注册

- 如果用于注册设备的 Azure 帐户没有足够的权限，则会出现错误 60052“设备可能未成功注册到项目”。
    - 确保用于注册设备的 Azure 用户帐户在订阅上至少具有“参与者”权限。
    - [详细了解](./migrate-appliance.md#appliance---vmware)所需的 Azure 角色和权限。
- 如果由于找不到设备的注册项目而导致注册失败，则会出现错误 60039“设备可能未成功注册到项目”。
    - 在 Azure 门户中，检查该项目是否在资源组中存在。
    - 如果项目不存在，请在资源组中创建一个新项目，然后重新注册设备。 [了解如何](./create-manage-projects.md#create-a-project-for-the-first-time)创建新项目。

## <a name="error-6003060031-key-vault-management-operation-failed"></a>错误 60030/60031：Key Vault 管理操作失败

如果收到错误 60030 或 60031“Azure Key Vault 管理操作失败”，请执行以下操作：

- 确保用于注册设备的 Azure 用户帐户在订阅上至少具有“参与者”权限。
- 确保帐户有权访问错误消息中指定的 Key Vault，然后重试该操作。
- 如果问题持续出现，请联系 Microsoft 支持。
- [详细了解](./migrate-appliance.md#appliance---vmware)所需的 Azure 角色和权限。

## <a name="error-60028-discovery-couldnt-be-initiated"></a>错误 60028：无法启动发现

错误 60028：“由于出错，无法启动发现。 对指定的一系列主机或群集的操作失败”，指示无法在错误信息中列出的主机上启动发现，因为访问或检索服务器信息时出现问题。 成功添加了其余主机。

- 使用“添加主机”选项重新添加错误信息中列出的主机。
- 如果出现验证错误，请查看修正指南以消除错误，然后再次尝试“保存并启动发现”选项。

## <a name="error-60025-azure-ad-operation-failed"></a>错误 60025：Azure AD 操作失败

如果用于启动发现的 Azure 用户账户与用于注册设备的帐户不同， 则会发生错误 60025：“Azure AD 操作失败。创建或更新 Azure AD 应用程序时出错”。 执行下列操作之一：

- 确保启动发现的用户帐户与用于注册设备的用户帐户相同。
- 向发现操作失败的用户帐户提供 Azure Active Directory 应用程序访问权限。
- 删除以前为项目创建的资源组。 创建另一个资源组以重新启动。
- [详细了解](./migrate-appliance.md#appliance---vmware) Azure Active Directory 应用程序权限。

## <a name="error-50004-cant-connect-to-host-or-cluster"></a>错误 50004：无法连接到主机或群集

如果设备的 Azure DNS 服务无法解析你提供的群集或主机名， 可能出现错误 50004：“无法连接到主机或群集，因为无法解析服务器名称。WinRM 错误代码：0x803381B9”。

- 如果在群集上看到此错误，则为群集 FQDN 错误。
- 可能还会在群集的主机中看到此错误。 这表明设备可以连接到群集，但群集返回的主机名不是 FQDN。 若要解决此错误，请添加 IP 地址和主机名的映射来更新设备上的 hosts 文件：
    1. 以管理员身份打开记事本。
    2. 打开 C:\Windows\System32\Drivers\etc\hosts 文件。
    3. 在一行中添加 IP 地址和主机名。 对于出现此错误的每个主机或群集重复此操作。
    4. 保存然后关闭 hosts 文件。
    5. 使用设备管理应用检查设备是否可以连接到主机。 30 分钟后，应该可在 Azure 门户中看到这些主机的最新信息。

## <a name="error-60001-unable-to-connect-to-server"></a>错误 60001：无法连接服务器

- 确保设备已连接到服务器
- 如果它是 Linux 服务器，确保通过以下步骤启用基于密码的身份验证：
    1. 登录 Linux 服务器，并使用命令“vi /etc/ssh/sshd_config”打开 SSH 配置文件
    2. 将“PasswordAuthentication”选项设置为“是”。 保存文件。
    3. 运行“service sshd restart”来重启 SSH 服务
- 如果它是 Windows 服务器，确保端口 5985 处于打开状态以允许远程 WMI 调用。
- 如果要发现 GCP Linux 服务器并使用根用户，请使用以下命令更改默认根登录设置
    1. 登录 Linux 服务器，并使用命令“vi /etc/ssh/sshd_config”打开 SSH 配置文件
    2. 将“PermitRootLogin”选项设置为“是”。
    3. 运行“service sshd restart”来重启 SSH 服务

## <a name="error-no-suitable-authentication-method-found"></a>错误：找不到合适的身份验证方法

确保通过以下步骤在 Linux 服务器上启用基于密码的身份验证：
    1. 登录 Linux 服务器，并使用命令“vi /etc/ssh/sshd_config”打开 SSH 配置文件
    2. 将“PasswordAuthentication”选项设置为“是”。 保存文件。
    3. 运行“service sshd restart”来重启 SSH 服务

## <a name="discovered-servers-not-in-portal"></a>已发现的服务器未在门户中显示

如果发现状态为“正在执行发现”，但尚未在门户中看到服务器，请等待几分钟：

- VMware 上的服务器大约 15 分钟后显示。
- 对于 Hyper-V 发现上的服务器，添加的每个主机大约需要两分钟显示。

如果等待后发现状态没有变化，请选择“服务器”选项卡上的“刷新”。这会显示在“Azure Migrate：发现和评估”和“Azure Migrate：服务器迁移”中发现的服务器数量。 

如果这不起作用，并且你需要发现 VMware 服务器：

- 请验证指定的 vCenter 帐户是否已正确设置权限，并且至少有权访问一台服务器。
- 如果 vCenter 帐户具有的是 vCenter VM 文件夹级别的访问权限，则 Azure Migrate 无法在 VMware 上发现服务器。 [详细了解](set-discovery-scope.md)如何限定发现范围。

## <a name="server-data-not-in-portal"></a>服务器数据未在门户中显示

如果发现的服务器未显示在门户中，或者服务器数据已过时，请等待几分钟。 最多 30 分钟后门户中便会显示发现的服务器的配置数据中的更改信息。 软件清单数据中的更改可能需要几个小时才能显示。 如果此时间之后未显示任何数据，请尝试刷新，如下所示

1. 在“Windows、Linux 和 SQL Server” > “Azure Migrate: 发现和评估”中，选择“概述”  。
2. 在“管理”下，选择“代理运行状况” 。
3. 选择“刷新代理”。
4. 等待刷新操作完成。 现在应会显示最新信息。

## <a name="deleted-servers-appear-in-portal"></a>门户中显示已删除的服务器

如果删除了服务器但这些服务器仍出现在门户中，请等待 30 分钟。 如果仍然出现，请按如上所述进行刷新。

## <a name="discovered-software-inventory-and-sql-server-instances-and-databases-not-in-portal"></a>发现的软件清单以及 SQL Server 实例和数据库未在门户中显示

在设备上启动发现后，可能需要长达 24 小时才能开始在门户中显示清单数据。

如果尚未在设备配置管理器上提供 Windows 身份验证或 SQL Server 身份验证凭据，请添加凭据，使设备可使用这些凭据连接到相应 SQL Server 实例。

连接后，设备会收集 SQL Server 实例和数据库的配置和性能数据。 SQL Server 配置数据每 24 小时更新一次，性能数据每 30 秒捕获一次。 因此，对 SQL Server 实例和数据库的属性（如数据库状态、兼容级别等）进行的任何更改可能需要长达 24 小时才能在门户中更新。

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>SQL Server 实例在门户上显示为“未连接”状态

若要查看 SQL Server 实例和数据库发现期间遇到的问题，请在项目的“已发现的服务器”页上的连接状态列中单击“未连接”状态。

对于包含未完全发现或处于未连接状态的 SQL 实例的服务器，在其上创建评估可能会导致将就绪状态标记为“未知”。

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>看不到物理服务器上的某些网络适配器的性能数据

如果物理服务器启用了 Hyper-V 虚拟化就会发生这种情况。 由于产品差异，将在发现的虚拟网络适配器上捕获网络吞吐量。

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>错误：上传的文件不是预期格式

某些工具具有区域设置，这些区域设置使用分号作为分隔符来创建 CSV 文件。 请更改设置以确保分隔符为逗号。

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>我导入了 CSV，但系统显示“正在发现”

如果 CSV 因验证失败而不能上传，则会显示此状态。 尝试再次导入 CSV。 你可以下载上一次上传的错误报告，并遵循该文件中的修复指南来修复错误。 可以从“发现服务器”页的“导入详细信息”部分下载错误报告。

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>即使在更新来宾凭据后，也看不到软件清单详细信息

软件清单发现每 24 小时运行一次。 如果希望立即查看详细信息，请按如下所示进行刷新。 这可能需要几分钟时间，具体取决于 已发现的服务器数量。

1. 在“Windows、Linux 和 SQL Server” > “Azure Migrate: 发现和评估”中，选择“概述”  。
2. 在“管理”下，选择“代理运行状况” 。
3. 选择“刷新代理”。
4. 等待刷新操作完成。 现在应会显示最新信息。

## <a name="unable-to-export-software-inventory"></a>无法导出软件清单

确保从门户下载清单的用户在订阅上拥有“参与者”权限。

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>找不到合适的身份验证方法来完成身份验证 (publickey)

基于密钥的身份验证将不起作用，请使用密码身份验证。

## <a name="common-app-discovery-errors"></a>常见的应用发现错误

Azure Migrate 支持使用“Azure Migrate: 发现和评估”来发现软件清单。 目前，只有 VMware 才支持应用发现。 [详细了解](how-to-discover-applications.md)设置应用发现的要求和步骤。

下表汇总了典型的应用发现错误。

| **错误** | **原因** | **操作** |
|--|--|--|
| 9000：无法检测到 VMware 工具状态。 | VMware 工具可能未安装或已损坏。 | 确保 VMware 工具已在服务器上安装并运行。 |
| 9001：没有安装 VMware 工具。 | VMware 工具可能未安装或已损坏。 | 确保 VMware 工具已在服务器上安装并运行。 |
| 9002：VMware 工具没有运行。 | VMware 工具可能未安装或已损坏。 | 确保 VMware 工具已在服务器上安装并运行。 |
| 9003：操作系统类型不受来宾服务器发现支持。 | 服务器上运行的操作系统既不是 Windows 也不是 Linux。 | 受支持的操作系统类型只有 Windows 和 Linux。 如果服务器确实运行的是 Windows 或 Linux，请检查 vCenter Server 中指定的操作系统类型。 |
| 9004：服务器未运行。 | 服务器已关机。 | 确保服务器已打开。 |
| 9005：操作系统类型不受来宾服务器发现支持。 | 操作系统类型不受来宾服务器发现支持。 | 受支持的操作系统类型只有 Windows 和 Linux。 |
| 9006：用于从来宾下载元数据文件的 URL 是空的。 | 如果发现代理未按预期工作，则可能发生这种情况。 | 此问题应会在 24 小时内自动消除。 如果问题持续出现，请联系 Microsoft 支持。 |
| 9007：找不到在来宾服务器中运行发现任务的进程。 | 如果发现代理工作不正常，则可能发生这种情况。 | 此问题应会在 24 小时内自动消除。 如果问题持续出现，请联系 Microsoft 支持。 |
| 9008：无法检索来宾服务器进程状态。 | 如果发生内部错误，可能会出现此问题。 | 此问题应会在 24 小时内自动消除。 如果问题持续出现，请联系 Microsoft 支持。 |
| 9009：Windows UAC 阻止了在服务器上执行发现任务。 | 服务器上的 Windows 用户帐户控制 (UAC) 设置是限制性的，会阻止发现已安装的软件清单。 | 请在服务器的“用户帐户控制”设置中，将 UAC 设置配置为较低的两个级别之一。 |
| 9010：服务器关机。 | 服务器关机。 | 确保服务器已打开。 |
| 9011：在来宾服务器文件系统中找不到已发现的元数据文件。 | 如果发生内部错误，可能会出现此问题。 | 此问题应会在 24 小时内自动消除。 如果问题持续出现，请联系 Microsoft 支持。 |
| 9012：发现的元数据文件是空的。 | 如果发生内部错误，可能会出现此问题。 | 此问题应会在 24 小时内自动消除。 如果问题持续出现，请联系 Microsoft 支持。 |
| 9013：每次登录都会新建一个临时配置文件。 | 每次登录 VMware 上的服务器都会新建一个临时配置文件。 | 请联系 Microsoft 支持部门，获取解决方案。 |
| 9014：无法在来宾服务器文件系统中检索元数据。 | 未连接到 ESXi 主机 | 确保设备可以连接到运行服务器的 ESXi 主机上的端口 443 |
| 9015：vCenter 用户帐户上未启用来宾操作角色 | vCenter 用户帐户上未启用来宾操作角色。 | 确保 vCenter 用户帐户上已启用来宾操作角色。 |
| 9016：无法执行发现，因为来宾操作代理已过时。 | VMware 工具没有正确安装或不是最新的。 | 确保 VMware 工具已正确安装且是最新的。 |
| 9017：在服务器上找不到包含发现的元数据的文件。 | 如果发生内部错误，可能会出现此问题。 | 请联系 Microsoft 支持部门，获取解决方案。 |
| 9018：来宾服务器中未安装 PowerShell。 | 来宾服务器中没有 PowerShell。 | 在来宾服务器中安装 PowerShell。 |
| 9019：无法执行发现，因为来宾服务器操作失败。 | 无法在服务器上执行 VMware 来宾操作。 | 确保服务器凭据有效，并且来宾服务器凭据中提供的用户名采用 UPN 格式。 |
| 9020：文件创建权限被拒绝。 | 与用户或组策略关联的角色限制用户在文件夹中创建文件 | 请检查所提供的来宾用户是否拥有对文件夹中文件的创建权限。 有关文件夹的名称，请查看“Azure Migrate: 发现和评估”中的“通知”。 |
| 9021：无法在系统临时路径中创建文件。 | VMware 工具报告系统临时路径而不是用户临时路径。 | 将 VMware 工具版本升级到高于 10287 (NGC/VI 客户端格式)。 |
| 9022：对 WMI 对象的访问被拒绝。 | 与用户或组策略关联的角色限制用户访问 WMI 对象。 | 请联系 Microsoft 支持。 |
| 9023：无法运行 PowerShell，因为 SystemRoot 环境变量值是空的。 | 对于来宾服务器，SystemRoot 环境变量的值是空的。 | 请联系 Microsoft 支持部门，获取解决方案。 |
| 9024：无法执行发现，因为 TEMP 环境变量值是空的。 | 对于来宾服务器，TEMP 环境变量的值是空的。 | 请联系 Microsoft 支持。 |
| 9025：来宾服务器中的 PowerShell 损坏。 | 来宾服务器中的 PowerShell 损坏。 | 在来宾服务器中重新安装 PowerShell，并验证 PowerShell 能否在来宾服务器上运行。 |
| 9026：无法在服务器上运行来宾操作。 | 服务器状态不允许在服务器上运行来宾操作。 | 请联系 Microsoft 支持部门，获取解决方案。 |
| 9027：来宾操作代理未在服务器中运行。 | 无法联系在虚拟服务器内运行的来宾操作代理。 | 请联系 Microsoft 支持部门，获取解决方案。 |
| 9028：无法创建文件，因为服务器中的磁盘存储空间不足。 | 磁盘空间不足。 | 确保服务器的磁盘存储中有足够的可用空间。 |
| 9029：所提供的来宾服务器凭据无法访问 PowerShell。 | 用户无法访问 PowerShell。 | 确保在设备上添加的用户可以访问来宾服务器上的 PowerShell。 |
| 9030：无法收集发现的元数据，因为 ESXi 主机已断开连接。 | ESXi 主机处于断开连接状态。 | 确保运行服务器的 ESXi 主机已连接。 |
| 9031：无法收集发现的元数据，因为 ESXi 主机没有响应。 | 远程主机处于无效状态。 | 确保运行服务器的 ESXi 主机正在运行且已连接。 |
| 9032：无法执行发现，因为发生了内部错误。 | 由于内部错误，可能会出现此问题。 | 请联系 Microsoft 支持部门，以获取解决方案。 |
| 9033：无法执行发现，因为服务器用户名包含无效字符。 | 在用户名中检测到无效字符。 | 重新提供服务器凭据，确保没有无效字符。 |
| 9034：所提供的用户名不是 UPN 格式。 | 用户名不是 UPN 格式。 | 确保用户名是用户主体名称 (UPN) 格式。 |
| 9035：无法执行发现，因为 PowerShell 语言模式未设置为“完整语言”。 | 来宾服务器中 PowerShell 的语言模式未设置为“完整语言”。 | 确保 PowerShell 语言模式设置为“完整语言”。 |
| 9037：数据收集已暂时暂停，因为服务器响应时间过长。 | 发现的服务器响应时间过长 | 无需采取措施。 将在 24 小时内尝试重新执行软件清单发现，并在 3 小时内重新执行依赖项分析（无代理）。 |
| 10000：操作系统类型不受支持。 | 服务器上运行的操作系统既不是 Windows 也不是 Linux。 | 受支持的操作系统类型只有 Windows 和 Linux。 |
| 10001：在设备上找不到服务器发现脚本。 | 发现未按预期执行。 | 请联系 Microsoft 支持部门，以获取解决方案。 |
| 10002：发现任务尚未及时完成。 | 发现代理未按预期运行。 | 此问题应会在 24 小时内自动解决。 如果问题持续出现，请联系 Microsoft 支持。 |
| 10003：执行发现任务的进程因错误而退出。 | 执行发现任务的进程因错误而退出。 | 此问题应会在 24 小时内自动解决。 如果该问题仍然存在，请联系 Microsoft 支持部门。 |
| 10004：没有为来宾操作系统类型提供凭据。 | Azure Migrate 设备中未提供用于访问此 OS 类型的服务器的凭据。 | 在设备上添加服务器凭据 |
| 10005：所提供的凭据无效。 | 为设备访问服务器所提供的凭据不正确。 | 更新设备中提供的凭据，并确保可使用这些凭据访问服务器。 |
| 10006：来宾 OS 类型不受凭据存储支持。 | 服务器上运行的操作系统既不是 Windows 也不是 Linux。 | 受支持的操作系统类型只有 Windows 和 Linux。 |
| 10007：无法处理已发现的元数据。 | 尝试反序列化 JSON 时出错。 | 请联系 Microsoft 支持部门，以获取解决方案。 |
| 10008：无法在服务器上创建文件。 | 此问题可能是由于内部错误所致。 | 请联系 Microsoft 支持部门，以获取解决方案。 |
| 10009：无法将发现的元数据写入到服务器上的文件。 | 由于内部错误，可能会出现此问题。 | 请联系 Microsoft 支持部门，获取解决方案。 |

## <a name="common-sql-server-instances-and-database-discovery-errors"></a>常见的 SQL Server 实例和数据库发现错误

Azure Migrate 支持使用“Azure Migrate：发现和评估”来发现在本地计算机上运行的 SQL Server 实例和数据库。 目前，只有 VMware 才支持 SQL 发现。 请参阅[“发现”教程](tutorial-discover-vmware.md)以进行入门。

下表汇总了典型的 SQL 发现错误。

| **错误** | **原因** | **操作** | **指南**
|--|--|--|--|
|30000：与此 SQL Server 关联的凭据不起作用。|手动关联的凭据无效，或自动关联的凭据无法再访问 SQL Server。|在设备上为 SQL Server 添加凭据，并等到下一个 SQL 发现周期或强制刷新。| - |
|30001：无法从设备连接到 SQL Server。|1. 设备没有在网络上发现 SQL Server。<br/>2. 防火墙阻止 SQL Server 与设备之间的连接。|1. 从 SQL Server 设置为允许从设备访问。<br/>2. 允许从设备到 SQL Server 的传入连接。| - | 
|30003：证书不受信任。|运行 SQL Server 的计算机上未安装受信任的证书。|请在服务器上设置受信任的证书。 [了解详细信息](/troubleshoot/sql/connect/error-message-when-you-connect)| [视图](/troubleshoot/sql/connect/error-message-when-you-connect) |
|30004：权限不足。|出现此错误可能是由于缺少扫描 SQL Server 实例所需的权限。 |将 sysadmin 角色授予设备上提供的凭据/帐户，以用于发现 SQL Server 实例和数据库。 [了解详细信息](/sql/t-sql/statements/grant-server-permissions-transact-sql)| [视图](/sql/t-sql/statements/grant-server-permissions-transact-sql) |
|30005：SQL Server 登录名无法进行连接，原因是其默认的主数据库出现问题。|数据库本身无效或登录名缺少数据库的 CONNECT 权限。|使用 ALTER LOGIN 将默认数据库设置为主数据库。<br/>将 sysadmin 角色授予设备上提供的凭据/帐户，以用于发现 SQL Server 实例和数据库。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error) |
|30006：SQL Server 登录名不能用于 Windows 身份验证。|1. 此登录名可能是 SQL Server 登录名，但服务器仅接受 Windows 身份验证。<br/>2. 你尝试使用 SQL Server 身份验证连接，但 SQL Server 上不存在所用登录名。<br/>3. 登录可能使用 Windows 身份验证，但登录名是无法识别的 Windows 主体。 无法识别的 Windows 主体表示 Windows 无法验证此登录名。 这可能是由于此 Windows 登录名来自不可信的域。|如果尝试使用 SQL Server 身份验证进行连接，请验证是否在混合身份验证模式下配置了 SQL Server 并且存在 SQL Server 登录名。<br/>如果尝试使用 Windows 身份验证进行连接，请验证您是否正确地登录到相应的域。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error) |
|30007：密码已过期。|该帐户的密码已过期。|SQL Server 登录密码可能已过期，请重新设置密码并/或延长密码到期日期。 [了解详细信息](/sql/relational-databases/native-client/features/changing-passwords-programmatically)| [视图](/sql/relational-databases/native-client/features/changing-passwords-programmatically) |
|30008：必须更改密码。|必须更改该帐户的密码。|更改针对 SQL Server 发现提供的凭据的密码。 [了解详细信息](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105))| [视图](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105)) |
|30009：发生内部错误。|发现 SQL Server 实例和数据库时发生内部错误。 |如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30010：找不到数据库|在所选服务器实例中找不到任何数据库。|将 sysadmin 角色授予设备上提供的凭据/帐户，以用于发现 SQL 数据库。| - |
|30011：评估 SQL 实例或数据库时发生内部错误。|执行评估时发生内部错误。|如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30012：SQL 连接失败。|1. 服务器上的防火墙拒绝了此连接。<br/>2. SQL Server Browser 服务 (sqlbrowser) 未启动。<br/>3. SQL Server 未响应客户端请求，可能是因为尚未启动服务器。<br/>4. SQL Server 客户端无法连接到服务器。 发生此错误的原因可能是服务器未配置为接受远程连接。<br/>5. SQL Server 客户端无法连接到服务器。 发生此错误的原因可能是客户端无法解析服务器的名称或服务器的名称不正确。<br/>6. TCP 或 Named Pipes 协议未启用。<br/>7. 指定的 SQL Server 实例名称无效。|请使用[此](https://go.microsoft.com/fwlink/?linkid=2153317)交互式用户指南解决连接问题。 按照指南进行操作之后请等待 24 小时，以便数据在服务中更新。 如果该问题仍然存在，请联系 Microsoft 支持部门。| [视图](https://go.microsoft.com/fwlink/?linkid=2153317) |
|30013：与 SQL Server 实例建立连接时出错。|1. 无法从设备解析 SQL Server 的名称。<br/>2. SQL Server 不允许远程连接。|如果可以从设备 ping SQL server，请等待 24 小时，然后检查此问题是否自动解决。 如果没有，请联系 Microsoft 支持部门。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error) |
|30014：用户名或密码无效。| 出现此错误的原因可能是身份验证失败，与密码或用户名错误有关。|请提供具有有效用户名和密码的凭据。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) |
|30015：发现 SQL 实例时出现内部错误。|发现 SQL 实例时出现内部错误。|如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30016：由于超时，与实例“%instance;”的连接失败。| 如果服务器上的防火墙拒绝连接，就可能发生这种情况。|验证 SQL Server 上的防火墙是否配置为接受连接。 如果错误仍然存在，请联系 Microsoft 支持部门。 [了解详细信息](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error)| [视图](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error) |
|30017：出现内部错误。|未经处理的异常。|如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30018：出现内部错误。|收集 SQL 实例的临时数据库大小、文件大小等数据时出现内部错误。|请等待 24 小时，如果问题仍然存在，请与 Microsoft 支持部门联系。| - |
|30019：出现内部错误。|收集数据库或实例的性能指标（如内存使用率等）时出现内部错误。|请等待 24 小时，如果问题仍然存在，请与 Microsoft 支持部门联系。| - |

## <a name="next-steps"></a>后续步骤

为 [VMware](how-to-set-up-appliance-vmware.md)、[Hyper-V](how-to-set-up-appliance-hyper-v.md) 或[物理服务器](how-to-set-up-appliance-physical.md)设置设备。