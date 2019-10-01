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
# <a name="script-tag-helper-in-aspnet-core"></a>ASP.NET Core のスクリプト タグ ヘルパー

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[スクリプト タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)によって、プライマリまたはフォール バック スクリプト ファイルへのリンクが生成されます。 通常、プライマリ スクリプト ファイルは [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN) 上にあります。

[!INCLUDE[](~/includes/cdn.md)]

スクリプト タグ ヘルパーを使用すると、スクリプト ファイルの CDN と、CDN が使用できない場合のフォールバックを指定できます。 スクリプト タグ ヘルパーによって、ローカル ホスティングの堅牢性が CDN のパフォーマンスの利点にもたらされます。

次の Razor マークアップでは、ASP.NET Core Web アプリ テンプレートを使用して作成されたレイアウト ファイルの `script` 要素を示します。

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

次に示すものは、前のコードからレンダリングされた HTML に似ています (非開発環境)。

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

前のコードでは、スクリプト タグ ヘルパーによって 2 番目のスクリプト (`<script>  (window.jQuery || document.write(`) 要素が生成されました。これは `window.jQuery` についてテストします。 `window.jQuery` が見つからない場合は、`document.write(` が実行し、スクリプトを作成します。 

## <a name="commonly-used-script-tag-helper-attributes"></a>一般的に使用されるスクリプト タグ ヘルパー属性

スクリプト タグ ヘルパーのすべての属性、プロパティ、メソッドについては、[スクリプト タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)に関するページを参照してください。

### <a name="href"></a>href

リンクされたリソースの優先アドレス。 このアドレスは、生成された HTML に常に渡されます。

### <a name="asp-fallback-href"></a>asp-fallback-href

プライマリ URL が失敗した場合にフォールバックする CSS スタイルシートの URL。

### <a name="asp-fallback-test-class"></a>asp-fallback-test-class

フォールバック テストで使用するためにスタイルシートに定義されているクラス名。 詳細については、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass> を参照してください。

### <a name="asp-fallback-test-property"></a>asp-fallback-test-property

フォールバック テストで使用する CSS プロパティ名。 詳細については、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty> を参照してください。

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

フォールバック テストで使用する CSS プロパティ値。 詳細については、<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue> を参照してください。

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

フォールバック テストで使用する CSS プロパティ値。 詳細については、「<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
