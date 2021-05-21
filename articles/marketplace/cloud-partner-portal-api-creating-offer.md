---
title: 创建或修改产品/服务 - Azure 市场
description: 用于新建产品/服务或更新现有产品/服务的 API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "87420220"
---
# <a name="create-or-modify-an-offer"></a>创建或修改产品/服务

> [!NOTE]
> 云合作伙伴门户 API 已与合作伙伴中心集成，并将继续在其中运行。 本次转换带来了少量更改。 查看[云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md)中列出的更改，确保转换到合作伙伴中心后代码继续正常工作。 CPP API 应仅用于在转换到合作伙伴中心之前已经集成的现有产品；新产品应使用合作伙伴中心提交 API。

此调用更新发布者命名空间中的特定产品/服务，或创建新的产品/服务。

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 参数

|  **名称**         |  **说明**                      |  **数据类型**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  发布者标识符，例如 `contoso` |   String |
| offerId           |  产品/服务标识符                     |   String        |
| api-version       |  API 最新版本            |   日期           |
|  |  |  |

## <a name="header"></a>标头

|  **名称**        |  **值**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| 授权    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>正文示例

以下示例创建 offerID 为 `contosovirtualmachine` 的产品/服务。

### <a name="request"></a>请求

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>响应

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> 若要修改此产品/服务，请在上述请求中添加设置为“*”的 **If-Match** 标头。 使用与上面相同的请求正文，但根据需要修改值。 

### <a name="response-status-codes"></a>响应状态代码

| **代码**  |  **说明**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. 请求已成功处理，并且已成功修改产品/服务。           |
|  201      | `Created`. 请求已成功处理，并且已成功创建产品/服务。   |
|  400      | `Bad/Malformed request`. 错误响应正文可以提供更多信息。            |
|  403      | `Forbidden`. 客户端无法访问请求的命名空间。                     |
|  404      | `Not found`. 客户端引用的实体不存在。                           |
|  412      | 服务器不满足请求者在请求中指定的某一前提条件。 客户端应检查随请求发送的 ETAG。 |
|  |  |

## <a name="uploading-artifacts"></a>上传项目

应该通过将项目（例如图像和徽标）上传到 Web 上的可访问位置来共享项目，然后将每个项目作为 URI 包含在 PUT 请求中，如上例所示。 系统会检测到 Azure 市场存储中不存在这些文件，并会将这些文件下载到存储中。  因此，你会发现以后的 GET 请求将返回这些文件的 Azure 市场服务 URL。

## <a name="categories-and-industries"></a>类别和行业

新建产品/服务时，必须在市场中为产品/服务指定类别。 （可选）对于某些产品/服务类型，还可以指定行业。 根据产品/服务类型，使用下表中的特定键值提供适用于该产品/服务的类别/行业。

### <a name="azure-marketplace-categories"></a>Azure 市场类别

这些类别及其各自的密钥适用于 Azure 应用、虚拟机、核心虚拟机、容器、容器应用、IoT Edge 模块和 SaaS 产品/服务类型。 粗斜体项（例如 ***analytics***）是类别，而标准文本项（例如 data-insights）是其下的子类别。 使用准确的密钥值，而不更改间距或大小写。

