---
title: 有关通过 Azure 门户在数据中心准备部署 Azure Stack Edge Pro FPGA 的教程
description: 本文是介绍如何部署 Azure Stack Edge Pro FPGA 的第一篇教程，涉及到如何在 Azure 门户中做好准备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: alkohli
ms.custom: subject-rbac-steps
ms.openlocfilehash: eecdead171e68915430aefe9aebeb24833485789
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688972"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-fpga"></a>教程：准备部署 Azure Stack Edge Pro FPGA  

本文是完整部署 Azure Stack Edge Pro FPGA 时必读的部署教程系列的第一篇教程。 本教程介绍如何准备 Azure 门户，以便部署 Azure Stack Edge 资源。 

需要有管理员权限才能完成安装和配置过程。 门户准备只需不到 10 分钟的时间。  

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 创建新资源
> * 获取激活密钥

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="get-started"></a>入门

若要部署 Azure Stack Edge Pro FPGA，请按指定顺序参阅以下教程。

| **#** | **此步骤的内容** | **使用这些文档** |
| --- | --- | --- | 
| 1. |[在 Azure 门户中做好部署 Azure Stack Edge Pro FPGA 的准备](azure-stack-edge-deploy-prep.md) |在安装 Azure Stack Edge 物理设备之前创建并配置 Azure Stack Edge 资源。 |
| 2. |[安装 Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-install.md)|将 Azure Stack Edge Pro FPGA 物理设备拆包、装入机架并布线。  |
| 3. |[连接、设置并激活 Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-connect-setup-activate.md) |连接到本地 Web UI，完成设备设置，然后激活设备。 现已准备好在设备设置 SMB 或 NFS 共享。  |
| 4. |[使用 Azure Stack Edge Pro FPGA 传输数据](azure-stack-edge-deploy-add-shares.md) |添加共享，并通过 SMB 或 NFS 连接到共享。 |
| 5. |[使用 Azure Stack Edge Pro FPGA 转换数据](azure-stack-edge-deploy-configure-compute.md) |在设备上配置计算模块，以便在将数据移到 Azure 时转换数据。 |

现在可以开始设置 Azure 门户。

## <a name="prerequisites"></a>先决条件

下面是 Azure Stack Edge 资源、Azure Stack Edge Pro FPGA 设备和数据中心网络的配置先决条件。

### <a name="for-the-azure-stack-edge-resource"></a>对于 Azure Stack Edge 资源

在开始之前，请确保：

