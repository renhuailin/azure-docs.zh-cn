---
title: 在 Azure 自动化中管理 Python 3 包
description: 本文介绍如何在 Azure 自动化中管理 Python 3 包 (预览) 。
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122028"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>在 Azure 自动化中管理 Python 3 包 (预览) 

Azure 自动化允许在 Azure 和 Linux 混合 Runbook 辅助角色上运行 Python 3 runbook (预览) 。 为了帮助简化 runbook，可以使用 Python 包导入所需的模块。 若要导入单个包，请参阅 [导入包](#import-a-package)。 若要导入包含多个包的包，请参阅 [导入具有依赖项的包](#import-a-package-with-dependencies)。 本文介绍如何在 Azure 自动化中管理和使用 Python 3 包 (预览) 。

## <a name="import-a-package"></a>导入包

在自动化帐户中，选择 "**共享资源**" 下的 " **Python 包**"。 选择 " **+ 添加 Python 包**"。

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="&quot;Python 3 包&quot; 页面的屏幕截图在左侧菜单中显示 &quot;Python 3 包&quot; 并突出显示 &quot;python 2&quot; 包。":::

在 " **添加 Python 包** " 页面上，选择 " **python 3** " 作为 **版本**，然后选择要上传的本地包。 包可以是 .whl 或 .tar.gz 文件 。 选择包后，选择 **"确定"** 将其上传。

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="屏幕截图显示 &quot;添加 Python 3 包&quot; 页面，其中选择了上传的 gz 文件。":::

导入包后，它将列在自动化帐户的 "Python 包" 页上的 " **python 3 包 (预览")** 选项卡下。如果需要删除包，请选择该包，然后单击 " **删除**"。

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="导入包后，屏幕截图显示 &quot;Python 3 包&quot; 页面。":::

### <a name="import-a-package-with-dependencies"></a>导入具有依赖项的包

可以导入 Python 3 包及其依赖项，方法是将以下 Python 脚本导入到 Python 3 runbook，然后运行它。

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>将脚本导入 runbook
有关导入 runbook 的信息，请参阅 [从 Azure 门户导入 runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal)。 在运行导入之前，请将文件从 GitHub 复制到门户可以访问的存储。

" **导入 runbook** " 页面默认情况下 runbook 名称与脚本名称匹配。 如果有权访问该字段，则可以更改该名称。 **Runbook type** 可能默认为 **Python 2**。 如果是这样，请确保将其更改为 **Python 3**。

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="屏幕截图显示 Python 3 runbook 导入页。":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>执行 runbook 以导入包和依赖项

创建并发布 runbook 后，运行它以导入包。 有关执行 runbook 的详细信息，请参阅 [在 Azure 自动化中启动 runbook](start-runbooks.md) 。

脚本 (`import_py3package_from_pypi.py`) 需要以下参数。

| 参数 | 说明 |
|---------------|-----------------|
|subscription_id | 自动化帐户的订阅 ID |
| resource_group | 在其中定义了 Automation 帐户的资源组的名称 |
| automation_account | 自动化帐户名称 |
| module_name | 要从中导入的模块的名称 `pypi.org` |

有关使用 runbook 的参数的详细信息，请参阅 [使用 runbook 参数](start-runbooks.md#work-with-runbook-parameters)。

## <a name="use-a-package-in-a-runbook"></a>在 runbook 中使用包

导入包后，可以在 runbook 中使用它。 添加以下代码以列出 Azure 订阅中的所有资源组。

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
