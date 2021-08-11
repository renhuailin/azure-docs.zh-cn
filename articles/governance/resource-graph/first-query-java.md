---
title: 快速入门：第一个 Java 查询
description: 本快速入门介绍为 Java 启用 Resource Graph Maven 包并运行第一个查询的步骤。
ms.date: 07/09/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 1d6d3dfb309b359e4b999d06671db7dc541d5f32
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459974"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>快速入门：使用 Java 运行你的第一个 Resource Graph 查询

第一步使用 Azure Resource Graph 是为了确保为 Java 安装了所需的 Maven 包。 本快速入门将指导你完成将 Maven 包添加到 Java 安装的过程。

在此过程结束时，你已将包添加到 Java 安装中，并运行你的第一个 Resource Graph 查询。

## <a name="prerequisites"></a>必备条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- 请确保安装最新的 Azure CLI（至少为 2.21.0）。 如果尚未安装，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

  > [!NOTE]
  > 在下面的示例中需要使用 Azure CLI，这样适用于 Java 的 Azure SDK 才能使用基于 CLI 的身份验证。 有关其他选项信息，请参阅[适用于 Java 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)。

- [Java 开发人员工具包](/azure/developer/java/fundamentals/java-support-on-azure)版本
  8.

- [Apache Maven](https://maven.apache.org/) 版本 3.6 或更高版本。

## <a name="create-the-resource-graph-project"></a>创建 Resource Graph 项目

若要使 Java 能够查询 Azure Resource Graph，请创建新的应用程序，并使用 Maven 进行配置，然后安装必要的 Maven 包。

1. 使用 [Maven 原型](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)初始化名为“argQuery”的新 Java 应用程序：

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. 将目录更改为新的项目文件夹 `argQuery`，并在你喜爱的编辑器中打开 `pom.xml`。 在现有 `<dependencies>` 节点下添加以下 `<dependency>` 节点：

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0</version>
    </dependency>
   ```

1. 在 `pom.xml` 文件中，将以下 `<properties>` 节点添加到基本 `<project>` 节点以更新源和目标版本：

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 在该 `pom.xml` 文件中，将以下 `<build>` 节点添加到基本 `<project>` 节点以配置要运行的项目目标和 main 类。

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. 用以下代码替换 `\argQuery\src\main\java\com\Fabrikam` 中默认的 `App.java`，并保存更新的文件：

   ```java
   package com.Fabrikam;

   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;

   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];

           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));

           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);

           QueryResponse response = manager.resourceProviders().resources(queryRequest);

           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. 生成 `argQuery` 控制台应用程序：

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>运行首个 Resource Graph 查询

生成 Java 控制台应用程序后，即可尝试创建简单的 Resource Graph 查询。 该查询返回前五个 Azure 资源，以及每个资源的名称和资源类型 。

在对 `argQuery` 的每次调用中，都包含替换为自己的值时需要使用的变量：

- `{subscriptionId}` - 替换为订阅 ID
- `{query}`：替换为 Azure Resource Graph 查询

1. 使用 Azure CLI 对 `az login` 进行身份验证。

1. 将目录更改为使用上述 `mvn -B archetype:generate` 命令创建的 `argQuery` 项目文件夹。

1. 使用 Maven 运行第一个 Azure Resource Graph 查询，以便编译控制台应用程序并传递参数。 `exec.args` 属性通过空格标识参数。 若要将查询标识为一个参数，请使用单引号引起来 (`'`)。

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 更改 `argQuery.exe` 的参数，并将查询更改为按“名称”属性 `order by`：

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 命令按此顺序执行，首先会限制查询结果，然后对它们进行排序。

1. 将最后一个参数更改为 `argQuery.exe`，并将查询更改为先按 Name 属性应用 `order by`，然后对前五个结果应用 `limit`：

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且按 Name 属性排序，但仍限制为前五个结果。

## <a name="clean-up-resources"></a>清理资源

如果要删除 Java 控制台应用程序和已安装的包，可以通过删除 `argQuery` 项目文件夹来完成。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个带有所需 Resource Graph 包的 Java 控制台应用程序，并运行了第一个查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)
