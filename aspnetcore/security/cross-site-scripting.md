---
title: ASP.NET Core でのクロスサイトスクリプティング (XSS) の防止
author: rick-anderson
description: クロス サイト スクリプティング (XSS) と ASP.NET Core アプリでこの脆弱性に対処する方法について説明します。
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1d6f605dc336d8768b8a47e4995f119d198a61af
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655076"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="92d80-103">ASP.NET Core でのクロスサイトスクリプティング (XSS) の防止</span><span class="sxs-lookup"><span data-stu-id="92d80-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="92d80-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92d80-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="92d80-105">クロスサイトスクリプティング（XSS）は、攻撃者がクライアントサイドのスクリプト (一般的に JavaScript）を Web ページに配置できるようにするセキュリティの脆弱性です。</span><span class="sxs-lookup"><span data-stu-id="92d80-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="92d80-106">他のユーザーが影響を受けたページを読み込むと、攻撃者のスクリプトが実行され、攻撃者は Cookie やセッションのトークンを読み取り、DOM の操作を通じて Web ページのコンテンツを変更したり、ブラウザーを別のページにリダイレクトできます。</span><span class="sxs-lookup"><span data-stu-id="92d80-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="92d80-107">アプリケーションがユーザー入力を受け取り、それを検証やエンコード、またはエスケープをしないでページを出力すると、XSS の脆弱性が発生します。</span><span class="sxs-lookup"><span data-stu-id="92d80-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="92d80-108">XSS からアプリケーションを保護</span><span class="sxs-lookup"><span data-stu-id="92d80-108">Protecting your application against XSS</span></span>

<span data-ttu-id="92d80-109">基本的なレベルの XSS では、アプリケーションを欺いして、レンダリングされたページに `<script>` タグを挿入したり、`On*` イベントを要素に挿入したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="92d80-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="92d80-110">開発者は、アプリに XSS が導入されないように次の防止策を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="92d80-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="92d80-111">以下の残りの手順に従わない限り、信頼できないデータを HTML の入力に入れるべきではありません。</span><span class="sxs-lookup"><span data-stu-id="92d80-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="92d80-112">信頼できないデータとは、攻撃者によって制御される可能性があるデータで、HTML のフォームの入力、クエリ文字列、HTTP ヘッダーです。また、攻撃者がアプリケーションを侵害できなくてもデータベースを侵害する可能性があるため、データベースから取得したデータソースもです。</span><span class="sxs-lookup"><span data-stu-id="92d80-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="92d80-113">信頼できないデータを HTML の要素の中に配置する前に、 HTML エンコードがされていることを確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="92d80-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="92d80-114">HTML エンコードは、&lt; などの文字を受け取り、&amp;lt; のように安全な形式に変更します。</span><span class="sxs-lookup"><span data-stu-id="92d80-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="92d80-115">信頼できないデータを HTML の属性の中に配置する前に、 HTML エンコードがされていることを確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="92d80-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="92d80-116">HTML 属性のエンコーディングは HTML エンコーディングの上位互換であり、 " や ' といった追加文字列をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="92d80-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="92d80-117">信頼できないデータを JavaScript に入れる前に、実行時に内容を取得する HTML 要素にデータを配置します。</span><span class="sxs-lookup"><span data-stu-id="92d80-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="92d80-118">それができなければ、データが JavaScript でエンコードされていることを確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="92d80-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="92d80-119">Javascript のエンコードでは、JavaScript では危険な文字が使用され、16進数に置き換えられます。たとえば、&lt; は `\u003C`としてエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="92d80-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="92d80-120">信頼できないデータを URL クエリ文字列に入れる前に、URL エンコードされていることを確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="92d80-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="92d80-121">Razor を使用した HTML エンコード</span><span class="sxs-lookup"><span data-stu-id="92d80-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="92d80-122">MVC で使われている Razor のエンジンは、自身でそうするのを防がない限り、変数から供給されるすべての出力を自動的にエンコードします。</span><span class="sxs-lookup"><span data-stu-id="92d80-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="92d80-123">*@* ディレクティブを使用する場合は、常に HTML 属性のエンコード規則が使用されます。</span><span class="sxs-lookup"><span data-stu-id="92d80-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="92d80-124">HTML 属性エンコーディングが HTML エンコーディングの上位互換であるため、HTML 属性エンコーディングが HTML エンコーディングのどちらを使うか気にする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="92d80-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="92d80-125">信頼できない入力データを JavaScript に直接代入するのではなく、必ず HTML コンテキストの中で @を使うよう確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="92d80-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="92d80-126">TagHelper もまた、タグのパラメーターで使う入力をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="92d80-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="92d80-127">次の Razor ビューを実行します。</span><span class="sxs-lookup"><span data-stu-id="92d80-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="92d80-128">このビューは、 *Untrustedinput*変数の内容を出力します。</span><span class="sxs-lookup"><span data-stu-id="92d80-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="92d80-129">この変数には、XSS 攻撃で使用される文字 (&lt;、&gt;) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="92d80-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="92d80-130">ソースを実行すると、次のようにエンコードされた出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="92d80-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="92d80-131">MVC ASP.NET Core は、出力時に自動的にエンコードされない `HtmlString` クラスを提供します。</span><span class="sxs-lookup"><span data-stu-id="92d80-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="92d80-132">これは、XSS の脆弱性が露呈するため、信頼できない入力と組み合わせて使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="92d80-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="92d80-133">Razor を使用した JavaScript のエンコード</span><span class="sxs-lookup"><span data-stu-id="92d80-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="92d80-134">ビューで処理するのに Javascript に値を代入したい場合があるとします。</span><span class="sxs-lookup"><span data-stu-id="92d80-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="92d80-135">2 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="92d80-135">There are two ways to do this.</span></span> <span data-ttu-id="92d80-136">値を代入するのに最も安全な方法は、タグのデータ属性の中に値を配置して JavaScript で読み取ることです。</span><span class="sxs-lookup"><span data-stu-id="92d80-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="92d80-137">例 :</span><span class="sxs-lookup"><span data-stu-id="92d80-137">For example:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="92d80-138">これにより、次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="92d80-138">This will produce the following HTML</span></span>

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="92d80-139">実行すると、次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="92d80-139">Which, when it runs, will render the following:</span></span>

