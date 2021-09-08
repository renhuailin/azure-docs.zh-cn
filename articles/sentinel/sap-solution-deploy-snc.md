---
title: 通过安全网络通信 (SNC) 部署 Azure Sentinel SAP 数据连接器 | Microsoft Docs
description: 了解如何通过 SNC 与基于 NetWeaver/ABAP 接口的日志建立安全连接，来为 SAP 环境部署 Azure Sentinel 数据连接器。
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 08/01/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: cdc576951702c8d381ebd3ae0661b0d40be98a09
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868251"
---
# <a name="deploy-the-azure-sentinel-sap-data-connector-with-snc"></a>通过 SNC 部署 Azure Sentinel SAP 数据连接器

本文介绍了如何在通过安全网络通信 (SNC) 与基于 NetWeaver/ABAP 接口的日志建立到 SAP 的安全连接时部署 Azure Sentinel SAP 数据连接器。

> [!NOTE]
> 部署 Azure Sentinel SAP 数据连接器的默认最佳方法是[使用 Azure VM](sap-deploy-solution.md)。 本文适用于高级用户。

> [!IMPORTANT]
> Azure Sentinel SAP 解决方案目前以预览版提供。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="prerequisites"></a>先决条件

无论采用何种部署方法，部署 Azure Sentinel SAP 数据连接器所需的基本先决条件都是相同的。

