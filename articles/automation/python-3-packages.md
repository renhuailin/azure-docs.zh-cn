---
title: 在 Azure 自动化中管理 Python 3 程序包
description: 本文介绍了如何在 Azure 自动化中管理 Python 3 程序包（预览版）。
services: automation
ms.subservice: process-automation
ms.date: 06/22/2021
ms.topic: conceptual
ms.openlocfilehash: d986d3ee30641e2a350d3f3838263670e4ce46da
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112515815"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>在 Azure 自动化中管理 Python 3 程序包（预览版）

通过 Azure 自动化可以在 Azure 沙盒环境和 Linux 混合 Runbook 辅助角色上运行 Python 3 runbook（预览版）。 为了帮助简化 runbook，可以使用 Python 包导入所需的模块。 若要导入单个包，请参阅[导入包](#import-a-package)。 若要导入包含多个包的包，请参阅[导入具有依赖项的包](#import-a-package-with-dependencies)。 本文介绍了如何在 Azure 自动化中管理和使用 Python 3 程序包（预览版）。

## <a name="packages-as-source-files"></a>作为源文件的包

Azure 自动化仅支持 Python 包，该 Python 只包含 Python 代码，不包括其他语言的扩展或者用其他语言写成的代码。 然而，Azure 沙盒可能没有所需的 C/C++ 二进制文件编辑器，所以建议使用 [wheel 文件](https://pythonwheels.com/)。 [Python 包索引](https://pypi.org/) (PyPI) 是 Python 编程语言的软件存储库。 从 PyPI 中选择 Python 3 包导入自动化帐户时，请注意以下文件名部分：

| 文件名部分 | 说明 |
|---|---|
|cp38|自动化支持 Python 3.8.x 进行云作业。|
|amd64|Azure 沙盒进程是 Windows 64 位体系结构。|

例如，如果要导入 pandas，可以选择一个名字和 `pandas-1.2.3-cp38-win_amd64.whl` 类似的 wheel 文件。

PyPI 上面的一些 Python 包不提供 wheel 文件。 在这种情况下，下载源文件（.zip 或者.tar.gz 文件）然后使用 `pip` 生成 wheel 文件。 比如，使用已安装 Python 3.8.x 和 wheel 包的 64 位机器执行以下步骤：

1. 下载源文件`pandas-1.2.4.tar.gz`。
1. 运行 pip，通过以下命令获取 wheel 文件 `pip wheel --no-deps pandas-1.2.4.tar.gz`。

## <a name="import-a-package"></a>导入包

在你的自动化帐户中，在“共享资源”下选择“Python 程序包”。 接下来，选择“+ 添加 Python 包”。

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="“Python 3 程序包”页的屏幕截图显示了左侧菜单中的“Python 3 程序包”，并突出显示了“添加 Python 2 程序包”。":::

在“添加 Python 包”页上，选择“Python 3”作为“版本”，然后选择要上传的本地包。 包可以是 .whl 或 .tar.gz 文件 。 选择程序包后，选择“确定”以上传程序包。

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="屏幕截图显示了“添加 Python 3 程序包”页面，其中选择了已上传的 tar.gz 文件。":::

导入包之后，它会列在自动化帐户的 Python 包页面，位于“Python 3 包(预览版)”选项卡的下面。如果需要删除包，请选定包然后选择“删除” 。

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="屏幕截图显示了导入程序包后的“Python 3 程序包”页。":::

### <a name="import-a-package-with-dependencies"></a>导入具有依赖项的包

通过将以下 Python 脚本导入到 Python3 runbook 中，然后运行它，可以导入 Python3 包及其依赖项。

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>将脚本导入 runbook
有关导入 runbook 的信息，请参阅[从 Azure 门户导入 runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal)。 在运行导入之前，请将文件从 GitHub 复制到可供门户访问的存储中。

“导入 runbook”页默认将 runbook 名称设置为与脚本名称匹配。 如果有权访问该字段，则可以更改该名称。 “Runbook 类型”可能默认为“Python 2”。 如果是这样，请确保将其更改为“Python 3”。

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="此屏幕截图显示了 Python 3 runbook 导入页。":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>执行 runbook 以导入包和依赖项

创建并发布 runbook 后，运行它来导入包。 若要详细了解如何执行 runbook，请参阅[在 Azure 自动化中启动 runbook](start-runbooks.md)。

脚本 (`import_py3package_from_pypi.py`) 需要以下参数。

| 参数 | 说明 |
|---------------|-----------------|
|subscription_id | 自动化帐户的订阅 ID |
| resource_group | 在其中定义自动化帐户的资源组的名称 |
| automation_account | 自动化帐户名称 |
| module_name | 要从 `pypi.org` 导入的模块的名称 |

若要详细了解如何在 runbook 中使用参数，请参阅[使用 runbook 参数](start-runbooks.md#work-with-runbook-parameters)。

## <a name="use-a-package-in-a-runbook"></a>在 runbook 中使用包

导入程序包后，可以在 runbook 中使用它。 添加以下代码以列出 Azure 订阅中的所有资源组。

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>后续步骤

要准备 Python runbook，请参阅[创建 Python runbook](learn/automation-tutorial-runbook-textual-python-3.md)。
