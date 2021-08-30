---
title: 在 Azure Spring Cloud 中使用权限
description: 本文介绍如何创建将权限委托给 Azure Spring Cloud 资源的自定义角色。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 61c4eb0322de60bb90d49bd71b111fe8b2db07ea
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015546"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>如何在 Azure Spring Cloud 中使用权限
本文介绍如何创建将权限委托给 Azure Spring Cloud 资源的自定义角色。 自定义角色通过各种默认权限对 [Azure 内置角色](../role-based-access-control/built-in-roles.md)进行扩展。

我们将实现以下自定义角色。

* **开发人员角色**：

    * 部署
    * 测试
    * 重启应用
    * 应用 Git 存储库中的应用配置以及对其进行更改
    * 获取日志流

* DevOps 工程师角色：

    * 在 Azure Spring Cloud 云中创建、读取、更新和删除任何内容
* 运营 - 现场可靠性工程角色：

    * 重启应用
    * 获取日志流
    * 无法对应用或配置进行更改

* Azure Pipelines/Jenkins/GitHub Actions 角色：

    * 执行创建、读取、更新和删除操作
    * 使用 Terraform 或 ARM 模板在 Azure Spring Cloud 和应用的服务实例中创建和配置任何内容：Azure Pipelines、Jenkins 和 GitHub Actions

## <a name="define-the-developer-role"></a>定义开发人员角色

开发人员角色包括重启应用和查看其日志流的权限。 此角色无法对应用或配置进行更改。

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. 在 Azure 门户中，打开你要在其中分配自定义角色的订阅。
2. 打开“访问控制(IAM)”。
3. 选择 **添加**。
4. 选择“添加自定义角色”。
5. 选择“下一步”：

   ![该屏幕截图显示“创建自定义角色”窗口的“基本信息”选项卡。](media/spring-cloud-permissions/create-custom-role.png)

6. 选择“添加权限”：

   ![该屏幕截图显示“添加权限”按钮。](media/spring-cloud-permissions/add-permissions.png)


