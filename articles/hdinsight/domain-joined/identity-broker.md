---
title: Azure HDInsight ID 代理 (HIB)
description: 了解 Azure HDInsight ID 代理如何简化已加入域的 Apache Hadoop 群集的身份验证。
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 8201935f9aff99c9fa2709fe0b1418b0dab56e87
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737769"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight ID 代理 (HIB)

本文介绍了如何设置和使用 Azure HDInsight ID 代理功能。 可以使用此功能获得对 Apache Ambari 的新式 OAuth 身份验证，同时执行多重身份验证，而无需在 Azure Active Directory 域服务 (Azure AD DS) 中使用旧密码哈希。

## <a name="overview"></a>概述

在以下情况下，HDInsight ID 代理简化了复杂的身份验证设置：

* 你的组织依赖于联合身份验证对访问云资源的用户进行身份验证。 以前，若要使用 HDInsight 企业安全性套餐群集，必须启用从本地环境到 Azure Active Directory (Azure AD) 的密码哈希同步。 对于某些组织而言，此要求可能比较困难或不理想。
* 你的组织希望对基于 Web 或 HTTP 的 Apache Ambari 和其他群集资源的访问强制执行多重身份验证。

HDInsight ID 代理提供身份验证基础结构，支持协议从 OAuth（新式）转换到 Kerberos（旧式），而无需将密码哈希同步到 Azure AD DS。 此基础结构包括在启用了 HDInsight ID 代理节点的 Windows Server 虚拟机 (VM) 上运行的组件以及群集网关节点。

使用下表根据你的组织需求确定最佳身份验证选项。

|身份验证选项 |HDInsight 配置 | 需考虑的因素 |
|---|---|---|
| 完全 OAuth | 企业安全性套餐 + HDInsight ID 代理 | 最安全的选项。 （支持多重身份验证。）不需要哈希传递同步。 不支持本地帐户的 ssh/kinit/keytab 访问，这些帐户在 Azure AD DS 中没有密码哈希。 仅限云的帐户仍然可以使用 ssh/kinit/keytab 访问。 通过 OAuth 实现的对 Ambari 的基于 Web 的访问。 需要更新旧版应用（例如 JDBC/ODBC）以支持 OAuth。|
| OAuth + 基本身份验证 | 企业安全性套餐 + HDInsight ID 代理 | 通过 OAuth 实现的对 Ambari 的基于 Web 的访问。 旧版应用继续使用基本身份验证。对于基本身份验证访问，必须禁用多重身份验证。 不需要哈希传递同步。 不支持本地帐户的 ssh/kinit/keytab 访问，这些帐户在 Azure AD DS 中没有密码哈希。 仅限云的帐户仍然可以使用 ssh/kinit 访问。 |
| 完全基本身份验证 | 企业安全性套餐 | 最类似于本地设置。 需要将密码哈希同步到 Azure AD DS。 本地帐户可以使用 ssh/kinit 或使用 keytab。 如果后备存储是 Azure Data Lake Storage Gen2，则必须禁用多重身份验证。 |

下图显示了启用 HDInsight ID 代理后针对所有用户（包括联合用户）的基于 OAuth 的新式身份验证流：

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="显示使用 HDInsight ID 代理的身份验证流的示意图。" border="false":::

在此图中，客户端（即浏览器或应用）需要首先获取 OAuth 令牌。 然后，将该令牌提供给 HTTP 请求中的网关。 如果已登录到其他 Azure 服务（例如 Azure 门户），可以使用单一登录体验登录到 HDInsight 群集。

仍有许多旧版应用程序仅支持基本身份验证（即用户名和密码）。 对于这些情况，仍然可以使用 HTTP 基本身份验证连接到群集网关。 在此设置中，必须确保从网关节点到 Active Directory 联合身份验证服务 (AD FS) 终结点的网络连接性，从而确保网关节点的直接视线。

下图显示了联合用户的基本身份验证流。 首先，网关尝试使用 [ROPC 流](../../active-directory/develop/v2-oauth-ropc.md)完成身份验证。 如果没有密码哈希同步到 Azure AD，则会回退到发现 AD FS 终结点并通过访问 AD FS 终结点完成身份验证。

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="显示基本身份验证体系结构的示意图。" border="false":::

## <a name="enable-hdinsight-id-broker"></a>启用 HDInsight ID 代理

创建启用了 HDInsight ID 代理的企业安全性套餐群集：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 按照企业安全性套餐群集的基本创建步骤进行操作。 有关详细信息，请参阅[创建使用企业安全性套餐的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)。
1. 选择“启用 HDInsight ID 代理”。

HDInsight ID 代理功能将向群集添加一个额外的 VM。 此 VM 是 HDInsight ID 代理节点，包括用来支持身份验证的服务器组件。 HDInsight ID 代理节点以域加入方式加入到 Azure AD DS 域。

