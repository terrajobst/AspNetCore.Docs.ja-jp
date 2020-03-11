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
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>ASP.NET Core でのクロスサイトスクリプティング (XSS) の防止

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

クロスサイトスクリプティング（XSS）は、攻撃者がクライアントサイドのスクリプト (一般的に JavaScript）を Web ページに配置できるようにするセキュリティの脆弱性です。 他のユーザーが影響を受けたページを読み込むと、攻撃者のスクリプトが実行され、攻撃者は Cookie やセッションのトークンを読み取り、DOM の操作を通じて Web ページのコンテンツを変更したり、ブラウザーを別のページにリダイレクトできます。 アプリケーションがユーザー入力を受け取り、それを検証やエンコード、またはエスケープをしないでページを出力すると、XSS の脆弱性が発生します。

## <a name="protecting-your-application-against-xss"></a>XSS からアプリケーションを保護

基本的なレベルの XSS では、アプリケーションを欺いして、レンダリングされたページに `<script>` タグを挿入したり、`On*` イベントを要素に挿入したりすることができます。 開発者は、アプリに XSS が導入されないように次の防止策を使う必要があります。

1. 以下の残りの手順に従わない限り、信頼できないデータを HTML の入力に入れるべきではありません。 信頼できないデータとは、攻撃者によって制御される可能性があるデータで、HTML のフォームの入力、クエリ文字列、HTTP ヘッダーです。また、攻撃者がアプリケーションを侵害できなくてもデータベースを侵害する可能性があるため、データベースから取得したデータソースもです。

2. 信頼できないデータを HTML の要素の中に配置する前に、 HTML エンコードがされていることを確認しましょう。 HTML エンコードは、&lt; などの文字を受け取り、&amp;lt; のように安全な形式に変更します。

3. 信頼できないデータを HTML の属性の中に配置する前に、 HTML エンコードがされていることを確認しましょう。 HTML 属性のエンコーディングは HTML エンコーディングの上位互換であり、 " や ' といった追加文字列をエンコードします。

4. 信頼できないデータを JavaScript に入れる前に、実行時に内容を取得する HTML 要素にデータを配置します。 それができなければ、データが JavaScript でエンコードされていることを確認しましょう。 Javascript のエンコードでは、JavaScript では危険な文字が使用され、16進数に置き換えられます。たとえば、&lt; は `\u003C`としてエンコードされます。

5. 信頼できないデータを URL クエリ文字列に入れる前に、URL エンコードされていることを確認しましょう。

## <a name="html-encoding-using-razor"></a>Razor を使用した HTML エンコード

MVC で使われている Razor のエンジンは、自身でそうするのを防がない限り、変数から供給されるすべての出力を自動的にエンコードします。 *@* ディレクティブを使用する場合は、常に HTML 属性のエンコード規則が使用されます。 HTML 属性エンコーディングが HTML エンコーディングの上位互換であるため、HTML 属性エンコーディングが HTML エンコーディングのどちらを使うか気にする必要はありません。 信頼できない入力データを JavaScript に直接代入するのではなく、必ず HTML コンテキストの中で @を使うよう確認しましょう。 TagHelper もまた、タグのパラメーターで使う入力をエンコードします。

次の Razor ビューを実行します。

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

このビューは、 *Untrustedinput*変数の内容を出力します。 この変数には、XSS 攻撃で使用される文字 (&lt;、&gt;) が含まれています。 ソースを実行すると、次のようにエンコードされた出力が表示されます。

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> MVC ASP.NET Core は、出力時に自動的にエンコードされない `HtmlString` クラスを提供します。 これは、XSS の脆弱性が露呈するため、信頼できない入力と組み合わせて使用しないでください。

## <a name="javascript-encoding-using-razor"></a>Razor を使用した JavaScript のエンコード

ビューで処理するのに Javascript に値を代入したい場合があるとします。 2 つの方法があります。 値を代入するのに最も安全な方法は、タグのデータ属性の中に値を配置して JavaScript で読み取ることです。 例 :

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

これにより、次の HTML が生成されます。

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

実行すると、次のように表示されます。

```
<"123">
   <"123">
```

JavaScript のエンコーダーを直接呼び出すこともできます。

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

ブラウザーで次のようにレンダリングされます。

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> DOM の要素を作成するのに、信頼できない入力を JavaScript で連結しないでください。 `createElement()` を使用し、`node.TextContent=`などのプロパティ値を適切に割り当てるか `element.SetAttribute()``element[attribute]=` /を使用する必要があります。それ以外の場合は、DOM ベースの XSS に公開します。

## <a name="accessing-encoders-in-code"></a>コード内のエンコーダーへのアクセス

HTML、JavaScript、および URL エンコーダーは、2つの方法でコードで使用できます。[依存関係の挿入](xref:fundamentals/dependency-injection)を使用して挿入することも、`System.Text.Encodings.Web` 名前空間に含まれる既定のエンコーダーを使用することもできます。 デフォルトのエンコーダーを使用する場合、安全として扱われる文字範囲を適用した場合は、効果が出ません - デフォルトのエンコーダーでは可能な限り最も安全なエンコード規則を使用します。

DI を使用して構成可能なエンコーダーを使用するには、コンストラクターが*htmlencoder*、 *JavaScriptEncoder* 、および*urlencoder*パラメーターを必要に応じて受け取る必要があります。 次に例を示します。

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

## <a name="encoding-url-parameters"></a>エンコード URL パラメーター

信頼できない入力の URL クエリ文字列を値として作成する場合は、`UrlEncoder` を使用して値をエンコードします。 たとえば、次のように入力します。

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

エンコード後に、エンコード値の変数には `%22Quoted%20Value%20with%20spaces%20and%20%26%22`が含まれます。 スペース、引用符、句読点やその他の安全でない文字は、16 進数の値にパーセントエンコードされます。たとえばスペースの文字は ％20 になります。

>[!WARNING]
> URL のパスに信頼できない入力を使用しないでください。 信頼できない入力は、必ずクエリ文字列として渡します。

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>エンコーダーのカスタマイズ

デフォルトでは、エンコーダーは Basic Latin Unicode の範囲の制限されたセーフリストを使用し、その範囲外の全ての文字を文字コードにエンコードします。 この動作は、文字列を出力するのにエンコーダーを使っているのため、Razor の TagHelper と HtmlHelper のレンダリングに影響します。

この理由は、未知または将来のブラウザーのバグから守るためです（過去に、ブラウザーのバグで、英語ではない文字の処理の解析に失敗しました）。 もしウェブサイトが中国語やキリル文字などラテン語以外の文字を多く利用している場合、これはあなたの望む動作ではないでしょう。

エンコーダーセーフリストをカスタマイズして、起動時にアプリケーションに適した Unicode 範囲を `ConfigureServices()`に含めることができます。

例として、デフォルトの設定で Razor の HtmlHelper を使用することができます;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Web ページのソースを見ると、中国語のテキストがエンコードされ、次のようにレンダリングされています。

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

エンコーダーによって安全として扱われる文字を拡大するには、`startup.cs`の `ConfigureServices()` メソッドに次の行を挿入します。

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

この例では、CjkUnifiedIdeographs の範囲の Unicode を含むセーフリストに拡張しました。 レンダリングされた出力は次のようになります;

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

セーフリストは、言語ではなく Unicode のコード表として指定されています。 [Unicode 規格](https://unicode.org/)には、文字を含むグラフを検索するために使用できる[コード表](https://www.unicode.org/charts/index.html)の一覧があります。 HTML、JavaScript、URL のそれぞれのエンコーダーで別々に設定する必要があります。

> [!NOTE]
> セーフリストのカスタマイズは、 DI 経由でのエンコーダーのみに影響します。 `System.Text.Encodings.Web.*Encoder.Default` によってエンコーダーに直接アクセスした場合は、既定の基本的なラテン only セーフセーフセーフポイントが使用されます。

## <a name="where-should-encoding-take-place"></a>エンコードはどこで行われるべきか？

一般的に受け入れられている手法として、エンコードは出力の時に行い、エンコードされた値をデータベースに保存すべきではありません。 出力時のエンコーディングをすることで、データの用途を変更できます。例えば、 HTML からクエリ文字列の値への変換です。 検索する前に値をエンコードすることがなくデータを簡単に検索することができ、変更やエンコーダーに加えられたバグの修正を活用することができます。

## <a name="validation-as-an-xss-prevention-technique"></a>XSS 防止の手法としての検証

検証は、XSS 攻撃を制限するのに役立つツールです。 0-9だけが含まれる数字の文字では、XSS を引き起こすことができません。 HTML がユーザーに入力できる状態だと、検証はより複雑になります。 できないことはありませんが、HTML の入力の解析は困難です。 Markdown は、埋め込まれた HTML を取り除くパーサーが結合されているので、リッチな入力を受け入れるためのより安全なオプションです。 しかし、検証だけに頼らないでください。 検証やサニタイズが実行されていても、常に出力の前に信頼できない入力をエンコードしましょう。
