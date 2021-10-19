---
title: 将 DICOMweb 标准 Api 与 Python 配合使用-Azure 医疗保健 Api
description: 本教程介绍如何将 DICOMweb 标准 Api 与 Python 配合使用。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: c474409b2dfdbca0a921690b1871c7268bde08d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781886"
---
# <a name="using-dicomwebtrade-standard-apis-with-python"></a>将 DICOMWeb &trade; 标准 api 与 Python 配合使用

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本教程使用 Python 演示如何使用 DICOM 服务。

在本教程中，我们将使用下面的 [示例 DCM DICOM 文件](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)。

* blue-circle dcm
* dicom-metadata.csv
* green-square dcm
* red-triangle dcm

 示例 DICOM 文件的文件名、studyUID、seriesUID 和 instanceUID 如下所示：

| 文件 | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|green-square dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

> [!NOTE]
> 其中每个文件代表一个实例，并属于同一学习。 此外，绿色正方形和红色三角形是同一系列的一部分，而蓝色圆圈位于单独的系列中。

## <a name="prerequisites"></a>先决条件

若要使用 DICOMWeb &trade; 标准 api，必须已部署 DICOM 服务的实例。 如果尚未部署 DICOM 服务，请参阅 [使用 Azure 门户部署 dicom 服务](deploy-dicom-services-in-azure.md)。

部署 DICOM 服务的实例后，请检索应用服务的 URL：

