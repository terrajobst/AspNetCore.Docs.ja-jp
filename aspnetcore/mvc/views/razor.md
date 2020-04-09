---
title: ASP.NET Core の Razor 構文リファレンス
author: rick-anderson
description: Web ページにサーバー ベースのコードを埋め込むための Razor マークアップの構文について説明します。
ms.author: riande
ms.date: 02/12/2020
uid: mvc/views/razor
ms.openlocfilehash: dd5c73be56ed0dafb759df2f5ff2eac1a3b5b09e
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381771"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="d04b9-103">ASP.NET Core の Razor 構文リファレンス</span><span class="sxs-lookup"><span data-stu-id="d04b9-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="d04b9-104">[リック・アンダーソン](https://twitter.com/RickAndMSFT)、[テイラー・マレン](https://twitter.com/ntaylormullen)、[ダン・ヴィカレル](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="d04b9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="d04b9-105">Razor は、Web ページにサーバー ベースのコードを埋め込むためのマークアップ構文です。</span><span class="sxs-lookup"><span data-stu-id="d04b9-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="d04b9-106">Razor 構文は、Razor マークアップ、C#、HTML で構成されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="d04b9-107">通常、Razor を含むファイルのファイル拡張子は *.cshtml* です。</span><span class="sxs-lookup"><span data-stu-id="d04b9-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="d04b9-108">Razor は [Razor コンポーネント](xref:blazor/components) ファイル (*.razor*) にもあります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="d04b9-109">HTML のレンダリング</span><span class="sxs-lookup"><span data-stu-id="d04b9-109">Rendering HTML</span></span>

<span data-ttu-id="d04b9-110">Razor の既定の言語は HTML です。</span><span class="sxs-lookup"><span data-stu-id="d04b9-110">The default Razor language is HTML.</span></span> <span data-ttu-id="d04b9-111">Razor マークアップからの HTML のレンダリングは、HTML ファイルからの HTML のレンダリングと同じです。</span><span class="sxs-lookup"><span data-stu-id="d04b9-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="d04b9-112">*.cshtml* Razor ファイル内の HTML マークアップは、サーバーによって変更されずにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="d04b9-113">Razor の構文</span><span class="sxs-lookup"><span data-stu-id="d04b9-113">Razor syntax</span></span>

<span data-ttu-id="d04b9-114">Razor は C# をサポートし、`@` 記号を使って HTML から C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="d04b9-115">Razor は C# の式を評価し、それらを HTML の出力でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="d04b9-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="d04b9-116">`@` 記号の後に [Razor の予約済みキーワード](#razor-reserved-keywords)が続いている場合は、Razor 固有のマークアップに移行します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="d04b9-117">それ以外の場合は、普通の C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="d04b9-118">Razor マークアップで `@` 記号をエスケープするには、`@` 記号をもう 1 つ使います。</span><span class="sxs-lookup"><span data-stu-id="d04b9-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="d04b9-119">HTML では、コードは 1 つの `@` 記号でレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="d04b9-120">メール アドレスを含む HTML の属性とコンテンツは、`@` 記号を遷移文字として扱いません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="d04b9-121">次の例のメール アドレスは、Razor の解析ではそのまま残ります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="d04b9-122">暗黙的な Razor 式</span><span class="sxs-lookup"><span data-stu-id="d04b9-122">Implicit Razor expressions</span></span>

<span data-ttu-id="d04b9-123">Razor の暗黙的な式は、`@` で始まって C# のコードが続きます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="d04b9-124">C# の `await` キーワードを除き、暗黙的な式にスペースを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="d04b9-125">C# のステートメントに明確な終わりがある場合は、スペースを混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="d04b9-126">暗黙的な式では、山かっこ (`<>`) の内側の文字は HTML タグとして解釈されるため、C# ジェネリックを含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="d04b9-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="d04b9-127">次のコードは有効では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="d04b9-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="d04b9-128">上記のコードでは、次のいずれかのようなコンパイラ エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="d04b9-129">The "int" element wasn't closed.</span><span class="sxs-lookup"><span data-stu-id="d04b9-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="d04b9-130">All elements must be either self-closing or have a matching end tag. ("int" 要素が閉じられませんでした。すべての要素は、自己終了するか、対応する終了タグが存在する必要があります。)</span><span class="sxs-lookup"><span data-stu-id="d04b9-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="d04b9-131">メソッド グループ 'GenericMethod' を非デリゲート型 'object' に変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="d04b9-132">このメソッドを呼び出しますか?</span><span class="sxs-lookup"><span data-stu-id="d04b9-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="d04b9-133">ジェネリック メソッドの呼び出しは、[明示的な Razor 式](#explicit-razor-expressions)または [Razor コード ブロック](#razor-code-blocks)にラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="d04b9-134">明示的な Razor 式</span><span class="sxs-lookup"><span data-stu-id="d04b9-134">Explicit Razor expressions</span></span>

<span data-ttu-id="d04b9-135">明示的な Razor 式は、`@` 記号とバランスの取れたかっこ記号で構成されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="d04b9-136">1 週間前の時刻を表示するには、次の Razor マークアップを使います。</span><span class="sxs-lookup"><span data-stu-id="d04b9-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="d04b9-137">`@()` のかっこ内の内容が評価されて、出力にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="d04b9-138">前のセクションで説明した暗黙的な式は、一般に、スペースを含むことはできません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="d04b9-139">次のコードでは、現在時刻から 1 週間は減算されません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="d04b9-140">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="d04b9-141">明示的な式を使うと、テキストと式の結果を連結できます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="d04b9-142">明示的な式を使わないと、`<p>Age@joe.Age</p>` はメール アドレスとして扱われ、`<p>Age@joe.Age</p>` がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="d04b9-143">明示的な式として記述すると、`<p>Age33</p>` がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="d04b9-144">明示的な式を使うと、ジェネリック メソッドから *.cshtml* ファイルに出力できます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="d04b9-145">次のマークアップは、C# ジェネリックの山かっこによって発生した前述のエラーを修正する方法について示します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="d04b9-146">コードは明示的な式として書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="d04b9-147">式のエンコード</span><span class="sxs-lookup"><span data-stu-id="d04b9-147">Expression encoding</span></span>

<span data-ttu-id="d04b9-148">文字列として評価される C# の式は、HTML でエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="d04b9-149">`IHtmlContent` として評価される C# の式は、`IHtmlContent.WriteTo` によって直接レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="d04b9-150">`IHtmlContent` として評価されない C# の式は、`ToString` によって文字列に変換され、レンダリングされる前にエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="d04b9-151">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="d04b9-152">HTML はブラウザーで次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="d04b9-153">`HtmlHelper.Raw` の出力はエンコードされませんが、HTML マークアップとしてレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="d04b9-154">サニタイズされていないユーザー入力で `HtmlHelper.Raw` を使うと、セキュリティ上のリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="d04b9-155">ユーザー入力には、悪意のある JavaScript または他の攻撃が含まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="d04b9-156">ユーザー入力をサニタイズすることは困難です。</span><span class="sxs-lookup"><span data-stu-id="d04b9-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="d04b9-157">ユーザー入力では `HtmlHelper.Raw` を使わないでください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="d04b9-158">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="d04b9-159">Razor コード ブロック</span><span class="sxs-lookup"><span data-stu-id="d04b9-159">Razor code blocks</span></span>

<span data-ttu-id="d04b9-160">Razor コード ブロックは、`@` で始まり、`{}` で囲まれています。</span><span class="sxs-lookup"><span data-stu-id="d04b9-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="d04b9-161">式とは異なり、コード ブロック内の C# コードはレンダリングされません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="d04b9-162">ビュー内のコード ブロックと式は同じスコープを共有し、次の順序で定義されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="d04b9-163">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d04b9-164">コード ブロックで、る[ローカル関数](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)をマークアップで宣言し、テンプレート メソッドとしてのサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="d04b9-165">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="d04b9-166">暗黙の移行</span><span class="sxs-lookup"><span data-stu-id="d04b9-166">Implicit transitions</span></span>

<span data-ttu-id="d04b9-167">コード ブロック内の既定の言語は C# ですが、Razor ページは HTML に移行して戻ることがあります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="d04b9-168">明示的に区切られた遷移</span><span class="sxs-lookup"><span data-stu-id="d04b9-168">Explicit delimited transition</span></span>

<span data-ttu-id="d04b9-169">HTML をレンダリングする必要のあるコード ブロックのサブセクションを定義するには、レンダリングする文字を Razor の `<text>` タグで囲みます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="d04b9-170">HTML タグによって囲まれていない HTML をレンダリングするには、この方法を使います。</span><span class="sxs-lookup"><span data-stu-id="d04b9-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="d04b9-171">HTML タグまたは Razor タグがない場合、Razor ラインタイム エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="d04b9-172">`<text>` タグは、内容をレンダリングするときに空白文字を制御するのに便利です。</span><span class="sxs-lookup"><span data-stu-id="d04b9-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="d04b9-173">`<text>` タグの間の内容だけがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="d04b9-174">`<text>` タグの前後にある空白文字は HTML の出力には表示されません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="d04b9-175">明示的な行の遷移</span><span class="sxs-lookup"><span data-stu-id="d04b9-175">Explicit line transition</span></span>

<span data-ttu-id="d04b9-176">残りの行全体をコード ブロック内に HTML としてレンダリングするには、`@:` 構文を使います。</span><span class="sxs-lookup"><span data-stu-id="d04b9-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="d04b9-177">コードに `@:` がないと、Razor ランタイム エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="d04b9-178">Razor ファイルに余分な `@` 文字があると、ブロックの後続のステートメントでコンパイラ エラーが発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="d04b9-179">これらのコンパイラ エラーは、実際のエラーは報告されたエラーより前で発生しているため、理解するのが難しい場合があります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="d04b9-180">このエラーは、複数の暗黙的/明示的な式を 1 つのコード ブロックに結合した後で発生することがよくあります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="d04b9-181">制御構造</span><span class="sxs-lookup"><span data-stu-id="d04b9-181">Control structures</span></span>

<span data-ttu-id="d04b9-182">制御構造は、コード ブロックの拡張機能です。</span><span class="sxs-lookup"><span data-stu-id="d04b9-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="d04b9-183">コード ブロックのすべての側面 (マークアップへの遷移、インライン C# ) が、次の構造にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="d04b9-184">条件付き \@if、else if、else、\@switch</span><span class="sxs-lookup"><span data-stu-id="d04b9-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="d04b9-185">`@if` は、いつコードを実行するかを制御します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="d04b9-186">`else` および `else if` には、`@` 記号は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="d04b9-187">次のマークアップでは、switch ステートメントの使い方を示します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="d04b9-188">ループ処理 \@for、\@foreach、\@while、\@do while</span><span class="sxs-lookup"><span data-stu-id="d04b9-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="d04b9-189">ループ制御ステートメントを使って、テンプレート化された HTML をレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="d04b9-190">人の一覧をレンダリングするには:</span><span class="sxs-lookup"><span data-stu-id="d04b9-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="d04b9-191">以下のループ ステートメントがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d04b9-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="d04b9-192">複合 \@using</span><span class="sxs-lookup"><span data-stu-id="d04b9-192">Compound \@using</span></span>

<span data-ttu-id="d04b9-193">C# では、オブジェクトを確実に破棄するために `using` オブジェクトが使われています。</span><span class="sxs-lookup"><span data-stu-id="d04b9-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="d04b9-194">Razor では、同じメカニズムが、追加コンテンツを含む HTML ヘルパーを作成するために使われています。</span><span class="sxs-lookup"><span data-stu-id="d04b9-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="d04b9-195">次のコードの HTML ヘルパーは、`@using` ステートメントを含む `<form>` タグをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="d04b9-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="d04b9-196">\@try、catch、finally</span><span class="sxs-lookup"><span data-stu-id="d04b9-196">\@try, catch, finally</span></span>

<span data-ttu-id="d04b9-197">例外処理は C# に似ています。</span><span class="sxs-lookup"><span data-stu-id="d04b9-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="d04b9-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="d04b9-198">\@lock</span></span>

<span data-ttu-id="d04b9-199">Razor には、重要なセクションを lock ステートメントで保護する機能があります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="d04b9-200">説明</span><span class="sxs-lookup"><span data-stu-id="d04b9-200">Comments</span></span>

<span data-ttu-id="d04b9-201">Razor は、C# と HTML のコメントをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="d04b9-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="d04b9-202">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="d04b9-203">Razor のコメントは、Web ページがレンダリングされる前に、サーバーによって削除されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="d04b9-204">Razor では、`@*  *@` を使ってコメントを区切ります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="d04b9-205">次のコードはコメント化されているため、サーバーはどのマークアップもレンダリングしません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="d04b9-206">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="d04b9-206">Directives</span></span>

<span data-ttu-id="d04b9-207">Razor のディレクティブは、`@` 記号の後の予約キーワードによる暗黙的な式で表されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="d04b9-208">通常、ディレクティブは、ビューの解析方法を変更したり、異なる機能を有効にしたりします。</span><span class="sxs-lookup"><span data-stu-id="d04b9-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="d04b9-209">Razor がビューのコードを生成する方法を理解すると、ディレクティブの動作を理解しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="d04b9-210">上のコードでは、次のようなクラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-210">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="d04b9-211">後の「[ビューに対して生成された Razor C# クラスを調べる](#inspect-the-razor-c-class-generated-for-a-view)」セクションでは、この生成されたクラスを表示する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="d04b9-212">\@attribute</span><span class="sxs-lookup"><span data-stu-id="d04b9-212">\@attribute</span></span>

<span data-ttu-id="d04b9-213">`@attribute` ディレクティブでは、指定された属性が生成されたページまたはビューのクラスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="d04b9-214">次の例では、`[Authorize]` 属性が追加されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="d04b9-215">\@code</span><span class="sxs-lookup"><span data-stu-id="d04b9-215">\@code</span></span>

<span data-ttu-id="d04b9-216">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-217">`@code` ブロックにより、[Razor コンポーネント](xref:blazor/components)では、C# メンバー (フィールド、プロパティ、メソッド) をコンポーネントに追加できます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="d04b9-218">Razor コンポーネントの`@code`場合は、[`@functions`](#functions)のエイリアスであり`@functions`、上に推奨されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="d04b9-219">複数の `@code` ブロックが許容されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="d04b9-220">\@functions</span><span class="sxs-lookup"><span data-stu-id="d04b9-220">\@functions</span></span>

<span data-ttu-id="d04b9-221">`@functions` ディレクティブでは、生成されたクラスに C# メンバー (フィールド、プロパティ、メソッド) を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d04b9-222">[Razor コンポーネント](xref:blazor/components)では、`@functions` ではなく `@code` を使用して C# メンバーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="d04b9-223">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="d04b9-224">このコードは、次の HTML マークアップを生成します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="d04b9-225">次のコードは、生成された Razor C# クラスです。</span><span class="sxs-lookup"><span data-stu-id="d04b9-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d04b9-226">`@functions` メソッドは、マークアップが与えられているとき、テンプレート メソッドとしてサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-226">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="d04b9-227">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="d04b9-228">\@implements</span><span class="sxs-lookup"><span data-stu-id="d04b9-228">\@implements</span></span>

<span data-ttu-id="d04b9-229">`@implements` ディレクティブでは、生成されたクラスのインターフェイスが実装されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="d04b9-230">次の例では、<xref:System.IDisposable.Dispose*> メソッドを呼び出せるように、<xref:System.IDisposable?displayProperty=fullName> が実装されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a><span data-ttu-id="d04b9-231">\@inherits</span><span class="sxs-lookup"><span data-stu-id="d04b9-231">\@inherits</span></span>

<span data-ttu-id="d04b9-232">`@inherits` ディレクティブは、ビューが継承するクラスの完全な制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="d04b9-233">次のコードは、カスタム Razor ページ型です。</span><span class="sxs-lookup"><span data-stu-id="d04b9-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="d04b9-234">`CustomText` がビューに表示されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="d04b9-235">このコードでは、次のような HTML がレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="d04b9-236">`@model` と `@inherits` は同じビューで使うことができます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="d04b9-237">`@inherits` は、ビューがインポートする *_ViewImports.cshtml* ファイルで指定できます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="d04b9-238">次のコードは、厳密に型指定されたビューの例です。</span><span class="sxs-lookup"><span data-stu-id="d04b9-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="d04b9-239">モデルに rick@contoso.com が渡された場合、ビューは次の HTML マークアップを生成します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="d04b9-240">\@inject</span><span class="sxs-lookup"><span data-stu-id="d04b9-240">\@inject</span></span>

<span data-ttu-id="d04b9-241">`@inject` ディレクティブを使うと、Razor ページで[サービス コンテナー](xref:fundamentals/dependency-injection)からビューにサービスを挿入できます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="d04b9-242">詳しくは、「[ビューへの依存関係の挿入](xref:mvc/views/dependency-injection)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="d04b9-243">\@layout</span><span class="sxs-lookup"><span data-stu-id="d04b9-243">\@layout</span></span>

<span data-ttu-id="d04b9-244">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-245">`@layout` ディレクティブにより、Razor コンポーネントのレイアウトが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="d04b9-246">レイアウト コンポーネントは、コードの重複や不整合を回避するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="d04b9-247">詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="d04b9-248">\@model</span><span class="sxs-lookup"><span data-stu-id="d04b9-248">\@model</span></span>

<span data-ttu-id="d04b9-249">"*このシナリオは、MVC ビューと Razor Pages (.cshtml) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="d04b9-250">`@model` ディレクティブにより、ビューまたはページに渡されるモデルの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="d04b9-251">個々のユーザー アカウントで作成された ASP.NET Core MVC または Razor Pages アプリでは、*Views/Account/Login.cshtml* に次のモデル宣言が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d04b9-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="d04b9-252">生成されるクラスは、`RazorPage<dynamic>` を継承します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="d04b9-253">Razor では、ビューに渡されるモデルにアクセスするための `Model` プロパティが公開されています。</span><span class="sxs-lookup"><span data-stu-id="d04b9-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="d04b9-254">`@model` ディレクティブにより、`Model` プロパティの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="d04b9-255">ディレクティブでは、ビューが派生する生成されたクラスの `T` を `RazorPage<T>` で指定します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="d04b9-256">`@model` ディレクティブが指定されていない場合、`Model` プロパティは `dynamic` 型になります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="d04b9-257">詳細については、「[厳密に型指定されたモデル」@modelおよび「キーワード」を](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="d04b9-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="d04b9-258">\@namespace</span></span>

<span data-ttu-id="d04b9-259">`@namespace` ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="d04b9-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="d04b9-260">生成された Razor ページ、MVC ビュー、または Razor コンポーネントのクラスの名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="d04b9-261">ディレクトリ ツリーで最も近いインポート ファイル (*_ViewImports.cshtml* (ビューまたはページ) または *_Imports.razor* (Razor コンポーネント)) から、ページ、ビュー、またはコンポーネント クラスのルート派生名前空間を設定します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="d04b9-262">次の表に示す Razor Pages の例の場合:</span><span class="sxs-lookup"><span data-stu-id="d04b9-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="d04b9-263">各ページで *Pages/_ViewImports.cshtml* がインポートされます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="d04b9-264">*Pages/_ViewImports.cshtml* に `@namespace Hello.World` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="d04b9-265">各ページには、その名前空間のルートとして `Hello.World` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="d04b9-266">ページ</span><span class="sxs-lookup"><span data-stu-id="d04b9-266">Page</span></span>                                        | <span data-ttu-id="d04b9-267">名前空間</span><span class="sxs-lookup"><span data-stu-id="d04b9-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="d04b9-268">*ページ/インデックス.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="d04b9-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="d04b9-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="d04b9-271">前のリレーションシップは、MVC ビューと Razor コンポーネントで使用されるインポート ファイルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="d04b9-272">複数のインポート ファイルに `@namespace` ディレクティブがあるとき、ディレクトリ ツリーでページ、ビュー、またはコンポーネントに最も近いファイルがルート名前空間の設定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="d04b9-273">前の例の *EvenMorePages* フォルダーに `@namespace Another.Planet` が含まれるインポート ファイルがある場合 (または、*Pages/MorePages/EvenMorePages/Page.cshtml* ファイルに `@namespace Another.Planet` が含まれる場合)、結果は次の表のようになります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="d04b9-274">ページ</span><span class="sxs-lookup"><span data-stu-id="d04b9-274">Page</span></span>                                        | <span data-ttu-id="d04b9-275">名前空間</span><span class="sxs-lookup"><span data-stu-id="d04b9-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="d04b9-276">*ページ/インデックス.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="d04b9-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="d04b9-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="d04b9-279">\@page</span><span class="sxs-lookup"><span data-stu-id="d04b9-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d04b9-280">`@page` ディレクティブには、それが表示されるファイルの型によって、さまざまな効果があります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="d04b9-281">ディレクティブ:</span><span class="sxs-lookup"><span data-stu-id="d04b9-281">The directive:</span></span>

* <span data-ttu-id="d04b9-282">*.cshtml* ファイルでは、ファイルが Razor Page であることを示します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="d04b9-283">詳細については、「[カスタム ルート](xref:razor-pages/index#custom-routes)」と「<xref:razor-pages/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="d04b9-284">Razor コンポーネントで要求を直接処理することを指定します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="d04b9-285">詳細については、「<xref:blazor/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d04b9-286">*.cshtml* ファイルの最初の行にある `@page` ディレクティブは、ファイルが Razor Page であることを示します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="d04b9-287">詳細については、「<xref:razor-pages/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="d04b9-288">\@section</span><span class="sxs-lookup"><span data-stu-id="d04b9-288">\@section</span></span>

<span data-ttu-id="d04b9-289">"*このシナリオは、MVC ビューと Razor Pages (.cshtml) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="d04b9-290">`@section` ディレクティブを [MVC および Razor Pages レイアウト](xref:mvc/views/layout)と組み合わせて使用すると、HTML ページのさまざまな部分のコンテンツをビューやページでレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="d04b9-291">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="d04b9-292">\@using</span><span class="sxs-lookup"><span data-stu-id="d04b9-292">\@using</span></span>

<span data-ttu-id="d04b9-293">`@using` ディレクティブは、生成されるビューに C# の `using` ディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d04b9-294">[Razor コンポーネント](xref:blazor/components)`@using`では、スコープ内にあるコンポーネントも制御します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="d04b9-295">ディレクティブ属性</span><span class="sxs-lookup"><span data-stu-id="d04b9-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="d04b9-296">\@attributes</span><span class="sxs-lookup"><span data-stu-id="d04b9-296">\@attributes</span></span>

<span data-ttu-id="d04b9-297">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-298">`@attributes` では、非宣言属性のレンダリングがコンポーネントに許可されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="d04b9-299">詳細については、「<xref:blazor/components#attribute-splatting-and-arbitrary-parameters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="d04b9-300">\@bind</span><span class="sxs-lookup"><span data-stu-id="d04b9-300">\@bind</span></span>

<span data-ttu-id="d04b9-301">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-302">コンポーネントのデータ バインドは、`@bind` 属性によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="d04b9-303">詳細については、「<xref:blazor/data-binding>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-303">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="d04b9-304">\@on{EVENT}</span><span class="sxs-lookup"><span data-stu-id="d04b9-304">\@on{EVENT}</span></span>

<span data-ttu-id="d04b9-305">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-306">Razor からは、コンポーネントのイベント処理機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="d04b9-307">詳細については、「<xref:blazor/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-307">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="d04b9-308">\@on{EVENT}:preventDefault</span><span class="sxs-lookup"><span data-stu-id="d04b9-308">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="d04b9-309">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-310">イベントの既定のアクションを禁止します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-310">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="d04b9-311">\@on{EVENT}:stopPropagation</span><span class="sxs-lookup"><span data-stu-id="d04b9-311">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="d04b9-312">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-312">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-313">イベントのイベント伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-313">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="d04b9-314">\@key</span><span class="sxs-lookup"><span data-stu-id="d04b9-314">\@key</span></span>

<span data-ttu-id="d04b9-315">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-315">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-316">`@key` ディレクティブ属性により、コンポーネントの比較アルゴリズムは、キーの値に基づいて要素またはコンポーネントの保存を保証します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-316">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="d04b9-317">詳細については、「<xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-317">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="d04b9-318">\@ref</span><span class="sxs-lookup"><span data-stu-id="d04b9-318">\@ref</span></span>

<span data-ttu-id="d04b9-319">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-319">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-320">コンポーネント参照 (`@ref`) からは、コンポーネント インスタンスにコマンドを発行できるように、そのインスタンスを参照する方法が与えられます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-320">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="d04b9-321">詳細については、「<xref:blazor/components#capture-references-to-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-321">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="d04b9-322">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="d04b9-322">\@typeparam</span></span>

<span data-ttu-id="d04b9-323">"*このシナリオは、Razor コンポーネント (.razor) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-323">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="d04b9-324">`@typeparam` ディレクティブによって、生成されるコンポーネント クラスのジェネリック型パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-324">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="d04b9-325">詳細については、「<xref:blazor/templated-components#generic-typed-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d04b9-325">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="d04b9-326">テンプレート化された Razor デリゲート</span><span class="sxs-lookup"><span data-stu-id="d04b9-326">Templated Razor delegates</span></span>

<span data-ttu-id="d04b9-327">Razor テンプレートを使用すると、次の形式で UI スニペットを定義できます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-327">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="d04b9-328">次の例では、テンプレート化された Razor デリゲートを <xref:System.Func%602> として指定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-328">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="d04b9-329">デリゲートによってカプセル化されるメソッドのパラメーターに対しては、[dynamic 型](/dotnet/csharp/programming-guide/types/using-type-dynamic)を指定します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-329">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="d04b9-330">デリゲートの戻り値としては、[object 型](/dotnet/csharp/language-reference/keywords/object)を指定します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-330">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="d04b9-331">テンプレートは、`Name` プロパティを持つ `Pet` の <xref:System.Collections.Generic.List%601> で使用されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-331">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="d04b9-332">テンプレートは、`foreach` ステートメントによって提供される `pets` で表示されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-332">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="d04b9-333">表示される出力:</span><span class="sxs-lookup"><span data-stu-id="d04b9-333">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="d04b9-334">メソッドへの引数としてインライン Razor テンプレートを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-334">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="d04b9-335">次の例では、`Repeat` メソッドは Razor テンプレートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-335">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="d04b9-336">メソッドは、テンプレートを使用して、リストから提供される項目の繰り返しで HTML コンテンツを生成します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-336">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="d04b9-337">前の例のペットのリストを使用して、次のように `Repeat` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-337">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="d04b9-338"><xref:System.Collections.Generic.List%601> の `Pet`。</span><span class="sxs-lookup"><span data-stu-id="d04b9-338"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="d04b9-339">各ペットを繰り返す回数。</span><span class="sxs-lookup"><span data-stu-id="d04b9-339">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="d04b9-340">順不同のリストのリスト項目に対して使用するインライン テンプレート。</span><span class="sxs-lookup"><span data-stu-id="d04b9-340">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="d04b9-341">表示される出力:</span><span class="sxs-lookup"><span data-stu-id="d04b9-341">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="d04b9-342">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="d04b9-342">Tag Helpers</span></span>

<span data-ttu-id="d04b9-343">"*このシナリオは、MVC ビューと Razor Pages (.cshtml) にのみ適用されます。*"</span><span class="sxs-lookup"><span data-stu-id="d04b9-343">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="d04b9-344">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)に関する 3 つのディレクティブがあります。</span><span class="sxs-lookup"><span data-stu-id="d04b9-344">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="d04b9-345">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="d04b9-345">Directive</span></span> | <span data-ttu-id="d04b9-346">関数</span><span class="sxs-lookup"><span data-stu-id="d04b9-346">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="d04b9-347">ビューでタグ ヘルパーを使えるようにします。</span><span class="sxs-lookup"><span data-stu-id="d04b9-347">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="d04b9-348">前に追加したタグ ヘルパーをビューから削除します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-348">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="d04b9-349">タグ プレフィックスを指定して、タグ ヘルパーのサポートを有効にしたり、タグ ヘルパーの使用を明示的にしたりします。</span><span class="sxs-lookup"><span data-stu-id="d04b9-349">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="d04b9-350">Razor の予約済みキーワード</span><span class="sxs-lookup"><span data-stu-id="d04b9-350">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="d04b9-351">Razor のキーワード</span><span class="sxs-lookup"><span data-stu-id="d04b9-351">Razor keywords</span></span>

* <span data-ttu-id="d04b9-352">page (ASP.NET Core 2.1 以降を必要とします)</span><span class="sxs-lookup"><span data-stu-id="d04b9-352">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="d04b9-353">namespace</span><span class="sxs-lookup"><span data-stu-id="d04b9-353">namespace</span></span>
* <span data-ttu-id="d04b9-354">functions</span><span class="sxs-lookup"><span data-stu-id="d04b9-354">functions</span></span>
* <span data-ttu-id="d04b9-355">継承</span><span class="sxs-lookup"><span data-stu-id="d04b9-355">inherits</span></span>
* <span data-ttu-id="d04b9-356">model</span><span class="sxs-lookup"><span data-stu-id="d04b9-356">model</span></span>
* <span data-ttu-id="d04b9-357">section</span><span class="sxs-lookup"><span data-stu-id="d04b9-357">section</span></span>
* <span data-ttu-id="d04b9-358">helper (現在は ASP.NET Core ではサポートされていません)</span><span class="sxs-lookup"><span data-stu-id="d04b9-358">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="d04b9-359">Razor のキーワードは、`@(Razor Keyword)` でエスケープします (例: `@(functions)`)。</span><span class="sxs-lookup"><span data-stu-id="d04b9-359">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="d04b9-360">C# Razor のキーワード</span><span class="sxs-lookup"><span data-stu-id="d04b9-360">C# Razor keywords</span></span>

* <span data-ttu-id="d04b9-361">case</span><span class="sxs-lookup"><span data-stu-id="d04b9-361">case</span></span>
* <span data-ttu-id="d04b9-362">do</span><span class="sxs-lookup"><span data-stu-id="d04b9-362">do</span></span>
* <span data-ttu-id="d04b9-363">default</span><span class="sxs-lookup"><span data-stu-id="d04b9-363">default</span></span>
* <span data-ttu-id="d04b9-364">for</span><span class="sxs-lookup"><span data-stu-id="d04b9-364">for</span></span>
* <span data-ttu-id="d04b9-365">foreach</span><span class="sxs-lookup"><span data-stu-id="d04b9-365">foreach</span></span>
* <span data-ttu-id="d04b9-366">if</span><span class="sxs-lookup"><span data-stu-id="d04b9-366">if</span></span>
* <span data-ttu-id="d04b9-367">else</span><span class="sxs-lookup"><span data-stu-id="d04b9-367">else</span></span>
* <span data-ttu-id="d04b9-368">ロック (lock)</span><span class="sxs-lookup"><span data-stu-id="d04b9-368">lock</span></span>
* <span data-ttu-id="d04b9-369">switch</span><span class="sxs-lookup"><span data-stu-id="d04b9-369">switch</span></span>
* <span data-ttu-id="d04b9-370">試す</span><span class="sxs-lookup"><span data-stu-id="d04b9-370">try</span></span>
* <span data-ttu-id="d04b9-371">catch</span><span class="sxs-lookup"><span data-stu-id="d04b9-371">catch</span></span>
* <span data-ttu-id="d04b9-372">finally</span><span class="sxs-lookup"><span data-stu-id="d04b9-372">finally</span></span>
* <span data-ttu-id="d04b9-373">using</span><span class="sxs-lookup"><span data-stu-id="d04b9-373">using</span></span>
* <span data-ttu-id="d04b9-374">while</span><span class="sxs-lookup"><span data-stu-id="d04b9-374">while</span></span>

<span data-ttu-id="d04b9-375">C# Razor のキーワードは、`@(@C# Razor Keyword)` で二重にエスケープする必要があります (例: `@(@case)`)。</span><span class="sxs-lookup"><span data-stu-id="d04b9-375">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="d04b9-376">最初の `@` は、Razor パーサーをエスケープします。</span><span class="sxs-lookup"><span data-stu-id="d04b9-376">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="d04b9-377">2 番目の `@` は、C# パーサーをエスケープします。</span><span class="sxs-lookup"><span data-stu-id="d04b9-377">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="d04b9-378">Razor で使われない予約済みキーワード</span><span class="sxs-lookup"><span data-stu-id="d04b9-378">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="d04b9-379">class</span><span class="sxs-lookup"><span data-stu-id="d04b9-379">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="d04b9-380">ビューに対して生成された Razor C# クラスを調べる</span><span class="sxs-lookup"><span data-stu-id="d04b9-380">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="d04b9-381">.NET Core SDK 2.1 以降、[Razor SDK](xref:razor-pages/sdk) は Razor ファイルのコンパイルを処理します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-381">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="d04b9-382">プロジェクトを作成する際に、Razor SDK はプロジェクト ルートに *obj/<build_configuration>/<target_framework_moniker>/Razor* ディレクトリを生成します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-382">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="d04b9-383">*Razor* ディレクトリ内のディレクトリ構造は、プロジェクトのディレクトリ構造をミラー化します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-383">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="d04b9-384">.NET Core 2.1 をターゲットとする ASP.NET Core 2.1 Razor Pages プロジェクト内の次のディレクトリ構造を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="d04b9-384">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="d04b9-385">**エリア/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-385">**Areas/**</span></span>
  * <span data-ttu-id="d04b9-386">**管理者/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-386">**Admin/**</span></span>
    * <span data-ttu-id="d04b9-387">**ページ/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-387">**Pages/**</span></span>
      * <span data-ttu-id="d04b9-388">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-388">*Index.cshtml*</span></span>
      * <span data-ttu-id="d04b9-389">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d04b9-389">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="d04b9-390">**ページ/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-390">**Pages/**</span></span>
  * <span data-ttu-id="d04b9-391">**共有/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-391">**Shared/**</span></span>
    * <span data-ttu-id="d04b9-392">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-392">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="d04b9-393">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-393">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="d04b9-394">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-394">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="d04b9-395">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d04b9-395">*Index.cshtml*</span></span>
  * <span data-ttu-id="d04b9-396">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d04b9-396">*Index.cshtml.cs*</span></span>

<span data-ttu-id="d04b9-397">*Debug* 構成でプロジェクトを作成すると、次の *obj* ディレクトリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-397">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="d04b9-398">**obj/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-398">**obj/**</span></span>
  * <span data-ttu-id="d04b9-399">**デバッグ/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-399">**Debug/**</span></span>
    * <span data-ttu-id="d04b9-400">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-400">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="d04b9-401">**カミソリ/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-401">**Razor/**</span></span>
        * <span data-ttu-id="d04b9-402">**エリア/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-402">**Areas/**</span></span>
          * <span data-ttu-id="d04b9-403">**管理者/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-403">**Admin/**</span></span>
            * <span data-ttu-id="d04b9-404">**ページ/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-404">**Pages/**</span></span>
              * <span data-ttu-id="d04b9-405">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d04b9-405">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="d04b9-406">**ページ/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-406">**Pages/**</span></span>
          * <span data-ttu-id="d04b9-407">**共有/**</span><span class="sxs-lookup"><span data-stu-id="d04b9-407">**Shared/**</span></span>
            * <span data-ttu-id="d04b9-408">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d04b9-408">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="d04b9-409">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d04b9-409">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="d04b9-410">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d04b9-410">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="d04b9-411">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d04b9-411">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="d04b9-412">*Pages/Index.cshtml* に対して生成されたクラスを表示するには、*obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs* を開きます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-412">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="d04b9-413">次のクラスを ASP.NET Core MVC プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-413">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="d04b9-414">`Startup.ConfigureServices` で、MVC によって追加された `RazorTemplateEngine` を `CustomTemplateEngine` クラスでオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d04b9-414">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="d04b9-415">`CustomTemplateEngine` の `return csharpDocument;` ステートメントにブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-415">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="d04b9-416">プログラムの実行がブレークポイントで停止したら、`generatedCode` の値を表示します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-416">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode のテキスト ビジュアライザーの表示](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="d04b9-418">ビューの参照と大文字/小文字の区別</span><span class="sxs-lookup"><span data-stu-id="d04b9-418">View lookups and case sensitivity</span></span>

<span data-ttu-id="d04b9-419">Razor ビュー エンジンによるビューの参照では、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-419">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="d04b9-420">ただし、実際の参照は、基になるファイル システムによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-420">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="d04b9-421">ファイル ベースのソース:</span><span class="sxs-lookup"><span data-stu-id="d04b9-421">File based source:</span></span>
  * <span data-ttu-id="d04b9-422">大文字と小文字が区別されないファイル システムを使っているオペレーティング システム (Windows など) では、物理的なファイル プロバイダーの参照は大文字と小文字を区別しません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-422">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="d04b9-423">たとえば、`return View("Test")` は、*/Views/Home/Test.cshtml*、*/Views/home/test.cshtml*、その他のすべての大文字と小文字のバリエーションと一致します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-423">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="d04b9-424">大文字と小文字が区別されるファイル システム (たとえば、Linux、OSX、および `EmbeddedFileProvider`) では、参照は大文字と小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-424">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="d04b9-425">たとえば、`return View("Test")` は */Views/Home/Test.cshtml* だけと一致します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-425">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="d04b9-426">プリコンパイル済みのビュー: ASP.NET Core 2.0 以降では、プリコンパイル済みのビューの参照は、すべてのオペレーティング システムで大文字と小文字を区別しません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-426">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="d04b9-427">動作は、Windows での物理ファイル プロバイダーの動作と同じです。</span><span class="sxs-lookup"><span data-stu-id="d04b9-427">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="d04b9-428">2 つのプリコンパイル済みビューの相違点が大文字と小文字の使い分けだけの場合、参照の結果はどちらになるかわかりません。</span><span class="sxs-lookup"><span data-stu-id="d04b9-428">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="d04b9-429">開発者には、ファイル名とディレクトリ名の大文字/小文字の使い分けを、次のものと一致させることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d04b9-429">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="d04b9-430">領域、コントローラー、アクションの名前。</span><span class="sxs-lookup"><span data-stu-id="d04b9-430">Area, controller, and action names.</span></span>
* <span data-ttu-id="d04b9-431">Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="d04b9-431">Razor Pages.</span></span>

<span data-ttu-id="d04b9-432">大文字と小文字の使い分けを一致させると、展開は基になっているファイル システムに関係なくビューを検索できます。</span><span class="sxs-lookup"><span data-stu-id="d04b9-432">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d04b9-433">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="d04b9-433">Additional resources</span></span>

<span data-ttu-id="d04b9-434">[Razor 構文を使用した web プログラミングのASP.NETの概要](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c)では、Razor 構文を使用したプログラミングの多くのサンプルを提供します。</span><span class="sxs-lookup"><span data-stu-id="d04b9-434">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
