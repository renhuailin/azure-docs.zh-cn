---
title: 将依赖项和第三方库引入 Azure Functions
description: 了解如何引入文件或第三方库
ms.date: 4/6/2020
ms.topic: article
zone_pivot_groups: bring-third-party-dependency-programming-functions
ms.openlocfilehash: 3cd1139e8c733650acc879bf1d0d5d9efb2f6fb4
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109716323"
---
# <a name="bring-dependencies-or-third-party-library-to-azure-functions"></a>将依赖项或第三方库引入 Azure Functions

本文介绍如何将第三方依赖项引入函数应用。 第三方依赖项示例为 json 文件、二进制文件和机器学习模型。 

在本文中，学习如何：
> [!div class="checklist"]
> * 通过 Functions Code 项目引入依赖项 
::: zone pivot="programming-language-python"
> [!div class="checklist"]
> * 通过装载 Azure Fileshare 引入依赖项
::: zone-end

## <a name="bring-in-dependencies-from-the-project-directory"></a>从项目目录引入依赖项
::: zone pivot="programming-language-python"
引入依赖项的最简单方法之一是将文件/项目与函数应用代码一起放入函数项目目录结构。 以下示例是 Python 函数项目中的目录示例：
```
<project_root>/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - dependencies/
 | | - dependency1
 | - .funcignore
 | - host.json
 | - local.settings.json
```
通过将依赖项放入函数应用项目目录中的文件夹，依赖项文件夹将与代码一起部署。 因此，函数代码可以通过文件系统 API 访问云中的依赖项。 

### <a name="accessing-the-dependencies-in-your-code"></a>访问代码中的依赖项

以下举例说明了如何访问和执行放入 ```<project_root>/ffmpeg_lib``` 目录中的 ```ffmpeg``` 依赖项。 


```python
import logging

import azure.functions as func
import subprocess

FFMPEG_RELATIVE_PATH = "../ffmpeg_lib/ffmpeg"

def main(req: func.HttpRequest,
         context: func.Context) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    # context.function_directory returns the current directory in which functions is executed 
    ffmpeg_path = "/".join([str(context.function_directory), FFMPEG_RELATIVE_PATH])
    
    try:
        byte_output  = subprocess.check_output([ffmpeg_path, command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```
>[!NOTE]
> 可能需使用 `chmod` 为 Linux 环境中的 ffmpeg 二进制文件提供 `Execute` 权限
::: zone-end

::: zone pivot="programming-language-java"
引入依赖项的最简单方法之一是将文件/项目与函数应用代码一起放入函数项目目录结构。 以下示例是 Java 函数项目中的目录示例：
```
<project_root>/
 | - src/
 | | - main/java/com/function
 | | | - Function.java
 | | - test/java/com/function
 | - artifacts/
 | | - dependency1
 | - host.json
 | - local.settings.json
 | - pom.xml
```
特别是对于 java，需在复制资源时专门将项目添加到 build/target 文件夹。 以下示例介绍如何在 Maven 中执行此操作：

```xml
...
<execution>
    <id>copy-resources</id>
    <phase>package</phase>
    <goals>
        <goal>copy-resources</goal>
    </goals>
    <configuration>
        <overwrite>true</overwrite>
        <outputDirectory>${stagingDirectory}</outputDirectory>
        <resources>
            <resource>
                <directory>${project.basedir}</directory>
                <includes>
                    <include>host.json</include>
                    <include>local.settings.json</include>
                    <include>artifacts/**</include>
                </includes>
            </resource>
        </resources>
    </configuration>
</execution>
...
```
通过将依赖项放入函数应用项目目录中的文件夹，依赖项文件夹将与代码一起部署。 因此，函数代码可以通过文件系统 API 访问云中的依赖项。 

### <a name="accessing-the-dependencies-in-your-code"></a>访问代码中的依赖项

以下举例说明了如何访问和执行放入 ```<project_root>/ffmpeg_lib``` 目录中的 ```ffmpeg``` 依赖项。 


