---
title: 'チュートリアル: ASP.NET Core を使用して jQuery で Web API を呼び出す'
author: rick-anderson
description: jQuery で ASP.NET Core Web API を呼び出す方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: a319e4b4ce09e9b09afeaff065d5740276deb115
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022555"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a>チュートリアル: jQuery で ASP.NET Core Web API を呼び出す

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

このチュートリアルでは、jQuery を使用して ASP.NET Core Web API を呼び出す方法について説明します

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.2 の場合は、2.2 バージョンの「[jQuery での API の呼び出し](xref:tutorials/first-web-api#call-the-api-with-jquery)」を参照してください。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>必須コンポーネント

* [Web API の作成のチュートリアル](xref:tutorials/first-web-api)を完了していること
* CSS、HTML、JavaScript、jQuery に関する知識

## <a name="call-the-api-with-jquery"></a>jQuery での API の呼び出し

このセクションでは、jQuery を使用して Web API を呼び出す、HTML ページが追加されます。 jQuery で要求を開始し、API の応答からの詳細を含むページを更新します。

*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。

*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。 その内容を次のマークアップに置き換えます。

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。 その内容を次のコードに置き換えます。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。

* *Properties\launchSettings.json* を開きます。
* アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。

jQuery を取得するには、いくつかの方法があります。 前のスニペットでは、ライブラリは CDN から読み込まれます。

このサンプルでは、API のすべての CRUD メソッドを呼び出します。 次に、API の呼び出しについて説明します。

### <a name="get-a-list-of-to-do-items"></a>To Do アイテムのリストの取得

jQuery [ajax](https://api.jquery.com/jquery.ajax/) 関数は、`GET` 要求を API に送信します。この API は、To Do アイテムの配列を表す JSON を返します。 要求が成功した場合、`success` コールバック関数が呼び出されます。 コールバックでは、DOM は To Do 情報で更新されます。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>To Do アイテムの追加

[ajax](https://api.jquery.com/jquery.ajax/) 関数は、要求本文内で To Do アイテムと共に `POST` 要求を送信します。 `accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。 To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。 API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>To Do アイテムの更新

To Do アイテムの更新は、追加操作に似ています。 アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>To Do アイテムの削除

To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

API ヘルプ ページを生成する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end
