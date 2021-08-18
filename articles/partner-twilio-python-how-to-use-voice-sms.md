---
title: 使用 Twilio 实现语音和短信 (Python) | Microsoft Docs
description: 了解如何在 Azure 中使用 Twilio API 服务发起电话呼叫和发送短信。 代码示例是使用 Python 编写的。
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: 7d2539f336da75b2bd2f5151257ecab42d2508f1
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112452631"
---
# <a name="use-twilio-for-voice-and-sms-capabilities-in-python"></a>通过 Python 使用 Twilio 实现语音和 SMS 功能
本文演示如何在 Azure 中使用 Twilio API 服务执行常见编程任务。 所涉及的任务包括发起电话呼叫和发送短信服务 (SMS) 消息。 

有关 Twilio 以及在应用程序中使用语音和短信的详细信息，请参阅[后续步骤](#NextSteps)部分。

## <a name="what-is-twilio"></a><a id="WhatIs"></a>什么是 Twilio？
通过 Twilio，开发人员可以将语音、IP 语音 (VoIP) 和消息传递嵌入应用程序。 开发人员对基于云的全球环境中所需的所有基础结构进行虚拟化，并通过 Twilio API 平台将其公开。 可轻松构建和扩展应用程序。

Twilio 组件包括：

- **Twilio 语音**：使应用程序可以发起和接收电话呼叫。
- **Twilio SMS**：使应用程序能够发送和接收文本消息。
- **Twilio 客户端**：可以从任何手机、平板电脑或浏览器发起 VoIP 呼叫。 支持用于实时通信的 WebRTC 规范。

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio 定价和特惠套餐
Azure 客户在升级 Twilio 帐户后即可获得 10 美元的 Twilio 信用额度的[特惠套餐][special_offer]。 此 Twilio 信用可应用于任何 Twilio 使用情况。 10 美元信用等价于发送多达 1,000 条 SMS 消息或接收长达 1000 分钟的入站语音，具体取决电话号码和消息或呼叫目标的位置。

Twilio 是一种现用现付服务。 没有安装费，并且可以随时关闭帐户。 可以在 [Twilio 定价][twilio_pricing]中找到更多详细信息。

## <a name="concepts"></a><a id="Concepts"></a>概念
Twilio API 是一个为应用程序提供语音和 SMS 功能的 RESTful API。 客户端库有多种语言版本。 有关列表，请参阅 [Twilio API 库][twilio_libraries]。

Twilio API 的关键方面是 Twilio 谓词和 Twilio 标记语言 (TwiML)。

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio 谓词
API 使用如下所示的谓词来告知 Twilio 要执行哪些操作：

* **&lt;Dial&gt;** ：将呼叫方连接到其他电话。
* **&lt;Gather&gt;** ：收集在电话键盘上输入的数字。
* **&lt;Hangup&gt;** ：结束呼叫。
* **&lt;Pause&gt;** ：安静地等待指定的秒数。
* **&lt;Play&gt;** ：播放音频文件。
* &lt;Queue&gt;：添加到呼叫方队列。
* **&lt;Record&gt;** ：录制呼叫方的声音，并返回录音文件的 URL。
* **&lt;Redirect&gt;** ：将对呼叫或短信的控制转让给其他 URL 上的 TwiML。
* **&lt;Reject&gt;** ：拒绝对 Twilio 号码的传入呼叫，且无需付费。
* &lt;Say&gt;：将短信转换为通话语音。
* **&lt;Sms&gt;** ：发送短信。

通过 [Twilio 标记语言文档][twiml]了解其他谓词和功能。

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML 是一组基于 XML 的指令，这些指令以用于告知 Twilio 如何处理呼叫或短信的 Twilio 谓词为基础。

例如，以下 TwiML 将文本 Hello World 转换为语音：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
  <Response>
    <Say>Hello World</Say>
  </Response>
```

当应用程序调用 [Twilio API][twilio_api] 时，某个 API 参数将为返回 TwiML 响应的 URL。 在开发过程中，可以使用 Twilio 提供的 URL 来提供应用程序将使用的 TwiML 响应。 还可以托管自己的 URL 以生成 TwiML 响应，也可以选择使用 `TwiMLResponse` 对象。

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>创建 Twilio 帐户
准备好获取 Twilio 帐户后，请在[试用 Twilio][try_twilio] 上注册。 可以先使用免费帐户，以后再升级帐户。

注册 Twilio 帐户时，将收到帐户安全标识符 (SID) 和身份验证令牌。 需要同时进行 Twilio API 调用。 为了防止对帐户进行未经授权的访问，请保护身份验证令牌。 帐户 SID 和身份验证令牌会显示在 [Twilio 控制台][twilio_console]上标记为“帐户 SID”和“身份验证令牌”的字段中。 

## <a name="create-a-python-application"></a><a id="create_app"></a>创建 Python 应用程序
使用 Twilio 且在 Azure 中运行的 Python 应用程序与任何其他使用 Twilio 的 Python 应用程序没有任何差别。 虽然 Twilio 服务是基于 REST 的且可通过几种方法从 Python 中调用，但本文将重点介绍如何将 Twilio 服务与 [GitHub 提供的用于 Python 的 Twilio 库][twilio_python]一起使用。 有关使用此库的信息，请参阅 [Twilio Python 库文档][twilio_lib_docs]。

首先，[设置一个新的 Azure Linux VM][azure_vm_setup]充当新 Python Web 应用程序的主机。 虚拟机运行后，需要在公共端口上公开应用程序。

添加传入规则：
  1. 转到[网络安全组][azure_nsg]页面。
  2. 选择与虚拟机对应的网络安全组。
  3. 为端口 80 添加传出规则信息。  请确保允许从任何地址传入呼叫。

设置 DNS 名称标签：
  1. 转到[公共 IP 地址][azure_ips]页面。
  2. 选择与虚拟机对应的公共 IP。
  3. 在“配置”部分中设置“DNS 名称标签”信息。 在此示例中，它类似于 \<your-domain-label\>.centralus.cloudapp.azure.com。

通过 SSH 连接到虚拟机后，可以安装所选的 Web 框架。 在 Python 中，最常见的两个是 [Flask](http://flask.pocoo.org/) 和 [Django](https://www.djangoproject.com)。 只需运行 `pip install` 命令即可安装其中任一个 Web 框架。

请记住，我们已将虚拟机配置为仅允许端口 80 上的流量。 因此，请确保将应用程序配置为使用此端口。

## <a name="configure-your-application-to-use-the-twilio-library"></a><a id="configure_app"></a>将应用程序配置为使用 Twilio 库
可以通过两种方式将应用程序配置为使用用于 Python 的 Twilio 库：

* 使用以下命令将适用于 Python 的 Twilio 库作为 Pip 包安装：
   
  `$ pip install twilio`

* [从 GitHub 下载用于 Python 的 Twilio 库][twilio_python]并进行安装，如下所示：

  `$ python setup.py install`

安装用于 Python 的 Twilio 库后，可以在 Python 文件中它：

`import twilio`

有关详细信息，请参阅 [Twilio GitHub 自述文件](https://github.com/twilio/twilio-python/blob/master/README.md)。

## <a name="make-an-outgoing-call"></a><a id="howto_make_call"></a>发起传出呼叫
下面的示例演示如何发起传出呼叫。 此代码还使用 Twilio 提供的网站返回 TwiML 响应。 将值替换为 `from_number` 和 `to_number` 电话号码。 确保在 `from_number` 运行代码之前验证 Twilio 帐户的电话号码。

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified.
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

> [!IMPORTANT]
> 电话号码的格式应为“+”和国家/地区代码。 例如 `+16175551212`（E.164 格式）。 Twilio 还接受未格式化的美国数字，如 `(415) 555-1212` 和 `415-555-1212`。

此代码使用 Twilio 提供的网站返回 TwiML 响应。 可以改用自己的站点来提供 TwiML 响应。 有关详细信息，请参阅[从自己的网站提供 TwiML 响应](#howto_provide_twiml_responses)。

## <a name="send-an-sms-message"></a><a id="howto_send_sms"></a>发送短信
以下示例演示了如何使用 `TwilioRestClient` 类发送短信。 Twilio 为试用帐户提供了 `from_number` 号码以用于发送短信。 在运行代码前，必须验证 Twilio 帐户的“被呼叫方”`to_number`号码。

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With a trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>从自己的网站提供 TwiML 响应
当应用程序发起对 Twilio API 的调用时，Twilio 会将请求发送到应返回 TwiML 响应的 URL。 上面的示例使用 Twilio 提供的 URL [https://twimlets.com/message][twimlet_message_url]。 

> [!NOTE]
> 虽然 TwiML 专供 Twilio 使用，但可以在浏览器中查看它。 例如，选择 [https://twimlets.com/message][twimlet_message_url] 查看空白的 `<Response>` 元素。 再如，选择 [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] 以查看包含 `<Say>` 元素的 `<Response>` 元素。

可以创建自己的返回 HTTP 响应的网站，而不用依赖 Twilio 提供的 URL。 可以使用任何语言创建返回 XML 响应的站点。 本文假设你将使用 Python 创建 TwiML。

以下示例将输出在呼叫时念出 **Hello World** 的 TwiML 响应。

使用 Flask：

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

使用 Django：

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

如上面的示例中所示，TwiML 响应只是一个 XML 文档。 用于 Python 的 Twilio 库包含将生成 TwiML 的类。 以下示例将生成与上面所示相同的响应，但该响应会使用用于 Python 的 Twilio 库中的 `twiml` 模块：

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

有关 TwiML 的详细信息，请参阅 [TwiML 参考][twiml_reference]。

将 Python 应用程序页设置为提供 TwiML 响应后，请使用应用程序的 URL 作为传入到 `client.calls.create` 方法中的 URL。 例如，如果将名为 *MyTwiML* 的 Web 应用程序部署到 Azure 托管服务，则可以使用其 URL 作为 webhook，如以下示例中所示：

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio Client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="use-additional-twilio-services"></a><a id="AdditionalServices"></a>使用其他 Twilio 服务
除了此处所示的示例之外，Twilio 还提供了基于 Web 的 API，可通过这些 API 从 Azure 应用程序中使用更多的 Twilio 功能。 有关完整详细信息，请参阅 [Twilio API 文档][twilio_api]。

## <a name="next-steps"></a><a id="NextSteps"></a>后续步骤
现在，已了解 Twilio 服务的基础知识，单击下面的链接可以了解详细信息：

* [Twilio 安全准则][twilio_security_guidelines]
* [Twilio 操作方法指南和示例代码][twilio_howtos]
* [Twilio 快速入门教程][twilio_quickstarts]
* [GitHub 上的 Twilio][twilio_on_github]
* [与 Twilio 技术支持交流][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/docs/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/all
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[azure_ips]: ./virtual-network/virtual-network-public-ip-address.md
[azure_vm_setup]: ./virtual-machines/linux/quick-create-portal.md
[azure_nsg]: ./virtual-network/manage-network-security-group.md
