---
title: ASP.NET Core のスクリプト タグ ヘルパー
author: rick-anderson
ms.author: riande
description: ASP.NET Core スクリプト タグ ヘルパーの属性と、HTML スクリプト タグの動作拡張時の各属性の役割を示します。
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 5f2fb8a45048804afa8aff2989cd53489e45a33b
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256466"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="e56a2-103">ASP.NET Core のスクリプト タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="e56a2-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="e56a2-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e56a2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e56a2-105">[スクリプト タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)によって、プライマリまたはフォール バック スクリプト ファイルへのリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e56a2-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="e56a2-106">通常、プライマリ スクリプト ファイルは [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN) 上にあります。</span><span class="sxs-lookup"><span data-stu-id="e56a2-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="e56a2-107">スクリプト タグ ヘルパーを使用すると、スクリプト ファイルの CDN と、CDN が使用できない場合のフォールバックを指定できます。</span><span class="sxs-lookup"><span data-stu-id="e56a2-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="e56a2-108">スクリプト タグ ヘルパーによって、ローカル ホスティングの堅牢性が CDN のパフォーマンスの利点にもたらされます。</span><span class="sxs-lookup"><span data-stu-id="e56a2-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="e56a2-109">次の Razor マークアップでは、ASP.NET Core Web アプリ テンプレートを使用して作成されたレイアウト ファイルの `script` 要素を示します。</span><span class="sxs-lookup"><span data-stu-id="e56a2-109">The following Razor markup shows the `script` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

<span data-ttu-id="e56a2-110">次に示すものは、前のコードからレンダリングされた HTML に似ています (非開発環境)。</span><span class="sxs-lookup"><span data-stu-id="e56a2-110">The following is similar to the rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

<span data-ttu-id="e56a2-111">前のコードでは、スクリプト タグ ヘルパーによって 2 番目のスクリプト (`<script>  (window.jQuery || document.write(`) 要素が生成されました。これは `window.jQuery` についてテストします。</span><span class="sxs-lookup"><span data-stu-id="e56a2-111">In the preceding code, the Script Tag Helper generated the second script ( `<script>  (window.jQuery || document.write(`) element, which tests for `window.jQuery`.</span></span> <span data-ttu-id="e56a2-112">`window.jQuery` が見つからない場合は、`document.write(` が実行し、スクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="e56a2-112">If `window.jQuery` is not found, `document.write(` runs and creates a script</span></span> 

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="e56a2-113">一般的に使用されるスクリプト タグ ヘルパー属性</span><span class="sxs-lookup"><span data-stu-id="e56a2-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="e56a2-114">スクリプト タグ ヘルパーのすべての属性、プロパティ、メソッドについては、[スクリプト タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e56a2-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="e56a2-115">href</span><span class="sxs-lookup"><span data-stu-id="e56a2-115">href</span></span>

<span data-ttu-id="e56a2-116">リンクされたリソースの優先アドレス。</span><span class="sxs-lookup"><span data-stu-id="e56a2-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="e56a2-117">このアドレスは、生成された HTML に常に渡されます。</span><span class="sxs-lookup"><span data-stu-id="e56a2-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="e56a2-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="e56a2-118">asp-fallback-href</span></span>

<span data-ttu-id="e56a2-119">プライマリ URL が失敗した場合にフォールバックする CSS スタイルシートの URL。</span><span class="sxs-lookup"><span data-stu-id="e56a2-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="e56a2-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="e56a2-120">asp-fallback-test-class</span></span>

<span data-ttu-id="e56a2-121">フォールバック テストで使用するためにスタイルシートに定義されているクラス名。</span><span class="sxs-lookup"><span data-stu-id="e56a2-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="e56a2-122">詳細については、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e56a2-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="e56a2-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="e56a2-123">asp-fallback-test-property</span></span>

<span data-ttu-id="e56a2-124">フォールバック テストで使用する CSS プロパティ名。</span><span class="sxs-lookup"><span data-stu-id="e56a2-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="e56a2-125">詳細については、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e56a2-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="e56a2-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="e56a2-126">asp-fallback-test-value</span></span>

<span data-ttu-id="e56a2-127">フォールバック テストで使用する CSS プロパティ値。</span><span class="sxs-lookup"><span data-stu-id="e56a2-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="e56a2-128">詳細については、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e56a2-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="e56a2-129">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="e56a2-129">asp-fallback-test-value</span></span>

<span data-ttu-id="e56a2-130">フォールバック テストで使用する CSS プロパティ値。</span><span class="sxs-lookup"><span data-stu-id="e56a2-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="e56a2-131">詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e56a2-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="e56a2-132">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e56a2-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
