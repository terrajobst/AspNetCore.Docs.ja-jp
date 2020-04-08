---
title: 'チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す'
author: rick-anderson
description: JavaScript を使用して ASP.NET Core Web API を呼び出す方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78644840"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="390aa-103">チュートリアル: JavaScript を使用して ASP.NET Core Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="390aa-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="390aa-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="390aa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="390aa-105">このチュートリアルでは、[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) を使用して JavaScript で ASP.NET Core Web API を呼び出す方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="390aa-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="390aa-106">ASP.NET Core 2.2 の場合は、2.2 バージョンの「[JavaScript で Web API を呼び出す](xref:tutorials/first-web-api#call-the-web-api-with-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="390aa-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="390aa-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="390aa-107">Prerequisites</span></span>

* <span data-ttu-id="390aa-108">[Web API の作成のチュートリアル](xref:tutorials/first-web-api)を完了していること</span><span class="sxs-lookup"><span data-stu-id="390aa-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="390aa-109">CSS、HTML、JavaScript に関する知識</span><span class="sxs-lookup"><span data-stu-id="390aa-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="390aa-110">JavaScript で Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="390aa-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="390aa-111">このセクションでは、To Do アイテムを作成および管理するためのフォームが含まれる HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="390aa-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="390aa-112">イベント ハンドラーを、ページ上の要素にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="390aa-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="390aa-113">イベント ハンドラーによって、Web API のアクション メソッドに HTTP 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="390aa-114">Fetch API の `fetch` 関数により、各 HTTP 要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="390aa-115">`fetch` 関数からは [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) オブジェクトが返され、このオブジェクトには `Response` オブジェクトとして表された HTTP 応答が含まれます。</span><span class="sxs-lookup"><span data-stu-id="390aa-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="390aa-116">一般的なパターンでは、`Response` オブジェクトに対して `json` 関数を呼び出して、JSON 応答の本文を抽出します。</span><span class="sxs-lookup"><span data-stu-id="390aa-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="390aa-117">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="390aa-118">`fetch` の最も簡単な呼び出しでは、ルートを表す 1 つのパラメーターが受け付けられます。</span><span class="sxs-lookup"><span data-stu-id="390aa-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="390aa-119">`init` オブジェクトである 2 番目のパラメーターは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="390aa-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="390aa-120">`init` は、HTTP 要求を構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="390aa-121">[静的ファイルを提供し](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ように、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="390aa-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="390aa-122">*Startup.cs* の `Configure` メソッドでは、次の強調表示されたコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="390aa-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="390aa-123">プロジェクト ルートに *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="390aa-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="390aa-124">*wwwroot* フォルダー内に *js* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="390aa-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="390aa-125">*index.html* という名前の HTML ファイルを、*wwwroot* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="390aa-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="390aa-126">*index.html* の内容を、次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="390aa-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="390aa-127">*site.js* という名前の JavaScript ファイルを、*wwwroot/js* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="390aa-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="390aa-128">*site.js* の内容を、次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="390aa-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="390aa-129">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="390aa-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="390aa-130">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="390aa-130">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="390aa-131">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="390aa-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="390aa-132">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="390aa-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="390aa-133">以下では、Web API 要求について説明します。</span><span class="sxs-lookup"><span data-stu-id="390aa-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="390aa-134">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="390aa-134">Get a list of to-do items</span></span>

<span data-ttu-id="390aa-135">次のコードでは、HTTP GET 要求が *api/TodoItems* ルートに送信されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="390aa-136">Web API から正常状態コードが返されると、`_displayItems` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="390aa-137">`_displayItems` によって受け入れられた配列パラメーターの各 To Do アイテムが、 **[Edit]** ボタンと **[Delete]** ボタンのあるテーブルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="390aa-138">Web API 要求が失敗した場合は、ブラウザーのコンソールにエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="390aa-139">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="390aa-139">Add a to-do item</span></span>

<span data-ttu-id="390aa-140">次のコードの内容は以下のとおりです。</span><span class="sxs-lookup"><span data-stu-id="390aa-140">In the following code:</span></span>

* <span data-ttu-id="390aa-141">`item` 変数は、To Do アイテムのオブジェクト リテラル表現を構築するために宣言されています。</span><span class="sxs-lookup"><span data-stu-id="390aa-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="390aa-142">フェッチ要求は、次のオプションで構成されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="390aa-143">`method`&mdash; POST HTTP アクション動詞が指定されています。</span><span class="sxs-lookup"><span data-stu-id="390aa-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="390aa-144">`body`&mdash; 要求本文の JSON 表現が指定されています。</span><span class="sxs-lookup"><span data-stu-id="390aa-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="390aa-145">JSON は、`item` に格納されているオブジェクト リテラルを [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 関数に渡すことによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="390aa-146">`headers`&mdash;`Accept` および `Content-Type` の HTTP 要求ヘッダーが指定されています。</span><span class="sxs-lookup"><span data-stu-id="390aa-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="390aa-147">どちらのヘッダーも `application/json` に設定され、それぞれ、受信および送信されるメディアの種類が指定されています。</span><span class="sxs-lookup"><span data-stu-id="390aa-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="390aa-148">HTTP POST 要求が *api/TodoItems* ルートに送信されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="390aa-149">Web API で正常状態コードが返されると、`getItems` 関数が呼び出されて、HTML テーブルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="390aa-150">Web API 要求が失敗した場合は、ブラウザーのコンソールにエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="390aa-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="390aa-151">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="390aa-151">Update a to-do item</span></span>

<span data-ttu-id="390aa-152">To Do アイテムの更新は追加と似ていますが、2 つの大きな違いがあります。</span><span class="sxs-lookup"><span data-stu-id="390aa-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="390aa-153">ルートに、更新するアイテムの一意の識別子がサフィックスとして付けられます。</span><span class="sxs-lookup"><span data-stu-id="390aa-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="390aa-154">たとえば、*api/TodoItems/1* のようになります。</span><span class="sxs-lookup"><span data-stu-id="390aa-154">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="390aa-155">`method` オプションで示されているように、HTTP アクション動詞は PUT です。</span><span class="sxs-lookup"><span data-stu-id="390aa-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="390aa-156">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="390aa-156">Delete a to-do item</span></span>

<span data-ttu-id="390aa-157">To Do アイテムを削除するには、要求の `method` オプションを `DELETE` に設定し、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="390aa-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="390aa-158">Web API のヘルプ ページを生成する方法については、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="390aa-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