* 已为 Azure Stack Edge 资源启用 Microsoft Azure 订阅。 确保使用了受支持的订阅，例如 [Microsoft 企业协议 (EA)](https://azure.microsoft.com/overview/sales-number/)、[云解决方案提供商 (CSP)](/partner-center/azure-plan-lp) 或 [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)。 不支持即用即付订阅。

* RBAC 角色：在 Azure 基于角色的访问控制 (RBAC) 中具有以下角色分配：

  * 若要创建 Azure Stack Edge、IoT 中心和 Azure 存储资源，用户必须在资源组范围具有“参与者”或“所有者”角色。

  * 若要在资源组范围将“参与者”角色分配给用户，必须在订阅范围具有“所有者”角色。

  有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

* 资源提供程序：已注册以下资源提供程序： 

  * 若要创建 Azure Stack Edge/Data Box Gateway 资源，请确保已注册 `Microsoft.DataBoxEdge` 提供程序。

  * 若要创建 IoT 中心资源，请确保已注册 `Microsoft.Devices` 提供程序。

  * 若要创建 Azure 存储资源，请确保已注册 Azure 存储。 默认情况下，Azure 存储资源提供程序 (SRP) 是已注册的资源提供程序，但某些情况下可能需要注册。

  若要注册资源提供程序，必须具有上述相关 RBAC 角色。

  有关如何注册的信息，请参阅[注册资源提供程序](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。

* 你拥有对 Azure Active Directory 图形 API 的管理员或用户访问权限。 有关详细信息，请参阅 [Azure Active Directory 图形 API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。

* 具有 Microsoft Azure 存储帐户和访问凭据。

* 你未被系统管理员设置的任何 Azure 策略阻止。 有关策略的详细信息，请参阅[快速入门：创建策略分配以识别不合规资源](../governance/policy/assign-policy-portal.md)。


### <a name="for-the-azure-stack-edge-pro-fpga-device"></a>对于 Azure Stack Edge Pro FPGA 设备

在部署物理设备之前，请确保：

* 你已经查看配送包中包含的安全信息。
* 在数据中心的标准 19 英寸机架中提供一个 1U 插槽用于安装设备的机架。
* 将设备安全放置在平坦、稳定的水平工作台面上。
* 用于安装设备的位置具有来自独立源的标准交流电，或具有带不间断电源 (UPS) 的机架电源分配单元 (PDU)。
* 有权访问物理设备。

### <a name="for-the-datacenter-network"></a>对于数据中心网络

在开始之前，请确保：

* 按你的 Azure Stack Edge Pro FPGA 设备的网络要求配置数据中心内的网络。 有关详细信息，请参阅 [Azure Stack Edge Pro FPGA 系统要求](azure-stack-edge-system-requirements.md)。

* 若要使 Azure Stack Edge Pro FPGA 正常运行，请做好以下准备：

  * 使用至少 10 Mbps 的下载带宽以确保设备保持更新。
  * 使用至少 20 Mbps 的专用上传和下载带宽传输文件。

## <a name="create-new-resource-for-existing-device"></a>为现有设备创建新资源

如果你是现有的 Azure Stack Edge Pro FPGA 客户并需要更换或重置现有的设备，请使用以下过程创建新资源。

如果你是新客户，我们建议先了解如何为工作负载使用 Azure Stack Edge Pro - GPU 设备。 有关详细信息，请参阅[什么是 Azure Stack Edge Pro with GPU](azure-stack-edge-gpu-overview.md)。 有关订购 Azure Stack Edge Pro with GPU 设备的信息，请参阅[为 Azure Stack Edge Pro - GPU 创建新资源](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)。

若要为现有设备创建新的 Azure Stack Edge 资源，请在 Azure 门户中执行以下步骤。

1. 使用 Microsoft Azure 凭据通过

    - URL [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。
    - 或者，通过 URL [https://portal.azure.us](https://portal.azure.us) 登录到 Azure 政府版门户。 有关更多详细信息，请参阅[使用门户连接到 Azure 政府版](../azure-government/documentation-government-get-started-connect-with-portal.md)。

1. 选择“+ 创建资源”。  搜索并选择“Azure Stack Edge”。 然后选择“创建”。

1. 选择 Azure Stack Edge Pro FPGA 设备的订阅，并在“寄送地址”中选择要将该设备寄送到的国家/地区。

   ![选择设备的订阅以及寄送到的国家/地区](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. 在显示的设备类型列表中，选择“Azure Stack Edge Pro - FPGA”。 然后选取“选择”  。 

   仅当存在现有的设备时，才会显示“Azure Stack Edge Pro - FPGA”设备类型。 如果需要订购新设备，请参阅[为 Azure Stack Edge Pro - GPU 创建新资源](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)。

   ![搜索 Azure Stack Edge 服务](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. 在“基本信息”选项卡上：

   1. 输入或选择以下项目详细信息。
    
       |设置  |值  |
       |---------|---------|
       |订阅    |系统会根据前面所做的选择自动填充此值。 订阅将链接到你的计费帐户。 |
       |资源组  |选择现有的组，或创建新组。<br>详细了解 [Azure 资源组](../azure-resource-manager/management/overview.md)。     |

   1. 输入或选择以下“实例详细信息”。

       |设置  |值  |
       |---------|---------|
       |名称   | 用于标识资源的友好名称。<br>名称可具有 2 到 50 个字符，包括字母、数字和连字符。<br> 名称以字母或数字开头和结尾。        |
       |区域     |有关可使用 Azure Stack Edge 资源的所有区域的列表，请参阅[可用的 Azure 产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 如果使用 Azure 政府版，则可选择 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)中显示的所有可用的政府区域。<br> 选择离要部署设备的地理区域最近的位置。|

   1. 选择“查看 + 创建”。

    ![项目和实例详细信息](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. 在“查看 + 创建”选项卡上，查看“使用条款”、“定价详细信息”和资源的详细信息。   然后选择“创建”。

    ![查看 Azure Stack Edge 资源详细信息和隐私条款](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. 创建资源需要几分钟时间。 成功创建并部署资源后，你会收到通知。 选择“转到资源”。

   ![转到 Azure Stack Edge 资源](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

在你下单以后，Microsoft 会审核该订单并通过电子邮件联系你，核对配送详细信息。

![关于审核 Azure Stack Edge Pro FPGA 订单的通知](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>获取激活密钥

在 Azure Stack Edge 资源启动并运行后，你需要获取激活密钥。 此密钥用于激活 Azure Stack Edge Pro FPGA 设备并将其连接到资源。 如果你仍在 Azure 门户中，则现在可以获取此密钥。

1. 转到创建的资源，然后选择“概述”。 你将看到一个通知，指示你的订单正在处理中。

    ![选择“概述”](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. 订单处理完毕并且设备就绪后，“概述”将更新。 接受默认的 Azure Key Vault 名称或输入新名称。 选择“生成激活密钥”。 选择复制图标复制密钥并将其保存供日后使用。

    ![获取激活密钥](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * 生成的激活密钥将在三天后过期。
> * 如果密钥已过期，请生成新密钥。 旧密钥不再有效。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解了以下 Azure Stack Edge Pro FPGA 主题：

> [!div class="checklist"]
>
> * 创建新资源
> * 获取激活密钥

请继续学习下一教程，了解如何安装 Azure Stack Edge Pro FPGA。

> [!div class="nextstepaction"]
> [安装 Azure Stack Edge Pro FPGA](./azure-stack-edge-deploy-install.md)