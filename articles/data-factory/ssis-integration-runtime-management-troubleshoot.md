---
title: 排查 SSIS Integration Runtime 管理问题
description: 本文提供有关排查 SSIS Integration Runtime (SSIS IR) 管理问题的指导
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 3419483442a208bd2ed15071ffcbd447dfe1bd26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736982"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>在 Azure 数据工厂中排查 SSIS Integration Runtime 管理问题

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文提供的故障排除指南针对 Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR)（简称 SSIS IR）中的管理问题。

## <a name="overview"></a>概述

如果在预配或取消预配 SSIS IR 时出现任何问题，则 Microsoft Azure 数据工厂门户中会显示错误消息，或者 PowerShell cmdlet 会返回一条错误。 错误始终以错误代码的格式显示，并附带详细的错误消息。

如果错误代码为 InternalServerError，则表示服务出现了暂时性的问题，应该在以后重试该操作。 如果重试不起作用，请联系 Azure 数据工厂支持团队。

否则，三个主要的外部依赖项可能会导致错误：Azure SQL 数据库或 Azure SQL 托管实例、自定义安装脚本以及虚拟网络配置。

## <a name="sql-database-or-sql-managed-instance-issues"></a>SQL 数据库或 SQL 托管实例问题

如果要使用 SSIS 目录数据库预配 SSIS IR，则需要 SQL 数据库或 SQL 托管实例。 SSIS IR 必须能够访问 SQL 数据库或 SQL 托管实例。 此外，SQL 数据库或 SQL 托管实例的登录帐户必须具有创建 SSIS 目录数据库 (SSISDB) 的权限。 如果出现错误，则会在数据工厂门户中显示带有详细 SQL 异常消息的错误代码。 使用以下列表中的信息来对错误代码进行故障排除。

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

在预配新的 SSIS IR 或 IR 正在运行时，可能会出现此问题。 如果在 IR 预配过程中遇到此错误，则可能会在错误消息中获取详细的 SqlException 消息，指示以下问题之一：

* 网络连接问题。 检查是否可以访问 SQL 数据库或 SQL 托管实例的主机名。 同时，确保没有防火墙或网络安全组 (NSG) 阻止 SSIS IR 访问服务器。
* SQL 身份验证期间登录失败。 提供的帐户无法登录 SQL Server 数据库。 请确保提供正确的用户帐户。
* Microsoft Azure Active Directory (Azure AD) 身份验证（托管标识）期间登录失败。 将工厂的托管标识添加到 AAD 组，并确保托管标识具有对目录数据库服务器的访问权限。
* 连接超时。 此错误始终是由与安全相关的配置引起的。 建议：
  1. 创建新 VM。
  1. 如果 IR 在虚拟网络中，请将 VM 加入相同的 IR Microsoft Azure 虚拟网络。
  1. 安装 SSMS 并检查 SQL 数据库或 SQL 托管实例的状态。

对于其他问题，请修复详细的 SQL 异常错误消息中显示的问题。 如果仍有问题，请联系 SQL 数据库或 SQL 托管实例支持团队。

如果在 IR 正在运行时出现错误，那么网络安全组或防火墙更改可能会阻止 SSIS IR 工作器节点访问 SQL 数据库或 SQL 托管实例。 取消阻止 SSIS IR 工作器节点，使其可以访问 SQL 数据库或 SQL 托管实例。

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

这种错误消息可能如下所示：“数据库 'SSISDB' 已达到大小配额。 请将数据分区或删除、删除索引，或查阅文档以找到可能的解决方法。” 

可能的解决方法包括：
* 增加 SSISDB 的配额大小。
* 通过以下方法更改 SSISDB 的配置以减小大小：
   * 缩短保留期，减少项目版本数。
   * 缩短日志的保持期。
   * 更改日志的默认级别。

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

此错误表示 SQL 数据库或 SQL 托管实例已有 SSISDB，并且它正被另一个 IR 使用。 需要提供不同的 SQL 数据库或 SQL 托管实例，或者删除现有 SSISDB 并重启新的 IR。

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

存在以下任一原因时，可能出现此错误：

* 为 SSIS IR 配置的用户帐户没有创建数据库的权限。 可以授予用户创建数据库的权限。
* 在数据库创建期间发生超时，如执行超时或 DB 操作超时。 应稍后重试此操作。 如果重试不起作用，请联系 SQL 数据库或 SQL 托管实例支持团队。

对于其他问题，请检查 SQL 异常错误消息，并修复错误详细信息中提到的问题。 如果仍有问题，请联系 SQL 数据库或 SQL 托管实例支持团队。

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

这种错误消息如下所示：“对象名称‘catalog.catalog_properties’无效。”在这种情况下，已经有一个名为 SSISDB 的数据库，但它不是由 SSIS IR 创建的，或者该数据库处于无效状态，这是由上一个 SSIS IR 预配中的错误引起的。 可以删除名称为 SSISDB 的现有数据库，也可以为 IR 配置新的 SQL 数据库或 SQL 托管实例。

## <a name="custom-setup-issues"></a>自定义安装问题

