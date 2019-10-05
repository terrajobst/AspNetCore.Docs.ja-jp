---
title: ASP.NET Core のグローバリゼーションおよびローカリゼーション
author: rick-anderson
description: ASP.NET Core がコンテンツをさまざまな言語と文化にローカライズするために提供するサービスとミドルウェアについて説明します。
ms.author: riande
ms.date: 01/14/2017
uid: fundamentals/localization
ms.openlocfilehash: 6dfbeae201a3586dfea6620917083130c4985b22
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703811"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="4fa2d-103">ASP.NET Core のグローバリゼーションおよびローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="4fa2d-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="4fa2d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="4fa2d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="4fa2d-105">このドキュメントが ASP.NET Core 3.0 用に更新されるまでは、Hisham のブログ「[What is new in Localization in ASP.NET Core 3.0](http://hishambinateya.com/what-is-new-in-localization-in-asp.net-core-3.0)」(ASP.NET Core 3.0 のローカライズの新機能) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-105">Until this document is updated for ASP.NET Core 3.0, see Hisham's Blog [What is new in Localization in ASP.NET Core 3.0](http://hishambinateya.com/what-is-new-in-localization-in-asp.net-core-3.0).</span></span>

<span data-ttu-id="4fa2d-106">ASP.NET Core で多言語の Web サイトを作成すると、より幅広い対象者がサイトにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-106">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="4fa2d-107">ASP.NET Core は、さまざまな言語と文化にローカライズするためのサービスとミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-107">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="4fa2d-108">国際化には、[グローバリゼーション](/dotnet/api/system.globalization)と[ローカリゼーション](/dotnet/standard/globalization-localization/localization)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-108">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="4fa2d-109">グローバリゼーションとは異なるカルチャをサポートするアプリを設計するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-109">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="4fa2d-110">グローバリゼーションによって、特定の地域に関連する定義済みの言語セットの入出力と表示のサポートが追加されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-110">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="4fa2d-111">ローカリゼーションとは、ローカライズのために既に処理されているグローバル化されたアプリを特定のカルチャ/ロケールに適合させるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-111">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="4fa2d-112">詳細については、本ドキュメントの末尾にある「**グローバリゼーションとローカリゼーションの用語**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-112">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="4fa2d-113">アプリのローカリゼーションには、以下のものが関与します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-113">App localization involves the following:</span></span>

1. <span data-ttu-id="4fa2d-114">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="4fa2d-114">Make the app's content localizable</span></span>

2. <span data-ttu-id="4fa2d-115">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-115">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="4fa2d-116">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-116">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="4fa2d-117">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="4fa2d-118">アプリのコンテンツをローカライズできるようにする</span><span class="sxs-lookup"><span data-stu-id="4fa2d-118">Make the app's content localizable</span></span>

<span data-ttu-id="4fa2d-119">ASP.NET Core で導入された `IStringLocalizer` と `IStringLocalizer<T>` は、ローカライズされたアプリの開発時に生産性を向上させるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-119">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="4fa2d-120">`IStringLocalizer` は、[ResourceManager](/dotnet/api/system.resources.resourcemanager) と [ResourceReader](/dotnet/api/system.resources.resourcereader) を使用して、実行時にカルチャ固有のリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-120">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="4fa2d-121">シンプルなインターフェイスには、ローカライズされた文字列を返すためのインデクサーと `IEnumerable` があります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-121">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="4fa2d-122">`IStringLocalizer` では、リソース ファイルに既定の言語文字列を格納する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-122">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="4fa2d-123">ローカリゼーションの対象となるアプリを開発することができ、開発の早い段階でリソース ファイルを作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="4fa2d-124">次のコードは、ローカリゼーションのために文字列 "About Title" をラップする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="4fa2d-125">上記のコードで、`IStringLocalizer<T>` の実装は、[依存関係の挿入](dependency-injection.md)によって実行されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-125">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="4fa2d-126">"About Title" のローカライズされた値が見つからない場合、インデクサーのキー、つまり文字列 "About Title" が返されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="4fa2d-127">アプリで、既定の言語のリテラル文字列をそのままにし、ローカライザーにそれらをラップすることができるので、アプリの開発に専念することができます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="4fa2d-128">既定の言語でアプリを開発し、既定のリソース ファイルを最初に作成せずに、ローカリゼーション手順用に準備します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="4fa2d-129">または、従来のアプローチを使用し、既定の言語文字列を取得するキーを提供できます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="4fa2d-130">既定の言語の *.resx* ファイルを使用せずに、文字列リテラルをラップするだけの新しいワークフローは、多くの開発者にとって、アプリをローカライズする際のオーバーヘッドの削減になります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="4fa2d-131">他の開発者は、長い文字列リテラルの操作が容易で、ローカライズされた文字列を更新しやすい従来のワークフローを好みます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="4fa2d-132">HTML を格納しているリソースには、`IHtmlLocalizer<T>` の実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="4fa2d-133">`IHtmlLocalizer` HTML は、リソース文字列でフォーマットされた引数をエンコードしますが、リソース文字列自体は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="4fa2d-134">下の強調表示されたサンプルでは、`name` パラメーターの値のみが HTML エンコードされます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="4fa2d-135">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="4fa2d-136">最下位のレベルでは、[依存関係の挿入](dependency-injection.md)から `IStringLocalizerFactory` を取得できます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="4fa2d-137">上記のコードは、2 つの各ファクトリ作成メソッドを示しています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="4fa2d-138">ローカライズされた文字列は、コントローラーまたは領域で仕切るか、1 つのコンテナーにすることができます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="4fa2d-139">サンプル アプリでは、共有されるリソースのために `SharedResource` というダミー クラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="4fa2d-140">一部の開発者は、`Startup` クラスを使用してグローバル文字列または共有文字列を格納します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="4fa2d-141">下のサンプルでは、`InfoController` と `SharedResource` のローカライザーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="4fa2d-142">ビューのローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="4fa2d-142">View localization</span></span>

<span data-ttu-id="4fa2d-143">`IViewLocalizer` サービスは、[ビュー](xref:mvc/views/overview)のローカライズされた文字列を提供します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="4fa2d-144">`ViewLocalizer` クラスは、このインターフェイスを実装し、ビューのファイル パスからリソースの場所を見つけます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="4fa2d-145">次のコードは、`IViewLocalizer` の既定の実装の使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="4fa2d-146">`IViewLocalizer` の既定の実装は、ビューのファイル名に基づいてリソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="4fa2d-147">グローバルな共有リソース ファイルを使用するオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="4fa2d-148">`ViewLocalizer` は、`IHtmlLocalizer` を使用してローカライザーを実装するので、Razor は、ローカライズされた文字列を HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="4fa2d-149">リソース文字列をパラメーター化することができます。`IViewLocalizer` は、パラメーターを HTML エンコードしますが、リソース文字列は HTML エンコードしません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="4fa2d-150">次の Razor マークアップについて考えます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="4fa2d-151">フランス語のリソース ファイルには次の値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="4fa2d-152">キー</span><span class="sxs-lookup"><span data-stu-id="4fa2d-152">Key</span></span> | <span data-ttu-id="4fa2d-153">[値]</span><span class="sxs-lookup"><span data-stu-id="4fa2d-153">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="4fa2d-154">描画されたビューには、リソース ファイルからの HTML マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-154">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="4fa2d-155">**注:** 一般的に、HTML ではなく、テキストのみをローカライズする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-155">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="4fa2d-156">ビュー内の共有リソース ファイルを使用するには、`IHtmlLocalizer<T>` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-156">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="4fa2d-157">DataAnnotations のローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="4fa2d-157">DataAnnotations localization</span></span>

<span data-ttu-id="4fa2d-158">DataAnnotations エラー メッセージは `IStringLocalizer<T>` を使用してローカライズします。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-158">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="4fa2d-159">オプション `ResourcesPath = "Resources"` を使用して、`RegisterViewModel` のエラー メッセージを次のいずれかのパスに格納できます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-159">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="4fa2d-160">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="4fa2d-160">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="4fa2d-161">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="4fa2d-161">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="4fa2d-162">ASP.NET Core MVC 1.1.0 以上では、非検証属性がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-162">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="4fa2d-163">ASP.NET Core MVC 1.0 は、非検証属性のローカライズされた文字列を検索**しません**。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-163">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="4fa2d-164">複数のクラスに 1 つのリソース文字列を使用する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-164">Using one resource string for multiple classes</span></span>

<span data-ttu-id="4fa2d-165">次のコードは、複数のクラスに検証属性の 1 つのリソース文字列を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-165">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="4fa2d-166">上記のコードでは、`SharedResource` は、resx に対応するクラスであり、ここに検証メッセージが格納されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-166">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="4fa2d-167">この方法では、DataAnnotations は、各クラスのリソースではなく、`SharedResource` のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-167">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="4fa2d-168">サポートする言語およびカルチャのローカライズされたリソースを提供する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-168">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="4fa2d-169">SupportedCultures と SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="4fa2d-169">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="4fa2d-170">ASP.NET Core では、`SupportedCultures` と `SupportedUICultures` という 2 つのカルチャ値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-170">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="4fa2d-171">日付、数値、および通貨の書式設定など、カルチャに依存する関数の結果は、`SupportedCultures` の [CultureInfo](/dotnet/api/system.globalization.cultureinfo) オブジェクトによって決まります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-171">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="4fa2d-172">テキストの並べ替え順序、大文字と小文字の表記規則、文字列比較も `SupportedCultures` によって決まります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-172">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="4fa2d-173">サーバーがカルチャを取得する方法の詳細については、「[CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-173">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="4fa2d-174">`SupportedUICultures` は、 *.resx* ファイルからのどの翻訳文字列が [ResourceManager](/dotnet/api/system.resources.resourcemanager) によって検索されるかを決定します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-174">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="4fa2d-175">`ResourceManager` は、`CurrentUICulture` によって決定されるカルチャ固有の文字列を単に検索します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-175">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="4fa2d-176">.NET のすべてのスレッドに `CurrentCulture` オブジェクトと `CurrentUICulture`オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-176">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="4fa2d-177">ASP.NET Core は、カルチャに依存する関数を表示するときに、これらの値を検査します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-177">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="4fa2d-178">たとえば、現在のスレッドのカルチャが "en-US" (英語、米国) に設定されている場合は、`DateTime.Now.ToLongDateString()` は、"Thursday, February 18, 2016" を表示しますが、`CurrentCulture` が "es-ES" (スペイン語、スペイン) に設定されている場合、出力は "jueves, 18 de febrero de 2016" になります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-178">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="4fa2d-179">リソース ファイル</span><span class="sxs-lookup"><span data-stu-id="4fa2d-179">Resource files</span></span>

<span data-ttu-id="4fa2d-180">リソース ファイルは、ローカライズ可能な文字列をコードから分離するために役立つメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-180">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="4fa2d-181">既定以外の言語の翻訳された文字列は、分離された *.resx* リソース ファイルです。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-181">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="4fa2d-182">たとえば、翻訳された文字列を含む *Welcome.es.resx* という名前のスペイン語のリソース ファイルを作成したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-182">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="4fa2d-183">"es" は、スペイン語の言語コードです。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-183">"es" is the language code for Spanish.</span></span> <span data-ttu-id="4fa2d-184">Visual Studio でこのリソース ファイルを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-184">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="4fa2d-185">**ソリューション エクスプローラー**で、リソース ファイルが格納されているフォルダーを右クリックし、 **[追加]**  >  **[新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-185">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![入れ子になったコンテキスト メニュー:ソリューション エクスプローラーで、リソースのコンテキスト メニューが開かれます。](localization/_static/newi.png)

2. <span data-ttu-id="4fa2d-188">**インストールされているテンプレートの検索**ボックスに、「resource」と入力し、ファイルに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-188">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![[新しい項目の追加] ダイアログ](localization/_static/res.png)

3. <span data-ttu-id="4fa2d-190">キーの値 (ネイティブの文字列) を **[名前]** 列に入力し、翻訳された文字列を **[値]** 列に入力します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-190">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx ファイル (スペイン語の Welcome リソース ファイル) と、[名前] 列の Hello という単語と、[値] 列の Hola という単語 (スペイン語のこんにちは)](localization/_static/hola.png)

    <span data-ttu-id="4fa2d-192">Visual Studio に *Welcome.es.resx* ファイルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-192">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Welcome スペイン語リソース ファイルを表示しているソリューション エクスプローラー](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="4fa2d-194">リソース ファイルの名前付け</span><span class="sxs-lookup"><span data-stu-id="4fa2d-194">Resource file naming</span></span>

<span data-ttu-id="4fa2d-195">リソースの名前は、クラスの完全な型名からアセンブリ名を除いたものになります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-195">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="4fa2d-196">たとえば、メイン アセンブリが `LocalizationWebsite.Web.dll` であるプロジェクト内のクラス `LocalizationWebsite.Web.Startup` のフランス語のリソースは、*Startup.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-196">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="4fa2d-197">クラス `LocalizationWebsite.Web.Controllers.HomeController` のリソースは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-197">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="4fa2d-198">対象となるクラスの名前空間が、アセンブリ名と同じでない場合は、完全な型名が必要です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-198">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="4fa2d-199">たとえば、同じプロジェクトで、型 `ExtraNamespace.Tools` のリソースは、*ExtraNamespace.Tools.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-199">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="4fa2d-200">サンプル プロジェクトで、`ConfigureServices` メソッドは `ResourcesPath` を "Resources" に設定するので、ホーム コントローラーのフランス語のりソース ファイルの相対パスは、*Resources/Controllers.HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-200">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="4fa2d-201">あるいは、フォルダーを使用してリソース ファイルを整理することもできます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-201">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="4fa2d-202">Home コント ローラーのパスは、*Resources/Controllers/HomeController.fr.resx* です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-202">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="4fa2d-203">`ResourcesPath` オプションを使用しない場合、 *.resx* ファイルは、プロジェクトの基本ディレクトリに置かれます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-203">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="4fa2d-204">`HomeController` のリソース ファイルは、*Controllers.HomeController.fr.resx* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-204">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="4fa2d-205">ドットまたはパスの名前付け規則の選択は、リソース ファイルを整理する方法によって決まります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-205">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="4fa2d-206">リソース名</span><span class="sxs-lookup"><span data-stu-id="4fa2d-206">Resource name</span></span> | <span data-ttu-id="4fa2d-207">ドットまたはパスの名前付け</span><span class="sxs-lookup"><span data-stu-id="4fa2d-207">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="4fa2d-208">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="4fa2d-208">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="4fa2d-209">ドット</span><span class="sxs-lookup"><span data-stu-id="4fa2d-209">Dot</span></span>  |
| <span data-ttu-id="4fa2d-210">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="4fa2d-210">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="4fa2d-211">パス</span><span class="sxs-lookup"><span data-stu-id="4fa2d-211">Path</span></span> |
|    |     |

<span data-ttu-id="4fa2d-212">Razor ビューの `@inject IViewLocalizer` を使用するリソース ファイルは同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-212">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="4fa2d-213">ビューのリソース ファイルには、ドットの名前付けまたはパスの名前付けを使用して名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-213">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="4fa2d-214">Razor ビューのリソース ファイルは、関連するビュー ファイルのパスを模倣します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-214">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="4fa2d-215">`ResourcesPath` を "Resources" に設定すると仮定すると、*Views/Home/About.cshtml* ビューに関連付けられるフランス語のリソース ファイルは、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-215">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="4fa2d-216">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="4fa2d-216">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="4fa2d-217">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="4fa2d-217">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="4fa2d-218">`ResourcesPath` オプションを使用しない場合、ビューの *.resx* ファイルは、ビューと同じフォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-218">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="4fa2d-219">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="4fa2d-219">RootNamespaceAttribute</span></span> 

<span data-ttu-id="4fa2d-220">アセンブリのルート名前空間がアセンブリ名と異なると、[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性によってアセンブリのルート名前空間が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-220">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

<span data-ttu-id="4fa2d-221">アセンブリのルート名前空間がアセンブリ名と異なる場合:</span><span class="sxs-lookup"><span data-stu-id="4fa2d-221">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="4fa2d-222">既定では、ローカライズが機能しません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-222">Localization does not work by default.</span></span>
* <span data-ttu-id="4fa2d-223">アセンブリ内でのリソースの検索方法が原因で、ローカライズが失敗します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-223">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="4fa2d-224">`RootNamespace` はビルド時の値で、実行中のプロセスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-224">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="4fa2d-225">`RootNamespace` が `AssemblyName` と異なる場合、*AssemblyInfo.cs* (パラメーターの値は実際の値に置き換えられます) に次を含めてください。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-225">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="4fa2d-226">上記のコードにより、resx ファイルが正常に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-226">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="4fa2d-227">カルチャ フォールバック動作</span><span class="sxs-lookup"><span data-stu-id="4fa2d-227">Culture fallback behavior</span></span>

<span data-ttu-id="4fa2d-228">リソースを探すとき、ローカリゼーションは、"カルチャ フォールバック" を行います。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-228">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="4fa2d-229">要求されたカルチャが存在しない場合、そのカルチャの親カルチャに戻ります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-229">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="4fa2d-230">なお、[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) プロパティは親カルチャを示します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-230">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="4fa2d-231">つまり、通常は (必ずではありませんが) ISO から国の識別子が削除されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-231">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="4fa2d-232">たとえば、メキシコで使われているスペイン語は、"es-MX" です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-232">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="4fa2d-233">これには、どの国にも固有でないスペイン語の "es" が親として付いています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-233">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="4fa2d-234">カルチャ "fr-CA" が使用された、"ようこそ" リソースの要求をサイトで受信するとします。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-234">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="4fa2d-235">ローカリゼーション システムでは、次の順番でリソースを検索し、最初の一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-235">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="4fa2d-236">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="4fa2d-236">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="4fa2d-237">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="4fa2d-237">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="4fa2d-238">*Welcome.resx* (`NeutralResourcesLanguage` が "fr-CA" の場合)</span><span class="sxs-lookup"><span data-stu-id="4fa2d-238">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="4fa2d-239">例として、".fr" カルチャ指定子を削除し、カルチャを French に設定した場合、既定のリソース ファイルは read で文字列がローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-239">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="4fa2d-240">リソース マネージャーは、要求されたカルチャを満たすものがない場合、既定またはフォールバックのリソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-240">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="4fa2d-241">要求されたカルチャのリソースが見つからないときにキーのみを返す場合は、既定のリソース ファイルを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-241">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="4fa2d-242">Visual Studio でリソース ファイルを生成する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-242">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="4fa2d-243">Visual Studio で、ファイル名にカルチャを指定せずにリソース ファイルを作成する場合 (たとえば、*Welcome.resx*)、Visual Studio は各文字列のプロパティを使用して、C# クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-243">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="4fa2d-244">通常、ASP.NET Core ではこれを行いません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-244">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="4fa2d-245">一般的に既定の *.resx* リソース ファイル (カルチャ名のない *.resx* ファイル) は使用しません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-245">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="4fa2d-246">カルチャ名を含む *.resx* ファイル (たとえば *Welcome.fr.resx*) を作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-246">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="4fa2d-247">カルチャ名を含む *.resx* ファイルを作成すると、Visual Studio はクラス ファイルを生成しません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-247">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span> <span data-ttu-id="4fa2d-248">多くの開発者は既定の言語リソース ファイルを作成しないと予想されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-248">We anticipate that many developers won't create a default language resource file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="4fa2d-249">その他のカルチャを追加する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-249">Add other cultures</span></span>

<span data-ttu-id="4fa2d-250">各言語とカルチャの組み合わせ (既定の言語以外) ごとに一意のリソース ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="4fa2d-251">ISO 言語コードがファイル名の一部となるリソース ファイル (たとえば、**en-us**、**fr-ca**、**en-gb**) を新規作成することで、異なるカルチャとロケールのリソース ファイルを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="4fa2d-252">*Welcome.es-MX.resx* (スペイン語/メキシコ) のように、ファイル名と *.resx* ファイル拡張子の間にこれらの ISO コードが置かれます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="4fa2d-253">要求ごとに言語/カルチャを選択するための戦略を実装する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="4fa2d-254">ローカリゼーションを構成する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-254">Configure localization</span></span>

<span data-ttu-id="4fa2d-255">ローカリゼーションは、`Startup.ConfigureServices` メソッドで構成されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="4fa2d-256">`AddLocalization` ローカリゼーション サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="4fa2d-257">上記のコードは、リソース パスを "Resources" に設定します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="4fa2d-258">`AddViewLocalization` ローカライズされたビュー ファイルのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="4fa2d-259">このサンプル ビューでは、ローカリゼーションは、ビュー ファイルのサフィックスに基づいています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="4fa2d-260">たとえば、*Index.fr.cshtml* ファイルの "fr" です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="4fa2d-261">`AddDataAnnotationsLocalization` `IStringLocalizer` 抽象化を介してローカライズされた `DataAnnotations` 検証メッセージのサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="4fa2d-262">ローカリゼーション ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="4fa2d-262">Localization middleware</span></span>

<span data-ttu-id="4fa2d-263">要求時に現在のカルチャが、ローカリゼーション [ミドルウェア](xref:fundamentals/middleware/index)で設定されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="4fa2d-264">ローカリゼーション ミドルウェアは、`Startup.Configure` メソッドで有効になります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="4fa2d-265">要求のカルチャをチェックする可能性があるすべてのミドルウェア (たとえば `app.UseMvcWithDefaultRoute()`) の前に、ローカリゼーション ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]

<span data-ttu-id="4fa2d-266">`UseRequestLocalization` は `RequestLocalizationOptions` オブジェクトを初期化します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="4fa2d-267">すべての要求で、`RequestLocalizationOptions` の `RequestCultureProvider` のリストが列挙され、要求のカルチャを正常に決定できる最初のプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="4fa2d-268">既定のプロバイダーは `RequestLocalizationOptions` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="4fa2d-269">既定のリストは、最も具体的なものから最も具体的でないものの順番になります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="4fa2d-270">記事の後半では、この順序を変更する方法、さらにカスタム カルチャ プロバイダーを追加する方法も説明します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="4fa2d-271">要求のカルチャを判断できるプロバイダーがない場合、`DefaultRequestCulture` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="4fa2d-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="4fa2d-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="4fa2d-273">いくつかのアプリでは、クエリ文字列を使用して、[カルチャおよび UI カルチャ](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)を設定します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="4fa2d-274">Cookie または Accept-language ヘッダーのアプローチを使用するアプリの場合、URL にクエリ文字列を追加すると、デバッグおよびコードのテストに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="4fa2d-275">既定では、`QueryStringRequestCultureProvider` が、`RequestCultureProvider` リストの最初のローカリゼーション プロバイダーとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="4fa2d-276">クエリ文字列パラメーター `culture` と `ui-culture` を渡します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="4fa2d-277">次の例では、特定のカルチャ (言語および地域) をスペイン語/メキシコに設定します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="4fa2d-278">2 つのいずれかのみ (`culture` または `ui-culture`) を渡した場合、クエリ文字列プロバイダーは、渡した 1 つのパラメーターを使用して両方の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="4fa2d-279">たとえば、カルチャだけを設定すると、`Culture` と `UICulture` の両方が設定されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="4fa2d-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="4fa2d-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="4fa2d-281">多くの場合、実稼働アプリケーションは、ASP.NET Core カルチャ Cookie を使用してカルチャを設定するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="4fa2d-282">Cookie を作成するには、`MakeCookieValue` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="4fa2d-283">`CookieRequestCultureProvider` `DefaultCookieName` は、ユーザーの優先するカルチャ情報を追跡するために使用される既定の Cookie 名を返します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="4fa2d-284">既定の Cookie 名は `.AspNetCore.Culture` です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="4fa2d-285">Cookie の形式は `c=%LANGCODE%|uic=%LANGCODE%` です。ここで、`c` は `Culture` であり、`uic` は `UICulture` です。たとえば、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="4fa2d-286">カルチャ情報と UI カルチャの 1 つのみを指定した場合、指定したカルチャが、カルチャ情報と UI カルチャの両方に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="4fa2d-287">Accept-Language HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="4fa2d-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="4fa2d-288">[Accept-language ヘッダー](https://www.w3.org/International/questions/qa-accept-lang-locales)は、ほとんどのブラウザーで設定可能であり、当初はユーザーの言語を指定するためのものでした。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="4fa2d-289">この設定は、ブラウザーが何を送信するように設定されているか、基になるオペレーティング システムから何を継承するかを示します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="4fa2d-290">ブラウザーの要求からの Accept Language HTTP ヘッダーは、ユーザーの優先言語を検出するための確実な方法ではありません (「[Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)」(ブラウザーの優先言語を設定する) を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="4fa2d-291">実稼働アプリには、ユーザーがカルチャの選択をカスタマイズする方法を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="4fa2d-292">IE で Accept-Language HTTP ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="4fa2d-293">歯車アイコンから、 **[インターネット オプション]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="4fa2d-294">**[言語]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-294">Tap **Languages**.</span></span>

    ![インターネット オプション](localization/_static/lang.png)

3. <span data-ttu-id="4fa2d-296">**[言語の優先順位の設定]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="4fa2d-297">**[言語の追加]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="4fa2d-298">言語を追加します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-298">Add the language.</span></span>

6. <span data-ttu-id="4fa2d-299">言語をタップして、 **[上へ移動]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-299">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="4fa2d-300">カスタム プロバイダーを使用する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-300">Use a custom provider</span></span>

<span data-ttu-id="4fa2d-301">お客様がデータベースに言語とカルチャを格納できるようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-301">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="4fa2d-302">ユーザーのためにこれらの値を検索するプロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-302">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="4fa2d-303">次のコードは、カスタム プロバイダーを追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-303">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="4fa2d-304">ローカリゼーション プロバイダーを追加または削除するには、`RequestLocalizationOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-304">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="4fa2d-305">プログラムでカルチャを設定する</span><span class="sxs-lookup"><span data-stu-id="4fa2d-305">Set the culture programmatically</span></span>

<span data-ttu-id="4fa2d-306">この [GitHub](https://github.com/aspnet/entropy) のサンプル **Localization.StarterWeb** プロジェクトには、`Culture` を設定するための UI が含まれています。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-306">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="4fa2d-307">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルを使用して、サポートされているカルチャの一覧からカルチャを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-307">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="4fa2d-308">*Views/Shared/_SelectLanguagePartial.cshtml* ファイルは、レイアウト ファイルの `footer` セクションに追加されるので、すべてのビューで使用できます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-308">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="4fa2d-309">`SetLanguage` メソッドはカルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-309">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="4fa2d-310">*_SelectLanguagePartial.cshtml* をこのプロジェクトのサンプル コードに接続することはできません。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-310">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="4fa2d-311">[GitHub](https://github.com/aspnet/entropy) の **Localization.StarterWeb** プロジェクトには、[依存関係の挿入](dependency-injection.md)コンテナーを介して Razor 部分に `RequestLocalizationOptions` を挿入するコードがあります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-311">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="4fa2d-312">グローバリゼーションとローカリゼーションの用語</span><span class="sxs-lookup"><span data-stu-id="4fa2d-312">Globalization and localization terms</span></span>

<span data-ttu-id="4fa2d-313">アプリをローカライズするプロセスでは、最新のソフトウェア開発でよく使用される関連する文字セットの基本的な理解と、それらに関連した問題の理解も必要です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-313">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="4fa2d-314">すべてのコンピューターは、テキストを数値 (コード) として格納しますが、さまざまなシステムが異なる数値を使用して同じテキストを格納します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-314">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="4fa2d-315">ローカリゼーション プロセスとは、特定のカルチャ/ロケール用にアプリのユーザー インターフェイス (UI) を変換することを指します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-315">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="4fa2d-316">[ローカライズ化](/dotnet/standard/globalization-localization/localizability-review)は、グローバル化されたアプリのローカリゼーションの準備ができていることを確認するための中間プロセスです。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-316">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="4fa2d-317">カルチャ名の [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 形式は `<languagecode2>-<country/regioncode2>` です。ここで、`<languagecode2>` は言語コードであり、`<country/regioncode2>` はサブカルチャ コードです。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-317">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="4fa2d-318">たとえば、スペイン語 (チリ) は `es-CL`、英語 (米国) は `en-US`、英語 (オーストラリア) は `en-AU` です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-318">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="4fa2d-319">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-319">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="4fa2d-320">「[Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)」(言語カルチャ名) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-320">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="4fa2d-321">多くの場合、国際化は "I18N" に省略されます。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-321">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="4fa2d-322">省略形は、最初と最後の文字およびそれらの間の文字の数になります。したがって、18 は、最初の "I" と最後の "N" の間の文字の数を表します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-322">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="4fa2d-323">同じことが、グローバリゼーション (G11N) とローカリゼーション (L10N) にも当てはまります。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-323">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="4fa2d-324">用語:</span><span class="sxs-lookup"><span data-stu-id="4fa2d-324">Terms:</span></span>

* <span data-ttu-id="4fa2d-325">グローバリゼーション (G11N):アプリが別の言語と地域をサポートするようにするプロセス。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-325">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="4fa2d-326">ローカリゼーション (L10N):特定の言語と地域向けにアプリをカスタマイズするプロセス。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-326">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="4fa2d-327">国際化 (I18N):グローバリゼーションとローカリゼーションの両方を示します。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-327">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="4fa2d-328">カルチャ:言語および必要に応じて地域です。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-328">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="4fa2d-329">ニュートラル カルチャ:指定した言語のみを含み、地域は含まないカルチャ。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-329">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="4fa2d-330">(例: "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-330">(for example "en", "es")</span></span>
* <span data-ttu-id="4fa2d-331">特定のカルチャ:指定した言語と地域を含むカルチャ。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-331">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="4fa2d-332">(例: "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-332">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="4fa2d-333">親カルチャ:特定のカルチャを含むニュートラル カルチャ。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-333">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="4fa2d-334">(たとえば、"en" は "en-US" および "en-GB" の親カルチャです)。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-334">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="4fa2d-335">ロケール:ロケールはカルチャと同じです。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-335">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="4fa2d-336">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4fa2d-336">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="4fa2d-337">記事で使用されている [Localization.StarterWeb プロジェクト](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="4fa2d-337">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="4fa2d-338">.NET アプリケーションのグローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="4fa2d-338">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="4fa2d-339">.resx ファイル内のリソース</span><span class="sxs-lookup"><span data-stu-id="4fa2d-339">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="4fa2d-340">Microsoft 多言語アプリ ツールキット</span><span class="sxs-lookup"><span data-stu-id="4fa2d-340">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="4fa2d-341">ローカリゼーションとジェネリック</span><span class="sxs-lookup"><span data-stu-id="4fa2d-341">Localization & Generics</span></span>](https://github.com/hishamco/hishambinateya.com/blob/master/Posts/localization-and-generics.md)
