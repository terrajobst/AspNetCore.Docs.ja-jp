---
title: ASP.NET Core のオプション パターン
author: guardrex
description: ASP.NET Core アプリの関連のある設定のグループを表すオプション パターンを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/18/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 4192bab8acef7c4f7bdf1ac481c468cd0a835420
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239791"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="13fb5-103">ASP.NET Core のオプション パターン</span><span class="sxs-lookup"><span data-stu-id="13fb5-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="13fb5-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="13fb5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="13fb5-105">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="13fb5-106">[構成設定](xref:fundamentals/configuration/index)がシナリオ別に個々のクラスに分離されるとき、アプリは次の 2 つの重要なソフトウェア エンジニアリング原則に従います。</span><span class="sxs-lookup"><span data-stu-id="13fb5-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="13fb5-107">[ISP (Interface Segregation Principle/インターフェイス分離の原則) すなわちカプセル化](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; それが使用する構成設定にのみ依存するシナリオ (クラス)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="13fb5-108">[懸念事項の分離 (Separation of Concerns)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; アプリのさまざまな部分の設定が互いに非依存。</span><span class="sxs-lookup"><span data-stu-id="13fb5-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="13fb5-109">構成データを検証するメカニズムもオプションによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="13fb5-110">詳しくは、「[オプションの検証](#options-validation)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="13fb5-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="13fb5-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="13fb5-112">Package</span><span class="sxs-lookup"><span data-stu-id="13fb5-112">Package</span></span>

<span data-ttu-id="13fb5-113">ASP.NET Core アプリでは、[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) パッケージが暗黙的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="13fb5-114">オプションのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="13fb5-114">Options interfaces</span></span>

<span data-ttu-id="13fb5-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を使用してオプションを取得し、`TOptions` インターフェイスのオプション通知を管理します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="13fb5-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> では次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="13fb5-117">変更通知</span><span class="sxs-lookup"><span data-stu-id="13fb5-117">Change notifications</span></span>
* [<span data-ttu-id="13fb5-118">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="13fb5-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="13fb5-119">再読み込み可能な構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="13fb5-120">選択的なオプションの無効化 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="13fb5-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="13fb5-121">[ポスト構成](#options-post-configuration)のシナリオでは、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で、オプションを設定または変更できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="13fb5-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> は、新しいオプション インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="13fb5-123"><xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> メソッドが 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="13fb5-124">既定の実装では、登録されている <xref:Microsoft.Extensions.Options.IConfigureOptions%601> と <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> がすべて受け取られ、先にすべての構成を実行し、その後、ポスト構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="13fb5-125"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> と <xref:Microsoft.Extensions.Options.IConfigureOptions%601> が区別され、適切なインターフェイスのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="13fb5-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって使用され、`TOptions` インスタンスをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="13fb5-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="13fb5-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は、値が再計算されるよう、モニターのオプション インスタンスを無効にします (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="13fb5-128"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> を利用し、手動で値を入力できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="13fb5-129"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> メソッドは、すべての名前付きインスタンスをオンデマンドで再作成するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="13fb5-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> は、要求ごとにオプションを再計算する必要があるシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="13fb5-131">詳しくは、「[IOptionsSnapshot で構成データを再読み込みする](#reload-configuration-data-with-ioptionssnapshot)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="13fb5-132"><xref:Microsoft.Extensions.Options.IOptions%601> はオプションをサポートするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="13fb5-133">ただし、<xref:Microsoft.Extensions.Options.IOptions%601> では、上記の <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> のシナリオはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="13fb5-134">既に <xref:Microsoft.Extensions.Options.IOptions%601> インターフェイスを使用しており、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって提供されるシナリオが必要ない既存のフレームワークとライブラリでは、<xref:Microsoft.Extensions.Options.IOptions%601> を継続して使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="13fb5-135">一般般的なオプションの構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-135">General options configuration</span></span>

<span data-ttu-id="13fb5-136">サンプル アプリの例 &num;1 は一般的なオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="13fb5-137">オプション クラスはパラメーターのないパブリック コンストラクターを持った非抽象でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="13fb5-138">次のクラス `MyOptions` には `Option1` と `Option2` という 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="13fb5-139">既定値の設定は任意ですが、次の例のクラス コンストラクターは既定値 `Option1` を設定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="13fb5-140">`Option2` には、プロパティを直接初期化することで既定値が設定されます (*Models/MyOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="13fb5-141">`MyOptions` クラスは <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービス コンテナーに追加され、構成にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="13fb5-142">次のページ モデルは[コンストラクターの依存関係挿入](xref:mvc/controllers/dependency-injection)と <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を利用し、設定にアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="13fb5-143">サンプルの *appsettings.json* ファイルは `option1` と `option2` の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="13fb5-144">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="13fb5-145">カスタム <xref:System.Configuration.ConfigurationBuilder> を使用して設定ファイルからオプションの構成を読み込むときには、基本パスが正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="13fb5-146"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して、設定ファイルからオプションの構成を読み込むときには、基本パスを明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="13fb5-147">デリゲートで単純なオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13fb5-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="13fb5-148">サンプル アプリの例 &num;2 では、デリゲートで単純なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="13fb5-149">デリゲートを使用し、オプション値を設定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-149">Use a delegate to set options values.</span></span> <span data-ttu-id="13fb5-150">サンプル アプリでは、`MyOptionsWithDelegateConfig` クラス (*Models/MyOptionsWithDelegateConfig.cs*) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="13fb5-151">次のコードでは、2 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="13fb5-152">デリゲートを利用し、`MyOptionsWithDelegateConfig` とのバインディングを構成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="13fb5-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="13fb5-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="13fb5-154">複数の構成プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="13fb5-155">構成プロバイダーは NuGet パッケージにあり、登録順に適用されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="13fb5-156">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="13fb5-157"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> を呼び出すたびに <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="13fb5-158">先の例では、値 `Option1` と `Option2` が両方とも *appsettings.json* で指定されていますが、構成されているデリゲートにより値 `Option1` と `Option2` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="13fb5-159">複数の構成サービスが有効になっているとき、最後に指定された構成ソースが*優先*され、それにより構成値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="13fb5-160">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="13fb5-161">サブオプション構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-161">Suboptions configuration</span></span>

<span data-ttu-id="13fb5-162">サンプル アプリの例 &num;3 はサブオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="13fb5-163">アプリでは、アプリの特定のシナリオ グループ (クラス) に関連するオプション クラスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="13fb5-164">構成値を必要とするアプリの各パーツには、そのパーツが使用する構成値へのアクセスのみを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="13fb5-165">オプションを構成にバインドするとき、オプション タイプの各プロパティはフォーム `property[:sub-property:]` の構成キーにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="13fb5-166">たとえば、`MyOptions.Option1` プロパティはキー `Option1` にバインドされます。このキーは *appsettings.json* の `option1` プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="13fb5-167">次のコードでは、3 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="13fb5-168">`MySubOptions` を *appsettings.json* ファイルのセクション `subsection` にバインドします。</span><span class="sxs-lookup"><span data-stu-id="13fb5-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="13fb5-169">`GetSection` メソッドでは、<xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-169">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="13fb5-170">サンプルの *appsettings.json* ファイルは、`suboption1` と `suboption2` のキーで `subsection` メンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-170">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="13fb5-171">`MySubOptions` クラスは、`SubOption1` プロパティと `SubOption2` プロパティを定義し、オプションの値を保持します (*Models/MySubOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-171">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="13fb5-172">ページ モデルの `OnGet` メソッドは、オプションの値を含む文字列を返します (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-172">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="13fb5-173">アプリの実行時、`OnGet` メソッドは文字列を返し、サブオプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-173">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="13fb5-174">オプションの挿入</span><span class="sxs-lookup"><span data-stu-id="13fb5-174">Options injection</span></span>

<span data-ttu-id="13fb5-175">オプションの挿入は、サンプル アプリの例 &num;4 に示されています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-175">Options injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="13fb5-176"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を次に挿入します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-176">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="13fb5-177">[@inject](xref:mvc/views/razor#inject) Razor ディレクティブを持つ Razor Pages または MVC ビュー。</span><span class="sxs-lookup"><span data-stu-id="13fb5-177">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="13fb5-178">ページまたはビュー モデル。</span><span class="sxs-lookup"><span data-stu-id="13fb5-178">A page or view model.</span></span>

<span data-ttu-id="13fb5-179">サンプル アプリの次の例では、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> をページ モデル (*Pages/Index.cshtml.cs*) に挿入しています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-179">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="13fb5-180">サンプル アプリでは、`@inject` ディレクティブを使用して `IOptionsMonitor<MyOptions>` を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-180">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="13fb5-181">アプリの実行時、レンダリングされたページにオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-181">When the app is run, the options values are shown in the rendered page:</span></span>

![オプション値の Option1: value1_from_json と Option2: -1 はモデルから読み込まれるか、ビューへの挿入によって読み込まれます。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="13fb5-183">IOptionsSnapshot で構成データを再読み込みする</span><span class="sxs-lookup"><span data-stu-id="13fb5-183">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="13fb5-184">サンプル アプリの例 &num;5 では、<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> で構成データを再読み込みする方法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-184">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="13fb5-185"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> を使用すると、オプションは要求の有効期間中にアクセスされ、キャッシュされたとき、要求につき 1 回計算されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-185">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="13fb5-186">`IOptionsMonitor` と `IOptionsSnapshot` の違いは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-186">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="13fb5-187">`IOptionsMonitor` は常に最新のオプション値を取得する[シングルトン サービス](xref:fundamentals/dependency-injection#singleton) です。これは、シングルトンの依存関係で特に便利です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-187">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="13fb5-188">`IOptionsSnapshot` は[スコープ サービス](xref:fundamentals/dependency-injection#scoped) であり、`IOptionsSnapshot<T>` オブジェクトの構築時にオプションのスナップショットを提供します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-188">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="13fb5-189">オプションのスナップショットは、一時的な依存関係およびスコープのある依存関係で使用されるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-189">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="13fb5-190">次の例では、*appsettings.json* の変更後、新しい <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> が作成されます (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-190">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="13fb5-191">サーバーに複数の要求が届くと、ファイルが変更され、構成が再読み込みされるまで、*appsettings.json* ファイルによって提供される定数値が返されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-191">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="13fb5-192">次のイメージでは、初期値の `option1` と `option2` が *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-192">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="13fb5-193">*appsettings.json* ファイルの値を `value1_from_json UPDATED` と `200` に変更します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-193">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="13fb5-194">*appsettings.json* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-194">Save the *appsettings.json* file.</span></span> <span data-ttu-id="13fb5-195">ブラウザーを更新し、オプション値が更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-195">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="13fb5-196">IConfigureNamedOptions による名前付きオプションのサポート</span><span class="sxs-lookup"><span data-stu-id="13fb5-196">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="13fb5-197">サンプル アプリの例 &num;6 は、<xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> による名前付きオプションのサポートです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-197">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="13fb5-198">*名前付きオプション*をサポートすることで、アプリでは名前付きオプション構成が区別されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-198">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="13fb5-199">サンプル アプリでは、名前付きオプションは [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) で宣言されます。これは、[ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-199">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="13fb5-200">サンプル アプリは、<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> を使用して名前付きオプションにアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="13fb5-201">サンプル アプリを実行すると、名前付きオプションが返されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="13fb5-202">`named_options_1` 値が構成から与えられます。これは *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="13fb5-203">`named_options_2` 値は次により提供されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="13fb5-204">`Option1` の `ConfigureServices` の `named_options_2` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="13fb5-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="13fb5-205">`MyOptions` クラスによって提供される `Option2` の既定値。</span><span class="sxs-lookup"><span data-stu-id="13fb5-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="13fb5-206">ConfigureAll メソッドを使用してすべてのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13fb5-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="13fb5-207">すべてのオプション インスタンスを <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> メソッドを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="13fb5-208">次のコードでは、すべての構成インスタンスの `Option1` が共通値で構成されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="13fb5-209">`Startup.ConfigureServices` メソッドに次のコードを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="13fb5-210">コードを追加した後、サンプル アプリを実行すると、次の結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="13fb5-211">すべてのオプションが名前付きインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-211">All options are named instances.</span></span> <span data-ttu-id="13fb5-212">既存の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> インスタンスは、`string.Empty` である、`Options.DefaultName` インスタンスを対象とするものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="13fb5-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> はまた、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="13fb5-214"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> の既定の実装には、それぞれを適切に使用するロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="13fb5-215">名前付きオプション `null` は、特定の名前付きオプションの代わりにすべての名前付きインスタンスを対象にするときに使用されます (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> と <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> ではこの規則が使用されます)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="13fb5-216">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="13fb5-216">OptionsBuilder API</span></span>

<span data-ttu-id="13fb5-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> は、`TOptions` インスタンスの構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="13fb5-218">`OptionsBuilder` は名前付きオプションの作成を簡略化します。これは最初の `AddOptions<TOptions>(string optionsName)` の呼び出しに対する 1 つのパラメーターにすぎず、後続のすべての呼び出しが表示されなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="13fb5-219">サービスの依存関係を受け入れるオプションの検証と `ConfigureOptions` のオーバーロードは、`OptionsBuilder` を介してのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="13fb5-220">DI サービスを使用してオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13fb5-220">Use DI services to configure options</span></span>

<span data-ttu-id="13fb5-221">オプションの構成中、2 とおりの方法で依存関係挿入から他のサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="13fb5-222">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) で [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="13fb5-223">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) から [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) のオーバーロードが与えられます。これにより、最大 5 つのサービスを使用し、オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-223">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="13fb5-224"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> または <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> を実装する独自の型を作成し、その型をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="13fb5-225">サービスの作成は複雑なため、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡す方法をおすすめします。</span><span class="sxs-lookup"><span data-stu-id="13fb5-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="13fb5-226">独自の型を作成することは、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) の使用時にフレームワークがユーザーの代わりに行うことと同じです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="13fb5-227">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すと、一時的な汎用の <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> が登録されます。これには、指定された汎用サービスの型を受け入れるコンストラクターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="13fb5-228">オプションの検証</span><span class="sxs-lookup"><span data-stu-id="13fb5-228">Options validation</span></span>

<span data-ttu-id="13fb5-229">オプションが構成されている場合は、オプションの検証を使用してオプションを検証することができます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="13fb5-230">オプションが有効なら `true` を、無効なら `false` を返す検証メソッドと共に、`Validate` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="13fb5-231">前の例では、名前付きのオプションのインスタンスを `optionalOptionsName` に設定しました。</span><span class="sxs-lookup"><span data-stu-id="13fb5-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="13fb5-232">既定のオプションのインスタンスは `Options.DefaultName` です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="13fb5-233">オプションのインスタンスが作成されると、検証が実行されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="13fb5-234">オプションのインスタンスが最初にアクセスされる際は、検証に合格することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-234">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="13fb5-235">オプションの検証は、最初に構成されて検証された後のオプションの変更を防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-235">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="13fb5-236">`Validate` メソッドは、`Func<TOptions, bool>` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-236">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="13fb5-237">検証を完全にカスタマイズするには、`IValidateOptions<TOptions>` を実装します。これにより、次が可能になります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-237">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="13fb5-238">複数のオプションの種類の検証: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="13fb5-238">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="13fb5-239">別のオプションの種類に基づく検証: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="13fb5-239">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="13fb5-240">`IValidateOptions` は次を検証します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-240">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="13fb5-241">特定の名前付きのオプションのインスタンス。</span><span class="sxs-lookup"><span data-stu-id="13fb5-241">A specific named options instance.</span></span>
* <span data-ttu-id="13fb5-242">`name` が `null` の場合はすべてのオプション。</span><span class="sxs-lookup"><span data-stu-id="13fb5-242">All options when `name` is `null`.</span></span>

<span data-ttu-id="13fb5-243">インターフェイスの実装から `ValidateOptionsResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-243">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="13fb5-244">データ注釈に基づく検証は、`OptionsBuilder<TOptions>` で <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> メソッドを呼び出すことにより、[Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) パッケージから利用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-244">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="13fb5-245">ASP.NET Core アプリでは、`Microsoft.Extensions.Options.DataAnnotations` が暗黙的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-245">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="13fb5-246">先行検証 (起動時にフェイル ファストする) は今後のリリースでの導入が検討されています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-246">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="13fb5-247">オプションのポスト構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-247">Options post-configuration</span></span>

<span data-ttu-id="13fb5-248">ポスト構成を <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-248">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="13fb5-249">ポスト構成は、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-249">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="13fb5-250"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> は、名前付きオプションのポスト構成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-250"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="13fb5-251">すべての構成インスタンスをポスト構成するには、<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-251">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="13fb5-252">スタートアップ時にオプションにアクセスする</span><span class="sxs-lookup"><span data-stu-id="13fb5-252">Accessing options during startup</span></span>

<span data-ttu-id="13fb5-253">サービスは `Configure` メソッドの実行前に構築されるため、<xref:Microsoft.Extensions.Options.IOptions%601> および <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は `Startup.Configure` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-253"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="13fb5-254">`Startup.ConfigureServices` では <xref:Microsoft.Extensions.Options.IOptions%601> または <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-254">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="13fb5-255">サービスの登録順序が原因で、オプションの状態が一貫しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-255">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="13fb5-256">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-256">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="13fb5-257">[構成設定](xref:fundamentals/configuration/index)がシナリオ別に個々のクラスに分離されるとき、アプリは次の 2 つの重要なソフトウェア エンジニアリング原則に従います。</span><span class="sxs-lookup"><span data-stu-id="13fb5-257">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="13fb5-258">[ISP (Interface Segregation Principle/インターフェイス分離の原則) すなわちカプセル化](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; それが使用する構成設定にのみ依存するシナリオ (クラス)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-258">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="13fb5-259">[懸念事項の分離 (Separation of Concerns)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; アプリのさまざまな部分の設定が互いに非依存。</span><span class="sxs-lookup"><span data-stu-id="13fb5-259">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="13fb5-260">構成データを検証するメカニズムもオプションによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-260">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="13fb5-261">詳しくは、「[オプションの検証](#options-validation)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-261">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="13fb5-262">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="13fb5-262">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="13fb5-263">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="13fb5-263">Prerequisites</span></span>

<span data-ttu-id="13fb5-264">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) を参照するか、[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-264">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="13fb5-265">オプションのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="13fb5-265">Options interfaces</span></span>

<span data-ttu-id="13fb5-266"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を使用してオプションを取得し、`TOptions` インターフェイスのオプション通知を管理します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-266"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="13fb5-267"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> では次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-267"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="13fb5-268">変更通知</span><span class="sxs-lookup"><span data-stu-id="13fb5-268">Change notifications</span></span>
* [<span data-ttu-id="13fb5-269">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="13fb5-269">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="13fb5-270">再読み込み可能な構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-270">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="13fb5-271">選択的なオプションの無効化 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="13fb5-271">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="13fb5-272">[ポスト構成](#options-post-configuration)のシナリオでは、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で、オプションを設定または変更できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-272">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="13fb5-273"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> は、新しいオプション インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-273"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="13fb5-274"><xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> メソッドが 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-274">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="13fb5-275">既定の実装では、登録されている <xref:Microsoft.Extensions.Options.IConfigureOptions%601> と <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> がすべて受け取られ、先にすべての構成を実行し、その後、ポスト構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-275">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="13fb5-276"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> と <xref:Microsoft.Extensions.Options.IConfigureOptions%601> が区別され、適切なインターフェイスのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-276">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="13fb5-277"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって使用され、`TOptions` インスタンスをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="13fb5-277"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="13fb5-278"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は、値が再計算されるよう、モニターのオプション インスタンスを無効にします (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-278">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="13fb5-279"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> を利用し、手動で値を入力できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-279">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="13fb5-280"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> メソッドは、すべての名前付きインスタンスをオンデマンドで再作成するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="13fb5-281"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> は、要求ごとにオプションを再計算する必要があるシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-281"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="13fb5-282">詳しくは、「[IOptionsSnapshot で構成データを再読み込みする](#reload-configuration-data-with-ioptionssnapshot)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-282">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="13fb5-283"><xref:Microsoft.Extensions.Options.IOptions%601> はオプションをサポートするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-283"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="13fb5-284">ただし、<xref:Microsoft.Extensions.Options.IOptions%601> では、上記の <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> のシナリオはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-284">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="13fb5-285">既に <xref:Microsoft.Extensions.Options.IOptions%601> インターフェイスを使用しており、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって提供されるシナリオが必要ない既存のフレームワークとライブラリでは、<xref:Microsoft.Extensions.Options.IOptions%601> を継続して使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-285">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="13fb5-286">一般般的なオプションの構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-286">General options configuration</span></span>

<span data-ttu-id="13fb5-287">サンプル アプリの例 &num;1 は一般的なオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-287">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="13fb5-288">オプション クラスはパラメーターのないパブリック コンストラクターを持った非抽象でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-288">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="13fb5-289">次のクラス `MyOptions` には `Option1` と `Option2` という 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-289">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="13fb5-290">既定値の設定は任意ですが、次の例のクラス コンストラクターは既定値 `Option1` を設定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-290">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="13fb5-291">`Option2` には、プロパティを直接初期化することで既定値が設定されます (*Models/MyOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-291">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="13fb5-292">`MyOptions` クラスは <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービス コンテナーに追加され、構成にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-292">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="13fb5-293">次のページ モデルは[コンストラクターの依存関係挿入](xref:mvc/controllers/dependency-injection)と <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を利用し、設定にアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-293">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="13fb5-294">サンプルの *appsettings.json* ファイルは `option1` と `option2` の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-294">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="13fb5-295">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-295">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="13fb5-296">カスタム <xref:System.Configuration.ConfigurationBuilder> を使用して設定ファイルからオプションの構成を読み込むときには、基本パスが正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-296">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="13fb5-297"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して、設定ファイルからオプションの構成を読み込むときには、基本パスを明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-297">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="13fb5-298">デリゲートで単純なオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13fb5-298">Configure simple options with a delegate</span></span>

<span data-ttu-id="13fb5-299">サンプル アプリの例 &num;2 では、デリゲートで単純なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-299">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="13fb5-300">デリゲートを使用し、オプション値を設定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-300">Use a delegate to set options values.</span></span> <span data-ttu-id="13fb5-301">サンプル アプリでは、`MyOptionsWithDelegateConfig` クラス (*Models/MyOptionsWithDelegateConfig.cs*) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-301">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="13fb5-302">次のコードでは、2 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-302">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="13fb5-303">デリゲートを利用し、`MyOptionsWithDelegateConfig` とのバインディングを構成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-303">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="13fb5-304">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="13fb5-304">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="13fb5-305">複数の構成プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-305">You can add multiple configuration providers.</span></span> <span data-ttu-id="13fb5-306">構成プロバイダーは NuGet パッケージにあり、登録順に適用されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-306">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="13fb5-307">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-307">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="13fb5-308"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> を呼び出すたびに <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-308">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="13fb5-309">先の例では、値 `Option1` と `Option2` が両方とも *appsettings.json* で指定されていますが、構成されているデリゲートにより値 `Option1` と `Option2` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-309">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="13fb5-310">複数の構成サービスが有効になっているとき、最後に指定された構成ソースが*優先*され、それにより構成値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-310">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="13fb5-311">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-311">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="13fb5-312">サブオプション構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-312">Suboptions configuration</span></span>

<span data-ttu-id="13fb5-313">サンプル アプリの例 &num;3 はサブオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-313">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="13fb5-314">アプリでは、アプリの特定のシナリオ グループ (クラス) に関連するオプション クラスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-314">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="13fb5-315">構成値を必要とするアプリの各パーツには、そのパーツが使用する構成値へのアクセスのみを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-315">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="13fb5-316">オプションを構成にバインドするとき、オプション タイプの各プロパティはフォーム `property[:sub-property:]` の構成キーにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-316">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="13fb5-317">たとえば、`MyOptions.Option1` プロパティはキー `Option1` にバインドされます。このキーは *appsettings.json* の `option1` プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-317">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="13fb5-318">次のコードでは、3 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-318">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="13fb5-319">`MySubOptions` を *appsettings.json* ファイルのセクション `subsection` にバインドします。</span><span class="sxs-lookup"><span data-stu-id="13fb5-319">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="13fb5-320">`GetSection` メソッドでは、<xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-320">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="13fb5-321">サンプルの *appsettings.json* ファイルは、`suboption1` と `suboption2` のキーで `subsection` メンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-321">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="13fb5-322">`MySubOptions` クラスは、`SubOption1` プロパティと `SubOption2` プロパティを定義し、オプションの値を保持します (*Models/MySubOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-322">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="13fb5-323">ページ モデルの `OnGet` メソッドは、オプションの値を含む文字列を返します (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-323">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="13fb5-324">アプリの実行時、`OnGet` メソッドは文字列を返し、サブオプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-324">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="13fb5-325">オプションの挿入</span><span class="sxs-lookup"><span data-stu-id="13fb5-325">Options injection</span></span>

<span data-ttu-id="13fb5-326">オプションの挿入は、サンプル アプリの例 &num;4 に示されています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-326">Options injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="13fb5-327"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を次に挿入します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-327">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="13fb5-328">[@inject](xref:mvc/views/razor#inject) Razor ディレクティブを持つ Razor Pages または MVC ビュー。</span><span class="sxs-lookup"><span data-stu-id="13fb5-328">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="13fb5-329">ページまたはビュー モデル。</span><span class="sxs-lookup"><span data-stu-id="13fb5-329">A page or view model.</span></span>

<span data-ttu-id="13fb5-330">サンプル アプリの次の例では、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> をページ モデル (*Pages/Index.cshtml.cs*) に挿入しています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-330">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="13fb5-331">サンプル アプリでは、`@inject` ディレクティブを使用して `IOptionsMonitor<MyOptions>` を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-331">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="13fb5-332">アプリの実行時、レンダリングされたページにオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-332">When the app is run, the options values are shown in the rendered page:</span></span>

![オプション値の Option1: value1_from_json と Option2: -1 はモデルから読み込まれるか、ビューへの挿入によって読み込まれます。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="13fb5-334">IOptionsSnapshot で構成データを再読み込みする</span><span class="sxs-lookup"><span data-stu-id="13fb5-334">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="13fb5-335">サンプル アプリの例 &num;5 では、<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> で構成データを再読み込みする方法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-335">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="13fb5-336"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> を使用すると、オプションは要求の有効期間中にアクセスされ、キャッシュされたとき、要求につき 1 回計算されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-336">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="13fb5-337">`IOptionsMonitor` と `IOptionsSnapshot` の違いは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-337">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="13fb5-338">`IOptionsMonitor` は常に最新のオプション値を取得する[シングルトン サービス](xref:fundamentals/dependency-injection#singleton) です。これは、シングルトンの依存関係で特に便利です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-338">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="13fb5-339">`IOptionsSnapshot` は[スコープ サービス](xref:fundamentals/dependency-injection#scoped) であり、`IOptionsSnapshot<T>` オブジェクトの構築時にオプションのスナップショットを提供します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-339">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="13fb5-340">オプションのスナップショットは、一時的な依存関係およびスコープのある依存関係で使用されるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-340">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="13fb5-341">次の例では、*appsettings.json* の変更後、新しい <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> が作成されます (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-341">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="13fb5-342">サーバーに複数の要求が届くと、ファイルが変更され、構成が再読み込みされるまで、*appsettings.json* ファイルによって提供される定数値が返されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-342">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="13fb5-343">次のイメージでは、初期値の `option1` と `option2` が *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-343">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="13fb5-344">*appsettings.json* ファイルの値を `value1_from_json UPDATED` と `200` に変更します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-344">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="13fb5-345">*appsettings.json* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-345">Save the *appsettings.json* file.</span></span> <span data-ttu-id="13fb5-346">ブラウザーを更新し、オプション値が更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-346">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="13fb5-347">IConfigureNamedOptions による名前付きオプションのサポート</span><span class="sxs-lookup"><span data-stu-id="13fb5-347">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="13fb5-348">サンプル アプリの例 &num;6 は、<xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> による名前付きオプションのサポートです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-348">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="13fb5-349">*名前付きオプション*をサポートすることで、アプリでは名前付きオプション構成が区別されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-349">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="13fb5-350">サンプル アプリでは、名前付きオプションは [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) で宣言されます。これは、[ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-350">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="13fb5-351">サンプル アプリは、<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> を使用して名前付きオプションにアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-351">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="13fb5-352">サンプル アプリを実行すると、名前付きオプションが返されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-352">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="13fb5-353">`named_options_1` 値が構成から与えられます。これは *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-353">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="13fb5-354">`named_options_2` 値は次により提供されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-354">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="13fb5-355">`Option1` の `ConfigureServices` の `named_options_2` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="13fb5-355">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="13fb5-356">`MyOptions` クラスによって提供される `Option2` の既定値。</span><span class="sxs-lookup"><span data-stu-id="13fb5-356">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="13fb5-357">ConfigureAll メソッドを使用してすべてのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13fb5-357">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="13fb5-358">すべてのオプション インスタンスを <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> メソッドを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-358">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="13fb5-359">次のコードでは、すべての構成インスタンスの `Option1` が共通値で構成されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-359">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="13fb5-360">`Startup.ConfigureServices` メソッドに次のコードを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-360">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="13fb5-361">コードを追加した後、サンプル アプリを実行すると、次の結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-361">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="13fb5-362">すべてのオプションが名前付きインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-362">All options are named instances.</span></span> <span data-ttu-id="13fb5-363">既存の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> インスタンスは、`string.Empty` である、`Options.DefaultName` インスタンスを対象とするものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-363">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="13fb5-364"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> はまた、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-364"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="13fb5-365"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> の既定の実装には、それぞれを適切に使用するロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-365">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="13fb5-366">名前付きオプション `null` は、特定の名前付きオプションの代わりにすべての名前付きインスタンスを対象にするときに使用されます (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> と <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> ではこの規則が使用されます)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-366">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="13fb5-367">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="13fb5-367">OptionsBuilder API</span></span>

<span data-ttu-id="13fb5-368"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> は、`TOptions` インスタンスの構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-368"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="13fb5-369">`OptionsBuilder` は名前付きオプションの作成を簡略化します。これは最初の `AddOptions<TOptions>(string optionsName)` の呼び出しに対する 1 つのパラメーターにすぎず、後続のすべての呼び出しが表示されなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-369">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="13fb5-370">サービスの依存関係を受け入れるオプションの検証と `ConfigureOptions` のオーバーロードは、`OptionsBuilder` を介してのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-370">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="13fb5-371">DI サービスを使用してオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13fb5-371">Use DI services to configure options</span></span>

<span data-ttu-id="13fb5-372">オプションの構成中、2 とおりの方法で依存関係挿入から他のサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-372">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="13fb5-373">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) で [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-373">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="13fb5-374">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) から [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) のオーバーロードが与えられます。これにより、最大 5 つのサービスを使用し、オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-374">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="13fb5-375"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> または <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> を実装する独自の型を作成し、その型をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-375">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="13fb5-376">サービスの作成は複雑なため、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡す方法をおすすめします。</span><span class="sxs-lookup"><span data-stu-id="13fb5-376">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="13fb5-377">独自の型を作成することは、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) の使用時にフレームワークがユーザーの代わりに行うことと同じです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-377">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="13fb5-378">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すと、一時的な汎用の <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> が登録されます。これには、指定された汎用サービスの型を受け入れるコンストラクターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-378">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="13fb5-379">オプションの検証</span><span class="sxs-lookup"><span data-stu-id="13fb5-379">Options validation</span></span>

<span data-ttu-id="13fb5-380">オプションが構成されている場合は、オプションの検証を使用してオプションを検証することができます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-380">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="13fb5-381">オプションが有効なら `true` を、無効なら `false` を返す検証メソッドと共に、`Validate` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-381">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="13fb5-382">前の例では、名前付きのオプションのインスタンスを `optionalOptionsName` に設定しました。</span><span class="sxs-lookup"><span data-stu-id="13fb5-382">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="13fb5-383">既定のオプションのインスタンスは `Options.DefaultName` です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-383">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="13fb5-384">オプションのインスタンスが作成されると、検証が実行されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-384">Validation runs when the options instance is created.</span></span> <span data-ttu-id="13fb5-385">オプションのインスタンスが最初にアクセスされる際は、検証に合格することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-385">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="13fb5-386">オプションの検証は、最初に構成されて検証された後のオプションの変更を防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-386">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="13fb5-387">`Validate` メソッドは、`Func<TOptions, bool>` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-387">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="13fb5-388">検証を完全にカスタマイズするには、`IValidateOptions<TOptions>` を実装します。これにより、次が可能になります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-388">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="13fb5-389">複数のオプションの種類の検証: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="13fb5-389">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="13fb5-390">別のオプションの種類に基づく検証: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="13fb5-390">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="13fb5-391">`IValidateOptions` は次を検証します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-391">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="13fb5-392">特定の名前付きのオプションのインスタンス。</span><span class="sxs-lookup"><span data-stu-id="13fb5-392">A specific named options instance.</span></span>
* <span data-ttu-id="13fb5-393">`name` が `null` の場合はすべてのオプション。</span><span class="sxs-lookup"><span data-stu-id="13fb5-393">All options when `name` is `null`.</span></span>

<span data-ttu-id="13fb5-394">インターフェイスの実装から `ValidateOptionsResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-394">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="13fb5-395">データ注釈に基づく検証は、`OptionsBuilder<TOptions>` で <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> メソッドを呼び出すことにより、[Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) パッケージから利用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-395">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="13fb5-396">`Microsoft.Extensions.Options.DataAnnotations` は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-396">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="13fb5-397">先行検証 (起動時にフェイル ファストする) は今後のリリースでの導入が検討されています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-397">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="13fb5-398">オプションのポスト構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-398">Options post-configuration</span></span>

<span data-ttu-id="13fb5-399">ポスト構成を <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-399">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="13fb5-400">ポスト構成は、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-400">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="13fb5-401"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> は、名前付きオプションのポスト構成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-401"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="13fb5-402">すべての構成インスタンスをポスト構成するには、<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-402">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="13fb5-403">スタートアップ時にオプションにアクセスする</span><span class="sxs-lookup"><span data-stu-id="13fb5-403">Accessing options during startup</span></span>

<span data-ttu-id="13fb5-404">サービスは `Configure` メソッドの実行前に構築されるため、<xref:Microsoft.Extensions.Options.IOptions%601> および <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は `Startup.Configure` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-404"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="13fb5-405">`Startup.ConfigureServices` では <xref:Microsoft.Extensions.Options.IOptions%601> または <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-405">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="13fb5-406">サービスの登録順序が原因で、オプションの状態が一貫しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-406">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="13fb5-407">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-407">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="13fb5-408">[構成設定](xref:fundamentals/configuration/index)がシナリオ別に個々のクラスに分離されるとき、アプリは次の 2 つの重要なソフトウェア エンジニアリング原則に従います。</span><span class="sxs-lookup"><span data-stu-id="13fb5-408">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="13fb5-409">[ISP (Interface Segregation Principle/インターフェイス分離の原則) すなわちカプセル化](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; それが使用する構成設定にのみ依存するシナリオ (クラス)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-409">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="13fb5-410">[懸念事項の分離 (Separation of Concerns)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; アプリのさまざまな部分の設定が互いに非依存。</span><span class="sxs-lookup"><span data-stu-id="13fb5-410">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="13fb5-411">構成データを検証するメカニズムもオプションによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-411">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="13fb5-412">詳しくは、「[オプションの検証](#options-validation)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-412">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="13fb5-413">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="13fb5-413">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="13fb5-414">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="13fb5-414">Prerequisites</span></span>

<span data-ttu-id="13fb5-415">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) を参照するか、[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-415">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="13fb5-416">オプションのインターフェイス</span><span class="sxs-lookup"><span data-stu-id="13fb5-416">Options interfaces</span></span>

<span data-ttu-id="13fb5-417"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を使用してオプションを取得し、`TOptions` インターフェイスのオプション通知を管理します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-417"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="13fb5-418"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> では次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-418"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="13fb5-419">変更通知</span><span class="sxs-lookup"><span data-stu-id="13fb5-419">Change notifications</span></span>
* [<span data-ttu-id="13fb5-420">名前付きオプション</span><span class="sxs-lookup"><span data-stu-id="13fb5-420">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="13fb5-421">再読み込み可能な構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-421">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="13fb5-422">選択的なオプションの無効化 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="13fb5-422">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="13fb5-423">[ポスト構成](#options-post-configuration)のシナリオでは、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で、オプションを設定または変更できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-423">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="13fb5-424"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> は、新しいオプション インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-424"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="13fb5-425"><xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> メソッドが 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-425">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="13fb5-426">既定の実装では、登録されている <xref:Microsoft.Extensions.Options.IConfigureOptions%601> と <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> がすべて受け取られ、先にすべての構成を実行し、その後、ポスト構成を実行します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-426">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="13fb5-427"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> と <xref:Microsoft.Extensions.Options.IConfigureOptions%601> が区別され、適切なインターフェイスのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-427">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="13fb5-428"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって使用され、`TOptions` インスタンスをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="13fb5-428"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="13fb5-429"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> は、値が再計算されるよう、モニターのオプション インスタンスを無効にします (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-429">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="13fb5-430"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> を利用し、手動で値を入力できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-430">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="13fb5-431"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> メソッドは、すべての名前付きインスタンスをオンデマンドで再作成するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-431">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="13fb5-432"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> は、要求ごとにオプションを再計算する必要があるシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-432"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="13fb5-433">詳しくは、「[IOptionsSnapshot で構成データを再読み込みする](#reload-configuration-data-with-ioptionssnapshot)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-433">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="13fb5-434"><xref:Microsoft.Extensions.Options.IOptions%601> はオプションをサポートするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-434"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="13fb5-435">ただし、<xref:Microsoft.Extensions.Options.IOptions%601> では、上記の <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> のシナリオはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-435">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="13fb5-436">既に <xref:Microsoft.Extensions.Options.IOptions%601> インターフェイスを使用しており、<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> によって提供されるシナリオが必要ない既存のフレームワークとライブラリでは、<xref:Microsoft.Extensions.Options.IOptions%601> を継続して使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-436">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="13fb5-437">一般般的なオプションの構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-437">General options configuration</span></span>

<span data-ttu-id="13fb5-438">サンプル アプリの例 &num;1 は一般的なオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-438">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="13fb5-439">オプション クラスはパラメーターのないパブリック コンストラクターを持った非抽象でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-439">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="13fb5-440">次のクラス `MyOptions` には `Option1` と `Option2` という 2 つのプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-440">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="13fb5-441">既定値の設定は任意ですが、次の例のクラス コンストラクターは既定値 `Option1` を設定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-441">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="13fb5-442">`Option2` には、プロパティを直接初期化することで既定値が設定されます (*Models/MyOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-442">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="13fb5-443">`MyOptions` クラスは <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービス コンテナーに追加され、構成にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-443">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="13fb5-444">次のページ モデルは[コンストラクターの依存関係挿入](xref:mvc/controllers/dependency-injection)と <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を利用し、設定にアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-444">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="13fb5-445">サンプルの *appsettings.json* ファイルは `option1` と `option2` の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-445">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="13fb5-446">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-446">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="13fb5-447">カスタム <xref:System.Configuration.ConfigurationBuilder> を使用して設定ファイルからオプションの構成を読み込むときには、基本パスが正しく設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-447">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="13fb5-448"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して、設定ファイルからオプションの構成を読み込むときには、基本パスを明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="13fb5-448">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="13fb5-449">デリゲートで単純なオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13fb5-449">Configure simple options with a delegate</span></span>

<span data-ttu-id="13fb5-450">サンプル アプリの例 &num;2 では、デリゲートで単純なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-450">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="13fb5-451">デリゲートを使用し、オプション値を設定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-451">Use a delegate to set options values.</span></span> <span data-ttu-id="13fb5-452">サンプル アプリでは、`MyOptionsWithDelegateConfig` クラス (*Models/MyOptionsWithDelegateConfig.cs*) を使用しています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-452">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="13fb5-453">次のコードでは、2 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-453">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="13fb5-454">デリゲートを利用し、`MyOptionsWithDelegateConfig` とのバインディングを構成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-454">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="13fb5-455">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="13fb5-455">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="13fb5-456">複数の構成プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-456">You can add multiple configuration providers.</span></span> <span data-ttu-id="13fb5-457">構成プロバイダーは NuGet パッケージにあり、登録順に適用されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-457">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="13fb5-458">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-458">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="13fb5-459"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> を呼び出すたびに <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-459">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="13fb5-460">先の例では、値 `Option1` と `Option2` が両方とも *appsettings.json* で指定されていますが、構成されているデリゲートにより値 `Option1` と `Option2` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-460">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="13fb5-461">複数の構成サービスが有効になっているとき、最後に指定された構成ソースが*優先*され、それにより構成値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-461">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="13fb5-462">アプリの実行時、ページ モデルの `OnGet` メソッドは文字列を返し、オプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-462">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="13fb5-463">サブオプション構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-463">Suboptions configuration</span></span>

<span data-ttu-id="13fb5-464">サンプル アプリの例 &num;3 はサブオプション構成です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-464">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="13fb5-465">アプリでは、アプリの特定のシナリオ グループ (クラス) に関連するオプション クラスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-465">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="13fb5-466">構成値を必要とするアプリの各パーツには、そのパーツが使用する構成値へのアクセスのみを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-466">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="13fb5-467">オプションを構成にバインドするとき、オプション タイプの各プロパティはフォーム `property[:sub-property:]` の構成キーにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-467">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="13fb5-468">たとえば、`MyOptions.Option1` プロパティはキー `Option1` にバインドされます。このキーは *appsettings.json* の `option1` プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-468">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="13fb5-469">次のコードでは、3 番目の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> サービスがサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-469">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="13fb5-470">`MySubOptions` を *appsettings.json* ファイルのセクション `subsection` にバインドします。</span><span class="sxs-lookup"><span data-stu-id="13fb5-470">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="13fb5-471">`GetSection` メソッドでは、<xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 名前空間が必要です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-471">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="13fb5-472">サンプルの *appsettings.json* ファイルは、`suboption1` と `suboption2` のキーで `subsection` メンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-472">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="13fb5-473">`MySubOptions` クラスは、`SubOption1` プロパティと `SubOption2` プロパティを定義し、オプションの値を保持します (*Models/MySubOptions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-473">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="13fb5-474">ページ モデルの `OnGet` メソッドは、オプションの値を含む文字列を返します (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-474">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="13fb5-475">アプリの実行時、`OnGet` メソッドは文字列を返し、サブオプション クラス値を表示します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-475">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="13fb5-476">ビュー モデルまたは直接的なビュー挿入で与えられるオプション</span><span class="sxs-lookup"><span data-stu-id="13fb5-476">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="13fb5-477">サンプル アプリの例 &num;4 は、ビュー モデルまたは直接的なビュー挿入で与えられるオプションです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-477">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="13fb5-478">オプションはビュー モデルで提供するか、ビューに <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> を直接挿入することで提供できます (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-478">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="13fb5-479">サンプル アプリでは、`@inject` ディレクティブを使用して `IOptionsMonitor<MyOptions>` を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-479">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="13fb5-480">アプリの実行時、レンダリングされたページにオプションの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-480">When the app is run, the options values are shown in the rendered page:</span></span>

![オプション値の Option1: value1_from_json と Option2: -1 はモデルから読み込まれるか、ビューへの挿入によって読み込まれます。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="13fb5-482">IOptionsSnapshot で構成データを再読み込みする</span><span class="sxs-lookup"><span data-stu-id="13fb5-482">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="13fb5-483">サンプル アプリの例 &num;5 では、<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> で構成データを再読み込みする方法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-483">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="13fb5-484"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> では、最小の処理オーバーヘッドでオプションを再読み込みできます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-484"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="13fb5-485">オプションは、要求の有効期間中にアクセスされ、キャッシュされたとき、要求につき 1 回計算されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-485">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="13fb5-486">次の例では、*appsettings.json* の変更後、新しい <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> が作成されます (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-486">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="13fb5-487">サーバーに複数の要求が届くと、ファイルが変更され、構成が再読み込みされるまで、*appsettings.json* ファイルによって提供される定数値が返されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-487">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="13fb5-488">次のイメージでは、初期値の `option1` と `option2` が *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-488">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="13fb5-489">*appsettings.json* ファイルの値を `value1_from_json UPDATED` と `200` に変更します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-489">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="13fb5-490">*appsettings.json* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-490">Save the *appsettings.json* file.</span></span> <span data-ttu-id="13fb5-491">ブラウザーを更新し、オプション値が更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-491">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="13fb5-492">IConfigureNamedOptions による名前付きオプションのサポート</span><span class="sxs-lookup"><span data-stu-id="13fb5-492">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="13fb5-493">サンプル アプリの例 &num;6 は、<xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> による名前付きオプションのサポートです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-493">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="13fb5-494">*名前付きオプション*をサポートすることで、アプリでは名前付きオプション構成が区別されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-494">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="13fb5-495">サンプル アプリでは、名前付きオプションは [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) で宣言されます。これは、[ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-495">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="13fb5-496">サンプル アプリは、<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> を使用して名前付きオプションにアクセスします (*Pages/Index.cshtml.cs*)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-496">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="13fb5-497">サンプル アプリを実行すると、名前付きオプションが返されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-497">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="13fb5-498">`named_options_1` 値が構成から与えられます。これは *appsettings.json* ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-498">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="13fb5-499">`named_options_2` 値は次により提供されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-499">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="13fb5-500">`Option1` の `ConfigureServices` の `named_options_2` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="13fb5-500">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="13fb5-501">`MyOptions` クラスによって提供される `Option2` の既定値。</span><span class="sxs-lookup"><span data-stu-id="13fb5-501">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="13fb5-502">ConfigureAll メソッドを使用してすべてのオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13fb5-502">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="13fb5-503">すべてのオプション インスタンスを <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> メソッドを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-503">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="13fb5-504">次のコードでは、すべての構成インスタンスの `Option1` が共通値で構成されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-504">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="13fb5-505">`Startup.ConfigureServices` メソッドに次のコードを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-505">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="13fb5-506">コードを追加した後、サンプル アプリを実行すると、次の結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-506">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="13fb5-507">すべてのオプションが名前付きインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-507">All options are named instances.</span></span> <span data-ttu-id="13fb5-508">既存の <xref:Microsoft.Extensions.Options.IConfigureOptions%601> インスタンスは、`string.Empty` である、`Options.DefaultName` インスタンスを対象とするものとして処理されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-508">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="13fb5-509"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> はまた、<xref:Microsoft.Extensions.Options.IConfigureOptions%601> を実装します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-509"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="13fb5-510"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> の既定の実装には、それぞれを適切に使用するロジックがあります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-510">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="13fb5-511">名前付きオプション `null` は、特定の名前付きオプションの代わりにすべての名前付きインスタンスを対象にするときに使用されます (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> と <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> ではこの規則が使用されます)。</span><span class="sxs-lookup"><span data-stu-id="13fb5-511">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="13fb5-512">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="13fb5-512">OptionsBuilder API</span></span>

<span data-ttu-id="13fb5-513"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> は、`TOptions` インスタンスの構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-513"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="13fb5-514">`OptionsBuilder` は名前付きオプションの作成を簡略化します。これは最初の `AddOptions<TOptions>(string optionsName)` の呼び出しに対する 1 つのパラメーターにすぎず、後続のすべての呼び出しが表示されなくなるためです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-514">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="13fb5-515">サービスの依存関係を受け入れるオプションの検証と `ConfigureOptions` のオーバーロードは、`OptionsBuilder` を介してのみ可能です。</span><span class="sxs-lookup"><span data-stu-id="13fb5-515">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="13fb5-516">DI サービスを使用してオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13fb5-516">Use DI services to configure options</span></span>

<span data-ttu-id="13fb5-517">オプションの構成中、2 とおりの方法で依存関係挿入から他のサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-517">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="13fb5-518">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) で [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-518">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="13fb5-519">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) から [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) のオーバーロードが与えられます。これにより、最大 5 つのサービスを使用し、オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-519">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="13fb5-520"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> または <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> を実装する独自の型を作成し、その型をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-520">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="13fb5-521">サービスの作成は複雑なため、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) に構成デリゲートを渡す方法をおすすめします。</span><span class="sxs-lookup"><span data-stu-id="13fb5-521">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="13fb5-522">独自の型を作成することは、[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) の使用時にフレームワークがユーザーの代わりに行うことと同じです。</span><span class="sxs-lookup"><span data-stu-id="13fb5-522">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="13fb5-523">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) を呼び出すと、一時的な汎用の <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> が登録されます。これには、指定された汎用サービスの型を受け入れるコンストラクターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="13fb5-523">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="13fb5-524">オプションのポスト構成</span><span class="sxs-lookup"><span data-stu-id="13fb5-524">Options post-configuration</span></span>

<span data-ttu-id="13fb5-525">ポスト構成を <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-525">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="13fb5-526">ポスト構成は、すべての <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 構成が行われた後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-526">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="13fb5-527"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> は、名前付きオプションのポスト構成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-527"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="13fb5-528">すべての構成インスタンスをポスト構成するには、<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="13fb5-528">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="13fb5-529">スタートアップ時にオプションにアクセスする</span><span class="sxs-lookup"><span data-stu-id="13fb5-529">Accessing options during startup</span></span>

<span data-ttu-id="13fb5-530">サービスは `Configure` メソッドの実行前に構築されるため、<xref:Microsoft.Extensions.Options.IOptions%601> および <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は `Startup.Configure` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="13fb5-530"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="13fb5-531">`Startup.ConfigureServices` では <xref:Microsoft.Extensions.Options.IOptions%601> または <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="13fb5-531">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="13fb5-532">サービスの登録順序が原因で、オプションの状態が一貫しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13fb5-532">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="13fb5-533">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="13fb5-533">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
