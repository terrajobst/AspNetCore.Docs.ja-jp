---
title: ASP.NET Core での Razor ページのルートとアプリの規則
author: guardrex
description: ルートとアプリ モデル プロバイダーの規則が、ページのルーティング、検出、および処理の制御にどのように役立つかについて確認します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/07/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 59c8af648b50deb51f3762c14348d08acd48886e
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724452"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>ASP.NET Core での Razor ページのルートとアプリの規則

作成者: [Luke Latham](https://github.com/guardrex)

[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。

個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。

ページ ルートを指定、ルート セグメントを追加またはルートにパラメーターを追加使用ページの`@page`ディレクティブ。 詳細については、次を参照してください。[カスタム ルート](xref:razor-pages/index#custom-routes)します。

ルート セグメントまたはパラメーター名として使用できません。 予約語があります。 詳細については、次を参照してください。[ルーティング。ルーティングの名前を予約](xref:fundamentals/routing#reserved-routing-names)します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

| シナリオ | このサンプルでは、次のデモを実行します。 |
| -------- | --------------------------- |
| [モデルの規則](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | ルート テンプレートとヘッダーをアプリのページに追加します。 |
| [ページ ルート アクション規則](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | ルート テンプレートをフォルダー内のページおよび単一ページに追加します。 |
| [ページ モデル アクション規則](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</li></ul> | ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。 |

Razor ページの規則が追加され、構成を使用して、<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>拡張メソッドを<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>でサービスのコレクションを`Startup`クラス。 次の規則の例は、このトピックで後述されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a>ルートの順序

ルートの指定、 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> (ルートの一致) を処理するためです。

| 注文            | 動作 |
| :--------------: | -------- |
| -1               | ルートは、他のルートが処理される前に処理されます。 |
| 0                | 順序が指定されていない (既定値)。 割り当てない`Order`(`Order = null`) 既定値は、ルート`Order`を処理するための 0 (ゼロ)。 |
| 1, 2, &hellip; n | ルートの処理順序を指定します。 |

ルートの処理規則を確立するには。

* ルートが順番に処理される (-1、0、1、2、 &hellip; n)。
* ルートがある同じ`Order`最も低い特定のルートの順に特定のルートが一致します。
* ときに、同じルート`Order`と同じ数のパラメーターが要求 URL と一致、ルートに追加された順序で処理、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>します。

可能であれば、確立されたルートの処理順序に応じてしないでください。 一般に、ルーティングが URL に一致する適切なルートを選択します。 ルートを設定する必要がありますと`Order`をルーティングするプロパティの要求を正しく、アプリのルーティング スキームは、おそらくクライアントに混乱を維持するために脆弱です。 アプリのルーティング スキームを簡略化しようとしてください。 サンプル アプリでは、明示的なルートを 1 つのアプリを使用してルーティングのシナリオをいくつかを示すために注文の処理が必要です。 ただし、設定のルートのプラクティスを回避しようとする必要があります`Order`運用アプリでします。

Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。 MVC のトピックでルートの順序については、「[コント ローラー アクションへのルーティング。属性ルートの順序付け](xref:mvc/controllers/routing#ordering-attribute-routes)します。

## <a name="model-conventions"></a>モデルの規則

デリゲートを追加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>を追加する[モデル規則](xref:mvc/controllers/application-model#conventions)Razor ページに適用されています。

### <a name="add-a-route-model-convention-to-all-pages"></a>すべてのページにルート モデル規則を追加します。

使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>のコレクションに<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>ページ ルートの中に適用されるインスタンスの構築をモデル化します。

サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `1` が設定されます。 これにより、次のルートが一致のサンプル アプリで動作します。

* ルート テンプレート`TheContactPage/{text?}`トピックの後半で追加されます。 Contact ページのルートの既定の順序を持つ`null`(`Order = 0`) する前に、一致するよう、`{globalTemplate?}`ルート テンプレート。
* `{aboutTemplate?}`トピックの後半でルート テンプレートが追加されます。 `{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。 [About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order` プロパティが設定されるため `RouteData.Values["aboutTemplate"]` (`Order = 2`) には読み込まれません。
* `{otherPagesTemplate?}`トピックの後半でルート テンプレートが追加されます。 `{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。 いずれかのページ、*ページ/OtherPages*フォルダーはルート パラメーターで要求されます (たとえば、 `/OtherPages/Page1/RouteDataValue`)、"RouteDataValue"に読み込まれる`RouteData.Values["globalTemplate"]`(`Order = 1`) および not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`)設定により、`Order`プロパティ。

可能であれば、任意の場所を設定しないでください、 `Order`、その結果は`Order = 0`します。 適切なルートを選択するルーティングに依存します。

追加するなどの razor ページ オプション<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>、MVC がサービス コレクションに追加されたときに追加されます`Startup.ConfigureServices`します。 例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>すべてのページに、アプリ モデル規則を追加します。

使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>のコレクションに<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>中に適用されるインスタンスのページ アプリ モデルの構築。

この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。 クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。 これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。 完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。

サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>すべてのページに、ハンドラーのモデル規則を追加します。

使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>のコレクションに<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>中に適用されるインスタンスのページ ハンドラーのモデルの構築。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>ページ ルート アクション規則

派生した既定のルート モデル プロバイダー<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>ページ ルートを構成するための機能拡張ポイントを提供するように設計されている規則を呼び出します。

### <a name="folder-route-model-convention"></a>フォルダー ルート モデル規則

使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>上のアクションを呼び出す、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>のページにある、指定したフォルダーのすべての。

サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。 これにより、テンプレートを`{globalTemplate?}`(にこのトピックの設定`1`) 単一のルート値を指定した場合の最初のルート データ値の位置が優先されます。 場合内のページ、*ページ/OtherPages*フォルダーはルート パラメーターの値で要求されます (たとえば、 `/OtherPages/Page1/RouteDataValue`)、"RouteDataValue"は読み込ま`RouteData.Values["globalTemplate"]`(`Order = 1`) および not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`)設定により、`Order`プロパティ。

可能であれば、任意の場所を設定しないでください、 `Order`、その結果は`Order = 0`します。 適切なルートを選択するルーティングに依存します。

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>ページ ルート モデル規則

使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>上のアクションを呼び出す、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>の指定した名前のページ。

サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。 これにより、テンプレートを`{globalTemplate?}`(にこのトピックの設定`1`) 単一のルート値を指定した場合の最初のルート データ値の位置が優先されます。 [About] ページがあるルート パラメーター値で要求されたかどうか`/About/RouteDataValue`、"RouteDataValue"は読み込ま`RouteData.Values["globalTemplate"]`(`Order = 1`) および not `RouteData.Values["aboutTemplate"]` (`Order = 2`) 設定により、`Order`プロパティ。

可能であれば、任意の場所を設定しないでください、 `Order`、その結果は`Order = 0`します。 適切なルートを選択するルーティングに依存します。

`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>パラメーターのトランスフォーマーを使用して、ページ ルートをカスタマイズするには

ASP.NET Core によって生成されたページのルートは、パラメーターのトランスフォーマーを使用してカスタマイズできます。 パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。 たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。

`PageRouteTransformerConvention`ページ ルート モデル規則では、アプリのルートを自動的に生成されたページのフォルダーとファイル名のセグメントにパラメーターのトランスフォーマーが適用されます。 たとえば、Razor ページのファイルを */Pages/SubscriptionManagement/ViewAll.cshtml*のルートから書き直す必要が`/SubscriptionManagement/ViewAll`に`/subscription-management/view-all`。

`PageRouteTransformerConvention` Razor ページのフォルダーとファイル名から取得したページ ルートを自動的に生成されたセグメントを変換するだけです。 使用して追加のルート セグメントを変換されません、`@page`ディレクティブ。 規則もによって追加されたルートを変換しない<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>します。

`PageRouteTransformerConvention`オプションとして登録されて`Startup.ConfigureServices`:

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

::: moniker-end

## <a name="configure-a-page-route"></a>ページ ルートの構成

使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>指定したページのパスにあるページにルートを構成します。 そのページに対して生成されたリンクでは、指定したルートを使用します。 `AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。

サンプル アプリでは、*Contact.cshtml* の `/TheContactPage` へのルートを作成します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。

サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。 また、訪問者が `/Contact` ルートでページにアクセスする場合、ページの `@page` ディレクティブにはこの省略可能なセグメントも含まれます。

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。 追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。 レンダリングされたページには、'text' セグメントの値が示されています。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>ページ モデル アクション規則

既定のページ モデル プロバイダーを実装する<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>ページ モデルを構成するための機能拡張ポイントを提供するように設計されている規則を呼び出します。 これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。

このセクションの例については、サンプル アプリを使用して、`AddHeaderAttribute`はクラス、 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>、応答ヘッダーを適用します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。

**フォルダー アプリ モデル規則**

使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>上のアクションを呼び出す<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>の指定したフォルダーの下のすべてのページ インスタンス。

このサンプルでは、ヘッダー (`OtherPagesHeader`) をアプリの *OtherPages* フォルダー内にあるページに追加して、`AddFolderApplicationModelConvention` を使用するデモを実行します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

**ページ アプリ モデル規則**

使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>を作成し、追加、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>上のアクションを呼び出す、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>の指定した名前のページ。

サンプルでは、ヘッダー (`AboutHeader`) を [About] ページに追加して、`AddPageApplicationModelConvention` を使用するデモを実行します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

**フィルターの構成**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 指定したフィルターを適用するを構成します。 フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。 条件を満たすと、ヘッダーが追加されます。 満たさない場合は、`EmptyFilter` が適用されます。

`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。 アクション フィルターは、Razor ページによって無視されますので、`EmptyFilter`パスが含まれていないかどうかは意図どおりに影響を与えません`OtherPages/Page2`します。

`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

**フィルター ファクトリの構成**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 構成を適用する、指定されたファクトリ[フィルター](xref:mvc/controllers/filters)すべての Razor ページにします。

サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー (`FilterFactoryHeader`) を追加して、[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を使用する例を提供します。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC フィルターとページ フィルター (IPageFilter)

Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。 MVC フィルターの他の種類を使用するために使用できます。[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)します。 詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。

ページ フィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) Razor ページに適用されるフィルターです。 詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
