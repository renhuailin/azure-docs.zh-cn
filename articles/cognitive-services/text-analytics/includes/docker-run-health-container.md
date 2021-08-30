---
title: docker run 命令的运行容器示例
titleSuffix: Azure Cognitive Services
description: 运行状况容器的 Docker run 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/18/2021
ms.author: aahi
ms.openlocfilehash: 47a7059e21f1c9b9d6d72644bc08c62b66afc772
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339671"
---
## <a name="install-the-container"></a>安装容器

可以通过多种方式来安装和运行运行状况文本分析容器。 

- 使用 [Azure 门户](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)创建文本分析资源，并使用 Docker 获取容器。
- 使用带有 Docker 的 Azure VM 运行容器。 请参阅 [Azure 上的 Docker](../../../docker/index.yml)。
- 使用以下 PowerShell 和 Azure CLI 脚本自动执行资源部署和容器配置。

### <a name="run-the-container-locally"></a>在本地运行容器

若要在下载容器映像后在自己的环境中运行容器，请执行以下 `docker run` 命令。 将下面的占位符替换为你自己的值：

| 占位符 | Value | 格式或示例 |
|-------------|-------|---|
| **{API_KEY}** | 文本分析资源的密钥。 可以在 Azure 门户中资源的“密钥和终结点”页上找到此项。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 用于访问文本分析 API 的终结点。 可以在 Azure 门户中资源的“密钥和终结点”页上找到此项。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

此命令：

- 从容器映像运行“针对运行状况的文本分析”容器
- 分配 6 个 CPU 核心和 12 千兆字节 (GB) 内存
- 公开 TCP 端口 5000，并为容器分配伪 TTY
- 接受最终用户许可协议 (Eula) 和负责任 AI (RAI) 条款
- 退出后自动删除容器。 容器映像在主计算机上仍然可用。

### <a name="demo-ui-to-visualize-output"></a>用于可视化输出的演示 UI

> [!NOTE]
> 演示仅适用于运行状况文本分析容器。

容器提供了基于 REST 的查询预测终结点 API。  我们还在容器中提供了一个可视化工具，通过将 `/demo` 追加到容器的终结点，可以访问该工具。 例如：

```
http://<serverURL>:5000/demo
```

使用下面的示例 cURL 请求将查询提交到已部署的容器，并使用适当的值替换 `serverURL` 变量。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>使用 Azure 用于容器的 Web 应用安装容器

Azure [用于容器的 Web 应用](https://azure.microsoft.com/services/app-service/containers/)是专用于在云中运行容器的 Azure 资源。 它提供自动缩放、Docker 容器和 Docker 编写支持、HTTPS 支持等开箱即用功能。

> [!NOTE]
> 使用 Azure Web 应用时，将自动获取 `<appservice_name>.azurewebsites.net` 格式的域

通过 HTTPS 使用订阅和容器映像，从而利用 Azure CLI 运行此 PowerShell 脚本来创建用于容器的 Web 应用。 等待脚本完成（大约 25-30 分钟），然后提交第一个请求。

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME 
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>使用 Azure 容器实例安装容器

还可以使用 Azure 容器实例 (ACI) 更轻松地部署。 ACI 资源允许在托管的无服务器 Azure 环境中按需运行 Docker 容器。 

有关使用 Azure 门户部署 ACI 资源的步骤，请参阅[如何使用 Azure 容器实例](../../containers/azure-container-instance-recipe.md)。 还可以通过 Azure CLI 使用以下 PowerShell 脚本，这将使用容器映像在订阅上创建 ACI。  等待脚本完成（大约 25-30 分钟），然后提交第一个请求。  由于每个 ACI 资源的最大 CPU 数的限制，如果你希望每个请求提交 5 个以上的大型文档（每个文档大约 5000 个字符），请不要选择此选项。
请参阅 [ACI 区域支持](../../../container-instances/container-instances-region-availability.md)文章，获取可用性信息。 

> [!NOTE] 
> Azure 容器实例不包括对内置域的 HTTPS 支持。 如果需要 HTTPS，则需要手动配置它，包括创建证书和注册域。 可以通过下面的 NGINX 查找有关如何执行此操作的说明。

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>保护 ACI 连接

默认情况下，将 ACI 与容器 API 一起使用时不提供安全性。 这是因为大多数情况下容器会作为 Pod 的一部分运行，而 Pod 受网络桥的保护，与外部隔离。 但是，可以使用前端组件来修改容器，使容器终结点保持专用。 以下示例使用 [NGINX](https://www.nginx.com) 作为入口网关，以支持 HTTPS/SSL 和客户端证书身份验证。

> [!NOTE]
> NGINX 是高性能的开源 HTTP 服务器和代理。 NGINX 容器可用于终止单个容器的 TLS 连接。 还可能会有更复杂的基于 NGINX 入口的 TLS 终止解决方案。

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>将 NGINX 设置为入口网关

NGINX 使用[配置文件](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)在运行时启用功能。 若要为另一个服务启用 TLS 终止，必须指定用于终止 TLS 连接的 SSL 证书，以及用于指定服务地址的 `proxy_pass`。 下面提供了示例。


> [!NOTE]
> `ssl_certificate` 需要路径在 NGINX 容器的本地文件系统中进行指定。 为 `proxy_pass` 指定的地址必须在 NGINX 容器的网络中可用。

NGINX 容器将 `/etc/nginx/conf.d/` 下装载的 `_.conf_` 中的所有文件加载到 HTTP 配置路径。

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>示例 Docker compose 文件

下面的示例演示如何创建 [docker compose](https://docs.docker.com/compose/reference/overview) 文件来部署 NGINX 和健康状况文本分析容器：

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

若要启动此 Docker compose 文件，请在文件根级别从控制台中执行以下命令：

```bash
docker-compose up
```

有关详细信息，请参阅有关 [NGINX SSL 终止](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)的 NGINX 文档。