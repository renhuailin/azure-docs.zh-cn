---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/22/2021
ms.author: lajanuar
ms.openlocfilehash: e3296ee84928256e399891b9f8a29318c565e5de
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725505"
---
* 为获得最佳结果，请针对每个文档提供一张清晰的照片或高质量的扫描。
* 支持的文件格式：JPEG、PNG、BMP、TIFF 和 PDF（文本嵌入或扫描式）。 最好是使用文本嵌入式 PDF，这可以避免在提取和定位字符时出错。
* 对于 PDF 和 TIFF，最多可处理 2000 页（对于免费层订阅，仅处理前两页）。
* 文件大小必须小于 50 MB。
* 图像尺寸必须介于 50 x 50 像素与 10000 x 10000 像素之间。
* PDF 尺寸不超过 17 x 17 英寸，相当于法律专用纸或 A3 纸张尺寸或更小。
* 训练数据的总大小不得超过 500 页。
* 如果 PDF 是密码锁定的文件，则必须先删除锁，然后才能提交它们。
* 对于非监督式学习（没有已标记的数据）：
  * 数据必须包含键和值。
  * 键必须显示在值的上方或左侧，不能显示在下方或右侧。