| 类别 | SaaS 密钥 | Azure 应用密钥 | 虚拟机、容器、容器应用、IoT Edge 模块、核心虚拟机密钥 |
| --- | --- | --- | --- |
| ***分析** _ | _*_analytics_*_ | _*_analytics-azure-apps_*_ | _ *_analytics-amp_** |
| 数据见解 | data-insights | data-insights | data-insights |
| 数据分析 | data-analytics | data-analytics | data-analytics |
| 大数据 | big-data | bigData | big-data |
| 预测分析 | predictive-analytics | predictive-analytics | predictive-analytics |
| 实时/流式处理分析 | real-time-streaming-analytics | real-time-streaming-analytics | real-time-streaming-analytics |
| 其他 | 其他 | other-analytics | 其他 |
| ***AI + 机器学习** _ | _*_ArtificialIntelligence_*_ | _*_ai-plus-machine-learning_*_ | _ *_ai-plus-machine-learning_** |
| 机器人服务 | bot-services | bot-services | bot-services |
| 认知服务 | cognitive-services | cognitive-services | cognitive-services |
| ML 服务 | ml-service | ml-service | ml-service |
| 自动化机器学习 | automated-ml | automated-ml | automated-ml |
| 业务/机器人流程自动化 | business-robotic-process-automation | business-robotic-process-automation | business-robotic-process-automation |
| 数据标记 | data-labelling | data-labelling | data-labelling |
| 数据准备 | data-preparation | data-preparation | data-preparation |
| 知识挖掘 | knowledge-mining | knowledge-mining | knowledge-mining |
| ML 操作 | ml-operations | ml-operations | ml-operations |
| 其他 | other-AI-plus-machine-learning | 其他 | 其他 |
| ***区块链** _ | _*_blockchain_*_ | _*_blockchain_*_ | _ *_blockchain_** |
| 应用加速器 | app-accelerators | app-accelerators | app-accelerators |
| 单节点账本 | single-node-ledger | single-node-ledger | single-node-ledger |
| 多节点账本 | multi-node-ledger | multi-node-ledger | multi-node-ledger |
| 工具 | 工具 | 工具 | 工具 |
| 其他 | 其他 | 其他 | 其他 |
| ***计算** _ | _*_compute-saas_*_ | _*_compute-azure-apps_*_ | _ *_计算_** |
| 应用程序基础结构 | appInfra | appInfrastructure | application-infrastructure |
| 操作系统 | clientOS | clientOS | operating-systems |
| 缓存 | cache | cache | cache |
| 其他 | other-compute | other-compute | 其他 |
| ***容器** _ | _*_containers_*_ | _*_containers_*_ | _ *_containers_** |
| 容器应用 | container-apps | container-apps | container-apps |
| 容器映像 | container-images | container-images | container-images |
| 容器入门 | get-started-with-containers | get-started-with-containers | get-started-with-containers |
| 其他 | 其他 | 其他 | 其他 |
| ***数据库** _ | _*_databases-saas_*_ | _*_database_*_ | _ *_databases_** |
| NoSQL 数据库 | nosql-databases | nosql-databases | nosql-databases |
| 关系数据库 | relational-databases | relational-databases | relational-databases |
| 账本/区块链数据库 | ledger-blockchain-databases | ledger-blockchain-databases | ledger-blockchain-databases |
| 数据湖 | data-lakes | data-lakes | data-lakes |
| 数据仓库 | data-warehouse | data-warehouse | data-warehouse |
| 其他 | other-databases | other-databases | 其他 |
| ***开发人员工具** _ | _*_developer-tools-saas_*_ | _*_developer-tools-azure-apps_*_ | _ *_developer-tools_** |
| 工具 | tools-developer-tools | tools-developer-tools | tools-developer-tools |
| 脚本 | 脚本 | 脚本 | 脚本 |
| 开发人员服务 | devService | devService | developer-service |
| 其他 | other-developer-tools | other-developer-tools | 其他 |
| ***DevOps** _ | _*_devops_*_ | _*_devops_*_ | _ *_devops_** |
| 其他 | 其他 | 其他 | 其他 |
| ***标识** _ | _*_identity_*_ | _*_identity_*_ | _ *_identity_** |
| 访问管理 | access-management | access-management | access-management |
| 其他 | 其他 | 其他 | 其他 |
| ***集成** _ | _*_integration_*_ | _*_integration_*_ | _ *_integration_** |
| 消息传递 | 消息传递 | 消息传递 | 消息传递 |
| 其他 | 其他 | 其他 | 其他 |
| ***物联网** _ | _*_IoT_*_ | _*_internet-of-things-azure-apps_*_ | _ *_internet-of-things_** |
| IoT Core Services | 不适用 | iot-core-services | iot-core-services |
| IoT Edge 模块 | 不适用 | iot-edge-modules | iot-edge-modules |
| IoT 解决方案 | iot-solutions | iot-solutions | iot-solutions |
| 数据分析和可视化效果 | data-analytics-and-visualization | data-analytics-and-visualization | data-analytics-and-visualization |
| IoT 连接性 | iot-connectivity | iot-connectivity | iot-connectivity |
| 其他 | other-internet-of-things | other-internet-of-things | 其他 |
| ***IT 和管理工具** _ | _*_ITandAdministration_*_ | _*_it-and-management-tools-azure-apps_*_ | _ *_it-and-management-tools_** |
| 管理解决方案 | management-solutions | management-solutions | management-solutions |
| 业务应用程序 | businessApplication | businessApplication | business-applications |
| 其他 | other-it-management-tools | other-it-management-tools | 其他 |
| ***监视与诊断** _ | _*_monitoring-and-diagnostics_*_ | _*_monitoring-and-diagnostics_*_ | _ *_monitoring-and-diagnostics_** |
| 其他 | 其他 | 其他 | 其他 |
| ***媒体** _ | _*_media_*_ | _*_media_*_ | _ *_media_** |
| 媒体服务 | media-services | media-services | media-services |
| 内容保护 | content-protection | content-protection | content-protection |
| 实时和按需流式处理 | live-and-on-demand-streaming | live-and-on-demand-streaming | live-and-on-demand-streaming |
| 其他 | 其他 | 其他 | 其他 |
| ***迁移** _ | _*_migration_*_ | _*_migration_*_ | _ *_migration_** |
| 数据迁移 | data-migration | data-migration | data-migration |
| 其他 | 其他 | 其他 | 其他 |
| ***混合现实** _ | _*_mixed-reality_*_ | _*_mixed-reality_*_ | _ *_mixed-reality_** |
| 其他 | 其他 | 其他 | 其他 |
| ***网络** _ | _*_networking_*_ | _*_networking_*_ | _ *_networking_** |
| 设备管理器 | appliance-managers | appliance-managers | appliance-managers |
| 连接 | 连接 | 连接 | 连接 |
| 防火墙 | 防火墙 | 防火墙 | 防火墙 |
| 负载均衡器 | load-balancers | load-balancers | load-balancers |
| 其他 | 其他 | 其他 | 其他 |
| ***安全性** _ | _*_security_*_ | _*_security_*_ | _ *_security_** |
| 标识和访问管理 | identity-and-access-management | identity-and-access-management | identity-and-access-management |
| 威胁防护 | threat-protection | threat-protection | threat-protection |
| 信息保护 | information-protection | information-protection | information-protection |
| 其他 | 其他 | 其他 | 其他 |
| ***存储** _ | _*_storage-saas_*_ | _*_storage-azure-apps_*_ | _ *_storage_** |
| 备份和恢复 | 备份 (backup) | 备份 (backup) | backup-and-recovery |
| 企业混合存储 | enterprise-hybrid-storage | enterprise-hybrid-storage | enterprise-hybrid-storage |
| 文件共享 | file-sharing | file-sharing | file-sharing |
| 数据生命周期管理 | data-lifecycle-management | data-lifecycle-management | data-lifecycle-management |
| 其他 | other-storage | other-storage | 其他 |
| ***Web** _ | _*_web_*_ | _*_web_*_ | _ *_web_** |
| 博客与 CMS | blogs-and-cmss | blogs-and-cmss | blogs-and-cmss |
| 入门级 Web 应用 | starter-web-apps | starter-web-apps | starter-web-apps |
| 电子商务 | ecommerce | ecommerce | ecommerce |
| Web 应用框架 | web-apps-frameworks | web-apps-frameworks | web-apps-frameworks |
| Web 应用 | web-apps | web-apps | web-apps |
| 其他 | 其他 | 其他 | 其他 |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource 类别

