---
title: 'チュートリアル: ASP.NET Core を使用して jQuery で Web API を呼び出す'
author: rick-anderson
description: jQuery で ASP.NET Core Web API を呼び出す方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: eb8b2453fd037170a49f531fea4c3ef1c056292d
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602571"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a><span data-ttu-id="6053f-103">チュートリアル: jQuery で ASP.NET Core Web API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="6053f-103">Tutorial: Call an ASP.NET Core web API with jQuery</span></span>

<span data-ttu-id="6053f-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6053f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6053f-105">このチュートリアルでは、jQuery を使用して ASP.NET Core Web API を呼び出す方法について説明します</span><span class="sxs-lookup"><span data-stu-id="6053f-105">This tutorial shows how to call an ASP.NET Core web API with jQuery</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6053f-106">ASP.NET Core 2.2 の場合は、2.2 バージョンの「[jQuery での API の呼び出し](xref:tutorials/first-web-api#call-the-api-with-jquery)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6053f-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the Web API with jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="6053f-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6053f-107">Prerequisites</span></span>

<span data-ttu-id="6053f-108">[Web API の作成のチュートリアル](xref:tutorials/first-web-api)を完了していること</span><span class="sxs-lookup"><span data-stu-id="6053f-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="6053f-109">jQuery での API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="6053f-109">Call the API with jQuery</span></span>

<span data-ttu-id="6053f-110">このセクションでは、jQuery を使用して Web API を呼び出す、HTML ページが追加されます。</span><span class="sxs-lookup"><span data-stu-id="6053f-110">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="6053f-111">jQuery で要求を開始し、API の応答からの詳細を含むページを更新します。</span><span class="sxs-lookup"><span data-stu-id="6053f-111">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="6053f-112">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="6053f-112">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

<span data-ttu-id="6053f-113">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6053f-113">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="6053f-114">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="6053f-114">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="6053f-115">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6053f-115">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

<span data-ttu-id="6053f-116">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="6053f-116">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="6053f-117">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6053f-117">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="6053f-118">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6053f-118">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="6053f-119">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="6053f-119">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="6053f-120">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="6053f-120">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="6053f-121">jQuery を取得するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="6053f-121">There are several ways to get jQuery.</span></span> <span data-ttu-id="6053f-122">前のスニペットでは、ライブラリは CDN から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6053f-122">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="6053f-123">このサンプルでは、API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6053f-123">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="6053f-124">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="6053f-124">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="6053f-125">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="6053f-125">Get a list of to-do items</span></span>

<span data-ttu-id="6053f-126">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 関数は、`GET` 要求を API に送信します。この API は、To Do アイテムの配列を表す JSON を返します。</span><span class="sxs-lookup"><span data-stu-id="6053f-126">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="6053f-127">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6053f-127">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="6053f-128">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="6053f-128">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="6053f-129">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="6053f-129">Add a to-do item</span></span>

<span data-ttu-id="6053f-130">[ajax](https://api.jquery.com/jquery.ajax/) 関数は、要求本文内で To Do アイテムと共に `POST` 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="6053f-130">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="6053f-131">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="6053f-131">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="6053f-132">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="6053f-132">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="6053f-133">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="6053f-133">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="6053f-134">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="6053f-134">Update a to-do item</span></span>

<span data-ttu-id="6053f-135">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="6053f-135">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="6053f-136">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="6053f-136">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="6053f-137">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="6053f-137">Delete a to-do item</span></span>

<span data-ttu-id="6053f-138">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="6053f-138">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

<span data-ttu-id="6053f-139">API ヘルプ ページを生成する方法については、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="6053f-139">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end