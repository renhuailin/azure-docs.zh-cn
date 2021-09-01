---
title: Azure Defender 的针对 CI/CD 工作流中容器映像的漏洞扫描程序
description: 了解如何使用适用于容器注册表的 Azure Defender 扫描 CI/CD 工作流中的容器映像
author: memildin
ms.author: memildin
ms.date: 05/25/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: eb7309f067c350eac0d9455767b137377caf588b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736306"
---
# <a name="identify-vulnerable-container-images-in-your-cicd-workflows"></a>识别 CI/CD 工作流中有漏洞的容器映像

本页介绍在你的 GitHub 工作流中构建了基于 Azure 容器注册表的容器映像的情况下，如何使用集成的漏洞扫描来扫描这种容器映像。

若要设置扫描程序，需要启用适用于容器注册表的 Azure Defender 和 CI/CD 集成。 当 CI/CD 工作流将映像推送到注册表时，可以查看注册表扫描结果以及 CI/CD 扫描结果摘要。

CI/CD 扫描的发现是对 Qualys 的现有注册表扫描结果的扩充。 Azure Defender的 CI/CD 扫描由 [Aqua Trivy](https://github.com/aquasecurity/trivy) 提供支持。

你将获取可跟踪性信息，例如 GitHub 工作流和 GitHub 运行 URL，以帮助识别导致易受攻击映像的工作流。

> [!TIP]
> 在注册表扫描中发现的漏洞可能与 CI/CD 扫描的发现结果不同。 造成这些差异的一个原因是注册表扫描是[连续的](defender-for-container-registries-introduction.md#when-are-images-scanned)，而 CI/CD 扫描在工作流将映像推送到注册表之前立即进行。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：| **此 CI/CD 集成处于预览状态。**<br>建议仅在非生产工作流上试验此集成。<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|定价：|适用于容器注册表的 Azure Defender 按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中的定价计费|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/no-icon.png"::: 国家/地区/主权云（Azure 政府、Azure 中国世纪互联）|
|||

## <a name="prerequisites"></a>先决条件

若要在 CI/CD 工作流将映像推送到注册表后即扫描映像，你必须在订阅中启用“适用于容器注册表的 Azure Defender”。 

## <a name="set-up-vulnerability-scanning-of-your-cicd-workflows"></a>设置 CI/CD 工作流的漏洞扫描

在 GitHub 工作流中启用映像的漏洞扫描：

[步骤 1. 在安全中心启用 CI/CD 集成](#step-1-enable-the-cicd-integration-in-security-center)

[步骤 2. 将必要的行添加到 GitHub 工作流](#step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan)

### <a name="step-1-enable-the-cicd-integration-in-security-center"></a>步骤 1。 在安全中心启用 CI/CD 集成

1. 从安全中心的侧栏中，选择“定价和设置”。
1. 选择相关订阅。
1. 从该订阅的设置页的侧栏中，选择“集成”。
1. 在出现的窗格中，选择一个要从你的工作流中推送 CI/CD 扫描结果的 Application Insights 帐户。
1. 将身份验证令牌和连接字符串复制到 GitHub 工作流中。

    :::image type="content" source="./media/defender-for-container-registries-cicd/enable-cicd-integration.png" alt-text="启用 CI/CD 集成以对 GitHub 工作流中的容器映像进行漏洞扫描。" lightbox="./media/defender-for-container-registries-cicd/enable-cicd-integration.png":::

    > [!IMPORTANT]
    > 身份验证令牌和连接字符串用于将所获取的安全遥测与订阅中的资源相关联。 如果你对这些参数使用无效值，将导致遥测丢失。

### <a name="step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan"></a>步骤 2。 将必要的行添加到 GitHub 工作流并执行扫描

1. 在 GitHub 工作流中，按如下所示启用 CI/CD 扫描：

    > [!TIP]
    > 建议在存储库中创建两个机密，以便在 YAML 文件中引用，如下所示。 机密可以按照自己的命名约定进行命名。 在本示例中，将作为 AZ_APPINSIGHTS_CONNECTION_STRING 和 AZ_SUBSCRIPTION_TOKEN 来引用机密。 


    ```yml
    - run: |
      echo "github.sha=$GITHUB_SHA"
      docker build -t githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - uses: Azure/container-scan@v0 
      name: Scan image for vulnerabilities
      id: container-scan
      continue-on-error: true
      with:
        image-name: githubdemo1.azurecr.io/k8sdemo:${{ github.sha }} 
    
    - name: Push Docker image - githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
      run: |
      docker push githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - name: Post logs to appinsights
      uses: Azure/publish-security-assessments@v0
      with: 
        scan-results-path: ${{ steps.container-scan.outputs.scan-report-path }}
        connection-string: ${{ secrets.AZ_APPINSIGHTS_CONNECTION_STRING }}
        subscription-token: ${{ secrets.AZ_SUBSCRIPTION_TOKEN }} 
    ```

1. 运行将映像推送到所选容器注册表的工作流。 将映像推送到注册表后，将运行注册表扫描，你可以在 Azure 安全中心内查看 CI/CD 扫描结果以及注册表扫描结果。

1. [查看 CI/CD 扫描结果](#view-cicd-scan-results)。

## <a name="view-cicd-scan-results"></a>查看 CI/CD 扫描结果

1. 若要查看发现结果，请访问“建议”页面。 如果发现问题，你将看到“应修正 Azure 容器注册表映像中的漏洞”的建议。

    ![修正问题的建议。](media/monitor-container-security/acr-finding.png)

1. 选择建议。 

    “建议详细信息”页随即打开，并且其中包含其他信息。 这些信息包括具有易受攻击映像（“受影响的资源”）的注册表列表以及补救步骤。 

1. 打开“受影响的资源”列表并选择不正常的注册表，以查看其中具有易受攻击映像的存储库。

    :::image type="content" source="media/defender-for-container-registries-cicd/select-registry.png" alt-text="选择不正常的注册表。":::

    “注册表详细信息”页随即打开，并列出受影响的存储库。

1. 选择特定的存储库以查看其中具有易受攻击映像的存储库。

    :::image type="content" source="media/defender-for-container-registries-cicd/select-repository.png" alt-text="选择不正常的存储库。":::

    “存储库详细信息”页随即打开。 它列出了易受攻击的映像以及对发现结果严重性的评估。

1. 选择特定映像以查看漏洞。

    :::image type="content" source="media/defender-for-container-registries-cicd/select-image.png" alt-text="选择不正常的映像。":::

    所选映像的发现结果列表随即打开。

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-scan-results.png" alt-text="映像扫描结果。":::

1. 若要详细了解哪些 GitHub 工作流正在推送这些易受攻击的映像，请选择信息气泡：

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-findings.png" alt-text="有关特定 GitHub 分支和提交项的 CI/CD 发现。":::



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Defender](azure-defender.md)
