---
title: ASP.NET Core での Razor ページのルートとアプリの規則
author: guardrex
description: ルートとアプリ モデル プロバイダーの規則が、ページのルーティング、検出、および処理の制御にどのように役立つかについて確認します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: d8377c0a0b8a29fe4b6a7fa67beeff84927c8b74
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114771"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>ASP.NET Core での Razor ページのルートとアプリの規則

作成者: [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。

個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。

ページルートを指定したり、ルートセグメントを追加したり、ルートにパラメーターを追加したりするには、ページの `@page` ディレクティブを使用します。 詳細については、「[カスタムルート](xref:razor-pages/index#custom-routes)」を参照してください。

ルートセグメントまたはパラメーター名として使用できない予約語があります。 詳細については、「[ルーティング: 予約済みルーティング名](xref:fundamentals/routing#reserved-routing-names)」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

| シナリオ | このサンプルでは、次のデモを実行します。 |
| -------- | --------------------------- |
| [モデルの規則](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | ルート テンプレートとヘッダーをアプリのページに追加します。 |
| [ページ ルート アクション規則](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | ルート テンプレートをフォルダー内のページおよび単一ページに追加します。 |
| [ページ モデル アクション規則](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</li></ul> | ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。 |

Razor Pages 規則は、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用して追加および構成され、`Startup` クラスのサービスコレクションで <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> します。 次の規則の例は、このトピックで後述されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>ルートの順序

ルートは、処理 (ルート一致) の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> を指定します。

| Order            | 動作 |
| :--------------: | -------- |
| -1               | ルートは、他のルートが処理される前に処理されます。 |
| 0                | 順序が指定されていません (既定値)。 `Order` (`Order = null`) が割り当てられていない場合、ルートは処理のために 0 (ゼロ) に `Order` ます。 |
| 1、2、&hellip; n | ルートの処理順序を指定します。 |

ルート処理は規約によって確立されます。

* ルートは順番に処理されます (-1、0、1、2、&hellip; n)。
* ルートの `Order`が同じである場合は、最も具体的なルートが最初に照合され、その後に特定のルートがより少なくなります。
* 同じ `Order` で同じ数のパラメーターが要求 URL と一致する場合、ルートは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>に追加された順序で処理されます。

可能な場合は、確立されたルート処理順序に依存しないようにしてください。 通常、ルーティングでは、URL が一致する正しいルートが選択されます。 要求を正しくルーティングするようにルート `Order` のプロパティを設定する必要がある場合、アプリケーションのルーティングスキームは、クライアントが混乱し、保守が脆弱になることがあります。 アプリのルーティングスキームを簡略化するためにシークします。 このサンプルアプリでは、単一のアプリを使用した複数のルーティングシナリオを示すために、明示的なルート処理を行う必要があります。 ただし、運用環境のアプリでルート `Order` を設定する方法を避けるようにしてください。

Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。 MVC のトピックに記載されている情報については、 [「コントローラーアクションへのルーティング: 属性ルートの順序付け](xref:mvc/controllers/routing#ordering-attribute-routes)」を参照してください。

## <a name="model-conventions"></a>モデルの規則

Razor Pages に適用されるモデルの[規則](xref:mvc/controllers/application-model#conventions)を追加するには、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> のデリゲートを追加します。

### <a name="add-a-route-model-convention-to-all-pages"></a>ルートモデルの規則をすべてのページに追加する

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページルートモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。

サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `1` が設定されます。 これにより、サンプルアプリで次のルート一致動作が保証されます。

* `TheContactPage/{text?}` のルートテンプレートは、このトピックの後半で追加します。 連絡先ページのルートには `null` (`Order = 0`) の既定の順序があるため、`{globalTemplate?}` ルートテンプレートの前に一致します。
* `{aboutTemplate?}` ルートテンプレートは、このトピックの後半で追加します。 `{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。 [About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`RouteData.Values["aboutTemplate"]` プロパティが設定されるため `Order = 2` (`Order`) には読み込まれません。
* `{otherPagesTemplate?}` ルートテンプレートは、このトピックの後半で追加します。 `{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。 *Pages/otherpages*フォルダー内のいずれかのページがルートパラメーター (`/OtherPages/Page1/RouteDataValue`など) で要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) は読み込まれません。

可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。 正しいルートを選択するには、ルーティングに依存します。

MVC が `Startup.ConfigureServices`のサービスコレクションに追加されると、<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>の追加などの Razor Pages オプションが追加されます。 例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>すべてのページにアプリモデル規則を追加する

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページアプリモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。

この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。 クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。 これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。 完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。

サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>すべてのページにハンドラーモデル規則を追加する

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページハンドラーモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して追加します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>ページ ルート アクション規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルートモデルプロバイダーは、ページルートを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。

### <a name="folder-route-model-convention"></a>フォルダールートモデルの規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページについて <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。

サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。 これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。 *Pages/otherpages*フォルダー内のページがルートパラメーター値 (`/OtherPages/Page1/RouteDataValue`など) で要求されている場合、"RouteDataValue" は、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。

可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。 正しいルートを選択するには、ルーティングに依存します。

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>ページルートモデルの規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。

サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。 これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。 `/About/RouteDataValue`でルートパラメーター値を使用して About ページが要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["aboutTemplate"]` (`Order`) ではなくなります。

可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。 正しいルートを選択するには、ルーティングに依存します。

`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>パラメータートランスフォーマーを使用してページルートをカスタマイズする

ASP.NET Core によって生成されたページルートは、パラメータートランスフォーマーを使用してカスタマイズできます。 パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。 たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。

`PageRouteTransformerConvention` ページルートモデル規則では、アプリで自動的に生成されたページルートのフォルダーとファイル名のセグメントにパラメータートランスフォーマーを適用します。 たとえば、 *//* //の Razor Pages ファイルでは、ルートが `/SubscriptionManagement/ViewAll` から `/subscription-management/view-all`に書き直されます。

`PageRouteTransformerConvention` は、Razor Pages フォルダーとファイル名から取得された、自動的に生成されたページルートのセグメントのみを変換します。 `@page` ディレクティブを使用して追加されたルートセグメントは変換されません。 この規則では、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>によって追加されたルートを変換することもできません。

`PageRouteTransformerConvention` は `Startup.ConfigureServices`のオプションとして登録されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>ページ ルートの構成

指定したページパスにあるページへのルートを構成するには、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用します。 そのページに対して生成されたリンクでは、指定したルートを使用します。 `AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。

サンプル アプリでは、`/TheContactPage`Contact.cshtml*の* へのルートを作成します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。

サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。 また、訪問者が `@page` ルートでページにアクセスする場合、ページの `/Contact` ディレクティブにはこの省略可能なセグメントも含まれます。

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。 追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。 レンダリングされたページには、'text' セグメントの値が示されています。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>ページ モデル アクション規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページモデルプロバイダーは、ページモデルを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。 これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。

このセクションの例では、サンプルアプリで <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>である `AddHeaderAttribute` クラスを使用して、応答ヘッダーを適用します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。

**フォルダー アプリ モデル規則**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスでアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。

このサンプルでは、ヘッダー (`AddFolderApplicationModelConvention`) をアプリの `OtherPagesHeader`OtherPages *フォルダー内にあるページに追加して、* を使用するデモを実行します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

**ページ アプリ モデル規則**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。

サンプルでは、ヘッダー (`AddPageApplicationModelConvention`) を [About] ページに追加して、`AboutHeader` を使用するデモを実行します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

**フィルターの構成**

指定したフィルターを適用するように <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 構成します。 フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。 条件を満たすと、ヘッダーが追加されます。 満たさない場合は、`EmptyFilter` が適用されます。

`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。 アクションフィルターは Razor Pages によって無視されるため、パスに `OtherPages/Page2`が含まれていない場合、`EmptyFilter` は意図したとおりには効果がありません。

`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

**フィルター ファクトリの構成**

指定したファクトリを、すべての Razor Pages に[フィルター](xref:mvc/controllers/filters)を適用するように構成 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> ます。

サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー ([) を追加して、](xref:mvc/controllers/filters#ifilterfactory)フィルター ファクトリ`FilterFactoryHeader`を使用する例を提供します。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC フィルターとページ フィルター (IPageFilter)

Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。 その他の型の MVC フィルターは、[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)を使用するために利用できます。 詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。

ページフィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor Pages に適用されるフィルターです。 詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。

個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。

ページルートを指定したり、ルートセグメントを追加したり、ルートにパラメーターを追加したりするには、ページの `@page` ディレクティブを使用します。 詳細については、「[カスタムルート](xref:razor-pages/index#custom-routes)」を参照してください。

ルートセグメントまたはパラメーター名として使用できない予約語があります。 詳細については、「[ルーティング: 予約済みルーティング名](xref:fundamentals/routing#reserved-routing-names)」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

| シナリオ | このサンプルでは、次のデモを実行します。 |
| -------- | --------------------------- |
| [モデルの規則](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | ルート テンプレートとヘッダーをアプリのページに追加します。 |
| [ページ ルート アクション規則](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | ルート テンプレートをフォルダー内のページおよび単一ページに追加します。 |
| [ページ モデル アクション規則](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</li></ul> | ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。 |

Razor Pages 規則は、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用して追加および構成され、`Startup` クラスのサービスコレクションで <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> します。 次の規則の例は、このトピックで後述されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>ルートの順序

ルートは、処理 (ルート一致) の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> を指定します。

| Order            | 動作 |
| :--------------: | -------- |
| -1               | ルートは、他のルートが処理される前に処理されます。 |
| 0                | 順序が指定されていません (既定値)。 `Order` (`Order = null`) が割り当てられていない場合、ルートは処理のために 0 (ゼロ) に `Order` ます。 |
| 1、2、&hellip; n | ルートの処理順序を指定します。 |

ルート処理は規約によって確立されます。

* ルートは順番に処理されます (-1、0、1、2、&hellip; n)。
* ルートの `Order`が同じである場合は、最も具体的なルートが最初に照合され、その後に特定のルートがより少なくなります。
* 同じ `Order` で同じ数のパラメーターが要求 URL と一致する場合、ルートは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>に追加された順序で処理されます。

可能な場合は、確立されたルート処理順序に依存しないようにしてください。 通常、ルーティングでは、URL が一致する正しいルートが選択されます。 要求を正しくルーティングするようにルート `Order` のプロパティを設定する必要がある場合、アプリケーションのルーティングスキームは、クライアントが混乱し、保守が脆弱になることがあります。 アプリのルーティングスキームを簡略化するためにシークします。 このサンプルアプリでは、単一のアプリを使用した複数のルーティングシナリオを示すために、明示的なルート処理を行う必要があります。 ただし、運用環境のアプリでルート `Order` を設定する方法を避けるようにしてください。

Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。 MVC のトピックに記載されている情報については、 [「コントローラーアクションへのルーティング: 属性ルートの順序付け](xref:mvc/controllers/routing#ordering-attribute-routes)」を参照してください。

## <a name="model-conventions"></a>モデルの規則

Razor Pages に適用されるモデルの[規則](xref:mvc/controllers/application-model#conventions)を追加するには、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> のデリゲートを追加します。

### <a name="add-a-route-model-convention-to-all-pages"></a>ルートモデルの規則をすべてのページに追加する

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページルートモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。

サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `1` が設定されます。 これにより、サンプルアプリで次のルート一致動作が保証されます。

* `TheContactPage/{text?}` のルートテンプレートは、このトピックの後半で追加します。 連絡先ページのルートには `null` (`Order = 0`) の既定の順序があるため、`{globalTemplate?}` ルートテンプレートの前に一致します。
* `{aboutTemplate?}` ルートテンプレートは、このトピックの後半で追加します。 `{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。 [About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`RouteData.Values["aboutTemplate"]` プロパティが設定されるため `Order = 2` (`Order`) には読み込まれません。
* `{otherPagesTemplate?}` ルートテンプレートは、このトピックの後半で追加します。 `{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。 *Pages/otherpages*フォルダー内のいずれかのページがルートパラメーター (`/OtherPages/Page1/RouteDataValue`など) で要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) は読み込まれません。

可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。 正しいルートを選択するには、ルーティングに依存します。

MVC が `Startup.ConfigureServices`のサービスコレクションに追加されると、<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>の追加などの Razor Pages オプションが追加されます。 例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>すべてのページにアプリモデル規則を追加する

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページアプリモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。

この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。 クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。 これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。 完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。

サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>すべてのページにハンドラーモデル規則を追加する

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページハンドラーモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>ページ ルート アクション規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルートモデルプロバイダーは、ページルートを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。

### <a name="folder-route-model-convention"></a>フォルダールートモデルの規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページについて <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。

サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。 これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。 *Pages/otherpages*フォルダー内のページがルートパラメーター値 (`/OtherPages/Page1/RouteDataValue`など) で要求されている場合、"RouteDataValue" は、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。

可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。 正しいルートを選択するには、ルーティングに依存します。

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>ページルートモデルの規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。

サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。 これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。 `/About/RouteDataValue`でルートパラメーター値を使用して About ページが要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["aboutTemplate"]` (`Order`) ではなくなります。

可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。 正しいルートを選択するには、ルーティングに依存します。

`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>パラメータートランスフォーマーを使用してページルートをカスタマイズする

ASP.NET Core によって生成されたページルートは、パラメータートランスフォーマーを使用してカスタマイズできます。 パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。 たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。

`PageRouteTransformerConvention` ページルートモデル規則では、アプリで自動的に生成されたページルートのフォルダーとファイル名のセグメントにパラメータートランスフォーマーを適用します。 たとえば、 *//* //の Razor Pages ファイルでは、ルートが `/SubscriptionManagement/ViewAll` から `/subscription-management/view-all`に書き直されます。

`PageRouteTransformerConvention` は、Razor Pages フォルダーとファイル名から取得された、自動的に生成されたページルートのセグメントのみを変換します。 `@page` ディレクティブを使用して追加されたルートセグメントは変換されません。 この規則では、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>によって追加されたルートを変換することもできません。

`PageRouteTransformerConvention` は `Startup.ConfigureServices`のオプションとして登録されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>ページ ルートの構成

指定したページパスにあるページへのルートを構成するには、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用します。 そのページに対して生成されたリンクでは、指定したルートを使用します。 `AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。

サンプル アプリでは、`/TheContactPage`Contact.cshtml*の* へのルートを作成します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。

サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。 また、訪問者が `@page` ルートでページにアクセスする場合、ページの `/Contact` ディレクティブにはこの省略可能なセグメントも含まれます。

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。 追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。 レンダリングされたページには、'text' セグメントの値が示されています。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>ページ モデル アクション規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページモデルプロバイダーは、ページモデルを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。 これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。

このセクションの例では、サンプルアプリで <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>である `AddHeaderAttribute` クラスを使用して、応答ヘッダーを適用します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。

**フォルダー アプリ モデル規則**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスでアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。

このサンプルでは、ヘッダー (`AddFolderApplicationModelConvention`) をアプリの `OtherPagesHeader`OtherPages *フォルダー内にあるページに追加して、* を使用するデモを実行します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

**ページ アプリ モデル規則**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。

サンプルでは、ヘッダー (`AddPageApplicationModelConvention`) を [About] ページに追加して、`AboutHeader` を使用するデモを実行します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

**フィルターの構成**

指定したフィルターを適用するように <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 構成します。 フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。 条件を満たすと、ヘッダーが追加されます。 満たさない場合は、`EmptyFilter` が適用されます。

`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。 アクションフィルターは Razor Pages によって無視されるため、パスに `OtherPages/Page2`が含まれていない場合、`EmptyFilter` は意図したとおりには効果がありません。

`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

**フィルター ファクトリの構成**

指定したファクトリを、すべての Razor Pages に[フィルター](xref:mvc/controllers/filters)を適用するように構成 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> ます。

サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー ([) を追加して、](xref:mvc/controllers/filters#ifilterfactory)フィルター ファクトリ`FilterFactoryHeader`を使用する例を提供します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC フィルターとページ フィルター (IPageFilter)

Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。 その他の型の MVC フィルターは、[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)を使用するために利用できます。 詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。

ページフィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor Pages に適用されるフィルターです。 詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。

個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。

ページルートを指定したり、ルートセグメントを追加したり、ルートにパラメーターを追加したりするには、ページの `@page` ディレクティブを使用します。 詳細については、「[カスタムルート](xref:razor-pages/index#custom-routes)」を参照してください。

ルートセグメントまたはパラメーター名として使用できない予約語があります。 詳細については、「[ルーティング: 予約済みルーティング名](xref:fundamentals/routing#reserved-routing-names)」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

| シナリオ | このサンプルでは、次のデモを実行します。 |
| -------- | --------------------------- |
| [モデルの規則](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | ルート テンプレートとヘッダーをアプリのページに追加します。 |
| [ページ ルート アクション規則](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | ルート テンプレートをフォルダー内のページおよび単一ページに追加します。 |
| [ページ モデル アクション規則](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</li></ul> | ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。 |

Razor Pages 規則は、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 拡張メソッドを使用して追加および構成され、`Startup` クラスのサービスコレクションで <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> します。 次の規則の例は、このトピックで後述されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>ルートの順序

ルートは、処理 (ルート一致) の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> を指定します。

| Order            | 動作 |
| :--------------: | -------- |
| -1               | ルートは、他のルートが処理される前に処理されます。 |
| 0                | 順序が指定されていません (既定値)。 `Order` (`Order = null`) が割り当てられていない場合、ルートは処理のために 0 (ゼロ) に `Order` ます。 |
| 1、2、&hellip; n | ルートの処理順序を指定します。 |

ルート処理は規約によって確立されます。

* ルートは順番に処理されます (-1、0、1、2、&hellip; n)。
* ルートの `Order`が同じである場合は、最も具体的なルートが最初に照合され、その後に特定のルートがより少なくなります。
* 同じ `Order` で同じ数のパラメーターが要求 URL と一致する場合、ルートは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>に追加された順序で処理されます。

可能な場合は、確立されたルート処理順序に依存しないようにしてください。 通常、ルーティングでは、URL が一致する正しいルートが選択されます。 要求を正しくルーティングするようにルート `Order` のプロパティを設定する必要がある場合、アプリケーションのルーティングスキームは、クライアントが混乱し、保守が脆弱になることがあります。 アプリのルーティングスキームを簡略化するためにシークします。 このサンプルアプリでは、単一のアプリを使用した複数のルーティングシナリオを示すために、明示的なルート処理を行う必要があります。 ただし、運用環境のアプリでルート `Order` を設定する方法を避けるようにしてください。

Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。 MVC のトピックに記載されている情報については、 [「コントローラーアクションへのルーティング: 属性ルートの順序付け](xref:mvc/controllers/routing#ordering-attribute-routes)」を参照してください。

## <a name="model-conventions"></a>モデルの規則

Razor Pages に適用されるモデルの[規則](xref:mvc/controllers/application-model#conventions)を追加するには、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> のデリゲートを追加します。

### <a name="add-a-route-model-convention-to-all-pages"></a>ルートモデルの規則をすべてのページに追加する

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページルートモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。

サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `1` が設定されます。 これにより、サンプルアプリで次のルート一致動作が保証されます。

* `TheContactPage/{text?}` のルートテンプレートは、このトピックの後半で追加します。 連絡先ページのルートには `null` (`Order = 0`) の既定の順序があるため、`{globalTemplate?}` ルートテンプレートの前に一致します。
* `{aboutTemplate?}` ルートテンプレートは、このトピックの後半で追加します。 `{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。 [About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`RouteData.Values["aboutTemplate"]` プロパティが設定されるため `Order = 2` (`Order`) には読み込まれません。
* `{otherPagesTemplate?}` ルートテンプレートは、このトピックの後半で追加します。 `{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。 *Pages/otherpages*フォルダー内のいずれかのページがルートパラメーター (`/OtherPages/Page1/RouteDataValue`など) で要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) は読み込まれません。

可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。 正しいルートを選択するには、ルーティングに依存します。

MVC が `Startup.ConfigureServices`のサービスコレクションに追加されると、<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>の追加などの Razor Pages オプションが追加されます。 例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>すべてのページにアプリモデル規則を追加する

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページアプリモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。

この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。 クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。 これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。 完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。

サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>すべてのページにハンドラーモデル規則を追加する

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> を使用して、ページハンドラーモデルの構築時に適用される <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> インスタンスのコレクションに <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> を作成して追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>ページ ルート アクション規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> から派生する既定のルートモデルプロバイダーは、ページルートを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。

### <a name="folder-route-model-convention"></a>フォルダールートモデルの規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページについて <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加できます。

サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。 これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。 *Pages/otherpages*フォルダー内のページがルートパラメーター値 (`/OtherPages/Page1/RouteDataValue`など) で要求されている場合、"RouteDataValue" は、`Order = 2`プロパティの設定によって `RouteData.Values["otherPagesTemplate"]` (`Order`) ではなく `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれます。

可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。 正しいルートを選択するには、ルーティングに依存します。

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>ページルートモデルの規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> を作成して追加します。

サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> プロパティに `2` が設定されます。 これにより、単一のルート値が指定された場合に、`{globalTemplate?}` のテンプレート (前のトピックの「`1`」を参照) が優先されるようになります。 `/About/RouteDataValue`でルートパラメーター値を使用して About ページが要求された場合、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order = 2`プロパティの設定によって `RouteData.Values["aboutTemplate"]` (`Order`) ではなくなります。

可能な限り、`Order`を設定しないでください。これにより `Order = 0`になります。 正しいルートを選択するには、ルーティングに依存します。

`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>ページ ルートの構成

指定したページパスにあるページへのルートを構成するには、<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> を使用します。 そのページに対して生成されたリンクでは、指定したルートを使用します。 `AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。

サンプル アプリでは、`/TheContactPage`Contact.cshtml*の* へのルートを作成します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。

サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。 また、訪問者が `@page` ルートでページにアクセスする場合、ページの `/Contact` ディレクティブにはこの省略可能なセグメントも含まれます。

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。 追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。 レンダリングされたページには、'text' セグメントの値が示されています。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>ページ モデル アクション規則

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> を実装する既定のページモデルプロバイダーは、ページモデルを構成するための機能拡張ポイントを提供するように設計された規則を呼び出します。 これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。

このセクションの例では、サンプルアプリで <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>である `AddHeaderAttribute` クラスを使用して、応答ヘッダーを適用します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。

**フォルダー アプリ モデル規則**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を使用すると、指定したフォルダーの下にあるすべてのページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> インスタンスでアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加できます。

このサンプルでは、ヘッダー (`AddFolderApplicationModelConvention`) をアプリの `OtherPagesHeader`OtherPages *フォルダー内にあるページに追加して、* を使用するデモを実行します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

**ページ アプリ モデル規則**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> を使用して、指定した名前のページの <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> に対してアクションを呼び出す <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> を作成して追加します。

サンプルでは、ヘッダー (`AddPageApplicationModelConvention`) を [About] ページに追加して、`AboutHeader` を使用するデモを実行します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

**フィルターの構成**

指定したフィルターを適用するように <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 構成します。 フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。 条件を満たすと、ヘッダーが追加されます。 満たさない場合は、`EmptyFilter` が適用されます。

`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。 アクションフィルターは Razor Pages によって無視されるため、パスに `OtherPages/Page2`が含まれていない場合、`EmptyFilter` は意図したとおりには効果がありません。

`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

**フィルター ファクトリの構成**

指定したファクトリを、すべての Razor Pages に[フィルター](xref:mvc/controllers/filters)を適用するように構成 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> ます。

サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー ([) を追加して、](xref:mvc/controllers/filters#ifilterfactory)フィルター ファクトリ`FilterFactoryHeader`を使用する例を提供します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC フィルターとページ フィルター (IPageFilter)

Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。 その他の型の MVC フィルターは、[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)を使用するために利用できます。 詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。

ページフィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor Pages に適用されるフィルターです。 詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