自定义安装提供了一个接口，用于在预配或重新配置 SSIS IR 时添加自己的安装步骤。 有关详细信息，请参阅[自定义 Azure-SSIS Integration Runtime 的安装](./how-to-configure-azure-ssis-ir-custom-setup.md)。

确保容器只包含必需的自定义安装文件，且容器中的所有文件都将下载到 SSIS IR 工作器节点。 建议在本地计算机上测试自定义安装脚本，以便在 SSIS IR 中运行脚本之前解决任何脚本执行问题。

在 IR 运行时，将检查自定义安装脚本容器，因为 SSIS IR 会定期更新。 此更新需要访问该容器才能下载自定义安装脚本并再次安装。 此过程还会检查容器是否可访问，以及 main.cmd 文件是否存在。

对于任何涉及自定义安装的错误，你将看到一个 CustomSetupScriptFailure 错误代码，其中包含 CustomSetupScriptBlobContainerInaccessible 或 CustomSetupScriptNotFound 之类的子代码。

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

此错误表示 SSIS IR 无法访问 Azure Blob 容器进行自定义安装。 确保可访问容器的 SAS URI 且其未过期。

如果 IR 正在运行，请停止它，使用新的自定义安装容器 SAS URI 重新配置 IR，然后重启 IR。

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

此错误表示 SSIS IR 在 Blob 容器中找不到自定义安装脚本 (main.cmd)。 确保容器中存在 main.cmd，这是自定义安装的入口点。

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

此错误表示自定义安装脚本 (main.cmd) 执行失败。 首先在本地计算机上尝试执行该脚本，或者检查 Blob 容器上的自定义安装执行日志。

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

此错误指示执行自定义安装脚本超时。 确保你的脚本可以在无提示的情况下执行，且无需任何交互式输入，并确保你的 blob 容器只包含必要的自定义安装文件。 建议先在本地计算机上测试脚本。 还应检查 Blob 容器中的自定义安装执行日志。 自定义安装的最长持续时间为 45 分钟，超过此时限则超时。这段时间包括从容器下载所有文件并将其安装在 SSIS IR 上的时间。 如果需要更长时间，请提交支持票证。

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

此错误表示尝试将自定义安装执行日志上传到 Blob 容器失败。 出现此问题可能是因为 SSIS IR 对 Blob 容器没有写入权限，也可能是因为存储或网络问题。 如果自定义安装成功，此错误不会影响任何 SSIS 功能，但日志将丢失。 如果自定义安装因另一个错误而失败，并且日志未上传，我们将首先报告此错误，以便可以上传日志进行分析。 另外，在解决此问题后，我们将报告更多具体问题（如有）。 如果重试后未解决此问题，请与 Azure 数据工厂支持团队联系。

## <a name="virtual-network-configuration"></a>虚拟网络配置

将 SSIS IR 加入 Azure 虚拟网络时，SSIS IR 使用用户订阅下的虚拟网络。 有关详细信息，请参阅[将 Azure-SSIS Integration Runtime 加入虚拟网络](./join-azure-ssis-integration-runtime-virtual-network.md)。
成功启动 SSIS IR 后，如果遇到网络连接问题，可以尝试使用[诊断连接工具](ssis-integration-runtime-diagnose-connectivity-faq.md)自行诊断问题。
出现与虚拟网络相关的问题时，将显示以下错误之一。

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

