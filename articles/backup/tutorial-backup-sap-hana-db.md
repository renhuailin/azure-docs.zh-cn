---
title: 教程 - 备份 Azure VM 中的 SAP HANA 数据库
description: 在本教程中，了解如何将 Azure VM 上运行的 SAP HANA 数据库备份到 Azure 备份恢复服务保管库。
ms.topic: tutorial
ms.date: 09/01/2021
ms.openlocfilehash: 3cfbd89e9df6cf2d0d30d744ee8e437e3c364094
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434243"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>教程：备份 Azure VM 中的 SAP HANA 数据库

本教程介绍如何将 Azure VM 上运行的 SAP HANA 数据库备份到 Azure 备份恢复服务保管库。 在本文中，你将学习如何：

> [!div class="checklist"]
>
> * 创建并配置保管库
> * 发现数据库
> * 配置备份

[这里](sap-hana-backup-support-matrix.md#scenario-support)有我们目前支持的所有方案。

>[!NOTE]
>自 2020 年 8 月 1 日起，适用于 RHEL 的 SAP HANA 备份（7.4、7.6、7.7 和 8.1）已正式发布。

## <a name="prerequisites"></a>先决条件

在配置备份之前，请确保执行以下操作：

* 在运行 SAP HANA 的 VM 所在的同一区域和订阅中标识或创建一个[恢复服务保管库](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)。
* 允许从 VM 连接到 Internet，以便 VM 可以访问 Azure，如下面的[设置网络连接](#set-up-network-connectivity)过程中所述。
* 对于 Azure 资源管理器，请确保 SAP HANA 服务器 VM 名称和资源组名称的组合长度不超过 84 个字符（对于经典 VM，则不超过 77 个字符）。 此限制是因为某些字符由该服务预留。
* **hdbuserstore** 中应存在一个满足以下条件的密钥：
  * 它应该出现在默认的 hdbuserstore 中。 默认值为安装 SAP HANA 的 `<sid>adm` 帐户。
  * 对于 MDC，该密钥应指向 **NAMESERVER** 的 SQL 端口。 对于 SDC，它应指向 INDEXSERVER 的 SQL 端口
  * 它应该包含用于添加和删除用户的凭据
  * 请注意，成功运行预注册脚本后可以删除此密钥
* 还可以选择为 hdbuserstore 中的现有 HANA SYSTEM 用户创建密钥，而不是创建上述步骤中列出的自定义密钥。
* 在安装了 HANA 的虚拟机中，以 root 用户身份运行 SAP HANA 备份配置脚本（注册前脚本）。 [此脚本](https://aka.ms/scriptforpermsonhana)可让 HANA 系统做好备份准备，并要求将你在上述步骤中创建的密钥作为输入传递。 要了解如何将此输入作为参数传递到脚本，请参阅[预注册脚本的功能](#what-the-pre-registration-script-does)部分。 它还详细说明了预注册脚本的功能。
* 如果 HANA 安装程序使用专用终结点，请使用 -sn 或 -skip-network-checks 参数运行[预注册脚本](https://aka.ms/scriptforpermsonhana)。

>[!NOTE]
>预注册脚本将为 RHEL（7.4、7.6 和 7.7）上运行的 SAP HANA 工作负载安装 compat-unixODBC234，为 RHEL 8.1 上的安装 unixODBC 。 [此包位于 SAP 解决方案 (RPM) 存储库中的 RHEL for SAP HANA（适用于 RHEL 7 服务器）更新服务中](https://access.redhat.com/solutions/5094721)。  对于 Azure 市场 RHEL 映像，存储库应为 rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms。

## <a name="set-up-network-connectivity"></a>设置网络连接

对于所有操作，在 Azure VM 上运行的 SAP HANA 数据库需要连接到 Azure 备份服务、Azure 存储和 Azure Active Directory。 这可以通过使用专用终结点，或允许访问所需的公共 IP 地址或 FQDN 来实现。 如果不允许正确连接到所需的 Azure 服务，则可能会导致诸如数据库发现、配置备份、执行备份和还原数据等操作失败。

下表列出了可用于建立连接的各种备选方案：

| **选项**                        | **优点**                                               | **缺点**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 专用终结点                 | 允许通过虚拟网络中的专用 IP 进行备份  <br><br>   提供网络和保管库端的精细控制 | 产生标准专用终结点[开销](https://azure.microsoft.com/pricing/details/private-link/) |
| NSG 服务标记                  | 由于范围更改会自动合并，因此管理变得更容易   <br><br>   无额外成本 | 只可用于 NSG  <br><br>    提供对整个服务的访问 |
| Azure 防火墙 FQDN 标记          | 自动管理必需的 FQDN，因此更易于管理 | 只可用于 Azure 防火墙                         |
| 允许访问服务 FQDN/IP | 无额外成本   <br><br>  适用于所有网络安全设备和防火墙 | 可能需要访问一组广泛的 IP 或 FQDN   |
| 使用 HTTP 代理                 | 对 VM 进行单点 Internet 访问                       | 通过代理软件运行 VM 带来的额外成本         |

关于使用这些选项的更多细节如下：

### <a name="private-endpoints"></a>专用终结点

使用专用终结点，可以从虚拟网络内的服务器安全地连接到恢复服务保管库。 专用终结点为保管库使用 VNET 地址空间中的 IP。 虚拟网络中的资源与保管库之间的网络流量将通过虚拟网络和 Microsoft 主干网络上的专用链接传输。 这样就不会从公共 Internet 泄露信息。 在[此处](./private-endpoints.md)详细了解 Azure 备份的专用终结点。

### <a name="nsg-tags"></a>NSG 标记

如果使用网络安全组 (NSG)，请使用 AzureBackup 服务标记以允许对 Azure 备份进行出站访问。 除了 Azure 备份标记外，还需要通过为 Azure AD (AzureActiveDirectory) 和 Azure 存储（存储）创建类似的 [NSG 规则](../virtual-network/network-security-groups-overview.md#service-tags)，以便在连接后进行身份验证和数据传输。 以下步骤介绍了为 Azure 备份标记创建规则的过程：

1. 在“所有服务”中转到“网络安全组”，然后选择“网络安全组”。

1. 在“设置”下选择“出站安全规则”。

1. 选择 **添加** 。 根据[安全规则设置](../virtual-network/manage-network-security-group.md#security-rule-settings)中所述，输入创建新规则所需的所有详细信息。 请确保将选项“目标”设置为“服务标记”，将“目标服务标记”设置为“AzureBackup”。

1. 选择“添加”，保存新创建的出站安全规则。

同样，可以为 Azure 存储和 Azure AD 创建 [NSG 出站安全规则](../virtual-network/network-security-groups-overview.md#service-tags)。 有关服务标记的详细信息，请参阅[此文](../virtual-network/service-tags-overview.md)。

### <a name="azure-firewall-tags"></a>Azure 防火墙标记

如果使用 Azure 防火墙，请使用 *AzureBackup* [Azure 防火墙 FQDN 标记](../firewall/fqdn-tags.md)创建应用程序规则。 这允许对 Azure 备份进行所有出站访问。

### <a name="allow-access-to-service-ip-ranges"></a>允许访问服务 IP 范围

如果选择允许访问服务 IP，请参阅[此处](https://www.microsoft.com/download/confirmation.aspx?id=56519)的 JSON 文件中的 IP 范围。 你需要允许访问与 Azure 备份、Azure 存储和 Azure Active Directory 对应的 IP。

### <a name="allow-access-to-service-fqdns"></a>允许访问服务 FQDN

还可以使用以下 FQDN 以允许从服务器访问所需的服务：

| 服务    | 要访问的域名                             |
| -------------- | ------------------------------------------------------------ |
| Azure 备份  | `*.backup.windowsazure.com`                             |
| Azure 存储 | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | 根据[这篇文章](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)，允许访问第 56 和 59 节下的 FQDN |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>使用 HTTP 代理服务器路由流量

备份在 Azure VM 上运行的 SAP HANA 数据库时，该 VM 上的备份扩展将使用 HTTPS API 将管理命令发送到 Azure 备份，并将数据发送到 Azure 存储。 备份扩展还使用 Azure AD 进行身份验证。 通过 HTTP 代理路由这三个服务的备份扩展流量。 使用上面提到的 IP 和 FQDN 列表，以允许访问所需的服务。 不支持已经过身份验证的代理服务器。

## <a name="understanding-backup-and-restore-throughput-performance"></a>了解备份和还原吞吐量性能

SAP HANA Azure VM 中通过 Backint 提供的备份（日志和非日志）将流式传输到 Azure 恢复服务保管库，因此，了解此流式传输方法非常重要。

HANA 的 Backint 组件提供了连接到数据库文件所在底层磁盘的“管道”（要读取的管道，以及要写入到的管道），这些数据库文件将由 Azure 备份服务读取并传输到 Azure 恢复服务保管库。 除了 Backint 本机验证检查以外，Azure 备份服务还会执行校验和来验证流。 这些验证将确保 Azure 恢复服务保管库中存在的数据确实可靠且可恢复。

由于流主要处理磁盘，因此你需要了解磁盘性能以衡量备份和还原性能。 请参阅[此文](../virtual-machines/disks-performance.md)，深入了解 Azure VM 中的磁盘吞吐量和性能。 这些也适用于备份和还原性能。

对于 HANA 的非日志备份（例如完整备份、差异备份和增量备份），Azure 备份服务会尝试实现最大大约为 420 MBps 的吞吐量，对于 HANA 的日志备份，则会尝试实现最大大约为 100 MBps 的吞吐量。 如上所述，这些并非保证的速度，并且它们取决于以下因素：

* VM 的最大非缓存磁盘吞吐量
* 底层磁盘类型及其吞吐量
* 同时尝试读取和写入同一磁盘的进程的数目。

> [!IMPORTANT]
> 在较小的 VM 中，如果非缓存的磁盘吞吐量非常接近或小于 400 MBps，那么你可能会担心整个磁盘 IOPS 都将由备份服务使用，而这可能会影响与磁盘上的读取/写入相关的 SAP HANA 操作。 在这种情况下，如果希望将备份服务使用量限制为最大限值，则可以参阅下一部分。

### <a name="limiting-backup-throughput-performance"></a>限制备份吞吐量性能

如果要将备份服务磁盘 IOPS 使用量限制为最大值，请执行以下步骤。

1. 转到“opt/msawb/bin”文件夹
2. 创建名为“ExtensionSettingOverrides”的新 JSON 文件
3. 将键值对添加到 JSON 文件，如下所示：

    ```json
    {
    "MaxUsableVMThroughputInMBPS": 200
    }
    ```

4. 更改文件的权限和所有权，如下所示：
    
    ```bash
    chmod 750 ExtensionSettingsOverrides.json
    chown root:msawb ExtensionSettingsOverrides.json
    ```

5. 不需要重新启动任何服务。 Azure 备份服务将尝试限制此文件中提到的吞吐量性能。

## <a name="what-the-pre-registration-script-does"></a>注册前脚本的功能

注册前脚本执行以下功能：

* 脚本将基于 Linux 分发安装或更新 Azure 备份代理所需的任何包。
* 执行与 Azure 备份服务器和相关服务（例如 Azure Active Directory 和 Azure 存储）之间的出站网络连接检查。
* 它使用[先决条件](#prerequisites)中提到的自定义用户密钥或 SYSTEM 用户密钥登录到 HANA 系统。 它用于在 HANA 系统中创建备份用户 (AZUREWLBACKUPHANAUSER)，成功运行预注册脚本后，可以删除该用户密钥。 请注意，不得删除 SYSTEM 用户密钥。
* 为 AZUREWLBACKUPHANAUSER 分配了以下必需的角色和权限：
  * 对于 MDC：数据库管理员和备份管理员（从 HANA 2.0 SPS05 开始）：在还原期间创建新数据库。
  * 对于 SDC：备份管理员：在还原期间创建新数据库。
  * 目录读取：读取备份目录。
  * SAP_INTERNAL_HANA_SUPPORT：访问一些专用表。 仅对于 HANA 2.0 SPS04 Rev 46 以下的 SDC 和 MDC 版本是必需的。 对于 HANA 2.0 SPS04 Rev 46 和更高版本不是必需的，因为我们现在会通过 HANA 团队提供的修补程序从公共表中获取所需的信息。
* 此脚本在 **hdbuserstore** 中为 HANA 备份插件的 AZUREWLBACKUPHANAUSER 添加一个密钥，以便处理所有操作（数据库查询、还原操作、配置和运行备份）。
* 你也可以选择创建自己的自定义备份用户。 确保为该用户分配了以下必需的角色和权限：
  * 对于 MDC：数据库管理员和备份管理员（从 HANA 2.0 SPS05 开始）：在还原期间创建新数据库。
  * 对于 SDC：备份管理员：在还原期间创建新数据库。
  * 目录读取：读取备份目录。
  * SAP_INTERNAL_HANA_SUPPORT：访问一些专用表。 仅对于 HANA 2.0 SPS04 Rev 46 以下的 SDC 和 MDC 版本是必需的。 对于 HANA 2.0 SPS04 Rev 46 和更高版本不是必需的，因为我们现在会通过 HANA 团队提供的修补程序从公共表中获取所需的信息。
* 然后在 hdbuserstore 中为 HANA 备份插件的自定义备份用户添加一个密钥，以便处理所有操作（数据库查询、还原操作、配置和运行备份）。 将此自定义备份用户密钥作为参数传递给脚本：`-bk CUSTOM_BACKUP_KEY_NAME` 或 `-backup-key CUSTOM_BACKUP_KEY_NAME`。  请注意，此自定义备份密钥的密码过期可能会导致备份和还原失败。

>[!NOTE]
> 若要了解脚本接受哪些其他参数，请使用命令 `bash msawb-plugin-config-com-sap-hana.sh --help`

若要确认创建密钥，请在具有 SIDADM 凭据的 HANA 计算机上运行以下 HDBSQL 命令：

```hdbsql
hdbuserstore list
```

命令输出应显示 {SID}{DBNAME} 密钥，用户显示为 AZUREWLBACKUPHANAUSER。

>[!NOTE]
> 请确保 `/usr/sap/{SID}/home/.hdb/` 下有一组唯一的 SSFS 文件。 此路径中应只有一个文件夹。

下面概述了完成预注册脚本运行所需的步骤。 请注意，在此流程中，我们将提供 SYSTEM 用户密钥作为预注册脚本的输入参数。

|谁  |从  |运行内容  |注释  |
|---------|---------|---------|---------|
|```<sid>```adm (OS)     |  HANA OS       |   阅读教程并下载预注册脚本      |   阅读[上述先决条件](#prerequisites)    从[此处](https://aka.ms/scriptforpermsonhana)下载预注册脚本  |
|```<sid>```adm (OS) 和 SYSTEM 用户 (HANA)    |      HANA OS   |   运行 hdbuserstore Set 命令      |   例如，hdbuserstore Set SYSTEM hostname>:3```<Instance#>```13 SYSTEM ```<password>``` **注意：**  请确保使用主机名，而不是 IP 地址或 FQDN      |
|```<sid>```adm (OS)    |   HANA OS      |  运行 hdbuserstore List 命令       |   检查结果是否包含默认存储，如下所示：```KEY SYSTEM  ENV : <hostname>:3<Instance#>13  USER: SYSTEM```      |
|Root (OS)     |   HANA OS        |    运行 Azure 备份 HANA 预注册脚本      |    ```./msawb-plugin-config-com-sap-hana.sh -a --sid <SID> -n <Instance#> --system-key SYSTEM```     |
|```<sid>```adm (OS)    |  HANA OS       |   运行 hdbuserstore List 命令      |    检查结果是否包含新行，如下所示：```KEY AZUREWLBACKUPHANAUSER  ENV : localhost: 3<Instance#>13   USER: AZUREWLBACKUPHANAUSER```     |

成功运行预注册脚本并进行验证后，可以继续检查[连接要求](#set-up-network-connectivity)，并从恢复服务保管库[配置备份](#discover-the-databases)

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是用于存储在不同时间创建的备份和恢复点的实体。 恢复服务保管库还包含与受保护虚拟机关联的备份策略。

若要创建恢复服务保管库，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中登录到自己的订阅。

2. 在左侧菜单中，选择“所有服务”

   ![选择“所有服务”](./media/tutorial-backup-sap-hana-db/all-services.png)

3. 在“所有服务”对话框中，输入“恢复服务” 。 资源列表根据输入进行筛选。 在资源列表中，选择“恢复服务保管库”。

   ![选择恢复服务保管库](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. 在“恢复服务保管库”仪表板上，选择“添加” 。

   ![添加恢复服务保管库](./media/tutorial-backup-sap-hana-db/add-vault.png)

   此时会打开“恢复服务保管库”对话框。 提供“名称”、“订阅”、“资源组”和“位置”的值

   ![创建恢复服务保管库](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * 名称：该名称用于标识恢复服务保管库，并且必须对于 Azure 订阅是唯一的。 指定的名称应至少包含 2 个字符，最多不超过 50 个字符。 名称必须以字母开头且只能包含字母、数字和连字符。 对于本教程，我们使用了名称“SAPHanaVault”。
   * **订阅**：选择要使用的订阅。 如果你仅是一个订阅的成员，则会看到该名称。 如果不确定要使用哪个订阅，请使用默认的（建议的）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。 在本教程中，我们使用了“SAP HANA 解决方案实验室订阅”。
   * **资源组**：使用现有资源组，或创建一个新的资源组。 在本教程中，我们使用了“SAPHANADemo”。<br>
   要查看订阅中可用的资源组列表，请选择“使用现有资源”，然后从下拉列表框中选择一个资源。 若要创建新资源组，请选择“新建”，然后输入名称。 有关资源组的完整信息，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)。
   * **位置**：为保管库选择地理区域。 保管库必须与运行 SAP HANA 的虚拟机位于同一区域中。 我们使用了“美国东部 2”。

5. 选择“查看 + 创建”。

   ![选择“查看和创建”](./media/tutorial-backup-sap-hana-db/review-create.png)

现在，恢复服务存储库已创建。

## <a name="discover-the-databases"></a>发现数据库

1. 在保管库的“开始使用”中，选择“备份” 。 在“工作负荷在哪里运行?”中，选择“Azure VM 中的 SAP HANA” 。
2. 选择“开始发现”。 这会开始在保管库区域中发现未受保护的 Linux VM。 你将看到想要保护的 Azure VM。
3. 在“选择虚拟机”中，选择脚本下载链接。此脚本可为 Azure 备份服务提供访问 SAP HANA VM 的权限，以进行数据库发现。
4. 在托管要备份的 SAP HANA 数据库的 VM 上运行此脚本。
5. 在 VM 上运行此脚本后，在“选择虚拟机”中选择该 VM。 然后选择“发现 DB”。
6. Azure 备份可发现该 VM 上的所有 SAP HANA 数据库。 在发现期间，Azure Backup 将 VM 注册到保管库，并在该 VM 上安装扩展。 不会在数据库中安装任何代理。

   ![发现数据库](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>配置备份

发现我们要备份的数据库之后，现在可以启用备份。

1. 选择“配置备份”。

   ![配置备份](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. 在“选择要备份的项”中，选择一个或多个要保护的数据库，然后选择“确定” 。

   ![选择要备份的项](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. 在“备份策略”>“选择备份策略”中，按照下一部分中的说明，为数据库创建一个新的备份策略。

   ![选择备份策略](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. 创建策略后，在“备份菜单”中选择“启用备份” 。

   ![选择“启用备份”](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. 在门户的“通知”区域跟踪备份配置进度。

## <a name="creating-a-backup-policy"></a>创建备份策略

备份策略定义备份创建时间以及这些备份的保留时间。

* 策略是在保管库级别创建的。
* 多个保管库可以使用相同的备份策略，但必须向每个保管库应用该备份策略。

按以下方式指定策略设置：

1. 在“策略名称”处输入新策略的名称。 对于本例，请输入“SAPHANA”。

   ![输入新策略的名称](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. 在“完整备份策略”中选择一个 **备份频率**。 可以选择“每日”或“每周” 。 对于本教程，我们选择了“每日”备份。

   ![选择备份频率](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. 在“保持期”中，对完整备份配置保留设置。
   * 默认情况下，选择所有选项。 清除你不想使用的所有保持期限制，并设置要使用的选项。
   * 任何备份类型（完整/差异/日志）的最短保持期均为七天。
   * 恢复点已根据其保留范围标记为保留。 例如，如果选择每日完整备份，则每天只触发一次完整备份。
   * 根据每周保持期和设置，将会标记并保留特定日期的备份。
   * 每月和每年保留范围的行为类似。
4. 在“完整备份策略”菜单中，选择“确定”接受设置。  
5. 然后选择“差异备份”，以添加差异策略。
6. 在“差异备份策略”中，选择“启用”打开频率和保留控件。 我们在每个星期日的凌晨 2:00 启用了差异备份，保持期为 30 天  。

   ![差异备份策略](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >可以选择差异备份或增量备份作为每日备份，但不能同时选择两者。

7. 在“增量备份策略”中，选择“启用”以打开频率和保留控件 。
    * 每天最多可以触发一次增量备份。
    * 增量备份最多可以保留 180 天。 如果需要保留更长时间，必须使用完整备份。

    ![增量备份策略](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

8. 选择“确定”保存策略，并返回“备份策略”主菜单。  
9. 请选择“日志备份”，以添加事务日志备份策略。
   * “日志备份”默认设为“启用” 。 由于 SAP HANA 管理所有日志备份，此类备份无法被禁用。
   * 我们已将备份计划设置为 2 小时，保持期为 15 天 。

    ![日志备份策略](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > 日志备份仅在成功完成一次完整备份之后进行。
   >

10. 选择“确定”保存策略，并返回“备份策略”主菜单。  
11. 完成定义备份策略后，选择“确定”。

现已成功为 SAP HANA 数据库配置备份。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure VM 上运行的 SAP HANA 数据库上运行按需备份](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* 了解如何[还原在 Azure VM 上运行的 SAP HANA 数据库](sap-hana-db-restore.md)
* 了解如何[管理使用 Azure 备份进行备份的 SAP HANA 数据库](sap-hana-db-manage.md)
* 了解[关于 SAP HANA 数据库备份常见问题的疑难解答](backup-azure-sap-hana-database-troubleshoot.md)
