---
title: 操作说明 - 在 Azure Spring Cloud 中使用权限
description: 本文介绍如何在 Azure Spring Cloud 中为权限创建自定义角色。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498636"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>如何在 Azure Spring Cloud 中使用权限
本文介绍如何创建将权限委托给 Azure Spring Cloud 资源的自定义角色。 自定义角色通过各种默认权限对 [Azure 内置角色](../role-based-access-control/built-in-roles.md)进行扩展。

我们将实现以下自定义角色：

* **开发人员角色**： 
    * 部署
    * 测试
    * 重启应用
    * 可以应用并对 Git 存储库中的应用配置进行更改
    * 可以获取日志流
* **Ops - 站点可靠性工程**： 
    * 重启应用
    * 获取日志流
    * 无法对应用或配置进行更改
* **Azure Pipelines/Jenkins/GitHub Actions 角色**：
    * 可以执行创建、读取、更新、删除操作
    * 可以在 Azure Spring Cloud 和应用的服务实例中创建和配置所有内容：使用 Terraform 或 ARM 模板的 Azure Pipelines、Jenkins 或 GitHub Actions

## <a name="define-developer-role"></a>定义开发人员角色

开发人员角色包括重启应用和查看其日志流的权限，但不能对应用、配置进行更改。

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>导航订阅和资源组访问控制 (IAM)

遵循以下步骤开始定义角色。

1. 在 Azure 门户中，打开你希望可在其中分配自定义角色的订阅或资源组。
2. 打开“访问控制(IAM)”。
3. 单击“+ 添加”。
4. 单击“添加自定义角色”。
5. 单击“下一步”  。

   ![创建自定义角色](media/spring-cloud-permissions/create-custom-role.png)