出现此错误有多种原因。 若要进行故障排除，请参阅[Forbidden](#forbidden)、[InvalidPropertyValue](#invalidpropertyvalue) 和 [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) 部分。

### <a name="forbidden"></a>禁止

这种错误可能与以下情况类似：“当前帐户不支持 SubnetId。 Microsoft.Batch 资源提供程序未在 VNet 的同一订阅下注册。”

这些详细信息表示 Azure Batch 无法访问虚拟网络。 在与虚拟网络相同的订阅下注册 Microsoft.Batch 资源提供程序。

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

这种错误可能类似于下面的一种情况： 

- “指定的 VNet 不存在，或 Batch 服务无权访问它。”
- “指定的子网 xxx 不存在。”

这些错误表示虚拟网络不存在，Azure Batch 服务无法访问它，或者提供的子网不存在。 请确保该虚拟网络和子网存在，并且 Azure Batch 可访问它们。

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

这种错误消息可能与以下情况类似：“未能在 VNet 中预配 Integration Runtime。 如果配置了 DNS 服务器或 NSG 设置，请确保 DNS 服务器可访问，并且 NSG 配置正确。”

在这种情况下，你可能有一个自定义的 DNS 服务器或 NSG 设置配置，这将阻止解析或访问 SSIS IR 所需的 Azure 服务器名称。 有关详细信息，请参阅 [SSIS IR 虚拟网络配置](./join-azure-ssis-integration-runtime-virtual-network.md)。 如果仍有问题，请联系 Azure 数据工厂支持团队。

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR 将定期自动更新。 升级期间将创建一个新的 Azure Batch 池，并删除旧的 Azure Batch 池。 此外，还将删除旧池的虚拟网络相关资源，并在订阅下创建新的虚拟网络相关资源。 此错误表示由于在订阅或资源组级别存在一个删除锁定，因此删除旧池的虚拟网络相关资源失败。 由于该删除锁定由客户控制和设置，因此在这种情况下，他们必须删除该删除锁定。

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

如果 SSIS IR 预配失败，则会删除创建的所有资源。 但是，如果订阅或资源组级别存在资源删除锁定，则不会按预期删除虚拟网络资源。 若要修复此错误，请删除删除锁定并重启 IR。

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

停止 SSIS IR 时，删除所有与虚拟网络相关的资源。 但如果在订阅或资源组级别存在资源删除锁定，删除可能会失败。 此处，该删除锁定也由客户控制和设置。 因此，他们必须删除该删除锁定，然后再次停止 SSIS IR。

### <a name="nodeunavailable"></a>NodeUnavailable

此错误发生在 IR 运行时，这意味着 IR 已变得不正常。 此错误始终是由 DNS 服务器或 NSG 配置中的更改引起的，该更改阻止 SSIS IR 连接到必要服务。 由于 DNS 服务器和 NSG 的配置由客户控制，因此客户必须自行修复阻止问题。 有关详细信息，请参阅 [SSIS IR 虚拟网络配置](./join-azure-ssis-integration-runtime-virtual-network.md)。 如果仍有问题，请联系 Azure 数据工厂支持团队。

## <a name="static-public-ip-addresses-configuration"></a>静态公共 IP 地址配置

将 Azure-SSIS IR 加入到 Azure 虚拟网络时，还可以为 IR 引入你自己的静态公共 IP 地址，以便 IR 可以访问仅限从特定 IP 地址访问的数据源。 有关详细信息，请参阅[将 Azure-SSIS Integration Runtime 加入虚拟网络](./join-azure-ssis-integration-runtime-virtual-network.md)。

除了以上虚拟网络问题之外，你还可以解决与静态公共 IP 地址相关的问题。 请检查以下错误以获得帮助。

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

启动 Azure-SSIS IR 时可能会由于多种原因而出现此问题：

| 错误消息 | 解决方案|
|:--- |:--- |
| 提供的静态公共 IP 地址已被使用，请为你的 Azure-SSIS Integration Runtime 提供两个未使用的静态公共 IP 地址。 | 你应当选择两个未使用的静态公共 IP 地址，或者删除对指定公共 IP 地址的当前引用，然后重启 Azure-SSIS IR。 |
| 提供的静态公共 IP 地址没有 DNS 名称，请为你的 Azure-SSIS Integration Runtime 提供两个具有 DNS 名称的静态公共 IP 地址。 | 可以在 Azure 门户中设置公共 IP 地址的 DNS 名称，如下图所示。 具体步骤如下所述：(1) 打开 Azure 门户并转到此公共 IP 地址的资源页；(2) 选择“配置”部分并设置 DNS 名称，然后单击“保存”按钮；(3) 重启你的 Azure-SSIS IR。 |
| 为 Azure-SSIS Integration Runtime 提供的 VNet 和静态公共 IP 地址必须位于同一位置。 | 根据 Azure 网络的要求，静态公共 IP 地址和虚拟网络应当位于同一位置和订阅中。 请提供两个有效的静态公共 IP 地址，然后重启 Azure-SSIS IR。 |
| 提供的静态公共 IP 地址是一个基本地址，请为你的 Azure-SSIS Integration Runtime 提供两个标准静态公共 IP 地址。 | 有关帮助信息，请参阅[公共 IP 地址的 SKU](../virtual-network/public-ip-addresses.md#sku)。 |

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

如果 Azure-SSIS IR 预配失败，则会删除创建的所有资源。 但是，如果在订阅或资源组（包含你的静态公共 IP 地址）级别存在资源删除锁，则不会按预期删除网络资源。 若要修复此错误，请移除删除锁并重启 IR。

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

停止 Azure-SSIS IR 时，在包含你的公共 IP 地址的资源组中创建的所有网络资源都将被删除。 但是，如果在订阅或资源组（包含你的静态公共 IP 地址）级别存在资源删除锁，则删除可能会失败。 请移除删除锁并重启 IR。

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR 会定期自动更新。 升级期间会创建新的 IR 节点，而旧节点将被删除。 此外，将会删除旧节点的已创建网络资源（例如负载均衡器和网络安全组），并在你的订阅下创建新的网络资源。 此错误意味着，由于在订阅或资源组（包含你的静态公共 IP 地址）级别存在删除锁，删除旧节点的网络资源失败。 请移除删除锁，以便可以清理旧节点并释放旧节点的静态公共 IP 地址。 否则，将无法释放静态公共 IP 地址，无法进一步升级 IR。

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

如果要引入你自己的静态公共 IP 地址，应提供两个公共 IP 地址。 其中一个将立即用来创建 IR 节点，另一个将在升级 IR 期间使用。 如果在升级期间另一个公共 IP 地址不可用，则会出现此错误。 有关可能的原因，请参阅 [InvalidPublicIPSpecified](#InvalidPublicIPSpecified)。