7. 在搜索框中，搜索“Microsoft.app”。 选择“Microsoft Azure Spring Cloud”：

   ![该屏幕截图显示 Microsoft.app 搜索结果。](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. 选择开发人员角色的权限。

   在“Microsoft.AppPlatform/Spring”下，选择：
   * **写入：创建或更新 Azure Spring Cloud 服务实例**
   * **读取：获取 Azure Spring Cloud 服务实例**
   * **其他：列出 Azure Spring Cloud 服务实例测试密钥**

   在“Microsoft.AppPlatform/Spring/apps”下，选择：
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序**
   * **其他：获取 Microsoft Azure Spring Cloud 应用程序资源上传 URL**

   在“Microsoft.AppPlatform/Spring/apps/bindings”下，选择：
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序绑定**

   在“Microsoft.AppPlatform/Spring/apps/deployments”下，选择：
   * **写入：写入 Microsoft Azure Spring Cloud 应用程序部署**
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：启动 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：停止 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：重启 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：获取 Microsoft Azure Spring Cloud 应用程序部署日志文件 URL**

   在“Microsoft.AppPlatform/Spring/apps/domains”下，选择：
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序自定义域**

   在“Microsoft.AppPlatform/Spring/certificates”下，选择：
   * **读取：读取 Microsoft Azure Spring Cloud 证书**

   在“Microsoft.AppPlatform/locations/operationResults/Spring”下，选择：
   * **读取：读取操作结果**

   在“Microsoft.AppPlatform/locations/operationStatus/operationId”下，选择：
   * **读取：读取操作状态**

    [ ![该屏幕截图显示开发人员权限的选项。](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. 选择 **添加**。

10. 检查这些权限。

11. 选择“查看并创建”。 

#### <a name="json"></a>[JSON](#tab/JSON)
1. 在 Azure 门户中，打开你要在其中分配自定义角色的订阅。
2. 打开“访问控制(IAM)”。
3. 选择 **添加**。
4. 选择“添加自定义角色”。
5. 选择“下一步”。

6. 选择“JSON”选项卡。

7. 选择“编辑”，然后删除默认文本：

   ![该屏幕截图显示默认 JSON 文本。](media/spring-cloud-permissions/create-custom-role-edit-json.png)

8. 粘贴以下 JSON 以定义开发人员角色：

   ```json
   {
     "properties": {
       "roleName": "Developer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/domains/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/certificates/read",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

   ![该屏幕截图显示“开发人员角色”的 JSON。](media/spring-cloud-permissions/create-custom-role-json.png)

9. 选择“保存”。

10. 检查这些权限。

11. 选择“查看并创建”。 

---

## <a name="define-the-devops-engineer-role"></a>定义 DevOps 工程师角色

此过程定义有权部署、测试和重启 Azure Spring Cloud 应用的角色。

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. 重复用于添加开发人员角色的过程的步骤 1 到 4。

2. 选择 DevOps 工程师角色的权限：

   在“Microsoft.AppPlatform/Spring”下，选择：
   * **写入：创建或更新 Azure Spring Cloud 服务实例**
   * **删除：删除 Azure Spring Cloud 服务实例**
   * **读取：获取 Azure Spring Cloud 服务实例**
   * **其他：启用 Azure Spring Cloud 服务实例测试终结点**
   * **其他：禁用 Azure Spring Cloud 服务实例测试终结点**
   * **其他：列出 Azure Spring Cloud 服务实例测试密钥**
   * **其他：再生成 Azure Spring Cloud 服务实例测试密钥**

   在“Microsoft.AppPlatform/Spring/apps”下，选择：
   * **写入：写入 Microsoft Azure Spring Cloud 应用程序**
   * **删除：删除 Microsoft Azure Spring Cloud 应用程序**
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序**
   * **其他：获取 Microsoft Azure Spring Cloud 应用程序资源上传 URL**
   * **其他：验证 Microsoft Azure Spring Cloud 应用程序自定义域**

   在“Microsoft.AppPlatform/Spring/apps/bindings”下，选择：
   * **写入：写入 Microsoft Azure Spring Cloud 应用程序绑定**
   * **删除：删除 Microsoft Azure Spring Cloud 应用程序绑定**
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序绑定**

   在“Microsoft.AppPlatform/Spring/apps/deployments”下，选择：
   * **写入：写入 Microsoft Azure Spring Cloud 应用程序部署**
   * **删除：删除 Azure Spring Cloud 应用程序部署**
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：启动 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：停止 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：重启 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：获取 Microsoft Azure Spring Cloud 应用程序部署日志文件 URL**

   在“Microsoft.AppPlatform/Spring/apps/deployments/skus”下，选择：
   * **读取：列出应用程序部署可用的 SKU**

   在“Microsoft.AppPlatform/locations”下，选择：
   * **其他：检查名称可用性**

   在“Microsoft.AppPlatform/locations/operationResults/Spring”下，选择：
   * **读取：读取操作结果**

   在“Microsoft.AppPlatform/locations/operationStatus/operationId”下，选择：
   * **读取：读取操作状态**

   在“Microsoft.AppPlatform/skus”下，选择：
   * **读取：列出可用的 SKU**

   [ ![该屏幕截图显示 DevOps 权限的选项。](media/spring-cloud-permissions/dev-ops-permissions.png) ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. 选择 **添加**。

4. 检查这些权限。

5. 选择“查看并创建”。 

#### <a name="json"></a>[JSON](#tab/JSON)

1. 重复用于添加开发人员角色的过程的步骤 1 到 4。
2. 选择“下一步”。

3. 选择“JSON”选项卡。

4. 选择“编辑”，然后删除默认文本：

   ![该屏幕截图显示默认 JSON 文本。](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. 粘贴以下 JSON 以定义 DevOps 工程师角色：

   ```json
   {
     "properties": {
       "roleName": "DevOps engineer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read",
             "Microsoft.AppPlatform/skus/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. 检查这些权限。

7. 选择“查看并创建”。 

---

## <a name="define-the-ops---site-reliability-engineering-role"></a>定义运营 - 现场可靠性工程角色

此过程定义有权部署、测试和重启 Azure Spring Cloud 应用的角色。

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. 重复用于添加开发人员角色的过程的步骤 1 到 4。
2. 选择 Ops - 站点可靠性工程角色的权限：

   在“Microsoft.AppPlatform/Spring”下，选择：
   * **读取：获取 Azure Spring Cloud 服务实例**
   * **其他：列出 Azure Spring Cloud 服务实例测试密钥**

   在“Microsoft.AppPlatform/Spring/apps”下，选择：
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序**

   在“Microsoft.AppPlatform/apps/deployments”下，选择：
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：启动 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：停止 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：重启 Microsoft Azure Spring Cloud 应用程序部署**

   在“Microsoft.AppPlatform/locations/operationResults/Spring”下，选择：
   * **读取：读取操作结果**

   在“Microsoft.AppPlatform/locations/operationStatus/operationId”下，选择：
   * **读取：读取操作状态**

   [ ![该屏幕截图显示运营 - 现场可靠性工程权限的选项。](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. 选择 **添加**。

4. 检查这些权限。

5. 选择“查看并创建”。 

#### <a name="json"></a>[JSON](#tab/JSON)
1. 重复用于添加开发人员角色的过程的步骤 1 到 4。
2. 选择“下一步”。

3. 选择“JSON”选项卡。

4. 选择“编辑”，然后删除默认文本：

   ![该屏幕截图显示默认 JSON 文本。](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. 粘贴以下 JSON 以定义运营 - 现场可靠性工程角色：

   ```json
   {
     "properties": {
       "roleName": "Ops - Site Reliability Engineering",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. 检查这些权限。

7. 选择“查看并创建”。 

---

## <a name="define-the-azure-pipelines--jenkins--github-actions-role"></a>定义 Azure Pipelines/Jenkins/GitHub Actions 角色

此角色可以在具有服务实例的 Azure Spring Cloud 和应用中创建和配置任何内容。 此角色用于发布或部署代码。

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

1. 重复用于添加开发人员角色的过程的步骤 1 到 4。
2. 打开“权限”选项。

3. 选择 Azure Pipelines/Jenkins/GitHub Actions 角色的权限：

   在“Microsoft.AppPlatform/Spring”下，选择：
   * **写入：创建或更新 Azure Spring Cloud 服务实例**
   * **删除：删除 Azure Spring Cloud 服务实例**
   * **读取：获取 Azure Spring Cloud 服务实例**
   * **其他：启用 Azure Spring Cloud 服务实例测试终结点**
   * **其他：禁用 Azure Spring Cloud 服务实例测试终结点**
   * **其他：列出 Azure Spring Cloud 服务实例测试密钥**
   * **其他：再生成 Azure Spring Cloud 服务实例测试密钥**

   在“Microsoft.AppPlatform/Spring/apps”下，选择：
   * **写入：写入 Microsoft Azure Spring Cloud 应用程序**
   * **删除：删除 Microsoft Azure Spring Cloud 应用程序**
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序**
   * **其他：获取 Microsoft Azure Spring Cloud 应用程序资源上传 URL**
   * **其他：验证 Microsoft Azure Spring Cloud 应用程序自定义域**

   在“Microsoft.AppPlatform/Spring/apps/bindings”下，选择：
   * **写入：写入 Microsoft Azure Spring Cloud 应用程序绑定**
   * **删除：删除 Microsoft Azure Spring Cloud 应用程序绑定**
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序绑定**

   在“Microsoft.AppPlatform/Spring/apps/deployments”下，选择：
   * **写入：写入 Microsoft Azure Spring Cloud 应用程序部署**
   * **删除：删除 Azure Spring Cloud 应用程序部署**
   * **读取：读取 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：启动 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：停止 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：重启 Microsoft Azure Spring Cloud 应用程序部署**
   * **其他：获取 Microsoft Azure Spring Cloud 应用程序部署日志文件 URL**

   在“Microsoft.AppPlatform/Spring/apps/deployments/skus”下，选择：
   * **读取：列出应用程序部署可用的 SKU**

   在“Microsoft.AppPlatform/locations”下，选择：
   * **其他：检查名称可用性**

   在“Microsoft.AppPlatform/locations/operationResults/Spring”下，选择：
   * **读取：读取操作结果**

   在“Microsoft.AppPlatform/locations/operationStatus/operationId”下，选择：
   * **读取：读取操作状态**

   在“Microsoft.AppPlatform/skus”下，选择：
   * **读取：列出可用的 SKU**

   [ ![该屏幕截图显示 Azure Pipelines/Jenkins/GitHub Actions 权限的选项。](media/spring-cloud-permissions/pipelines-permissions-box.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)

4. 选择 **添加**。

5. 检查这些权限。

6. 选择“查看并创建”。 

#### <a name="json"></a>[JSON](#tab/JSON)

1. 重复用于添加开发人员角色的过程的步骤 1 到 4。
2. 选择“下一步”。

3. 选择“JSON”选项卡。

4. 选择“编辑”，然后删除默认文本：

   ![该屏幕截图显示默认 JSON 文本。](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. 粘贴以下 JSON 以定义 Azure Pipelines /Jenkins/GitHub Actions 角色：

   ```json
   {
     "properties": {
       "roleName": "Azure Pipelines/Provisioning",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. 选择 **添加**。

7. 检查这些权限。

---

## <a name="see-also"></a>请参阅

* [使用 Azure 门户创建或更新 Azure 自定义角色](../role-based-access-control/custom-roles-portal.md)

有关定义自定义权限的三种方法的详细信息，请参阅：
* [克隆角色](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [从头开始](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [从 JSON 开始](../role-based-access-control/custom-roles-portal.md#start-from-json)
