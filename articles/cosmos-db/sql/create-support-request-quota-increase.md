---
title: 如何请求增加 Azure Cosmos DB 资源的配额
description: 了解如何请求增加 Azure Cosmos DB 资源的配额。 还将了解如何启用订阅来访问区域。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 8911bca3f93ec71559f79254dd5794c2387857b3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113531"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>如何请求增加 Azure Cosmos DB 资源的配额
[!INCLUDE[appliesto-all-apis](../includes/appliesto-all-apis.md)]

Azure Cosmos DB 中的资源具有[默认配额/限制](../concepts-limits.md)。 但是，在某些情况下，工作负载需要的配额高于默认值。 在这种情况下，你必须与 Azure Cosmos DB 团队联系以请求增加配额。 本文介绍如何请求增加 Azure Cosmos DB 资源的配额。 还将了解如何启用订阅来访问区域。

## <a name="create-a-new-support-request"></a>创建新的支持请求

若要请求增加配额，必须创建新支持请求并提供工作负载详细信息。 然后，Azure Cosmos DB 团队将评估请求并进行批准。 从 Azure 门户中使用以下步骤创建新的支持请求：

1. 登录到 Azure 门户。

1. 在左侧菜单中，选择“帮助和支持”，然后选择“新建支持请求”。

1. 在“基本信息”选项卡中填充以下详细信息：

   * 对于“问题类型”，选择“服务和订阅限制(配额)”  
   * 对于“订阅”，请选择要增加其配额的订阅。
   * 对于“配额类型”，请选择“Cosmos DB”

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="创建新的 Cosmos DB 支持请求以增加配额":::

1. 在“详细信息”选项卡中，输入对应于配额请求的详细信息。 此选项卡上提供的信息将用于进一步评估问题，并帮助支持工程师解决该问题。

1. 在此窗体中填充以下详细信息：

   * **说明**：提供请求的简短说明，如工作负载、默认值不够用的原因。 根据要增加配额的资源类型，必须在“说明”字段中提供以下详细信息：

     **区域请求** 如果你的请求是为了将区域添加到允许列表中，请确保提供以下值：

        * 区域名称
        * 订阅 ID

     **吞吐量限制请求** 如果你的请求是为了增加吞吐量的配额，请确保提供以下值：

        * 数据库名称
        * 订阅 ID
        * 新吞吐量限制

     **数据库帐户限制请求** 如果你的请求是为了增加订阅中数据库帐户数的配额，请确保提供以下值：

       * 订阅 ID
       * 新数据库帐户限制

   * **文件上传**：上传诊断文件或你认为与支持请求相关的任何其他文件。 若要详细了解文件上传指南，请参阅 [Azure 支持](../../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files)文章。

   * **严重性**：根据业务影响选择可用的严重性级别之一。

   * **首选联系方式**：可以选择通过“电子邮件”或“电话”进行联系。

1. 在窗体上填写剩余的详细信息，例如可用性、支持语言、联系信息、电子邮件和电话号码。

1. 选择“下一步: 审阅并创建”。 验证提供的信息，然后选择“创建”以创建支持请求。

Azure Cosmos DB 支持团队将在 24 小时内评估你的请求并与你联系。

## <a name="next-steps"></a>后续步骤

* 请参阅 [Azure Cosmos DB 默认服务配额](../concepts-limits.md)
