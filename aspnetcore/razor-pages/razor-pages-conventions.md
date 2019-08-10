---
title: ASP.NET Core での Razor ページのルートとアプリの規則
author: guardrex
description: ルートとアプリ モデル プロバイダーの規則が、ページのルーティング、検出、および処理の制御にどのように役立つかについて確認します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c93f169c422d260f738faba4812861521f383e51
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914977"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>ASP.NET Core での Razor ページのルートとアプリの規則

作成者: [Luke Latham](https://github.com/guardrex)

[ページ ルートとアプリ モデル プロバイダーの規則](xref:mvc/controllers/application-model#conventions)を使用して、Razor ページ アプリでページのルーティング、検出、および処理を制御する方法について説明します。

個別のページにカスタムのページ ルートを構成する必要がある場合、このトピックで後述される「[AddPageRoute convention](#configure-a-page-route)」で、ページへのルーティングを構成します。

ページルートを指定したり、ルートセグメントを追加したり、ルートにパラメーターを追加したり`@page`するには、ページのディレクティブを使用します。 詳細については、「[カスタムルート](xref:razor-pages/index#custom-routes)」を参照してください。

ルートセグメントまたはパラメーター名として使用できない予約語があります。 詳細については[、「ルーティング:予約済みの](xref:fundamentals/routing#reserved-routing-names)ルーティング名。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

| シナリオ | このサンプルでは、次のデモを実行します。 |
| -------- | --------------------------- |
| [モデルの規則](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | ルート テンプレートとヘッダーをアプリのページに追加します。 |
| [ページ ルート アクション規則](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | ルート テンプレートをフォルダー内のページおよび単一ページに追加します。 |
| [ページ モデル アクション規則](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (フィルター クラス、ラムダ式、またはフィルター ファクトリ)</li></ul> | ヘッダーをフォルダー内のページに追加し、ヘッダーを単一ページに追加し、ヘッダーをアプリのページに追加するように[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を構成します。 |

Razor Pages 規則は、 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>拡張<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>メソッドを使用して、 `Startup`クラスのサービスコレクションに対して追加および構成されます。 次の規則の例は、このトピックで後述されます。

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

ルートは、 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>処理 (ルート照合) のを指定します。

| 注文            | 動作 |
| :--------------: | -------- |
| -1               | ルートは、他のルートが処理される前に処理されます。 |
| 0                | 順序が指定されていません (既定値)。 [割り当て`Order`なし`Order = null`] () の`Order`場合、処理のためにルートは 0 (ゼロ) に設定されます。 |
| 1、2、 &hellip; n | ルートの処理順序を指定します。 |

ルート処理は規約によって確立されます。

* ルートは順番に処理されます (-1、0、1、 &hellip; 2、n)。
* ルートが同じ`Order`である場合、最も具体的なルートは最初に照合され、それによって特定のルートが小さくなります。
* 同じ`Order`と同数のパラメーターを持つルートが要求 URL と一致する場合、ルートは<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>に追加された順序で処理されます。

可能な場合は、確立されたルート処理順序に依存しないようにしてください。 通常、ルーティングでは、URL が一致する正しいルートが選択されます。 要求を正しくルーティングする`Order`ようにルートのプロパティを設定する必要がある場合、アプリケーションのルーティングスキームは、クライアントが混乱して保守が脆弱になることがあります。 アプリのルーティングスキームを簡略化するためにシークします。 このサンプルアプリでは、単一のアプリを使用した複数のルーティングシナリオを示すために、明示的なルート処理を行う必要があります。 ただし、運用環境のアプリではルート`Order`を設定しないようにすることをお勧めします。

Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。 MVC トピックのルートの順序に関する情報は、 [「コントローラーアクションへのルーティング」で参照できます。属性ルート](xref:mvc/controllers/routing#ordering-attribute-routes)の順序付け。

## <a name="model-conventions"></a>モデルの規則

の<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>デリゲートを追加して、Razor Pages に適用される[モデル規則](xref:mvc/controllers/application-model#conventions)を追加します。

### <a name="add-a-route-model-convention-to-all-pages"></a>ルートモデルの規則をすべてのページに追加する

を<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>作成し、ページルート<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>モデルの構築中<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>に適用されるインスタンスのコレクションにを追加するには、を使用します。

サンプル アプリでは、`{globalTemplate?}` ルート テンプレートをアプリ内のすべてのページに追加します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `1` が設定されます。 これにより、サンプルアプリで次のルート一致動作が保証されます。

* の`TheContactPage/{text?}`ルートテンプレートは、このトピックの後半で追加します。 連絡先ページのルートの既定の`null`順序は (`Order = 0`) であるため、ルートテンプレートの`{globalTemplate?}`前に一致します。
* `{aboutTemplate?}`ルートテンプレートは、このトピックの後半で追加します。 `{aboutTemplate?}` テンプレートの `Order` には `2` が指定されます。 [About] ページが `/About/RouteDataValue` で要求されると、"RouteDataValue" は `RouteData.Values["globalTemplate"]` (`Order = 1`) に読み込まれ、`Order` プロパティが設定されるため `RouteData.Values["aboutTemplate"]` (`Order = 2`) には読み込まれません。
* `{otherPagesTemplate?}`ルートテンプレートは、このトピックの後半で追加します。 `{otherPagesTemplate?}` テンプレートの `Order` には `2` が指定されます。 *Pages/otherpages*フォルダー内のいずれかのページが`/OtherPages/Page1/RouteDataValue`ルートパラメーター (など) で要求されると、"routedatavalue" は ( `RouteData.Values["globalTemplate"]` `Order = 1` `RouteData.Values["otherPagesTemplate"]` `Order = 2` )に読み込まれ、`Order`プロパティ。

可能な限り、を設定`Order`しないで`Order = 0`ください。 正しいルートを選択するには、ルーティングに依存します。

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>の`Startup.ConfigureServices`サービスコレクションに MVC を追加すると、Razor Pages のオプション ([追加] など) が追加されます。 例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)を参照してください。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

`localhost:5000/About/GlobalRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>すべてのページにアプリモデル規則を追加する

を作成し、ページアプリ<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>モデルの構築中<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>に適用されるインスタンスのコレクションにを追加します。 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>

この規則やこのトピックで後述されるその他の規則のデモを実行するには、サンプル アプリに `AddHeaderAttribute` クラスを含めます。 クラス コンストラクターは、`name` 文字列と `values` 文字列配列を受け入れます。 これらの値は、応答ヘッダーを設定するために、その `OnResultExecuting` メソッド内で使用されます。 完全クラスは、このトピックで後述される「[ページ モデル アクション規則](#page-model-action-conventions)」セクションで示されます。

サンプル アプリでは、`AddHeaderAttribute` クラスを使用して、ヘッダー (`GlobalHeader`) をアプリ内のすべてのページに追加します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

*Startup.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、GlobalHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>すべてのページにハンドラーモデル規則を追加する

を<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>作成し、ページハンドラー <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>モデルの構築時<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>に適用されるインスタンスのコレクションにを追加するには、を使用します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

*Startup.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a>ページ ルート アクション規則

から<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>派生する既定のルートモデルプロバイダーは、ページルートを構成するための機能拡張ポイントを提供するように設計された規約を呼び出します。

### <a name="folder-route-model-convention"></a>フォルダールートモデルの規則

を<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>使用して、 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>指定<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>したフォルダーの下にあるすべてのページに対してアクションを呼び出すを作成および追加します。

サンプル アプリでは <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> を使用して、`{otherPagesTemplate?}` ルート テンプレートを *OtherPages* フォルダーのページに追加します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。 これにより、単一の`{globalTemplate?}`ルート値が指定されて`1`いる場合に、のテンプレート (トピックで前述した) が、最初のルートデータ値の位置に優先されます。 *Pages/otherpages*フォルダー内`/OtherPages/Page1/RouteDataValue`のページがルートパラメーター値 (など) で要求されている場合、"routedatavalue"`Order = 1`は () `RouteData.Values["globalTemplate"]`に`RouteData.Values["otherPagesTemplate"]` `Order = 2`読み込まれ、`Order`プロパティ。

可能な限り、を設定`Order`しないで`Order = 0`ください。 正しいルートを選択するには、ルーティングに依存します。

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` でサンプルの Page1 ページを要求し、その結果を調べます。

![OtherPages フォルダーの Page1 は、GlobalRouteValue と OtherPagesRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>ページルートモデルの規則

を作成し、指定し<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>た名前を持つページ<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>のに対してアクションを呼び出すを追加します。 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>

サンプル アプリでは `AddPageRouteModelConvention` を使用して、`{aboutTemplate?}` ルート テンプレートを [About] ページに追加します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> の <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> プロパティに `2` が設定されます。 これにより、単一の`{globalTemplate?}`ルート値が指定されて`1`いる場合に、のテンプレート (トピックで前述した) が、最初のルートデータ値の位置に優先されます。 [About] ページが`/About/RouteDataValue`でルートパラメーター値と共に要求された場合、プロパティの`Order`設定`RouteData.Values["globalTemplate"]`により、" `RouteData.Values["aboutTemplate"]` routedatavalue" は (`Order = 1`) に読み込まれ、(`Order = 2`) ではありません。

可能な限り、を設定`Order`しないで`Order = 0`ください。 正しいルートを選択するには、ルーティングに依存します。

`localhost:5000/About/GlobalRouteValue/AboutRouteValue` でサンプルの [About] ページを要求し、その結果を調べます。

![[About] ページは、GlobalRouteValue と AboutRouteValue のルート セグメントで要求されます。 レンダリングされたページは、ルート データの値がページの OnGet メソッドでキャプチャされたことを示しています。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>パラメータートランスフォーマーを使用してページルートをカスタマイズする

ASP.NET Core によって生成されたページルートは、パラメータートランスフォーマーを使用してカスタマイズできます。 パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。 たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。

ページ`PageRouteTransformerConvention`ルートモデルの規則では、アプリケーションで自動的に生成されたページルートのフォルダーとファイル名のセグメントにパラメータートランスフォーマーが適用されます。 たとえば、 *//* //の Razor Pages ファイルでは、ルートはから`/SubscriptionManagement/ViewAll`に`/subscription-management/view-all`書き直されます。

`PageRouteTransformerConvention`は、Razor Pages フォルダーとファイル名から取得した、自動的に生成されたページルートのセグメントのみを変換します。 `@page`ディレクティブを使用して追加されたルートセグメントは変換されません。 この規則では、によって<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>追加されたルートを変換することもありません。

は`PageRouteTransformerConvention` 、の`Startup.ConfigureServices`オプションとして登録されます。

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

指定<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>したページパスにあるページへのルートを構成するには、を使用します。 そのページに対して生成されたリンクでは、指定したルートを使用します。 `AddPageRoute` では、`AddPageRouteModelConvention` を使用してルートを確立します。

サンプル アプリでは、*Contact.cshtml* の `/TheContactPage` へのルートを作成します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

[Contact] ページには、既定のルート経由の `/Contact` でアクセスすることもできます。

サンプル アプリの [Contact] ページに対するカスタム ルートでは、省略可能な `text` ルート セグメント (`{text?}`) を許可します。 また、訪問者が `/Contact` ルートでページにアクセスする場合、ページの `@page` ディレクティブにはこの省略可能なセグメントも含まれます。

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

レンダリングされたページの **Contact** リンク用に生成された URL には、更新されたルートが反映されることに注意してください。

![ナビゲーション バーのサンプル アプリの [Contact] リンク](razor-pages-conventions/_static/contact-link.png)

![レンダリングされた HTML の [Contact] リンクを調べると、href に '/TheContactPage' が設定されています](razor-pages-conventions/_static/contact-link-source.png)

通常のルート (`/Contact`) またはカスタム ルート (`/TheContactPage`) のいずれかで、[Contact] ページにアクセスします。 追加の `text` ルート セグメントを指定した場合、ページには指定した HTML エンコードのセグメントが示されます。

![URL の 'TextValue' の省略可能な 'text' ルート セグメントを適用する Microsoft Edge ブラウザーの例。 レンダリングされたページには、'text' セグメントの値が示されています。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>ページ モデル アクション規則

ページモデルを構成するための<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>機能拡張ポイントを提供するように設計された呼び出し規約を実装する既定のページモデルプロバイダー。 これらの規則は、ページ検出をビルドおよび変更したり、シナリオを処理したりするときに便利です。

このセクションの例では、サンプルアプリは、応答`AddHeaderAttribute`ヘッダーを適用するクラス<xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>() を使用します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

規則を使用して、このサンプルでは、フォルダー内のすべてのページおよび単一ページに属性を適用する方法のデモを実行します。

**フォルダー アプリ モデル規則**

を作成し、指定し<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>たフォルダーの下に<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>あるすべてのページのインスタンスに対してアクションを呼び出すを追加します。 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>

このサンプルでは、ヘッダー (`OtherPagesHeader`) をアプリの *OtherPages* フォルダー内にあるページに追加して、`AddFolderApplicationModelConvention` を使用するデモを実行します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

`localhost:5000/OtherPages/Page1` でサンプルの Page1 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPages/Page1 ページの応答ヘッダーは、OtherPagesHeader が追加されていることを示しています。](razor-pages-conventions/_static/page1-otherpages-header.png)

**ページ アプリ モデル規則**

を作成し、指定し<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>た名前を持つページ<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>のに対してアクションを呼び出すを追加します。 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>

サンプルでは、ヘッダー (`AboutHeader`) を [About] ページに追加して、`AddPageApplicationModelConvention` を使用するデモを実行します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、AboutHeader が追加されたことを示しています。](razor-pages-conventions/_static/about-page-about-header.png)

**フィルターの構成**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>適用するように指定されたフィルターを構成します。 フィルター クラスを実装できますが、サンプル アプリでは、ラムダ式でフィルターを実装する方法を示しています。これは、フィルターを返すファクトリとしてバックグラウンドで実装されます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

ページ アプリ モデルは、*OtherPages* フォルダーの Page2 ページにつながるセグメントの相対パスを確認するために使用されます。 条件を満たすと、ヘッダーが追加されます。 満たさない場合は、`EmptyFilter` が適用されます。

`EmptyFilter` は[アクション フィルター](xref:mvc/controllers/filters#action-filters)です。 アクションフィルターは Razor Pages によって無視さ`EmptyFilter`れるため、パスにが含まれ`OtherPages/Page2`ていない場合、は意図したとおりには効果がありません。

`localhost:5000/OtherPages/Page2` でサンプルの Page2 ページを要求し、そのヘッダーを調べて結果を確認します。

![OtherPagesPage2Header は、Page2 への応答に追加されます。](razor-pages-conventions/_static/page2-filter-header.png)

**フィルター ファクトリの構成**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>指定したファクトリを、すべての Razor Pages に[フィルター](xref:mvc/controllers/filters)を適用するように構成します。

サンプル アプリでは、アプリのページに対する 2 つの値と共にヘッダー (`FilterFactoryHeader`) を追加して、[フィルター ファクトリ](xref:mvc/controllers/filters#ifilterfactory)を使用する例を提供します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

*AddHeaderWithFactory.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

`localhost:5000/About` でサンプルの [About] ページを要求し、そのヘッダーを調べて結果を確認します。

![[About] ページの応答ヘッダーは、FilterFactoryHeader ヘッダーが 2 つ追加されたことを示しています。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC フィルターとページ フィルター (IPageFilter)

Razor ページはハンドラー メソッドを使用するため、MVC [アクション フィルター](xref:mvc/controllers/filters#action-filters)は Razor ページによって無視されます。 他の種類の MVC フィルターを使用することもできます。[承認](xref:mvc/controllers/filters#authorization-filters)、[例外](xref:mvc/controllers/filters#exception-filters)、[リソース](xref:mvc/controllers/filters#resource-filters)、および[結果](xref:mvc/controllers/filters#result-filters)。 詳細については、「[フィルター](xref:mvc/controllers/filters)」トピックを参照してください。

ページフィルター (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) は、Razor Pages に適用されるフィルターです。 詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
