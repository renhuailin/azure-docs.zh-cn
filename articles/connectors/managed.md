---
title: 托管连接器操作
description: 使用 Microsoft 托管触发器和操作创建使用 Azure 逻辑应用集成其他应用、数据、服务和系统的自动工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 05/16/2021
ms.openlocfilehash: c3010cb1d972bb898fd8346266166bb632191ead
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259080"
---
# <a name="managed-connectors-in-azure-logic-apps"></a>Azure 逻辑应用中的托管连接器

[托管连接器](apis-list.md)提供了一些方法，供你访问未提供[内置触发器和操作](built-in.md)的其他服务和系统。 可以使用这些触发器和操作创建集成数据、应用、基于云的服务和本地系统的工作流。 与内置触发器和操作相比，这些连接器通常绑定到特定服务或系统，例如 Azure Blob 存储、Office 365、SQL、Salesforce 或 SFTP 服务器。 托管连接器由 Microsoft 管理并在 Azure 中承载，通常要求首先从工作流创建连接并验证身份。 同时提供了定期和基于 Webhook 的触发器，因此如果使用定期触发器，请查看[定期行为概述](apis-list.md#recurrence-behavior)。

对于少量服务、系统和协议，Azure 逻辑应用提供内置操作以及其[托管连接器版本](managed.md)。 根据创建的是在多租户 Azure 逻辑应用中运行的基于消耗计划的逻辑应用资源，还是在单租户 Azure 逻辑应用中运行的基于标准计划的逻辑应用资源，可用的数量和范围有所不同。 有关详细信息，请参阅[单租户与多租户以及集成服务环境 (ISE)](../logic-apps/single-tenant-overview-compare.md)。 在大多数情况下，内置版本提供更好的性能、更全面的功能以及更实惠的价格。

例如，如果创建单租户逻辑应用，则内置操作可用于 Azure 服务总线、Azure 事件中心、SQL Server 和 MQ。 在少数情况下，内置版本和托管连接器版本都可用。 在大多数情况下，内置版本提供更好的性能、更全面的功能以及更实惠的价格。 如果创建多租户逻辑应用，则内置操作可用于 Azure Functions、Azure 应用服务和 Azure API Management。

Azure 逻辑应用中的某些托管连接器属于多个子类别。 例如，SAP 连接器既是[企业连接器](#enterprise-connectors)，也是[本地连接器](#on-premises-connectors)。

* [标准连接器](#standard-connectors)提供对 Azure Blob 存储、Office 365、SharePoint、Salesforce、Power BI、OneDrive 等服务的访问。
* [企业连接器](#enterprise-connectors)提供对 SAP、IBM MQ 和 IBM 3270 等企业系统的访问。
* [本地连接器](#on-premises-connectors)提供对本地系统（如 SQL Server、SharePoint Server、SAP、Oracle DB、文件共享等）的访问。
* [集成帐户连接器](#integration-account-connectors)可帮助转换和验证 XML、编码和解码平面文件，以及使用 AS2、EDIFACT 和 X12 协议处理企业到企业 (B2B) 消息。
* [集成服务环境连接器](#ise-connectors)，专用于在 ISE 中运行，并提供相较于非 ISE 版本的优势。

## <a name="standard-connectors"></a>标准连接器

Azure 逻辑应用提供这些常用的标准连接器，以便使用这些服务和系统构建自动工作流。 某些标准连接器还支持[本地系统](#on-premises-connectors)或[集成帐户](#integration-account-connectors)。

:::row:::
    :::column:::
        [![Azure 服务总线图标][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure 服务总线**][azure-service-bus-doc]
        \
        \
        使用逻辑应用中最常用的连接器管理异步消息、会话和主题订阅。
    :::column-end:::
    :::column:::
        [![SQL Server 图标][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        连接到本地 SQL Server 或云中的 Azure SQL 数据库，以便可以管理记录、运行存储过程或执行查询。
    :::column-end:::
    :::column:::
        [![Azure Blob 存储图标][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob 存储**][azure-blob-storage-doc]
        \
        \
        连接到 Azure 存储帐户，以便可以创建和管理 Blob 内容。
    :::column-end:::
    :::column:::
        [![Office 365 Outlook 图标][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        连接到工作或学校电子邮件帐户，以便可以创建和管理电子邮件、任务、日历事件和会议、联系人、请求，等等。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![STFP-SSH 图标][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        使用 SSH 连接到可从 Internet 访问的 SFTP 服务器，以便可以处理文件和文件夹。
    :::column-end:::
    :::column:::
        [![SharePoint Online 图标][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        连接到 SharePoint Online，以便可以管理文件、附件、文件夹，等等。
    :::column-end:::
    :::column:::
        [![Azure 队列图标][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure 队列**][azure-queues-doc]
        \
        \
        连接到 Azure 存储帐户，以便创建和管理队列与消息。
    :::column-end:::
    :::column:::
        [![FTP 图标][ftp-icon]][ftp-doc]
        \
        \
        [**FTP**][ftp-doc]
        \
        \
        连接到可从 Internet 访问的 FTP 服务器，以便可以处理文件和文件夹。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![文件系统图标][file-system-icon]][file-system-doc]
        \
        \
        [**文件系统**][file-system-doc]
        \
        \
        连接到本地文件共享，以便可以创建和管理文件。
    :::column-end:::
    :::column:::
        [![Azure 事件中心图标][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure 事件中心**][azure-event-hubs-doc]
        \
        \
        通过事件中心使用和发布事件。 例如，通过事件中心获取逻辑应用的输出，然后将输出发送到实时分析提供程序。
    :::column-end:::
    :::column:::
        [![Azure 事件网格图标][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure 事件网格**][azure-event-grid-doc]
        \
        \
        监视事件网格发布的事件（例如，当 Azure 资源或第三方资源发生更改时）。
    :::column-end:::
    :::column:::
        [![Salesforce 图标][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        连接到 Salesforce 帐户，以便可以创建和管理记录、作业、对象，等等。
    :::column-end:::
:::row-end:::

## <a name="on-premises-connectors"></a>本地连接器

在创建与本地系统的连接之前，必须先[下载、安装并设置本地数据网关][gateway-doc]。 此网关提供安全信道，无需设置所需的网络基础结构。

以下连接器是 Azure 逻辑应用提供的一些常用[标准连接器](#standard-connectors)，用于访问本地系统中的数据和资源。 对于本地连接器列表，请参阅[支持的数据源](../logic-apps/logic-apps-gateway-connection.md#supported-connections)。

:::row:::
    :::column:::
        [![Biztalk Server 图标][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![文件系统图标][file-system-icon]][file-system-doc]
        \
        \
        [**文件系统**][file-system-doc]
    :::column-end:::
    :::column:::
        [![IBM DB2 图标][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![IBM Informix 图标][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![MySQL 图标][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB 图标][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![PostgreSQL 图标][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![SharePoint Server 图标][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server 图标][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Teradata 图标][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>集成帐户连接器

集成帐户连接器专门支持 Azure 逻辑应用中的[企业到企业 (B2B) 通信方案](../logic-apps/logic-apps-enterprise-integration-overview.md)。 [创建集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)并定义 B2B 项目（如贸易合作伙伴、协议、映射和架构）后，可以使用集成帐户连接器对消息进行编码和解码、转换内容等。

例如，如果使用 Microsoft BizTalk Server，则可以使用 [BizTalk Server 本地连接器](#on-premises-connectors)从工作流创建连接。 然后，可以使用这些集成帐户连接器在工作流中扩展或执行类似于 BizTalk 的操作。

> [!NOTE]
> 必须先[将逻辑应用资源链接到集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，然后才能在基于消耗计划的多租户 Azure 逻辑应用中使用集成帐户连接器。 

:::row:::
    :::column:::
        [![AS2 解码图标][as2-icon]][as2-doc]
        \
        \
        [**AS2 解码**][as2-doc]
    :::column-end:::
    :::column:::
        [![AS2 编码图标][as2-icon]][as2-doc]
        \
        \
        [**AS2 编码**][as2-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT 解码图标][edifact-icon]][edifact-decode-doc]
        \
        \
        [**EDIFACT 解码**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT 编码图标][edifact-icon]][edifact-encode-doc]
        \
        \
        [**EDIFACT 编码**][edifact-encode-doc]
    :::column-end:::
    :::column:::
        [![X12 解码图标][x12-icon]][x12-decode-doc]
        \
        \
        [**X12 解码**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![X12 编码图标][x12-icon]][x12-encode-doc]
        \
        \
        [**X12 编码**][x12-encode-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>企业连接器

以下连接器提供对企业系统的访问，会产生额外成本：

:::row:::
    :::column:::
        [![IBM 3270 图标][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![IBM MQ 图标][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP 图标][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="ise-connectors"></a>ISE 连接器

在集成服务环境 (ISE) 中，这些托管连接器还具有 [ISE 版本](apis-list.md#ise-and-connectors)，这些版本的功能与多租户版本不同：

> [!NOTE]
> 在 ISE 及其连接器中运行的逻辑应用（无论这些连接器在何处运行）遵循固定定价计划与基于消耗量的定价计划。 有关详细信息，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)和[逻辑应用定价详细信息](https://azure.microsoft.com/pricing/details/logic-apps/)。

:::row:::
    :::column:::
        [![AS2 ISE 图标][as2-icon]][as2-doc]
        \
        \
        [AS2 ISE][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure 自动化 ISE 图标][azure-automation-icon]][azure-automation-doc]
        \
        \
        [Azure 自动化 ISE][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob 存储 ISE 图标][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [Azure Blob 存储 ISE][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB ISE 图标][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [Azure Cosmos DB ISE][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure 事件中心 ISE 图标][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [Azure 事件中心 ISE][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure 事件网格 ISE 图标][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [Azure 事件网格 ISE][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Azure 文件存储 ISE 图标][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Azure 文件存储** ISE][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure 密钥保管库 ISE 图标][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [Azure Key Vault ISE][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor 日志 ISE 图标][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [Azure Monitor 日志 ISE][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure 服务总线 ISE 图标][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [Azure 服务总线 ISE][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ISE 图标][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [Azure Synapse Analytics ISE][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Azure 表存储 ISE 图标][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [Azure 表存储 ISE][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure 队列 ISE 图标][azure-queues-icon]][azure-queues-doc]
        \
        \
        [Azure 队列 ISE][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT ISE 图标][edifact-icon]][edifact-doc]
        \
        \
        [EDIFACT ISE][edifact-doc]
    :::column-end:::
    :::column:::
        [![文件系统 ISE 图标][file-system-icon]][file-system-doc]
        \
        \
        [文件系统 ISE][file-system-doc]
    :::column-end:::
    :::column:::
        [![FTP ISE 图标][ftp-icon]][ftp-doc]
        \
        \
        [FTP ISE][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![IBM 3270 ISE 图标][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [IBM 3270 ISE][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![IBM DB2 ISE 图标][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [IBM DB2 ISE][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![IBM MQ ISE 图标][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [IBM MQ ISE][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP ISE 图标][sap-icon]][sap-connector-doc]
        \
        \
        [SAP ISE][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ISE 图标][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [SFTP-SSH ISE][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![SMTP ISE 图标][smtp-icon]][smtp-doc]
        \
        \
        [SMTP ISE][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server ISE 图标][sql-server-icon]][sql-server-doc]
        \
        \
        [SQL Server ISE][sql-server-doc]
    :::column-end:::
    :::column:::
        [![X12 ISE 图标][x12-icon]][x12-doc]
        \
        \
        [X12 ISE][x12-doc]
    :::column-end:::
:::row-end:::

有关详细信息，请参阅以下主题：

* [从 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)
* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建可从逻辑应用调用的自定义 API](../logic-apps/logic-apps-create-api-app.md)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "为云和本地基础结构创建和管理自动化作业"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "使用 Azure Blob 存储连接器管理 Blob 容器中的文件"
[azure-cosmos-db-doc]: /connectors/documentdb/ "连接到 Azure Cosmos DB，以便访问文档和存储过程"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "监视事件网格发布的事件（例如，当 Azure 资源或第三方资源发生更改时）"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "连接到 Azure 事件中心，以便在逻辑应用与事件中心之间接收和发送事件"
[azure-file-storage-doc]: /connectors/azurefile/ "连接到 Azure 存储帐户，以便创建、更新、获取和删除文件"
[azure-key-vault-doc]: /connectors/keyvault/ "连接到 Azure 密钥保管库，以便管理机密和密钥"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "在 Log Analytics 工作区和 Application Insights 组件之间针对 Azure Monitor 日志运行查询"
[azure-queues-doc]: /connectors/azurequeues/ "连接到 Azure 存储帐户，以便创建和管理队列与消息"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "从服务总线队列和主题发送消息，并从服务总线队列和订阅接收消息"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "连接到 Azure Synapse Analytics，以便可以查看数据"
[azure-table-storage-doc]: /connectors/azuretables/ "连接到 Azure 存储帐户，以便创建、更新和查询表与其他对象"
[biztalk-server-doc]: /connectors/biztalk/ "连接到 BizTalk Server，以便将基于 BizTalk 的应用程序与 Azure 逻辑应用一起运行"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "连接到本地文件系统"
[ftp-doc]: ./connectors-create-api-ftp.md "连接到 FTP/FTPS 服务器以执行 FTP 任务，例如上传、获取、删除文件，等等"
[github-doc]: ./connectors-create-api-github.md "连接到 GitHub，对问题进行跟踪"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "连接到 Google Calendar，以便管理日历"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "连接到 Google Sheets，以便修改工作表"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "连接到 Google Tasks，以便管理任务"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "连接到 IBM 大型机上的 3270 应用"
[ibm-db2-doc]: ./connectors-create-api-db2.md "连接到云中或本地的 IBM DB2。更新行、获取表，等等"
[ibm-informix-doc]: ./connectors-create-api-informix.md "连接到云中或本地的 Informix。读取行、列出表，等等"
[ibm-mq-doc]: ./connectors-create-api-mq.md "连接到本地或 Azure 中的 IBM MQ 以发送和接收消息"
[instagram-doc]: ./connectors-create-api-instagram.md "连接到 Instagram。触发事件或针对事件进行操作"
[mandrill-doc]: ./connectors-create-api-mandrill.md "连接到 Mandrill 进行通信"
[mysql-doc]: /connectors/mysql/ "连接到本地 MySQL 数据库，以便读取和写入数据"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "连接到工作或学校帐户，以便发送和接收电子邮件、管理日历和联系人，以及执行其他操作"
[onedrive-doc]: ./connectors-create-api-onedrive.md "连接到个人 Microsoft OneDrive，以便上传、删除、列出文件，以及执行其他操作"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "连接到企业 Microsoft OneDrive，以便上传、删除、列出文件，以及执行其他操作"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "连接到 Oracle 数据库，以便添加、插入、删除行，以及执行其他操作"
[outlook.com-doc]: ./connectors-create-api-outlook.md "连接到 Outlook 邮箱，以便管理电子邮件、日历、联系人，以及执行其他操作"
[postgre-sql-doc]: /connectors/postgresql/ "连接到 PostgreSQL 数据库，以便从表中读取数据"
[salesforce-doc]: ./connectors-create-api-salesforce.md "连接到 Salesforce 帐户。管理帐户、潜在客户、商机等"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "连接到本地 SAP 系统"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "连接到 SendGrid。发送电子邮件和管理收件人列表"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "使用 SSH 连接到 SFTP 帐户。上传、获取、删除文件，等等"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "连接到 SharePoint 本地服务器。管理文档、列出项，等等"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "连接到 SharePoint Online。管理文档、列出项，等等"
[slack-doc]: ./connectors-create-api-slack.md "连接到 Slack，并将消息发布到 Slack 通道"
[smtp-doc]: ./connectors-create-api-smtp.md "连接到 SMTP 服务器并发送带附件的电子邮件"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "连接到 SparkPost 进行通信"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "连接到 Azure SQL 数据库或 SQL Server。在 SQL 数据库表中创建、更新、获取和删除条目"
[teradata-doc]: /connectors/teradata/ "连接到 Teradata 数据库以从表中读取数据"
[twilio-doc]: ./connectors-create-api-twilio.md "连接到 Twilio。发送和获取消息、获取可用号码，管理来电号码，等等"
[youtube-doc]: ./connectors-create-api-youtube.md "连接到 YouTube。管理视频和频道"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[x12-icon]: ./media/apis-list/x12.png

<!-- Integration account connector docs -->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "对使用 AS2 协议的消息进行编码和解码"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "对使用 EDIFACT 协议的消息进行编码和解码"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "对使用 EDIFACT 协议的消息进行解码"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "对使用 EDIFACT 协议的消息进行编码"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "对使用 X12 协议的消息进行编码和解码"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "对使用 X12 协议的消息进行解码"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "对使用 X12 协议的消息进行编码"

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "通过本地数据网关，从逻辑应用连接到本地数据源"