```java
public class Function {
    final static String BASE_PATH = "BASE_PATH";
    final static String FFMPEG_PATH = "/artifacts/ffmpeg/ffmpeg.exe";
    final static String HELP_FLAG = "-h";
    final static String COMMAND_QUERY = "command";

    @FunctionName("HttpExample")
    public HttpResponseMessage run(
            @HttpTrigger(
                name = "req",
                methods = {HttpMethod.GET, HttpMethod.POST},
                authLevel = AuthorizationLevel.ANONYMOUS)
                HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) throws IOException{
        context.getLogger().info("Java HTTP trigger processed a request.");
            
        // Parse query parameter
        String flags = request.getQueryParameters().get(COMMAND_QUERY);
        
        if (flags == null || flags.isBlank()) {
            flags = HELP_FLAG;
        }

        Runtime rt = Runtime.getRuntime();
        String[] commands = { System.getenv(BASE_PATH) + FFMPEG_PATH, flags};
        Process proc = rt.exec(commands);
        
        BufferedReader stdInput = new BufferedReader(new 
        InputStreamReader(proc.getInputStream()));
   
        String out = stdInput.lines().collect(Collectors.joining("\n"));
        if(out.isEmpty()) {
            BufferedReader stdError = new BufferedReader(new 
                InputStreamReader(proc.getErrorStream()));
            out = stdError.lines().collect(Collectors.joining("\n"));
        }
        return request.createResponseBuilder(HttpStatus.OK).body(out).build();

    }
```
>[!NOTE]
> 若要使这段代码在 Azure 中正常运行，需指定自定义应用程序设置“BASE_PATH”，并将值设置为“/home/site/wwwroot”
::: zone-end


::: zone pivot="programming-language-python"
## <a name="bring-dependencies-by-mounting-a-file-share"></a>通过装载文件共享引入依赖项

在 Linux 上运行函数应用时，还可以通过另一种方式引入第三方依赖项。 Functions 允许装载 Azure 文件存储中托管的文件共享。 要将依赖项或项目与应用程序代码分离时，请考虑使用该方法。

首先，需创建 Azure 存储帐户。 在帐户中，还需在 Azure 文件中创建文件共享。 若要创建这些资源，请遵循此[指南](../storage/files/storage-how-to-use-files-portal.md)

创建存储帐户和文件共享后，使用 [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) 命令将文件共享附加到函数应用，如下例所示。

```console
az webapp config storage-account add \
  --name < Function-App-Name > \
  --resource-group < Resource-Group > \
  --subscription < Subscription-Id > \
  --custom-id < Unique-Custom-Id > \
  --storage-type AzureFiles \
  --account-name < Storage-Account-Name > \
  --share-name < File-Share-Name >  \
  --access-key < Storage-Account-AccessKey > \
  --mount-path </path/to/mount>
```



|       标志    |  值     |
| ------------- | ---------------------------------- | 
| custom-id      |  任何唯一字符串 |
| storage-type      |  目前仅支持 AzureFiles |
| share-name      |  现存共享 |
| mount-path     |   容器内可访问共享的路径。 值必须为 `/dir-name` 格式，并且不能以 `/home` 开头 |

可在[此处](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-update)查找更多用于修改/删除文件共享配置的命令


### <a name="uploading-the-dependencies-to-azure-files"></a>将依赖项上传到 Azure 文件存储

可以选择通过 Azure 门户将依赖项上传到 Azure 文件存储。 有关通过门户上传依赖项的说明，请参阅此[指南](../storage/files/storage-how-to-use-files-portal.md#upload-a-file)。 还可以选择通过 [Azure CLI](../storage/files/storage-how-to-use-files-cli.md#upload-a-file) 和 [PowerShell](../storage/files/storage-how-to-use-files-powershell.md#upload-a-file) 将依赖项上传到 Azure 文件存储。


### <a name="accessing-the-dependencies-in-your-code"></a>访问代码中的依赖项

在文件共享中上传依赖项后，可以通过代码访问依赖项。 指定的 mount-path（例如 ```/path/to/mount```）提供了装载的共享。 可以使用文件系统 API 访问目标目录。

以下示例显示了可访问 `ffmpeg` 库的 HTTP 触发器代码，该代码存储在装载的文件共享中。

```python
import logging

import azure.functions as func
import subprocess 

FILE_SHARE_MOUNT_PATH = os.environ['FILE_SHARE_MOUNT_PATH']
FFMPEG = "ffmpeg"

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    try:
        byte_output  = subprocess.check_output(["/".join(FILE_SHARE_MOUNT_PATH, FFMPEG), command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```

将此代码部署到 Azure 中的函数应用时，需[创建应用设置](functions-how-to-use-azure-function-app-settings.md#settings)，并将键名设置为 `FILE_SHARE_MOUNT_PATH`，将值设置为装载的文件共享路径（本例中为 `/azure-files-share`）。 若要进行本地调试，需在 `FILE_SHARE_MOUNT_PATH` 中填充在本地计算机中存储依赖项的文件路径。 以下举例说明了如何使用 `local.settings.json` 设置 `FILE_SHARE_MOUNT_PATH`：

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "FILE_SHARE_MOUNT_PATH" : "PATH_TO_LOCAL_FFMPEG_DIR"
  }
}

```
::: zone-end



## <a name="next-steps"></a>后续步骤

+ [Azure Functions Python 开发人员指南](functions-reference-python.md)
+ [Azure Functions Java 开发人员指南](functions-reference-java.md)
+ [Azure Functions 开发人员参考](functions-reference.md)
