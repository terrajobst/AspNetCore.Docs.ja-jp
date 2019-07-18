---
title: ASP.NET Core のフォームのタグ ヘルパー
author: rick-anderson
description: フォームで使用される組み込みのタグ ヘルパーについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/working-with-forms
ms.openlocfilehash: a07bb4f539c8bd38b08402c598924e14c748921d
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815232"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="6770e-103">ASP.NET Core のフォームのタグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="6770e-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[N. Taylor Mullen](https://github.com/NTaylorMullen)、[Dave Paquette](https://twitter.com/Dave_Paquette)、[Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="6770e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="6770e-105">このドキュメントでは、フォームとフォームでよく使用される HTML 要素の使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6770e-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="6770e-106">HTML の [Form](https://www.w3.org/TR/html401/interact/forms.html) 要素には、Web アプリケーションからサーバーにデータをポスト バックするために使用する主要なメカニズムがあります。</span><span class="sxs-lookup"><span data-stu-id="6770e-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="6770e-107">このドキュメントでは、[タグ ヘルパー](tag-helpers/intro.md)と、タグ ヘルパーを利用して堅牢な HTML フォームを生産的に作成する方法について主に説明します。</span><span class="sxs-lookup"><span data-stu-id="6770e-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="6770e-108">このドキュメントを読む前に、[タグ ヘルパーの概要](tag-helpers/intro.md)に関するページを読むことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6770e-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="6770e-109">多くの場合、HTML ヘルパーには特定のタグ ヘルパーの代替方法が用意されていますが、タグ ヘルパーは HTML ヘルパーの置き換えではない点と、各 HTML ヘルパーに対応するタグ ヘルパーがない点を認識することが重要です。</span><span class="sxs-lookup"><span data-stu-id="6770e-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="6770e-110">HTML ヘルパーの代替が存在する場合は記載します。</span><span class="sxs-lookup"><span data-stu-id="6770e-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="6770e-111">フォーム タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-111">The Form Tag Helper</span></span>

<span data-ttu-id="6770e-112">[フォーム](https://www.w3.org/TR/html401/interact/forms.html) タグ ヘルパー:</span><span class="sxs-lookup"><span data-stu-id="6770e-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="6770e-113">MVC コントローラー アクションまたは名前付きルートについて HTML の [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` 属性値を生成します。</span><span class="sxs-lookup"><span data-stu-id="6770e-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="6770e-114">クロスサイト リクエスト フォージェリを防ぐために、非表示の[要求検証トークン](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)を生成します (HTTP POST アクション メソッドで `[ValidateAntiForgeryToken]` 属性と共に使用する場合)</span><span class="sxs-lookup"><span data-stu-id="6770e-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="6770e-115">`asp-route-<Parameter Name>` 属性を提供します (`<Parameter Name>` がルート値に追加される場合)。</span><span class="sxs-lookup"><span data-stu-id="6770e-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="6770e-116">`Html.BeginForm` および `Html.BeginRouteForm` に `routeValues` パラメーターを指定すると、同様の機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="6770e-117">HTML ヘルパーの代替の `Html.BeginForm` と `Html.BeginRouteForm` があります</span><span class="sxs-lookup"><span data-stu-id="6770e-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="6770e-118">サンプル:</span><span class="sxs-lookup"><span data-stu-id="6770e-118">Sample:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="6770e-119">上記のフォーム タグ ヘルパーで、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-119">The Form Tag Helper above generates the following HTML:</span></span>

```HTML
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="6770e-120">MVC ランタイムで、フォーム タグ ヘルパーの属性 `asp-controller` と `asp-action` から `action` 属性値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="6770e-121">また、フォーム タグ ヘルパーも、クロスサイト リクエスト フォージェリを防ぐために、非表示の[要求検証トークン](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)を生成します (HTTP POST アクション メソッドで `[ValidateAntiForgeryToken]` 属性と共に使用する場合)。</span><span class="sxs-lookup"><span data-stu-id="6770e-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="6770e-122">純粋な HTML フォームをクロスサイト リクエスト フォージェリから保護することは難しいため、フォーム タグ ヘルパーが提供するサービスを利用してください。</span><span class="sxs-lookup"><span data-stu-id="6770e-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="6770e-123">名前付きのルートの使用</span><span class="sxs-lookup"><span data-stu-id="6770e-123">Using a named route</span></span>

<span data-ttu-id="6770e-124">`asp-route` タグ ヘルパー属性で、HTML `action` 属性のマークアップを生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="6770e-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="6770e-125">`register` という名前の[ルート](../../fundamentals/routing.md)を持つアプリケーションは、登録ページに次のマークアップを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6770e-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="6770e-126">*Views/Account* フォルダー (*個々のユーザー アカウント*を使用して新しい Web アプリケーションを作成するときに生成されるフォルダー) のビューの多くには、[asp-route-returnurl](xref:mvc/views/working-with-forms) 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6770e-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="6770e-127">承認済みで認証されていないリソースまたは承認されていないリソースにアクセスしようとすると、組み込みのテンプレートを使用して、`returnUrl` のみが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="6770e-128">未承認のアクセスを試行すると、セキュリティ ミドルウェアによって、`returnUrl` が設定されたログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="6770e-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="6770e-129">フォーム アクション タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="6770e-130">フォーム アクション タグ ヘルパーにより、生成された`<button ...>` または `<input type="image" ...>` タグ上に `formaction` 属性が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="6770e-131">`formaction` 属性では、フォームがそのデータを送信する場所を制御します。</span><span class="sxs-lookup"><span data-stu-id="6770e-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="6770e-132">これは、種類が `image` の [\<input>](https://www.w3.org/wiki/HTML/Elements/input) 要素と、[\<button>](https://www.w3.org/wiki/HTML/Elements/button) 要素にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="6770e-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="6770e-133">フォーム アクション タグ ヘルパーにより、[AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) の `asp-` 属性を複数使うことが可能になり、対応する要素に向けて何の `formaction` リンクが生成されるかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="6770e-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="6770e-134">`formaction` の値を制御するためにサポートされている [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 属性:</span><span class="sxs-lookup"><span data-stu-id="6770e-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="6770e-135">属性</span><span class="sxs-lookup"><span data-stu-id="6770e-135">Attribute</span></span>|<span data-ttu-id="6770e-136">説明</span><span class="sxs-lookup"><span data-stu-id="6770e-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="6770e-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="6770e-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="6770e-138">コントローラーの名前です。</span><span class="sxs-lookup"><span data-stu-id="6770e-138">The name of the controller.</span></span>|
|[<span data-ttu-id="6770e-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="6770e-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="6770e-140">アクション メソッドの名前です。</span><span class="sxs-lookup"><span data-stu-id="6770e-140">The name of the action method.</span></span>|
|[<span data-ttu-id="6770e-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="6770e-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="6770e-142">領域の名前です。</span><span class="sxs-lookup"><span data-stu-id="6770e-142">The name of the area.</span></span>|
|[<span data-ttu-id="6770e-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="6770e-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="6770e-144">Razor ページの名前です。</span><span class="sxs-lookup"><span data-stu-id="6770e-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="6770e-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="6770e-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="6770e-146">Razor ページ ハンドラーの名前です。</span><span class="sxs-lookup"><span data-stu-id="6770e-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="6770e-147">asp-route</span><span class="sxs-lookup"><span data-stu-id="6770e-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="6770e-148">ルートの名前です。</span><span class="sxs-lookup"><span data-stu-id="6770e-148">The name of the route.</span></span>|
|[<span data-ttu-id="6770e-149">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="6770e-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="6770e-150">単一の URL ルート値です。</span><span class="sxs-lookup"><span data-stu-id="6770e-150">A single URL route value.</span></span> <span data-ttu-id="6770e-151">たとえば、`asp-route-id="1234"` のようにします。</span><span class="sxs-lookup"><span data-stu-id="6770e-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="6770e-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="6770e-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="6770e-153">すべてのルート値です。</span><span class="sxs-lookup"><span data-stu-id="6770e-153">All route values.</span></span>|
|[<span data-ttu-id="6770e-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="6770e-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="6770e-155">URL フラグメントです。</span><span class="sxs-lookup"><span data-stu-id="6770e-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="6770e-156">コントローラーに送信する例</span><span class="sxs-lookup"><span data-stu-id="6770e-156">Submit to controller example</span></span>

<span data-ttu-id="6770e-157">次のマークアップでは、入力またはボタンが選択されたときに、`HomeController` の `Index` アクションにフォームを送信します。</span><span class="sxs-lookup"><span data-stu-id="6770e-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="6770e-158">以前のマークアップでは次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="6770e-159">ページに送信する例</span><span class="sxs-lookup"><span data-stu-id="6770e-159">Submit to page example</span></span>

<span data-ttu-id="6770e-160">次のマークアップでは、`About` Razor ページにフォームを送信します。</span><span class="sxs-lookup"><span data-stu-id="6770e-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="6770e-161">以前のマークアップでは次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="6770e-162">ルートに送信する例</span><span class="sxs-lookup"><span data-stu-id="6770e-162">Submit to route example</span></span>

<span data-ttu-id="6770e-163">`/Home/Test` エンドポイントを検討します。</span><span class="sxs-lookup"><span data-stu-id="6770e-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="6770e-164">次のマークアップでは、`/Home/Test` エンドポイントにフォームを送信します。</span><span class="sxs-lookup"><span data-stu-id="6770e-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="6770e-165">以前のマークアップでは次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="6770e-166">入力タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-166">The Input Tag Helper</span></span>

<span data-ttu-id="6770e-167">入力タグ ヘルパーは、HTML の [\<input>](https://www.w3.org/wiki/HTML/Elements/input) 要素を Razor ビューのモデル式にバインドします。</span><span class="sxs-lookup"><span data-stu-id="6770e-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="6770e-168">構文:</span><span class="sxs-lookup"><span data-stu-id="6770e-168">Syntax:</span></span>

```HTML
<input asp-for="<Expression Name>">
```

<span data-ttu-id="6770e-169">入力タグ ヘルパー:</span><span class="sxs-lookup"><span data-stu-id="6770e-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="6770e-170">`asp-for` 属性で指定された式の名前の `id` および `name` HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="6770e-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="6770e-171">`asp-for="Property1.Property2"` は `m => m.Property1.Property2` と同じです。</span><span class="sxs-lookup"><span data-stu-id="6770e-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="6770e-172">式の名前は、`asp-for` 属性値に使用されるものです。</span><span class="sxs-lookup"><span data-stu-id="6770e-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="6770e-173">詳細については、「[式の名前](#expression-names)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6770e-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="6770e-174">モデル プロパティに適用されているモデル型と[データ注釈](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)に基づいて HTML `type` 属性値を設定します</span><span class="sxs-lookup"><span data-stu-id="6770e-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="6770e-175">HTML `type` 属性値が指定されている場合は、上書きしません</span><span class="sxs-lookup"><span data-stu-id="6770e-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="6770e-176">モデル プロパティに適用された[データ注釈](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)属性から [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) 検証属性を生成します</span><span class="sxs-lookup"><span data-stu-id="6770e-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="6770e-177">`Html.TextBoxFor` および `Html.EditorFor` と重複する HTML ヘルパー機能があります。</span><span class="sxs-lookup"><span data-stu-id="6770e-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="6770e-178">詳細については、「**入力タグ ヘルパーの代替となる HTML ヘルパー**」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6770e-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="6770e-179">厳密な型指定を提供します。</span><span class="sxs-lookup"><span data-stu-id="6770e-179">Provides strong typing.</span></span> <span data-ttu-id="6770e-180">プロパティの名前が変更され、タグ ヘルパーを更新しない場合は、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

<span data-ttu-id="6770e-181">`Input` タグ ヘルパーは、.NET 型に基づいて HTML `type` 属性を設定します。</span><span class="sxs-lookup"><span data-stu-id="6770e-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="6770e-182">次の表は、一般的な.NET 型と生成される HTML 型の一部をまとめたものです (すべての .NET 型を網羅した一覧ではありません)。</span><span class="sxs-lookup"><span data-stu-id="6770e-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="6770e-183">.NET 型</span><span class="sxs-lookup"><span data-stu-id="6770e-183">.NET type</span></span>|<span data-ttu-id="6770e-184">入力の型</span><span class="sxs-lookup"><span data-stu-id="6770e-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="6770e-185">Bool</span><span class="sxs-lookup"><span data-stu-id="6770e-185">Bool</span></span>|<span data-ttu-id="6770e-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="6770e-186">type="checkbox"</span></span>|
|<span data-ttu-id="6770e-187">String</span><span class="sxs-lookup"><span data-stu-id="6770e-187">String</span></span>|<span data-ttu-id="6770e-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="6770e-188">type="text"</span></span>|
|<span data-ttu-id="6770e-189">DateTime</span><span class="sxs-lookup"><span data-stu-id="6770e-189">DateTime</span></span>|<span data-ttu-id="6770e-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="6770e-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="6770e-191">Byte</span><span class="sxs-lookup"><span data-stu-id="6770e-191">Byte</span></span>|<span data-ttu-id="6770e-192">type="number"</span><span class="sxs-lookup"><span data-stu-id="6770e-192">type="number"</span></span>|
|<span data-ttu-id="6770e-193">Int</span><span class="sxs-lookup"><span data-stu-id="6770e-193">Int</span></span>|<span data-ttu-id="6770e-194">type="number"</span><span class="sxs-lookup"><span data-stu-id="6770e-194">type="number"</span></span>|
|<span data-ttu-id="6770e-195">Single、Double</span><span class="sxs-lookup"><span data-stu-id="6770e-195">Single, Double</span></span>|<span data-ttu-id="6770e-196">type="number"</span><span class="sxs-lookup"><span data-stu-id="6770e-196">type="number"</span></span>|

<span data-ttu-id="6770e-197">次の表は、入力タグ ヘルパーが特定の入力の型にマップする一般的な[データ注釈](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)属性の一部をまとめたものです (すべての検証属性を網羅した一覧ではありません)。</span><span class="sxs-lookup"><span data-stu-id="6770e-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="6770e-198">属性</span><span class="sxs-lookup"><span data-stu-id="6770e-198">Attribute</span></span>|<span data-ttu-id="6770e-199">入力の型</span><span class="sxs-lookup"><span data-stu-id="6770e-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="6770e-200">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="6770e-200">[EmailAddress]</span></span>|<span data-ttu-id="6770e-201">type="email"</span><span class="sxs-lookup"><span data-stu-id="6770e-201">type="email"</span></span>|
|<span data-ttu-id="6770e-202">[Url]</span><span class="sxs-lookup"><span data-stu-id="6770e-202">[Url]</span></span>|<span data-ttu-id="6770e-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="6770e-203">type="url"</span></span>|
|<span data-ttu-id="6770e-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="6770e-204">[HiddenInput]</span></span>|<span data-ttu-id="6770e-205">type="hidden"</span><span class="sxs-lookup"><span data-stu-id="6770e-205">type="hidden"</span></span>|
|<span data-ttu-id="6770e-206">[Phone]</span><span class="sxs-lookup"><span data-stu-id="6770e-206">[Phone]</span></span>|<span data-ttu-id="6770e-207">type="tel"</span><span class="sxs-lookup"><span data-stu-id="6770e-207">type="tel"</span></span>|
|<span data-ttu-id="6770e-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="6770e-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="6770e-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="6770e-209">type="password"</span></span>|
|<span data-ttu-id="6770e-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="6770e-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="6770e-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="6770e-211">type="date"</span></span>|
|<span data-ttu-id="6770e-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="6770e-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="6770e-213">type="time"</span><span class="sxs-lookup"><span data-stu-id="6770e-213">type="time"</span></span>|

<span data-ttu-id="6770e-214">サンプル:</span><span class="sxs-lookup"><span data-stu-id="6770e-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="6770e-215">上記のコードで、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-215">The code above generates the following HTML:</span></span>

```HTML
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="6770e-216">`Email` および `Password` プロパティに適用されたデータ注釈によって、モデルに関するメタデータが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="6770e-217">入力タグ ヘルパーはモデルのメタデータを使用し、[HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` 属性を生成します ([モデルの検証](../models/validation.md)に関するページを参照してください)。</span><span class="sxs-lookup"><span data-stu-id="6770e-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="6770e-218">これらの属性に、入力フィールドにアタッチする検証コントロールを記述します。</span><span class="sxs-lookup"><span data-stu-id="6770e-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="6770e-219">これで、控えめな HTML5 と [jQuery](https://jquery.com/) の検証機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="6770e-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="6770e-220">控えめな属性の形式は `data-val-rule="Error Message"` です。この rule は検証ルールの名前です (`data-val-required`、`data-val-email`、`data-val-maxlength` など)。属性にエラー メッセージが指定されている場合は、`data-val-rule` 属性の値として表示されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="6770e-221">`data-val-maxlength-max="1024"` など、ルールに関する追加の詳細情報を提供するフォームの属性 `data-val-ruleName-argumentName="argumentValue"` もあります。</span><span class="sxs-lookup"><span data-stu-id="6770e-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="6770e-222">入力タグ ヘルパーの代替となる HTML ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="6770e-223">`Html.TextBox`、`Html.TextBoxFor`、`Html.Editor`、`Html.EditorFor` には、入力タグ ヘルパーと重複する機能があります。</span><span class="sxs-lookup"><span data-stu-id="6770e-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="6770e-224">入力タグ ヘルパーでは `type` 属性が自動的に設定されますが、`Html.TextBox` と `Html.TextBoxFor` では自動設定されません。</span><span class="sxs-lookup"><span data-stu-id="6770e-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="6770e-225">`Html.Editor` と `Html.EditorFor` はコレクション、複雑なオブジェクト、テンプレートを処理しますが、入力タグ ヘルパーは処理しません。</span><span class="sxs-lookup"><span data-stu-id="6770e-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="6770e-226">入力タグ ヘルパーの `Html.EditorFor` と `Html.TextBoxFor` は厳密に型指定されていますが (ラムダ式を使用します)、`Html.TextBox` と `Html.Editor` は厳密に型指定されていません (式の名前を使用します)。</span><span class="sxs-lookup"><span data-stu-id="6770e-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="6770e-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="6770e-227">HtmlAttributes</span></span>

<span data-ttu-id="6770e-228">`@Html.Editor()` と `@Html.EditorFor()` は、既定のテンプレートを実行するときに `htmlAttributes` という名前の特殊な `ViewDataDictionary` エントリを使用します。</span><span class="sxs-lookup"><span data-stu-id="6770e-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="6770e-229">この動作は、必要に応じて `additionalViewData` パラメーターを使用して拡張されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="6770e-230">キー "htmlAttributes" は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="6770e-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="6770e-231">キー "htmlAttributes" は、`htmlAttributes` のような入力ヘルパーに渡される `@Html.TextBox()` オブジェクトと同様に処理されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="6770e-232">式の名前</span><span class="sxs-lookup"><span data-stu-id="6770e-232">Expression names</span></span>

<span data-ttu-id="6770e-233">`asp-for` 属性値は `ModelExpression` であり、ラムダ式の右辺です。</span><span class="sxs-lookup"><span data-stu-id="6770e-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="6770e-234">そのため、生成されるコードで `asp-for="Property1"` は `m => m.Property1` になります。したがって、`Model` をプレフィックスとして付加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6770e-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="6770e-235">"\@" 文字を使用してインライン式を開始し、`m.` の前に移動できます。</span><span class="sxs-lookup"><span data-stu-id="6770e-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe">
```

<span data-ttu-id="6770e-236">以下が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-236">Generates the following:</span></span>

```HTML
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="6770e-237">`i` の値が `23` の場合、`asp-for="CollectionProperty[23].Member"` はコレクションのプロパティを使用して、`asp-for="CollectionProperty[i].Member"` と同じ名前を生成します。</span><span class="sxs-lookup"><span data-stu-id="6770e-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="6770e-238">ASP.NET Core MVC で `ModelExpression` の値が計算されるとき、`ModelState` を含む、いくつかのソースが検査されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="6770e-239">`<input type="text" asp-for="@Name">` を検討します。</span><span class="sxs-lookup"><span data-stu-id="6770e-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="6770e-240">計算された `value` 属性は、次のうちの最初の非 null 値です。</span><span class="sxs-lookup"><span data-stu-id="6770e-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="6770e-241">キー "Name" を持つ `ModelState` エントリ。</span><span class="sxs-lookup"><span data-stu-id="6770e-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="6770e-242">式 `Model.Name` の結果。</span><span class="sxs-lookup"><span data-stu-id="6770e-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="6770e-243">子プロパティの移動</span><span class="sxs-lookup"><span data-stu-id="6770e-243">Navigating child properties</span></span>

<span data-ttu-id="6770e-244">ビュー モデルのプロパティ パスを使用して、子プロパティに移動することもできます。</span><span class="sxs-lookup"><span data-stu-id="6770e-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="6770e-245">子 `Address` プロパティを含むより複雑なモデル クラスを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="6770e-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="6770e-246">このビューでは、`Address.AddressLine1` にバインドしています。</span><span class="sxs-lookup"><span data-stu-id="6770e-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="6770e-247">`Address.AddressLine1` に対して次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="6770e-248">式の名前とコレクション</span><span class="sxs-lookup"><span data-stu-id="6770e-248">Expression names and Collections</span></span>

<span data-ttu-id="6770e-249">`Colors` の配列を含むサンプル モデル:</span><span class="sxs-lookup"><span data-stu-id="6770e-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="6770e-250">アクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="6770e-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

<span data-ttu-id="6770e-251">次の Razor は、特定の `Color` 要素にアクセスする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6770e-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="6770e-252">*Views/Shared/EditorTemplates/String.cshtml* テンプレート:</span><span class="sxs-lookup"><span data-stu-id="6770e-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="6770e-253">`List<T>` を使用するサンプル:</span><span class="sxs-lookup"><span data-stu-id="6770e-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="6770e-254">次の Razor は、コレクションに対して反復処理を実行する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6770e-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="6770e-255">*Views/Shared/EditorTemplates/ToDoItem.cshtml* テンプレート:</span><span class="sxs-lookup"><span data-stu-id="6770e-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="6770e-256">値を `asp-for` または `Html.DisplayFor` と同じコンテキストで使用する場合は、可能であれば `foreach` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6770e-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="6770e-257">一般に、反復子を割り当てる必要がないため、(使用可能なシナリオでは) `for` の方が `foreach` よりも優れています。ただし、LINQ 式内でのインデクサーの評価はコストが高くなる可能性があるため、最小限に抑える必要があります。</span><span class="sxs-lookup"><span data-stu-id="6770e-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="6770e-258">上記のコメント付きサンプル コードは、ラムダ式を `@` 演算子に置き換えて、リスト内の各 `ToDoItem` にアクセスする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6770e-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="6770e-259">Textarea タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="6770e-260">`Textarea Tag Helper` タグ ヘルパーは、入力タグ ヘルパーと似ています。</span><span class="sxs-lookup"><span data-stu-id="6770e-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="6770e-261">[\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) 要素のモデルから `id` および `name` 属性と、データ検証属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="6770e-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="6770e-262">厳密な型指定を提供します。</span><span class="sxs-lookup"><span data-stu-id="6770e-262">Provides strong typing.</span></span>

* <span data-ttu-id="6770e-263">HTML ヘルパーの代替: `Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="6770e-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="6770e-264">サンプル:</span><span class="sxs-lookup"><span data-stu-id="6770e-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="6770e-265">次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-265">The following HTML is generated:</span></span>

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="6770e-266">ラベル タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-266">The Label Tag Helper</span></span>

* <span data-ttu-id="6770e-267">式の名前の [\<label>](https://www.w3.org/wiki/HTML/Elements/label) 要素に対してラベルのキャプションと `for` 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="6770e-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="6770e-268">HTML ヘルパーの代替: `Html.LabelFor`。</span><span class="sxs-lookup"><span data-stu-id="6770e-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="6770e-269">`Label Tag Helper` は、純粋な HTML の label 要素よりも次の点で優れています。</span><span class="sxs-lookup"><span data-stu-id="6770e-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="6770e-270">`Display` 属性からわかりやすいラベル値が自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="6770e-271">意図した表示名は時間と共に変化する可能性があります。また、`Display` 属性とラベル タグ ヘルパーの組み合わせによって、使用されているあらゆる場所に `Display` が適用されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="6770e-272">ソース コードのマークアップが少ない</span><span class="sxs-lookup"><span data-stu-id="6770e-272">Less markup in source code</span></span>

* <span data-ttu-id="6770e-273">モデルのプロパティを使用した厳密な型指定。</span><span class="sxs-lookup"><span data-stu-id="6770e-273">Strong typing with the model property.</span></span>

<span data-ttu-id="6770e-274">サンプル:</span><span class="sxs-lookup"><span data-stu-id="6770e-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="6770e-275">`<label>` 要素に対して次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-275">The following HTML is generated for the `<label>` element:</span></span>

```HTML
<label for="Email">Email Address</label>
```

<span data-ttu-id="6770e-276">ラベル タグ ヘルパーから "Email" の属性値 `for` が生成されました。これは、`<input>` に関連付けられた ID です。</span><span class="sxs-lookup"><span data-stu-id="6770e-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="6770e-277">タグ ヘルパーでは一貫性のある `id` および `for` 要素が生成されるので、正しく関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="6770e-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="6770e-278">このサンプルのキャプションは `Display` 属性に由来します。</span><span class="sxs-lookup"><span data-stu-id="6770e-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="6770e-279">モデルに `Display` 属性を含めなかった場合、キャプションは式のプロパティ名になります。</span><span class="sxs-lookup"><span data-stu-id="6770e-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="6770e-280">検証タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="6770e-281">2 つの検証タグ ヘルパーがあります。</span><span class="sxs-lookup"><span data-stu-id="6770e-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="6770e-282">`Validation Message Tag Helper` (モデルの単一のプロパティに関する検証メッセージを表示する) と `Validation Summary Tag Helper` (検証エラーの概要を表示する) です。</span><span class="sxs-lookup"><span data-stu-id="6770e-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="6770e-283">`Input Tag Helper` は、モデル クラスのデータ注釈属性に基づいて、HTML5 のクライアント側検証属性を input 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="6770e-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="6770e-284">検証はサーバー側でも実行されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-284">Validation is also performed on the server.</span></span> <span data-ttu-id="6770e-285">検証エラーが発生すると、検証タグ ヘルパーによってこれらのエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="6770e-286">検証メッセージ タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="6770e-287">[HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` 属性を [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) 要素に追加します。これによって、指定されたモデル プロパティの入力フィールドに検証エラー メッセージがアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="6770e-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="6770e-288">クライアント側の検証エラーが発生すると、[jQuery](https://jquery.com/) によって `<span>` 要素のエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="6770e-289">検証はサーバー側でも実行されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-289">Validation also takes place on the server.</span></span> <span data-ttu-id="6770e-290">クライアントで JavaScript が無効にされている場合や、一部の検証をサーバー側でのみ実行できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6770e-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="6770e-291">HTML ヘルパーの代替: `Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="6770e-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="6770e-292">`Validation Message Tag Helper` は、HTML の [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) 要素で `asp-validation-for` 属性と共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```HTML
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="6770e-293">検証メッセージ タグ ヘルパーから、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="6770e-294">一般的に、同じプロパティの場合は、`Input` タグ ヘルパーの後に `Validation Message Tag Helper` を使用します。</span><span class="sxs-lookup"><span data-stu-id="6770e-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="6770e-295">こうすることで、エラーの原因となった入力の近くで検証エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="6770e-296">クライアント側の検証のために、正しい JavaScript および [jQuery](https://jquery.com/) のスクリプト参照を使用したビューを用意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6770e-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="6770e-297">詳細については、[モデルの検証](../models/validation.md)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6770e-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="6770e-298">サーバー側の検証エラーが発生した場合 (カスタムのサーバー側の検証がある場合や、クライアント側の検証が無効な場合など)、MVC はそのエラー メッセージを `<span>` 要素の本文として配置します。</span><span class="sxs-lookup"><span data-stu-id="6770e-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="6770e-299">検証概要タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="6770e-300">`asp-validation-summary` 属性を持つ `<div>` 要素をターゲットとします</span><span class="sxs-lookup"><span data-stu-id="6770e-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="6770e-301">HTML ヘルパーの代替: `@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="6770e-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="6770e-302">`Validation Summary Tag Helper` は、検証メッセージの概要を表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="6770e-303">`asp-validation-summary` 属性値には、次のいずれかを指定できます。</span><span class="sxs-lookup"><span data-stu-id="6770e-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="6770e-304">asp-validation-summary</span><span class="sxs-lookup"><span data-stu-id="6770e-304">asp-validation-summary</span></span>|<span data-ttu-id="6770e-305">検証メッセージが表示されます</span><span class="sxs-lookup"><span data-stu-id="6770e-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="6770e-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="6770e-306">ValidationSummary.All</span></span>|<span data-ttu-id="6770e-307">プロパティとモデル レベル</span><span class="sxs-lookup"><span data-stu-id="6770e-307">Property and model level</span></span>|
|<span data-ttu-id="6770e-308">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="6770e-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="6770e-309">モデル</span><span class="sxs-lookup"><span data-stu-id="6770e-309">Model</span></span>|
|<span data-ttu-id="6770e-310">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="6770e-310">ValidationSummary.None</span></span>|<span data-ttu-id="6770e-311">なし</span><span class="sxs-lookup"><span data-stu-id="6770e-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="6770e-312">サンプル</span><span class="sxs-lookup"><span data-stu-id="6770e-312">Sample</span></span>

<span data-ttu-id="6770e-313">次の例では、データ モデルは `DataAnnotation` 属性で修飾され、`<input>` 要素に関する検証エラー メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-313">In the following example, the data model is decorated with `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="6770e-314">検証エラーが発生すると、検証タグ ヘルパーはエラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="6770e-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="6770e-315">生成される HTML (モデルが有効な場合):</span><span class="sxs-lookup"><span data-stu-id="6770e-315">The generated HTML (when the model is valid):</span></span>

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="6770e-316">選択タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="6770e-316">The Select Tag Helper</span></span>

* <span data-ttu-id="6770e-317">モデルのプロパティについて、[select](https://www.w3.org/wiki/HTML/Elements/select) 要素と、関連する [option](https://www.w3.org/wiki/HTML/Elements/option) 要素を生成します。</span><span class="sxs-lookup"><span data-stu-id="6770e-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="6770e-318">HTML ヘルパーの代替の `Html.DropDownListFor` と `Html.ListBoxFor` があります</span><span class="sxs-lookup"><span data-stu-id="6770e-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="6770e-319">`Select Tag Helper` `asp-for` は [select](https://www.w3.org/wiki/HTML/Elements/select) 要素のモデル プロパティ名を指定し、`asp-items` は [option](https://www.w3.org/wiki/HTML/Elements/option) 要素を指定します。</span><span class="sxs-lookup"><span data-stu-id="6770e-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="6770e-320">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6770e-320">For example:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="6770e-321">サンプル:</span><span class="sxs-lookup"><span data-stu-id="6770e-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="6770e-322">`Index` メソッドは `CountryViewModel` を初期化し、選択された国を設定し、それを `Index` ビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="6770e-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="6770e-323">HTTP POST `Index` メソッドによって選択内容が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="6770e-324">`Index` ビュー:</span><span class="sxs-lookup"><span data-stu-id="6770e-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="6770e-325">次の HTML が生成されます ("CA" が選択されている場合)。</span><span class="sxs-lookup"><span data-stu-id="6770e-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="6770e-326">選択タグ ヘルパーで `ViewBag` または `ViewData` を使用することはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="6770e-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="6770e-327">ビュー モデルは、MVC メタデータを提供する場合に堅牢性が高くなり、一般的にあまり問題にはなりません。</span><span class="sxs-lookup"><span data-stu-id="6770e-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="6770e-328">`asp-for` 属性値は特殊なケースであり、(`asp-items` などの) 他のタグ ヘルパー属性とは異なり、`Model` プレフィックスは必須ではありません</span><span class="sxs-lookup"><span data-stu-id="6770e-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="6770e-329">Enum バインディング</span><span class="sxs-lookup"><span data-stu-id="6770e-329">Enum binding</span></span>

<span data-ttu-id="6770e-330">`<select>` を `enum` プロパティと組み合わせて使用し、`enum` 値から `SelectListItem` 要素を生成すると便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="6770e-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="6770e-331">サンプル:</span><span class="sxs-lookup"><span data-stu-id="6770e-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="6770e-332">`GetEnumSelectList` メソッドは列挙型の場合に `SelectList` オブジェクトを生成します。</span><span class="sxs-lookup"><span data-stu-id="6770e-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="6770e-333">より高機能な UI にするために、列挙子リストを `Display` 属性で修飾することができます。</span><span class="sxs-lookup"><span data-stu-id="6770e-333">You can decorate your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="6770e-334">次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-334">The following HTML is generated:</span></span>

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="6770e-335">オプション グループ</span><span class="sxs-lookup"><span data-stu-id="6770e-335">Option Group</span></span>

<span data-ttu-id="6770e-336">HTML の [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) 要素は、ビュー モデルに 1 つ以上の `SelectListGroup` オブジェクトが含まれている場合に生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="6770e-337">`CountryViewModelGroup` は `SelectListItem` 要素を "北米" グループと "ヨーロッパ" グループに分けます。</span><span class="sxs-lookup"><span data-stu-id="6770e-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="6770e-338">この 2 つのグループを次に示します。</span><span class="sxs-lookup"><span data-stu-id="6770e-338">The two groups are shown below:</span></span>

![オプション グループの例](working-with-forms/_static/grp.png)

<span data-ttu-id="6770e-340">生成される HTML:</span><span class="sxs-lookup"><span data-stu-id="6770e-340">The generated HTML:</span></span>

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="6770e-341">複数選択</span><span class="sxs-lookup"><span data-stu-id="6770e-341">Multiple select</span></span>

<span data-ttu-id="6770e-342">`asp-for` 属性に指定されているプロパティが `IEnumerable` の場合、選択タグ ヘルパーは [multiple = "multiple"](https://w3c.github.io/html-reference/select.html) 属性を自動的に生成します。</span><span class="sxs-lookup"><span data-stu-id="6770e-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="6770e-343">たとえば、次のようなモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="6770e-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="6770e-344">次のビューを対象にします。</span><span class="sxs-lookup"><span data-stu-id="6770e-344">With the following view:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="6770e-345">次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-345">Generates the following HTML:</span></span>

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="6770e-346">選択なし</span><span class="sxs-lookup"><span data-stu-id="6770e-346">No selection</span></span>

<span data-ttu-id="6770e-347">複数のページで "未指定" オプションを使用しているとわかった場合は、テンプレートを作成して HTML の繰り返しを除去することができます。</span><span class="sxs-lookup"><span data-stu-id="6770e-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="6770e-348">*Views/Shared/EditorTemplates/CountryViewModel.cshtml* テンプレート:</span><span class="sxs-lookup"><span data-stu-id="6770e-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="6770e-349">HTML の [\<option>](https://www.w3.org/wiki/HTML/Elements/option) 要素の追加は、*選択なし*の場合に限定されません。</span><span class="sxs-lookup"><span data-stu-id="6770e-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="6770e-350">たとえば、次のビューおよびアクション メソッドで、上記のコードのような HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="6770e-351">現在の `Country` 値に応じて、(`selected="selected"` 属性を含む) 正しい `<option>` 要素が選択されます。</span><span class="sxs-lookup"><span data-stu-id="6770e-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="6770e-352">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6770e-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="6770e-353">HTML の Form 要素</span><span class="sxs-lookup"><span data-stu-id="6770e-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="6770e-354">要求検証トークン</span><span class="sxs-lookup"><span data-stu-id="6770e-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="6770e-355">IAttributeAdapter インターフェイス</span><span class="sxs-lookup"><span data-stu-id="6770e-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="6770e-356">このドキュメントのコード スニペット</span><span class="sxs-lookup"><span data-stu-id="6770e-356">Code snippets for this document</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