这些类别及其各自的密钥适用于 SaaS、PowerBI 应用、Dynamics 365 Business Central、Dynamics 365 for Customer Engagement 和适用于运营产品/服务类型的 Dynamics 365。 粗斜体项（例如 ***analytics***）是类别，而标准文本项（例如 advanced-analytics）是其下的子类别。 使用准确的密钥值，而不更改间距或大小写。

| 类别 | SaaS 密钥 | Dynamics 365 Business Central、Dynamics 365 for Customer Engagement、Dynamics 365 for Operation Keys | PowerBI 应用密钥 |
| --- | --- | --- | --- |
| ***分析** _ | _*_analytics_*_ | _*_分析_*_ | _ *_分析_** |
| 高级分析 | advanced-analytics | advanced-analytics | advanced-analytics |
| 可视化效果和报表 | visualization-reporting | visualization-reporting | visualization-reporting |
| 其他 | 其他 | other-analytics | other-analytics |
| ***AI + 机器学习** _ | _*_ArtificialIntelligence_*_ | _*_ai-plus-machine-learning-dynamics_*_ | _ *_ai-plus-machine-learning-appsource_** |
| 面向企业的 AI | ai-for-business | ai-for-business | ai-for-business |
| 机器人应用 | bot-apps | bot-apps | bot-apps |
| 其他 | other-ai-plus-machine-learning | other-ai-plus-machine-learning | other-ai-plus-machine-learning |
| ***协作** _ | _*_协作_*_ | _*_协作_*_ | _ *_协作_** |
| 联络和人脉 | contact-people | contact-people | contact-and-people |
| 会议管理 | meeting-management | meeting-management | meeting-management |
| 网站设计和管理 | site-design-management | site-design-management | site-design-and-management |
| 任务和项目管理 | task-project-management | task-project-management | task-and-project-management |
| 语音和视频会议 | voice-video-conferencing | voice-video-conferencing | voice-and-video-conferencing |
| 其他 | other-collaboration | other-collaboration | 其他 |
| ***符合性和法律** _ | _*_compliance_*_ | _*_compliance_*_ | _ *_compliance-and-legal_** |
| 税务和审计 | tax-audit | tax-audit | tax-and-audit |
| Legal | Legal | Legal | 法律 |
| 数据、治理和隐私 | data-governance-privacy | data-governance-privacy | data-governance-and-privacy |
| 医疗和安全 | health-safety | health-safety | health-and-safety |
| 其他 | other-compliance-legal | other-compliance-legal | 其他 |
| ***客户服务** _ | _*_CustomerService_*_ | _*_CustomerService_*_ | _ *_customer-service_** |
| 联系中心 | contact-center | contact-center | contact-center |
| 面对面服务 | face-to-face-service | face-to-face-service | face-to-face-service |
| 后端办公系统和员工服务 | back-office-employee-service | back-office-employee-service | back-office-and-employee-service |
| 知识和案例管理 | knowledge-case-management | knowledge-case-management | knowledge-and-case-management |
| 社交媒体和全渠道参与 | social-media-omnichannel-engagement | social-media-omnichannel-engagement | social-media-and-omnichannel-engagement |
| 其他 | other-customer-service | other-customer-service | 其他 |
| ***财务** _ | _*_Finance_*_ | _*_Finance_*_ | _ *_finance_** |
| 计帐 | 会计 | 会计 | 会计 |
| 资产管理 | asset-management | asset-management | asset-management |
| 分析、合并和报表 | analytics-consolidation-reporting | analytics-consolidation-reporting | analytics-consolidation-and-reporting |
| 信贷和收款 | credit-collections | credit-collections | credit-and-collections |
| 符合性和风险管理 | compliance-risk-management | compliance-risk-management | compliance-and-risk-management |
| 其他 | other-finance | other-finance | 其他 |
| ***人力资源** _ | _*_HumanResources_*_ | _*_HumanResources_*_ | _ *_human-resources_** |
| 人才收购 | talent-acquisition | talent-acquisition | talent-acquisition |
| 人才管理 | talent-management | talent-management | talent-management |
| 人力资源运营 | hr-operations | hr-operations | hr-operations |
| 劳动力规划和分析 | workforce-planning-analytics | workforce-planning-analytics | workforce-planning-and-analytics |
| 其他 | other-human-resources | other-human-resources | 其他 |
| ***物联网** _ | _*_IoT_*_ | _*_internet-of-things-dynamics_*_ | _ *_internet-of-things-appsource_** |
| 资产管理和运营 | asset-management-operations | asset-management-operations | asset-management-and-operations |
| 联网产品 | connected-products | connected-products | connected-products |
| 智能供应链 | intelligent-supply-chain | intelligent-supply-chain | intelligent-supply-chain |
| 预测性维护 | predictive-maintenance | predictive-maintenance | predictive-maintenance |
| 远程监视 | remote-monitoring | remote-monitoring | remote-monitoring |
| 安保 | safety-security | safety-security | safety-and-security |
| 智能基础结构和资源 | smart-infrastructure-resources | smart-infrastructure-resources | smart-infrastructure-and-resources |
| 车辆和出行 | vehicles-mobility | vehicles-mobility | vehicles-and-mobility |
| 其他 | other-internet-of-things | other-internet-of-things | 其他 |
| ***IT 和管理工具** _ | _*_ITandAdministration_*_ | _*_ITandAdministration_*_ | _ *_it-and-management-tools_** |
| 管理解决方案 | management-solutions | management-solutions | management-solutions |
| 业务应用程序 | businessApplication | businessApplication | business-applications |
| 其他 | other-it-management-tools | other-it-management-tools | 其他 |
| ***市场营销** _ | _*_Marketing_*_ | _*_Marketing_*_ | _ *_marketing_** |
| 播发 | 播发 | 播发 | 播发 |
| 分析 | analytics-marketing | analytics-marketing | analytics-marketing |
| 市场活动管理和自动化 | campaign-management-automation | campaign-management-automation | campaign-management-and-automation |
| 电子邮件市场营销 | email-marketing | email-marketing | email-marketing |
| L2 - 事件和资源管理 | events-resource-management | events-resource-management | events-and-resource-management |
| 研究和分析 | research-analytics | research-analytics | research-and-analysis |
| 社交媒体 | social-media | social-media | social-media |
| 其他 | other-marketing | other-marketing | 其他 |
| ***运营和供应链** _ | _*_OperationsSupplyChain_*_ | _*_OperationsSupplyChain_*_ | _ *_operations-and-supply-chain_** |
| 资产与生产管理 | asset-production-management | asset-production-management | asset-and-production-management |
| 需求预测 | demand-forecasting | demand-forecasting | demand-forecasting |
| 信息管理与连接 | information-management-connectivity | information-management-connectivity | information-management-and-connectivity |
| 规划、购买与报表 | planning-purchasing-reporting | planning-purchasing-reporting | planning-purchasing-and-reporting |
| 质量与服务管理 | quality-service-management | quality-service-management | quality-and-service-management |
| 销售与订单管理 | sales-order-management | sales-order-management | sales-and-order-management |
| 运输和仓库管理 | transportation-warehouse-management | transportation-warehouse-management | transportation-and-warehouse-management |
| 其他 | other-operations-supply-chain | other-operations-supply-chain | 其他 |
| ***工作效率** _ | _*_工作效率_*_ | _*_工作效率_*_ | _ *_productivity_** |
| 内容创建和管理 | content-creation-management | content-creation-management | content-creation-and-management |
| 语言和翻译 | language-translation | language-translation | language-and-translation |
| 文档管理 | document-management | document-management | document-management |
| 电子邮件管理 | email-management | email-management | email-management |
| 搜索和引用 | search-reference | search-reference | search-and-reference |
| 其他 | other-productivity | other-productivity | 其他 |
| 游戏化 | 游戏化 | 游戏化 | 游戏化 |
| ***销售** _ | _*_Sales_*_ | _*_Sales_*_ | _ *_Sales_** |
| 电话销售 | telesales | telesales | telesales |
| 配置、定价和报价(CPQ) | configure-price-quote | configure-price-quote | configure-price-quote |
| 合同管理 | contract-management | contract-management | contract-management |
| CRM | crm | crm | crm |
| 电子商务 | e-commerce | e-commerce | e-commerce |
| 业务数据扩充 | business-data-enrichment | business-data-enrichment | business-data-enrichment |
| 销售支持 | sales-enablement | sales-enablement | sales-enablement |
| 其他 | other-sales | other-sales | other-sales |
| ***地理位置** _ | _*_geolocation_*_ | _*_geolocation_*_ | _ *_geolocation_** |
| Maps | maps | maps | maps |
| 新闻和天气 | news-and-weather | news-and-weather | news-and-weather |
| 其他 | other-geolocation | other-geolocation | other-geolocation |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource 行业

