---
title: 使用 Azure 门户配置文件上传 | Microsoft Docs
description: 如何使用 Azure 门户配置 IoT 中心，以便从连接的设备上传文件。 包括有关配置目标 Azure 存储帐户的信息。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: robinsh
ms.openlocfilehash: bf8bf9d1b472ff1986596f6cae7e1f7b415d3bc6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729839"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>使用 Azure 门户配置 IoT 中心文件上传

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

本文介绍如何使用 Azure 门户在 IoT 中心配置文件上传。 

若要使用 [IoT 中心的文件上传功能](iot-hub-devguide-file-upload.md)，必须先将 Azure 存储帐户和 Blob 容器与 IoT 中心关联。 IoT 中心会自动生成对此 Blob 容器具有写入权限的 SAS URI，以供设备上传文件时使用。 除了存储帐户和 blob 容器之外，还可以设置 SAS URI 的生存时间以及 IoT 中心对 Azure 存储使用的身份验证类型。 还可以配置可由 IoT 中心传送给后端服务的可选文件上传通知的设置。

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* Azure IoT 中心。 如果没有 IoT 中心，请参阅[使用门户创建 IoT 中心](iot-hub-create-through-portal.md)。

## <a name="configure-your-iot-hub"></a>配置 IoT 中心

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心，并选择“文件上传”以显示文件上传属性。 然后，在“存储容器设置”下，选择“Azure 存储容器”。 

    :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-settings.png" alt-text="在门户中查看 IoT 中心文件上传设置":::

1. 在当前订阅中选择 Azure 存储帐户和 Blob 容器，以便与 IoT 中心关联。 如有必要，可以在“存储帐户”窗格上创建 Azure 存储帐户，并在“容器”窗格上创建 blob 容器。 

   :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-container-selection.png" alt-text="在门户中查看用于文件上传的存储容器":::

1. 选择 Azure 存储帐户和 blob 容器后，配置其余文件上传属性。    

    :::image type="content" source="./media/iot-hub-configure-file-upload/file-upload-selected-container.png" alt-text="在门户中配置 IoT 中心文件上传":::

    * **接收已上传文件的通知**：通过切换来启用或禁用文件上传通知。

    * **SAS TTL**：此设置是 IoT 中心返回给设备的 SAS URI 生存时间。 默认设置为一小时，但可以使用滑块自定义为其他值。

    * 文件通知设置默认 TTL：文件上传通知到期前的生存时间。 默认设置为一天，但可以使用滑块自定义为其他值。

    * **文件通知最大传送数**：IoT 中心将尝试传送文件上传通知的次数。 默认设置为 10，但可以使用滑块自定义为其他值。

    * “身份验证类型”：默认情况下，Azure IoT 中心使用基于密钥的身份验证来与 Azure 存储进行连接和授权。 还可以配置用户分配的或系统分配的托管标识，以对 Azure IoT 中心 向 Azure 存储进行身份验证。 托管标识在 Azure AD 中以安全的方式为 Azure 服务提供自动托管标识。 若要了解如何配置托管标识，请参阅 [IoT 中心对托管标识的支持](./iot-hub-managed-identity.md)。 在 Azure 存储帐户和 IoT 中心配置一个或多个托管标识后，可以选择一个托管标识，使用“系统分配”或“用户分配”的按钮向 Azure 存储进行身份验证。 

        > [!NOTE]
        > 身份验证类型设置配置 IoT 中心如何向 Azure 存储帐户进行身份验证。 设备始终使用从 IoT 中心获取的 SAS URI 向 Azure 存储进行身份验证。 

1. 选择“保存”以保存设置。 请务必检查确认是否成功完成。 某些选择（如“身份验证类型”）仅在保存设置后进行验证。 

## <a name="next-steps"></a>后续步骤

* [从设备上传文件概述](iot-hub-devguide-file-upload.md)
* [IoT 中心对托管标识的支持](./iot-hub-managed-identity.md)
* [文件上传操作指南](./iot-hub-csharp-csharp-file-upload.md)
