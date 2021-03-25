---
title: 如何 - 使用 Fortanix Confidential Computing Manager 运行应用程序
description: 了解如何使用 Fortanix Confidential Computing Manager 来转换容器化映像
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: 5bc1ff4a288e492613f02a8b095c5ef80436d18e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558740"
---
# <a name="how-to-run-an-application-with-fortanix-confidential-computing-manager"></a>如何：使用 Fortanix Confidential Computing Manager 运行应用程序

使用 [Fortanix](https://www.fortanix.com/) 提供的 [Fortanix Confidential Computing Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) 和 [Fortanix Node Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) 开始在 Azure 机密计算中运行你的应用程序。


Fortanix 是第三方软件供应商，它提供了基于 Azure 基础结构构建的产品和服务。 还有其他的第三方提供商也在 Azure 提供了类似的机密计算服务。

> [!Note]
 > 本文档中提及的产品不受 Microsoft 的控制。 Microsoft 向你提供此信息仅便于你参考，对这些非 Microsoft 产品的提及并不表示 Microsoft 予以认可。



本教程介绍如何将应用程序映像转换为受机密计算保护的映像。 此环境使用 [Fortanix](https://www.fortanix.com/) 软件，它由 Azure DCsv2 系列支持 Intel SGX 的虚拟机提供保障。 该解决方案对标识验证和数据访问控制等关键的安全策略进行了协调。

 如需 Fortanix 特定的支持，请加入 [Fortanix Slack 社区](https://fortanix.com/community/)并使用频道 #enclavemanager。


## <a name="prerequisites"></a>先决条件

1. 如果没有 Fortanix Confidential Computing Manager 帐户，请在开始之前[注册](https://em.fortanix.com/auth/sign-up)一个。
1. 一个专用的 [Docker](https://docs.docker.com/) 注册表，用于推送转换后的应用程序映像。
1. 如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

> [!NOTE]
> 免费试用帐户无法访问本教程中使用的虚拟机。 请升级为即用即付订阅。

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>向 Fortanix Confidential Computing Manager 添加应用程序
1. 登录 [Fortanix Confidential Computing Manager (Fortanix EM)](https://em.fortanix.com)
1. 导航到“帐户”(Accounts) 页面，然后选择“ADD 帐户”(ADD ACCOUNT) 来创建一个新帐户 。

![创建帐户](media/how-to-fortanix-enclave-manager/create-account.png)

1. 创建帐户后，点击“选择”(SELECT) 来选择新创建的帐户。 现在，我们可开始注册计算节点和创建应用程序了。
1. 选择“+应用程序”(+ APPLICATION) 按钮来添加一个应用程序。 在本例中，我们将添加一个 Flask Server Enclave OS 应用程序。

1. 为 Enclave OS 应用程序选择“添加”(ADD) 按钮。

    ![添加应用程序](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > 本教程只介绍如何添加 Enclave OS 应用程序。 [详细阅读](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager)如何将 EDP Rust 应用程序引入 Fortanix Confidential Computing Manager。

6. 在本教程中，我们将对示例应用程序使用 Fortanix 的 docker 注册表。 填入下列项中的详细信息。 使用专用 docker 注册表来保留输出映像。

    - **应用程序名称**：Python 应用程序服务器
    - **说明**：Python Flask 服务器
    - **输入映像名称**：fortanix/python-flask
    - **输出映像名称**：fortanx-private/python-flask-sgx
    - **ISVPRODID**：1
    - **ISVSVM**：1
    - **内存大小**：1 GB
    - **线程计数**：128

    可选：运行应用程序。
    - **Docker 中心**：[https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **应用**：fortanix/python-flask

        运行下面的命令：
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. 添加证书。 填入下述详细信息，然后选择“下一步”(NEXT)：
    - **域**：myapp.domain.dom
    - **类型**：Confidential Computing Manager 颁发的证书
    - **密钥路径**：/appkey.pem
    - **密钥类型**：RSA
    - **证书路径**：/appcert.pem
    - **RSA 密钥大小**：2048 位


## <a name="create-an-image"></a>创建映像
Fortanix CCM 映像是应用程序的软件发行版或版本。 每个映像都与一个 enclave 哈希 (MRENCLAVE) 相关联。
1. 在“添加映像”(Add Image) 页面上，为“输出映像名称”(Output image name) 输入注册表凭据  。 这些凭据用于访问要推送映像的专用 docker 注册表。

    ![创建映像](media/how-to-fortanix-enclave-manager/create-image.png)
1. 提供映像标记，然后选择“创建”(Create)。

    ![添加标记](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>域和映像允许列表
如果某个应用程序的域已添加到允许列表，那么该应用将从 Fortanix Confidential Computing Manager 获得一个 TLS 证书。 同样地，当应用程序从转换后的映像运行时，它将尝试连接 Fortanix Confidential Computing Manager。 然后，该应用程序会请求 TLS 证书。

切换到左侧的“任务”(Tasks) 选项卡，然后批准待定的请求以允许域和映像。

## <a name="enroll-compute-node-agent-in-azure"></a>在 Azure 中注册计算节点代理

### <a name="generate-and-copy-join-token"></a>生成和复制联接令牌
在 Fortanix Confidential Computing Manager 中，你将创建一个令牌。 Azure 中的计算节点可使用该令牌对其自身进行身份验证。 你将需要向你的 Azure 虚拟机提供该令牌。
1. 在管理控制台中，选择“+注册节点”(+ ENROLL NODE) 按钮。
1. 选择“生成令牌”(GENERATE TOKEN) 可生成联接令牌。 复制令牌。

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>在 Azure 市场中的 Fortanix Node Agent 内注册节点

创建 Fortanix Node Agent 时，会在你的 Azure 资源组中部署一个虚拟机、网络接口、虚拟网络、网络安全组和一个公共 IP 地址。 系统将就虚拟机向你的 Azure 订阅按小时计费。 在创建 Fortanix Node Agent 代理之前，请查看 DCsv2 系列的[虚拟机定价页面](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。 删除未在使用时的 Azure 资源。

1. 请转到 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/)并使用 Azure 凭据登录。
1. 在搜索栏中，键入“Fortanix Confidential Computing Node Agent”。 选择在名为“Fortanix Confidential Computing Node Agent”的搜索框中显示的应用，导航到产品的主页。
     ![在市场中搜索](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. 选择“立即获取”、根据需要填入你的信息，然后选择“继续” 。 你将会被重定向到 Azure 门户。
1. 选择“创建”，进入 Fortanix Confidential Computing Node Agent 部署页面。
1. 在本页上，你要输入信息来部署虚拟机。 具体的说，此虚拟机 (VM) 是一个 DCsv2 系列的支持 Intel SGX 的虚拟机，它由 Azure 提供且安装有 Fortanix Node Agent 软件。 通过该 Node Agent，转换后的映像可在 Azure 中的 Intel SGX 节点上安全地运行。  选择你想要部署虚拟机及其关联资源的订阅和资源组 。

    > [!NOTE]
    > 在 Azure 中部署 DCsv2 系列的虚拟机时存在一些限制。 你可能需要为额外的核心请求配额。 有关详细信息，请阅读 [Azure VM 上的机密计算解决方案](./virtual-machine-solutions.md)。

1. 选择可用区域。
1. 在“节点名称”字段中，输入虚拟机的名称。
1. 输入用户名和密码（或 SSH 密钥）来对虚拟机进行身份验证。
1. 将默认 OS 磁盘大小保留为 200，并选择一个 VM 大小（Standard_DC4s_v2 可满足本教程的需求）
1. 在“联接令牌”字段中粘贴之前生成的令牌。

     ![部署资源](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent1.png)

1. 选择“查看 + 创建”  。 确保验证通过，然后选择“创建”。 一旦部署所有资源，计算节点现在就已在 Fortanix Confidential Computing Manager 中注册。

## <a name="run-the-application-image-on-the-compute-node"></a>在计算节点上运行应用程序映像
执行以下命令来运行应用程序。 请务必更改节点 IP、端口和转换后的映像名称，将其作为特定应用程序的输入。

在本教程中，要执行以下命令：     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

其中：
- 52.152.206.164 是 Node Agent 主机 IP
- 9092 是 Node Agent 侦听的端口
- fortanix-private/python-flask-sgx 是转换后的应用。若要找到它，可访问 Fortanix Confidential Computing Manager Web 门户，在“映像”表中的“映像名称”列下单击“映像”选项卡 。

## <a name="verify-and-monitor-the-running-application"></a>验证和监视正在运行的应用程序
1. 返回到 [Fortanix Confidential Computing Manager](https://em.fortanix.com/console)
1. 确保你在注册了节点的帐户中操作
1. 选择左侧导航窗格中的顶部图标，导航到“管理控制台”(Management Console)。
1. 选择“应用程序”(Application) 选项卡
1. 验证是否存在正在运行且具有关联计算节点的应用程序


## <a name="clean-up-resources"></a>清理资源

不再需要资源组、虚拟机和相关资源时，可将它们删除。 删除资源组将取消注册与转换后的映像关联的节点。

选择虚拟机的资源组，然后选择“删除”  。 确认资源组名称，以完成资源删除。

若要删除你创建的 Fortanix Confidential Computing Manager 帐户，请转到 Fortanix Confidential Computing Manager 中的[帐户页面](https://em.fortanix.com/accounts)。 将鼠标悬停在想要删除的帐户上。 选择右上角的黑色垂直圆点，然后选择“删除帐户”(Delete Account)。

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 Fortanix 工具转换了应用程序映像，使其在机密计算虚拟机上运行。 有关 Azure 上的机密计算虚拟机的详细信息，请参阅[有关虚拟机的解决方案](virtual-machine-solutions.md)。

若要详细了解 Azure 的机密计算产品/服务，请查看 [Azure机密计算概述](overview.md)

 了解如何在 Azure 上使用其他第三方产品/服务（例如 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 和 [Scone](https://sconedocs.github.io)）完成类似的任务。
