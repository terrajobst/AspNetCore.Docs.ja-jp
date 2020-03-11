---
title: 'チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す'
author: rick-anderson
description: JavaScript を使用して ASP.NET Core Web API を呼び出す方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644840"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

このチュートリアルでは、[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) を使用して JavaScript で ASP.NET Core Web API を呼び出す方法について説明します。

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.2 の場合は、2.2 バージョンの「[JavaScript で Web API を呼び出す](xref:tutorials/first-web-api#call-the-web-api-with-javascript)」を参照してください。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>必須コンポーネント

* [Web API の作成のチュートリアル](xref:tutorials/first-web-api)を完了していること
* CSS、HTML、JavaScript に関する知識

## <a name="call-the-web-api-with-javascript"></a>JavaScript で Web API を呼び出す

このセクションでは、To Do アイテムを作成および管理するためのフォームが含まれる HTML ページを追加します。 イベント ハンドラーを、ページ上の要素にアタッチします。 イベント ハンドラーによって、Web API のアクション メソッドに HTTP 要求が送信されます。 Fetch API の `fetch` 関数により、各 HTTP 要求が開始されます。

`fetch` 関数からは [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) オブジェクトが返され、このオブジェクトには `Response` オブジェクトとして表された HTTP 応答が含まれます。 一般的なパターンでは、`Response` オブジェクトに対して `json` 関数を呼び出して、JSON 応答の本文を抽出します。 JavaScript により、Web API の応答からの詳細を使ってページが更新されます。

`fetch` の最も簡単な呼び出しでは、ルートを表す 1 つのパラメーターが受け付けられます。 `init` オブジェクトである 2 番目のパラメーターは省略可能です。 `init` は、HTTP 要求を構成するために使用されます。

1. [静的ファイルを提供し](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ように、アプリを構成します。 *Startup.cs* の `Configure` メソッドでは、次の強調表示されたコードが必要です。

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. プロジェクト ルートに *wwwroot* フォルダーを作成します。

1. *wwwroot* フォルダー内に *js* フォルダーを作成します。

1. *index.html* という名前の HTML ファイルを、*wwwroot* フォルダーに追加します。 *index.html* の内容を、次のマークアップに置き換えます。

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. *site.js* という名前の JavaScript ファイルを、*wwwroot/js* フォルダーに追加します。 *site.js* の内容を、次のコードに置き換えます。

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。

1. *Properties\launchSettings.json* を開きます。
1. アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。

このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。 以下では、Web API 要求について説明します。

### <a name="get-a-list-of-to-do-items"></a>To Do アイテムのリストの取得

次のコードでは、HTTP GET 要求が *api/TodoItems* ルートに送信されます。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Web API から正常状態コードが返されると、`_displayItems` 関数が呼び出されます。 `_displayItems` によって受け入れられた配列パラメーターの各 To Do アイテムが、 **[Edit]** ボタンと **[Delete]** ボタンのあるテーブルに追加されます。 Web API 要求が失敗した場合は、ブラウザーのコンソールにエラーが記録されます。

### <a name="add-a-to-do-item"></a>To Do アイテムの追加

次のコードの内容は以下のとおりです。

* `item` 変数は、To Do アイテムのオブジェクト リテラル表現を構築するために宣言されています。
* フェッチ要求は、次のオプションで構成されます。
  * `method`&mdash; POST HTTP アクション動詞が指定されています。
  * `body`&mdash; 要求本文の JSON 表現が指定されています。 JSON は、`item` に格納されているオブジェクト リテラルを [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 関数に渡すことによって生成されます。
  * `headers`&mdash;`Accept` および `Content-Type` の HTTP 要求ヘッダーが指定されています。 どちらのヘッダーも `application/json` に設定され、それぞれ、受信および送信されるメディアの種類が指定されています。
* HTTP POST 要求が *api/TodoItems* ルートに送信されます。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Web API で正常状態コードが返されると、`getItems` 関数が呼び出されて、HTML テーブルが更新されます。 Web API 要求が失敗した場合は、ブラウザーのコンソールにエラーが記録されます。

### <a name="update-a-to-do-item"></a>To Do アイテムの更新

To Do アイテムの更新は追加と似ていますが、2 つの大きな違いがあります。

* ルートに、更新するアイテムの一意の識別子がサフィックスとして付けられます。 たとえば、*api/TodoItems/1* のようになります。
* `method` オプションで示されているように、HTTP アクション動詞は PUT です。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>To Do アイテムの削除

To Do アイテムを削除するには、要求の `method` オプションを `DELETE` に設定し、URL でアイテムの一意の識別子を指定します。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Web API のヘルプ ページを生成する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
