---
title: 使用 Fortanix Confidential Computing Manager 运行应用
description: 了解如何使用 Fortanix Confidential Computing Manager 来转换容器化映像。
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: JenCook
ms.openlocfilehash: 72c3f523ebc3cfef43709153c09c416d32e0b1b8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469593"
---
# <a name="run-an-application-by-using-fortanix-confidential-computing-manager"></a>使用 Fortanix Confidential Computing Manager 运行应用程序

了解如何使用 [Fortanix](https://www.fortanix.com/) 提供的 [Fortanix Confidential Computing Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.em_managed?tab=Overview) 和 [Node Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) 在 Azure 机密计算中运行应用程序。


Fortanix 是第三方软件供应商，提供适用于 Azure 基础结构的产品和服务。 还有其他第三方提供商也为 Azure 提供了类似的机密计算服务。

> [!Note]
> 本文档中引用的一些产品不由 Microsoft 提供。 Microsoft 只是为了方便而提供此信息。 对这些非 Microsoft 产品的引用并不表示 Microsoft 认可。

本教程介绍如何将应用程序映像转换为受机密计算保护的映像。 此环境使用 [Fortanix](https://www.fortanix.com/) 软件，该软件由 Azure DCsv2 系列支持 Intel SGX 的虚拟机提供保障。 该解决方案对身份验证和数据访问控制等关键的安全策略进行了协调。

如需 Fortanix 支持，请加入 [Fortanix Slack 社区](https://fortanix.com/community/)。 使用 `#enclavemanager` 频道。

## <a name="prerequisites"></a>先决条件

- 如果没有 Fortanix Confidential Computing Manager 帐户，请在开始之前[注册](https://ccm.fortanix.com/auth/sign-up)一个。
- 需要一个专用的 [Docker](https://docs.docker.com/) 注册表，用于推送转换后的应用程序映像。
- 如果没有 Azure 订阅，可以在开始之前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

> [!NOTE]
> 免费试用帐户无法访问本教程中使用的虚拟机。 若要完成本教程，需要一个即用即付订阅。

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>向 Fortanix Confidential Computing Manager 添加应用程序

1. 登录 [Fortanix Confidential Computing Manager (Fortanix EM)](https://ccm.fortanix.com)。
1. 转到“帐户”页面，然后选择“添加帐户”来创建一个新帐户 ：

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="该屏幕截图显示如何创建帐户。":::

1. 创建帐户后，单击“选择帐户”来选择新创建的帐户。 接下来，可以开始注册计算节点并创建应用程序。
1. 在“应用程序”选项卡上，选择“+ 应用程序”以添加应用程序 。 在此示例中，我们将添加运行 Python Flask 服务器的 Enclave OS 应用程序。

1. 为“Enclave OS 应用程序”选择“添加”按钮 ：

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="该屏幕截图显示如何添加应用程序。":::

   > [!NOTE]
   > 本教程只介绍如何添加 Enclave OS 应用程序。 有关添加 EDP Rust 应用程序的信息，请参阅[将 EDP Rust 应用引入到机密计算管理器](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager)。

1. 在本教程中，我们将对示例应用程序使用 Fortanix 的 Docker 注册表。 为以下设置输入指定值。 使用专用 Docker 注册表来存储输出映像。

    - **应用程序名称**：Python 应用程序服务器
    - **说明**：Python Flask 服务器
    - **输入映像名称**：fortanix/python-flask
    - **输出映像名称**：fortanix-private/python-flask-sgx（替换为自己的注册表。）
    - **ISVPRODID**：1
    - **ISVSVM**：1
    - **内存大小**：1 GB
    - **线程计数**：128

      可选：运行未转换的应用程序。
    - **Docker 中心**：[https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **应用**：fortanix/python-flask

      运行下面的命令：

      ```bash
         sudo docker run fortanix/python-flask
      ```
      > [!NOTE]
      > 建议不要使用专用 Docker 注册表来存储输出映像。

1. 添加证书。 输入下列值，再选择“下一步”：
    - **域**：myapp.domain.com
    - **类型**：Confidential Computing Manager 颁发的证书
    - **密钥路径**：/run/key.pem
    - **密钥类型**：RSA
    - **证书路径**：/run/cert.pem
    - **RSA 密钥大小**：2048 位

## <a name="create-an-image"></a>创建映像

Fortanix CCM 映像是应用程序的软件发行版或版本。 每个映像都与一个 enclave 哈希 (MRENCLAVE) 相关联。

1. 在“添加映像”页面上，为“输出映像名称”输入注册表凭据 。 这些凭据用于访问要推送映像的专用 Docker 注册表。 由于输入映像存储在公共注册表中，因此不需要为输入映像提供凭据。
1. 输入映像标记，然后选择“创建”：

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="该屏幕截图显示如何创建映像。":::


## <a name="domain-and-image-allowlist"></a>域和映像允许列表

如果某个应用程序的域已添加到允许列表，那么该应用程序将从 Fortanix Confidential Computing Manager 获得一个 TLS 证书。 当 Enclave OS 应用程序启动时，它将与Fortanix Confidential Computing Manager 联系以接收该 TLS 证书。

在屏幕左侧的“任务”选项卡上，批准待定的请求以允许域和映像。

## <a name="enroll-the-compute-node-agent-in-azure"></a>在 Azure 中注册计算节点代理

### <a name="create-and-copy-a-join-token"></a>创建和复制联接标记

将在 Fortanix Confidential Computing Manager 中创建一个令牌。 Azure 中的计算节点可使用该令牌对其自身进行身份验证。 Azure 虚拟机将需要此令牌。

1. 在“计算节点”选项卡上，选择“注册节点”。
1. 选择“复制”按钮以复制联接令牌。 计算节点使用此联接令牌对自身进行身份验证。

### <a name="enroll-nodes-into-fortanix-node-agent"></a>在 Fortanix Node Agent 内注册节点

创建 Fortanix Node Agent 时，会在 Azure 资源组中部署一个虚拟机、网络接口、虚拟网络、网络安全组和一个公共 IP 地址。 系统将就虚拟机向你的 Azure 订阅按小时计费。 在创建 Fortanix Node Agent 代理之前，请查看 DCsv2 系列的 Azure [虚拟机定价页面](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。 删除未使用的任何 Azure 资源。

1. 请转到 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/)并使用 Azure 凭据登录。
1. 在搜索栏中，输入“Fortanix Confidential Computing Node Agent”。 在搜索结果中，选择“Fortanix Confidential Computing Node Agent”以转到[应用的主页](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent?tab=OverviewFortanix)：

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png" alt-text="显示如何访问应用主页的屏幕截图。":::
1. 选择“立即获取”，根据需要填入你的信息，然后选择“继续” 。 你将会被重定向到 Azure 门户。
1. 选择“创建”，进入 Fortanix Confidential Computing Node Agent 部署页面。
1. 在本页上，要输入信息来部署虚拟机。 此虚拟机是一个 DCsv2 系列的支持 Intel SGX 的虚拟机，它由 Azure 提供且安装有 Fortanix Node Agent 软件。 通过该 Node Agent，转换后的映像可在 Azure 中的 Intel SGX 节点上更加安全地运行。 选择你想要部署虚拟机及其关联资源的订阅和资源组 。

   > [!NOTE]
   > 在 Azure 中部署 DCsv2 系列虚拟机时，约束适用。 可能需要为额外的核心请求配额。 有关详细信息，请阅读 [Azure VM 上的机密计算解决方案](./virtual-machine-solutions.md)。

1. 选择可用区域。
1. 在“节点名称”框中，输入虚拟机的名称。
1. 输入用户名和密码（或 SSH 密钥），向虚拟机进行身份验证。
1. 保留“200”的默认“OS 磁盘大小” 。 选择“VM 大小”。 （Standard_DC4s_v2 适用于本教程。)
1. 在“加入令牌”框中，粘贴之前在本教程中创建的令牌：

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="该屏幕截图显示如何部署资源。":::

1. 选择“查看 + 创建”。 确保验证通过，然后选择“创建”。 部署所有资源后，计算节点将注册到 Fortanix Confidential Computing Manager 中。

## <a name="run-the-application-image-on-the-compute-node"></a>在计算节点上运行应用程序映像

通过运行以下命令运行应用程序。 务必将节点 IP、端口和转换后的映像名称更改为应用程序的值。

在本教程中，要运行以下命令：

```bash
    sudo docker run \
        --device /dev/isgx:/dev/isgx \
        --device /dev/gsgx:/dev/gsgx \
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket \
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

在此命令中：

- `52.152.206.164` 是节点代理主机 IP。
- `9092` 是节点代理侦听的默认端口。
- `fortanix-private/python-flask-sgx` 是转换后的应用。 可在 Fortanix Confidential Computing Manager Web 门户中找到它。 它在“映像”选项卡上，“映像”表的“映像名称”列中。  。

## <a name="verify-and-monitor-the-running-application"></a>验证和监视正在运行的应用程序

1. 返回 [Fortanix Confidential Computing Manager](https://ccm.fortanix.com/console)。
1. 确保在注册了节点的“帐户”中操作。
1. 在“应用程序”选项卡上，验证是否存在正在运行且具有关联计算节点的应用程序。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组、虚拟机和相关资源时，可将它们删除。 删除资源组将取消注册与转换后的映像关联的节点。

选择虚拟机的资源组，然后选择“删除”。 确认资源组名称，以完成资源删除。

若要删除创建的 Fortanix Confidential Computing Manager 帐户，请转到 Fortanix Confidential Computing Manager 中的[帐户页面](https://ccm.fortanix.com/accounts)。 将鼠标悬停在要删除的帐户上。 选择右上角的黑色垂直圆点，然后选择“删除帐户”。

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="屏幕截图显示如何删除帐户。":::

## <a name="next-steps"></a>后续步骤

在本教程中，使用 Fortanix 工具转换了应用程序映像，使其在机密计算虚拟机上运行。 有关 Azure 上的机密计算虚拟机的详细信息，请参阅[有关虚拟机的解决方案](virtual-machine-solutions.md)。

若要详细了解 Azure 的机密计算产品/服务，请参阅 [Azure 机密计算概述](overview.md)。

也可了解如何在 Azure 上使用其他第三方产品/服务（例如 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 和 [Scone](https://sconedocs.github.io)）完成类似的任务。
