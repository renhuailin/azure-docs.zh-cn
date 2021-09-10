---
title: 在 Azure Spring Cloud 中设置 Config Server 实例
description: 了解如何在 Azure 门户中为 Azure Spring Cloud 设置 Spring Cloud Config Server 实例
ms.service: spring-cloud
ms.topic: how-to
ms.author: karler
author: karlerickson
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: 0de08976f0391c995004265ac1b1a33cf4a5c491
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835785"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>为服务设置 Spring Cloud 配置服务器实例

本文适用于：✔️ Java ✔️ C#

本文介绍如何将 Spring Cloud 配置服务器实例连接到 Azure Spring Cloud 服务。

Spring Cloud Config 为分布式系统中的外部化配置提供服务器和客户端支持。 使用配置服务器实例可在一个中心位置管理所有环境中应用程序的外部属性。 有关详细信息，请参阅 [Spring Cloud 配置服务器引用](https://spring.io/projects/spring-cloud-config)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个已预配的处于运行状态的 Azure Spring Cloud 服务。 若要设置并启动 Azure Spring Cloud 服务，请参阅[快速入门：使用 Azure CLI 启动 Java Spring 应用程序](./quickstart.md)。

## <a name="restriction"></a>限制

在后端上使用配置服务器时存在一些限制。 某些属性将自动注入到应用程序环境，以访问“配置服务器”和“服务发现”。 如果你同时从配置服务器文件配置了这些属性，可能会遇到冲突和意外的行为。 属性包括：

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> 我们强烈建议 _不要_ 将上述属性放入配置服务器应用程序文件中。

## <a name="create-your-config-server-files"></a>创建配置服务器文件

Azure Spring Cloud 支持使用 Azure DevOps、GitHub、GitLab 和 Bitbucket 来存储配置服务器文件。 准备好存储库后，请按照以下说明创建配置文件，并将其存储到这些位置。

此外，一些可配置属性仅适用于某些类型。 以下小节列出了每个存储库类型的属性。

### <a name="public-repository"></a>公共存储库

使用公共存储库时，可配置属性的受限程度更严重。

下表列出了用于设置公共 Git 存储库的所有可配置属性：

> [!NOTE]
> 使用连字符 (-) 分隔单词是目前唯一受支持的命名约定。 例如，可使用“default-label”，但不能使用“defaultLabel” 。

| properties        | 必选 | Feature                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | 是    | 用作配置服务器后端的 Git 存储库的 URI 以“http://”、“https://”、“git@”或“ssh://”开头   。 |
| `default-label` | 否     | Git 存储库的默认标签应为存储库的分支名称、标记名称或 commit-id  。 |
| `search-paths`  | 否     | 用于搜索 Git 存储库子目录的字符串数组。 |

------

### <a name="private-repository-with-ssh-authentication"></a>使用 SSH 身份验证的专用存储库

下表列出了用于设置使用 SSH 的专用 Git 存储库的所有可配置属性：

> [!NOTE]
> 使用连字符 (-) 分隔单词是目前唯一受支持的命名约定。 例如，可使用“default-label”，但不能使用“defaultLabel” 。

| properties                   | 必选 | Feature                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | 是    | 用作配置服务器后端的 Git 存储库的 URI 应以“http://”、“https://”、“git@”或“ssh://”开头   。 |
| `default-label`            | 否     | Git 存储库的默认标签应为存储库的分支名称、标记名称或 commit-id  。 |
| `search-paths`             | 否     | 用于搜索 Git 存储库子目录的字符串数组。 |
| `private-key`              | 否     | 用于访问 Git 存储库的 SSH 私钥，如果 URI 以“git@”或“ssh://”开头，则此私钥是必需的 。 |
| `host-key`                 | 否     | Git 存储库服务器的主机密钥，不应包含 `host-key-algorithm` 涵盖的算法前缀。 |
| `host-key-algorithm`       | 否     | 主机密钥算法应为 ssh-dss、ssh-rsa、ecdsa-sha2-nistp256、ecdsa-sha2-nistp384 或 ecdsa-sha2-nistp521    。 仅当存在 `host-key` 时，才是必需的。 |
| `strict-host-key-checking` | 否     | 指示在利用专用 `host-key` 时配置服务器实例是否无法启动。 应为 true（默认值）或 false 。 |

> [!NOTE]
> 如果未指定，Config Server 会采用 `master`（om Git 本身）作为默认标签。 但 GitHub 最近已将默认分支从 `master` 更改为 `main`。 为了避免 Azure Spring Cloud Config Server 故障，在使用 GitHub 设置 Config Server 时，请注意默认标签，尤其是对于新创建的存储库。

-----

### <a name="private-repository-with-basic-authentication"></a>使用基本身份验证的专用存储库

下面列出了用于设置使用基本身份验证的专用 Git 存储库的所有可配置属性。

> [!NOTE]
> 使用连字符 (-) 分隔单词是目前唯一受支持的命名约定。 例如，可使用“default-label”，但不能使用“defaultLabel” 。

| properties        | 必选 | Feature                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | 是    | 用作配置服务器后端的 Git 存储库的 URI 应以“http://”、“https://”、“git@”或“ssh://”开头   。 |
| `default-label` | 否     | Git 存储库的默认标签应为存储库的分支名称、标记名称或 commit-id  。 |
| `search-paths`  | 否     | 用于搜索 Git 存储库子目录的字符串数组。 |
| `username`      | 否     | 用于访问 Git 存储库服务器的用户名，如果 Git 存储库服务器支持 `Http Basic Authentication`，则此用户名是必需的。 |
| `password`      | 否     | 用于访问 Git 存储库服务器的密码或个人访问令牌，如果 Git 存储库服务器支持 `Http Basic Authentication`，则此密码或个人访问令牌是必需的。 |

> [!NOTE]
> 许多 `Git` 存储库服务器都支持对 HTTP 基本身份验证使用令牌，而不支持使用密码。 某些存储库允许令牌无限期保留。 但是，某些 Git 存储库服务器（包括 Azure DevOps Server）会在数小时后强制令牌过期。 导致令牌过期的存储库不应在 Azure Spring Cloud 中使用基于令牌的身份验证。
> Github 已删除对密码身份验证的支持，因此你需要使用个人访问令牌，而不是 Github 的密码身份验证。 有关详细信息，请参阅[令牌身份验证](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)。

### <a name="git-repositories-with-pattern"></a>带模式的 Git 存储库

下面列出了用于设置带模式的 Git 存储库的所有可配置属性。

> [!NOTE]
> 使用连字符 (-) 分隔单词是目前唯一受支持的命名约定。 例如，可使用“default-label”，但不能使用“defaultLabel” 。

| properties                           | 必选         | Feature                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | 否             | 包含具有给定名称的 Git 存储库的设置的图。 |
| `repos."uri"`                      | 在 `repos` 上选择“是” | 用作配置服务器后端的 Git 存储库的 URI 应以“http://”、“https://”、“git@”或“ssh://”开头   。 |
| `repos."name"`                     | 在 `repos` 上选择“是” | 用于标识 Git 存储库的名称，仅当 `repos` 存在时才是必需的。 例如，team-A、team-B 。 |
| `repos."pattern"`                  | 否             | 用于匹配应用程序名称的字符串数组。 对于每个模式，使用带有通配符的 `{application}/{profile}` 格式。 |
| `repos."default-label"`            | 否             | Git 存储库的默认标签应为存储库的分支名称、标记名称或 commit-id  。 |
| `repos."search-paths`"             | 否             | 用于搜索 Git 存储库子目录的字符串数组。 |
| `repos."username"`                 | 否             | 用于访问 Git 存储库服务器的用户名，如果 Git 存储库服务器支持 `Http Basic Authentication`，则此用户名是必需的。 |
| `repos."password"`                 | 否             | 用于访问 Git 存储库服务器的密码或个人访问令牌，如果 Git 存储库服务器支持 `Http Basic Authentication`，则此密码或个人访问令牌是必需的。 |
| `repos."private-key"`              | 否             | 用于访问 Git 存储库的 SSH 私钥，如果 URI 以“git@”或“ssh://”开头，则此私钥是必需的 。 |
| `repos."host-key"`                 | 否             | Git 存储库服务器的主机密钥，不应包含 `host-key-algorithm` 涵盖的算法前缀。 |
| `repos."host-key-algorithm"`       | 否             | 主机密钥算法应为 ssh-dss、ssh-rsa、ecdsa-sha2-nistp256、ecdsa-sha2-nistp384 或 ecdsa-sha2-nistp521    。 仅当存在 `host-key` 时，才是必需的。 |
| `repos."strict-host-key-checking"` | 否             | 指示在利用专用 `host-key` 时配置服务器实例是否无法启动。 应为 true（默认值）或 false 。 |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>将配置服务器存储库附加到 Azure Spring Cloud

将配置文件保存到存储库后，需要将 Azure Spring Cloud 连接到该存储库。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 转到 Azure Spring Cloud 的“概览”页。

3. 在左侧导航窗格中选择“Config Server”。

4. 在“默认存储库”部分，将“URI”设置为“https://github.com/Azure-Samples/piggymetrics-config” 。

5. 选择“验证”。

    ![导航到配置服务器](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

6. 完成验证后，选择“应用”以保存更改。

    ![正在验证配置服务器](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

7. 更新配置可能需要几分钟。

    ![正在更新配置服务器](media/spring-cloud-quickstart-launch-app-portal/updating-config.png)

8. 配置完成后，会收到通知。

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>将存储库信息直接输入到 Azure 门户

#### <a name="default-repository"></a>默认存储库

* **公共存储库**：在“默认存储库”部分的“Uri”框中粘贴存储库 URI 。  将“标签”设置为“配置” 。确保“身份验证”设置为“公共”，然后选择“应用”以完成操作  。

* **专用存储库**：Azure Spring Cloud 支持基本的基于密码/令牌的身份验证和 SSH。

    * **基本身份验证**：在“默认存储库”部分的“Uri”框中，粘贴存储库 URI，然后选择“身份验证”（“铅笔”图标）按钮  。 在“编辑身份验证”窗格的“身份验证类型”下拉列表中选择“HTTP 基本”，然后输入你的用户名和密码/令牌以授权访问 Azure Spring Cloud  。 选择“确定”，然后选择“应用”完成配置服务器实例的设置 。

    ![“编辑身份验证”窗格基本身份验证](media/spring-cloud-tutorial-config-server/basic-auth.png)

    > [!CAUTION]
    > 一些 Git 存储库服务器将个人令牌或访问令牌（例如密码）用于基本身份验证 。 你可以在 Azure Spring Cloud 中使用这种类型的令牌作为密码，因为它将永不过期。 但对于其他 Git 存储库服务器（例如 BitBucket 和 Azure DevOps Server），访问令牌将在一个或两小时后过期。 这意味着，在将这些存储库服务器与 Azure Spring Cloud 一起使用时，此选项是不可行的。
    > Github 已删除对密码身份验证的支持，因此你需要使用个人访问令牌，而不是 Github 的密码身份验证。 有关详细信息，请参阅[令牌身份验证](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)。

    * **SSH**：在“默认存储库”部分的“Uri”框中，粘贴存储库 URI，然后选择“身份验证”（“铅笔”图标）按钮  。 在“编辑身份验证”窗格中的“身份验证类型”下拉列表中，选择“SSH”，然后输入“私钥”   。 （可选）指定“主机密钥”和“主机密钥算法” 。 请确保在配置服务器存储库中包含公钥。 选择“确定”，然后选择“应用”完成配置服务器实例的设置 。

    ![“编辑身份验证”窗格 ssh 身份验证](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>模式存储库

如果要使用可选的“模式存储库”来配置服务，请使用与“默认存储库”相同的方式指定“URI”和“身份验证”。 请确保为模式包含“名称”，然后选择“应用”以将其附加到实例 。

### <a name="enter-repository-information-into-a-yaml-file"></a>将存储库信息输入 YAML 文件

如果已使用存储库设置编写 YAML 文件，则可以将该文件直接从本地计算机导入到 Azure Spring Cloud。 使用基本身份验证的专用存储库的简单 YAML 文件如下所示：

```yaml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

单击“导入设置”按钮，然后从项目目录中选择 YAML 文件。 选择“导入”，然后将弹出“通知”中的 `async` 操作 。 1-2 分钟后，它应报告成功。

![“配置服务器通知”窗格](media/spring-cloud-tutorial-config-server/local-yml-success.png)

YAML 文件中的信息应显示在 Azure 门户中。 选择“应用”以完成操作。

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>使用 Azure Repos 进行 Azure Spring Cloud 配置

Azure Spring Cloud 可以访问公开、由 SSH 保护的，或使用 HTTP 基本身份验证保护的 Git 存储库。 我们将使用最后一个选项，因为它更易于使用 Azure Repos 进行创建和管理。

### <a name="get-repo-url-and-credentials"></a>获取存储库 URL 和凭据

1. 在项目的 Azure Repos 门户中，选择“克隆”按钮：

    ![克隆按钮](media/spring-cloud-tutorial-config-server/clone-button.png)

1. 从文本框中复制克隆 URL。 此 URL 通常采用以下格式：

    ```text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    删除 `https://` 后和 `dev.azure.com` 之前的所有内容，包括 `@`。 生成的 URL 格式应为：

    ```text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    保存此 URL 以便在下一部分中使用。

1. 选择“生成 Git 凭据”。 将显示用户名和密码。 保存这些内容以便在下一部分中使用。

### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>配置 Azure Spring Cloud 以访问 Git 存储库

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 转到 Azure Spring Cloud 的“概览”页。

1. 选择要配置的服务。

1. 在服务页的左窗格中的“设置”下，选择“配置服务器”选项卡 。配置之前创建的存储库：

   - 添加从上一部分中保存的存储库 URL。
   - 选择“身份验证”，然后选择“HTTP 基本” 。
   - “用户名”是从上一部分中保存的用户名。
   - “密码”是从上一部分保存的密码。
   - 选择“应用”，然后等待操作成功完成。

   ![Spring Cloud Config Server](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-configuration"></a>删除配置

可以选择“配置服务器”选项卡中显示的“重置”按钮，以彻底清除现有的设置 。 如果你希望将配置服务器实例连接到另一个源（例如，从 GitHub 移到 Azure DevOps），请删除配置服务器设置。

## <a name="config-server-refresh"></a>配置服务器刷新

更改属性时，需要通知使用这些属性的服务，然后才能进行更改。 Spring Cloud Config 的默认解决方案是手动触发[刷新事件](https://spring.io/guides/gs/centralized-configuration/)。如果有很多应用实例，这可能不可行。 也可在 Azure Spring Cloud 中让配置客户端根据刷新间隔来轮询更改，从而自动刷新配置服务器中的值。

- 首先将 spring-cloud-starter-azure-spring-cloud-client 包括在 pom.xml 的 dependency 节中。

  ```xml
  <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
      <version>2.4.0</version>
  </dependency>
  ```

- 接着在 application.yml 中启用 auto-refresh 并设置适当的刷新间隔。 在此示例中，客户端将每隔 5 秒钟轮询一次配置更改，这是可为刷新间隔设置的最小值。
默认情况下，auto-refresh 设置为 false，refresh-interval 设置为 60 秒。

  ```yml
  spring:
    cloud:
      config:
        auto-refresh: true
        refresh-interval: 5
  ```

- 最后在代码中添加 @refreshScope。 在此示例中，变量 connectTimeout 将每隔 5 秒钟自动刷新一次。

  ```java
  @RestController
  @RefreshScope
  public class HelloController {
      @Value("${timeout:4000}")
      private String connectTimeout;
  }
  ```

## <a name="next-steps"></a>后续步骤

本文介绍了如何启用和配置 Spring Cloud Config Server 实例。 若要详细了解如何管理应用程序，请参阅[在 Azure Spring Cloud 中缩放应用程序](./how-to-scale-manual.md)。