在开始之前，请确保系统满足主要的 [SAP 数据连接器部署过程](sap-deploy-solution.md#prerequisites)中记录的先决条件。

使用 SNC 的其他先决条件包括：

- 通过 SNC 与 SAP 建立安全连接。 在要连接到的 AS ABAP 系统的存储库常量中定义特定于连接的 SNC 参数。 有关详细信息，请参阅 [SAP 社区 Wiki 页面](https://wiki.scn.sap.com/wiki/display/Security/Securing+Connections+to+AS+ABAP+with+SNC)。

- SAPCAR 实用工具，可从 SAP 服务市场下载。 有关详细信息，请参阅 [SAP 安装指南](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/467291d0dc104d19bba073a0380dc6b4.html)

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

## <a name="deploy-the-sap-data-connector"></a>部署 SAP 数据连接器

此过程介绍如何在通过 SNC 进行连接时在 VM 上部署 SAP 数据连接器。

建议在为[密钥保管库](#create-your-azure-key-vault)添加 [SAP 凭据](#add-azure-key-vault-secrets)后执行此过程。

**部署 SAP 数据连接器**：

1. 在数据连接器 VM 上，通过 [SAP 启动板站点](https://support.sap.com) > “SAP NW RFC SDK” > “SAP NW RFC SDK 7.50” > “nwrfc750X_X-xxxxxxx.zip”下载最新的 SAP NW RFC SDK  。

    > [!NOTE]
    > 需要 SAP 用户登录信息才能访问此 SDK，并且必须下载与操作系统相匹配的 SDK。
    >
    > 确保选择“LINUX ON X86_64”选项。

1. 创建一个新文件夹，并提供一个有意义的名称，然后将 SDK zip 文件复制到新文件夹中。

1. 将 Azure Sentinel 解决方案 GitHub 存储库克隆到数据连接器 VM 上，并将 Azure Sentinel SAP 解决方案 systemconfig.ini 文件复制到新文件夹中。

    例如：

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. 根据需要编辑 systemconfig.ini 文件，使用嵌入的注释作为指南。

    需要编辑除密钥保管库机密之外的所有配置。 有关详细信息，请参阅[手动配置 SAP 数据连接器](sap-solution-deploy-alternate.md#manually-configure-the-sap-data-connector)。

1. 使用 systemconfig.ini 文件中的说明来定义要引入到 Azure Sentinel 中的日志。 

    有关示例，请参阅[定义发送到 Azure Sentinel 的 SAP 日志](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-azure-sentinel)。

    > [!NOTE]
    > SNC 通信的相关日志仅包括通过 NetWeaver/ABAP 接口检索到的日志。 SAP Control 和 HANA 日志超出了 SNC 的范围。
    >

1. 使用 systemconfig.ini 文件中的说明来定义以下配置：

    - 是否将用户电子邮件地址包含在审核日志中
    - 是否重试失败的 API 调用
    - 是否包括 cexal 审核日志
    - 是否在两次数据提取之间留出间隔时间，尤其是对于大型提取

    有关详细信息，请参阅 [SAL 日志连接器配置](sap-solution-deploy-alternate.md#sal-logs-connector-settings)。

1. 将更新后的 systemconfig.ini 文件保存到 VM 上的 sapcon 目录中 。

1. 在安装了 SAP 数据连接器的情况下，下载并运行预定义的 Docker 映像。  运行：

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker create -v $(pwd):/sapcon-app/sapcon/config/system -v /home/azureuser /sap/sec:/sapcon-app/sec --env SCUDIR=/sapcon-app/sec --name sapcon-snc mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    ```


## <a name="post-deployment-sap-system-procedures"></a>部署后 SAP 系统过程

部署 SAP 数据连接器后，执行以下 SAP 系统过程：

1. 通过 [SAP 服务市场](https://launchpad.support.sap.com/#/) > “软件下载” > “浏览下载目录” > “SAP 加密软件”下载 SAP 加密库  。

    有关详细信息，请参阅 [SAP 安装指南](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/86921b29cac044d68d30e7b125846860.html)。

1. 使用 SAPCAR 实用工具提取库文件，并将文件部署到 `<sec>` 目录中的 SAP 数据连接器 VM。

1. 验证是否拥有运行库文件的权限。

1. 定义一个名为 SECUDIR 的环境变量，其值为 `<sec>` 目录的完整路径。

1. 创建个人安全环境 (PSE)。 SAP 数据连接器 VM 的 `<sec>` 目录中提供了 sapgenspe 命令行工具。

    例如：

    ```bash
    ./sapgenpse get_pse -p my_pse.pse -noreq -x my_pin "CN=sapcon.com, O=my_company, C=IL"
    ```

    有关详细信息，请参阅 SAP 文档中的[创建个人安全环境](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/285bb1fda3fa472c8d9205bae17a6f95.html)。

1. 为 PSE 创建凭据。 例如：

    ```bash
    ./sapgenpse seclogin -p my_pse.pse -x my_pin -O MXDispatcher_Service_User
    ```

    有关详细信息，请参阅 SAP 文档中的[创建凭据](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/d8b50371667740e797e6c9f0e9b7141f.html)。

1. 在标识中心和 AS ABAP 的 SNC PSE 之间交换公钥证书。

    例如，若要导出标识中心的公钥证书，请运行以下命令：

    ```bash
    ./sapgenpse export_own_cert -o my_cert.crt -p my_pse.pse -x abcpin
    ```

    将证书导入 AS ABAP 的 SNC PSE，再从 PSE 导出，然后导入回标识中心。

    例如，若要将证书导入标识中心，请运行以下命令：

    ```bash
    ./sapgenpse maintain_pk -a full_path/my_secure_dir/my_exported_cert.crt -p my_pse.pse -x my_pin
    ```

    有关详细信息，请参阅 SAP 文档中的[交换公钥证书](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/7bbf90b29c694e6080e968559170fbcd.html)。


## <a name="edit-the-sap-data-connector-configuration"></a>编辑 SAP 数据连接器配置

1. 在 SAP 数据连接器 VM 中，导航到 systemconfig.ini 文件，然后使用相关值定义以下参数：

    ```ini
    [Secrets Source]
    secrets = AZURE_KEY_VAULT
    ```

1. 在 [Azure 密钥保管库](#create-your-azure-key-vault)中，生成以下机密：

    - `<Interprefix>-ABAPSNCPARTNERNAME`，其中值是 `<Relevant DN details>`
    - `<Interprefix>-ABAPSNCLIB`，其中值是 `<lib_Path>`
    - `<Interprefix>-ABAPX509CERT`，其中值是 `<Certificate_Code>)`

    例如：

    ```ini
    S4H-ABAPSNCPARTNERNAME  =  'p:CN=help.sap.com, O=SAP_SE, C=IL' (Relevant DN)
    S4H-ABAPSNCLIB = 'home/user/sec-dir' (Relevant directory)
    S4H-ABAPX509CERT = 'MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO' (Relevant certificate code)
    ```

    > [!NOTE]
    > 默认情况下，`<Interprefix>` 值是 SID，例如 `A4H-<ABAPSNCPARTNERNAME>`。
    >

如果要将机密直接输入配置文件，请按如下所示定义参数：

```ini
[Secrets Source]
secrets = DOCKER_FIXED
[ABAP Central Instance]
snc_partnername =  <Relevant_DN_Deatils>
snc_lib =  <lib_Path>
x509cert = <Certificate_Code>
For example:
snc_partnername =  p:CN=help.sap.com, O=SAP_SE, C=IL (Relevant DN)
snc_lib = /sapcon-app/sec/libsapcrypto.so (Relevant directory)
x509cert = MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO (Relevant certificate code)
```

### <a name="attach-the-snc-parameters-to-your-user"></a>将 SNC 参数附加到用户

1. 在 SAP 数据连接器 VM 上，调用 `SM30` 事务并选择维护 `USRACLEXT` 表。

1. 添加新条目。 在“用户”字段中，输入用于连接到 ABAP 系统的通信用户。

1. 在系统提示时输入 SNC 名称。 SNC 名称是在创建 Identity Manager PSE 时提供的唯一可分辨名称。 例如：`CN=IDM, OU=SAP, C=DE`

    请确保在 SNC 名称前添加 `p`。 例如：`p:CN=IDM, OU=SAP, C=DE`。

1. 选择“保存”  。

数据连接器 VM 上已启用 SNC。

## <a name="activate-the-sap-data-connector"></a>激活 SAP 数据连接器

此过程描述如何使用本文前面的过程创建的安全 SNC 连接来激活 SAP 数据连接器。

1. 激活 docker 映像：

    ```bash
    docker start sapcon-<SID>
    ```

1. 检查连接。 运行：

    ```bash
    docker logs sapcon-<SID>
    ```

1. 如果连接失败，请使用日志来了解问题。

    如果需要，请禁用 docker 映像：

    ```bash
    docker stop sapcon-<SID>
    ```

例如，出现问题的原因可能是 systemconfig.ini 文件或 Azure 密钥保管库中配置错误，或者通过 SNC 创建安全连接的某些步骤未正确运行。

请尝试再次执行上述步骤来通过 SNC 配置安全连接。 有关详细信息，请参阅[对 Azure Sentinel SAP 解决方案部署进行故障排除](sap-deploy-troubleshoot.md)。

## <a name="next-steps"></a>后续步骤

激活 SAP 数据连接器后，请继续部署“Azure Sentinel - 适用于 SAP 的持续威胁监视”解决方案。 有关详细信息，请参阅[部署 SAP 安全内容](sap-deploy-solution.md#deploy-sap-security-content)。

通过部署此解决方案，SAP 数据连接器可以在 Azure Sentinel 中显示，并支持部署 SAP 工作簿和分析规则。 完成后，手动添加和自定义 SAP 关注列表。

有关详细信息，请参阅：

- [Azure Sentinel SAP 解决方案详细的 SAP 要求](sap-solution-detailed-requirements.md)
- [Azure Sentinel SAP 解决方案日志参考](sap-solution-log-reference.md)
- [Azure Sentinel SAP 解决方案：安全内容参考](sap-solution-security-content.md)

