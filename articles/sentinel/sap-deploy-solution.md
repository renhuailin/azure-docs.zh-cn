---
title: 部署 SAP 连续威胁监视 | Microsoft Docs
description: 了解如何为 SAP 环境部署 Azure Sentinel 解决方案。
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 07/06/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: 7bddb61bbbab008fad4e538400bbe4396ac744b4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723456"
---
#  <a name="deploy-sap-continuous-threat-monitoring-public-preview"></a>部署 SAP 连续威胁监视（公共预览版）

本文将引导你逐步完成为 SAP 部署 Azure Sentinel 连续威胁监视的过程。

> [!IMPORTANT]
> Azure Sentinel SAP 解决方案目前以预览版提供。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="overview"></a>概述

[Azure Sentinel 解决方案](sentinel-solutions.md)包括捆绑的安全内容，例如威胁检测、工作簿和监视列表。 通过这些解决方案，你能够使用单个进程为特定数据连接器载入 Azure Sentinel 安全内容。

借助 Azure Sentinel SAP 数据连接器，可以监视 SAP 系统，以应对业务层和应用程序层中的复杂威胁。

SAP 数据连接器从整个 SAP 系统环境中流式传输大量（14 个）应用程序日志，通过 NetWeaver RFC 调用从高级业务应用程序编程 (ABAP) 收集日志，并通过 OSSAP 控制接口收集文件存储数据。 SAP 数据连接器增加了 Azure Sentinels 监视 SAP 基础结构的能力。

要将 SAP 日志引入 Azure Sentinel，必须在 SAP 环境中安装 Azure Sentinel SAP 数据连接器。 建议在 Azure VM 上使用 Docker 容器进行部署，如本教程中所述。

部署 SAP 数据连接器后，部署 SAP 解决方案安全内容，以顺利深入了解组织的 SAP 环境并改进任何相关的安全操作功能。

在本教程中，学习以下内容：

> [!div class="checklist"]
> * 如何为 SAP 数据连接器部署准备 SAP 系统
> * 如何使用 Docker 容器和 Azure VM 部署 SAP 数据连接器
> * 如何在 Azure Sentinel 中部署 SAP 解决方案安全内容

## <a name="prerequisites"></a>先决条件

为了按照本教程中的说明部署 Azure Sentinel SAP 数据连接器和安全内容，必须具备以下先决条件：

