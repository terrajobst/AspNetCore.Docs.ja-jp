---
title: ASP.NET Core のスクリプト タグ ヘルパー
author: rick-anderson
ms.author: riande
description: ASP.NET Core スクリプト タグ ヘルパーの属性と、HTML スクリプト タグの動作拡張時の各属性の役割を示します。
ms.custom: mvc
ms.date: 12/02/2019
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: a037abb6a454e6d06305e7d7f6ecad0c2a0ca717
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652094"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="b0711-103">ASP.NET Core のスクリプト タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="b0711-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="b0711-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b0711-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b0711-105">[スクリプト タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)によって、プライマリまたはフォール バック スクリプト ファイルへのリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b0711-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="b0711-106">通常、プライマリ スクリプト ファイルは [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN) 上にあります。</span><span class="sxs-lookup"><span data-stu-id="b0711-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="b0711-107">スクリプト タグ ヘルパーを使用すると、スクリプト ファイルの CDN と、CDN が使用できない場合のフォールバックを指定できます。</span><span class="sxs-lookup"><span data-stu-id="b0711-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="b0711-108">スクリプト タグ ヘルパーによって、ローカル ホスティングの堅牢性が CDN のパフォーマンスの利点にもたらされます。</span><span class="sxs-lookup"><span data-stu-id="b0711-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="b0711-109">次の Razor マークアップでは、フォールバックを含む `script` 要素が示されています。</span><span class="sxs-lookup"><span data-stu-id="b0711-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="b0711-110">CDN スクリプトの読み込みを延期する場合、`<script>` 要素の [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) 属性を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="b0711-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="b0711-111">スクリプト タグ ヘルパーでは、[asp-fallback-test](#asp-fallback-test) 式を直ちに実行する JavaScript がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b0711-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="b0711-112">CDN スクリプトの読み込みが遅延されている場合、式は失敗します。</span><span class="sxs-lookup"><span data-stu-id="b0711-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="b0711-113">一般的に使用されるスクリプト タグ ヘルパー属性</span><span class="sxs-lookup"><span data-stu-id="b0711-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="b0711-114">スクリプト タグ ヘルパーのすべての属性、プロパティ、メソッドについては、[スクリプト タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0711-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="b0711-115">asp-fallback-test</span><span class="sxs-lookup"><span data-stu-id="b0711-115">asp-fallback-test</span></span>

<span data-ttu-id="b0711-116">フォールバック テストに使用するプライマリ スクリプトで定義されているスクリプト メソッド。</span><span class="sxs-lookup"><span data-stu-id="b0711-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="b0711-117">詳細については、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0711-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="b0711-118">asp-fallback-src</span><span class="sxs-lookup"><span data-stu-id="b0711-118">asp-fallback-src</span></span>

<span data-ttu-id="b0711-119">プライマリ側でエラーが発生した場合にフォールバックするスクリプト タグの URL。</span><span class="sxs-lookup"><span data-stu-id="b0711-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="b0711-120">詳細については、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b0711-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0711-121">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b0711-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
