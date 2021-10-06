---
title: 为部署为 Web 服务的模型配置身份验证
titleSuffix: Azure Machine Learning
description: 了解如何为部署到 Azure 机器学习中的 Web 服务的机器学习模型配置身份验证。
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: mlops
ms.date: 11/06/2020
ms.topic: how-to
ms.openlocfilehash: d167ea6f71e1f9b47468c81e84e9221e5ec58c75
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427621"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>为部署为 Web 服务的模型配置身份验证

使用 Azure 机器学习，可以将经过训练的机器学习模型部署为 Web 服务。 本文介绍如何为这些部署配置身份验证。

可以将 Azure 机器学习创建的模型部署配置为使用两种身份验证方法之一：

* **基于密钥**：使用静态密钥向 Web 服务进行身份验证。
* **基于令牌**：必须从 Azure 机器学习工作区（使用 Azure Active Directory）获取临时令牌，并用于对 Web 服务进行身份验证。 此令牌在一段时间后将过期，并且必须刷新才能继续使用 Web 服务。

    > [!NOTE]
    > 只有部署到 Azure Kubernetes 服务时，基于令牌的身份验证才适用。

## <a name="key-based-authentication"></a>基于密钥的身份验证

部署在 Azure Kubernetes 服务 (AKS) 上的 Web 服务默认情况下会启用基于密钥的身份验证。

默认情况下，Azure 容器实例 (ACI) 部署的服务禁用基于密钥的身份验证，但你可以在创建 ACI Web 服务时通过设置 `auth_enabled=True` 来启用它。 以下代码是一个示例，演示了如何创建启用了基于密钥的身份验证的 ACI 部署配置。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

然后可以通过 `Model` 类在部署中使用自定义 ACI 配置。

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

若要提取身份验证密钥，请使用 `aci_service.get_keys()`。 若要重新生成密钥，请使用 `regen_key()` 函数并传递“主要”或“辅助”密钥 。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>基于令牌的身份验证

如果要为 Web 服务启用令牌身份验证，用户必须向 Web 服务提供 Azure 机器学习 JSON Web 令牌才能访问。 令牌在指定的时间范围后过期，需要刷新才能继续调用。

* 部署到 Azure Kubernetes 服务时，会默认禁用令牌身份验证。
* 部署到 Azure 容器实例时，不支持令牌身份验证。
* 令牌身份验证 **不能与基于密钥的身份验证同时使用**。

若要控制令牌身份验证，请在创建或更新部署时使用 `token_auth_enabled` 参数：

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

如果启用了令牌身份验证，可以使用 `get_token` 方法检索 JSON Web (JWT) 令牌以及该令牌的到期时间：

> [!TIP]
> 如果使用服务主体获取令牌，并希望它具有检索令牌所需的最小访问权限，请向其分配工作区的“读取者”角色。

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> 需要在令牌的 `refresh_by` 时间后请求一个新令牌。 如果需要刷新 Python SDK 外的令牌，一个选择是使用服务主体身份验证的 REST API 定期进行 `service.get_token()` 调用，如前文所述。
>
> 我们强烈建议在 Azure Kubernetes 服务群集所在的相同区域中创建 Azure 机器学习工作区。
>
> 若要使用令牌进行身份验证，Web 服务将调用创建 Azure 机器学习工作区的区域。 如果工作区的区域不可用，即使你的群集和工作区不在同一区域，你也无法获取 Web 服务的令牌。 结果是直到工作区的区域再次可用时，Azure AD 身份验证才可用。
>
> 此外，群集区域和工作区区域的距离越远，获取令牌所需的时间就越长。

## <a name="next-steps"></a>后续步骤

若要详细了解如何向已部署的模型进行身份验证，请参阅[为部署为 Web 服务的模型创建客户端](how-to-consume-web-service.md)。