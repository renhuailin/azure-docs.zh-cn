---
title: Application Insights 的版本批注 | Microsoft 文档
description: 为 Application Insights 中的指标资源管理器图表添加部署或版本标记。
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 776efd56aaa523d1c2621c51cba0446a42bb7411
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461906"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>为 Application Insights 中的指标图表添加批注

注释显示将新版本部署到了何处，或者显示其他重要事件。 使用批注可让轻松查看更改是否对应用程序的性能产生了任何影响。 [Azure Pipelines](/azure/devops/pipelines/tasks/) 生成系统可自动创建批注。 也可以通过 PowerShell 创建批注，用于标记所要处理的任何事件。

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 生成中的版本批注

版本批注是 Azure DevOps 的基于云的 Azure Pipelines 服务功能。

### <a name="install-the-annotations-extension-one-time"></a>安装批注扩展（一次性操作）

若要创建版本批注，必须安装 Visual Studio Marketplace 中提供的多个 Azure DevOps 扩展中的一个。

1. 登录到 [Azure DevOps](https://azure.microsoft.com/services/devops/) 项目。
   
1. 在 Visual Studio Marketplace 的[版本批注扩展](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)页上选择你的 Azure DevOps 组织，然后选择“安装”将该扩展添加到你的 Azure DevOps 组织。
   
   ![选择 Azure DevOps 组织，然后选择“安装”。](./media/annotations/1-install.png)
   
只需为 Azure DevOps 组织安装该扩展一次。 现在，可为组织中的任何项目配置版本批注。

### <a name="configure-release-annotations"></a>配置版本批注

为每个 Azure Pipelines 发布模板单独创建一个 API 密钥。

1. 登录到 [Azure 门户](https://portal.azure.com)并打开负责监视应用程序的 Application Insights 资源。 或者，如果你没有，请[创建一个新的 Application Insights 资源](./app-insights-overview.md)。
   
1. 打开“API 访问”选项卡并复制 **Application Insights ID**。
   
   ![在“API 访问”下，复制应用程序 ID。](./media/annotations/2-app-id.png)

1. 在另一个浏览器窗口中，打开或创建用于管理 Azure Pipelines 部署的发布模板。
   
1. 添加“添加任务”，然后从菜单中选择“Application Insights 版本批注”任务。
   
   ![选择“添加任务”，然后选择“Application Insights 版本批注”。](./media/annotations/3-add-task.png)

   > [!NOTE]
   > “发布注释”任务目前仅支持基于 Windows 的代理；它不会在 Linux、macOS 或其他类型的代理上运行。
   
1. 在“应用程序 ID”下，粘贴从“API 访问”选项卡复制的 Application Insights ID。
   
   ![粘贴 Application Insights ID](./media/annotations/4-paste-app-id.png)
   
1. 返回 Application Insights 的“API 访问”窗口，选择“创建 API 密钥”。 
   
   ![在“API 访问”选项卡中，选择“创建 API 密钥”。](./media/annotations/5-create-api-key.png)
   
1. 在“创建 API 密钥”窗口中键入说明，选择“编写批注”，然后选择“生成密钥”。 复制新密钥。
   
   ![在“创建 API 密钥”窗口中键入说明，选择“编写批注”，然后选择“生成密钥”。](./media/annotations/6-create-api-key.png)
   
1. 在“发布模板”窗口中的“变量”选项卡上，选择“添加”以创建新 API 密钥的变量定义。

1. 在“名称”下输入 `ApiKey`，然后在“值”下粘贴从“API 访问”选项卡复制的 API 密钥。
   
   ![在“Azure DevOps 变量”选项卡中选择“添加”，将变量命名为 ApiKey，然后在“值”下面粘贴 API 密钥。](./media/annotations/7-paste-api-key.png)
   
1. 在“发布模板”主窗口中选择“保存”以保存模板。


   > [!NOTE]
   > API 密钥的限制在 [REST API 速率限制文档](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits)中进行了说明。

## <a name="view-annotations"></a>查看批注


   > [!NOTE]
   > 发布注释目前在 Application Insights 的“指标”窗格中不可用

现在，每当使用发布模板部署新版本时，就会将批注发送到 Application Insights。 可在以下位置查看注释：

在“使用情况”窗格中，也可以手动创建发布注释：

![条形图的屏幕截图，其中显示了几个小时内的用户访问次数。 发布注释在图表上方显示为绿色复选标记，指示发布发生的时间](./media/annotations/usage-pane.png)

在任何基于日志的工作簿查询中，可视化效果沿 x 轴显示时间。

![工作簿窗格的屏幕截图，其中显示了带注释的基于时序日志的查询](./media/annotations/workbooks-annotations.png)

若要在工作簿中启用注释，请转到“高级设置”，然后选择“显示注释”。

![“高级设置”菜单的屏幕截图，其中的“显示注释”选项突出显示，并且设置旁边有用于启用它的复选标记。](./media/annotations/workbook-show-annotations.png)

选择任一注释标记可打开有关该发布的详细信息，包括请求者、源代码管理分支、发布管道和环境。

## <a name="create-custom-annotations-from-powershell"></a>通过 PowerShell 创建自定义批注
可以使用 GitHub 中的 CreateReleaseAnnotation PowerShell 脚本，通过所需的任何流程创建批注，而无需使用 Azure DevOps。

1. 创建 CreateReleaseAnnotation.ps1 的本地副本：

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
1. 使用上述过程中的步骤获取 Application Insights ID，并通过 Application Insights 的“API 访问”选项卡创建 API 密钥。
   
1. 使用以下代码调用 PowerShell 脚本（请将带尖括号的占位符替换为你自己的值）。 `-releaseProperties` 是可选的。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

可以修改脚本，例如，为以往的内容创建批注。

## <a name="next-steps"></a>后续步骤

* [创建工作项](./diagnostic-search.md#create-work-item)
* [使用 PowerShell 自动化](./powershell.md)