这些行业及其各自的密钥适用于 SaaS、PowerBI 应用、Dynamics 365 Business Central、Dynamics 365 for Customer Engagement 和适用于运营产品/服务类型的 Dynamics 365。 粗斜体项（例如 ***Automotive***）是类别，而标准文本项（例如 AutomotiveL2）是其下的子类别。 使用准确的密钥值，而不更改间距或大小写。

| 行业 | SaaS、Dynamics 365 Business Central、Dynamics 365 for Customer Engagement、Dynamics 365 for Operation Keys | PowerBI 应用密钥 |
| --- | --- | --- |
| ***汽车** _ | _*_汽车_*_ | _ *_automotive_** |
| 汽车 | AutomotiveL2 | AutomotiveL2 |
| ***农业** _ | _*_农业_*_ | _ *_agriculture_** |
| 其他 - 未划分 | Agriculture\_OtherUnsegmented | other-unsegmented |
| ***分发** _ | _*_分发_*_ | _ *_distribution_** |
| 批发 | 批发 | wholesale |
| 包裹寄送 | ParcelAndPackageShipping | parcel-and-package-shipping |
| ***教育** _ | _*_教育_*_ | _ *_education_** |
| 高等教育 | HigherEducation | higher-education |
| 中小学教育/K-12 | PrimaryAndSecondaryEducationK12 | primary-and-secondary-education |
| 图书馆和博物馆 | LibrariesAndMuseums | libraries-and-museums |
| ***金融服务** _ | _*_FinancialServices_*_ | _ *_financial-services_** |
| 银行业和资本市场 | BankingAndCapitalMarkets | banking-and-capital-markets |
| 保险 | 保险 | 保险 |
| ***政府** _ | _*_政府机关_*_ | _ *_government_** |
| 国防和情报 | DefenseAndIntelligence | defense-and-intelligence |
| 公安与司法 | PublicSafetyAndJustice | public-safety-and-justice |
| 平民政府 | CivilianGovernment | civilian-government |
| ***医疗保健** _ | _*_HealthCareandLifeSciences_*_ | _ *_healthcare_** |
| 医疗付款人 | HealthPayor | health-payor |
| 医疗提供方 | HealthProvider | health-provider |
| 医药业 | 医药业 | 药品 |
| ***制造和资源** _ | _*_制造_*_ | _ *_manufacturing-and-resources_** |
| 化工和农业化学 | ChemicalAndAgrochemical | chemical-and-agrochemical |
| 离散制造 | DiscreteManufacturing | discrete-manufacturing |
| 能源 | 能源 | energy |
| ***零售和消费品** _ | _*_RetailandConsumerGoods_*_ | _ *_retail-and-consumer-goods_** |
| 消费品 | ConsumerGoods | consumer-goods |
| 零售商 | 零售商 | retailers |
| ***媒体和通信** _ | _*_MediaAndCommunications_*_ | _ *_media-and-communications_** |
| 媒体和娱乐 | MediaandEntertainment | media-and-entertainment |
| 电信 | 电信 | telecommunications |
| ***专业服务** _ | _*_ProfessionalServices_*_ | _ *_professional-services_** |
| Legal | Legal | 法律 |
| 合作伙伴专业服务 | PartnerProfessionalServices | partner-professional-services |
| ***建筑和建设** _ | _*_ArchitectureAndConstruction_*_ | _ *_architecture-and-construction_** |
| 其他 - 未划分 | ArchitectureAndConstruction\_OtherUnsegmented | other-unsegmented |
| ***酒店餐饮和旅游** _ | _*_HospitalityandTravel_*_ | _ *_hospitality-and-travel_** |
|    酒店和休闲 | HotelsAndLeisure | hotels-and-leisure |
| 旅游和运输业 | TravelAndTransportation | travel-and-transportation |
| 餐饮服务 | RestaurantsAndFoodServices | restaurants-and-food-services |
| ***其他公共部门行业** _ | _*_OtherPublicSectorIndustries_*_ | _ *_other-public-sector-industries_** |
| 林业和渔业 | ForestryAndFishing | forestry-and-fishing |
| 非营利组织 | 非营利组织 | nonprofits |
| ***房地产** _ | _*_RealEstate_*_ | _ *_real-estate_** |
| 其他 - 未划分 | RealEstate\_OtherUnsegmented | other-unsegmented |
|||