```
<"123">
   <"123">
```

<span data-ttu-id="92d80-140">JavaScript のエンコーダーを直接呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="92d80-140">You can also call the JavaScript encoder directly:</span></span>

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

<span data-ttu-id="92d80-141">ブラウザーで次のようにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="92d80-141">This will render in the browser as follows:</span></span>

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> <span data-ttu-id="92d80-142">DOM の要素を作成するのに、信頼できない入力を JavaScript で連結しないでください。</span><span class="sxs-lookup"><span data-stu-id="92d80-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="92d80-143">`createElement()` を使用し、`node.TextContent=`などのプロパティ値を適切に割り当てるか `element.SetAttribute()``element[attribute]=` /を使用する必要があります。それ以外の場合は、DOM ベースの XSS に公開します。</span><span class="sxs-lookup"><span data-stu-id="92d80-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="92d80-144">コード内のエンコーダーへのアクセス</span><span class="sxs-lookup"><span data-stu-id="92d80-144">Accessing encoders in code</span></span>

<span data-ttu-id="92d80-145">HTML、JavaScript、および URL エンコーダーは、2つの方法でコードで使用できます。[依存関係の挿入](xref:fundamentals/dependency-injection)を使用して挿入することも、`System.Text.Encodings.Web` 名前空間に含まれる既定のエンコーダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="92d80-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="92d80-146">デフォルトのエンコーダーを使用する場合、安全として扱われる文字範囲を適用した場合は、効果が出ません - デフォルトのエンコーダーでは可能な限り最も安全なエンコード規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="92d80-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="92d80-147">DI を使用して構成可能なエンコーダーを使用するには、コンストラクターが*htmlencoder*、 *JavaScriptEncoder* 、および*urlencoder*パラメーターを必要に応じて受け取る必要があります。</span><span class="sxs-lookup"><span data-stu-id="92d80-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="92d80-148">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="92d80-148">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="92d80-149">エンコード URL パラメーター</span><span class="sxs-lookup"><span data-stu-id="92d80-149">Encoding URL Parameters</span></span>

<span data-ttu-id="92d80-150">信頼できない入力の URL クエリ文字列を値として作成する場合は、`UrlEncoder` を使用して値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="92d80-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="92d80-151">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="92d80-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="92d80-152">エンコード後に、エンコード値の変数には `%22Quoted%20Value%20with%20spaces%20and%20%26%22`が含まれます。</span><span class="sxs-lookup"><span data-stu-id="92d80-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="92d80-153">スペース、引用符、句読点やその他の安全でない文字は、16 進数の値にパーセントエンコードされます。たとえばスペースの文字は ％20 になります。</span><span class="sxs-lookup"><span data-stu-id="92d80-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="92d80-154">URL のパスに信頼できない入力を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="92d80-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="92d80-155">信頼できない入力は、必ずクエリ文字列として渡します。</span><span class="sxs-lookup"><span data-stu-id="92d80-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="92d80-156">エンコーダーのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="92d80-156">Customizing the Encoders</span></span>

