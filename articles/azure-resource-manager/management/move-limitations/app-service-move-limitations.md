---
title: 移动 Azure 应用服务资源
description: 使用 Azure 资源管理器将应用服务资源移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 34f86cfd3a3822b77992f5d1ae77afaf14fdc7ab
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251449"
---
# <a name="move-guidance-for-app-service-resources"></a>针对应用服务资源的移动指南

本文介绍移动应用服务资源的步骤。 将应用服务资源移到新订阅有特定要求。

## <a name="move-across-subscriptions"></a>跨订阅移动

在订阅之间移动 Web 应用时，应遵循以下指导：

- 将资源移动到新的资源组或订阅属于元数据更改，不应影响资源的任何运作方式。 例如，移动应用服务时，应用服务的入站 IP 地址不会更改。
- 目标资源组中不能有任何现有的应用服务资源。 应用服务资源包括：
    - Web 应用
    - 应用服务计划
    - 上传或导入的 TLS/SSL 证书
    - 应用服务环境
- 资源组中的所有应用服务资源必须一起移动。
- 应用服务环境不能移到新资源组，也不能移到新订阅。 但是，可以将某个 Web 应用和应用服务计划转移到新订阅，而无需移动应用服务环境。 移动后，Web 应用不再在应用服务环境中托管。
- 只要将证书与资源组中的所有其他资源一起移动，就可以将绑定的证书移到 Web，而无需删除 TLS 绑定。 但是，无法移动免费的应用服务托管证书。 对于该情况，请参阅[移动免费托管证书](#move-with-free-managed-certificates)。
- 只能从最初创建应用服务资源的资源组中移动它们。 如果应用服务资源不再位于其原始资源组中，请将其移回其原始资源组。 然后，在订阅之间移动资源。 如需有关查找原始资源组的帮助，请参阅下一部分。

## <a name="find-original-resource-group"></a>查找原始资源组

如果忘记了原始资源组，可以通过诊断来查找。 对于 Web 应用，请选择“诊断和解决问题”。 然后，选择“配置和管理”。

![选择诊断](./media/app-service-move-limitations/select-diagnostics.png)

选择“迁移选项”。

![选择迁移选项](./media/app-service-move-limitations/select-migration.png)

选择通过建议的步骤来移动 Web 应用的选项。

![选择建议的步骤](./media/app-service-move-limitations/recommended-steps.png)

可以看到在移动资源之前需采取的建议操作。 该信息包含 Web 应用的原始资源组。

![屏幕捕获显示了针对移动 Microsoft.Web 资源的建议步骤。](./media/app-service-move-limitations/recommendations.png)

## <a name="move-hidden-resource-types-in-portal"></a>在门户中移动隐藏的资源类型

使用门户移动应用服务资源时，可能会出现一个错误，指示尚未移动所有资源。 如果看到此错误，请检查是否存在门户未显示的资源类型。 选择“显示隐藏的类型”。 然后，选择要移动的所有资源。

:::image type="content" source="./media/app-service-move-limitations/show-hidden-types.png" alt-text="显示隐藏的类型":::

## <a name="move-with-free-managed-certificates"></a>移动免费托管证书

无法移动免费的应用服务托管证书。 请删除托管证书，然后在移动 Web 应用后重新创建它。 若要获取有关如何删除证书的说明，请使用迁移操作工具。

如果在非预期的资源组中创建了免费的应用服务托管证书，请尝试将应用服务计划移回其原始资源组。 然后，重新创建免费的托管证书。 此问题将得到解决。

## <a name="move-support"></a>移动支持

若要确定可以移动哪些应用服务资源，请查看以下项的移动支持状态：

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>后续步骤

有关用于移动资源的命令，请参阅[将资源移到新资源组或订阅](../move-resource-group-and-subscription.md)。
