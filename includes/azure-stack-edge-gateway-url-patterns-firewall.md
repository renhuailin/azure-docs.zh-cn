---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: a4bb1c7cb2329f729ff8109add19db6842271c5e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827092"
---
|    URL 模式 |    组件或功能  |
|-----------------------------------------------------|-----------------------------------------|
|    https://\*.databoxedge.azure.com/\*<br>https://\*.servicebus.windows.net/\*<br>https://login.microsoftonline.com |    Azure Stack Edge 服务<br>Azure 服务总线<br>身份验证服务 - Azure Active Directory                           |
|    http:\//crl.microsoft.com/pki/\*<br>http:\//www.microsoft.com/pki/\*                                                                                                                                                                                                                                                                                                                                                                                                  |    证书吊销                                                                               |
|    https://\*.core.windows.net/\*<br>https://\*.data.microsoft.com<br>http://\*.msftncsi.com<br>https://www.msftconnecttest.com/connecttest.txt |    Azure 存储帐户和监视 |
|    http:\//windowsupdate.microsoft.com<br>http://\*.windowsupdate.microsoft.com<br>https://\*.windowsupdate.microsoft.com<br>http://\*.update.microsoft.com<br>https://\*.update.microsoft.com<br>http://\*.windowsupdate.com<br>http://download.microsoft.com<br>http://\*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://\*.ws.microsoft.com<br>https://\*.ws.microsoft.com<br>http://\*.mp.microsoft.com |    Microsoft 更新服务器                                                                             |
|    http://\*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                                          |    Akamai CDN                                                                                           |
|    http://\*.data.microsoft.com     |    Windows 中的遥测服务，请参阅update for customer experience and diagnostic telemetry（客户体验和诊断遥测的更新） |
<!--|    http://www.msftconnecttest.com/connecttest.txt  |    用于诊断     ||  |-->   

