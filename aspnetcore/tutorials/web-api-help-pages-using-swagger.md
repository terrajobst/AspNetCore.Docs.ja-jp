---
title: Swagger / OpenAPI を使用する ASP.NET Core Web API のヘルプ ページ
author: rsuter
description: このチュートリアルでは、Swagger を追加して、Web API アプリのドキュメントとヘルプ ページを生成する手順を説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 09/20/2018
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 06a942ca213c7c0a58932494be0ed23cb6896d48
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856087"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="23979-103">Swagger/OpenAPI を使用する ASP.NET Core Web API のヘルプ ページ</span><span class="sxs-lookup"><span data-stu-id="23979-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="23979-104">作成者: [Christoph Nienaber](https://twitter.com/zuckerthoben) および [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="23979-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="23979-105">Web API を使用する場合、さまざまなメソッドを理解することは開発者にとって困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="23979-105">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="23979-106">[Swagger](https://swagger.io/) ([OpenAPI](https://www.openapis.org/) とも呼ばれる) では、Web API の役立つドキュメントとヘルプ ページの生成に関する問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="23979-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="23979-107">Swagger では、対話型のドキュメント、クライアント SDK の生成、API の発見可能性などの利点を提供します。</span><span class="sxs-lookup"><span data-stu-id="23979-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="23979-108">この記事では、[Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) と [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger の実装を示します。</span><span class="sxs-lookup"><span data-stu-id="23979-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="23979-109">**Swashbuckle.AspNetCore** は、ASP.NET Core Web API の Swagger ドキュメントを生成するためのオープン ソース プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="23979-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="23979-110">**NSwag** は、Swagger ドキュメントを生成し、[Swagger UI](https://swagger.io/swagger-ui/) や [ReDoc](https://github.com/Rebilly/ReDoc) を ASP.NET Core Web API に統合するためのもう 1 つのオープン ソース プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="23979-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="23979-111">また、NSwag によって、API 用に C# と TypeScript のクライアント コードを生成するための手段が提供されます。</span><span class="sxs-lookup"><span data-stu-id="23979-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="23979-112">Swagger / OpenAPI とは</span><span class="sxs-lookup"><span data-stu-id="23979-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="23979-113">Swagger は、[REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API を記述するために、言語に関係なく使える仕様です。</span><span class="sxs-lookup"><span data-stu-id="23979-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="23979-114">Swagger プロジェクトは、現在、OpenAPI と呼ばれている [OpenAPI イニシアティブ](https://www.openapis.org/)に寄贈されました。</span><span class="sxs-lookup"><span data-stu-id="23979-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="23979-115">どちらの名前も同様に使用できますが、OpenAPI の使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="23979-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="23979-116">Open API では、実装 (ソース コード、ネットワーク アクセス、ドキュメント) に直接アクセスすることなく、コンピューターと人間の両方がサービスの機能を理解できます。</span><span class="sxs-lookup"><span data-stu-id="23979-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="23979-117">関連付けられていないサービスに接続するために必要な作業量を最小限にすることが 1 つの目標です。</span><span class="sxs-lookup"><span data-stu-id="23979-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="23979-118">もう 1 つの目標は、正確にサービスをドキュメント化するために必要な時間を減らすことです。</span><span class="sxs-lookup"><span data-stu-id="23979-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="swagger-specification-swaggerjson"></a><span data-ttu-id="23979-119">Swagger 仕様 (swagger.json)</span><span class="sxs-lookup"><span data-stu-id="23979-119">Swagger specification (swagger.json)</span></span>

<span data-ttu-id="23979-120">Swagger フローの基本は、Swagger 仕様です&mdash;既定では、ドキュメントの名前は *swagger.json* です。</span><span class="sxs-lookup"><span data-stu-id="23979-120">The core to the Swagger flow is the Swagger specification&mdash;by default, a document named *swagger.json*.</span></span> <span data-ttu-id="23979-121">この仕様は、使用しているサービスに基づいて Swagger ツール チェーン (またはサード パーティによるその実装) によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="23979-121">It's generated by the Swagger tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="23979-122">ここでは API の機能と HTTP を使用してアクセスする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="23979-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="23979-123">これにより Swagger UI が駆動され、検出とクライアント コードの生成が行えるように、ツール チェーンによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="23979-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="23979-124">簡略化された Swagger 仕様の例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="23979-124">Here's an example of a Swagger specification, reduced for brevity:</span></span>

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a><span data-ttu-id="23979-125">Swagger UI</span><span class="sxs-lookup"><span data-stu-id="23979-125">Swagger UI</span></span>

<span data-ttu-id="23979-126">[Swagger UI](https://swagger.io/swagger-ui/) では、生成された Swagger 仕様を使用して、サービスに関する情報を提供する Web ベースの UI を提供します。</span><span class="sxs-lookup"><span data-stu-id="23979-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated Swagger specification.</span></span> <span data-ttu-id="23979-127">ミドルウェアの登録呼び出しを使用して ASP.NET Core アプリでホストすることができるように、Swashbuckle と NSwag の両方に埋め込みバージョンの Swagger UI が含まれます。</span><span class="sxs-lookup"><span data-stu-id="23979-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="23979-128">Web UI は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="23979-128">The web UI looks like this:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="23979-130">コントローラー内の各パブリック アクション メソッドを UI からテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="23979-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="23979-131">メソッドの名前をクリックし、セクションを展開します。</span><span class="sxs-lookup"><span data-stu-id="23979-131">Click a method name to expand the section.</span></span> <span data-ttu-id="23979-132">任意の必要なパラメーターを追加し、 **[Try it out!]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="23979-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Swagger GET テストの例](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="23979-134">スクリーンショットに使用される Swagger UI バージョンは、バージョン 2 です。</span><span class="sxs-lookup"><span data-stu-id="23979-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="23979-135">バージョン 3 の例については、[Petstore の例](https://petstore.swagger.io/)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23979-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="23979-136">次の手順</span><span class="sxs-lookup"><span data-stu-id="23979-136">Next steps</span></span>

* [<span data-ttu-id="23979-137">Swashbuckle の概要</span><span class="sxs-lookup"><span data-stu-id="23979-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="23979-138">NSWag の概要</span><span class="sxs-lookup"><span data-stu-id="23979-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