1. 登录到 [Azure 门户](https://ms.portal.azure.com/)。
1. 搜索 **最新资源** 并选择 DICOM 服务实例。
1. 复制 DICOM 服务的 **服务 URL** 。 
2. 如果尚未获得令牌，请参阅 [使用 Azure CLI 获取 DICOM 服务的访问令牌](dicom-get-access-token-azure-cli.md)。 

在此代码中，我们将访问公共预览版 Azure 服务。 重要的是，不要) 上传任何私有运行状况信息 (PHI。

## <a name="working-with-the-dicom-service"></a>使用 DICOM 服务

DICOMweb &trade; 标准使用 `multipart/related` 与 DICOM 特定 accept 标头结合使用的 HTTP 请求。 熟悉其他基于 REST 的 Api 的开发人员通常会发现，使用 DICOMweb &trade; 标准非常笨拙。 但是，一旦您已启动并运行，就很容易使用了。 只需稍微熟悉入门即可。

### <a name="import-the-appropriate-python-libraries"></a>导入适当的 Python 库

首先，导入必需的 Python 库。

我们使用同步库选择了实现此示例 `requests` 。 对于异步支持，请考虑使用 `httpx` 或其他异步库。 此外，我们还将从中导入两个支持函数， `urllib3` 以支持处理 `multipart/related` 请求。

此外，我们正在导入 `DefaultAzureCredential` 到 Azure 并获取令牌。

```python
import requests
import pydicom
from pathlib import Path
from urllib3.filepost import encode_multipart_formdata, choose_boundary
from azure.identity import DefaultAzureCredential
```

### <a name="configure-user-defined-variables-to-be-used-throughout"></a>配置要在整个中使用的用户定义变量

将 {} 中的所有变量值替换为你自己的值。 此外，请验证任何构造的变量是否正确。  例如， `base_url` 使用服务 URL 构造，并追加到所使用的 REST API 版本。 DICOM 服务的服务 URL 将为： ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` 。 可以使用 Azure 门户导航到 DICOM 服务并获取服务 URL。 有关版本控制的详细信息，还可以访问 [DICOM 服务文档的 API 版本控制](api-versioning-dicom-service.md) 。 如果使用的是自定义 URL，则需要使用自己的值覆盖该值。

```python
dicom_service_name = "{server-name}"
path_to_dicoms_dir = "{path to the folder that includes green-square.dcm and other dcm files}"

base_url = f"{Service URL}/v{version}"

study_uid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; #StudyInstanceUID for all 3 examples
series_uid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; #SeriesInstanceUID for green-square and red-triangle
instance_uid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; #SOPInstanceUID for red-triangle
```

### <a name="authenticate-to-azure-and-get-a-token"></a>向 Azure 进行身份验证并获取令牌

`DefaultAzureCredential` 使我们可以使用各种方法来获取登录到服务的令牌。 我们将使用 `AzureCliCredential` 获取令牌以登录到服务。 还可以使用其他凭据提供程序，例如 `ManagedIdentityCredential` 和 `EnvironmentCredential` 。 若要使用 AzureCliCredential，必须先从 CLI 登录到 Azure，再运行此代码。  (参阅 [使用 Azure CLI 获取 DICOM 服务的访问令牌](dicom-get-access-token-azure-cli.md) ，以获取详细信息。 ) 或者，只需复制并粘贴从 CLI 登录时检索的令牌即可。

> [!NOTE]
> `DefaultAzureCredential` 返回几个不同的凭据对象。 我们 `AzureCliCredential` 在返回的集合中引用为第五项。 这可能不一致。 如果是这样，则取消注释 `print(credential.credential)` 该行。 这会列出所有项目。 找到正确的索引，并撤回 Python 使用从零开始的索引。

> [!NOTE]
> 如果尚未使用 CLI 登录到 Azure，则会失败。 若要运行此操作，你必须从 CLI 登录到 Azure。 

```python
from azure.identity import DefaultAzureCredential
credential = DefaultAzureCredential()

#print(credential.credentials) # this can be used to find the index of the AzureCliCredential
token = credential.credentials[4].get_token('https://dicom.healthcareapis.azure.com')
bearer_token = f'Bearer {token.token}'
```

### <a name="create-supporting-methods-to-support-multipartrelated"></a>创建支持方法以支持 `multipart\related`

`Requests`库 (和大多数 Python 库) 不会 `multipart\related` 以支持 DICOMweb 的方式使用 &trade; 。 由于存在这些库，因此必须添加几个方法来支持处理 DICOM 文件。

`encode_multipart_related` 使用一组字段 (在 DICOM 情况下，这些库通常是第10部分的 dam 文件) 和可选的用户定义的边界。 它同时返回完整正文，以及可以使用的 content_type。

```python
def encode_multipart_related(fields, boundary=None):
    if boundary is None:
        boundary = choose_boundary()

    body, _ = encode_multipart_formdata(fields, boundary)
    content_type = str('multipart/related; boundary=%s' % boundary)

    return body, content_type
```

### <a name="create-a-requests-session"></a>创建 `requests` 会话

创建一个 `requests` 名为的会话， `client` 该会话将用于与 DICOM 服务通信。


```python
client = requests.session()
```

### <a name="verify-authentication-is-configured-correctly"></a>验证身份验证是否已正确配置

调用 changefeed API 终结点，该终结点将在身份验证成功时返回200。

```python
headers = {"Authorization":bearer_token}
url= f'{base_url}/changefeed'

response = client.get(url,headers=headers)
if (response.status_code != 200):
    print('Error! Likely not authenticated!')
```

## <a name="uploading-dicom-instances-stow"></a> (船舶上传 DICOM 实例) 

以下示例突出显示了持久性 DICOM 文件。

### <a name="store-instances-using-multipartrelated"></a>使用存储实例 `multipart/related`

此示例演示如何上传单个 DICOM 文件，并使用一个 Python 将 DICOM 文件预加载 (为) 内存中的字节数。 通过将一个文件数组传递到的字段参数 `encode_multipart_related` ，可以将多个文件上载到一个 POST 中。 它有时用于在一个完整的系列或研究中上传多个实例。

_详细_

* 路径：。/studies
* 方法：POST
* 标头：
    * Accept： application/dicom + json
    * Content-type：多部分/相关;type = "application/dicom"
    * 授权：持有者 $token "

* 正文：
    * Content-type：每个上传的文件的 application/dicom，由边界值分隔

某些编程语言和工具的行为方式不同。 例如，其中一些要求您定义自己的边界。 对于这种情况，可能需要使用略微修改的 Content-type 标头。 已成功使用以下项。
* Content-type：多部分/相关;type = "application/dicom";边界 = ABCD1234
* Content-type：多部分/相关;边界 = ABCD1234
* 内容类型：多部分/相关

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    rawfile = reader.read()
files = {'file': ('dicomfile', rawfile, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```

### <a name="store-instances-for-a-specific-study"></a>存储特定研究的实例

此示例演示如何将多个 DICOM 文件上传到指定的学习中。 它使用一位 Python 将 DICOM 文件 () 字节预加载到内存中。  

通过将一个文件数组传递到的字段参数 `encode_multipart_related` ，可以将多个文件上载到一个 POST 中。 它有时用于上传完整的系列或研究。 

_详细_
* 路径：。/studies/{study}
* 方法：POST
* 标头：
    * Accept： application/dicom + json
    * Content-type：多部分/相关;type = "application/dicom"
    * 授权：持有者 $token "
* 正文：
    * Content-type：每个上传的文件的 application/dicom，由边界值分隔


```python

filepath_red = Path(path_to_dicoms_dir).joinpath('red-triangle.dcm')
filepath_green = Path(path_to_dicoms_dir).joinpath('green-square.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath_red,'rb') as reader:
    rawfile_red = reader.read()
with open(filepath_green,'rb') as reader:
    rawfile_green = reader.read()  
       
files = {'file_red': ('dicomfile', rawfile_red, 'application/dicom'),
         'file_green': ('dicomfile', rawfile_green, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```
### <a name="store-single-instance-non-standard"></a>将单一实例存储 (非标准) 

下面的代码示例演示如何上传单个 DICOM 文件。 它是一个非标准 API 终结点，可简化在请求正文中发送的二进制字节上传单个文件的方法

_详细_
* 路径：。/studies
* 方法：POST
* 标头：
   *  Accept： application/dicom + json
   *  Content-type： application/dicom
   *  授权：持有者 $token "
* 正文：
    * 包含单个 DICOM 文件作为二进制字节。

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    body = reader.read()

headers = {'Accept':'application/dicom+json', 'Content-Type':'application/dicom', "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
response  # response should be a 409 Conflict if the file was already uploaded in the above request
```

## <a name="retrieve-dicom-instances-wado"></a> (WADO 检索 DICOM 实例) 

以下示例突出显示了检索 DICOM 实例的情况。

### <a name="retrieve-all-instances-within-a-study"></a>检索研究中的所有实例

此示例将检索单个研究中的所有实例。

_详细_
* 路径：。/studies/{study}
* 方法：GET
* 标头：
   * 接受：多部分/相关;type = "application/dicom";传输语法 = *
   * 授权：持有者 $token "

之前上传的所有三个 dcm 文件都属于同一研究，因此响应应返回所有这三个实例。 验证响应的状态代码是否为 "正常" 并返回所有三个实例。

```python
url = f'{base_url}/studies/{study_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="use-the-retrieved-instances"></a>使用检索到的实例

实例以二进制字节的形式进行检索。 您可以循环访问返回的项，并将这些字节转换为类似于文件的，可供读取 `pydicom` 。


```python
import requests_toolbelt as tb
from io import BytesIO

mpd = tb.MultipartDecoder.from_response(response)
for part in mpd.parts:
    # Note that the headers are returned as binary!
    print(part.headers[b'content-type'])
    
    # You can convert the binary body (of each part) into a pydicom DataSet
    #   And get direct access to the various underlying fields
    dcm = pydicom.dcmread(BytesIO(part.content))
    print(dcm.PatientName)
    print(dcm.SOPInstanceUID)
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>检索研究中所有实例的元数据

此请求检索单个研究中所有实例的元数据。

_详细_
* 路径：。/studies/{study}/metadata
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 $token "

之前上传的所有三个 `.dcm` 文件都属于同一项研究，因此响应应返回所有这三个实例的元数据。 验证响应的状态代码是否为 "正常"，以及是否返回了所有元数据。

```python
url = f'{base_url}/studies/{study_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-all-instances-within-a-series"></a>检索序列内的所有实例

此请求检索单个序列中的所有实例。

_详细_
* 路径：。/studies/{study}/series/{series}
* 方法：GET
* 标头：
   * 接受：多部分/相关;type = "application/dicom";传输语法 = *
   * 授权：持有者 $token "

此系列有两个实例 (绿色正方形和红色三角形) ，因此响应应返回这两个实例。 验证响应的状态代码是否为 "正常"，同时返回这两个实例。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-all-instances-in-series"></a>检索序列中所有实例的元数据

此请求检索单个序列内所有实例的元数据。

_详细_
* 路径：。/studies/{study}/series/{series}/metadata
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 $token "

此系列有两个实例 (绿色正方形和红色三角形) ，因此对于这两个实例，响应应返回。 验证响应的状态代码是否为 "正常"，同时返回这两个实例元数据。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>检索一系列研究中的单个实例

此请求检索单个实例。

_详细_
* 路径：。/studies/{study}/series{series}/instances/{instance}
* 方法：GET
* 标头：
   * Accept： application/dicom;传输语法 = *
   * 授权：持有者 $token "

此代码示例只应返回实例红色三角形。 验证响应的状态代码是否为 "正常" 并返回该实例。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
headers = {'Accept':'application/dicom; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>在一系列研究中检索单个实例的元数据

此请求检索单个研究和系列中单个实例的元数据。

_详细_
* 路径：。/studies/{study}/series/{series}/instances/{instance}/metadata
* 方法：GET
* 标头：
  * Accept： application/dicom + json
  * 授权：持有者 $token "

此代码示例只应返回实例红色三角形的元数据。 验证响应的状态代码是否为 "正常" 并返回元数据。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>从单个实例中检索一个或多个帧

此请求从单个实例中检索一个或多个帧。

_详细_
* 路径：。/studies/{study}/series{series}/instances/{instance}/frames/1、2、3
* 方法：GET
* 标头：
   * 授权：持有者 $token "
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1` (默认) 或
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=*` 或
   * `Accept: multipart/related; type="application/octet-stream";`

此代码示例应从红色三角形返回唯一的帧。 验证响应的状态代码是否为 "正常" 并返回该框架。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/frames/1'
headers = {'Accept':'multipart/related; type="application/octet-stream"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

## <a name="query-dicom-qido"></a>查询 DICOM (QIDO) 

在下面的示例中，我们使用其唯一标识符搜索项。 还可以搜索其他属性，如 PatientName。

有关支持的 DICOM 属性，请参阅 [DICOM 一致性声明](dicom-services-conformance-statement.md#supported-search-parameters) 文档。

### <a name="search-for-studies"></a>搜索检查

此请求按 DICOM 属性搜索一个或多个研究。

_详细_
* 路径：。研究?StudyInstanceUID = {学习}
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 $token "

验证响应是否包含一项研究，以及响应代码是否正常。

```python
url = f'{base_url}/studies'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'StudyInstanceUID':study_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series"></a>搜索序列

此请求按 DICOM 属性搜索一个或多个序列。

_详细_
* 路径：。数据?SeriesInstanceUID = {series}
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 $token "

验证响应是否包含一个序列，以及响应代码是否正常。

```python
url = f'{base_url}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series-within-a-study"></a>在研究中搜索系列

此请求按 DICOM 属性在单个研究中搜索一个或多个序列。

_详细_
* 路径：。/studies/{study}/series?SeriesInstanceUID = {series}
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 $token "

验证响应是否包含一个序列，以及响应代码是否正常。

```python
url = f'{base_url}/studies/{study_uid}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances"></a>搜索实例

此请求按 DICOM 属性搜索一个或多个实例。

_详细_
* 路径：。实例?SOPInstanceUID = {instance}
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 $token "

验证响应包括一个实例，并验证响应代码是否正常。

```python
url = f'{base_url}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study"></a>搜索研究中的实例

此请求按 DICOM 属性在单个研究中搜索一个或多个实例。

_详细_
* 路径：。/studies/{study}/instances?SOPInstanceUID = {instance}
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 $token "

验证响应包括一个实例，并验证响应代码是否正常。

```python
url = f'{base_url}/studies/{study_uid}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study-and-series"></a>搜索研究和系列中的实例

此请求在单个研究中搜索一个或多个实例，并按 DICOM 属性搜索单个序列。

_详细_
* 路径：。/studies/{study}/series/{series}/instances?SOPInstanceUID = {instance}
* 方法：GET
* 标头：
   * Accept： application/dicom + json
   * 授权：持有者 $token "

验证响应包括一个实例，并验证响应代码是否正常。

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances'
headers = {'Accept':'application/dicom+json'}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

## <a name="delete-dicom"></a>删除 DICOM

> [!NOTE]
> 删除不属于 DICOM 标准，但为了方便起见，已添加。

如果删除成功，则返回204响应代码。 如果 (s) 的项不存在或已被删除，则返回404响应代码。

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>删除研究和系列中的特定实例

此请求将删除单个研究和单个序列内的单个实例。

_详细_
* 路径：。/studies/{study}/series/{series}/instances/{instance}
* 方法： DELETE
* 标头：
   * 授权：持有者 $token

此请求从服务器中删除红色三角形实例。 如果成功，则响应状态代码不包含任何内容。

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-series-within-a-study"></a>删除学习中的特定系列

此请求将删除单个序列 (以及单个研究中) 的所有子实例。

_详细_
* 路径：。/studies/{study}/series/{series}
* 方法： DELETE
* 标头：
   * 授权：持有者 $token

此代码示例将删除绿色方形实例 (它是从服务器) 中保留的唯一元素。 如果成功，则响应状态代码不是内容。

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-study"></a>删除特定学习

此请求将删除单个研究 (并且) 的所有子系列和实例。

_详细_
* 路径：。/studies/{study}
* 方法： DELETE
* 标头：
   * 授权：持有者 $token

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="next-steps"></a>后续步骤

有关 DICOM 服务的信息，请参阅 

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)
