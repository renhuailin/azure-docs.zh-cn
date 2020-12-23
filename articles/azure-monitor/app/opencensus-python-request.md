---
title: 使用 OpenCensus Python 在 Azure Application Insights 中跟踪传入请求 | Microsoft Docs
description: 使用 OpenCensus Python 监视 Python 应用的请求调用。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 4b88550ad489607bb66eb737067190d45a466a43
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607069"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>使用 OpenCensus Python 跟踪传入请求

使用 OpenCensus Python 及其各种集成收集传入请求数据。 跟踪发送到基于常用 Web 框架 `django`、`flask` 和 `pyramid` 构建的 Web 应用程序的传入请求数据。 然后，将数据作为 `requests` 遥测发送到 Azure Monitor 下的 Application Insights。

首先，使用最新版 [OpenCensus Python SDK](./opencensus-python.md) 检测 Python 应用程序。

> [!NOTE]
> 本文包含对字词 *黑名单* 的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。

## <a name="tracking-django-applications"></a>跟踪 Django 应用程序

1. 从 [PyPI](https://pypi.org/project/opencensus-ext-django/) 下载并安装 `opencensus-ext-django`，然后使用 `django` 中间件检测应用程序。 系统会跟踪发送到 `django` 应用程序的传入请求。

2. 将 `opencensus.ext.django.middleware.OpencensusMiddleware` 添加到 `settings.py` 文件中的 `MIDDLEWARE` 下。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 确保 AzureExporter 已在 `settings.py` 中的 `OPENCENSUS` 下正确配置。 对于来自不想跟踪的 URL 的请求，请将其添加到 `BLACKLIST_PATHS` 中。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>跟踪 Flask 应用程序

1. 从 [PyPI](https://pypi.org/project/opencensus-ext-flask/) 下载并安装 `opencensus-ext-flask`，然后使用 `flask` 中间件检测应用程序。 系统会跟踪发送到 `flask` 应用程序的传入请求。

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. 你也可通过 `app.config` 配置 `flask` 应用程序。 对于来自不想跟踪的 URL 的请求，请将其添加到 `BLACKLIST_PATHS` 中。

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>跟踪 Pyramid 应用程序

1. 从 [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) 下载并安装 `opencensus-ext-django`，然后使用 `pyramid` 中间件检测应用程序。 系统会跟踪发送到 `pyramid` 应用程序的传入请求。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 可以直接在代码中配置 `pyramid` 中间件。 对于来自不想跟踪的 URL 的请求，请将其添加到 `BLACKLIST_PATHS` 中。

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>跟踪 FastAPI 应用程序

OpenCensus 没有 FastAPI 的扩展。 若要编写自己的 FastAPI 中间件，请完成以下步骤：

1. 以下依赖项是必需的： 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. 添加 [FastAPI 中间件](https://fastapi.tiangolo.com/tutorial/middleware/)。 请确保设置了 span 类型服务器： `span.span_kind = SpanKind.SERVER` 。

3. 运行应用程序。 应自动跟踪对 FastAPI 应用程序所做的调用，并且遥测应该直接记录到 Azure Monitor。

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>后续步骤

* [应用程序映射](./app-map.md)
* [可用性](./monitor-web-app-availability.md)
* [搜索](./diagnostic-search.md)
* [日志（分析）查询](../log-query/log-query-overview.md)
* [事务诊断](./transaction-diagnostics.md)

