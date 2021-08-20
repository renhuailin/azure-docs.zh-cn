---
title: 模型转换错误代码
description: Azure Object Anchors 服务的模型转换错误代码。
author: jastenze
manager: vriveras
ms.author: jastenze
ms.date: 04/20/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: e47e4e9f784e51c646da22063ee954d9b60a4e11
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202664"
---
# <a name="model-conversion-error-codes"></a>模型转换错误代码

为了反映模型转换失败的常见模式，从 `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionOperation` 中的 `Value` 字段获取的 `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionProperties` 对象包含了一个类型为 `ConversionErrorCode` 的 ErrorCode 字段。 此类型针对错误消息本地化、故障恢复枚举以下常见的失败模式，并为用户提供有关如何更正错误的提示。

| 错误代码                    | 说明                       |  缓解措施                       |
| ---                      | ---                               | ---                               |
| INVALID_ASSET_URI | 找不到启动转换作业时提供的 URI 处的资产。 | 触发资产转换作业时，提供从服务处获取的上传 URI，已将要转换的资产上传到该服务。 |
| INVALID_JOB_ID | 为要创建的资产转换作业提供的 ID 设置为默认的全零 GUID。 | 如果在创建资产转换作业时指定了 GUID，请确保它不是默认的全零 GUID。 |
| INVALID_GRAVITY | 创建资产转换作业时提供的重力矢量是一个完全归零的矢量。 | 开始资产转换时，提供与上传资产对应的重力矢量。 |
| INVALID_SCALE | 提供的比例因子不是非零正值。 | 开始资产转换时，请提供与上传资产的度量单位比例（相对于米）相对应的标量值。 |
| ASSET_SIZE_TOO_LARGE | 通过资产或其序列化等效项生成的中间 .PLY 文件太大。 | 在提交资产进行转换之前，请参阅资产大小准则以确保符合性：aka.ms/aoa/faq |
| ASSET_DIMENSIONS_OUT_OF_BOUNDS | 资产维度超出了物理维度限制。 这可能是创建作业时资产规模设置不当的迹象。 | 在提交资产进行转换之前，请参阅资产大小准则以确保符合性，并确保提供的规模与上传的资产相对应：aka.ms/aoa/faq |
| ZERO_FACES | 通过资产生成的中间 .PLY 文件被判定为不包含面，因此它对于转换无效。 | 确保资产是有效的网格。 |
| INVALID_FACE_VERTICES | 通过资产生成的中间 .PLY 文件包含引用了不存在的顶点的面。 | 确保资产文件的构造有效。 |
| ZERO_TRAJECTORIES_GENERATED | 通过上传的资产生成的相机轨迹为空。 | 在提交资产进行转换之前，请参阅资产准则以确保符合性：aka.ms/aoa/faq |
| TOO_MANY_RIG_POSES | 中间 .PLY 文件中的远程测试机组姿势数量超出了服务限制。 | 在提交资产进行转换之前，请参阅资产大小准则以确保符合性：aka.ms/aoa/faq |
| SERVICE_ERROR | 发生未知服务错误。 | 如果问题仍然存在，请与 Object Anchors 服务团队的成员联系： https://github.com/Azure/azure-object-anchors/issues |
| ASSET_CANNOT_BE_CONVERTED | 提供的资产已损坏、格式错误或无法以其提供的格式进行转换。 | 确保资产是指定类型的有效构造文件，并在提交资产进行转换之前参考资产大小准则以确保符合性：aka.ms/aoa/faq |

在实际资产转换作业之外发生的任何错误都将视为异常并引发。 尤其是，如果服务调用收到不成功的（4xx 或 5xx）或意外 HTTP 响应代码，可能会引发 `Azure.RequestFailedException`。 有关这些异常的更多详细信息，请检查异常的 `Status`、`ErrorCode` 或 `Message` 字段。

| 异常                  | 原因                       |
| ---                      | ---                               |
| ArgumentException |  <ul><li>发生条件：使用构造无效或全零帐户 ID 通过 ObjectAnchorsConversionClient 构造请求。</li><li>发生条件：尝试使用无效的空白帐户域初始化 ObjectAnchorsConversionClient。</li><li>发生条件：通过 ObjectAnchorsConversionClientOptions 向 ObjectAnchorsConversionClient 提供不受支持的服务版本。</li></ul> |
| ArgumentNullException | <ul><li>发生条件：尝试使用无效的空帐户域初始化 ObjectAnchorsConversionClient。</li><li>发生条件：尝试使用无效的空凭据初始化 ObjectAnchorsConversionClient。</li></ul> |
| RequestFailedException | <ul><li>发生条件：因错误的 HTTP 状态代码（与将运行/正在运行/已运行的作业的状态无关）生成的所有其他问题，例如未找到帐户、前端检测到无效的上传 URL、前端服务错误等。</li></ul> |
| UnsupportedAssetFileTypeException | <ul><li>发生条件：尝试使用带有 Azure Object Anchors 转换服务不支持的扩展或指定的 filetype 的资产提交作业。</li></ul> |