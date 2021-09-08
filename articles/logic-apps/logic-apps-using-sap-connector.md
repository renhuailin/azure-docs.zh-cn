---
title: 连接到 SAP 系统
description: 使用 Azure 逻辑应用将工作流自动化，以访问和管理 SAP 资源。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, azla
ms.topic: how-to
ms.date: 08/31/2021
tags: connectors
ms.openlocfilehash: 32f51d110f1ebb8d7b0a39a3183665c65d2b1367
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469547"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 SAP 系统

本文介绍如何使用 [SAP 连接器](/connectors/sap/)访问 Azure 逻辑应用中的 SAP 资源。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 要在其中访问 SAP 资源的逻辑应用工作流。 如果不熟悉 Azure 逻辑应用，请参阅 [Azure 逻辑应用服务概述](logic-apps-overview.md)和[关于在 Azure 门户中创建首个逻辑应用工作流的快速入门](quickstart-create-first-logic-app-workflow.md)。

  * 如果你使用的是以前版本的 SAP 连接器（已弃用），则必须先[迁移到当前连接器](#migrate-to-current-connector)，然后才能连接到 SAP 服务器。

  * 如果要在多租户 Azure 中运行逻辑应用工作流，请参阅[多租户先决条件](#multi-tenant-azure-prerequisites)。

  * 如果你正在高级[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中运行逻辑应用工作流，请参阅 [ISE 先决条件](#ise-prerequisites)。

* 要从 Azure 逻辑应用访问的 [SAP 应用程序服务器](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)或 [SAP 消息服务器](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)。 有关支持此连接器的 SAP 服务器的信息，请查看 [SAP 兼容性](#sap-compatibility)。

  > [!IMPORTANT]
  > 请确保将你的 SAP 服务器和用户帐户设置为允许使用 RFC。 如要了解包括受支持的用户帐户类型和每种操作类型所需的最低授权 (RFC、BAPI、IDOC) 等更多详情，请查看以下 SAP 说明：[460089 - 适用于外部 RFC 计划的最低授权配置文件](https://launchpad.support.sap.com/#/notes/460089)。 
  >
  > * 对于 RFC 操作，用户帐户还需要具有访问函数模块 `RFC_GROUP_SEARCH` 和 `DD_LANGU_TO_ISOLA` 的权限。
  > * 对于 BAPI 操作，用户帐户还需要具有访问以下函数模块的权限：`BAPI_TRANSACTION_COMMIT`、`BAPI_TRANSACTION_ROLLBACK`、`RPY_BOR_TREE_INIT`、`SWO_QUERY_METHODS` 和 `SWO_QUERY_API_METHODS`。
  > * 对于 IDOC 操作，用户帐户还需要具有访问以下函数模块的权限：`IDOCTYPES_LIST_WITH_MESSAGES`、`IDOCTYPES_FOR_MESTYPE_READ`、`INBOUND_IDOCS_FOR_TID`、`OUTBOUND_IDOCS_FOR_TID`、`GET_STATUS_FROM_IDOCNR` 和 `IDOC_RECORD_READ`。
  > * 对于“读取表格”操作，用户帐户还需要具有访问下列“其中一个”函数模块的权限：`RFC BBP_RFC_READ_TABLE` 或 `RFC_READ_TABLE`。

* 要发送到 SAP 服务器的消息内容，例如示例 IDoc 文件。 此内容必须采用 XML 格式，并且包含要使用的 [SAP 操作](#actions)的命名空间。 你可以[通过将 IDoc 包装在 XML 信封中来发送带有平面文件架构的 IDoc](#send-flat-file-idocs)。

* 如果想要使用“从 SAP 收到消息时”触发器，必须完成以下任务：

  * 设置 SAP 网关的安全权限或访问控制列表 (ACL)。 在“secinfo”和“reginfo”文件（在网关监视器对话框“T-Code SMGW”中可见）中，依序执行以下操作：“转到 > 专家函数 > 外部安全性 > ACL 文件的维护”。 需要以下权限设置：

    `P TP=LOGICAPP HOST=<on-premises-gateway-server-IP-address> ACCESS=*`

    此行格式如下：

    `P TP=<trading-partner-identifier-(program-name)-or-*-for-all-partners> HOST=<comma-separated-list-with-external-host-IP-or-network-names-that-can-register-the-program> ACCESS=<*-for-all-permissions-or-a-comma-separated-list-of-permissions>`

    如果未配置 SAP 网关安全权限，则可能会收到此错误：

    `Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed`

    有关详细信息，请参阅 [SAP 说明 1850230 - GW：“不允许注册事务程序&lt;程序 ID&gt;”](https://userapps.support.sap.com/sap/support/knowledge/en/1850230)。

  * 设置 SAP 网关安全日志记录，以帮助查找访问控制列表 (ACL) 问题。 有关详细信息，请参阅[设置网关日志记录的 SAP 帮助主题](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)。

  * 在“RFC 连接配置”(T-Code SM59) 对话框中，使用“TCP/IP”类型创建 RFC 连接。 “激活类型”必须是“注册服务器程序”。 将 RFC 连接的“与目标系统的通信类型”值设为“Unicode”。

  * 如果使用“IDOC 格式”参数设置为“FlatFile”的 SAP 触发器和[“平面文件解码”操作](logic-apps-enterprise-integration-flatfile.md)，则必须在平面文件架构中使用 `early_terminate_optional_fields` 属性，并将值设为 `true`。

    因为在 tRFC 调用 `IDOC_INBOUND_ASYNCHRONOUS` 中由 SAP 发送的平面文件 IDoc 数据记录不会填充到完整的 SDATA 字段长度，所以这是必要要求。 Azure 逻辑应用会提供未填充的平面文件 IDoc 原始数据，与接收自 SAP 的格式相同。 此外，将此 SAP 触发器与平面文件解码操作组合使用时，提供给操作的架构必须与之相匹配。

  > [!NOTE]
  > 此 SAP 触发器使用同一 URI 位置来续订和取消订阅 Webhook。 续订操作使用 HTTP `PATCH` 方法，而取消订阅操作使用 HTTP `DELETE` 方法。 此行为可能会使续订操作在触发器历史记录中显示为取消订阅操作，但实际上它仍然是续订操作，因为触发器使用 `PATCH` 作为 HTTP 方法，而不是 `DELETE`。

### <a name="sap-compatibility"></a>SAP 兼容性

SAP 连接器与以下类型的 SAP 系统兼容：

* 本地和基于云的 SAP HANA 系统，例如 S/4 HANA。

* 经典本地 SAP 系统，例如 R/3 和 ECC。

SAP 连接器支持基于 SAP NetWeaver 的系统中的以下消息和数据集成类型：

* 中间文档 (IDoc)

* 商业应用程序编程接口 (BAPI)

* 远程函数调用 (RFC) 和事务 RFC (tRFC)

SAP 连接器使用 [SAP .NET 连接器 (NCo) 库](https://support.sap.com/en/product/connectors/msnet.html)。

若要使用可用的 [SAP 触发器](#triggers)和 [SAP 操作](#actions)，需要首先对连接进行身份验证。 你可以使用用户名和密码对连接进行身份验证。 SAP 连接器也支持通过 [SAP 安全网络通信 (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true) 来进行身份验证。 可以将 SNC 用于 SAP NetWeaver 单一登录 (SSO)，或用于外部产品的附加安全功能。 如果使用 SNC，请查看 [SNC 先决条件](#snc-prerequisites)和 [ISE 连接器的 SNC 先决条件](#snc-prerequisites-ise)。

### <a name="migrate-to-current-connector"></a>迁移到最新连接器

以前的 SAP 应用程序服务器和 SAP 消息服务器连接器已于 2020 年 2 月 29 日弃用。 若要迁移到当前 SAP 连接器，请执行以下步骤：

1. 将[本地数据网关](https://www.microsoft.com/download/details.aspx?id=53127)更新到当前版本。 有关详细信息，请参阅[为 Azure 逻辑应用安装本地数据网关](logic-apps-gateway-install.md)。

1. 在使用已弃用 SAP 连接器的逻辑应用工作流中，删除“发送到 SAP”操作。

1. 在当前 SAP 连接器中添加“将消息发送到 SAP”操作。

1. 通过新操作重新连接到 SAP 系统。

1. 保存逻辑应用工作流。 在设计器工具栏上选择“保存”。

## <a name="multi-tenant-azure-prerequisites"></a>多租户 Azure 先决条件

如果你的逻辑应用工作流在多租户 Azure 中运行，则适用这些先决条件。 托管 SAP 连接器不会以本机方式在 [ISE](connect-virtual-network-vnet-isolated-environment-overview.md) 中运行。

> [!TIP]
> 如果你使用的是高级 ISE，则可以使用 SAP ISE 连接器，而不是托管 SAP 连接器。 有关详细信息，请参阅 [ISE 先决条件](#ise-prerequisites)。

托管 SAP 连接器通过[本地数据网关](logic-apps-gateway-connection.md)与 SAP 系统集成。 例如，在“发送消息”方案中，如果将消息从逻辑应用工作流发送到 SAP 系统，则数据网关将充当 RFC 客户端，将从逻辑应用工作流收到的请求转发到 SAP。 同理，在“接收消息”方案中，数据网关将充当 RFC 服务器，从 SAP 接收请求并将其转发到逻辑应用工作流。

1. 在主机或虚拟机上[下载并安装本地数据网关](logic-apps-gateway-install.md)，该主机或虚拟机与要连接的 SAP 系统位于同一虚拟网络中。

1. 在 Azure 门户中为本地数据网关[创建 Azure 网关资源](logic-apps-gateway-connection.md#create-azure-gateway-resource)。 网关有助于安全访问本地数据和资源。 请确保使用受支持版本的网关。

  > [!TIP]
  > 如果网关遇到问题，请尝试[升级到最新版本](https://aka.ms/on-premises-data-gateway-installer)，其中可能包括可解决问题的更新。

1. [下载最新的 SAP 客户端库](#sap-client-library-prerequisites)并将其与本地数据网关安装在同一台本地计算机上。

1. 为安装本地数据网关的主机配置网络主机名称和服务名称解析。

  从 Azure 逻辑应用连接时如果要使用主机名或服务名称，则必须设置每个 SAP 应用程序、消息和网关服务器及其服务，以进行名称解析。 网络主机名称解析是在 `%windir%\System32\drivers\etc\hosts` 文件中或在本地数据网关主机可用的 DNS 服务器中配置。 服务名称解析是在 `%windir%\System32\drivers\etc\services` 中配置。 如果你不打算使用网络主机名或服务名称进行连接，则可以改用主机 IP 地址和服务端口号。

   如果没有适用 SAP 系统的 DNS 条目，请参阅下例中适合主机文件的条目示例：

   ```text
   10.0.1.9           sapserver                   # SAP single-instance system host IP by simple computer name
   10.0.1.9           sapserver.contoso.com       # SAP single-instance system host IP by fully qualified DNS name
   ```

   适合服务文件的条目示例集如下：

   ```text
   sapdp00            3200/tcp              # SAP system instance 00 dialog (application) service port
   sapgw00            3300/tcp              # SAP system instance 00 gateway service port
   sapmsDV6           3601/tcp              # SAP system ID DV6 message service port
   ```

## <a name="ise-prerequisites"></a>ISE 先决条件

ISE 提供对受 Azure 虚拟网络保护的资源的访问权限，并提供其他 ISE 本机连接器，这些连接器让逻辑应用工作流无需使用本地数据网关即可直接访问本地资源。

1. 如果还没有带有 blob 容器的 Azure 存储帐户，请使用 [Azure 门户](../storage/blobs/storage-quickstart-blobs-portal.md)或 [Azure 存储资源管理器](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)创建一个容器。

1. 在本地计算机上[下载并安装最新的 SAP 客户端库](#sap-client-library-prerequisites)。 应具有以下程序集文件：

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. 创建一个包含这些程序集文件的 .zip 文件。 将包上传到 Azure 存储中的 blob 容器。

1. 在 Azure 门户或 Azure 存储资源管理器中，浏览到上传 .zip 文件的容器位置。

1. 复制容器位置的 URL。 请确保包含共享访问签名 (SAS) 令牌，以便对 SAS 令牌进行授权。 否则，SAP ISE 连接器的部署将失败。

1. 在 ISE 中安装并部署 SAP 连接器。 有关详细信息，请参阅[添加 ISE 连接器](add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)。

   1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开 ISE。

   1. 在 ISE 菜单上，选择“托管连接器”&gt;“添加” 。 在连接器列表中，找到并选择“SAP”。

   1. 在“添加新的托管连接器”窗格的“SAP 包”框中，粘贴包含 SAP 程序集的 .zip 文件的 URL 。 同样，请确保包含 SAS 令牌。

   1. 选择“创建”以完成创建 ISE 连接器。

1. 如果 SAP 实例和 ISE 位于不同的虚拟网络中，则还需要[将这些网络对等互连](../virtual-network/tutorial-connect-virtual-networks-portal.md)，以便它们连接在一起。 另请参阅 [ISE 连接器的 SNC 先决条件](#snc-prerequisites-ise)。

1. 获取从逻辑应用工作流连接时计划要使用的 SAP 应用程序、消息和网关服务器的 IP 地址。 网络名称解析不适用于 ISE 中的 SAP 连接。

1. 获取与逻辑应用连接时要使用的 SAP 应用程序、消息和网关服务的端口号。 服务名称解析不适用于 ISE 中的 SAP 连接器。

### <a name="sap-client-library-prerequisites"></a>SAP 客户端库先决条件

下面列出与连接器一起使用的 SAP 客户端库的先决条件：

* 请确保安装最新版本，[使用 .NET Framework 4.0 编译的 Microsoft .NET 3.0.24.0 的 SAP 连接器 (NCo 3.0) - Windows 64 位 (x64)](https://support.sap.com/en/product/connectors/msnet.html)。 早期版本的 SAP NCo 可能会遇到以下问题：

  * 当同时发送多个 IDoc 消息时，此条件会阻止所有稍后发送到 SAP 目标的消息，从而导致消息出现超时问题。

  * 会话激活可能会由于会话泄漏而失败。 此条件可能会阻止 SAP 发送到逻辑应用工作流触发器的调用。

  * 本地数据网关（2021 年 6 月版本）需要使用 SAP NCo 中的 `SAP.Middleware.Connector.RfcConfigParameters.Dispose()` 方法来释放资源。

* 必须安装 64 位版本的 SAP 客户端库，因为数据网关仅在 64 位系统上运行。 安装不受支持的 32 位版本会导致“错误的映像”错误。

* 根据你的场景，将程序集文件从默认安装文件夹复制到其他位置，如下所示。

  * 对于在 ISE 中运行的逻辑应用工作流，请遵循 [ISE 先决条件](#ise-prerequisites)。

  * 对于在多租户 Azure 中运行并使用本地数据网关的逻辑应用工作流，请将程序集文件复制到数据网关安装文件夹。 

    > [!NOTE]
    > 如果 SAP 连接失败并显示错误消息，请检查你的帐户信息和/或权限，然后重试，确保已将程序集文件复制到数据网关安装文件夹。
    > 
    > 可以使用 [.NET 程序集绑定日志查看器](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)来排除进一步的问题。 
    > 使用此工具可以检查程序集文件是否位于正确的位置。 
  
  * 在安装 SAP 客户端库时，可以选择“全局程序集缓存注册”选项。

请注意 SAP 客户端库、.NET Framework、.NET 运行时和网关之间的以下关系：

* Microsoft SAP 适配器和网关主机服务都使用 .NET Framework 4.7.2。

* SAP NCo for .NET Framework 4.0 适用于使用 .NET 运行时 4.0 到 4.8 的进程。

* SAP NCo for .NET Framework 2.0 适用于使用 .NET 运行时 2.0 到 3.5 的进程，但不再适用于最新网关。

### <a name="snc-prerequisites"></a>SNC 先决条件

如果将本地数据网关与可选 SNC 一起使用（仅在多租户 Azure 中受支持），则必须配置这些附加设置。 如果使用的是 ISE，请查看 [ISE 连接器的 SNC 先决条件](#snc-prerequisites-ise)

如果将 SNC 用于 SSO，请确保以映射到 SAP 用户的用户身份运行数据网关服务。 若要更改默认帐户，请选择“更改帐户”并输入用户凭据。 

![屏幕截图：显示 Azure 门户的本地数据网关设置和“服务设置”页面，已选中“更改网关服务帐户”按钮。](./media/logic-apps-using-sap-connector/gateway-account.png)

如果要通过外部安全产品启用 SNC，请在安装数据网关的同一台计算机上复制 SNC 库或文件。 SNC 产品的部分示例包括 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos 和 NTLM。 有关为数据网关启用 SNC 的详细信息，请参阅[启用安全网络通信 (SNC)](#enable-secure-network-communications)。

> [!TIP]
> SNC 库的版本及其依赖项必须与 SAP 环境兼容。
>
> * 必须专门使用 `sapgenpse.exe` 作为 SAPGENPSE 实用工具。
> * 如果使用本地数据网关，则还要将这些相同的二进制文件复制到安装文件夹中。
> * 如果连接中提供了 PSE，则无需为本地数据网关复制和设置 PSE 和 SECUDIR。
> * 你还可使用本地数据网关来排查任何库兼容性问题。

#### <a name="snc-prerequisites-ise"></a>SNC 先决条件 (ISE)

SAP 连接器的 ISE 版本支持 SNC X.509。 可以通过以下步骤为 SAP ISE 连接启用 SNC：

> [!IMPORTANT]
> 在重新部署现有 SAP 连接器以使用 SNC 之前，必须删除与旧连接器的所有连接。 多个逻辑应用工作流可以使用相同的 SAP 连接。 因此，必须从 ISE 中的所有逻辑应用工作流中删除任何 SAP 连接。 然后，必须删除旧连接器。
>
> 删除旧连接器时，仍可以保留使用此连接器的逻辑应用工作流。 重新部署连接器后，可以在 SAP 触发器和这些逻辑应用工作流中的操作中对新连接进行身份验证。

首先，如果已部署不带 SNC 或 SAPGENPSE 库的 SAP 连接器，请删除所有连接和连接器。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从逻辑应用工作流中删除与 SAP 连接器的所有连接。

   1. 在 Azure 门户中，打开你的逻辑应用资源。

   1. 在逻辑应用菜单的“开发工具”下，选择“API 连接”。

   1. 在“API 连接”页面上，选择你的 SAP 连接。

   1. 在连接的页面菜单中，选择“删除”。

   1. 接受确认提示以删除连接。

   1. 等待出现已删除连接的门户通知。

1. 或者，从 ISE 的 API 连接中删除与 SAP 连接器的连接。

   1. 在 Azure 门户中打开 ISE 资源。

   1. 在 ISE 菜单的“设置”下，选择“API 连接”。

   1. 在“API 连接”页面上，选择你的 SAP 连接。

   1. 在连接的页面菜单中，选择“删除”。

   1. 接受确认提示以删除连接。

   1. 等待出现已删除连接的门户通知。

接下来，从 ISE 中删除 SAP 连接器。 必须先删除所有逻辑应用中与此连接器建立的所有连接，然后才能删除该连接器。 如果尚未删除所有连接，请查看前面的步骤。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 再次在 Azure 门户中打开 ISE 资源。

1. 在 ISE 菜单的“设置”下，选择“托管连接器”。

1. 在“托管连接器”页面上，选中 SAP 连接器的复选框。

1. 在工具栏中选择“删除”。

1. 接受确认提示以删除连接器。

1. 等待出现已删除连接器的门户通知。

接下来，在 ISE 中部署或重新部署 SAP 连接器：

1. 准备一个新的 zip 存档文件，以备部署 SAP 连接器时使用。 必须包含 SNC 库和 SAPGENPSE 实用工具。

   1. 将所有 SNC、SAPGENPSE 和 NCo 库复制到 zip 存档的根文件夹中。 不要将这些二进制文件放在子文件夹中。

   1. 必须使用 64 位版本的 SNC 库。 不支持 32 位版本。

   1. SNC 库及其依赖项必须与 SAP 环境兼容。 若要了解如何检查兼容性，请参阅 [ISE 先决条件](#ise-prerequisites)。

1. 使用新的 zip 存档，按照 [ISE 先决条件](#ise-prerequisites)中的部署步骤操作。

最后，在使用 SAP 连接器的所有逻辑应用中创建使用 SNC 的新连接。 对于每个连接，请执行以下步骤：

1. 再次在工作流设计器中打开工作流。

1. 创建或编辑使用 SAP 连接器的步骤。

1. 输入有关 SAP 连接的必需信息。

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings.png" alt-text="屏幕截图：工作流设计器，其中显示 SAP 连接设置。" lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings.png":::

   > [!NOTE]
   > “SAP 用户名”和“SAP 密码”字段均为可选。 如果未提供用户名和密码，连接器将使用稍后步骤中提供的客户端证书进行身份验证。

1. 启用 SNC。

   1. 对于“使用 SNC”，选中此复选框。

   1. 对于“SNC 库”，请输入 SNC 库的名称。 例如 `sapcrypto.dll`。

   1. 对于“SNC 合作伙伴名称”，请输入后端的 SNC 名称。 例如 `p:CN=DV3, OU=LA, O=MS, C=US`。

   1. 对于“SNC 证书”，请输入 base64 编码格式的 SNC 客户端公共证书。 请勿包含 PEM 页眉或页脚。

   1. 可以视需要输入“SNC 我的名称”、“SNC 保护质量”的 SNC 设置。

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png" alt-text="屏幕截图：显示新 SAP 连接的工作流设计器和 SNC 配置设置。" lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png":::

1. 配置 PSE 设置。 对于“PSE”，请输入 SNC PSE 作为 base64 编码的二进制文件。

   * PSE 必须包含专用客户端证书，该证书的指纹与上一步中提供的公共客户端证书匹配。

   * PSE 可能包含其他客户端证书。

   * PSE 不得具有 PIN。 如果需要，使用 SAPGENPSE 实用工具将 PIN 设置为空。

   对于证书轮换，请执行以下步骤：

   1. 针对 ISE 中使用 SAP ISE X.509 的所有连接更新 base64 编码的二进制 PSE。

   1. 将新证书导入 PSE 的副本。

   1. 将 PSE 文件编码为 base64 编码的二进制文件。

   1. 编辑 SAP 连接器的 API 连接，并保存新的 PSE 文件。

   连接器会检测 PSE 更改，并在下次连接请求期间更新自己的副本。

   > [!NOTE]
   > 如果为 ISE 使用多个 SNC 客户端证书，则必须为所有连接提供相同的 PSE。 你可以将客户端公共证书参数设置为“为 ISE 中使用的每个连接指定证书”。

1. 选择“创建”以创建连接。 如果参数正确，系统会创建连接。 如果参数出现问题，连接创建对话框将显示错误消息。

   > [!TIP]
   > 若要排查连接参数问题，可以使用本地数据网关和网关的本地日志。

1. 请在工作流设计器工具栏上选择“保存”，以保存更改。

## <a name="send-idoc-messages-to-sap-server"></a>将 IDoc 消息发送到 SAP 服务器

按照以下示例创建一个逻辑应用工作流，向 SAP 服务器发送 IDoc 消息并返回响应：

1. [创建由 HTTP 请求触发的逻辑应用工作流。](#add-http-request-trigger)

1. [在工作流中创建操作，以将消息发送到 SAP。](#create-sap-action-to-send-message)

1. [在工作流中创建 HTTP 响应操作。](#create-http-response-action)

1. [如果要使用 RFC 接收来自 SAP ABAP 的响应，请创建远程函数调用 (RFC) 请求-响应模式。](#create-rfc-request-response)

1. [测试逻辑应用。](#test-your-logic-app-workflow)

<a name="add-http-request-trigger"></a>

### <a name="add-an-http-request-trigger"></a>添加 HTTP 请求触发器

若要让逻辑应用工作流通过 XML HTTP 从 SAP 接收 IDoc，可以使用[请求触发器](../connectors/connectors-native-reqres.md)。

> [!TIP]
> 若要通过公共编程接口通信 (CPIC) 接收普通 XML 或平面文件格式的 IDoc，请参阅[从 SAP 接收消息](#receive-message-sap)部分。

本部分将继续使用“请求”触发器，首先使用 Azure 中的终结点创建逻辑应用工作流，以将“HTTP POST”请求发送到工作流。 当逻辑应用工作流收到这些 HTTP 请求时，触发器将会触发并运行工作流中的下一个步骤。

1. 在 [Azure 门户](https://portal.azure.com)中创建一个空白的逻辑应用，此时会打开工作流设计器。

1. 在搜索框中，输入 `http request` 作为筛选器。 在触发器列表中，选择“当收到 HTTP 请求时”。  

   ![屏幕截图：显示工作流设计器，正在将新的请求触发器添加到逻辑应用工作流中。](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 保存逻辑应用工作流，该工作流将生成可接收请求的终结点 URL。 在设计器工具栏上选择“保存”。 此时，终结点 URL 会显示在触发器中。

   ![屏幕截图：显示工作流设计器，请求触发器中正在复制生成的 POST URL。](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>创建 SAP 操作以发送消息

接下来，创建一个操作，以便在[请求触发器](#add-http-request-trigger)触发时向 SAP 发送 IDoc 消息。 默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。  详细了解[“安全类型化”选项](#safe-typing)。

1. 在工作流设计器中的触发器下，选择“新建步骤”。

   ![屏幕截图：显示工作流设计器，正在编辑工作流以添加新步骤。](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜索框中，输入 `send message sap` 作为筛选器。 在“操作”列表中选择“将消息发送到 SAP”。  

   ![屏幕截图：显示工作流设计器，已选中“将消息发送到 SAP”操作。](./media/logic-apps-using-sap-connector/select-sap-send-action.png)

   也可以选择“企业”选项卡，然后选择 SAP 操作。 

   ![屏幕截图：显示工作流设计器，已在“企业”选项卡下选中“将消息发送到 SAP”操作。](./media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 如果连接已存在，请继续执行下一步。 如果系统提示你创建新的连接，请提供以下信息以连接到你的本地 SAP 服务器。

   1. 为连接提供一个名称。

   1. 如果使用数据网关，请执行以下步骤：

      1. 在“数据网关”部分的“订阅”下，首先选择在 Azure 门户中为数据网关安装创建的数据网关资源的 Azure 订阅 。

      1. 在“连接网关”下，选择 Azure 中的数据网关资源。

   1. 对于“登录类型”属性，请根据该属性是设置为“应用程序服务器”还是“组”来执行相关步骤。

      * 对于“应用程序服务器”，必须指定以下属性（通常显示为可选）： 

        ![屏幕截图：显示如何创建 SAP 应用程序服务器连接。](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 对于“组”，必须指定以下属性（通常显示为可选）： 

        ![屏幕截图：显示如何创建 SAP 消息服务器连接。](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

        在 SAP 中，通过打开“CCMS：维护登录组”(T-Code SMLG) 对话框来维护登录组。 有关详细信息，请参阅 [SAP 说明 26317 - 为登录组设置自动负载均衡](https://service.sap.com/sap/support/notes/26317)。

      默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。  详细了解[“安全类型化”选项](#safe-typing)。

   1. 完成后，选择“创建”  。

      Azure 逻辑应用会设置并测试连接，以确保连接正常工作。

      > [!NOTE]
      > 如果收到以下错误，则说明 SAP NCo 客户端库的安装存在问题： 
      >
      > **测试连接失败。错误“无法处理请求”。错误详细信息：“无法加载文件或程序集 'sapnco, Version=3.0.0.42, Culture=neutral, PublicKeyToken 50436dca5c7f7d23' 或某个依赖项。系统找不到指定文件。”**
      >
      > 确保[安装所需版本的 SAP NCo 客户端库并满足所有其他先决条件](#sap-client-library-prerequisites)。

1. 现在，请找到并选择 SAP 服务器中的某个操作。

   1. 在“SAP 操作”框中，选择文件夹图标。  在文件列表中，找到并选择要使用的 SAP 消息。 使用箭头可在列表中导航。

      此示例选择了类型为“订单”的 IDoc。 

      ![屏幕截图：显示查找和选择 IDoc 操作。](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      如果找不到所需的操作，可以手动输入路径，例如：

      ![屏幕截图：显示手动提供 IDoc 操作的路径。](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 通过表达式编辑器提供“SAP 操作”的值。  这样，便可以对不同的消息类型使用相同的操作。

      有关 IDoc 操作的详细信息，请参阅 [IDoc 操作的消息架构](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

   1. 在“输入消息”框中单击，以显示动态内容列表。  在该列表中的“收到 HTTP 请求时”下面，选择“正文”字段。  

      此步骤包含来自 HTTP 请求触发器的正文内容，并会将该输出发送到 SAP 服务器。

      ![屏幕截图：显示在请求触发器中选择“正文”属性。](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      完成后，SAP 操作如以下示例所示：

      ![屏幕截图：显示完成 SAP 操作。](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 保存逻辑应用工作流。 在设计器工具栏上选择“保存”。

### <a name="send-flat-file-idocs"></a>发送平面文件 IDoc

如果将 IDoc 包装在 XML 信封中，则可以将其与平面文件架构一起使用。 若要发送平面文件 IDoc，请使用一般说明[创建 SAP 操作，以发送 IDoc 消息](#create-sap-action-to-send-message)，并进行以下更改。

1. 对于“将消息发送到 SAP”操作，请使用 SAP 操作 URI `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc`。

1. 使用 XML 信封设置输入消息的格式。

有关示例，请参阅以下示例 XML 有效负载：

```xml
<ReceiveIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
<idocData>EDI_DC 3000000001017945375750 30INVOIC011BTSVLINV30KUABCABCFPPC LDCA X004010810 4 SAPMSX LSEDI ABCABCFPPC 000d3ae4-723e-1edb-9ca4-cc017365c9fd 20210217054521INVOICINVOIC01ZINVOIC2RE 20210217054520
E2EDK010013000000001017945375000001E2EDK01001000000010 ABCABC1.00000 0060 INVO9988298128 298.000 298.000 LB Z4LR EN 0005065828 L
E2EDKA1 3000000001017945375000002E2EDKA1 000000020 RS ABCABCFPPC 0005065828 ABCABCABC ABCABC Inc. Limited Risk Distributor ABCABC 1950 ABCABCABCA Blvd ABCABAABCAB L5N8L9 CA ABCABC E ON V-ABCABC LDCA
E2EDKA1 3000000001017945375000003E2EDKA1 000000020 AG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000004E2EDKA1 000000020 RE 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000005E2EDKA1 000000020 RG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000006E2EDKA1 000000020 WE 0005001847 41 ABCABC ABCABC INC (ABCABC) DC A. ABCABCAB 88 ABCABC CRESCENT ABCABAABCAB L5R 4A2 CA ABCABC 111-111-1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000007E2EDKA1 000000020 Z3 0005533050 ABCABCABC ABCABC Inc. ABCA Bank Swift Code -ABCABCABCAB Sort Code - 1950 ABCABCABCA Blvd. Acc No -1111111111 ABCABAABCAB L5N8L9 CA ABCABC E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000008E2EDKA1 000000020 BK 1075 ABCABCABC ABCABC Inc 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDKA1 3000000001017945375000009E2EDKA1 000000020 CR 1075 CONTACT ABCABCABC 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDK02 3000000001017945375000010E2EDK02 000000020 0099988298128 20210217
E2EDK02 3000000001017945375000011E2EDK02 000000020 00140-N6260-S 20210205
E2EDK02 3000000001017945375000012E2EDK02 000000020 0026336270425 20210217
E2EDK02 3000000001017945375000013E2EDK02 000000020 0128026580537 20210224
E2EDK02 3000000001017945375000014E2EDK02 000000020 01740-N6260-S
E2EDK02 3000000001017945375000015E2EDK02 000000020 900IAC
E2EDK02 3000000001017945375000016E2EDK02 000000020 901ZSH
E2EDK02 3000000001017945375000017E2EDK02 000000020 9078026580537 20210217
E2EDK03 3000000001017945375000018E2EDK03 000000020 02620210217
E2EDK03 3000000001017945375000019E2EDK03 000000020 00120210224
E2EDK03 3000000001017945375000020E2EDK03 000000020 02220210205
E2EDK03 3000000001017945375000021E2EDK03 000000020 01220210217
E2EDK03 3000000001017945375000022E2EDK03 000000020 01120210217
E2EDK03 3000000001017945375000023E2EDK03 000000020 02420210217
E2EDK03 3000000001017945375000024E2EDK03 000000020 02820210418
E2EDK03 3000000001017945375000025E2EDK03 000000020 04820210217
E2EDK17 3000000001017945375000026E2EDK17 000000020 001DDPDelivered Duty Paid
E2EDK17 3000000001017945375000027E2EDK17 000000020 002DDPdestination
E2EDK18 3000000001017945375000028E2EDK18 000000020 00160 0 Up to 04/18/2021 without deduction
E2EDK28 3000000001017945375000029E2EDK28 000000020 CA BOFACATT Bank of ABCABAB ABCABC ABCABAB 50127217 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000030E2EDK28 000000020 CA 026000082 ABCAbank ABCABC ABCABAB 201456700OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000031E2EDK28 000000020 GB ABCAGB2L ABCAbank N.A ABCABA E14, 5LB GB63ABCA18500803115593 ABCABCABC ABCABC Inc. GB63ABCA18500803115593
E2EDK28 3000000001017945375000032E2EDK28 000000020 CA 020012328 ABCABANK ABCABC ABCABAB ON M5J 2M3 2014567007 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000033E2EDK28 000000020 CA 03722010 ABCABABC ABCABABC Bank of Commerce ABCABAABCAB 64-04812 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000034E2EDK28 000000020 IE IHCC In-House Cash Center IHCC1075 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000035E2EDK28 000000020 CA 000300002 ABCAB Bank of ABCABC ABCABAB 0021520584OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000036E2EDK28 000000020 US USCC US Cash Center (IHC) city USCC1075 ABCABCABC ABCABC Inc.
E2EDK29 3000000001017945375000037E2EDK29 000000020 0064848944US A CAD CA ABCABC CA United States US CA A Air Air
E2EDKT1 3000000001017945375000038E2EDKT1 000000020 ZJ32E EN
E2EDKT2 3000000001017945375000039E2EDKT2 000038030 GST/HST877845941RT0001 *
E2EDKT2 3000000001017945375000040E2EDKT2 000038030 QST1021036966TQ0001 *
E2EDKT1 3000000001017945375000041E2EDKT1 000000020 Z4VL
E2EDKT2 3000000001017945375000042E2EDKT2 000041030 0.000 *
E2EDKT1 3000000001017945375000043E2EDKT1 000000020 Z4VH
E2EDKT2 3000000001017945375000044E2EDKT2 000043030 *
E2EDK14 3000000001017945375000045E2EDK14 000000020 008LDCA
E2EDK14 3000000001017945375000046E2EDK14 000000020 00710
E2EDK14 3000000001017945375000047E2EDK14 000000020 00610
E2EDK14 3000000001017945375000048E2EDK14 000000020 015Z4F2
E2EDK14 3000000001017945375000049E2EDK14 000000020 0031075
E2EDK14 3000000001017945375000050E2EDK14 000000020 021M
E2EDK14 3000000001017945375000051E2EDK14 000000020 0161075
E2EDK14 3000000001017945375000052E2EDK14 000000020 962M
E2EDP010013000000001017945375000053E2EDP01001000000020 000011 2980.000 EA 298.000 LB MOUSE 298.000 Z4TN 4260
E2EDP02 3000000001017945375000054E2EDP02 000053030 00140-N6260-S 00000120210205 DFUE
E2EDP02 3000000001017945375000055E2EDP02 000053030 0026336270425 00001120210217
E2EDP02 3000000001017945375000056E2EDP02 000053030 0168026580537 00001020210224
E2EDP02 3000000001017945375000057E2EDP02 000053030 9100000 00000120210205 DFUE
E2EDP02 3000000001017945375000058E2EDP02 000053030 911A 00000120210205 DFUE
E2EDP02 3000000001017945375000059E2EDP02 000053030 912PP 00000120210205 DFUE
E2EDP02 3000000001017945375000060E2EDP02 000053030 91300 00000120210205 DFUE
E2EDP02 3000000001017945375000061E2EDP02 000053030 914CONTACT ABCABCABC 00000120210205 DFUE
E2EDP02 3000000001017945375000062E2EDP02 000053030 963 00000120210205 DFUE
E2EDP02 3000000001017945375000063E2EDP02 000053030 965 00000120210205 DFUE
E2EDP02 3000000001017945375000064E2EDP02 000053030 9666336270425 00000120210205 DFUE
E2EDP02 3000000001017945375000065E2EDP02 000053030 9078026580537 00001020210205 DFUE
E2EDP03 3000000001017945375000066E2EDP03 000053030 02920210217
E2EDP03 3000000001017945375000067E2EDP03 000053030 00120210224
E2EDP03 3000000001017945375000068E2EDP03 000053030 01120210217
E2EDP03 3000000001017945375000069E2EDP03 000053030 02520210217
E2EDP03 3000000001017945375000070E2EDP03 000053030 02720210217
E2EDP03 3000000001017945375000071E2EDP03 000053030 02320210217
E2EDP03 3000000001017945375000072E2EDP03 000053030 02220210205
E2EDP19 3000000001017945375000073E2EDP19 000053030 001418VVZ
E2EDP19 3000000001017945375000074E2EDP19 000053030 002RJR-00001 AB ABCABCABC Mouse FORBUS BLUETOOTH
E2EDP19 3000000001017945375000075E2EDP19 000053030 0078471609000
E2EDP19 3000000001017945375000076E2EDP19 000053030 003889842532685
E2EDP19 3000000001017945375000077E2EDP19 000053030 011CN
E2EDP26 3000000001017945375000078E2EDP26 000053030 00459064.20
E2EDP26 3000000001017945375000079E2EDP26 000053030 00352269.20
E2EDP26 3000000001017945375000080E2EDP26 000053030 01052269.20
E2EDP26 3000000001017945375000081E2EDP26 000053030 01152269.20
E2EDP26 3000000001017945375000082E2EDP26 000053030 0126795.00
E2EDP26 3000000001017945375000083E2EDP26 000053030 01552269.20
E2EDP26 3000000001017945375000084E2EDP26 000053030 00117.54
E2EDP26 3000000001017945375000085E2EDP26 000053030 00252269.20
E2EDP26 3000000001017945375000086E2EDP26 000053030 940 2980.000
E2EDP26 3000000001017945375000087E2EDP26 000053030 939 2980.000
E2EDP05 3000000001017945375000088E2EDP05 000053030 + Z400MS List Price 52269.20 17.54 1 EA CAD 2980
E2EDP05 3000000001017945375000089E2EDP05 000053030 + XR1 Tax Jur Code Level 6795.00 13.000 52269.20
E2EDP05 3000000001017945375000090E2EDP05 000053030 + Tax Subtotal1 6795.00 2.28 1 EA CAD 2980
E2EDP05 3000000001017945375000091E2EDP05 000053030 + Taxable Amount + TaxSubtotal1 59064.20 19.82 1 EA CAD 2980
E2EDP04 3000000001017945375000092E2EDP04 000053030 CX 13.000 6795.00 7000000000
E2EDP04 3000000001017945375000093E2EDP04 000053030 CX 0 0 7001500000
E2EDP04 3000000001017945375000094E2EDP04 000053030 CX 0 0 7001505690
E2EDP28 3000000001017945375000095E2EDP28 000053030 00648489440000108471609000 CN CN ABCAB ZZ 298.000 298.000 LB US 400 United Stat KY
E2EDPT1 3000000001017945375000096E2EDPT1 000053030 0001E EN
E2EDPT2 3000000001017945375000097E2EDPT2 000096040 AB ABCABCABC Mouse forBus Bluetooth EN/XC/XD/XX Hdwr Black For Bsnss *
E2EDS01 3000000001017945375000098E2EDS01 000000020 0011
E2EDS01 3000000001017945375000099E2EDS01 000000020 01259064.20 CAD
E2EDS01 3000000001017945375000100E2EDS01 000000020 0056795.00 CAD
E2EDS01 3000000001017945375000101E2EDS01 000000020 01159064.20 CAD
E2EDS01 3000000001017945375000102E2EDS01 000000020 01052269.20 CAD
E2EDS01 3000000001017945375000103E2EDS01 000000020 94200000 CAD
E2EDS01 3000000001017945375000104E2EDS01 000000020 9440.00 CAD
E2EDS01 3000000001017945375000105E2EDS01 000000020 9450.00 CAD
E2EDS01 3000000001017945375000106E2EDS01 000000020 94659064.20 CAD
E2EDS01 3000000001017945375000107E2EDS01 000000020 94752269.20 CAD
E2EDS01 3000000001017945375000108E2EDS01 000000020 EXT
Z2XSK010003000000001017945375000109Z2XSK01000000108030 Z400 52269.20
Z2XSK010003000000001017945375000110Z2XSK01000000108030 XR1 13.000 6795.00 CX
</idocData>
</ReceiveIdoc>
```

### <a name="create-http-response-action"></a>创建 HTTP 响应操作

现在，请将响应操作添加到逻辑应用的工作流，并包括来自 SAP 操作的输出。 这样，逻辑应用工作流便可将来自 SAP 服务器的结果返回到原始请求方。

1. 在工作流设计器中的 SAP 操作下，选择“新建步骤”。

1. 在搜索框中，输入 `response` 作为筛选器。 在“操作”列表中选择“响应”。  

1. 在“正文”框中单击，以显示动态内容列表。  在该列表中的“将消息发送到 SAP”下面，选择“正文”字段。  

   ![屏幕截图：显示完成 SAP 操作。](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 保存逻辑应用工作流。

### <a name="create-rfc-request-response"></a>创建 RFC 请求-响应

如果需要通过使用远程函数调用 (RFC) 从 SAP ABAP 接收对 Azure 逻辑应用的响应，则必须创建请求和响应模式。 若要在逻辑应用工作流中接收 IDoc，应将其第一个操作设置为状态代码为 `200 OK` 且没有内容的[响应操作](../connectors/connectors-native-reqres.md#add-a-response-action)。 此建议步骤会立即通过 tRFC 完成 SAP 逻辑工作单元 (LUW) 异步传输，这会使 SAP CPIC 对话再次可用。 然后，你可以在逻辑应用工作流中添加更多操作来处理收到的 IDoc，而不会阻止进一步的传输。

> [!NOTE]
> SAP 触发器通过 tRFC 接收 IDoc，tRFC 在设计上没有响应参数。

若要实现请求和响应模式，必须首先使用 [`generate schema` 命令](#generate-schemas-for-artifacts-in-sap)发现 RFC 架构。 生成的架构包含两个可能的根节点： 

* 请求节点，即你从 SAP 接收的调用。
* 响应节点，即你对 SAP 的回应。

在下面的示例中，将从 `STFC_CONNECTION` RFC 模块生成请求和响应模式。 解析请求 XML 以提取 SAP 请求 `<ECHOTEXT>` 的节点值。 响应将当前时间戳作为动态值插入。 从逻辑应用工作流向 SAP 发送 `STFC_CONNECTION` RFC 时，会收到类似的响应。

```xml
<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>
```

### <a name="test-your-logic-app-workflow"></a>测试逻辑应用工作流

1. 如果尚未启用逻辑应用，请在逻辑应用菜单中选择“概览”。  在工具栏中选择“启用”。 

1. 在设计器工具栏上选择“运行”。  此步骤会手动启动逻辑应用工作流。

1. 通过将 HTTP POST 请求发送到 HTTP 请求触发器中的 URL 来触发逻辑应用工作流。 在请求中包括消息内容。 若要发送请求，可以使用 [Postman](https://www.getpostman.com/apps) 等工具。

   在本文中，请求会发送 IDoc 文件，该文件必须采用 XML 格式，并且包含你所用 SAP 操作的命名空间，例如：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
   <idocData>
   <...>
   </idocData>
   </Send>
   ```

1. 发送 HTTP 请求之后，等待逻辑应用工作流返回响应。

   > [!NOTE]
   > 如果未在[请求超时限制](logic-apps-limits-and-config.md)所规定的时间内完成响应所需的所有步骤，逻辑应用工作流可能会超时。 如果发生这种情况，请求可能会被阻止。 为了诊断问题，请了解如何[检查和监视逻辑应用](monitor-logic-apps.md)。

现已创建一个可与 SAP 服务器通信的逻辑应用工作流。 为逻辑应用工作流设置 SAP 连接后，可以探索其他可用的 SAP 操作，例如 BAPI 和 RFC。

<a name="receive-message-sap"></a>

## <a name="receive-message-from-sap"></a>从 SAP 接收消息

此示例中使用的逻辑应用工作流会在应用从 SAP 系统收到消息时触发。

### <a name="add-an-sap-trigger"></a>添加 SAP 触发器

1. 在 Azure 门户中创建一个空白的逻辑应用，此时会打开工作流设计器。

1. 在搜索框中，输入 `when message received sap` 作为筛选器。 在“触发器”列表中选择“从 SAP 收到消息时”。  

   ![屏幕截图：演示如何添加 SAP 触发器。](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   也可选择“企业”选项卡，然后选择触发器： 

   ![屏幕截图：显示如何从“企业”选项卡中添加 SAP 触发器。](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 如果连接已存在，请继续下一步，以便可以设置 SAP 触发器。 但是，如果系统提示输入连接详细信息，请提供该信息，以便立即创建到本地 SAP 服务器的连接。

   1. 为连接提供一个名称。

   1. 如果使用数据网关，请执行以下步骤：

      1. 在“数据网关”部分的“订阅”下，首先选择在 Azure 门户中为数据网关安装创建的数据网关资源的 Azure 订阅 。

      1. 在“连接网关”下，选择 Azure 中的数据网关资源。

   1. 继续提供有关连接的信息。 对于“登录类型”属性，请根据该属性是设置为“应用程序服务器”还是“组”来执行相关步骤：   

      * 对于“应用程序服务器”，必须指定以下属性（通常显示为可选）： 

        ![屏幕截图：显示如何创建与 SAP 应用程序服务器的连接。](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 对于“组”，必须指定以下属性（通常显示为可选）： 

        ![屏幕截图：显示如何创建与 SAP 消息服务器的连接](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。  详细了解[“安全类型化”选项](#safe-typing)。

   1. 完成后，选择“创建”  。

      Azure 逻辑应用会设置并测试连接，以确保连接正常工作。

1. 根据你的 SAP 系统配置，提供[所需的参数](#parameters)，并添加可用于此触发器的任何其他参数，例如：

   * 若要以纯 XML 的形式接收 IDoc，请在“从 SAP 收到消息时”触发器（支持 SAP 普通 XML 格式）中，添加“IDOC 格式”参数并将其设置为“SapPlainXml”。

   * 若要使用相同的 SAP 触发器接收平面文件格式的 IDoc，请添加“IDOC 格式”参数并将其设置为“FlatFile”。 如果也使用[平面文件解码操作](logic-apps-enterprise-integration-flatfile.md)，则必须在平面文件架构中使用 `early_terminate_optional_fields` 属性，并将值设置为 `true`。

     因为在 tRFC 调用 `IDOC_INBOUND_ASYNCHRONOUS` 中由 SAP 发送的平面文件 IDoc 数据记录不会填充到完整的 SDATA 字段长度，所以这是必要要求。 Azure 逻辑应用会提供未填充的平面文件 IDoc 原始数据，与接收自 SAP 的格式相同。 此外，将此 SAP 触发器与平面文件解码操作组合使用时，提供给操作的架构必须与之相匹配。

   * 若要[筛选从 SAP 服务器接收的消息，请指定 SAP 操作列表](#filter-with-sap-actions)。

     例如，从文件选取器中选择一个 SAP 操作：

     ![屏幕截图：显示将 SAP 操作添加到逻辑应用工作流。](./media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

     或者，可以手动指定操作：

     ![屏幕截图：显示手动输入要使用的 SAP 操作。](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

     以下示例演示将触发器设置为接收多个消息时操作的显示方式。

     ![屏幕截图：显示接收多个消息的触发器示例。](./media/logic-apps-using-sap-connector/example-trigger.png)

     有关 SAP 操作的详细信息，请参阅 [IDoc 操作的消息架构](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. 现在请保存逻辑应用工作流，以便可以开始从 SAP 系统接收消息。 在设计器工具栏上选择“保存”。

   现在，逻辑应用工作流已准备好从 SAP 系统接收消息。

   > [!NOTE]
   > 这些步骤中的 SAP 触发器是基于 Webhook 的触发器，而不是轮询触发器。 如果使用的是数据网关，则仅当消息存在时才会从数据网关调用触发器，因此不需要轮询。

1. 在逻辑应用工作流的触发器历史记录中，检查触发器注册是否成功。

如果收到“500 网关错误”或“400 请求错误”，并且其中包含类似“sapgw00 服务未知”的消息，则对端口号的网络服务名称解析失败，例如：

```json
{
   "body": {
      "error": {
         "code": 500,
         "source": "EXAMPLE-FLOW-NAME.eastus.environments.microsoftazurelogicapps.net",
         "clientRequestId": "00000000-0000-0000-0000-000000000000",
         "message": "BadGateway",
         "innerError": {
            "error": {
               "code": "UnhandledException",
               "message": "\nERROR service 'sapgw00' unknown\nTIME Wed Nov 11 19:37:50 2020\nRELEASE 721\nCOMPONENT NI (network interface)\nVERSION 40\nRC -3\nMODULE ninti.c\nLINE 933\nDETAIL NiPGetServByName: 'sapgw00' not found\nSYSTEM CALL getaddrinfo\nCOUNTER 1\n\nRETURN CODE: 20"
            }
         }
      }
   }
}
```

* 选项 1：在 API 连接和触发器配置中，将网关服务名称替换为其端口号。 在错误示例中，`sapgw00` 需要替换为实际端口号，例如 `3300`。 对于 ISE，这是唯一可用的选项。

* 选项 2：如果使用的是本地数据网关，可以将网关服务名称添加到 `%windir%\System32\drivers\etc\services` 中的端口映射，然后重启本地数据网关服务，例如：

  ```text
  sapgw00  3300/tcp
  ```

SAP 应用程序服务器或消息服务器名称解析为 IP 地址时，可能会出现类似的错误。 对于 ISE，必须指定 SAP 应用程序服务器或消息服务器的 IP 地址。 对于本地数据网关，可以改为将名称添加到 `%windir%\System32\drivers\etc\hosts` 中的 IP 地址映射，例如：

```text
10.0.1.9 SAPDBSERVER01 # SAP System Server VPN IP by computer name
10.0.1.9 SAPDBSERVER01.someguid.xx.xxxxxxx.cloudapp.net # SAP System Server VPN IP by fully qualified computer name
```

### <a name="parameters"></a>参数

除了简单的字符串和数字输入，SAP 连接器还接受以下表参数（`Type=ITAB` 输入）：

* 适用于较早 SAP 版本的表方向参数（输入和输出）。

* 更改参数，用于替换较新 SAP 版本的表方向参数。

* 分层表参数

### <a name="filter-with-sap-actions"></a>使用 SAP 操作进行筛选

可以选择提供包含单个或多个 SAP 操作的列表或数组，以筛选逻辑应用工作流从 SAP 服务器接收的消息。 默认情况下，此数组为空，这意味着逻辑应用接收来自 SAP 服务器的所有消息，而不进行筛选。

设置阵列筛选器时，触发器仅接收来自指定 SAP 操作类型的消息，并拒绝来自 SAP 服务器的所有其他消息。 然而，此筛选器并不影响所接收的有效负载的类型是弱还是强。

任何 SAP 操作筛选均发生在本地数据网关的 SAP 适配器级别。 有关详细信息，请参阅[如何将测试 IDoc 从 SAP 发送到 Azure 逻辑应用](#test-sending-idocs-from-sap)。

如果无法将 IDoc 数据包从 SAP 发送到逻辑应用工作流的触发器，请参阅 SAP tRFC (T-code SM58) 对话框中的事务性 RFC (tRFC) 调用拒绝消息。 在 SAP 接口中，你可能会得到以下错误消息，由于“状态文本”字段上的 substring 限制，这些消息会被剪裁。

#### <a name="the-requestcontext-on-the-ireplychannel-was-closed-without-a-reply-being-sent"></a>IReplyChannel 上的 RequestContext 已关闭，未发送回复

此错误消息表示当通道的全方位处理程序由于错误终止通道并重建通道以处理其他消息时，会发生意外故障。

若要确认逻辑应用工作流已收到 IDoc，请[添加一个响应操作](../connectors/connectors-native-reqres.md#add-a-response-action)，该操作会返回 `200 OK` 状态码。 将正文留空，不更改标头或向其添加内容。 IDoc 是通过 tRFC 传输的，tRFC 不允许响应有效负载。

若要改为拒绝 IDoc，请使用除 `200 OK` 以外的任何 HTTP 状态代码。 然后，SAP 适配器会代表你将异常返回到 SAP。 你应仅拒绝 IDoc 将传输错误的信号发回到 SAP，如应用程序无法处理的被错误路由的 IDoc。 不应因应用程序级别的错误而拒绝 IDoc，例如 IDoc 中包含有问题的数据。 如果因应用程序级别的验证延迟传输接受，可能会由于阻碍连接传输其他 IDoc，而对性能造成不良影响。

如果收到此错误消息，并在调用 Azure 逻辑应用时出现系统故障，请检查是否已针对你的特定环境为本地数据网关服务配置网络设置。 例如，如果网络环境需要使用代理来调用 Azure 终结点，则需要将本地数据网关服务配置为使用代理。 有关详细信息，请参阅[代理配置](/dotnet/framework/network-programming/proxy-configuration)。

如果收到此错误消息，并在调用 Azure 逻辑应用时出现间歇性故障，则可能需要增加重试次数和/或重试间隔时间。

1. 检查本地数据网关服务配置文件 `Microsoft.PowerBI.EnterpriseGateway.exe.config` 中的 SAP 设置。

   重试次数设置类似于 `WebhookRetryMaximumCount="2"`。 重试间隔设置类似于 `WebhookRetryDefaultDelay="00:00:00.10"`，时间跨度格式为 `HH:mm:ss.ff`。

1. 保存所做更改。 重启本地数据网关。

#### <a name="the-segment-or-group-definition-e2edk36001-was-not-found-in-the-idoc-meta"></a>在 IDoc 元数据中找不到段或组定义 E2EDK36001

此错误消息表示出现预期故障和其他错误。 例如，由于 IDoc XML 有效负载的片段不是由 SAP 发布的，因此无法生成该有效负载。 因此，缺少转换所需的段类型元数据。

若要将这些段发布到 SAP，请联系 SAP 系统的 ABAP 工程师。

### <a name="asynchronous-request-reply-for-triggers"></a>触发器的异步请求-响应

针对 Azure 逻辑应用触发器，SAP 连接器支持 Azure 的[异步请求-响应模式](/azure/architecture/patterns/async-request-reply)。 可以使用此模式创建成功的请求，如果使用默认的同步请求-响应模式，这些请求将会失败。

> [!TIP]
> 在具有多个响应操作的逻辑应用工作流中，所有响应操作都必须使用相同的请求-响应模式。 例如，如果你的逻辑应用工作流使用可能具有多个响应操作的 switch 控件，则必须将所有响应操作配置为使用相同的请求-响应模式（同步或异步）。

如果为响应操作启用异步响应，则逻辑应用工作流会在接受处理请求后以 `202 Accepted` 回复响应。 响应包含位置标头，可用于检索请求的最终状态。

若要使用 SAP 连接器为逻辑应用工作流配置异步请求-响应模式，请按以下步骤操作：

1. 在工作流设计器中打开逻辑应用。

1. 确认 SAP 连接器是逻辑应用工作流的触发器。

1. 打开逻辑应用工作流的“响应”操作。 在操作的标题栏中，选择菜单（“…”）&gt;“设置” 。

1. 在响应操作的“设置”中，打开“异步响应”下的开关 。 选择“完成”。

1. 保存对逻辑应用工作流所做的更改。

## <a name="find-extended-error-logs"></a>查找扩展错误日志

有关完整的错误消息，请查看 SAP 适配器的扩展日志。 你还可以[为 SAP 连接器启用扩展日志文件](#extended-sap-logging-in-on-premises-data-gateway)。

* 对于 2020 年 4 月或更早发布的本地数据网关，默认情况下禁用日志。

* 对于 2020 年 6 月及之后发布的本地数据网关，你可以[在应用设置中启用网关日志](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app)。

  * 默认的日志记录级别为“警告”。

  * 如果在本地数据网关应用的“诊断”设置中启用了“附加日志记录”，则日志记录级别将提升至“信息”  。

  * 要将日志记录级别提升至“详细”，请在配置文件中更新以下设置。 通常，配置文件位于 `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config`。

    ```xml
    <setting name="SapTraceLevel" serializeAs="String">
       <value>"Verbose"</value>
    </setting>
    ```

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>本地数据网关上的扩展 SAP 日志记录

如果[为 Azure 逻辑应用使用本地数据网关](../logic-apps/logic-apps-gateway-install.md)，则可以为 SAP 连接器配置扩展日志文件。 你可以使用本地数据网关将 Windows 事件跟踪 (ETW) 事件重定向到包含在网关的日志记录 zip 文件中的轮换日志文件中。

你可以[将网关的所有配置和服务日志](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app)从网关应用的设置导出到 .zip 文件中。

> [!NOTE]
> 扩展日志记录处于始终启用状态时，可能会影响逻辑应用工作流的性能。 最佳做法是在完成问题分析和故障排除后关闭扩展日志文件。

#### <a name="capture-etw-events"></a>捕获 ETW 事件

（可选）高级用户可以直接捕获 ETW 事件。 然后你可以[在事件中心的 Azure 诊断中使用数据](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)或[将数据收集到 Azure Monitor 日志中](../azure-monitor/agents/diagnostics-extension-logs.md)。 有关详细信息，请参阅[收集和存储数据的最佳做法](/azure/architecture/best-practices/monitoring#collecting-and-storing-data)。 可以使用 [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) 处理生成的 ETL 文件，也可以编写自己的程序。 本演练使用 PerfView：

1. 在 PerfView 菜单中，选择“收集”&gt;“收集”以捕获事件 。

1. 在“其他提供程序”字段中，输入 `*Microsoft-LobAdapter` 以指定用于捕获 SAP 适配器事件的 SAP 提供程序。 如果未指定此信息，则跟踪只包含常规 ETW 事件。

1. 保留其他默认设置。 如果需要，可以在“数据文件”字段中更改文件名或位置。

1. 选择“开始收集”以开始跟踪。

1. 重现问题或收集足够的分析数据后，请选择“停止收集”。

1. 若要与另一方（如 Azure 支持工程师）共享你的数据，请压缩 ETL 文件。

1. 查看跟踪的内容：

   1. 在 PerfView 中，选择“文件”&gt;“打开”，然后选择刚刚生成的 ETL 文件 。

   1. 在 PerfView 侧栏中，ETL 文件下的“事件”部分。

   1. 在“筛选器”下，按 `Microsoft-LobAdapter` 筛选以仅查看相关事件和网关进程。

### <a name="test-your-workflow"></a>测试工作流

1. 若要触发逻辑应用工作流，请从 SAP 系统发送一条消息。

1. 在逻辑应用菜单中，选择“概述”  。 查看逻辑应用工作流的任何新运行的“运行历史记录”。

1. 打开最近的运行，触发器输出部分会显示从 SAP 系统发送的消息。

### <a name="test-sending-idocs-from-sap"></a>测试从 SAP 发送 IDoc

若要将 IDoc 从 SAP 发送到逻辑应用工作流，需要满足以下最低配置：

> [!IMPORTANT]
> 仅当使用逻辑应用工作流测试 SAP 配置时，才使用这些步骤。 生产环境需要其他配置。

1. [在 SAP 中配置 RFC 目标。](#create-rfc-destination)

1. [创建与 RFC 目标的 ABAP 连接。](#create-abap-connection)

1. [创建接收方端口。](#create-receiver-port)

1. [创建发送方端口。](#create-sender-port)

1. [创建逻辑系统合作伙伴。](#create-logical-system-partner)

1. [创建合作伙伴配置文件。](#create-partner-profiles)

1. [测试发送消息。](#test-sending-messages)

#### <a name="create-rfc-destination"></a>创建 RFC 目标

1. 若要打开“RFC 连接的配置”设置，请在 SAP 界面中使用“sm59”事务代码 (T-Code)，前缀为“/n”。

1. 选择“TCP/IP 连接” > “创建” 。

1. 使用以下设置创建新 RFC 目标：

    1. 为“RFC 目标”输入名称。

    1. 在“技术设置”选项卡上，对于“激活类型”，选择“注册服务器程序”  。

    1. 为“程序 ID”输入一个值。 在 SAP 中，使用此标识符来注册逻辑应用工作流的触发器。

       > [!IMPORTANT]
       > SAP“程序 ID”区分大小写。 配置逻辑应用工作流和 SAP 服务器时，请确保“程序 ID”始终使用相同的大小写格式。 否则，当你尝试将 IDoc 发送到 SAP 时，可能会在 tRFC 监视器 (T-Code SM58) 中收到以下错误：
       >
       > * **找不到函数 IDOC_INBOUND_ASYNCHRONOUS**
       > * 不支持非 ABAP RFC 客户端（合作伙伴类型）
       >
       > 有关 SAP 的详细信息，请参阅以下说明（需要登录）：
       >
       > * [https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329)
       > * [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597)

    1. 在“Unicode”选项卡上，对于“与目标系统的通信类型”，选择“Unicode”  。

       > [!NOTE]
       > SAP .NET 客户端库仅支持 Unicode 字符编码。 如果在将 IDoc 从 SAP 发送到 Azure 逻辑应用时出现错误 `Non-ABAP RFC client (partner type ) not supported`，请检查“与目标系统的通信类型”值是否设置为“Unicode” 。

1. 保存所做更改。

1. 创建一个以名为“从 SAP 收到消息时”的 SAP 触发器启动的逻辑应用工作流，使用 Azure 逻辑应用注册新的“程序 ID”。

   这样一来，保存工作流时，Azure 逻辑应用就会在 SAP 网关上注册“程序 ID”。

1. 在工作流的触发器历史记录、本地数据网关 SAP 适配器日志和 SAP 网关跟踪日志中检查注册状态。 在 SAP 网关监视器对话框 (T-Code SMGW) 中，在“已登录客户端”下，新注册应显示为“已注册的服务器”。

1. 若要测试连接，请在 SAP 接口中的新“RFC 目标”下，选择“连接测试” 。

#### <a name="create-abap-connection"></a>创建 ABAP 连接

1. 若要打开“RFC 连接的配置”设置，请在 SAP 界面中使用“sm59”事务代码 (T-Code)，前缀为“/n”。

1. 选择“ABAP 连接” > “创建” 。

1. 对于“RFC 目标”，输入[测试 SAP 系统](#create-rfc-destination)的标识符。

1. 保存所做更改。

1. 若要测试你的连接，请选择“连接测试”。

#### <a name="create-receiver-port"></a>创建接收方端口

1. 若要打开“IDOC 处理中的端口”设置，请在 SAP 界面中使用“we21”事务代码 (T-Code)，前缀为“/n”。

1. 选择“端口” > 事务 RFC” > “创建”  。

1. 在打开的设置框中，选择“自己的端口名”。 为测试端口输入一个名称。 保存所做更改。

1. 在新接收方端口的设置中，对于“RFC 目标”，输入[测试 RFC 目标](#create-rfc-destination)的标识符。

1. 保存所做更改。

#### <a name="create-sender-port"></a>创建发送方端口

1. 若要打开“IDOC 处理中的端口”设置，请在 SAP 界面中使用“we21”事务代码 (T-Code)，前缀为“/n”。

1. 选择“端口” > 事务 RFC” > “创建”  。

1. 在打开的设置框中，选择“自己的端口名”。 对于测试端口，输入以 SAP 开头的名称 。 所有发送方端口名称必须以字母 SAP 开头，例如 SAPTEST 。 保存所做更改。

1. 在新的发送方端口的设置中，对于 RFC 目标，输入 [ABAP 连接](#create-abap-connection)的标识符。

1. 保存所做更改。

#### <a name="create-logical-system-partner"></a>创建逻辑系统合作伙伴

1. 若要打开“更改视图‘逻辑系统’：概述”设置，请在 SAP 界面中使用“bd54”事务代码 (T-Code)。

1. 接受出现的警告消息：“注意：该表是跨客户端的”

1. 在显示现有逻辑系统的列表上方，选择“新条目”。

1. 对于新的逻辑系统，请输入 Log.System 标识符和简短的名称描述 。 保存所做更改。

1. 出现“工作台提示”时，通过提供描述来创建新请求，或者如果已经创建了请求，则跳过此步骤。

1. 创建工作台请求后，将该请求链接到表更新请求。 若要确认表已更新，请保存所做的更改。

#### <a name="create-partner-profiles"></a>创建合作伙伴配置文件

对于生产环境，必须创建两个合作伙伴配置文件。 第一个配置文件用于发送方，即你的组织和 SAP 系统。 第二个配置文件用于接收方，即逻辑应用。

1. 若要打开“合作伙伴配置文件”设置，请在 SAP 界面中使用“we20”事务代码 (T-Code)，前缀为“/n”。

1. 在“合作伙伴配置文件”下，选择“合作伙伴类型 LS” > “创建”  。

1. 使用以下设置创建新合作伙伴配置文件：

    * 对于“合作伙伴编号”，请输入[逻辑系统合作伙伴的标识符](#create-logical-system-partner)。

    * 对于“合作伙伴类型”**请输入“LS”** 。

    * 对于“代理”，请输入在注册 Azure 逻辑应用或其他非 SAP 系统的程序标识符时要使用的 SAP 用户帐户的标识符。

1. 保存所做更改。 如果尚未[创建逻辑系统合作伙伴](#create-logical-system-partner)，则会收到错误消息，“请输入有效的合作伙伴编号”。

1. 在合作伙伴配置文件设置中的“出站参数”下，选择“创建出站参数” 。

1. 使用以下设置新建出站参数：

    * 输入“消息类型”，例如 CREMAS 。

    * 输入[接收方端口的标识符](#create-receiver-port)。

    * 输入包的 IDoc 大小 **。** 大小”。 或者，要[从 SAP 一次发送一个 IDoc](#receive-idoc-packets-from-sap)，请选择“立即传递 IDoc”。

1. 保存所做更改。

#### <a name="test-sending-messages"></a>测试发送消息

1. 若要打开“IDoc 处理中的测试工具”设置，请在 SAP 界面中使用“we19”事务代码 (T-Code)，前缀为“/n”。

1. 在“测试模板”下，选择“通过消息类型”，然后输入消息类型，例如 CREMAS  。 选择“创建”。

1. 对于消息“哪个 IDoc 类型？”，通过选择“继续”进行确认 。

1. 选择“EDIDC”节点。 为接收方和发送方端口输入合适的值。 选择“继续”。

1. 选择“标准出站处理”。

1. 要启动出站 IDoc 处理，请选择“继续”。 处理完成后，将显示“IDoc 已发送到 SAP 系统或外部程序”消息。

1. 要检查是否存在处理错误，请使用前缀为“/n”的“sm58”事务代码 (T-Code)。

## <a name="receive-idoc-packets-from-sap"></a>从 SAP 接收 IDoc 数据包

可以将 SAP 设置为[以数据包的形式发送 IDoc](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html)，数据包是成批或成组的 IDoc。 SAP 连接器和其他组件（具体来说是触发器）不需额外配置即可接收 IDoc 数据包。 但是，若要在触发器接收 IDoc 数据包之后处理该数据包中的每个项，则必须执行一些其他的步骤，将数据包拆分成单个 IDoc。

下面是一个示例，演示如何使用 [`xpath()` 函数](./workflow-definition-language-functions-reference.md#xpath)从数据包提取单个 IDoc：

1. 在开始之前，需要具有 SAP 触发器的逻辑应用工作流。 如果你的逻辑应用工作流中还没有该触发器，请按本主题中的上述步骤操作，[设置一个带 SAP 触发器的逻辑应用工作流](#receive-message-from-sap)。

   > [!IMPORTANT]
   > SAP“程序 ID”区分大小写。 配置逻辑应用工作流和 SAP 服务器时，请确保“程序 ID”始终使用相同的大小写格式。 否则，当你尝试将 IDoc 发送到 SAP 时，可能会在 tRFC 监视器 (T-Code SM58) 中收到以下错误：
   >
   > * **找不到函数 IDOC_INBOUND_ASYNCHRONOUS**
   > * **不支持非 ABAP RFC 客户端(合作伙伴类型)**
   >
   > 有关 SAP 的详细信息，请参阅以下说明（需要登录）：[https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329) 和 [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597)。

   例如：

   ![屏幕截图：显示将 SAP 触发器添加到逻辑应用工作流。](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. [向逻辑应用工作流添加响应操作](../connectors/connectors-native-reqres.md#add-a-response-action)，以便立即以 SAP 请求的状态进行响应。 最佳做法是在触发之后立即添加此操作，以释放与 SAP 服务器的通信通道。 选择以下状态代码 (`statusCode`) 之一以用于响应操作：

    * 202 Accepted，表示请求已被接受进行处理，但处理尚未完成。

    * 204 No Content，表示服务器已成功完成请求，响应有效负载正文中没有要发送的其他内容。 

    * **200 正常**。 此状态代码始终包含有效负载，即使服务器生成的有效负载正文长度为零。 

1. 从逻辑应用工作流从 SAP 接收的 XML IDoc 获取根命名空间。 若要从 XML 文档提取该命名空间，请添加一个步骤，使用 `xpath()` 表达式创建本地字符串变量并存储该命名空间：

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![屏幕截图：显示从 IDoc 获取根命名空间。](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 若要提取单个 IDoc，请添加一个步骤，使用另一个 `xpath()` 表达式创建数组变量并存储 IDoc 集合：

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![屏幕截图：显示获取项数组。](./media/logic-apps-using-sap-connector/get-array.png)

   数组变量通过对集合进行枚举，使每个 IDoc 可供逻辑应用工作流单独处理。 在以下示例中，逻辑应用工作流将使用循环将每个 IDoc 传输给 SFTP 服务器：

   ![屏幕截图：显示向 SFTP 服务器发送 IDoc。](./media/logic-apps-using-sap-connector/loop-batch.png)

   每个 IDoc 必须包含根命名空间，因此必须将文件内容与根命名空间一起包装在 `<Receive></Receive>` 元素中，然后才能将 IDoc 发送到下游应用（在此示例中为 SFTP 服务器）。

创建新的逻辑应用工作流时，可以通过在工作流设计器中选择此模板来使用此模式的快速启动模板。

![屏幕截图：显示选择批逻辑应用模板。](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>为 SAP 中的项目生成架构

本示例使用可通过 HTTP 请求触发的逻辑应用工作流。 若要为指定的 IDoc 和 BAPI 生成架构，SAP 操作“生成架构”会将请求发送到 SAP 系统。

此 SAP 操作返回 [XML 架构](#sample-xml-schemas)，而不是 XML 文档本身的内容或数据。 使用 Azure 资源管理器连接器将响应中返回的架构上传到集成帐户。 架构包含以下部分：

* 请求消息的结构。 使用此信息形成你的 BAPI `get` 列表。

* 响应消息的结构。 使用此信息分析响应。

若要发送请求消息，请使用通用 SAP 操作“将消息发送到 SAP”，或使用定向“\[BAPI] 调用 SAP 中的方法”操作 。

### <a name="sample-xml-schemas"></a>示例 XML 架构

如果要了解如何生成创建示例文档时使用的 XML 架构，请参阅以下示例。 这些示例演示了如何使用多种类型的有效负载，包括：

* [RFC 请求](#xml-samples-for-rfc-requests)

* [BAPI 请求](#xml-samples-for-bapi-requests)

* [IDoc 请求](#xml-samples-for-idoc-requests)

* 简单或复杂的 XML 架构数据类型

* 表参数

* 可选的 XML 行为

可以使用可选的 XML prolog 开始 XML 架构。 SAP 连接器可以使用 XML prolog，也可以不使用。

```xml
<?xml version="1.0" encoding="utf-8">
```

#### <a name="xml-samples-for-rfc-requests"></a>RFC 请求的 XML 示例

下面的示例是一个基本的 RFC 调用。 RFC 名称为 `STFC_CONNECTION`。 该请求使用默认的命名空间 `xmlns=`，但是你可以分配并使用命名空间别名，例如 `xmmlns:exampleAlias=`。 命名空间值是适用于 Microsoft 服务的 SAP 中所有 RFC 的命名空间。 请求中有一个简单的输入参数 `<REQUTEXT>`。

```xml
<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>
```

下面的示例是一个带有表参数的 RFC 调用。 此示例调用及其测试 RFC 组作为所有 SAP 系统的一部分提供。 表参数的名称为 `TCPICDAT`。 表行类型为 `ABAPTEXT`，这个元素在表中的每一行中重复出现。 此示例包含一个名为 `LINE` 的行。 带有表参数的请求可以包含任意数目的字段，其中数值为正整数 (n)。 

```xml
<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <RESTART_QNAME>exampleQName</RESTART_QNAME>
   <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
   </TCPICDAT>
</STFC_WRITE_TO_TCPIC>
```

> [!NOTE]
> 使用 SAP Logon 的数据浏览器 (T-Code SE16) 查看 STFC_WRITE_TO_TCPIC 的结果。使用表名 TCPIC。

下面的示例是一个 RFC 调用，其中包含具有匿名字段的表参数。 匿名字段是指未分配名称的字段。 复杂类型在单独的命名空间下声明，在该命名空间中，声明为当前节点及其所有子元素设置新的默认值。 此示例将十六进制代码 `x002F` 用作符号 / 的转义字符，因为该符号保留在 SAP 字段名称中。

```xml
<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <IM_XML_TABLE>
      <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
      </RFC_XMLCNT>
   </IM_XML_TABLE>
</RFC_XML_TEST_1>
```

下面的示例包含命名空间的前缀。 可以一次声明所有前缀，也可以声明任意数量的前缀作为节点的属性。 RFC 命名空间别名 `ns0` 用作基本类型的根和参数。

> [!NOTE]
> 复杂类型在别名为 `ns3` 的 RFC 类型的不同命名空间下声明，而不是在别名为 `ns0` 的常规 RFC 命名空间下声明。

```xml
<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
   <ns0:DELIMITER>0</ns0:DELIMITER>
   <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
   <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
   <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
   <ns0:FIELDS>
      <ns3:RFC_DB_FLD>
         <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
      </ns3:RFC_DB_FLD>
   </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>
```

#### <a name="xml-samples-for-bapi-requests"></a>BAPI 请求的 XML 示例

以下 XML 示例是[调用 BAPI 方法](#actions)的示例请求。

> [!NOTE]
> SAP 会在对 Azure 逻辑应用在没有输入筛选器的情况下签发的 RFC `RPY_BOR_TREE_INIT` 的响应中描述业务对象，以使业务对象可供外部系统使用。 逻辑应用检查输出表 `BOR_TREE`。 此 `SHORT_TEXT` 字段用于业务对象的名称。 Azure 逻辑应用无法访问输出表中 SAP 未返回的业务对象。
> 
> 如果使用自定义业务对象，则必须确保在 SAP 中发布这些业务对象。 否则，SAP 不会在输出表 `BOR_TREE` 中列出自定义业务对象。 你必须先从 SAP 公开业务对象，然后才能在逻辑应用中访问自定义业务对象。 

下面的示例使用 BAPI 方法 `GETLIST` 获取银行列表。 此示例包含银行的业务对象 `BUS1011`。

```xml
<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
   <BANK_CTRY>US</BANK_CTRY>
   <MAX_ROWS>10</MAX_ROWS>
</GETLIST>
```

下面的示例使用 `CREATE` 方法创建一个银行对象。 本示例使用与上一个示例相同的业务对象 `BUS1011`。 使用 `CREATE` 方法创建银行时，请确保提交更改，因为默认情况下不会提交此方法。

> [!TIP]
> 确保你的 XML 文档遵循 SAP 系统中配置的任何验证规则。 例如，在此示例文档中，银行密钥 (`<BANK_KEY>`) 必须是银行路由号码，在美国也称为 ABA 号码。

```xml
<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>
```

下面的示例使用银行路由号码（`<BANK_KEY>` 的值）获取银行的详细信息。 

```xml
<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>
```

#### <a name="xml-samples-for-idoc-requests"></a>IDoc 请求的 XML 示例

要生成纯文本 SAP IDoc XML 架构，请使用“SAP 登录”应用程序和 `WE-60` T-Code。 通过 GUI 访问 SAP 文档，并为 IDoc 类型和扩展生成 XSD 格式的 XML 架构。 有关通用 SAP 格式和有效负载的说明及其内置对话框，请参阅 [SAP 文档](https://help.sap.com/viewer/index)。

此示例声明根节点和命名空间。 示例代码中的 URI `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` 声明以下配置：

* `/IDoc` 是所有 IDoc 的根节点。

* `/3` 是常见段定义的记录类型版本。

* `/ORDERS05` 是 IDoc 类型。

* `//` 为空段，因为没有 IDoc 扩展名。

* `/700` 为 SAP 版本。

* `/Send` 是将信息发送到 SAP 的操作。

```xml
<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
   <ns0:idocData>
```

可以重复 `idocData` 节点以在单个调用中发送一批 IDoc。 在下面的示例中，有一条控件记录 `EDI_DC40` 和多条数据记录。

```xml
<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
      <...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
    <...>
  </ns0:idocData>
```

下面的示例是示例 IDoc 控件记录，使用的前缀为 `EDI_DC`。 必须更新值才能匹配 SAP 安装和 IDoc 类型。 例如，你的 IDoc 客户端代码可能不是 `800`。 请与 SAP 团队联系，确保为 SAP 安装使用正确的值。

```xml
<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>
```

下面的示例是包含纯文本段的示例数据记录。 此示例使用 SAP 日期格式。 强类型文档可以使用本机 XML 日期格式，如 `2020-12-31 23:59:59`。

```xml
<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>
```

下面的示例是包含分组段的数据记录。 该记录包括一个组父节点 `E2EDKT1002GRP` 和多个子节点，包括 `E2EDKT1002` 和 `E2EDKT2001`。 

```xml
<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>
```

推荐的方法是创建 IDoc 标识符以用于 tRFC。 你可以使用 SAP 连接器 API 中的[发送 IDoc 操作](/connectors/sap/#send-idoc)设置此事务标识符 `tid`。

下面的示例是设置事务标识符 (`tid`) 的替代方法。 在此示例中，最后一个数据记录段节点和 IDoc 数据节点处于关闭状态。 然后，GUID `guid` 用作检测重复项的 tRFC 标识符。

```xml
     </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>
```

### <a name="add-the-request-trigger"></a>添加“请求”触发器

1. 在 Azure 门户中创建一个空白的逻辑应用，此时会打开工作流设计器。

1. 在搜索框中，输入 `http request` 作为筛选器。 在触发器列表中，选择“当收到 HTTP 请求时”。  

   ![屏幕截图：显示如何添加请求触发器。](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 现在请保存逻辑应用，以便可以为逻辑应用工作流生成终结点 URL。 在设计器工具栏上选择“保存”。 

   终结点 URL 现在会显示在触发器中，例如：

   ![屏幕截图：显示生成终结点 URL。](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>添加 SAP 操作以生成架构

1. 在工作流设计器中的触发器下，选择“新建步骤”。

   ![屏幕截图：显示向逻辑应用工作流添加新步骤。](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜索框中，输入 `generate schemas sap` 作为筛选器。 在“操作”列表中选择“生成架构”。  
  
   ![屏幕截图：显示向工作流添加“生成架构”操作。](./media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   也可以选择“企业”选项卡，然后选择 SAP 操作。 

   ![屏幕截图：显示从“企业”选项卡中选择“生成架构”操作。](./media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 如果连接已存在，请继续下一步，以便可以设置 SAP 操作。 但是，如果系统提示输入连接详细信息，请提供该信息，以便立即创建到本地 SAP 服务器的连接。

   1. 为连接提供一个名称。

   1. 在“数据网关”部分的“订阅”下，首先选择在 Azure 门户中为数据网关安装创建的数据网关资源的 Azure 订阅 。 

   1. 在“连接网关”下，选择 Azure 中的数据网关资源。

   1. 继续提供有关连接的信息。 对于“登录类型”属性，请根据该属性是设置为“应用程序服务器”还是“组”来执行相关步骤：   

      * 对于“应用程序服务器”，必须指定以下属性（通常显示为可选）： 

        ![屏幕截图：显示为 SAP 应用程序服务器创建连接](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 对于“组”，必须指定以下属性（通常显示为可选）： 

        ![屏幕截图：显示为 SAP 消息服务器创建连接](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

   1. 完成后，请选择“创建”。

      Azure 逻辑应用会设置并测试连接，以确保连接正常工作。

1. 提供要为其生成架构的项目的路径。

   可以通过文件选取器选择 SAP 操作：

   ![屏幕截图：显示如何选择 SAP 操作。](./media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   或者，可以手动输入操作：

   ![屏幕截图：显示手动输入 SAP 操作。](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   若要为多个项目生成架构，请提供每个项目的 SAP 操作详细信息，例如：

   ![屏幕截图：显示选择“添加新项”。](./media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![屏幕截图：显示两个项。](./media/logic-apps-using-sap-connector/schema-generator-example.png)

   有关 SAP 操作的详细信息，请参阅 [IDoc 操作的消息架构](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

1. 保存逻辑应用工作流。 在设计器工具栏上选择“保存”。

默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。  详细了解[“安全类型化”选项](#safe-typing)。

### <a name="test-your-workflow"></a>测试工作流

1. 在设计器工具栏上，选择“运行”以触发逻辑应用工作流的运行。

1. 打开该运行，并检查“生成架构”操作的输出。 

   输出中会显示针对指定的消息列表生成的架构。

### <a name="upload-schemas-to-an-integration-account"></a>将架构上传到集成帐户

（可选）可以下载生成的架构，或将其存储在 Blob、存储或集成帐户等存储库中。 集成帐户可以就其他 XML 操作提供一流的使用体验。本示例演示如何使用 Azure 资源管理器连接器将架构上传到同一逻辑应用工作流的集成帐户。

1. 在工作流设计器中的触发器下，选择“新建步骤”。

1. 在搜索框中，输入 `resource manager` 作为筛选器。 选择“创建或更新资源”。 

   ![屏幕截图：显示如何选择 Azure 资源管理器操作。](./media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. 输入操作的详细信息，包括 Azure 订阅、Azure 资源组和集成帐户。 若要将 SAP 令牌添加到字段，请在这些字段对应的框中单击，然后从显示的动态内容列表中选择。

   1. 打开“添加新参数”列表，然后选择“位置”和“属性”字段。   

   1. 按此示例所示，提供这些新字段的详细信息。

      ![屏幕截图：显示如何为 Azure 资源管理器操作输入详细信息。](./media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP 的“生成架构”操作会生成集合形式的架构，因此，设计器会自动将一个 **For each** 循环添加到该操作。  以下示例演示此操作的显示方式：

   ![屏幕截图：显示包含“for each”循环的 Azure 资源管理器操作。](./media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > 架构使用 base64 编码格式。 若要将架构上传到集成帐户，必须使用 `base64ToString()` 函数将其解码。 以下示例演示了 `"properties"` 元素的代码：
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. 保存逻辑应用工作流。 在设计器工具栏上选择“保存”。

### <a name="test-your-workflow"></a>测试工作流

1. 在设计器工具栏上，选择“运行”以手动触发逻辑应用工作流。

1. 成功运行后，转到集成帐户，并检查生成的架构是否存在。

<a name="enable-secure-network-communications"></a>

## <a name="enable-secure-network-communications-snc"></a>启用安全网络通信 (SNC)

在开始之前，请确保满足前面列出的[先决条件](#prerequisites)，这些先决条件仅适用于使用数据网关且逻辑应用工作流在多租户 Azure 中运行的情况：

* 请确保将本地数据网关安装在与 SAP 系统位于同一网络中的计算机上。

* 为实现 SSO，需要以映射到 SAP 用户的用户身份运行数据网关。

* 提供其他安全功能的 SNC 库已安装在数据网关所在的同一台计算机上。 部分示例包括 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos 和 NTLM。

   若要对传入和传出 SAP 系统的请求启用 SNC，请在 SAP 连接中选中“使用 SNC”复选框，并提供以下属性： 

   ![屏幕截图：显示如何在 SAP 连接中设置 SNC。](./media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 属性 | 说明 |
   |----------| ------------|
   | **SNC 库路径** | SNC 库的名称、相对于 NCo 安装位置的路径或绝对路径。 例如 `sapsnc.dll`、`.\security\sapsnc.dll` 或 `c:\security\sapsnc.dll`。 |
   | **SNC SSO** | 通过 SNC 进行连接时，SNC 标识通常用于对调用方进行身份验证。 另一个选项是重写，以便可以使用用户和密码信息对调用方进行身份验证，但该行仍会加密。 |
   | **我的 SNC 名称** | 在大多数情况下，你可以省略此属性。 安装的 SNC 解决方案通常知道自己的 SNC 名称。 只有对于支持“多个标识”的解决方案，才需要指定用于此特定目标或服务器的标识。 |
   | **SNC 合作伙伴名称** | 后端 SNC 的名称。 |
   | **SNC 保护质量** | 此特定目标或服务器的 SNC 通信使用的服务质量。 默认值由后端系统定义。 最大值由 SNC 使用的安全产品定义。 |
   |||

   > [!NOTE]
   > 不要在装有数据网关和 SNC 库的计算机上设置 SNC_LIB 和 SNC_LIB_64 环境变量。 如果已设置这些环境变量，它们会优先于通过连接器传递的 SNC 库值。

## <a name="safe-typing"></a>安全类型化

创建 SAP 连接时，默认会使用强类型化通过针对架构执行 XML 验证来检查无效值。 此行为可帮助提前检测问题。 “安全类型化”选项用于实现后向兼容，它只会检查字符串长度。  如果选择“安全类型化”，则 SAP 中的 DATS 类型和 TIMS 类型将被视为字符串而不是其 XML 等效形式 `xs:date` 和 `xs:time`，其中 `xmlns:xs="http://www.w3.org/2001/XMLSchema"`。  安全类型化会影响所有架构生成操作、“被发送”有效负载和“被接收”响应的发送消息，以及触发器的行为。

使用强类型化时（未启用 **安全类型化**），架构会将 DATS 和 TIMS 类型映射到更简单的 XML 类型：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

使用强类型化发送消息时，DATS 和 TIMS 响应符合匹配的 XML 类型格式：

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

启用 **安全类型化** 时，架构会将 DATS 和 TIMS 类型映射到只施加长度限制的 XML 字符串字段，例如：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

在启用 **安全类型化** 的情况下发送消息时，DATS 和 TIMS 响应如以下示例所示：

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="send-sap-telemetry-foron-premises-data-gateway-to-azure-application-insights"></a>将本地数据网关的 SAP 遥测发送到 Azure Application Insights

本地数据网关进行 2021 年 8 月更新之后，SAP 连接器操作可以将来自 SAP 客户端库的遥测数据和来自 Microsoft SAP 适配器的跟踪发送到 Azure Monitor 中的 [Application Insights](../azure-monitor/app/app-insights-overview.md) 功能。 该遥测主要包含以下数据：

* 基于 SAP NCo 指标和监视器的指标和跟踪。

* 来自 Microsoft SAP 适配器的跟踪。

### <a name="metrics-and-traces-from-sap-client-library"></a>来自 SAP 客户端库的指标和跟踪

指标是根据本地数据网关上资源的使用情况和可用性，在一段时间内可能会变化，也可能不会变化的数值。 这些指标可用于更好地了解系统运行状况并创建有关以下活动的警报：

* 系统运行状况是否正在变糟。

* 异常事件。

* 系统上的负荷过重。

此信息将发送到 Application Insights 表 `customMetrics`。 默认情况下，每隔 30 秒发送一次指标。

SAP NCo 指标和跟踪基于 SAP NCo 指标，特别是以下 NCo 类：

* RfcDestinationMonitor。

* RfcConnectionMonitor。

* RfcServerMonitor。

* RfcRepositoryMonitor。

有关每个类提供的指标的详细信息，请查看 [SAP NCo 文档（需要登录）](https://support.sap.com/en/product/connectors/msnet.html#section_512604546)。

跟踪包含与指标一起使用的文本信息。 此信息将发送到名为 `traces` 的 Application Insights 表。 默认情况下，每隔 10 分钟发送一次跟踪。

### <a name="set-up-sap-telemetry-for-application-insights"></a>为 Application Insights 设置 SAP 遥测

需要先创建并设置 Application Insights 资源，然后才能将网关安装的 SAP 遥测发送到 Application Insights。 有关详细信息，请查看以下文档：

* [创建 Application Insights 资源（经典）](../azure-monitor/app/create-new-resource.md)

* [基于工作区的 Application Insights 资源](../azure-monitor/app/create-workspace-resource.md)

若要实现将 SAP 遥测发送到 Application Insights，请执行以下步骤：

1. 从以下位置下载 Microsoft.ApplicationInsights.EventSourceListener.dll 的 NuGet 包：[https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0)。

1. 将下载的文件添加到本地数据网关安装目录。

1. 在本地数据网关安装目录中，检查 Microsoft.ApplicationInsights.dll 文件是否与添加的 Microsoft.ApplicationInsights.EventSourceListener.dll 文件具有相同的版本号 。 该网关当前使用版本 2.14.0。

1. 在 ApplicationInsights.config 文件中，通过取消注释带有 `<InstrumentationKey></Instrumentation>` 元素的行来添加 [Application Insights 检测密钥](../azure-monitor/app/app-insights-overview.md#how-does-application-insights-work)。 将占位符 your-Application-Insights-instrumentation-key 替换为你的密钥，例如：

      ```xml
      <?xml version="1.0" encoding="utf-8"?>
      <ApplicationInsights schemaVersion="2014-05-30" xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
         <!-- Uncomment this element and insert your Application Insights key to receive ETW telemetry about your gateway <InstrumentationKey>*your-instrumentation-key-placeholder*</InstrumentationKey> -->
         <TelemetryModules>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
               <IsHeartbeatEnabled>false</IsHeartbeatEnabled>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
               <Sources>
                  <Add Name="Microsoft-LobAdapter" Level="Verbose" />
               </Sources>
            </Add>
         </TelemetryModules>
      </ApplicationInsights>
      ```

1. 在 ApplicationInsights.config 文件中，可以根据要求更改 SAP 连接器操作所需的跟踪 `Level` 值，例如：

   ```xml
   <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
         <Add Name="Microsoft-LobAdapter" Level="Verbose" />
      </Sources>
   </Add>
   ```

   有关详细信息，请查看以下文档：

   * `Level` 值：[EventLevel 枚举](/dotnet/api/system.diagnostics.tracing.eventlevel)

   * [EventSource 跟踪](../azure-monitor/app/configuration-with-applicationinsights-config.md#eventsource-tracking)

   * [EventSource 事件](../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events)

1. 应用所做的更改后，重启本地数据网关服务。

### <a name="review-metrics-in-application-insights"></a>在 Application Insights 中查看指标

在逻辑应用工作流中运行 SAP 操作后，可以查看发送到 Application Insights 的遥测。

1. 在 Azure 门户中，打开 Application Insights 资源。

1. 在资源菜单的“监视”下，选择“日志” 。

   以下屏幕截图显示 Azure 门户中打开“日志”窗格的 Application Insights：

   [![该屏幕截图显示 Azure 门户中打开“日志”窗格以创建查询的 Application Insights。](./media/logic-apps-using-sap-connector/application-insights-query-panel.png)](./media/logic-apps-using-sap-connector/application-insights-query-panel.png#lightbox)

1. 在“日志”窗格中，可以使用基于特定要求的 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/concepts/) 创建[查询](/azure/data-explorer/kusto/query/)。

   可以使用类似于以下示例查询的查询模式：

   ```Kusto
   customMetrics
   | extend DestinationName = tostring(customDimensions["DestinationName"])
   | extend MetricType = tostring(customDimensions["MetricType"])
   | where customDimensions contains "RfcDestinationMonitor"
   | where name contains "MaxUsedCount"
   ```

1. 运行查询后，查看结果。

   以下屏幕截图显示示例查询的指标结果表：

   [![该屏幕截图显示 Application Insights 中的指标结果表。](./media/logic-apps-using-sap-connector/application-insights-metrics.png)](./media/logic-apps-using-sap-connector/application-insights-metrics.png#lightbox)

   * MaxUsedCount 是“受监视目标同时使用的最大客户端连接数”， 如 [SAP NCo 文档（需要登录）](https://support.sap.com/en/product/connectors/msnet.html#section_512604546)中所述。 可以使用该值来了解同时打开的连接数。

   * “valueCount”列的每个读数显示为 2，因为指标每隔 30 秒生成，而 Application Insights 每分钟聚合一次这些指标 。

   * DestinationName 列包含一个字符串，该字符串是 Microsoft SAP 适配器的内部名称。

     为了更好地了解此远程函数调用 (RFC) 目标，请将该值与 `traces` 配合使用，例如：

     ```Kusto
     customMetrics
     | extend DestinationName = tostring(customDimensions["DestinationName"])
     | join kind=inner (traces
        | extend DestinationName = tostring(customDimensions["DestinationName"]),
        AppServerHost = tostring(customDimensions["AppServerHost"]),
        SncMode = tostring(customDimensions["SncMode"]),
        SapClient = tostring(customDimensions["Client"])
        | where customDimensions contains "RfcDestinationMonitor"
        )
        on DestinationName , $left.DestinationName == $right.DestinationName
     | where customDimensions contains "RfcDestinationMonitor"
     | where name contains "MaxUsedCount"
     | project AppServerHost, SncMode, SapClient, name, valueCount, valueSum, valueMin, valueMax
     ```

也可以使用 Application Insights 中的以下功能创建指标图表或警报，例如：

[![该屏幕截图显示 Application Insights 中采用图表格式的结果。](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png)](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png#lightbox)

### <a name="traces-from-microsoft-sap-adapter"></a>来自 Microsoft SAP 适配器的跟踪

可以使用从 Microsoft SAP 适配器发送的跟踪来进行问题后分析，并查找任何现有的内部系统错误，这些错误可能会在 SAP 连接器操作中出现，也可能不会出现。 这些跟踪已将 `message` 设置为 `"n\a"`，因为它们来自早于 Application Insights 的早期事件源框架，例如：

```Kusto
traces
| where message == "n/a"
| where severityLevel > 0
| extend ActivityId = tostring(customDimensions["ActivityId"])
| extend fullMessage = tostring(customDimensions["fullMessage"])
| extend shortMessage = tostring(customDimensions["shortMessage"])
| where ActivityId contains "8ad5952b-371e-4d80-b355-34e28df9b5d1"
```

以下屏幕截图显示示例查询的跟踪结果表：

[![该屏幕截图显示 Application Insights 中的跟踪结果表。](./media/logic-apps-using-sap-connector/application-insights-traces.png)](./media/logic-apps-using-sap-connector/application-insights-traces.png#lightbox)

## <a name="advanced-scenarios"></a>高级方案

### <a name="change-language-headers"></a>更改语言标头

从逻辑应用连接到 SAP 时，连接的默认语言为英语。 可以通过对入站请求使用[标准 HTTP 标头 `Accept-Language`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) 来设置连接的语言。

> [!TIP]
> 大多数 web 浏览器会根据用户的设置来添加 `Accept-Language` 标头。 在工作流设计器中创建新的 SAP 连接时，Web 浏览器会应用此标头，更新 Web 浏览器的设置以使用首选语言，或使用 Azure 资源管理器而不是工作流设计器来创建 SAP 连接。

例如，可以使用“请求”触发器将带有 `Accept-Language` 标头的请求发送到逻辑应用工作流。 逻辑应用工作流中的所有操作都将收到该标头。 然后，SAP 在其系统消息（如 BAPI 错误消息）中使用指定的语言。

逻辑应用工作流的 SAP 连接参数没有语言属性。 因此，如果你使用 `Accept-Language` 标头，可能会出现以下错误：请检查你的帐户信息和/或权限，然后重试。 在这种情况下，请改为检查 SAP 组件的错误日志。 错误实际上发生在使用标头的 SAP 组件中，因此你可能会收到以下错误消息之一：

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`

* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>显式确认事务

将事务从逻辑应用发送到 SAP 时，此交换以两步方式完成，详见 SAP 文档：[Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)（事务性 RFC 服务器程序）。 默认情况下，“发送到 SAP”操作  在单个调用中处理函数传输步骤和事务确认步骤。 SAP 连接器提供将这两个步骤分离的选项。 可以发送 IDoc，并且可以使用显式的“\[IDOC] 确认事务 ID”操作，而不自动对事务进行确认。

不想在 SAP 中复制事务时（例如，在由于网络问题之类的原因而导致故障的情况下），可以使用这个分离事务 ID 确认的功能。 采用单独确认事务 ID 的方式，事务只在 SAP 系统中完成一次。

下面以示例方式展示了此模式：

1. 创建空白逻辑应用并添加请求触发器。

1. 从 SAP 连接器中，添加“\[IDOC] 将文档发送到 SAP”操作。 提供发送到 SAP 系统的 IDoc 的详细信息。

1. 若要在单独的步骤中显式确认事务 ID，请在“确认 TID”字段中选择“否”。   对于可选的“事务 ID GUID”字段，你可以手动指定值，或让连接器自动生成并在“\[IDOC] 发送文档到 SAP”操作的响应中返回此 GUID。

   ![屏幕截图：显示“[IDOC] 发送文档到 SAP”操作的属性](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 若要显式确认事务 ID，请添加“\[IDOC] 确认事务 ID”操作，确保[避免将重复的 IDoc 发送到 SAP](#avoid-sending-duplicate-idocs)。 在“事务 ID”框中单击，以显示动态内容列表。  在该列表中，选择从“\[IDOC] 将文档发送到 SAP”操作中返回的“事务 ID”值 。

   ![屏幕截图：显示“确认事务 ID”操作](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   此步骤完成以后，会在两端（SAP 连接器端和 SAP 系统端）将当前的事务标记为完成。

#### <a name="avoid-sending-duplicate-idocs"></a>避免发送重复的 IDoc

如果遇到逻辑应用工作流向 SAP 发送重复 IDoc 的问题，请按照以下步骤创建一个字符串变量作为 IDoc 事务标识符。 如果出现诸如临时中断、网络问题或丢失确认等问题，创建此事务标识符有助于防止重复的网络传输。

> [!NOTE]
> SAP 系统会在指定时间后（默认情况下为 24 小时）忘记事务标识符。 因此，如果 ID 或 GUID 未知，SAP 一定会确认事务标识符。
> 如果确认事务标识符失败，则表明在 SAP 能够确认之前，与 SAP 系统的通信就已失败。

1. 在工作流设计器中，将操作“初始化变量”添加到逻辑应用工作流。

1. 在操作“初始化变量”的编辑器中，配置以下设置。 然后保存更改。

   1. 对于“名称”，请输入变量的名称。 例如，`IDOCtransferID`。

   1. 对于“类型”，请选择“字符串”作为变量类型 。

   1. 对于“值”，请选择文本框“输入初始值”以打开动态内容菜单 。

   1. 选择“表达式”选项卡。在函数列表中，输入函数 `guid()`。

   1. 选择“确定”，保存所做更改。 现在将“值”字段设置为 `guid()` 函数，该函数将生成 GUID。

1. 在“初始化变量”操作后，添加操作“\[IDOC] 将文档发送到 SAP” 。

1. 在操作“\[IDOC] 将文档发送到 SAP”的编辑器中，配置以下设置。 然后保存更改。

   1. 对于“IDOC 类型”，选择消息类型；对于“输入 IDOC 消息”，请指定消息 。

   1. 对于“SAP 发行版”，选择 SAP 配置的值。

   1. 对于“记录类型版本”，选择 SAP 配置的值。

   1. 对于“确认 TID”，选择“否” 。

   1. 选择“添加新参数列表” > “事务ID GUID” 。

   1. 选择该文本框以打开动态内容菜单。 在“变量”选项卡下，选择所创建变量的名称，例如 `IDOCtransferID`。

1. 在操作“\[IDOC] 将文档发送到 SAP”的标题栏上，选择“...” > “设置”  。

   对于“重试策略”，建议选择“默认”&gt;“完成”  。 但是，你可以根据自己的特定需求配置自定义策略。 对于自定义策略，建议至少配置一次重试，以解决网络临时中断的问题。

1. 在操作“\[IDOC] 将文档发送到 SAP”后，添加操作“\[IDOC] 确认事务 ID” 。

1. 在操作“\[IDOC] 确认事务 ID”的编辑器中，配置以下设置。 然后保存更改。

1. 对于“事务 ID”，请再次输入变量的名称。 例如，`IDOCtransferID`。

1. （可选）在测试环境中验证重复数据删除。 使用与上一步相同的事务 ID GUID 重复执行“\[IDOC] 将文档发送到 SAP”操作 。

   当两次发送相同的 IDoc 时，可以验证 SAP 是否能够识别 tRFC 调用的重复项并将两个调用解析为一条入站 IDoc 消息。

## <a name="known-issues-and-limitations"></a>已知问题和限制

下面是托管（非 ISE）SAP 连接器目前存在的已知问题和限制：

* 通常，SAP 触发器不支持数据网关群集。 在某些故障转移案例中，与 SAP 系统通信的数据网关节点可能不同于主动节点，从而导致意外的行为。

  * 对于“发送”方案，支持在故障转移模式下使用数据网关群集。

  * 监控状态的 [SAP 操作](#actions)不支持在负载均衡模式下使用数据网关群集。 这些操作包括“\[BAPI - RFC] 创建监控状态会话”、“\[BAPI] 提交事务”、“\[BAPI] 回退事务”、“\[BAPI - RFC] 关闭监控状态会话”以及所有指定会话 ID 值的操作    。 监控状态通信必须保留在同一个数据网关群集节点上。

  * 对于监控状态 SAP 操作，请在非群集模式或仅为故障转移设置的群集中使用数据网关。

* SAP 连接器目前不支持 SAP 路由器字符串。 本地数据网关必须位于要连接的 SAP 系统所在的同一 LAN 中。

* 对于 [ISE 中的逻辑应用](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，此连接器的 ISE 标记版本改用 [ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="connector-reference"></a>连接器参考

有关 SAP 连接器的详细信息，请参阅[连接器参考](/connectors/sap/)。 你可以找到有关 SAP 连接器、触发器和操作的限制、参数和返回值的详细信息。

### <a name="triggers"></a>触发器

:::row:::
    :::column span="1":::
        [从 SAP 收到消息时](/connectors/sap/#when-a-message-is-received)
    :::column-end:::
    :::column span="3":::
        从 SAP 收到消息时，执行某些操作。
    :::column-end:::
:::row-end:::

### <a name="actions"></a>操作

:::row:::
    :::column span="1":::
        [[BAPI-RFC] 关闭监控状态会话](/connectors/sap/#[bapi---rfc]-close-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        关闭 SAP 系统的现有监控状态连接会话。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[BAPI-RFC] 创建监控状态会话](/connectors/sap/#[bapi---rfc]-create-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        创建 SAP 系统的监控状态连接会话。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[BAPI] 调用 SAP 中的方法](/connectors/sap/#[bapi]-call-method-in-sap-(preview))
    :::column-end:::
    :::column span="3":::
        在 SAP 系统中调用 BAPI 方法。
        \
        \
        调用时必须使用以下参数：    \
        \
        业务对象 (`businessObject`)，这是一个可搜索的下拉菜单。
        \
        \
        方法 (`method`)，它在你选择业务对象之后填充可用的方法 。 可用的方法因所选业务对象而异。
        \
        \
        输入 BAPI 参数 (`body`)，在其中调用包含 BAPI 方法输入参数值的 XML 文档，或者包含 BAPI 参数的存储 Blob 的 URI。
        \
        \
        有关如何使用“[BAPI] 调用 SAP 中的方法”操作的详细示例，请参阅 [BAPI 请求的 XML 示例](#xml-samples-for-bapi-requests)。
        \
        如果使用工作流设计器编辑 BAPI 请求，则可以使用以下搜索功能：    \
        \
        在设计器中选择一个对象，以查看可用方法的下拉菜单。
        \
        \
        使用 BAPI API 调用提供的可搜索列表，按关键字筛选业务对象类型。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[BAPI] 提交事务](/connectors/sap/#[bapi]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        提交会话的 BAPI 事务。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[BAPI] 回退事务](/connectors/sap/#[bapi]-roll-back-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        回退会话的 BAPI 事务。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[IDOC-RFC] 确认事务 ID](/connectors/sap/#[idoc---rfc]-confirm-transaction-id-(preview))
    :::column-end:::
    :::column span="3":::
        向 SAP 发送事务标识符确认。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[IDOC] 获取事务的 IDOC 列表](/connectors/sap/#[idoc]-get-idoc-list-for-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        按会话标识符或事务标识符获取事务的 IDoc 列表。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[IDOC] 获取 IDOC 状态](/connectors/sap/#[idoc]-get-idoc-status-(preview))
    :::column-end:::
    :::column span="3":::
        获取 IDoc 的状态。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[IDOC] 将文档发送到 SAP](/connectors/sap/#[idoc]-send-document-to-sap-(preview))
    :::column-end:::
    :::column span="3":::
        将 IDoc 消息发送到 SAP 服务器。
        \
        \
        调用时必须使用以下参数：    \
        \
        带有可选扩展的 IDOC 类型 (`idocType`)，这是一个可搜索的下拉菜单。
        \
        \
        输入 IDOC消息 (`body`)，在其中调用包含 IDoc 有效负载的 XML 文档，或包含 IDoc XML 文档的存储 blob 的 URI。 根据 WE60 IDoc 文档，此文档必须符合 SAP IDoc XML 架构，或符合为匹配的 SAP IDoc 操作 URI 生成的架构。
        \
        \
        选择 IDoc 类型后，可选参数 SAP 发行版 (`releaseVersion`) 会填充值，具体取决于所选的 IDoc 类型。
        \
        \
        有关如何使用“发送 IDoc”操作的详细示例，请参阅[将 IDoc 消息发送到 SAP 服务器的演练](#send-idoc-messages-to-sap-server)。
        \
        \
        有关如何使用可选参数“确认 TID”(`confirmTid`)，请参阅[显式确认事务的演练](#confirm-transaction-explicitly)。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[RFC] 将 RFC 添加到事务](/connectors/sap/#[rfc]-add-rfc-to-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        添加对事务的 RFC 调用。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[RFC] 调用 SAP 中的函数](/connectors/sap/#[rfc]-call-function-in-sap-(preview))
    :::column-end:::
    :::column span="3":::
        在 SAP 系统上调用 RFC 操作（sRFC、tRFC 或 qRFC）。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[RFC] 提交事务](/connectors/sap/#[rfc]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        提交会话和/或队列的 RFC 事务。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[RFC] 创建事务](/connectors/sap/#[rfc]-create-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        按标识符和/或队列名称创建新事务。 如果事务存在，则获取详细信息。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [[RFC] 获取事务](/connectors/sap/#[rfc]-get-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        按标识符和/或队列名称获取事务的详细信息。 如果不存在，则创建新事务。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**生成架构**](/connectors/sap/#generate-schemas)
    :::column-end:::
    :::column span="3":::
        为 IDoc、BAPI 或 RFC 生成 SAP 项目的架构。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [读取 SAP 表](/connectors/sap/#read-sap-table-(preview))
    :::column-end:::
    :::column span="3":::
        读取 SAP 表。
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**将消息发送到 SAP**](/connectors/sap/#send-message-to-sap)
    :::column-end:::
    :::column span="3":::
        向 SAP 发送任意消息类型（RFC、BAPI、IDoc）。
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>后续步骤

* 从 Azure 逻辑应用[连接到本地系统](logic-apps-gateway-connection.md)
* 了解如何通过 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 验证、转换和使用其他消息操作
* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
