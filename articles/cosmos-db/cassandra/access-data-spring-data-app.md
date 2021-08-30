---
title: 如何将 Spring Data Apache Cassandra API 用于 Azure Cosmos DB
description: 了解如何将 Spring Data Apache Cassandra API 用于 Azure Cosmos DB。
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: how-to
ms.date: 07/17/2021
ms.openlocfilehash: 92f9045891aa56589e13d887dfea6c3157292185
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778780"
---
# <a name="how-to-use-spring-data-apache-cassandra-api-with-azure-cosmos-db"></a>如何将 Spring Data Apache Cassandra API 用于 Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

本文演示了如何创建一个示例应用程序，该应用程序使用 [Spring Data] 通过 [Azure Cosmos DB Cassandra API](/azure/cosmos-db/cassandra-introduction) 存储和检索信息。

## <a name="prerequisites"></a>先决条件

为完成本文介绍的步骤，需要满足以下先决条件：

* Azure 订阅；如果没有 Azure 订阅，可激活 [MSDN 订阅者权益]或注册[免费的 Azure 帐户]。
* 一个受支持的 Java 开发工具包 (JDK)。 有关在 Azure 上进行开发时可供使用的 JDK 的详细信息，请参阅 [Azure 和 Azure Stack 上的 Java 支持](/azure/developer/java/fundamentals/java-support-on-azure)。
* [Apache Maven](http://maven.apache.org/) 3.0 或更高版本。
* 用来测试功能的 [Curl](https://curl.haxx.se/) 或类似的 HTTP 实用工具。
* [Git](https://git-scm.com/downloads) 客户端。

> [!NOTE]
> 下面所述的示例实现了自定义扩展，以便在使用 Azure Cosmos DB Cassandra API 时获得更好的体验。 它们包括自定义重试和负载均衡策略，以及实现推荐的连接设置。 要更全面地了解如何使用自定义策略，请参阅[版本 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample) 和[版本 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample-v4) 的 Java 示例。 

## <a name="create-a-cosmos-db-cassandra-api-account"></a>创建 Cosmos DB Cassandra API 帐户

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="configure-the-sample-application"></a>配置示例应用程序

以下过程配置测试性应用程序。

1. 打开命令 shell 并克隆以下示例之一：

   对于 Java [版本 3 驱动程序](https://github.com/datastax/java-driver/tree/3.x)和相应的 Spring 版本：

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v3.git
   ```
   
   对于 Java [版本 4 驱动程序](https://github.com/datastax/java-driver/tree/4.x)和相应的 Spring 版本：

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v4.git
   ```     

    > [!NOTE]    
    > 尽管下面描述的用法对于上面的 Java 版本 3 和版本 4 示例是相同的，但为了包括自定义重试和负载均衡策略，它们的实现方式是不同的。 如果对现有的 Spring java 应用程序进行更改，我们建议查看代码以了解如何实现自定义策略。  

1. 在示例项目的 *resources* 目录中找到 *application.properties* 文件，或者创建此文件（若此文件尚不存在）。

1. 在文本编辑器中打开 *application.properties* 文件，在文件中添加或配置以下行，并将示例值替换为上文中的相应值：

   ```yaml
   spring.data.cassandra.contact-points=<Account Name>.cassandra.cosmos.azure.com
   spring.data.cassandra.port=10350
   spring.data.cassandra.username=<Account Name>
   spring.data.cassandra.password=********
   ```

   其中：

   | 参数 | 描述 |
   |---|---|
   | `spring.data.cassandra.contact-points` | 指定本文上文中所述的 **接触点**。 |
   | `spring.data.cassandra.port` | 指定本文上文中所述的 **端口**。 |
   | `spring.data.cassandra.username` | 指定本文上文中所述的 **用户名**。 |
   | `spring.data.cassandra.password` | 指定本文上文中所述的 **主要密码**。 |

1. 保存并关闭 application.properties 文件。

## <a name="package-and-test-the-sample-application"></a>打包并测试示例应用程序 

浏览到 .pom 文件所在目录，以便生成并测试应用程序。

1. 使用 Maven 生成示例应用程序，例如：

   ```shell
   mvn clean package
   ```

1. 启动示例应用程序；例如：

   ```shell
   java -jar target/spring-data-cassandra-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. 在命令提示符下使用 `curl` 创建新记录，如以下示例所示：

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   你的应用程序应返回如下所示的值：

   ```shell
   Added Pet{id=60fa8cb0-0423-11e9-9a70-39311962166b, name='dog', species='canine'}.

   Added Pet{id=72c1c9e0-0423-11e9-9a70-39311962166b, name='cat', species='feline'}.
   ```

1. 在命令提示符下使用 `curl` 检索所有现有记录，如以下示例所示：

   ```shell
   curl -s http://localhost:8080/pets
   ```

   你的应用程序应返回如下所示的值：

   ```json
   [{"id":"60fa8cb0-0423-11e9-9a70-39311962166b","name":"dog","species":"canine"},{"id":"72c1c9e0-0423-11e9-9a70-39311962166b","name":"cat","species":"feline"}]
   ```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

若要了解有关 Spring 和 Azure 的详细信息，请继续访问“Azure 上的 Spring”文档中心。

> [!div class="nextstepaction"]
> [Azure 上的 Spring](../../index.yml)

### <a name="additional-resources"></a>其他资源

有关如何将 Azure 与 Java 配合使用的详细信息，请参阅[面向 Java 开发人员的 Azure] 和[使用 Azure DevOps 和 Java]。

<!-- URL List -->

[面向 Java 开发人员的 Azure]: ../index.yml
[免费的 Azure 帐户]: https://azure.microsoft.com/pricing/free-trial/
[使用 Azure DevOps 和 Java]: /azure/devops/
[MSDN 订阅者权益]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/access-data-spring-data-app/create-cosmos-db-01.png
[COSMOSDB02]: media/access-data-spring-data-app/create-cosmos-db-02.png
[COSMOSDB03]: media/access-data-spring-data-app/create-cosmos-db-03.png
[COSMOSDB04]: media/access-data-spring-data-app/create-cosmos-db-04.png
[COSMOSDB05]: media/access-data-spring-data-app/create-cosmos-db-05.png
[COSMOSDB05-1]: media/access-data-spring-data-app/create-cosmos-db-05-1.png
[COSMOSDB06]: media/access-data-spring-data-app/create-cosmos-db-06.png