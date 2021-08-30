---
title: Azure Sentinel SAP 数据连接器专业配置选项、本地部署和 SAPControl 日志源 | Microsoft Docs
description: 了解如何使用专业配置选项和本地计算机为 SAP 环境部署 Azure Sentinel 数据连接器， 并详细了解 SAPControl 日志源。
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 05/19/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: ba0457bef8ad4e732cffe229e850272f68a6d30f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723439"
---
# <a name="expert-configuration-options-on-premises-deployment-and-sapcontrol-log-sources"></a>专业配置选项、本地部署和 SAPControl 日志源

本文介绍如何在专业或自定义进程中部署 Azure Sentinel SAP 数据连接器（例如使用本地计算机和 Azure 密钥保管库存储凭据）。

> [!NOTE]
> 部署 Azure Sentinel SAP 数据连接器的默认最佳方法是[使用 Azure VM](sap-deploy-solution.md)。 本文适用于高级用户。

> [!IMPORTANT]
> Azure Sentinel SAP 解决方案目前以预览版提供。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="prerequisites"></a>先决条件

无论采用何种部署方法，部署 Azure Sentinel SAP 数据连接器所需的基本先决条件都是相同的。

在开始之前，请确保系统符合主要的 [SAP 数据连接器部署教程](sap-deploy-solution.md#prerequisites)中记录的先决条件。

有关详细信息，请参阅 [Azure Sentinel SAP 解决方案的详细 SAP 要求（公共预览版）](sap-solution-detailed-requirements.md)。

## <a name="create-your-azure-key-vault"></a>创建 Azure 密钥保管库

创建专用于 Azure Sentinel SAP 数据连接器的 Azure 密钥保管库。

运行以下命令，以创建 Azure 密钥保管库，并授予对 Azure 服务主体的访问权限： 

``` azurecli
kvgp=<KVResourceGroup>

kvname=<keyvaultname>

spname=<sp-name>

kvname=<keyvaultname>
# Optional when Azure MI not enabled - Create sp user for AZ cli connection, save details for env.list file
az ad sp create-for-rbac –name $spname 

SpID=$(az ad sp list –display-name $spname –query “[].appId” --output tsv

#Create key vault
az keyvault create \
  --name $kvname \
  --resource-group $kvgp
  
# Add access to SP
az keyvault set-policy --name $kvname --resource-group $kvgp --object-id $spID --secret-permissions get list set
```

有关详细信息，请参阅[快速入门：使用 Azure CLI 创建密钥保管库](../key-vault/general/quick-create-cli.md)。

## <a name="add-azure-key-vault-secrets"></a>添加 Azure Key Vault 机密

若要添加 Azure Key Vault 机密，请使用你自己的系统 ID 和要添加的凭据运行以下脚本：

```azurecli
#Add Abap username
az keyvault secret set \
  --name <SID>-ABAPUSER \
  --value "<abapuser>" \
  --description SECRET_ABAP_USER --vault-name $kvname

#Add Abap Username password
az keyvault secret set \
  --name <SID>-ABAPPASS \
  --value "<abapuserpass>" \
  --description SECRET_ABAP_PASSWORD --vault-name $kvname

#Add java Username
az keyvault secret set \
  --name <SID>-JAVAOSUSER \
  --value "<javauser>" \
  --description SECRET_JAVAOS_USER --vault-name $kvname

#Add java Username password
az keyvault secret set \
  --name <SID>-JAVAOSPASS \
  --value "<javauserpass>" \
  --description SECRET_JAVAOS_PASSWORD --vault-name $kvname

#Add abapos username
az keyvault secret set \
  --name <SID>-ABAPOSUSER \
  --value "<abaposuser>" \
  --description SECRET_ABAPOS_USER --vault-name $kvname

#Add abapos username password
az keyvault secret set \
  --name <SID>-ABAPOSPASS \
  --value "<abaposuserpass>" \
  --description SECRET_ABAPOS_PASSWORD --vault-name $kvname

#Add Azure Log ws ID
az keyvault secret set \
  --name <SID>-LOG_WS_ID \
  --value "<logwsod>" \
  --description SECRET_AZURE_LOG_WS_ID --vault-name $kvname

#Add Azure Log ws public key
az keyvault secret set \
  --name <SID>-LOG_WS_PUBLICKEY \
  --value "<loswspubkey>" \
  --description SECRET_AZURE_LOG_WS_PUBLIC_KEY --vault-name $kvname
```

有关详细信息，请参阅 [az keyvault secret](/cli/azure/keyvault/secret) CLI 文档。

## <a name="perform-an-expert--custom-installation"></a>执行专业/自定义安装

此过程介绍如何使用专业或自定义安装（例如在本地安装时）部署 SAP 数据连接器。 

建议在为密钥保管库添加 SAP 凭据后执行此过程。

**部署 SAP 数据连接器**：

1. 在本地计算机上，从 [SAP 启动板站点](https://support.sap.com) > “SAP NW RFC SDK” > “SAP NW RFC SDK 7.50” > “nwrfc750X_X-xxxxxxx.zip”中下载最新的 SAP NW RFC SDK  。

    > [!NOTE]
    > 需要 SAP 用户登录信息才能访问此 SDK，并且必须下载与操作系统相匹配的 SDK。
    >
    > 确保选择“LINUX ON X86_64”选项。

1. 在本地计算机上，创建一个新文件夹并提供一个有意义的名称，然后将 SDK zip 文件复制到新文件夹中。

1. 将 Azure Sentinel 解决方案 GitHub 存储库克隆到本地计算机上，并将 Azure Sentinel SAP 解决方案 systemconfig.ini 文件复制到新文件夹中。

    例如：

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini 
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. 根据需要编辑 systemconfig.ini 文件，使用嵌入的注释作为指南。 有关详细信息，请参阅[手动配置 SAP 数据连接器](#manually-configure-the-sap-data-connector)。

    若要测试配置，你可能需要将用户和密码直接添加到 systemconfig.ini 配置文件。 尽管我们建议使用 [Azure 密钥保管库](#add-azure-key-vault-secrets)来存储凭据，但你也可以使用 env.list 文件和 [Docker 机密](#manually-configure-the-sap-data-connector)，或者将凭据直接添加到 systemconfig.ini 文件 。

1. 使用 systemconfig.ini 文件中的说明来定义要引入到 Azure Sentinel 中的日志。 有关示例，请参阅[定义发送到 Azure Sentinel 的 SAP 日志](#define-the-sap-logs-that-are-sent-to-azure-sentinel)。

1. 使用 systemconfig.ini 文件中的说明来定义以下配置：

    - 是否将用户电子邮件地址包含在审核日志中
    - 是否重试失败的 API 调用
    - 是否包括 cexal 审核日志
    - 是否在两次数据提取之间留出间隔时间，尤其是对于大型提取

    有关详细信息，请参阅 [SAL 日志连接器配置](#sal-logs-connector-settings)。

1. 将更新的 systemconfig.ini 文件保存到计算机上的 sapcon 目录中 。

1. 如果已选择使用 env.list 文件存储凭据，请创建一个包含所需凭据的临时 env.list 文件 。 Docker 容器正常运行后，请确保删除此文件。

    > [!NOTE]
    > 下面的脚本将每个 Docker 容器连接到特定的 ABAP 系统。 根据环境需要修改脚本。
    >

    运行：

    ```bash
    ##############################################################
    ##############################################################
    # env.list template for Credentials
    SAPADMUSER=<SET_SAPCONTROL_USER>
    SAPADMPASSWORD=<SET_SAPCONTROL_PASS>
    ABAPUSER=SET_ABAP_USER>
    ABAPPASS=<SET_ABAP_PASS>
    JAVAUSER=<SET_JAVA_OS_USER>
    JAVAPASS=<SET_JAVA_OS_USER>
    ##############################################################
    ##############################################################
    # env.list template for AZ Cli when MI is not enabled
    AZURE_TENANT_ID=<your tenant id>
    AZURE_CLIENT_ID=<your client/app id>
    ##############################################################
    ```

1. 在安装了 SAP 数据连接器的情况下，下载并运行预定义的 Docker 映像。  运行：

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker run --env-file=<env.list_location> -d --restart unless-stopped -v /home/$(pwd)/sapcon/<sap-sid>/:/sapcon-app/sapcon/config/system --name sapcon-<sid> sapcon
    rm -f <env.list_location>
    ```

1. 验证 Docker 容器是否正常运行。 运行：

    ```bash
    docker logs –f sapcon-[SID]
    ```

1. 继续部署“Azure Sentinel - 适用于 SAP 的持续威胁监视”解决方案。

    通过部署此解决方案，SAP 数据连接器可以在 Azure Sentinel 中显示，并支持部署 SAP 工作簿和分析规则。 完成后，手动添加和自定义 SAP 关注列表。

    有关详细信息，请参阅[部署 SAP 安全内容](sap-deploy-solution.md#deploy-sap-security-content)。

## <a name="manually-configure-the-sap-data-connector"></a>手动配置 SAP 数据连接器

Azure Sentinel SAP 解决方案数据连接器在 systemconfig.ini 文件中配置，该文件在[部署过程](#perform-an-expert--custom-installation)中已克隆到 SAP 数据连接器计算机。

以下代码显示 systemconfig.ini 文件示例：

```Python
[Secrets Source]
secrets = '<DOCKER_RUNTIME/AZURE_KEY_VAULT/DOCKER_SECRETS/DOCKER_FIXED>'
keyvault = '<SET_YOUR_AZURE_KEYVAULT>'
intprefix = '<SET_YOUR_PREFIX>'

[ABAP Central Instance]
##############################################################
# Define the following values according to your server configuration.
ashost = <SET_YOUR_APPLICATION_SERVER_HOST>
mshost = <SET_YOUR_MESSAGE_SERVER_HOST> - #In case different then App
##############################################################
group = <SET_YOUR_LOGON_GROUP>
msserv = <SET_YOUR_MS_SERVICE> - #Required only if the message server service is not defined as sapms<SYSID> in /etc/services
sysnr = <SET_YOUR_SYS_NUMBER>
user = <SET_YOUR_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
passwd = <SET_YOUR_PASSWORD>
snc_partnername = <SET_YOUR_SNC_PARTNER_NAME>
snc_lib = <SET_YOUR_SNC_LIBRARY_PATH>
x509cert = <SET_YOUR_X509_CERTIFICATE>
##############################################################
sysid = <SET_YOUR_SYSTEM_ID>
client = <SET_YOUR_CLIENT>

[Azure Credentials]
loganalyticswsid = <SET_YOUR_LOG_ANALYTICS_WORKSPACE_ID>
publickey = <SET_YOUR_PUBLIC_KEY>

[File Extraction ABAP]
osuser = <SET_YOUR_SAPADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
ospasswd = <SET_YOUR_SAPADM_PASS>
x509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
appserver = <SET_YOUR_SAPCTRL_SERVER IP OR FQDN>
instance = <SET_YOUR_SAP_INSTANCE NUMBER, example 10>
abapseverity = <SET_ABAP_SEVERITY 0 = All logs ; 1 = Warning ; 2 = Error>
abaptz = <SET_ABAP_TZ --Use ONLY GMT FORMAT-- example - For OS Timezone = NZST use abaptz = GMT+12>

[File Extraction JAVA]
javaosuser = <SET_YOUR_JAVAADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
javaospasswd = <SET_YOUR_JAVAADM_PASS>
javax509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
javaappserver = <SET_YOUR_JAVA_SAPCTRL_SERVER IP ADDRESS OR FQDN>
javainstance = <SET_YOUR_JAVA_SAP_INSTANCE for example 10>
javaseverity = <SET_JAVA_SEVERITY  0 = All logs ; 1 = Warning ; 2 = Error>
javatz = <SET_JAVA_TZ --Use ONLY GMT FORMAT-- example - For OS Timezone = NZST use javatz = GMT+12>
```

### <a name="define-the-sap-logs-that-are-sent-to-azure-sentinel"></a>定义发送到 Azure Sentinel 的 SAP 日志

将以下代码添加到 Azure Sentinel SAP 解决方案 systemconfig.ini 文件中，以定义发送到 Azure Sentinel 的日志。

有关详细信息，请参阅 [Azure Sentinel SAP 解决方案日志参考（公共预览版）](sap-solution-log-reference.md)。

```Python
##############################################################
# Enter True OR False for each log to send those logs to Azure Sentinel
[Logs Activation Status]
ABAPAuditLog = True
ABAPJobLog = True
ABAPSpoolLog = True
ABAPSpoolOutputLog = True
ABAPChangeDocsLog = True
ABAPAppLog = True
ABAPWorkflowLog = True
ABAPCRLog = True
ABAPTableDataLog = False
# ABAP SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
ABAPFilesLogs = False
SysLog = False
ICM = False
WP = False
GW = False
# Java SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
JAVAFilesLogs = False
##############################################################
```

### <a name="sal-logs-connector-settings"></a>SAL 日志连接器设置

将以下代码添加到 Azure Sentinel SAP 数据连接器 systemconfig.ini 文件中，以定义已引入到 Azure Sentinel 中的 SAP 日志的其他设置。

有关详细信息，请参阅[执行专业/自定义 SAP 数据连接器安装](#perform-an-expert--custom-installation)。


```Python
##############################################################
[Connector Configuration]
extractuseremail = True
apiretry = True
auditlogforcexal = False
auditlogforcelegacyfiles = False
timechunk = 60
##############################################################
```

通过本部分，你能够配置以下参数：

|参数名称  |说明  |
|---------|---------|
|**extractuseremail**     |  确定用户电子邮件地址是否包含在审核日志中。       |
|**apiretry**     |   确定是否将 API 调用作为故障转移机制进行重试。      |
|**auditlogforcexal**     |  确定系统是否对非 SAL 系统（如 SAP BASIS 版本 7.4）强制使用审核日志。       |
|**auditlogforcelegacyfiles**     |  确定系统是否强制使用带有旧系统功能（例如补丁级别较低的 SAP BASIS 版本 7.4 的功能）的审核日志。|
|**timechunk**     |   确定系统在两次数据提取之间等待特定的分钟数。 如果预期有大量数据，请使用此参数。 <br><br>例如，在第一个 24 小时内的初始数据加载期间，你可能希望数据提取每 30 分钟运行一次，以便每次有足够的时间提取数据。 在这种情况下，请将此值设置为 30。  |
|     |         |

### <a name="configuring-an-abap-sap-control-instance"></a>配置 ABAP SAP Control 实例

要将所有 ABAP 日志（包括基于 NW RFC 和 SAP Control Web 服务的日志）引入 Azure Sentinel，请配置以下 ABAP SAP Control 详细信息：

|设置  |说明  |
|---------|---------|
|**javaappserver**     |输入 SAP Control ABAP 服务器主机。 <br>例如：`contoso-erp.appserver.com`         |
|**javainstance**     |输入 SAP Control ABAP 实例编号。 <br>例如：`00`         |
|**abaptz**     |输入在 SAP Control ABAP 服务器上配置的时区（GMT 格式）。 <br>例如：`GMT+3`         |
|**abapseverity**     |输入要将 ABAP 日志引入 Azure Sentinel 的最低（含）严重性级别。  值包括： <br><br>- **0** = 所有日志 <br>- **1** = 警告 <br>- **2** = 错误     |


### <a name="configuring-a-java-sap-control-instance"></a>配置 Java SAP Control 实例

要将 SAP Control Web 服务日志引入 Azure Sentinel，请配置以下 JAVA SAP Control 实例详细信息：

|参数  |说明  |
|---------|---------|
|**javaappserver**     |输入 SAP Control Java 服务器主机。 <br>例如：`contoso-java.server.com`         |
|**javainstance**     |输入 SAP Control ABAP 实例编号。 <br>例如：`10`         |
|**javatz**     |输入在 SAP Control Java 服务器上配置的时区（GMT 格式）。 <br>例如：`GMT+3`         |
|**javaseverity**     |输入要将 Web 服务日志引入 Azure Sentinel 的最低（含）严重性级别。  值包括： <br><br>- **0** = 所有日志 <br>- **1** = 警告 <br>- **2** = 错误     |

## <a name="next-steps"></a>后续步骤

安装 SAP 数据连接器后，可以添加与 SAP 相关的安全内容。

有关详细信息，请参阅[部署 SAP 解决方案](sap-deploy-solution.md#deploy-sap-security-content)。

有关详细信息，请参阅：

- [Azure Sentinel SAP 解决方案详细的 SAP 要求](sap-solution-detailed-requirements.md)
- [Azure Sentinel SAP 解决方案日志参考](sap-solution-log-reference.md)
- [Azure Sentinel SAP 解决方案：安全内容参考](sap-solution-security-content.md)
- [Azure Sentinel SAP 解决方案部署故障排除](sap-deploy-troubleshoot.md)
