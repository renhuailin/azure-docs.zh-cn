---
title: 使用 GitHub Actions 扫描容器映像
description: 了解如何使用容器扫描操作扫描容器映像
author: v-abiss
ms.author: v-abiss
ms.topic: quickstart
ms.reviewer: jukullam
ms.service: azure
ms.date: 05/20/2021
ms.custom: github-actions-azure
ms.openlocfilehash: 984bf8f225c34f34910bc57cb019a75301778360
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113288939"
---
# <a name="scan-container-images-using-github-actions"></a>使用 GitHub Actions 扫描容器映像

通过创建用于生成和扫描容器映像的工作流来开始使用 [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions)。

借助 GitHub Actions，可以生成和扫描映像，以及从工作流将映像推送到公共或专用[容器注册表](https://azure.microsoft.com/en-in/services/container-registry/)，从而加快 CI/CD 过程。

本文使用[GitHub 市场](https://github.com/marketplace) 中的 [容器映像扫描](https://github.com/marketplace/actions/container-image-scan)。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 用于存储生成和推送的所有容器映像的 Azure 容器注册表。 可以从 [Azure 门户创建 Azure 容器注册表](../container-registry/container-registry-get-started-portal.md)。
- 活动存储库的 GitHub 帐户。 如果没有该帐户，请注册[免费版](https://github.com/join)。 
    - 此示例使用 [Java Spring PetClinic 示例应用程序](https://github.com/spring-projects/spring-petclinic)。

## <a name="workflow-file-overview"></a>工作流文件概述

工作流通过存储库的 `/.github/workflows/` 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

此文件包含三个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1. 在 Azure 上创建容器注册表。 <br /> 2.创建 GitHub 机密。 <br /> 3. 使用 GH actions 登录到注册表。 |
|**生成** | 1.设置环境。 <br /> 2. 生成应用。 |
|**生成、扫描映像并将映像推送到容器注册表** | 1. 生成映像。 <br /> 2. 扫描映像。 <br /> 3. 推送映像。|

## <a name="create-github-secrets"></a>创建 GitHub 机密

若要使用 [Azure 容器注册表登录操作](https://github.com/marketplace/actions/azure-container-registry-login)，首先需要将容器注册表详细信息作为机密添加到 GitHub 存储库。

在本例中，将创建三个可用于向 Azure 进行身份验证的机密。  

1. 打开 GitHub 存储库并转到“设置”。

    :::image type="content" source="media/github-action-scan/github-repo-settings.png" alt-text="在导航中选择“设置”。":::

1. 依次选择“机密”和“新建机密” 。

    :::image type="content" source="media/github-action-scan/azure-secret-add.png" alt-text="选择以添加机密。":::

1. 导航到容器注册表中的“访问密钥”，粘贴从 Azure 门户使用以下值创建的每个机密的值。 

    | GitHub 机密名称 | Azure 容器注册表值 |
    |---------|---------|
    |`ACR_LOGIN_SERVER` | **登录服务器** |
    |`REGISTRY_USERNAME` | **用户名** |
    |`REGISTRY_PASSWORD` | **password** |
    
1. 通过选择“添加机密”保存。

## <a name="add-a-dockerfile"></a>添加 Dockerfile

使用以下代码片段创建 `Dockerfile` 并提交到存储库。

```
FROM openjdk:11-jre-stretch
ADD target/spring-petclinic-2.4.2.jar spring-petclinic-2.4.2.jar
EXPOSE 8080
ENTRYPOINT [ "java", "-jar", "spring-petclinic-2.4.2.jar" ]
```


## <a name="use-the-docker-login-action"></a>使用 Docker 登录操作

对[Azure 容器注册表登录操作](https://github.com/marketplace/actions/azure-container-registry-login)使用机密，以对 Azure 进行身份验证。

在此工作流中，使用 Azure 容器注册表登录信息进行身份验证，并将注册表的“登录服务器”、**用户名和“密码”详细信息分别存储在 [docker-login action](https://github.com/Azure/docker-login) 中的 `secrets.ACR_LOGIN_SERVER`、`secrets.REGISTRY_USERNAME`、`secrets.REGISTRY_PASSWORD` 中。 有关如何在工作流文件中引用 GitHub 机密的详细信息，请参阅 GitHub 文档中的[在工作流中使用加密的机密](https://docs.github.com/en/actions/reference/encrypted-secrets#using-encrypted-secrets-in-a-workflow)。


```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest
    steps:
    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
```

## <a name="configure-java"></a>配置 Java

通过 [Java 设置 SDK 操作](https://github.com/marketplace/actions/setup-java-jdk)来设置 Java 环境。 在本例中，将设置环境，使用 Maven 进行生成，然后生成、扫描映像，并将映像推送到容器注册表。

[GitHub 项目](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts)提供一种方法以在作业之间共享工作流中的文件。 

```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest    
    steps:
    - name: Checkout
      uses: actions/checkout@v2    

    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        java-version: '1.8.x'
        
    - name: Build with Maven
      run: mvn package --file pom.xml
```

## <a name="build-your-image"></a>生成映像 

使用以下代码片段在工作流中生成并标记映像。 以下代码片段使用 Docker CLI 在 shell 终端内生成和标记映像。 

```yaml
    - name: Build and Tag image
      run: |
        docker build -f ./Dockerfile -t ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }} .
        
```

## <a name="scan-the-image"></a>扫描映像

将生成的映像推送到容器注册表之前，请确保使用 [Container image scan action](https://github.com/marketplace/actions/container-image-scan) 扫描并检查映像中是否有漏洞。

将以下代码片段添加到工作流中，将扫描映像中是否有“重大漏洞”，使要推送的映像安全并符合标准。

还可通过此操作添加其他输入，或将 `allowedlist.yaml` 文件添加到存储库以忽略任何漏洞和最佳做法检查。 

```yaml
    - name: Scan image
      uses: Azure/container-scan@v0
      with:
        image-name: ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        severity-threshold: CRITICAL
        run-quality-checks: true        
```

示例 `allowedlist.yaml`。

```yaml
general:
  vulnerabilities:
    - CVE-2003-1307
    - CVE-2011-3374
  bestPracticeViolations:
    - CIS-DI-0005
```
## <a name="push-the-image-to-a-private-registry"></a>将映像推送到专用注册表

扫描映像且未发现漏洞后，即可将生成的映像安全推送到专用注册表。 以下代码片段在 shell 终端中使用 Docker CLI 推送映像。

```yaml
    - name: Push image
      run: |
        docker push ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        
```
## <a name="next-steps"></a>后续步骤
- 了解如何[从 Azure 容器注册表部署到 Azure 容器实例](../container-instances/container-instances-using-azure-container-registry.md)。