---
title: "include file" description: "include file" services: app-service author: cephalin ms.service: app-service ms.topic: "include" ms.date: 05/02/2021 ms.author: cephalin ms.custom: "include file", devx-track-azurecli
---

在 Cloud Shell 中，使用 [`az appservice plan create`](/cli/azure/appservice/plan) 命令创建一个应用服务计划。

<!-- [!INCLUDE [app-service-plan](app-service-plan.md)] -->

以下示例在免费定价层中创建名为 `myAppServicePlan` 的应用服务计划：

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE --is-linux
```

创建应用服务计划后，Azure CLI 会显示类似于以下示例的信息：

<pre>
{ 
  "freeOfferExpirationTime": null,
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>