<span data-ttu-id="92d80-157">デフォルトでは、エンコーダーは Basic Latin Unicode の範囲の制限されたセーフリストを使用し、その範囲外の全ての文字を文字コードにエンコードします。</span><span class="sxs-lookup"><span data-stu-id="92d80-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="92d80-158">この動作は、文字列を出力するのにエンコーダーを使っているのため、Razor の TagHelper と HtmlHelper のレンダリングに影響します。</span><span class="sxs-lookup"><span data-stu-id="92d80-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="92d80-159">この理由は、未知または将来のブラウザーのバグから守るためです（過去に、ブラウザーのバグで、英語ではない文字の処理の解析に失敗しました）。</span><span class="sxs-lookup"><span data-stu-id="92d80-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="92d80-160">もしウェブサイトが中国語やキリル文字などラテン語以外の文字を多く利用している場合、これはあなたの望む動作ではないでしょう。</span><span class="sxs-lookup"><span data-stu-id="92d80-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="92d80-161">エンコーダーセーフリストをカスタマイズして、起動時にアプリケーションに適した Unicode 範囲を `ConfigureServices()`に含めることができます。</span><span class="sxs-lookup"><span data-stu-id="92d80-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="92d80-162">例として、デフォルトの設定で Razor の HtmlHelper を使用することができます;</span><span class="sxs-lookup"><span data-stu-id="92d80-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="92d80-163">Web ページのソースを見ると、中国語のテキストがエンコードされ、次のようにレンダリングされています。</span><span class="sxs-lookup"><span data-stu-id="92d80-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="92d80-164">エンコーダーによって安全として扱われる文字を拡大するには、`startup.cs`の `ConfigureServices()` メソッドに次の行を挿入します。</span><span class="sxs-lookup"><span data-stu-id="92d80-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="92d80-165">この例では、CjkUnifiedIdeographs の範囲の Unicode を含むセーフリストに拡張しました。</span><span class="sxs-lookup"><span data-stu-id="92d80-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="92d80-166">レンダリングされた出力は次のようになります;</span><span class="sxs-lookup"><span data-stu-id="92d80-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="92d80-167">セーフリストは、言語ではなく Unicode のコード表として指定されています。</span><span class="sxs-lookup"><span data-stu-id="92d80-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="92d80-168">[Unicode 規格](https://unicode.org/)には、文字を含むグラフを検索するために使用できる[コード表](https://www.unicode.org/charts/index.html)の一覧があります。</span><span class="sxs-lookup"><span data-stu-id="92d80-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="92d80-169">HTML、JavaScript、URL のそれぞれのエンコーダーで別々に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="92d80-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="92d80-170">セーフリストのカスタマイズは、 DI 経由でのエンコーダーのみに影響します。</span><span class="sxs-lookup"><span data-stu-id="92d80-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="92d80-171">`System.Text.Encodings.Web.*Encoder.Default` によってエンコーダーに直接アクセスした場合は、既定の基本的なラテン only セーフセーフセーフポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="92d80-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="92d80-172">エンコードはどこで行われるべきか？</span><span class="sxs-lookup"><span data-stu-id="92d80-172">Where should encoding take place?</span></span>

<span data-ttu-id="92d80-173">一般的に受け入れられている手法として、エンコードは出力の時に行い、エンコードされた値をデータベースに保存すべきではありません。</span><span class="sxs-lookup"><span data-stu-id="92d80-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="92d80-174">出力時のエンコーディングをすることで、データの用途を変更できます。例えば、 HTML からクエリ文字列の値への変換です。</span><span class="sxs-lookup"><span data-stu-id="92d80-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="92d80-175">検索する前に値をエンコードすることがなくデータを簡単に検索することができ、変更やエンコーダーに加えられたバグの修正を活用することができます。</span><span class="sxs-lookup"><span data-stu-id="92d80-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="92d80-176">XSS 防止の手法としての検証</span><span class="sxs-lookup"><span data-stu-id="92d80-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="92d80-177">検証は、XSS 攻撃を制限するのに役立つツールです。</span><span class="sxs-lookup"><span data-stu-id="92d80-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="92d80-178">0-9だけが含まれる数字の文字では、XSS を引き起こすことができません。</span><span class="sxs-lookup"><span data-stu-id="92d80-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="92d80-179">HTML がユーザーに入力できる状態だと、検証はより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="92d80-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="92d80-180">できないことはありませんが、HTML の入力の解析は困難です。</span><span class="sxs-lookup"><span data-stu-id="92d80-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="92d80-181">Markdown は、埋め込まれた HTML を取り除くパーサーが結合されているので、リッチな入力を受け入れるためのより安全なオプションです。</span><span class="sxs-lookup"><span data-stu-id="92d80-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="92d80-182">しかし、検証だけに頼らないでください。</span><span class="sxs-lookup"><span data-stu-id="92d80-182">Never rely on validation alone.</span></span> <span data-ttu-id="92d80-183">検証やサニタイズが実行されていても、常に出力の前に信頼できない入力をエンコードしましょう。</span><span class="sxs-lookup"><span data-stu-id="92d80-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
