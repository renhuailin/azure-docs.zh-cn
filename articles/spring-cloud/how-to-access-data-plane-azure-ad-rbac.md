---
title: 访问 Config Server 和服务注册表
titleSuffix: Azure Spring Cloud
description: 如何使用 Azure Active Directory 基于角色的访问控制访问 Config Server 和服务注册表终结点。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: ce5905b43d37faf3b1a6965ae04efb9f8f73c2ff
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835878"
---
# <a name="access-config-server-and-service-registry"></a>访问 Config Server 和服务注册表

本文介绍如何使用 Azure Active Directory (Azure AD) 基于角色的访问控制 (RBAC) 来访问由 Azure Spring Cloud 管理的 Spring Cloud Config Server 和 Spring Cloud 服务注册表。

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>向 Azure AD 用户/组、MSI 或服务主体分配角色

在 [管理组 | 订阅 | 资源组 | 资源] 范围中向 [用户 | 组 | 服务主体 | 托管标识] 分配角色。

| 角色名称                                       | 说明                                                                  |
|-------------------------------------------------|------------------------------------------------------------------------------|
| Azure Spring Cloud Config Server 读者         | 允许对 Azure Spring Cloud Config Server 进行读取访问。                       |
| Azure Spring Cloud Config Server 参与者    | 允许对 Azure Spring Cloud Config Server 进行读取、写入和删除访问。    |
| Azure Spring Cloud 服务注册表读者      | 允许对 Azure Spring Cloud 服务注册表进行读取访问。                    |
| Azure Spring Cloud 服务注册表参与者 | 允许对 Azure Spring Cloud 服务注册表进行读取、写入和删除访问。 |

有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

## <a name="access-config-server-and-service-registry-endpoints"></a>访问 Config Server 和服务注册表终结点

分配角色后，被分派人可通过以下过程访问 Spring Cloud Config Server 和 Spring Cloud 服务注册表终结点：

1. 获取访问令牌。 为 Azure AD 用户分配角色后，他们可运行以下命令，使用用户身份、服务主体或托管标识登录到 Azure CLI 以获取访问令牌。 有关详细信息，请参阅 [Azure CLI 身份验证](/cli/azure/authenticate-azure-cli)。

    ```azurecli
    az login
    az account get-access-token
    ```

1. 编写终结点。 我们支持 Azure Spring Cloud 托管 Spring Cloud Config Server 和 Spring Cloud 服务注册表的默认终结点。

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/{path}'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/{path}'*

    >[!NOTE]
    > 如果使用的是 Azure 中国，请将 `*.azuremicroservices.io` 替换为 `*.microservices.azure.cn`。 有关详细信息，请参阅 [Azure 中国开发者指南](/azure/china/resources-developer-guide)中的[检查 Azure 中的终结点](/azure/china/resources-developer-guide#check-endpoints-in-azure)部分。

1. 使用访问令牌访问编写的终结点。 将访问令牌放在标头中以提供授权：`--header 'Authorization: Bearer {TOKEN_FROM_PREVIOUS_STEP}`。

    例如：

    a. 访问诸如“https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/health”的终结点可查看 Config Server 的运行状况。

    b. 访问诸如“https://SERVICE_NAME.svc.azuremicroservices.io/eureka/eureka/apps”的终结点可以查看 Spring Cloud 服务注册表（此处为 Eureka）中的已注册应用。

    如果响应为“401 未授权”，则检查是否已成功分配角色。  角色生效或验证访问令牌是否未过期需要几分钟时间。

有关执行器终结点的详细信息，请参阅[生产就绪的终结点](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)。

有关 Eureka 终结点，请参阅 [Eureka-REST-operations](https://github.com/Netflix/eureka/wiki/Eureka-REST-operations)

有关 Config Server 终结点和详细路径信息，请参阅 [ResourceController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/resource/ResourceController.java) 和 [EncryptionController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/encryption/EncryptionController.java)。

## <a name="register-spring-boot-apps-to-spring-cloud-config-server-and-service-registry-managed-by-azure-spring-cloud"></a>将 Spring Boot 应用注册到 Azure Spring Cloud 托管 Spring Cloud Config Server 和服务注册表

分配角色后，可使用 Azure AD 令牌身份验证将 Spring Boot 应用注册到 Azure Spring Cloud 托管 Spring Cloud Config Server 和服务注册表。 Config Server 和服务注册表都支持[自定义 REST 模板](https://cloud.spring.io/spring-cloud-config/reference/html/#custom-rest-template)来注入持有者令牌进行身份验证。

有关更多信息，请参阅示例[访问 Azure Spring Cloud 托管 Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client) 以及[访问 Azure Spring Cloud 托管 Spring Cloud 服务注册表](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client)。 以下部分介绍了这些示例的一些重要详细信息。

在 AccessTokenManager.java 中：

`AccessTokenManager` 负责从 Azure AD 获取访问令牌。 在 application.properties 文件中配置服务主体的登录信息并初始化 `ApplicationTokenCredentials` 以获取令牌。 可以在这两个示例中查找此文件。

```java
prop.load(in);
tokenClientId = prop.getProperty("access.token.clientId");
String tenantId = prop.getProperty("access.token.tenantId");
String secret = prop.getProperty("access.token.secret");
String clientId = prop.getProperty("access.token.clientId");
credentials = new ApplicationTokenCredentials(
    clientId, tenantId, secret, AzureEnvironment.AZURE);
```

在 CustomConfigServiceBootstrapConfiguration.java 中：

`CustomConfigServiceBootstrapConfiguration` 可实现 Config Server 的自定义 REST 模板，并将来自 Azure AD 的令牌作为 `Authorization` 标头注入。 可在 [Config Server 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client)中找到此文件。

```java
public class RequestResponseHandlerInterceptor implements ClientHttpRequestInterceptor {

    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) throws IOException {
        String accessToken = AccessTokenManager.getToken();
        request.getHeaders().remove(AUTHORIZATION);
        request.getHeaders().add(AUTHORIZATION, "Bearer " + accessToken);

        ClientHttpResponse response = execution.execute(request, body);
        return response;
    }

}
```

在 CustomRestTemplateTransportClientFactories.java 中：

前两个类用于实现 Spring Cloud 服务注册表的自定义 REST 模板。 `intercept` 部分与上面的 Config Server 中相同。 确保将 `factory.mappingJacksonHttpMessageConverter()` 添加到消息转换器。 可以在 [Spring Cloud 服务注册表示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client)中找到此文件。

```java
private RestTemplate customRestTemplate() {
    /*
     * Inject your custom rest template
     */
    RestTemplate restTemplate = new RestTemplate();
    restTemplate.getInterceptors()
        .add(new RequestResponseHandlerInterceptor());
    RestTemplateTransportClientFactory factory = new RestTemplateTransportClientFactory();

    restTemplate.getMessageConverters().add(0, factory.mappingJacksonHttpMessageConverter());

    return restTemplate;
}
```

如果在 Kubernetes 群集上运行应用程序，我们建议使用 IP 地址注册 Spring Cloud 服务注册表以进行访问。

```properties
eureka.instance.prefer-ip-address=true
```

## <a name="next-steps"></a>后续步骤

* [Azure CLI 身份验证](/cli/azure/authenticate-azure-cli)
* [生产就绪终结点](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)
* [创建角色和权限](how-to-permissions.md)
