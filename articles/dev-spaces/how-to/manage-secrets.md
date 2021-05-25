---
title: 使用 Azure Dev Space 时如何管理机密
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: 了解在使用 Azure Dev Spaces 开发应用程序时，如何在运行时或生成时使用 Kubernetes 机密
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
ms.custom: devx-track-js
ms.openlocfilehash: 8791480f420dfd76d5291ce82e8ebf7412a41326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972962"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>使用 Azure Dev Space 时如何管理机密

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

你的服务可能需要适用于某些服务（例如数据库或其他安全的 Azure 服务）的特定密码、连接字符串和其他机密。 在配置文件中设置这些机密的值即可让其以环境变量的形式在代码中使用。  必须小心处理这些配置文件，以免机密的安全性受损。

## <a name="storing-and-using-runtime-secrets"></a>存储和使用运行时机密

Azure Dev Spaces 提供了两个建议的简化选项，用于在 Azure Dev Spaces 客户端工具生成的 Helm 图表中存储机密：在 `values.dev.yaml` 文件中，在 `azds.yaml` 中直接内联。 建议不要将机密存储在 `values.yaml` 中。

> [!NOTE]
> 下面的方法演示了如何存储和使用客户端工具生成的 Helm 图表中的机密。 如果创建了自己的 Helm 图表，则可以直接使用该 Helm 图表来管理和存储机密。

### <a name="using-valuesdevyaml"></a>使用 values.dev.yaml

在已经使用 Azure Dev Spaces 准备好的项目中，在 `azds.yaml` 所在的文件夹中创建一个 `values.dev.yaml` 文件以定义机密密钥和值。 例如：

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

使用 `?` 验证 `azds.yaml` 文件是否引用了 `values.dev.yaml` 作为可选。 例如：

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

如果有其他机密文件，也可以在此处添加它们。

进行更新或验证你的服务是否引用了你的机密作为环境变量。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用 `azds up` 运行更新后的服务。

```console
azds up
```
 
使用 `kubectl` 验证你的机密是否已创建。

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> 建议不要将机密存储在源代码管理中。 如果使用的是 Git，请将 `values.dev.yaml` 添加到 `.gitignore` 文件中，以避免在源代码管理中提交机密。

### <a name="using-azdsyaml"></a>使用 azds.yaml

在已经使用 Azure Dev Spaces 准备好的项目中，使用 $PLACEHOLDER 语法在 `azds.yaml` 中的 configuration.develop.install.set 下添加机密密钥和值。 例如：

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> 你可以直接输入机密值，而无需在 `azds.yaml` 中使用 $PLACEHOLDER 语法。 但是，不建议使用此方法，因为 `azds.yaml` 存储在源代码管理中。
     
在 `azds.yaml` 所在的文件夹中创建一个 `.env` 文件，以定义 $PLACEHOLDER 值。 例如：

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> 建议不要将机密存储在源代码管理中。 如果使用的是 Git，请将 `.env` 添加到 `.gitignore` 文件中，以避免在源代码管理中提交机密。

进行更新或验证你的服务是否引用了你的机密作为环境变量。 例如：

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
使用 `azds up` 运行更新后的服务。

```console
azds up
```
 
使用 `kubectl` 验证你的机密是否已创建。

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>使用机密作为生成参数

上一部分介绍了如何存储和使用在容器运行时使用的机密。 你也可以通过 `azds.yaml` 在容器生成时使用机密，例如专用 NuGet 的密码。

在 `azds.yaml` 中，使用 `<variable name>: ${secret.<secret name>.<secret key>}` 语法在 configurations.develop.build.args 中设置生成时间机密。 例如：

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

在上面的示例中，mynugetsecret 是现有的机密，而 pattoken 是现有的密钥。

>[!NOTE]
> 机密名称和密钥可能包含 `.` 字符。 在将机密作为生成参数传递时，使用 `\` 转义 `.`。 例如，使用 token 的密钥 `MYTOKEN: ${secret.foo\.bar.token}`，传递一个名为 foo.bar 的机密。 此外，还可以通过前缀和后缀文本来评估机密。 例如，`MYURL: eus-${secret.foo\.bar.token}-version1`。 另外，可以将父空间和祖父空间中的可用机密作为生成参数进行传递。

在 Dockerfile 中，通过 ARG 指令来使用机密，稍后在 Dockerfile 中使用该相同的变量。 例如：

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

使用这些更改更新在群集中运行的服务。 在命令行上运行以下命令：

```
azds up
```

## <a name="next-steps"></a>后续步骤

通过这些方法，现在可以安全地连接到数据库、Azure Redis 缓存，或者访问安全的 Azure 服务。
 
