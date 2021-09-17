---
title: 教程：准备 Azure 门户、数据中心环境以便部署 Azure Stack Edge Pro GPU | Microsoft Docs
description: 本文是介绍如何部署 Azure Stack Edge Pro GPU 的第一篇教程，涉及如何准备 Azure 门户。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/06/2021
ms.author: alkohli
ms.openlocfilehash: b223d428daf1a7080478f4f80b6a997fb97cd7a0
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322531"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>教程：准备部署 Azure Stack Edge Pro with GPU 

本文是完整部署 Azure Stack Edge Pro with GPU 时必读的部署教程系列的第一篇教程。 本教程介绍如何准备 Azure 门户，以便部署 Azure Stack Edge 资源。

需要有管理员权限才能完成安装和配置过程。 门户准备只需不到 10 分钟的时间。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建新资源
> * 获取激活密钥

### <a name="get-started"></a>入门

若要进行 Azure Stack Edge Pro 部署，需要先准备环境。 环境准备就绪后，请执行必需步骤，如有必要，请执行可选步骤和过程来完全部署设备。 逐步部署说明将指示何时应执行每个必需步骤以及可选步骤。

| 步骤 | 说明 |
| --- | --- |
| **准备工作** |在为即将进行的部署执行准备工作时必须完成这些步骤。 |
| **[部署配置清单](#deployment-configuration-checklist)** |在部署之前或在部署期间使用此清单来收集和记录信息。 |
| **[部署先决条件](#prerequisites)** |这些先决条件会验证环境是否已做好部署准备。 |
|  | |
|**部署教程** |需要完成这些教程，才能在生产环境中部署 Azure Stack Edge Pro 设备。 |
|**[1.在 Azure 门户中做好部署 Azure Stack Edge Pro 的准备](azure-stack-edge-gpu-deploy-prep.md)** |在安装 Azure Stack Edge 物理设备之前创建并配置 Azure Stack Edge 资源。 |
|**[2.安装 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md)**|将 Azure Stack Edge Pro 物理设备拆包、装入机架并布线。  |
|**[3.连接到 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md)** |安装设备后，连接到设备本地 Web UI。  |
|**[4.配置 Azure Stack Edge Pro 的网络设置](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |配置网络，包括设备的计算网络和 Web 代理设置。   |
|**[5.配置 Azure Stack Edge Pro 的设备设置](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |分配设备名称和 DNS 域，配置更新服务器和设备时间。 |
|**[6.配置 Azure Stack Edge Pro 的安全设置](azure-stack-edge-gpu-deploy-configure-certificates.md)** |为设备配置证书。 可使用设备生成的证书，或者使用自己的证书。   |
|**[7.激活 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md)** |使用从服务中获取的激活密钥来激活设备。 现已准备好在设备设置 SMB 或 NFS 共享或通过 REST 进行连接。 |
|**[8.配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)** |配置设备上的计算角色。 还会创建 Kubernetes 群集。 |
|**[9A.使用 Edge 共享传输数据](./azure-stack-edge-gpu-deploy-add-shares.md)** |添加共享，并通过 SMB 或 NFS 连接到共享。 |
|**[9B.使用 Edge 存储帐户传输数据](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)** |添加存储帐户，并通过 REST API 连接到 blob 存储。 |


现在可以开始收集 Azure Stack Edge Pro 设备软件配置的相关信息。

## <a name="deployment-configuration-checklist"></a>部署配置清单

在部署设备之前，需要收集信息来配置 Azure Stack Edge Pro 设备上的软件。 提前准备其中的一些信息有助于简化在环境中部署设备的过程。 使用 [Azure Stack Edge Pro 部署配置清单](azure-stack-edge-gpu-deploy-checklist.md)，在部署设备时记下配置详细信息。


## <a name="prerequisites"></a>先决条件

下面是 Azure Stack Edge 资源、Azure Stack Edge Pro 设备和数据中心网络的配置先决条件。

### <a name="for-the-azure-stack-edge-resource"></a>对于 Azure Stack Edge 资源

<!--Why isn't the include file used, as for the Pro R and Mini R SKUs? Check for differences. Also, the presentation of requirements is organized a bit differently; standard presentation would be more usable, even if the GPU requirements are different.-->

在开始之前，请确保：

- 已为 Azure Stack Edge 资源启用 Microsoft Azure 订阅。 确保使用了受支持的订阅，例如 [Microsoft 企业协议 (EA)](https://azure.microsoft.com/overview/sales-number/)、[云解决方案提供商 (CSP)](/partner-center/azure-plan-lp) 或 [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)。 不支持即用即付订阅。 若要确定你的 Azure 订阅的类型，请参阅[什么是 Azure 产品/服务？](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer)。
- 你在资源组级别拥有对 Azure Stack Edge Pro、IoT 中心和 Azure 存储资源的所有者或参与者访问权限。

    - 若要创建任何 Azure Stack Edge 资源，你应该在资源组级别范围内具有参与者（或更高级别）权限。 
    - 你还需要确保已注册 `Microsoft.DataBoxEdge` 和 `MicrosoftKeyVault` 资源提供程序。 若要创建任何 IoT 中心资源，应注册 `Microsoft.Devices` 提供程序。 
        - 若要注册资源提供程序，请在 Azure 门户中转到“主页”>“订阅”> 你的订阅 >“资源提供程序”。 
        - 搜索特定资源提供程序（如 `Microsoft.DataBoxEdge`）并将其注册。 
    - 若要创建存储帐户资源，你同样需要资源组级别范围内的参与者或更高级别访问权限。 Azure 存储在默认情况下是已注册的资源提供程序。
- 若要在 Azure Edge Hardware Center 创建订单，需确保注册 `Microsoft.EdgeOrder` 提供程序。 有关如何注册的信息，请转到[注册资源提供程序](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers)。
- 你需要对 Azure Active Directory Graph API 具有管理员或用户访问权限，以便生成激活密钥或凭据操作，例如使用存储帐户创建共享。 有关详细信息，请参阅 [Azure Active Directory 图形 API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。

### <a name="for-the-azure-stack-edge-pro-device"></a>对于 Azure Stack Edge Pro 设备

在部署物理设备之前，请确保：

- 你已经查看配送包中包含的安全信息。
- 在数据中心的标准 19 英寸机架中提供一个 1U 插槽用于安装设备的机架。
- 将设备安全放置在平坦、稳定的水平工作台面上。
- 用于安装设备的位置具有来自独立源的标准交流电，或具有带不间断电源 (UPS) 的机架电源分配单元 (PDU)。
- 有权访问物理设备。


### <a name="for-the-datacenter-network"></a>对于数据中心网络

在开始之前，请确保：

- 按你的 Azure Stack Edge Pro 设备的网络要求配置数据中心内的网络。 有关详细信息，请参阅 [Azure Stack Edge Pro 系统要求](azure-stack-edge-system-requirements.md)。

- 若要使 Azure Stack Edge Pro 正常运行，请做好以下准备：

    - 使用至少 10 Mbps 的下载带宽以确保设备更新。
    - 使用至少 20 Mbps 的专用上传和下载带宽传输文件。

## <a name="create-a-new-resource"></a>创建新资源

如果现有的 Azure Stack Edge 资源可以管理物理设备，请跳过此步骤，转到[获取激活密钥](#get-the-activation-key)。

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center（预览版）](#tab/azure-edge-hardware-center)

Azure Edge Hardware Center（预览版）是一种新服务，可让你从 Azure 混合产品组合（包括 Azure Stack Edge Pro 设备）浏览和订购各种硬件。

通过 Azure Edge Hardware Center 下订单时，可以订购多个设备以寄送到多个地址，并且可重复使用来自其他订单的地址。

通过 Azure Edge Hardware Center 进行订购会创建一个 Azure 资源，其中包含所有与订单相关的信息。 会为每个订购的单元创建一个资源。 收到设备之后必须创建 Azure Stack Edge 资源才能激活和管理设备。

[!INCLUDE [Create order in Azure Edge Hardware Center](../../includes/azure-edge-hardware-center-new-order.md)]

#### <a name="create-a-management-resource-for-each-device"></a>为每个设备创建管理资源

[!INCLUDE [Create management resource](../../includes/azure-edge-hardware-center-create-management-resource.md)]

### <a name="portal-classic"></a>[门户（经典）](#tab/azure-portal)

若要通过 Azure Stack Edge 服务创建 Azure Stack Edge 资源，请在 Azure 门户中执行以下步骤。

1. 使用 Microsoft Azure 凭据通过以下 URL 登录到 Azure 门户：[https://portal.azure.com](https://portal.azure.com)。

2. 在“Azure 服务”中，搜索并选择“Azure Stack Edge” 。 然后选择“+ 创建”。 

3. 在“管理 Azure Stack Edge 设备”中，选择“试用 Azure Hardware Center”链接 。

    ![通过“+ 创建”按钮打开的“管理 Azure Stack Edge 设备”屏幕的屏幕截图。 突出显示了“试用 Azure Edge Hardware Center”链接。](media/azure-stack-edge-gpu-deploy-prep/classic-order-experience-1.png)

4. 如果不想通过 Hardware Center 进行订购，请在“开始使用”屏幕上选择“使用经典订购体验进行订购” 。

   ![Azure Stack Edge 中“开始使用”屏幕的屏幕截图。 突出显示了“使用经典订购体验进行订购”链接。](media/azure-stack-edge-gpu-deploy-prep/classic-order-experience-2.png)

5. 选取要用于 Azure Stack Edge Pro GPU 设备的订阅。 选择要将物理设备寄送到的国家或地区。 然后选择“显示设备”。

    ![“选择设备类型”屏幕的屏幕截图，用于选择订阅，然后寄送到 Azure Stack Edge 资源的相应区域。 突出显示“显示设备”按钮。](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

6. 选择设备类型。 在“Azure Stack Edge Pro”下，选择“Azure Stack Edge Pro with GPU”，然后选择“选择”  。 如果发现任何问题或无法选择设备类型，请转到[排查订单问题](azure-stack-edge-troubleshoot-ordering.md)。

    ![“选择设备类型”屏幕的屏幕截图，用于选择 Azure Stack Edge资源的设备类型。 突出显示设备类型的“选择”按钮。](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

7. 根据业务需要，可以从 Nvidia 选择带有 1 个或 2 个图形处理单元 (GPU) 的 Azure Stack Edge Pro。 

    ![为 Azure Stack Edge 资源选择 Azure Stack Edge Pro GPU 设备的配置的屏幕截图。 突出显示硬件配置和“选择”按钮。](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

8. 在“基本信息”选项卡上，输入或选择以下“项目详细信息”。  
    
    |设置  |值  |
    |---------|---------|
    |订阅    |系统会根据前面所做的选择自动填充此订阅。 订阅将链接到你的计费帐户。 |
    |资源组  |选择现有的组，或创建新组。<br>详细了解 [Azure 资源组](../azure-resource-manager/management/overview.md)。     |

9. 输入或选择以下“实例详细信息”。

    |设置  |值  |
    |---------|---------|
    |名称   | 用于标识资源的友好名称。<br>该名称的长度必须介于 2 和 50 个字符之间，只能包含字母、数字和连字符。<br> 名称以字母或数字开头和结尾。        |
    |区域     |有关可使用 Azure Stack Edge 资源的所有区域的列表，请参阅[可用的 Azure 产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 如果使用 Azure 政府版，则可选择 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)中显示的所有可用的政府区域。<br> 选择离要部署设备的地理区域最近的位置。|

    ![Azure Stack Edge 的创建资源并订购设备向导的“基本信息”选项卡屏幕截图。 突出显示“基本信息”选项卡和“下一步: 送货地址”按钮。](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)

10. 在完成时选择“下一步:送货地址”。

    - 如果已经有一台设备，请选择与“我已有设备”对应的组合框。

        ![“送货地址”选项卡的屏幕截图，其中在“为 Azure Stack Edge 创建资源向导”中选择了“我已有设备的送货地址”选项。](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - 如果这是你订购的新设备，请输入联系人姓名、公司、要将该设备寄送到的地址，以及联系人信息。

        ![创建新 Azure Stack Edge 资源时创建资源向导中的“送货地址”选项卡屏幕截图。](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

11. 在完成时选择“下一步:  标记”。 （可选）提供标记，以便对资源进行分类并合并账单。 在完成时选择“下一步:查看 + 创建”。

12. 在“查看 + 创建”选项卡上，查看“定价详细信息”、“使用条款”和资源的详细信息。 选择与“我已经查看隐私条款”对应的组合框。

    ![Azure Stack Edge 订单的“查看 + 创建”选项卡屏幕截图。](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png) 

    你还会收到通知，了解到在资源创建期间启用了托管标识，可通过它向云服务进行身份验证。 只要资源存在，就会存在此标识。

13. 选择“创建”。

    创建资源需要几分钟时间。 还会创建一个托管标识，Azure Stack Edge 设备可通过它与 Azure 中的资源提供程序通信。

    成功创建并部署资源后，你会收到通知。 选择“转到资源”。

    ![显示屏幕截图，指示新 Azure Stack Edge 资源的部署已完成。 突出显示“转到资源”按钮。](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

在你下单以后，Microsoft 会审核该订单并通过电子邮件联系你，核对配送详细信息。

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

> [!NOTE]
> 如果要一次创建多个订单或克隆现有订单，可以使用 [Azure 示例中的脚本](https://github.com/Azure-Samples/azure-stack-edge-order)。 有关详细信息，请参阅自述文件。

若在处理订单的过程中遇到任何问题，请参阅[排查订单问题](azure-stack-edge-troubleshoot-ordering.md)。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如有必要，请为 Azure CLI 准备环境。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

若要创建 Azure Stack Edge 资源，请在 Azure CLI 中运行以下命令。

1. 通过使用 [az group create](/cli/azure/group#az_group_create) 命令来创建资源组，或者使用某个现有的资源组：

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. 若要创建设备，请使用 [az databoxedge device create](/cli/azure/databoxedge/device#az_databoxedge_device_create) 命令：

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeP_Base
   ```

   选择离要部署设备的地理区域最近的位置。 该区域只存储用于设备管理的元数据。 实际数据可以存储在任何存储帐户中。

   有关可使用 Azure Stack Edge 资源的所有区域的列表，请参阅[可用的 Azure 产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)。 如果使用 Azure 政府版，则可选择 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)中显示的所有可用的政府区域。

1. 若要创建订单，请运行 [az databoxedge order create](/cli/azure/databoxedge/order#az_databoxedge_order_create) 命令：

   ```azurecli 
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

创建资源需要几分钟时间。 运行 [az databoxedge order show](/cli/azure/databoxedge/order#az_databoxedge_order_show) 命令以查看该订单：

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

在你下单后，Microsoft 会审核该订单并通过电子邮件联系你，核对配送详细信息。

---

## <a name="get-the-activation-key"></a>获取激活密钥

在 Azure Stack Edge 资源启动并运行后，你需要获取激活密钥。 此密钥用于激活 Azure Stack Edge Pro 设备并将其连接到资源。 如果你仍在 Azure 门户中，则现在可以获取此密钥。

1. 选择创建的资源，然后选择“概述”。

2. 在右侧窗格上，为 Azure Key Vault 输入一个名称，或者接受默认名称。 密钥保管库名称的长度可介于 3 至 24 个字符之间。

   对于随设备一起激活的每个 Azure Stack Edge 资源，都会创建一个密钥保管库。 通过密钥保管库，可存储和访问机密，例如密钥保管库中存储的服务的通道完整性密钥 (CIK)。 

   指定密钥保管库名称后，请选择“生成密钥”来创建一个激活密钥。 

   ![新创建的 Azure Stack Edge 资源的“概述”窗格屏幕截图。 突出显示“生成激活密钥”按钮。](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

   创建密钥保管库和激活密钥需要几分钟时间，请稍候。 选择复制图标复制密钥并将其保存供日后使用。<!--Verify that the new screen has a copy icon.-->


> [!IMPORTANT]
> - 生成的激活密钥将在三天后过期。
> - 如果密钥已过期，请生成新密钥。 旧密钥不再有效。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解了以下 Azure Stack Edge Pro 主题：

> [!div class="checklist"]
> * 创建新资源
> * 获取激活密钥

请继续学习下一教程，了解如何安装 Azure Stack Edge Pro。

> [!div class="nextstepaction"]
> [安装 Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-install.md)