|区域  |说明  |
|---------|---------|
|**Azure 先决条件**     |  对 Azure Sentinel 的访问权限。 请记下你的 Azure Sentinel 工作区 ID 及密钥，以便在本教程中[部署 SAP 数据连接器](#deploy-your-sap-data-connector)时使用。 <br>要从 Azure Sentinel 查看这些详细信息，请转到“设置” > “工作区设置” > “代理管理”  。 <br><br>能够创建 Azure 资源。 有关详细信息，请参阅 [Azure 资源管理器文档](../azure-resource-manager/management/manage-resources-portal.md)。 <br><br>对 Azure 密钥保管库的访问权限。 本教程介绍了使用 Azure 密钥保管库来存储凭据的建议步骤。 有关详细信息，请参阅 [Azure Key Vault 文档](../key-vault/index.yml)。       |
|**系统先决条件**     |   软件。 SAP 数据连接器部署脚本会自动安装必备软件。 有关详细信息，请参阅[自动安装的软件](#automatically-installed-software)。 <br><br> 系统连接。 确保用作 SAP 数据连接器主机的 VM 有权访问： <br>- Azure Sentinel <br>- Azure 密钥保管库 <br>- SAP 环境主机，通过以下 TCP 端口：32xx、5xx13 和 33xx，其中 xx 是 SAP 实例编号   。 <br><br>请确保你还拥有 SAP 用户帐户，以便访问 SAP 软件下载页面。<br><br>系统体系结构。 SAP 解决方案作为 Docker 容器部署在 VM 上，每个 SAP 客户端都需要自己的容器实例。 有关大小建议，请参阅[建议的虚拟机大小](sap-solution-detailed-requirements.md#recommended-virtual-machine-sizing)。 <br>你的 VM 和 Azure Sentinel 工作区可以位于不同的 Azure 订阅中，甚至可以位于不同的 Azure AD 租户中。|
|**SAP 先决条件**     |   支持的 SAP 版本。 建议使用 [SAP_BASIS 版本 750 SP13](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) 或更高版本。 <br>如果你使用的是较旧的 SAP 版本 [SAP_BASIS 740](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows)，本教程中的选择步骤提供了其他说明。<br><br> SAP 系统详细信息。 请记下以下 SAP 系统详细信息以供本教程使用：<br>    - SAP 系统 IP 地址<br>- SAP 系统编号，如 `00`<br>    - SAP 系统 ID（来自 SAP NetWeaver 系统）。 例如 `NPL`。 <br>- SAP 客户端 ID，如 `001`。<br><br>SAP NetWeaver 实例访问权限。 访问 SAP 实例必须使用以下选项之一： <br>- [SAP ABAP 用户/密码](#configure-your-sap-system)。 <br>- 具有 X509 证书的用户，使用 SAP CRYPTOLIB PSE。 此选项可能需要专家手动步骤。<br><br>SAP 团队的支持。  需要 SAP 团队的支持，以确保为解决方案部署[正确配置](#configure-your-sap-system) SAP 系统。   |
|     |         |


### <a name="automatically-installed-software"></a>自动安装的软件

[SAP 数据连接器部署脚本](#deploy-your-sap-data-connector)使用 SUDO（根）权限在 VM 上安装以下软件：

- [Unzip.](https://www.microsoft.com/en-us/p/unzip/9mt44rnlpxxt?activetab=pivot:overviewtab)
- [NetCat](https://sectools.org/tool/netcat/)
- [Python 3.6 或更高版本](https://www.python.org/downloads/)
- [Python3-pip](https://pypi.org/project/pip/)
- [Docker](https://www.docker.com/)

## <a name="configure-your-sap-system"></a>配置 SAP 系统

此过程介绍如何确保 SAP 系统安装了正确的必备组件，并针对 Azure Sentinel SAP 数据连接器部署进行了配置。

> [!IMPORTANT]
> 与 SAP 团队一起执行此过程以确保正确配置。
>

**为 SAP 数据连接器配置 SAP 系统：**

1. 确保在系统中根据你的版本部署以下 SAP 说明：

    |SAP Basis 版本  |所需注释 |
    |---------|---------|
    |- 750 SP01 到 SP12<br>- 751 SP01 到 SP06<br>- 752 SP01 到 SP03     |  2641084：安全审核日志数据的标准化读取访问       |
    |- 700 到 702<br>- 710 到 711、730、731、740 和 750     | 2173545：CD：CHANGEDOCUMENT_READ_ALL        |
    |- 700 到 702<br>- 710 到 711、730、731 和 740<br>- 750 到 752     | 2502336：CD（更改文档）：RSSCD100 - 仅从存档读取，而不从数据库中读取        |
    |     |         |

    更高版本不需要额外的说明。 有关详细信息，请参阅 [SAP 支持速启动板站点](https://support.sap.com/en/index.html)，使用 SAP 用户帐户登录。

1. 从位于 https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR: 的 Azure Sentinel GitHub 存储库下载并安装以下 SAP 更改请求之一

    - **SAP 版本 750 或更高版本**：安装 SAP 更改请求 144 (NPLK900144)
    - **SAP 版本 740**：安装 SAP 更改请求 146 (NPLK900146)

    执行此步骤时，请确保使用二进制模式将文件传输到 SAP 系统，并使用 STMS_IMPORT SAP 事务代码。

    > [!NOTE]
    > 在 SAP“导入选项”区域中，可能显示“忽略无效的组件版本”选项 。 如果显示，请选择此选项，然后继续。
    >

1. 通过导入 SAP 更改请求 14 (NPLK900140)，创建名为 /MSFTSEN/SENTINEL_CONNECTOR 的新 SAP 角色。 使用 STMS_IMPORT SAP 事务代码。

    验证创建的角色是否具有所需的权限，例如：

    :::image type="content" source="media/sap/required-sap-role-authorizations.png" alt-text="Azure Sentinel SAP 数据连接器所需的 SAP 角色权限。":::

    有关详细信息，请参阅 [ABAP 用户的授权](sap-solution-detailed-requirements.md#required-abap-authorizations)。

1. 为 SAP 数据连接器创建一个非对话框的 RFC/NetWeaver 用户并附加新创建的 /MSFTSEN/SENTINEL_CONNECTOR 角色。

    - 附加角色后，验证是否已向用户分配角色权限。
    - 此过程需要用到 ABAP 用户的用户名和密码。 创建新用户并获得所需权限后，请务必更改 ABAP 用户密码。

1. 下载 SAP NetWeaver RFC SDK 7.50 for Linux on x86_64 64 BIT 版本并将其放置在 VM 上，因为在安装过程中需要用到它。

    例如，在 [SAP 软件下载站点](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT) > SAP NW RFC SDK > SAP NW RFC SDK 7.50 > nwrfc750X_X-xxxxxxx.zip 上查找 SDK  。 确保下载 LINUX ON X86_64 65BIT 选项。 将文件（例如使用 SCP）复制到 VM。

    需要一个 SAP 用户帐户来访问 SAP 软件下载页面。

1. （可选）SAP Auditlog 文件在系统范围内使用并支持多个 SAP 客户端。 但是，Azure Sentinel SAP 解决方案的每个实例仅支持单个 SAP 客户端。

    因此，如果拥有多客户端 SAP 系统，建议仅为部署 SAP 解决方案的客户端启用 Auditlog 文件以避免数据重复。


## <a name="deploy-a-linux-vm-for-your-sap-data-connector"></a>为 SAP 数据连接器部署 Linux VM

此过程介绍了如何使用 Azure CLI 部署 Ubuntu 服务器 18.04 LTS VM 并为其分配[系统托管标识](../active-directory/managed-identities-azure-resources/index.yml)。

> [!TIP]
> 还可以在 RHEL 7.7 及更高版本或 SUSE 15 及更高版本上部署数据连接器。 请注意，必须完全更新所有 OS 和修补程序。
>

**部署和准备 Ubuntu VM**：

1. 使用以下命令作为示例，插入资源组和 VM 名称的值：

    ```azurecli
    az vm create  --resource-group [resource group name]   --name [VM Name] --image UbuntuLTS  --admin-username AzureUser --data-disk-sizes-gb 10 – --size Standard_DS2_– --generate-ssh-keys  --assign-identity
    ```

1. 在新的 VM 上，安装：

    - [Venv](https://docs.python.org/3.8/library/venv.html)，使用 Python 3.8 或更高版本。
    - [Azure CLI](/cli/azure/)，版本 2.8.0 或更高版本。

> [!IMPORTANT]
> 请确保为组织应用任何安全最佳做法，就像对任何其他 VM 一样。
>

有关详细信息，请参阅[快速入门：使用 Azure CLI 创建 Linux 虚拟机](../virtual-machines/linux/quick-create-cli.md)。

## <a name="create-key-vault-for-your-sap-credentials"></a>为 SAP 凭据创建密钥保管库

本教程使用新创建的或专用的 [Azure 密钥保管库](../key-vault/index.yml)来存储 SAP 数据连接器的凭据。

**创建 Azure 密钥保管库或将密钥保管库设置为专用**：

1. 创建一个新的 Azure 密钥保管库，或选择一个现有的密钥保管库专用于 SAP 数据连接器部署。

    例如，要创建新的密钥保管库，请运行以下命令，使用密钥保管库资源组的名称并输入密钥保管库名称：

    ```azurecli
    kvgp=<KVResourceGroup>

    kvname=<keyvaultname>

    #Create Key Vault
    az keyvault create \
      --name $kvname \
      --resource-group $kvgp
    ```

1. 为 VM 的托管标识分配访问策略，包括 GET、LIST 和 SET 权限。

    在 Azure 密钥保管库中，选择“访问策略” > “添加访问策略 - 机密权限: Get、List 和 Set” > “选择主体”  。 输入 [VM 名称](#deploy-a-linux-vm-for-your-sap-data-connector)，然后选择“添加” > “保存” 。

    有关详细信息，请参阅 [Key Vault 文档](../key-vault/general/assign-access-policy-portal.md)。

1. 运行以下命令以获取 [VM 的主体 ID](#deploy-a-linux-vm-for-your-sap-data-connector)，输入 Azure 资源组的名称：

    ```azurecli
    VMPrincipalID=$(az vm show -g [resource group] -n [Virtual Machine] --query identity.principalId -o tsv)
    ```

    将显示你的主体 ID，供你在以下步骤中使用。

1. 运行以下命令将 VM 的访问权限分配给密钥保管库，输入资源组的名称和上一步返回的主体 ID 值。

    ```azurecli
    az keyvault set-policy -n [key vault] -g [resource group] --object-id $VMPrincipalID --secret-permissions get list set
    ```

## <a name="deploy-your-sap-data-connector"></a>部署 SAP 数据连接器

Azure Sentinel SAP 数据连接器部署脚本会安装[所需的软件](#automatically-installed-software)，然后在[新创建的 VM](#deploy-a-linux-vm-for-your-sap-data-connector) 上安装连接器，将凭据存储在[专用密钥保管库](#create-key-vault-for-your-sap-credentials)中。

SAP 数据连接器部署脚本存储在 [Azure Sentinel GitHub 存储库 > DataConnectors > SAP](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh) 中。

要运行 SAP 数据连接器部署脚本，将需要以下详细信息：

- Azure Sentinel 工作区详细信息，如[先决条件](#prerequisites)部分所列。
- [先决条件](#prerequisites)部分列出的 SAP 系统详细信息。
- 使用 SUDO 权限访问 VM 用户。
- 在[配置 SAP 系统](#configure-your-sap-system)中创建的 SAP 用户，应用了 /MSFTSEN/SENTINEL_CONNECTOR 角色。
- SAP 团队的帮助。

**运行 SAP 解决方案部署脚本**：

1. 运行以下命令以在 VM 上部署 SAP 解决方案：

    ```azurecli
    wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-kickstart.sh
    ```

1. 按照屏幕上的说明输入 SAP 和密钥保管库详细信息并完成部署。 部署完成后会出现一条确认消息：

    ```azurecli
    The process has been successfully completed, thank you!
    ```

    Azure Sentinel 开始在配置的时间范围内检索 SAP 日志，直到初始化时间前 24 小时。

1. 建议检查系统日志以确保数据连接器在传输数据。 运行：

    ```bash
    docker logs -f sapcon-[SID]
    ```

## <a name="deploy-sap-security-content"></a>部署 SAP 安全内容

从 Azure Sentinel“解决方案”和“监视列表”区域部署 [SAP 安全内容](sap-solution-security-content.md) 。

“Azure Sentinel - 适用于 SAP 的持续威胁监视”解决方案使 SAP 数据连接器能够显示在 Azure Sentinel“数据连接器”区域中，并部署“SAP - 系统应用程序及产品”工作簿以及与 SAP 相关的分析规则  。

将与 SAP 相关的监视列表手动添加到 Azure Sentinel 工作区。

**部署 SAP 解决方案安全内容**：

1. 从 Azure Sentinel 导航菜单中选择“解决方案(预览版)”。

    “解决方案”页面显示经过筛选的可搜索解决方案列表。

1. 选择“Azure Sentinel - 适用于 SAP 的持续威胁监视(预览版)”，打开 SAP 解决方案页面。

    :::image type="content" source="media/sap/sap-solution.png" alt-text="Azure Sentinel - 适用于 SAP 的持续威胁监视（预览版）解决方案。":::

1. 选择“创建”以启动解决方案部署向导，然后输入要在其中部署解决方案的 Azure 订阅、资源组和 Log Analytics 工作区的详细信息。

1. 选择“下一步”以循环显示“数据连接器”、“分析”和“工作簿”选项卡，你可在其中了解将随此解决方案一起部署的组件   。

    此工作簿的默认名称为“SAP - 系统应用程序及产品 - 预览版”。 可根据需要在“工作簿”选项卡中进行更改。

    有关详细信息，请参阅 [Azure Sentinel SAP 解决方案：安全内容参考（公共预览版）](sap-solution-security-content.md)。

1. 在“查看 + 创建”选项卡中，等待“已通过验证”消息，然后选择“创建”以部署解决方案  。

    > [!TIP]
    > 还可以选择“下载模板”以获得将解决方案部署为代码的链接。

1. 部署完成后，页面右上角会出现一条确认消息。

    要显示新部署的内容，请转到：

    - “威胁管理” > “工作簿” > “我的工作簿”，找到[内置 SAP 工作簿](sap-solution-security-content.md#built-in-workbooks)  。
    - “配置” > “分析”，找到一系列[与 SAP 相关的分析规则](sap-solution-security-content.md#built-in-analytics-rules) 。

1. 添加与 SAP 相关的监视列表，以便在搜索、检测规则、威胁搜寻和响应 playbook 中使用。 这些监视列表为 Azure Sentinel SAP 连续威胁监视解决方案提供配置。

    1. 从位于 https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists 的 Azure Sentinel GitHub 存储库下载 SAP 监视列表。

    1. 在 Azure Sentinel“监视列表”区域中，将监视列表添加到 Azure Sentinel 工作区。 使用下载的 CSV 文件作为源，然后根据环境需要对其进行自定义。 

        [![添加到 Azure Sentinel 的 SAP 相关监视列表。](media/sap/sap-watchlists.png)](media/sap/sap-watchlists.png#lightbox)

        有关详细信息，请参阅[使用 Azure Sentinel 监视列表](watchlists.md)和[可用的 SAP 监视列表](sap-solution-security-content.md#available-watchlists)。

1. 在 Azure Sentinel 中，导航到“适用于 SAP 的 Azure Sentinel 持续威胁监视”数据连接器以确认连接：

    [![适用于 SAP 的 Azure Sentinel 持续威胁监视数据连接器页面。](media/sap/sap-data-connector.png)](media/sap/sap-data-connector.png#lightbox)

    SAP ABAP 日志显示在 Azure Sentinel“日志”页面中的“自定义日志”下 ：

    [![Azure Sentinel 中“自定义日志”下的 SAP ABAP 日志。](media/sap/sap-logs-in-sentinel.png)](media/sap/sap-logs-in-sentinel.png#lightbox)

    有关详细信息，请参阅 [Azure Sentinel SAP 解决方案日志参考（公共预览版）](sap-solution-log-reference.md)。


## <a name="update-your-sap-data-connector"></a>更新 SAP 数据连接器

如果已使用早期版本的 SAP 数据连接器运行 Docker 容器，请运行 SAP 数据连接器更新脚本以获取可用的最新功能。

1. 确保你拥有来自 Azure Sentinel github 存储库的最新版本的相关部署脚本。 运行：

    ```azurecli
    wget -O sapcon-instance-update.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-instance-update.sh && bash ./sapcon-instance-update.sh
    ```

1. 在 SAP 数据连接器计算机上运行以下命令：

    ```azurecli
    ./ sapcon-instance-update.sh
    ```

已更新计算机上的 SAP 数据连接器 Docker 容器。

## <a name="collect-sap-hana-audit-logs"></a>收集 SAP HANA 审核日志

如果你的 SAP HANA 数据库审核日志已配置 Syslog，则还需要配置 Log Analytics 代理以收集 Syslog 文件。

1. 请确保将 SAP HANA 审核日志跟踪配置为使用 Syslog，如 SAP 说明 0002624117 中所述，可从 [SAP 启动板支持站点](https://launchpad.support.sap.com/#/notes/0002624117)访问。 有关详细信息，请参阅：

    - [SAP HANA 审核线索 - 最佳做法](https://archive.sap.com/documents/docs/DOC-51098)
    - [审核建议](https://help.sap.com/viewer/742945a940f240f4a2a0e39f93d3e2d4/2.0.05/en-US/5c34ecd355e44aa9af3b3e6de4bbf5c1.html)

1. 查看操作系统 Syslog 文件，了解任何相关的 HANA 数据库事件。

1. 在计算机上安装并配置 Log Analytics 代理：

    1. 以具有 sudo 权限的用户身份登录 HANA 数据库操作系统。
    1. 在 Azure 门户中，转到 Log Analytics 工作区。 在左侧的“设置”下，选择“代理管理 > Linux 服务器” 。
    1. 将“下载和载入适用于 Linux 的代理”下的框中显示的代码复制到终端，并运行脚本。

    Log Analytics 代理已安装在计算机上并已连接到工作区。 有关详细信息，请参阅 Microsoft GitHub 存储库上的[在 Linux 计算机上安装 Log Analytics 代理](../azure-monitor/agents/agent-linux.md)和[适用于 Linux 的 OMS 代理](https://github.com/microsoft/OMS-Agent-for-Linux)。

1. 刷新“代理管理 > Linux 服务器”选项卡，查看是否连接了 1 台 Linux 计算机。

1. 在左侧的“设置”下，选择“代理配置”，然后选择“Syslog”选项卡。  

1. 选择“添加设施”以添加要收集的设施。 

    > [!TIP]
    > 由于保存 HANA 数据库事件的设施在不同的发行版中可能会发生变化，因此建议添加所有设施，对照 Syslog 日志进行检查，然后删除任何不相关的设施。
    >

1. 在 Azure Sentinel 中，检查 HANA 数据库事件现在是否显示在引入的日志中。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Sentinel SAP 解决方案：

- [专家配置选项、本地部署和 SAPControl 日志源](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP 解决方案详细的 SAP 要求](sap-solution-detailed-requirements.md)
- [Azure Sentinel SAP 解决方案日志参考](sap-solution-log-reference.md)
- [Azure Sentinel SAP 解决方案：内置安全内容](sap-solution-security-content.md)
- [Azure Sentinel SAP 解决方案部署故障排除](sap-deploy-troubleshoot.md)

有关详细信息，请参阅 [Azure Sentinel 解决方案](sentinel-solutions.md)。