:::image type="content" source="./media/identity-broker/identity-broker-enable.png" alt-text="显示用于启用 HDInsight ID 代理的选项的示意图。" border="true":::

### <a name="use-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

如果将名为 `idbrokernode` 且具有以下特性的新角色添加到模板的计算配置文件中，则会在启用了 HDInsight ID 代理节点的情况下创建群集：

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

若要查看 ARM 模板的完整示例，请参阅[此处](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)发布的模板。


## <a name="tool-integration"></a>工具集成

更新 HDInsight 工具以本机支持 OAuth。 使用这些工具对群集进行基于 OAuth 的新式访问。 HDInsight [IntelliJ 插件](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib)可以用于基于 Java 的应用程序，例如 Scala。 [适用于 Visual Studio Code 的 Spark 和 Hive 工具](../hdinsight-for-vscode.md)可用于 PySpark 和 Hive 作业。 这些工具同时支持批处理和交互式作业。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>在 Azure AD DS 中在没有密码哈希的情况下进行 SSH 访问

|SSH 选项 |需考虑的因素 |
|---|---|
| 本地 VM 帐户（例如 sshuser） | 你创建群集时提供了此帐户。 此帐户没有 Kerberos 身份验证。 |
| 仅限云的帐户（例如 alice@contoso.onmicrosoft.com） | 密码哈希在 Azure AD DS 中可用。 Kerberos 身份验证可以通过 SSH Kerberos 进行。 |
| 本地帐户（例如 alice@contoso.com） | 只有在 Azure AD DS 中提供了密码哈希，才能进行 SSH Kerberos 身份验证。 否则，该用户无法通过 SSH 连接到群集。 |

若要通过 SSH 连接到已加入域的 VM，或者要运行 `kinit` 命令，必须提供密码。 SSH Kerberos 身份验证要求 Azure AD DS 中存在哈希。 如果只想将 SSH 用于管理方案，则可创建一个仅限云的帐户，并使用该帐户通过 SSH 连接到群集。 其他本地用户仍可使用 Ambari、HDInsight 工具或 HTTP 基本身份验证，而不需要在 Azure AD DS 中有密码哈希。

如果你的组织未将密码哈希同步到 Azure AD DS，则最佳做法是在 Azure AD 中创建一个仅限云的用户。 然后，在创建群集时将其分配为群集管理员，并将其用于管理目的。 可以使用该用户通过 SSH 获取对 VM 的根访问权限。

若要排查身份验证问题，请参阅[此指南](./domain-joined-authentication-issues.md)。

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>客户端使用 OAuth 连接到使用 HDInsight ID 代理的 HDInsight 网关

在 HDInsight ID 代理设置中，可以更新连接到网关的自定义应用和客户端，以便首先获取所需的 OAuth 令牌。 请按照[此文档](../../storage/common/storage-auth-aad-app.md)中的步骤使用以下信息获取令牌：

*    OAuth 资源 URI：`https://hib.azurehdinsight.net` 
*   AppId：7865c1d2-f040-46cc-875f-831a1ef6a28a
*    权限：（名称：Cluster.ReadWrite，id：8f89faa0-ffef-4007-974d-4989b39ad77d）

获取 OAuth 令牌后，在向群集网关（例如 https://\<clustername\>-int.azurehdinsight.net）发出的 HTTP 请求的授权标头中使用该令牌。 Apache livy API 的示例 curl 命令可能如下例所示：
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

若要使用 Beeline 和 Livy，还可以按照[此处](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples)提供的示例代码来设置客户端，以使用 OAuth 并连接到群集。

## <a name="faq"></a>常见问题解答
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>AAD 中的 HDInsight 创建了什么应用？
对于每个群集，会在 AAD 中注册第三方应用程序，并将群集 URI 作为 identifierUri（如 `https://clustername.azurehdinsight.net`）。

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>为什么在使用支持 HIB 的群集之前，用户会看到要求提供许可的提示？
在 AAD 中，所有第三方应用程序都需要先获得用户许可才能对用户进行身份验证或访问数据。

### <a name="can-the-consent-be-approved-programatically"></a>可否以编程方式提供许可？
Microsoft Graph API 允许自动提供许可，请参阅 [API 文档](/graph/api/resources/oauth2permissiongrant)了解相关信息。自动提供许可的顺序为：

* 注册应用并向应用授予 Application.ReadWrite.All 权限，以访问 Microsoft Graph
* 创建群集后，基于标识符 URI 查询群集应用
* 注册应用的许可

删除群集后，HDInsight 会删除该应用，无需清除任何许可。

 


## <a name="next-steps"></a>后续步骤

* [使用 Azure Active Directory 域服务配置具有企业安全性套餐的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)
* [将 Azure Active Directory 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
* [监视群集性能](../hdinsight-key-scenarios-to-monitor.md)