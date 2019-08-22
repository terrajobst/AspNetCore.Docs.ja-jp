---
title: ASP.NET Core のタグ ヘルパー コンポーネント
author: scottaddie
description: タグ ヘルパー コンポーネントについてと、ASP.NET Core でのその使用方法について説明します。
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: de053dd0138224a9bece218010eaaedcf7e084cb
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583541"
---
# <a name="tag-helper-components-in-aspnet-core"></a>ASP.NET Core のタグ ヘルパー コンポーネント

作成者: [Scott Addie](https://twitter.com/Scott_Addie)、[Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)

タグ ヘルパー コンポーネントは、サーバー側のコードから HTML 要素を、条件に応じて変更または追加できるタグ ヘルパーです。 この機能は、ASP.NET Core 2.0 以降で使用できます。

ASP.NET Core には、組み込みのタグ ヘルパー コンポーネントが 2 つ (`head` と `body`) 含まれています。 これらは <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> 名前空間に配置され、MVC と Razor Pages の両方で使用できます。 タグ ヘルパー コンポーネントには、 *_ViewImports.cshtml* でのアプリへの登録は必要ありません。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="use-cases"></a>ユース ケース

タグ ヘルパー コンポーネントの 2 つの一般的なユース ケースは、次のとおりです。

1. [`<link>` を `<head>` に挿入する](#inject-into-html-head-element)
1. [`<script>` を `<body>` に挿入する](#inject-into-html-body-element)

次のセクションでは、これらのユース ケースについて説明します。

### <a name="inject-into-html-head-element"></a>HTML の head 要素の挿入

HTML `<head>` 要素内で、CSS ファイルは HTML `<link>` 要素でよくインポートされます。 次のコードでは、`head` タグ ヘルパー コンポーネントを使用して `<link>` 要素が `<head>` 要素に挿入されます。

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

上のコードでは以下の操作が行われます。

* `AddressStyleTagHelperComponent` は、<xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent> を実装します。 抽象化では次のことを行います。
  * <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext> を使ってクラスの初期化を許可。
  * タグ ヘルパー コンポーネントの使用を有効にして、HTML 要素を追加または変更。
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> プロパティでは、コンポーネントがレンダリングされる順序を定義します。 アプリでタグ ヘルパー コンポーネントが複数使用されている場合、`Order` が必要です。
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> では、実行コンテキストの <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> プロパティ値が `head` と比較されます。 比較評価の結果が true の場合は、`_style` フィールドのコンテンツが HTML `<head>` 要素に挿入されます。

### <a name="inject-into-html-body-element"></a>HTML の body 要素に挿入

`body` タグ ヘルパー コンポーネントによって、`<script>` 要素を `<body>` 要素に挿入できます。 次のコードはこの技法を示しています。

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

個別の HTML ファイルを使用して、`<script>` 要素を格納します。 HTML ファイルを使用すると、コードはより整理され、よりメンテナンスしやすくなります。 上のコードでは、*TagHelpers/Templates/AddressToolTipScript.html* のコンテンツを読み取り、タグ ヘルパーの出力でそれを追加します。 *AddressToolTipScript.html* ファイルには、次のマークアップが含まれます。

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

上のコードでは、[ブートストラップ ヒント ウィジェット](https://getbootstrap.com/docs/3.3/javascript/#tooltips)を `printable` 属性を含む任意の `<address>` 要素にバインドします。 要素の上にマウス ポインターが移動したときに、効果が表示されます。

## <a name="register-a-component"></a>コンポーネントの登録

タグ ヘルパー コンポーネントは、アプリのタグ ヘルパー コンポーネント コレクションに追加する必要があります。 コレクションに追加するには、次の 3 つの方法があります。

* [サービス コンテナーによる登録](#registration-via-services-container)
* [Razor ファイルによる登録](#registration-via-razor-file)
* [ページ モデルまたはコントローラーによる登録](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>サービス コンテナーによる登録

タグ ヘルパー コンポーネント クラスが <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> で管理されていない場合、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) システムで登録する必要があります。 次の `Startup.ConfigureServices` コードでは、[一時的な有効期間](xref:fundamentals/dependency-injection#lifetime-and-registration-options)で `AddressStyleTagHelperComponent` クラスと `AddressScriptTagHelperComponent` クラスを登録します。

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>Razor ファイルによる登録

タグ ヘルパー コンポーネントが DI で登録されていない場合は、Razor Pages ページまたは MVC ビューから登録できます。 この技法は、Razor ファイルから挿入されたマークアップとコンポーネントの実行する順番を制御するために使用されます。

`ITagHelperComponentManager` を使用して、タグ ヘルパー コンポーネントを追加したり、アプリから削除したりします。 次のコードでは、`AddressTagHelperComponent` を使ってこの技法を示します。

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

上のコードでは以下の操作が行われます。

* `@inject` ディレクティブでは、`ITagHelperComponentManager` のインスタンスが提供されます。 インスタンスは、Razor ファイルでダウンストリームのアクセスを行うために、`manager` という名前の変数に割り当てられます。
* `AddressTagHelperComponent` のインスタンスは、アプリのタグ ヘルパー コンポーネント コレクションに追加されます。

`AddressTagHelperComponent` は、`markup` と `order` パラメーターを受け入れるコンストラクターに反映するために変更されます。

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

指定された `markup` パラメーターは、次のように `ProcessAsync` で使用されます。

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>ページ モデルまたはコントローラーによる登録

タグ ヘルパー コンポーネントが DI で登録されていない場合は、Razor Pages ページ モデルまたは MVC コントローラーから登録できます。 この技法は、Razor ファイルから C# ロジックを分離するのに便利です。

コンストラクター挿入を使用して、`ITagHelperComponentManager` のインスタンスにアクセスします。 タグ ヘルパー コンポーネントは、インスタンスのタグ ヘルパー コンポーネント コレクションに追加されます。 次の Razor Pages ページ モデルでは、`AddressTagHelperComponent` を使ったこの技法を示します。

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

上のコードでは以下の操作が行われます。

* コンストラクター挿入を使用して、`ITagHelperComponentManager` のインスタンスにアクセスします。
* `AddressTagHelperComponent` のインスタンスは、アプリのタグ ヘルパー コンポーネント コレクションに追加されます。

## <a name="create-a-component"></a>コンポーネントの作成

カスタムのタグ ヘルパー コンポーネントを作成するには

* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> から派生するパブリック クラスを作成します。
* [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) 属性をクラスに適用します。 ターゲット HTML 要素の名前を指定します。
* *省略可能*:IntelliSense で型を表示しないようにするには、[[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) 属性をクラスに適用します。

次のコードでは、`<address>` HTML 要素をターゲットとするカスタムのタグ ヘルパー コンポーネントが作成されます。

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

カスタムの `address` タグ ヘルパー コンポーネントを使用して、次のように HTML マークアップを挿入します。

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

上の `ProcessAsync` メソッドでは、<xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> に提供された HTML が一致する `<address>` 要素に挿入されます。 挿入は次の場合に発生します。

* 実行コンテキストの `TagName` プロパティ値が `address` と等しい場合。
* 対応する `<address>` 要素に `printable` 属性がある場合。

たとえば、次の `<address>` 要素を処理しているときに、`if` ステートメントの評価の結果が true になります。

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