6. 单击“添加权限”。

   ![添加权限启动](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>搜索 Azure Spring Cloud 权限：
7. 在搜索框中，搜索“Microsoft.app”。
选择 *Microsoft Azure 春季云*"。

   ![选择 Azure Spring Cloud](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. 选择开发人员角色的权限：

从“Microsoft.AppPlatform/Spring”中，选择：
* 写入：创建或更新 Azure Spring Cloud 服务实例
* 读取：获取 Azure Spring Cloud 服务实例
* 其他：列出 Azure Spring Cloud 服务实例测试密钥

从“Microsoft.AppPlatform/Spring/apps”中，选择：
* 读取：读取 Microsoft Azure 春季云应用程序
* 其他：获取 Microsoft Azure 春季 Cloud 应用程序资源上传 URL

从“Microsoft.AppPlatform/Spring/apps/bindings”中，选择：
* 读取：读取 Microsoft Azure 春季云应用程序绑定

从“Microsoft.AppPlatform/Spring/apps/deployments”中，选择：
* 编写：编写 Microsoft Azure 春季云应用程序部署
* 阅读：阅读 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：开始 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：停止 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：重启 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：获取 Microsoft Azure 春季 Cloud 应用程序部署日志文件 URL

从“Microsoft.AppPlatform/Spring/apps/domains”中，选择：
* 读取：读取 Microsoft Azure 春季 Cloud 应用程序自定义域

从“Microsoft.AppPlatform/Spring/certificates”中，选择：
* 读取：读取 Microsoft Azure 春季云证书

从“Microsoft.AppPlatform/locations/operationResults/Spring”中，选择：
* 读取：读取操作结果

从“Microsoft.AppPlatform/locations/operationStatus/operationId”中，选择：
* 读取：读取操作状态

    [ ![创建开发人员权限](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. 单击 **添加**。

10. 检查这些权限。

11. 单击“审阅并创建”。

## <a name="define-devops-engineer-role"></a>定义 DevOps 工程师角色
此过程定义有权部署、测试和重启 Azure Spring Cloud 应用的角色。

1. 重复此过程以导航订阅、资源组并使用访问控制功能 (IAM)。
2. 选择 DevOps 工程师角色的权限：

从“Microsoft.AppPlatform/Spring”中，选择：
* 写入：创建或更新 Azure Spring Cloud 服务实例
* 删除：删除 Azure Spring Cloud 服务实例
* 读取：获取 Azure Spring Cloud 服务实例
* 其他：启用 Azure Spring Cloud 服务实例测试终结点
* 其他：禁用 Azure Spring Cloud 服务实例测试终结点
* 其他：列出 Azure Spring Cloud 服务实例测试密钥
* 其他：再生成 Azure Spring Cloud 服务实例测试密钥

从“Microsoft.AppPlatform/Spring/apps”中，选择：
* 写入：写入 Microsoft Azure 春季云应用程序
* 删除：删除 Microsoft Azure 春季云应用程序
* 读取：读取 Microsoft Azure 春季云应用程序
* 其他：获取 Microsoft Azure 春季 Cloud 应用程序资源上传 URL
* 其他：验证 Microsoft Azure 春季 Cloud 应用程序自定义域

从“Microsoft.AppPlatform/Spring/apps/bindings”中，选择：
* 写入：写入 Microsoft Azure 春季云应用程序绑定
* 删除：删除 Microsoft Azure 弹簧云应用程序绑定
* 读取：读取 Microsoft Azure 春季云应用程序绑定

从“Microsoft.AppPlatform/Spring/apps/deployments”中，选择：
* 编写：编写 Microsoft Azure 春季云应用程序部署
* 删除：删除 Azure Spring Cloud 应用程序部署
* 阅读：阅读 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：开始 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：停止 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：重启 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：获取 Microsoft Azure 春季 Cloud 应用程序部署日志文件 URL

从“Microsoft.AppPlatform/Spring/apps/deployments/skus”中，选择：
* 读取：列出应用程序部署可用的 SKU

从“Microsoft.AppPlatform/locations”中，选择：
* 其他：检查名称可用性

从“Microsoft.AppPlatform/locations/operationResults/Spring”中，选择：读取：读取操作结果

从“Microsoft.AppPlatform/locations/operationStatus/operationId”中，选择：
* 读取：读取操作状态

从“Microsoft.AppPlatform/skus”中，选择：
* 读取：列出可用的 SKU

   [ ![Dev/Op 权限](media/spring-cloud-permissions/dev-ops-permissions.png) ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. 单击 **添加**。

4. 检查这些权限。

5. 单击“审阅并创建”。

## <a name="define-ops---site-reliability-engineering-role"></a>定义 Ops - 站点可靠性工程角色
此过程定义有权部署、测试和重启 Azure Spring Cloud 应用的角色。

1. 重复此过程以导航订阅、资源组并使用访问控制功能 (IAM)。

2. 选择 Ops - 站点可靠性工程角色的权限：

从“Microsoft.AppPlatform/Spring”中，选择：
* 读取：获取 Azure Spring Cloud 服务实例
* 其他：列出 Azure Spring Cloud 服务实例测试密钥

从“Microsoft.AppPlatform/Spring/apps”中，选择：
* 读取：读取 Microsoft Azure 春季云应用程序

从“Microsoft.AppPlatform/apps/deployments”中，选择：
* 阅读：阅读 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：开始 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：停止 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：重启 Microsoft Azure 春季 Cloud 应用程序部署

从“Microsoft.AppPlatform/locations/operationResults/Spring”中，选择：
* 读取：读取操作结果

从“Microsoft.AppPlatform/locations/operationStatus/operationId”中，选择：
* 读取：读取操作状态

   [ ![Ops/SRE 权限](media/spring-cloud-permissions/ops-sre-permissions.png) ](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. 单击 **添加**。

4. 检查这些权限。

5. 单击“审阅并创建”。

## <a name="define-azure-pipelinesprovisioning-role"></a>定义 Azure Pipelines/预配角色
此 Jenkins/Github Actions 角色可以在 Azure Spring Cloud 和应用中使用服务实例创建和配置所有内容。 此角色用于发布或部署代码。

1. 重复此过程以导航订阅、资源组和访问访问控制 (IAM)。

2. 打开“权限”选项。

3. 选择 Azure Pipelines/预配角色的权限：
  
从“Microsoft.AppPlatform/Spring”中，选择：
* 写入：创建或更新 Azure Spring Cloud 服务实例
* 删除：删除 Azure Spring Cloud 服务实例
* 读取：获取 Azure Spring Cloud 服务实例
* 其他：启用 Azure Spring Cloud 服务实例测试终结点
* 其他：禁用 Azure Spring Cloud 服务实例测试终结点
* 其他：列出 Azure Spring Cloud 服务实例测试密钥
* 其他：再生成 Azure Spring Cloud 服务实例测试密钥

从“Microsoft.AppPlatform/Spring/apps”中，选择：
* 写入：写入 Microsoft Azure 春季云应用程序
* 删除：删除 Microsoft Azure 春季云应用程序
* 读取：读取 Microsoft Azure 春季云应用程序
* 其他：获取 Microsoft Azure 春季 Cloud 应用程序资源上传 URL
* 其他：验证 Microsoft Azure 春季 Cloud 应用程序自定义域

从“Microsoft.AppPlatform/Spring/apps/bindings”中，选择：
* 写入：写入 Microsoft Azure 春季云应用程序绑定
* 删除：删除 Microsoft Azure 弹簧云应用程序绑定
* 读取：读取 Microsoft Azure 春季云应用程序绑定

从“Microsoft.AppPlatform/Spring/apps/deployments”中，选择：
* 编写：编写 Microsoft Azure 春季云应用程序部署
* 删除：删除 Azure Spring Cloud 应用程序部署
* 阅读：阅读 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：开始 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：停止 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：重启 Microsoft Azure 春季 Cloud 应用程序部署
* 其他：获取 Microsoft Azure 春季 Cloud 应用程序部署日志文件 URL

从“Microsoft.AppPlatform/skus”中，选择：
* 读取：列出可用的 SKU

从“Microsoft.AppPlatform/locations”中，选择：
* 其他：检查名称可用性

从“Microsoft.AppPlatform/locations/operationResults/Spring”中，选择：
* 读取：读取操作结果

从“Microsoft.AppPlatform/locations/operationStatus/operationId”中，选择：
* 读取：读取操作状态

从“Microsoft.AppPlatform/skus”中，选择：
* 读取：列出可用的 SKU

   [ ![ 权限](media/spring-cloud-permissions/pipelines-permissions-box.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. 单击 **添加**。

5. 检查这些权限。

6. 单击“审阅并创建”。


## <a name="see-also"></a>请参阅
* [使用 Azure 门户创建或更新 Azure 自定义角色](../role-based-access-control/custom-roles-portal.md)

有关定义自定义权限的三种方法的详细信息，请参阅：
* [克隆角色](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [从头开始](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [从 JSON 开始](../role-based-access-control/custom-roles-portal.md#start-from-json)