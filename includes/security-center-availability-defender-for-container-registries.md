---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 05/19/2021
ms.topic: include
ms.openlocfilehash: 103a23a478a3629eb913c3d1672b3665bc9cba9c
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559615"
---
## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|适用于容器注册表的 Azure Defender 按[定价页](../articles/security-center/security-center-pricing.md)中的定价计费|
|支持的注册表和映像：|可通过 shell 从公共 internet 访问的 ACR 注册表中的 Linux 映像|
|不支持的注册表和映像：|Windows 映像<br>“专用”注册表<br>超级简单的映像，例如 [Docker 暂存](https://hub.docker.com/_/scratch/)映像或仅包含应用程序及其运行时依赖项而无包管理器、shell 或 OS 的“无分发版”映像<br>包含[开放容器计划 (OCI) 映像格式规范](https://github.com/opencontainers/image-spec/blob/master/spec.md)的映像|
|所需角色和权限：|安全读取者和 [Azure 容器注册表角色和权限](../articles/container-registry/container-registry-roles.md)|
|云：|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: 商用云<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov 和 China Gov - 当前仅支持“推送扫描”功能。 参阅[何时扫描映像？](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)了解详细信息|
|||
