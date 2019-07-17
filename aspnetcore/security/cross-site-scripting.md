---
title: ASP.NET Core でのクロスサイトスクリプティング (XSS) の防止
author: rick-anderson
description: クロス サイト スクリプティング (XSS) と ASP.NET Core アプリでこの脆弱性に対処する方法について説明します。
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1e9e988be68313cfd493832519c1be89335d6e48
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815211"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="72ff9-103">ASP.NET Core でのクロスサイトスクリプティング (XSS) の防止</span><span class="sxs-lookup"><span data-stu-id="72ff9-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="72ff9-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="72ff9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="72ff9-105">クロスサイトスクリプティング（XSS）は、攻撃者がクライアントサイドのスクリプト (一般的に JavaScript）を Web ページに配置できるようにするセキュリティの脆弱性です。</span><span class="sxs-lookup"><span data-stu-id="72ff9-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="72ff9-106">他のユーザーが影響を受けたページを読み込むと、攻撃者のスクリプトが実行され、攻撃者は Cookie やセッションのトークンを読み取り、DOM の操作を通じて Web ページのコンテンツを変更したり、ブラウザーを別のページにリダイレクトできます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="72ff9-107">アプリケーションがユーザー入力を受け取り、それを検証やエンコード、またはエスケープをしないでページを出力すると、XSS の脆弱性が発生します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="72ff9-108">XSS からアプリケーションを保護</span><span class="sxs-lookup"><span data-stu-id="72ff9-108">Protecting your application against XSS</span></span>

<span data-ttu-id="72ff9-109">基本的には、レンダリングされたページに `<script>` タグを挿入することでアプリを操作したり、 `On*` イベント を要素に挿入することで、XSS は動作します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="72ff9-110">開発者は、アプリに XSS が導入されないように次の防止策を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="72ff9-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="72ff9-111">以下の残りの手順に従わない限り、信頼できないデータを HTML の入力に入れるべきではありません。</span><span class="sxs-lookup"><span data-stu-id="72ff9-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="72ff9-112">信頼できないデータとは、攻撃者によって制御される可能性があるデータで、HTML のフォームの入力、クエリ文字列、HTTP ヘッダーです。また、攻撃者がアプリケーションを侵害できなくてもデータベースを侵害する可能性があるため、データベースから取得したデータソースもです。</span><span class="sxs-lookup"><span data-stu-id="72ff9-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="72ff9-113">信頼できないデータを HTML の要素の中に配置する前に、 HTML エンコードがされていることを確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="72ff9-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="72ff9-114">HTML エンコーディングは、 < といった文字列を &lt; のような安全な文字列に変換します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="72ff9-115">信頼できないデータを HTML の属性の中に配置する前に、 HTML エンコードがされていることを確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="72ff9-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="72ff9-116">HTML 属性のエンコーディングは HTML エンコーディングの上位互換であり、 " や ' といった追加文字列をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="72ff9-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="72ff9-117">信頼できないデータを JavaScript に入れる前に、実行時に内容を取得する HTML 要素にデータを配置します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="72ff9-118">それができなければ、データが JavaScript でエンコードされていることを確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="72ff9-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="72ff9-119">JavaScript のエンコーディングは、JavaScript にとって危険な文字列をとり、それらを16進数に変換します。例えば、&lt; は `\u003C` にエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="72ff9-120">信頼できないデータを URL クエリ文字列に入れる前に、URL エンコードされていることを確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="72ff9-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="72ff9-121">Razor を使用して HTML エンコード</span><span class="sxs-lookup"><span data-stu-id="72ff9-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="72ff9-122">MVC での使用を自動的に Razor エンジンがすべてをエンコード大変そうように作業する場合を除き、変数の出力に属しています。</span><span class="sxs-lookup"><span data-stu-id="72ff9-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="72ff9-123">使用するたびに、HTML 属性エンコードの規則を使用して、 *@* ディレクティブ。</span><span class="sxs-lookup"><span data-stu-id="72ff9-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="72ff9-124">HTML として HTML エンコード、つまり、HTML エンコードまたは HTML 属性エンコードを使用する必要があるかどうかを検討することをお持ちのスーパー セットは、属性のエンコーディングします。</span><span class="sxs-lookup"><span data-stu-id="72ff9-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="72ff9-125">のみを使用するコンテキストでは、HTML、JavaScript に直接信頼されていない入力を挿入しようとしています。 ときではなくすることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="72ff9-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="72ff9-126">タグ ヘルパーは、タグのパラメーターで使用する入力をエンコードしても。</span><span class="sxs-lookup"><span data-stu-id="72ff9-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="72ff9-127">次の Razor ビューを実行します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="72ff9-128">このビューの内容を出力する、 *untrustedInput*変数。</span><span class="sxs-lookup"><span data-stu-id="72ff9-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="72ff9-129">この変数には、つまり、XSS 攻撃に使用されるいくつかの文字が含まれています。 &lt;、"と&gt;します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="72ff9-130">ソースを調べるには、エンコードとして表示される出力は示しています。</span><span class="sxs-lookup"><span data-stu-id="72ff9-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="72ff9-131">ASP.NET Core MVC の提供、`HtmlString`出力時に自動的にエンコードされていないクラス。</span><span class="sxs-lookup"><span data-stu-id="72ff9-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="72ff9-132">これは、ことはありません使用してください信頼できない入力と組み合わせて、これが XSS の脆弱性を公開します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="72ff9-133">Razor を使用して JavaScript のエンコード</span><span class="sxs-lookup"><span data-stu-id="72ff9-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="72ff9-134">ビューで処理する JavaScript に値を挿入することがあります。</span><span class="sxs-lookup"><span data-stu-id="72ff9-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="72ff9-135">これには、2 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="72ff9-135">There are two ways to do this.</span></span> <span data-ttu-id="72ff9-136">値を挿入する最も安全な方法では、タグのデータ属性に値を配置し、JavaScript で取得します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="72ff9-137">例えば:</span><span class="sxs-lookup"><span data-stu-id="72ff9-137">For example:</span></span>

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

<span data-ttu-id="72ff9-138">次の HTML が生成されます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-138">This will produce the following HTML</span></span>

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

<span data-ttu-id="72ff9-139">これは、実行時にレンダリングされます以下。</span><span class="sxs-lookup"><span data-stu-id="72ff9-139">Which, when it runs, will render the following:</span></span>

```none
<"123">
   <"123">
   ```

<span data-ttu-id="72ff9-140">JavaScript エンコーダーを直接呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-140">You can also call the JavaScript encoder directly:</span></span>

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

<span data-ttu-id="72ff9-141">次のように、ブラウザーでレンダリングこのされます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-141">This will render in the browser as follows:</span></span>

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> <span data-ttu-id="72ff9-142">DOM 要素を作成する JavaScript での信頼されていない入力は連結しません。</span><span class="sxs-lookup"><span data-stu-id="72ff9-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="72ff9-143">使用する必要があります`createElement()`プロパティの値を次のように適切に割り当てると`node.TextContent=`、使用または`element.SetAttribute()` / `element[attribute]=` xss の DOM ベースの自分をさらすそれ以外の場合。</span><span class="sxs-lookup"><span data-stu-id="72ff9-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="72ff9-144">コード内のエンコーダーへのアクセス</span><span class="sxs-lookup"><span data-stu-id="72ff9-144">Accessing encoders in code</span></span>

<span data-ttu-id="72ff9-145">HTML、JavaScript、および URL のエンコーダーは 2 つの方法でコードに使用できるを使用してそれらを挿入できる[依存関係の注入](xref:fundamentals/dependency-injection)に含まれている既定のエンコーダーを使用することも、`System.Text.Encodings.Web`名前空間。</span><span class="sxs-lookup"><span data-stu-id="72ff9-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="72ff9-146">適用するいずれかの既定のエンコーダーを使用する場合は、安全として扱われる文字の範囲が有効になります - 既定のエンコーダーが可能な最も安全なエンコードの規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="72ff9-147">DI は、コンス トラクターを受け取る必要がありますを使用して構成可能なエンコーダーを使用する、 *HtmlEncoder*、 *JavaScriptEncoder*と*UrlEncoder*として適切なパラメーター。</span><span class="sxs-lookup"><span data-stu-id="72ff9-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="72ff9-148">たとえば、</span><span class="sxs-lookup"><span data-stu-id="72ff9-148">For example;</span></span>

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

## <a name="encoding-url-parameters"></a><span data-ttu-id="72ff9-149">URL パラメーターのエンコード</span><span class="sxs-lookup"><span data-stu-id="72ff9-149">Encoding URL Parameters</span></span>

<span data-ttu-id="72ff9-150">信頼できない入力値の使用として使用して URL クエリ文字列を構築する場合、`UrlEncoder`値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="72ff9-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="72ff9-151">例えば以下のようにします。</span><span class="sxs-lookup"><span data-stu-id="72ff9-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="72ff9-152">変数が含まれる、encodedValue エンコード後`%22Quoted%20Value%20with%20spaces%20and%20%26%22`します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="72ff9-153">スペース、引用符、句読点、およびその他の安全でない文字パーセント エンコードされる 16 進数の値に、たとえば空白文字が 20% になります。</span><span class="sxs-lookup"><span data-stu-id="72ff9-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="72ff9-154">URL パスの一部として、信頼できない入力を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="72ff9-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="72ff9-155">常に信頼されていない入力を渡すクエリ文字列の値として。</span><span class="sxs-lookup"><span data-stu-id="72ff9-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="72ff9-156">エンコーダーのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="72ff9-156">Customizing the Encoders</span></span>

<span data-ttu-id="72ff9-157">既定では、エンコーダーは、基本的なラテン文字 Unicode 範囲が限定セーフ リストを使用しており、同等の文字コードとしての範囲にないすべての文字をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="72ff9-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="72ff9-158">この動作を文字列を出力するエンコーダーを使用すると、Razor TagHelper と HtmlHelper のレンダリングも影響します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="72ff9-159">この理由は、不明または将来のブラウザーのバグ (以前のブラウザーのバグが英語以外の文字の処理に基づく解析をトリップした) から保護するためです。</span><span class="sxs-lookup"><span data-stu-id="72ff9-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="72ff9-160">Web サイトは、中国語などのラテン語以外の文字を頻繁に使用場合、キリル文字、またはその他のユーザーはおそらく目的の動作をします。</span><span class="sxs-lookup"><span data-stu-id="72ff9-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="72ff9-161">Unicode で、起動時に、アプリケーションに適切な範囲を含めるエンコーダーのセーフ リストをカスタマイズする`ConfigureServices()`します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="72ff9-162">たとえば、Razor HtmlHelper を使用する既定の構成を使用して次のようにします。</span><span class="sxs-lookup"><span data-stu-id="72ff9-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="72ff9-163">Web ページのソースを表示すると、中国語のテキストがエンコードされていると、次のように表示されたが表示されます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="72ff9-164">として扱われます、文字幅を広げないエンコーダーによって安全な挿入するには、次の行、`ConfigureServices()`メソッド`startup.cs`;</span><span class="sxs-lookup"><span data-stu-id="72ff9-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="72ff9-165">この例では、セーフ リストに含める、Unicode 範囲 CjkUnifiedIdeographs 拡大変換されます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="72ff9-166">レンダリングされた出力になるようになりました</span><span class="sxs-lookup"><span data-stu-id="72ff9-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="72ff9-167">セーフ リストの範囲は、Unicode コード値表、いない言語として指定されます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="72ff9-168">[Unicode 標準](https://unicode.org/)のリストを持つ[コード グラフ](https://www.unicode.org/charts/index.html)文字を含むグラフの検索に使用できます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="72ff9-169">各エンコーダーは、Html、JavaScript、および Url は、個別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="72ff9-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="72ff9-170">セーフ リストのカスタマイズには、エンコーダーは DI を使用してソースのみに影響します。</span><span class="sxs-lookup"><span data-stu-id="72ff9-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="72ff9-171">使用してエンコーダーに直接アクセスする場合`System.Text.Encodings.Web.*Encoder.Default`し、既定、Basic Latin セーフリストのみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="72ff9-172">エンコードの実行を配置する必要がありますか。</span><span class="sxs-lookup"><span data-stu-id="72ff9-172">Where should encoding take place?</span></span>

<span data-ttu-id="72ff9-173">一般には、エンコードが行われる出力の時点でとエンコードされた値をデータベースに格納しない必要がありますのことをお勧めが受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="72ff9-174">エンコード出力の時点で、クエリ文字列値を HTML からのデータなどの使用を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="72ff9-175">検索する前に値をエンコードすることがなく、データを簡単に検索することができ、変更やエンコーダーに加えられたバグの修正を活用することができます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="72ff9-176">XSS 防止手法として検証</span><span class="sxs-lookup"><span data-stu-id="72ff9-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="72ff9-177">検証では、XSS 攻撃を制限することで便利なツールを指定できます。</span><span class="sxs-lookup"><span data-stu-id="72ff9-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="72ff9-178">たとえば、文字 0 ~ 9 のみを含む数値文字列では、XSS 攻撃をトリガーしません。</span><span class="sxs-lookup"><span data-stu-id="72ff9-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="72ff9-179">ユーザー入力に HTML を受け入れるときに、検証が複雑になります。</span><span class="sxs-lookup"><span data-stu-id="72ff9-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="72ff9-180">HTML 入力を解析することは、不可能でない場合は困難です。</span><span class="sxs-lookup"><span data-stu-id="72ff9-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="72ff9-181">埋め込みの HTML を取り除くパーサーと組み合わせると、markdown は、豊富な入力を受け入れるためのより安全なオプションです。</span><span class="sxs-lookup"><span data-stu-id="72ff9-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="72ff9-182">単独で検証に依存しないようにします。</span><span class="sxs-lookup"><span data-stu-id="72ff9-182">Never rely on validation alone.</span></span> <span data-ttu-id="72ff9-183">どのような検証または消去が実行されても、出力する前に信頼されていない入力は常にエンコードします。</span><span class="sxs-lookup"><span data-stu-id="72ff9-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
