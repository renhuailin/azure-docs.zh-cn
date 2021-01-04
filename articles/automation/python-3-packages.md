---
title: 在 Azure 自动化中管理 Python 3 包
description: 本文介绍如何在 Azure 自动化中管理 Python 3 包 (预览) 。
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734295"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>在 Azure 自动化中管理 Python 3 包 (预览) 

Azure 自动化允许在 Azure 和 Linux 混合 Runbook 辅助角色上运行 Python 3 runbook (预览) 。 为了帮助简化 runbook，可以使用 Python 包导入所需的模块。 本文介绍如何在 Azure 自动化中管理和使用 Python 3 包 (预览) 。

## <a name="import-packages"></a>导入程序包

在自动化帐户中，选择 "**共享资源**" 下的 " **Python 包**"。 选择 " **+ 添加 Python 包**"。

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="&quot;Python 3 包&quot; 页面的屏幕截图在左侧菜单中显示 &quot;Python 3 包&quot; 并突出显示 &quot;python 2&quot; 包。":::

在 " **添加 Python 包** " 页面上，选择 "python 3" 作为 **版本**，然后选择要上传的本地包。 包可以是 .whl 或 .tar.gz 文件 。 选择包后，选择 **"确定"** 将其上传。

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="屏幕截图显示 &quot;添加 Python 3 包&quot; 页面，其中选择了上传的 gz 文件。":::

导入包后，它将列在自动化帐户的 "Python 包" 页上的 " **python 3 包 (预览")** 选项卡下。如果需要删除包，请选择该包，然后单击 " **删除**"。

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="导入包后，屏幕截图显示 &quot;Python 3 包&quot; 页面。":::

## <a name="import-packages-with-dependencies"></a>导入具有依赖项的包

在导入过程中，Azure 自动化不会解析 Python 包的依赖项。 但是，有一种方法可以导入包含其所有依赖项的包。

### <a name="manually-download"></a>手动下载

在安装了 [Python 3.8](https://www.python.org/downloads/release/python-380/) 和 [pip](https://pip.pypa.io/en/stable/) 的 Windows 64 位计算机上，运行以下命令以下载包及其所有依赖项：

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

下载包后，可以将它们导入到自动化帐户中。

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
