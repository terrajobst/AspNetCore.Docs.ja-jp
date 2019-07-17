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
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>ASP.NET Core でのクロスサイトスクリプティング (XSS) の防止

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

クロスサイトスクリプティング（XSS）は、攻撃者がクライアントサイドのスクリプト (一般的に JavaScript）を Web ページに配置できるようにするセキュリティの脆弱性です。 他のユーザーが影響を受けたページを読み込むと、攻撃者のスクリプトが実行され、攻撃者は Cookie やセッションのトークンを読み取り、DOM の操作を通じて Web ページのコンテンツを変更したり、ブラウザーを別のページにリダイレクトできます。 アプリケーションがユーザー入力を受け取り、それを検証やエンコード、またはエスケープをしないでページを出力すると、XSS の脆弱性が発生します。

## <a name="protecting-your-application-against-xss"></a>XSS からアプリケーションを保護

基本的には、レンダリングされたページに `<script>` タグを挿入することでアプリを操作したり、 `On*` イベント を要素に挿入することで、XSS は動作します。 開発者は、アプリに XSS が導入されないように次の防止策を使う必要があります。

1. 以下の残りの手順に従わない限り、信頼できないデータを HTML の入力に入れるべきではありません。 信頼できないデータとは、攻撃者によって制御される可能性があるデータで、HTML のフォームの入力、クエリ文字列、HTTP ヘッダーです。また、攻撃者がアプリケーションを侵害できなくてもデータベースを侵害する可能性があるため、データベースから取得したデータソースもです。

2. 信頼できないデータを HTML の要素の中に配置する前に、 HTML エンコードがされていることを確認しましょう。 HTML エンコーディングは、 < といった文字列を &lt; のような安全な文字列に変換します。

3. 信頼できないデータを HTML の属性の中に配置する前に、 HTML エンコードがされていることを確認しましょう。 HTML 属性のエンコーディングは HTML エンコーディングの上位互換であり、 " や ' といった追加文字列をエンコードします。

4. 信頼できないデータを JavaScript に入れる前に、実行時に内容を取得する HTML 要素にデータを配置します。 それができなければ、データが JavaScript でエンコードされていることを確認しましょう。 JavaScript のエンコーディングは、JavaScript にとって危険な文字列をとり、それらを16進数に変換します。例えば、&lt; は `\u003C` にエンコードされます。

5. 信頼できないデータを URL クエリ文字列に入れる前に、URL エンコードされていることを確認しましょう。

## <a name="html-encoding-using-razor"></a>Razor を使用して HTML エンコード

MVC での使用を自動的に Razor エンジンがすべてをエンコード大変そうように作業する場合を除き、変数の出力に属しています。 使用するたびに、HTML 属性エンコードの規則を使用して、 *@* ディレクティブ。 HTML として HTML エンコード、つまり、HTML エンコードまたは HTML 属性エンコードを使用する必要があるかどうかを検討することをお持ちのスーパー セットは、属性のエンコーディングします。 のみを使用するコンテキストでは、HTML、JavaScript に直接信頼されていない入力を挿入しようとしています。 ときではなくすることを確認する必要があります。 タグ ヘルパーは、タグのパラメーターで使用する入力をエンコードしても。

次の Razor ビューを実行します。

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

このビューの内容を出力する、 *untrustedInput*変数。 この変数には、つまり、XSS 攻撃に使用されるいくつかの文字が含まれています。 &lt;、"と&gt;します。 ソースを調べるには、エンコードとして表示される出力は示しています。

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC の提供、`HtmlString`出力時に自動的にエンコードされていないクラス。 これは、ことはありません使用してください信頼できない入力と組み合わせて、これが XSS の脆弱性を公開します。

## <a name="javascript-encoding-using-razor"></a>Razor を使用して JavaScript のエンコード

ビューで処理する JavaScript に値を挿入することがあります。 これには、2 つの方法があります。 値を挿入する最も安全な方法では、タグのデータ属性に値を配置し、JavaScript で取得します。 例えば:

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

次の HTML が生成されます。

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

これは、実行時にレンダリングされます以下。

```none
<"123">
   <"123">
   ```

JavaScript エンコーダーを直接呼び出すことができます。

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

次のように、ブラウザーでレンダリングこのされます。

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> DOM 要素を作成する JavaScript での信頼されていない入力は連結しません。 使用する必要があります`createElement()`プロパティの値を次のように適切に割り当てると`node.TextContent=`、使用または`element.SetAttribute()` / `element[attribute]=` xss の DOM ベースの自分をさらすそれ以外の場合。

## <a name="accessing-encoders-in-code"></a>コード内のエンコーダーへのアクセス

HTML、JavaScript、および URL のエンコーダーは 2 つの方法でコードに使用できるを使用してそれらを挿入できる[依存関係の注入](xref:fundamentals/dependency-injection)に含まれている既定のエンコーダーを使用することも、`System.Text.Encodings.Web`名前空間。 適用するいずれかの既定のエンコーダーを使用する場合は、安全として扱われる文字の範囲が有効になります - 既定のエンコーダーが可能な最も安全なエンコードの規則を使用します。

DI は、コンス トラクターを受け取る必要がありますを使用して構成可能なエンコーダーを使用する、 *HtmlEncoder*、 *JavaScriptEncoder*と*UrlEncoder*として適切なパラメーター。 たとえば、

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

## <a name="encoding-url-parameters"></a>URL パラメーターのエンコード

信頼できない入力値の使用として使用して URL クエリ文字列を構築する場合、`UrlEncoder`値をエンコードします。 例えば以下のようにします。

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

変数が含まれる、encodedValue エンコード後`%22Quoted%20Value%20with%20spaces%20and%20%26%22`します。 スペース、引用符、句読点、およびその他の安全でない文字パーセント エンコードされる 16 進数の値に、たとえば空白文字が 20% になります。

>[!WARNING]
> URL パスの一部として、信頼できない入力を使用しないでください。 常に信頼されていない入力を渡すクエリ文字列の値として。

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>エンコーダーのカスタマイズ

既定では、エンコーダーは、基本的なラテン文字 Unicode 範囲が限定セーフ リストを使用しており、同等の文字コードとしての範囲にないすべての文字をエンコードします。 この動作を文字列を出力するエンコーダーを使用すると、Razor TagHelper と HtmlHelper のレンダリングも影響します。

この理由は、不明または将来のブラウザーのバグ (以前のブラウザーのバグが英語以外の文字の処理に基づく解析をトリップした) から保護するためです。 Web サイトは、中国語などのラテン語以外の文字を頻繁に使用場合、キリル文字、またはその他のユーザーはおそらく目的の動作をします。

Unicode で、起動時に、アプリケーションに適切な範囲を含めるエンコーダーのセーフ リストをカスタマイズする`ConfigureServices()`します。

たとえば、Razor HtmlHelper を使用する既定の構成を使用して次のようにします。

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Web ページのソースを表示すると、中国語のテキストがエンコードされていると、次のように表示されたが表示されます。

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

として扱われます、文字幅を広げないエンコーダーによって安全な挿入するには、次の行、`ConfigureServices()`メソッド`startup.cs`;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

この例では、セーフ リストに含める、Unicode 範囲 CjkUnifiedIdeographs 拡大変換されます。 レンダリングされた出力になるようになりました

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

セーフ リストの範囲は、Unicode コード値表、いない言語として指定されます。 [Unicode 標準](https://unicode.org/)のリストを持つ[コード グラフ](https://www.unicode.org/charts/index.html)文字を含むグラフの検索に使用できます。 各エンコーダーは、Html、JavaScript、および Url は、個別に構成する必要があります。

> [!NOTE]
> セーフ リストのカスタマイズには、エンコーダーは DI を使用してソースのみに影響します。 使用してエンコーダーに直接アクセスする場合`System.Text.Encodings.Web.*Encoder.Default`し、既定、Basic Latin セーフリストのみが使用されます。

## <a name="where-should-encoding-take-place"></a>エンコードの実行を配置する必要がありますか。

一般には、エンコードが行われる出力の時点でとエンコードされた値をデータベースに格納しない必要がありますのことをお勧めが受け入れられます。 エンコード出力の時点で、クエリ文字列値を HTML からのデータなどの使用を変更することができます。 検索する前に値をエンコードすることがなく、データを簡単に検索することができ、変更やエンコーダーに加えられたバグの修正を活用することができます。

## <a name="validation-as-an-xss-prevention-technique"></a>XSS 防止手法として検証

検証では、XSS 攻撃を制限することで便利なツールを指定できます。 たとえば、文字 0 ~ 9 のみを含む数値文字列では、XSS 攻撃をトリガーしません。 ユーザー入力に HTML を受け入れるときに、検証が複雑になります。 HTML 入力を解析することは、不可能でない場合は困難です。 埋め込みの HTML を取り除くパーサーと組み合わせると、markdown は、豊富な入力を受け入れるためのより安全なオプションです。 単独で検証に依存しないようにします。 どのような検証または消去が実行されても、出力する前に信頼されていない入力は常にエンコードします。
