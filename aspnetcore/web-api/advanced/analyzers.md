---
title: Web API アナライザーを使用する
author: pranavkm
description: ASP.NET Core MVC Web API アナライザー パッケージについて説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
uid: web-api/advanced/analyzers
ms.openlocfilehash: 7b6a7328deb8718a2a1c67c104cec359a4f13497
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082522"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="4dfc1-103">Web API アナライザーを使用する</span><span class="sxs-lookup"><span data-stu-id="4dfc1-103">Use web API analyzers</span></span>

<span data-ttu-id="4dfc1-104">ASP.NET Core 2.2 以降には、Web API プロジェクトでの使用を目的とした MVC アナライザー パッケージが用意されています。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="4dfc1-105">アナライザーは、[Web API 規約](xref:web-api/advanced/conventions)の設定中に <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> の注釈が付けられたコントローラーで動作します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="4dfc1-106">アナライザー パッケージでは、次のようなコントローラーのアクションが通知されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="4dfc1-107">宣言されていない状態コードを返す。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="4dfc1-108">宣言されていない成功の結果を返す。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="4dfc1-109">返されない状態コードを文書化する。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="4dfc1-110">明示的なモデル検証チェックを含める。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="4dfc1-111">アナライザー パッケージを参照する</span><span class="sxs-lookup"><span data-stu-id="4dfc1-111">Reference the analyzer package</span></span>

<span data-ttu-id="4dfc1-112">ASP.NET Core 3.0 以降、アナライザーは .NET Core SDK に含まれています。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="4dfc1-113">プロジェクトでアナライザーを有効にするには、`IncludeOpenAPIAnalyzers` プロパティをプロジェクト ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="4dfc1-114">パッケージ インストール</span><span class="sxs-lookup"><span data-stu-id="4dfc1-114">Package installation</span></span>

<span data-ttu-id="4dfc1-115">次のいずれかの方法を使用して、[Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4dfc1-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4dfc1-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4dfc1-117">**[パッケージ マネージャー コンソール]** ウィンドウから:</span><span class="sxs-lookup"><span data-stu-id="4dfc1-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="4dfc1-118">**[表示]** >  **[その他のウィンドウ]** > **[パッケージ マネージャー コンソール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-118">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="4dfc1-119">*ApiConventions.csproj* ファイルが存在するディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="4dfc1-120">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4dfc1-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4dfc1-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4dfc1-122">**[Solution Pad]** > **[パッケージを追加]** で *[パッケージ]* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="4dfc1-123">**[パッケージを追加]** ウィンドウの **[ソース]** ドロップダウンを "nuget.org" に設定します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="4dfc1-124">検索ボックスに「Microsoft.AspNetCore.Mvc.Api.Analyzers」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="4dfc1-125">結果ウィンドウから "Microsoft.AspNetCore.Mvc.Api.Analyzers" パッケージを選択して、 **[パッケージを追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4dfc1-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4dfc1-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4dfc1-127">**統合端末**からから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-127">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4dfc1-128">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4dfc1-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4dfc1-129">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="4dfc1-130">Web API 規則のアナライザー</span><span class="sxs-lookup"><span data-stu-id="4dfc1-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="4dfc1-131">OpenAPI ドキュメントには、アクションによって返される可能性のある状態コードと応答の種類が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="4dfc1-132">ASP.NET Core MVC では、<xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> や <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> などの属性がアクションの文書化に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="4dfc1-133">Web API の文書化の詳細については、<xref:tutorials/web-api-help-pages-using-swagger> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="4dfc1-134">パッケージのいずれかのアナライザーが、<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> の注釈が付けられたコントローラーを検査し、応答全体を文書化していないアクションを特定します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="4dfc1-135">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="4dfc1-136">前のアクションでは、HTTP 200 の成功の戻り値の型は文書化していますが、HTTP 404 のエラー状態コードは文書化していません。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="4dfc1-137">アナライザーは、HTTP 404 状態コードの文書化の不備を警告として報告します。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="4dfc1-138">問題を解決するためのオプションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc1-138">An option to fix the problem is provided.</span></span>

![警告を報告するアナライザー](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="4dfc1-140">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4dfc1-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
