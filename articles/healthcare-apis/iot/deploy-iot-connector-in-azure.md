---
title: 在门户中部署 IoT Azure 门户 - Azure 医疗保健 API
description: 本文将了解如何在应用程序部署中部署 IoT Azure 门户。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/13/2021
ms.author: jasteppe
ms.openlocfilehash: 064c904b33317d72adbef771353a98a947438252
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003205"
---
# <a name="deploy-iot-connector-in-the-azure-portal"></a>在 Azure 门户 中部署 IoT 连接器

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本快速入门将了解如何在 Azure 门户 中部署 IoT 连接器。 配置 IoT 连接器后，可以使用设备消息的 Azure 事件中心将数据从 物联网 (IoT) 快速医疗保健互操作性资源 (FHIR&#174;) 服务中。

## <a name="prerequisites"></a>先决条件

在开始在 Azure 医疗保健 API 中创建 IoT 连接器实例的步骤之前，必须完成以下先决条件。

* [Azure 帐户](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)
* [部署在资源组Azure 门户](../../azure-resource-manager/management/manage-resource-groups-portal.md)
* [部署在事件中心命名空间和事件中心Azure 门户](../../event-hubs/event-hubs-create.md)
* [Azure 医疗保健 API 中部署的工作区](../workspace-overview.md)  
* [Azure 医疗保健 API 中部署的 FHIR 服务](../fhir/fhir-portal-quickstart.md) 


## <a name="deploy-iot-connector"></a>部署 IoT 连接器 

1. 登录到 [Azure 门户，](https://portal.azure.com)然后在"搜索栏"字段中输入医疗保健 API **工作区资源** 名称。
 
   ![在搜索栏字段中输入工作区资源名称的屏幕截图。](media/select-workspace-resource-group.png#lightbox)

2. 选择 **"IoT 连接器"边栏** 选项卡。

   ![IoT 连接器边栏选项卡的屏幕截图。](media/iot-connector-blade.png#lightbox)

3. 接下来，选择 **"添加 IoT 连接器"。**

   ![添加 IoT 连接器的屏幕截图。](media/add-iot-connector.png#lightbox)

## <a name="configure-iot-connector-to-ingest-data"></a>配置 IoT 连接器以将数据

在" **基本信息"** 选项卡下，完成"实例详细信息 **"下的必填字段**。

![IoT 配置实例详细信息的屏幕截图。](media/basics-instance-details.png#lightbox)

1. 输入 **IoT 连接器名称**。

   **IoT 连接器名称** 是 IoT 连接器的友好名称。 为用户输入唯一IoT Connector。 例如，可以将它命名 `healthdemo-iot` 。

2. 输入 **事件中心名称**。

   事件中心名称是已部署 **的事件中心实例** 的名称。 

   有关事件Azure 事件中心，请参阅 [快速入门：使用](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)Azure 门户。

3. 输入使用者 **组**。

   通过使用搜索栏转到已部署的事件中心实例，并选择"使用者组"边栏选项卡，可找到使用者 **组** 名称。

   ![使用者组名称的屏幕截图。](media/consumer-group-name.png#lightbox)

   有关使用者组的信息，请参阅 中的[功能和Azure 事件中心。](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#event-consumers)

4. 输入完全限定命名空间 **的名称**。

    完全 **限定命名空间** 是 **位于** 事件中心命名空间的"概述"页上的 **主机** 名。

    ![完全限定命名空间的屏幕截图。](media/event-hub-hostname.png#lightbox)  

    有关事件中心命名空间的详细信息，请参阅事件中心文档中的功能[](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace)和术语中的命名空间Azure 事件中心命名空间。

5. 选择 **"下一步：设备映射"。** 
  
## <a name="configure-device-mapping-properties"></a>配置设备映射属性

> [!TIP]
> IoMT 连接器数据映射器是一种开源工具，用于可视化映射配置以规范化设备的输入数据，然后将它转换为 FHIR 资源。 开发人员可以使用此工具编辑和测试设备和 FHIR 目标映射，并导出要上传到 ioT 连接器Azure 门户。 此工具还有助于开发人员了解其设备的设备和 FHIR 目标映射配置。
>
> 有关详细信息，请参阅开源文档：
>
> [IoMT 连接器数据映射器](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [设备内容映射](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)

1. 在" **设备映射"** 选项卡下，输入与 IoT 连接器关联的设备映射 JSON 代码。

   ![配置设备映射的屏幕截图。](media/configure-device-mapping.png#lightbox)

2. 选择 **"下一>** 目标"，配置与 IoT 连接器关联的目标属性。

## <a name="configure-fhir-destination-mapping-properties"></a>配置 FHIR 目标映射属性

在" **目标** "选项卡下，输入与 IoT 连接器关联的目标属性。

   !["配置目标属性"的屏幕截图。](media/configure-destination-properties.png#lightbox)

1. 输入 FHIR 服务的 Azure **资源 ID。**

   **FHIR 服务器** (**也称为 FHIR** 服务) ，其位置是使用搜索栏转到已部署的 FHIR服务，然后选择"属性"**边栏选项卡**。 将资源 ID **字符串复制** 并粘贴到 **FHIR 服务器** 文本字段。

    ![输入 FHIR 服务器名称的屏幕截图。](media/fhir-service-resource-id.png#lightbox) 

2. 输入目标 **名称**。

   " **目标名称** "是目标友好名称。 输入目标的唯一名称。 例如，可以将它命名 `iotmedicdevice` 。

3. 选择 **"创建****"或"查找**"作为 **"解析类型"。**

    > [!NOTE]
    > 若要使 IoT 连接器目标在 FHIR 服务中创建有效的观察资源，FHIR 服务器中必须存在设备资源和患者资源，以便观察结果可以正确引用创建数据的设备以及测量数据所基于的患者。 IoT 连接器可以使用两种模式来解析设备和患者资源。

   **创建**

     IoT 连接器目标尝试使用事件中心消息中包含的设备标识符从 FHIR 服务器检索设备资源。 它还尝试使用事件中心消息中包含的患者标识符从 FHIR 服务器检索患者资源。 如果找不到任一资源， (患者或两者) 仅包含事件中心消息中包含的标识符的新资源。 使用"创建 **"** 选项时，可以在设备映射中配置设备标识符和患者标识符。 换句话说，当目标IoT Connector"创建"模式时，它可以正常运行，而无需向 FHIR 服务器添加设备和患者资源。

   **查找**

     IoT 连接器目标尝试使用事件中心消息中包含的设备标识符从 FHIR 服务器检索设备资源。 如果找不到设备资源，则会导致错误，并且不会处理数据。 若要 **使 Lookup** 正常工作，必须存在标识符与事件中心消息中包含的设备标识符匹配的设备资源，并且设备资源 **必须** 引用同样存在的患者资源。 换句话说，当 IoT 连接器目标为查找模式时，必须先将设备和患者资源添加到FHIR 服务器，然后才能处理数据。

   有关详细信息，请参阅开放源代码文档 [FHIR 目标映射](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping)。

4. 在 **"目标映射**"下，在代码编辑器中输入 JSON 代码。

   有关映射器工具的信息，请参阅 [IoMT 连接器数据映射器工具](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)。

5. 可以选择" **查看 + 创建"，** 或者，如果要配置标记 **，>** 选择"下一步： 标记"。  

## <a name="optional-configure-tags"></a> (可选) 配置标记

标记是用于对资源进行分类的名称和值对。 有关标记详细信息，请参阅使用 [标记来组织 Azure 资源和管理层次结构](../../azure-resource-manager/management/tag-resources.md)。

在" **标记** "选项卡下，输入与 IoT 连接器关联的标记属性。

   ![标记属性的屏幕截图。](media/tag-properties.png#lightbox)
 
1. 输入“名称”。
2. 输入值。
3. 选择“查看 + 创建”。

   应看到" **验证成功"** 消息，如下图所示。 

   ![验证成功消息的屏幕截图。](media/iot-connector-validation-success.png#lightbox) 

   > [!NOTE]
   > 如果 IoT 连接器未验证，请查看验证失败消息并排查问题。 建议查看已配置的每个 IoT 连接器选项卡下的属性。

4. 接下来，选择“创建”。

   新部署的 IoT 连接器会显示在 Azure 资源组中。

   !["Azure 最近使用的资源"列表中列出的"已部署 IoT 连接器"的屏幕截图。](media/azure-resources-iot-connector-deployed.png#lightbox)  

    部署 IoT 连接器后，我们将演练分配访问事件中心和 FHIR 服务的权限的步骤。 

## <a name="granting-iot-connector-access"></a>授予 IoT 连接器访问权限

若要确保 IoT 连接器正常工作，它必须已授予对事件中心和 FHIR 服务的访问权限。 

### <a name="accessing-the-iot-connector-from-the-event-hub"></a>从事件中心访问 IoT 连接器

1. 在 **"Azure 资源组"** 列表中，选择事件中心 **命名空间 的名称**。

2. 选择 **"IAM ("** 边) "访问控制"，然后选择 **"+ 添加"。**   

   ![事件中心命名空间的访问控制的屏幕截图。](media/access-control-blade-add.png#lightbox)

3. 选择“添加角色分配”。

   ![添加角色分配的屏幕截图。](media/event-hub-add-role-assignment.png#lightbox)
 
4. 选择 " **角色**"，然后选择 " **Azure 事件中心数据接收方**"。

   ![添加角色分配必填字段的屏幕截图。](media/event-hub-add-role-assignment-fields.png#lightbox)

   Azure 事件中心数据接收方角色允许向其分配此角色的 IoT 连接器从此事件中心接收数据。

   有关应用程序角色的详细信息，请参阅 [& Authentication for The 保健 api (preview) 的授权 ](.././authentication-authorization.md)。

5. 选择 "分配对的 **访问权限**"，并保留默认选项 "选择 **用户、组或服务主体**"。

6. 在 " **选择** " 字段中，输入 IoT 连接器的安全主体。  

   `<your workspace name>/iotconnectors/<your IoT connector name>`
 
   部署 IoT 连接器时，它将创建一个托管标识。 托管识别名称是工作区名称、资源类型 (的连接，这是 IoT 连接器) 和 IoT 连接器的名称。

7. 选择“保存”。

   成功将角色分配添加到事件中心后，通知会显示带有文本 "添加角色分配" 的绿色复选标记。  此消息表示 IoT 连接器现在可以从事件中心读取。

   ![添加的角色分配消息的屏幕截图。](media/event-hub-added-role-assignment.png#lightbox)

有关创作对事件中心资源的访问权限的详细信息，请参阅[使用 Azure Active Directory 授予访问权限](../../event-hubs/authorize-access-azure-active-directory.md)。  

### <a name="accessing-the-iot-connector-from-the-fhir-service"></a>从 FHIR 服务访问 IoT 连接器

1. 在 " **Azure 资源组" 列表** 中，选择你的  **FHIR 服务** 的名称。
 
2. 选择 " **访问控制 (IAM)** 边栏选项卡，然后选择" **+ 添加**"。 

3. 选择“添加角色分配”。

  ![FHIR 服务的添加角色分配的屏幕截图。](media/fhir-service-add-role-assignment.png#lightbox)

4. 选择该 **角色**，然后选择 " **FHIR 数据编写器**"。

   FHIR 数据写入者角色提供 IoT 连接器用于运行的读写访问权限。 由于 IoT 连接器部署为单独的资源，因此 FHIR 服务将从 IoT 连接器接收请求。 如果 FHIR 服务不知道发出请求的人员，或者如果没有分配的角色，则会拒绝请求，因为该服务未获授权。

   有关应用程序角色的详细信息，请参阅 [& Authentication for The 保健 api (preview) 的授权 ](.././authentication-authorization.md)。

5. 在 " **选择** " 字段中，输入 IoT 连接器的安全主体。  

    `<your workspace name>/iotconnectors/<your IoT connector name>`

6. 选择“保存”。

   ![FHIR 服务添加的角色分配消息的屏幕截图。](media/fhir-service-added-role-assignment.png#lightbox)

   有关向 FHIR 服务分配角色的详细信息，请参阅 [为 FHIR 服务配置 AZURE RBAC](../fhir/configure-azure-rbac-for-fhir.md)。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure 门户中部署 IoT 连接器。 有关 IoT 连接器的概述，请参阅

>[!div class="nextstepaction"]
>[IoT 连接器概述](iot-connector-overview.md)

 (FHIR&#174;) 是 [HL7](https://hl7.org/fhir/) 的注册商标，并与 HL7 的权限一起使用。