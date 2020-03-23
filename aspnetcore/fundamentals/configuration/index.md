---
title: ASP.NET Core の構成
author: rick-anderson
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: e1237db2625a127bfa5c31ac29b4394be6941b2f
ms.sourcegitcommit: 9e2b3aaccc9a41291eb23bf4561159e79cf6bc9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2020
ms.locfileid: "79546342"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="5dd74-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="5dd74-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="5dd74-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="5dd74-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5dd74-105">ASP.NET Core の構成は、1つまたは複数の[構成プロバイダー](#cp)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="5dd74-106">構成プロバイダーは、以下のようなさまざまな構成ソースを使用して、キーと値のペアから構成データを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="5dd74-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="5dd74-107">*appsettings.json* などの設定ファイル</span><span class="sxs-lookup"><span data-stu-id="5dd74-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="5dd74-108">環境変数</span><span class="sxs-lookup"><span data-stu-id="5dd74-108">Environment variables</span></span>
* <span data-ttu-id="5dd74-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5dd74-109">Azure Key Vault</span></span>
* <span data-ttu-id="5dd74-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5dd74-110">Azure App Configuration</span></span>
* <span data-ttu-id="5dd74-111">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="5dd74-111">Command-line arguments</span></span>
* <span data-ttu-id="5dd74-112">インストール済みまたは作成済みのカスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="5dd74-113">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="5dd74-113">Directory files</span></span>
* <span data-ttu-id="5dd74-114">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="5dd74-114">In-memory .NET objects</span></span>

<span data-ttu-id="5dd74-115">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5dd74-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="5dd74-116">既定の構成</span><span class="sxs-lookup"><span data-stu-id="5dd74-116">Default configuration</span></span>

<span data-ttu-id="5dd74-117">[dotnet new](/dotnet/core/tools/dotnet-new) または Visual Studio で作成された ASP.NET Core の web アプリが、次のコードを生成します:</span><span class="sxs-lookup"><span data-stu-id="5dd74-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="5dd74-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="5dd74-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) は:既存の `IConfiguration` をソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="5dd74-120">既定の構成では、[ホスト](#hvac)構成を追加し、_アプリ_構成の最初のソースとして設定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="5dd74-121">[JSON 構成プロバイダー](#file-configuration-provider)を使用する [appsettings.json](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="5dd74-122">[JSON 構成プロバイダー](#file-configuration-provider)を使用する *appsettings.* `Environment`*json*。</span><span class="sxs-lookup"><span data-stu-id="5dd74-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="5dd74-123">たとえば、*appsettings*.***Production***.*json* および  *appsettings*.***Development***.*json*。</span><span class="sxs-lookup"><span data-stu-id="5dd74-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="5dd74-124">`Development` 環境でアプリが実行される際の [App シークレット](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="5dd74-125">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="5dd74-126">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-126">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="5dd74-127">後から追加される構成プロバイダーは、それ以前のキー設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="5dd74-128">たとえば、`MyKey` が *appsettings.json* と環境の両方で設定されている場合、環境の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="5dd74-129">既定の構成プロバイダーを使用すると、[コマンドライン構成プロバイダー](#command-line-configuration-provider) が他のすべてのプロバイダーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="5dd74-130">`CreateDefaultBuilder` の詳細については、[既定のビルダー設定](xref:fundamentals/host/generic-host#default-builder-settings)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="5dd74-131">以下のコードでは、追加した順に、有効な構成プロバイダーが表示されます:</span><span class="sxs-lookup"><span data-stu-id="5dd74-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="5dd74-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="5dd74-132">appsettings.json</span></span>

<span data-ttu-id="5dd74-133">以下の *appsettings.json* ファイルについて考えます:</span><span class="sxs-lookup"><span data-stu-id="5dd74-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="5dd74-134">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="5dd74-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-135">既定の <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> は、以下の順序で構成を読み込みます:</span><span class="sxs-lookup"><span data-stu-id="5dd74-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="5dd74-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="5dd74-136">*appsettings.json*</span></span>
1. <span data-ttu-id="5dd74-137">*appsettings.* `Environment` *.json*:たとえば、*appsettings*.***Production***.*json* および *appsettings*.***Development***.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="5dd74-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="5dd74-138">ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="5dd74-139">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5dd74-140">*appsettings*.`Environment`.*json* の値は、*appsettings. json*のキーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="5dd74-141">たとえば、既定では次のようになります:</span><span class="sxs-lookup"><span data-stu-id="5dd74-141">For example, by default:</span></span>

* <span data-ttu-id="5dd74-142">開発においては、*appsettings*.***Development***.*json* 構成が *appsettings.json* の値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="5dd74-143">運用環境では、*appsettings*.***Production***.*json* 構成が *appsettings. json*の値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="5dd74-144">たとえば、Azure にアプリをデプロイする場合。</span><span class="sxs-lookup"><span data-stu-id="5dd74-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="5dd74-145">オプションパターンを使用して、階層型の構成データをバインドします</span><span class="sxs-lookup"><span data-stu-id="5dd74-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="5dd74-146">関連する構成値を読み取る方法としては、[オプション パターン](xref:fundamentals/configuration/options)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="5dd74-147">たとえば、以下の構成値を読み取るには、次のようにします:</span><span class="sxs-lookup"><span data-stu-id="5dd74-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="5dd74-148">次の `PositionOptions` クラスを作成します:</span><span class="sxs-lookup"><span data-stu-id="5dd74-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="5dd74-149">型のパブリックな読み取り/書き込みプロパティは、すべてバインドされます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="5dd74-150">フィールドはバインド***されません***。</span><span class="sxs-lookup"><span data-stu-id="5dd74-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="5dd74-151">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-151">The following code:</span></span>

* <span data-ttu-id="5dd74-152">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) を呼び出して、`PositionOptions` クラスを `Position` セクションにバインドします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="5dd74-153">`Position` 構成データを表示します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="5dd74-155">`ConfigurationBinder.Get<T>` は `ConfigurationBinder.Bind` を使用するよりも便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="5dd74-156">次のコードは、`PositionOptions` クラスで `ConfigurationBinder.Get<T>` を使用する方法を示しています:</span><span class="sxs-lookup"><span data-stu-id="5dd74-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-157">***オプション パターン*** を使用する際の別の方法として、`Position` セクションをバインドし、[依存関係挿入サービスコンテナー](xref:fundamentals/dependency-injection)に追加する方法があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5dd74-158">次のコードでは、`PositionOptions` は <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> でサービスコンテナーに追加され、構成にバインドされます:</span><span class="sxs-lookup"><span data-stu-id="5dd74-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="5dd74-159">下記のコードは、上記のコードを使用して位置オプションを読み取ります:</span><span class="sxs-lookup"><span data-stu-id="5dd74-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-160">[既定の](#default)構成を利用する場合、[reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) で *appsettings.json* と *appsettings.* `Environment` *.json* ファイルを有効化できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="5dd74-161">アプリの***開始後***に *appsettings.json* と *appsettings.* `Environment` *.json* ファイルに加えられた変更は、[JSON 構成プロバイダー](#jcp)が読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="5dd74-162">追加の JSON 構成ファイルを追加する方法の詳細については、このドキュメント中の「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="5dd74-163">セキュリティとシークレット マネージャー</span><span class="sxs-lookup"><span data-stu-id="5dd74-163">Security and secret manager</span></span>

<span data-ttu-id="5dd74-164">構成データのガイドライン:</span><span class="sxs-lookup"><span data-stu-id="5dd74-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="5dd74-165">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="5dd74-166">[シークレット マネージャー](xref:security/app-secrets) を使用すると、開発時にシークレットを格納できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="5dd74-167">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="5dd74-168">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="5dd74-169">[既定](#default)では、[シークレット マネージャー](xref:security/app-secrets)は*appsettings.json* と *appsettings.* `Environment` *.json* の後に構成設定を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="5dd74-170">パスワードその他の機密データの格納については、次を参照してください：</span><span class="sxs-lookup"><span data-stu-id="5dd74-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="5dd74-171"><xref:security/app-secrets>:ここには、環境変数を使用して機密データを格納する際のアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="5dd74-172">シークレット マネージャーでは、[ファイル構成プロバイダー](#fcp)を使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="5dd74-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="5dd74-174">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="5dd74-175">環境変数</span><span class="sxs-lookup"><span data-stu-id="5dd74-175">Environment variables</span></span>

<span data-ttu-id="5dd74-176">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> は *appsettings.json*、*appsettings.* `Environment` *.json*、および [シークレット マネージャー](xref:security/app-secrets)の読み取り後に、環境変数のキーと値のペアから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="5dd74-177">そのため、環境から読み取られたキー値は、*appsettings.json*、*appsettings.* `Environment` *.json*、シークレット マネージャーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5dd74-178">次の `set` コマンドは：</span><span class="sxs-lookup"><span data-stu-id="5dd74-178">The following `set` commands:</span></span>

* <span data-ttu-id="5dd74-179">Windows で[ 上記の例 ](#appsettingsjson)の環境キーと値を設定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="5dd74-180">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)を使用する際に、設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="5dd74-181">`dotnet run` コマンドは、プロジェクト ディレクトリで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-181">The `dotnet run` command must be run in the project directory.</span></span>

```cmd
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="5dd74-182">上記の環境設定は：</span><span class="sxs-lookup"><span data-stu-id="5dd74-182">The preceding environment settings:</span></span>

* <span data-ttu-id="5dd74-183">コマンド ウィンドウから起動されたプロセスでのみ設定可能です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="5dd74-184">Visual Studio で起動されたブラウザーでは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="5dd74-185">次の [setx](/windows-server/administration/windows-commands/setx) コマンドで、Windows 上の環境キーと値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="5dd74-186">`set` とは異なり、`setx` 設定は保持されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="5dd74-187">`/M` は、システム環境で変数を設定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="5dd74-188">`/M` スイッチが使用されていない場合には、ユーザー環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="5dd74-189">上記のコマンドが *apsettings.json* と *appsettings.* `Environment` *.json* をオーバーライドすることをテストするには:</span><span class="sxs-lookup"><span data-stu-id="5dd74-189">To test that the preceding commands override *apsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="5dd74-190">Visual Studio の場合:Visual Studio を終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="5dd74-191">CLI の場合:新しいコマンド ウィンドウを起動し、`dotnet run` を入力します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="5dd74-192">環境変数のプレフィックスを指定する文字列を指定して <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="5dd74-193">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-193">In the preceding code:</span></span>

* <span data-ttu-id="5dd74-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` は[既定の構成プロバイダー](#default)の後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="5dd74-195">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="5dd74-196">`MyCustomPrefix_` プレフィックスを使用して設定された環境変数は、[既定の構成プロバイダー](#default)をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="5dd74-197">これには、プレフィックスのない環境変数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="5dd74-198">構成のキーと値のペアの読み取り時に、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="5dd74-199">次のコマンドは、カスタム プレフィックスをテストします：</span><span class="sxs-lookup"><span data-stu-id="5dd74-199">The following commands test the custom prefix:</span></span>

```cmd
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="5dd74-200">[既定の構成](#default)では、`DOTNET_` と `ASPNETCORE_` のプレフィックスが付いた環境変数とコマンド ライン引数を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="5dd74-201">`DOTNET_` と `ASPNETCORE_` のプレフィックスは ASP.NET Core によって[ホストとアプリの構成](xref:fundamentals/host/generic-host#host-configuration)に使用されますが、ユーザーの構成には使用されません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="5dd74-202">ホストとアプリの構成の詳細については、「[.NET 汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="5dd74-203">[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの**新しいアプリケーション設定**を選択します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="5dd74-204">Azure App Service アプリケーションの設定は：</span><span class="sxs-lookup"><span data-stu-id="5dd74-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="5dd74-205">保存時に暗号化され、暗号化されたチャネルで送信されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="5dd74-206">環境変数として公開されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-206">Exposed as environment variables.</span></span>

<span data-ttu-id="5dd74-207">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="5dd74-208">Azure データベース接続文字列の詳細については、「[接続文字列のプレフィックス](#constr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="5dd74-209">コマンド ライン</span><span class="sxs-lookup"><span data-stu-id="5dd74-209">Command-line</span></span>

<span data-ttu-id="5dd74-210">[既定](#default)の構成を使用して、<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> は、以下の構成ソースの後にコマンド ライン引数のキーと値のペアから構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="5dd74-211">*appsettings.json* と *appsettings*.`Environment`.*json* ファイル。</span><span class="sxs-lookup"><span data-stu-id="5dd74-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="5dd74-212">開発環境の [App シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5dd74-213">環境変数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-213">Environment variables.</span></span>

<span data-ttu-id="5dd74-214">[既定](#default)では、コマンド ラインで設定された構成値は、他のすべての構成プロバイダーで設定された構成値をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="5dd74-215">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="5dd74-215">Command-line arguments</span></span>

<span data-ttu-id="5dd74-216">次のコマンドは `=` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="5dd74-217">次のコマンドは `/` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="5dd74-218">次のコマンドは `--` を使用してキーと値を設定します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="5dd74-219">キーの値:</span><span class="sxs-lookup"><span data-stu-id="5dd74-219">The key value:</span></span>

* <span data-ttu-id="5dd74-220">`=` の後に続ける必要があります。または、値がスペースの後にある場合は、キーのプレフィックスが `--` または `/` である必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="5dd74-221">`=` を使用する場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="5dd74-222">たとえば、`MySetting=` のようにします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="5dd74-223">同じコマンド内では、`=` を使用するコマンド ライン引数のキーと値のペアを、スペースを使用するキーと値のペアと混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="5dd74-224">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="5dd74-224">Switch mappings</span></span>

<span data-ttu-id="5dd74-225">スイッチ マッピングでは、**キー**名の置換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="5dd74-226"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換するディクショナリを提供します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="5dd74-227">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="5dd74-228">ディクショナリ中にコマンド ライン キーが見つかった場合は、そのディクショナリの値が返され、キーと値のペアがアプリの構成に設定されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="5dd74-229">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="5dd74-230">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="5dd74-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="5dd74-231">スイッチは `-` または `--`で始める必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="5dd74-232">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="5dd74-233">スイッチ マッピング ディクショナリを使用するには、それを `AddCommandLine` の呼び出しに渡します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="5dd74-234">次のコードは、置換されたキーのキー値を示しています：</span><span class="sxs-lookup"><span data-stu-id="5dd74-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-235">次のコマンドを実行して、キーの置換をテストします：</span><span class="sxs-lookup"><span data-stu-id="5dd74-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="5dd74-236">メモ:現時点では、`=` を使用してシングルダッシュの `-` でキーの置換値を設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="5dd74-237">[こちらの GitHub のイシュー](https://github.com/dotnet/extensions/issues/3059)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="5dd74-238">次のコマンドを実行して、キーの置換をテストできます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="5dd74-239">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="5dd74-240">`CreateDefaultBuilder` メソッドの `AddCommandLine` 呼び出しには、マップされたスイッチが含まれていないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5dd74-241">解決策は、`CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることです。</span><span class="sxs-lookup"><span data-stu-id="5dd74-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="5dd74-242">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="5dd74-242">Hierarchical configuration data</span></span>

<span data-ttu-id="5dd74-243">構成 API は、構成キーの区切り記号を使用して階層データをフラット化することにより、階層型の構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-243">The Configuration API is reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="5dd74-244">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *appsettings.json* 　ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="5dd74-245">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-246">階層型の構成データを読み取る方法としては、オプション パターンを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="5dd74-247">詳細については、このドキュメント中の「[階層型の構成データをバインドする](#optpat)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="5dd74-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="5dd74-249">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="5dd74-250">構成キーと値</span><span class="sxs-lookup"><span data-stu-id="5dd74-250">Configuration keys and values</span></span>

<span data-ttu-id="5dd74-251">構成キー:</span><span class="sxs-lookup"><span data-stu-id="5dd74-251">Configuration keys:</span></span>

* <span data-ttu-id="5dd74-252">構成キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-252">Are case-insensitive.</span></span> <span data-ttu-id="5dd74-253">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="5dd74-254">キーと値が複数の構成プロバイダーで設定されている場合は、最後に追加されたプロバイダーの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="5dd74-255">詳細については、「[既定の構成](#default)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="5dd74-256">階層キー</span><span class="sxs-lookup"><span data-stu-id="5dd74-256">Hierarchical keys</span></span>
  * <span data-ttu-id="5dd74-257">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="5dd74-258">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="5dd74-259">ダブル アンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロン `:` に自動で変換されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="5dd74-260">Azure Key Vault では、階層型のキーは区切り記号に `--` を使用します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="5dd74-261">アプリの構成にシークレットを読み込む際には、`--` を `:` に置換して記述します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-261">Write code to replace the `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="5dd74-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5dd74-263">配列のバインドについては、「[配列をクラスにバインドする](#boa)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="5dd74-264">構成値:</span><span class="sxs-lookup"><span data-stu-id="5dd74-264">Configuration values:</span></span>

* <span data-ttu-id="5dd74-265">構成値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-265">Are strings.</span></span>
* <span data-ttu-id="5dd74-266">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="5dd74-267">構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-267">Configuration providers</span></span>

<span data-ttu-id="5dd74-268">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="5dd74-269">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-269">Provider</span></span> | <span data-ttu-id="5dd74-270">以下から構成を提供します</span><span class="sxs-lookup"><span data-stu-id="5dd74-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="5dd74-271">Azure Key Vault 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="5dd74-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5dd74-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="5dd74-273">Azure App Configuration プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="5dd74-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5dd74-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="5dd74-275">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="5dd74-276">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="5dd74-276">Command-line parameters</span></span> |
| [<span data-ttu-id="5dd74-277">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="5dd74-278">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="5dd74-278">Custom source</span></span> |
| [<span data-ttu-id="5dd74-279">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="5dd74-280">環境変数</span><span class="sxs-lookup"><span data-stu-id="5dd74-280">Environment variables</span></span> |
| [<span data-ttu-id="5dd74-281">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="5dd74-282">INI、JSON、および XML ファイル</span><span class="sxs-lookup"><span data-stu-id="5dd74-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="5dd74-283">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="5dd74-284">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="5dd74-284">Directory files</span></span> |
| [<span data-ttu-id="5dd74-285">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="5dd74-286">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="5dd74-286">In-memory collections</span></span> |
| [<span data-ttu-id="5dd74-287">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="5dd74-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="5dd74-288">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="5dd74-288">File in the user profile directory</span></span> |

<span data-ttu-id="5dd74-289">構成ソースは、構成プロバイダーで指定された順序で読み取られます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="5dd74-290">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="5dd74-291">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5dd74-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="5dd74-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="5dd74-292">*appsettings.json*</span></span>
1. <span data-ttu-id="5dd74-293">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="5dd74-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="5dd74-294">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="5dd74-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="5dd74-295">[環境変数構成プロバイダー](#evcp)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="5dd74-296">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="5dd74-297">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするには、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="5dd74-298">上記の一連のプロバイダーは、[既定の構成](#default)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="5dd74-299">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="5dd74-299">Connection string prefixes</span></span>

<span data-ttu-id="5dd74-300">構成 API には、4つの接続文字列環境変数に対する特別なプロセスルールがあります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="5dd74-301">これらの接続文字列は、アプリ環境用の Azure 接続文字列の構成に含まれています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="5dd74-302">表に示されたプレフィックスを持つ環境変数は、[既定の構成](#default) を使用するとき、または `AddEnvironmentVariables` にプレフィックスが指定されていない場合に、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="5dd74-303">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="5dd74-303">Connection string prefix</span></span> | <span data-ttu-id="5dd74-304">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="5dd74-305">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="5dd74-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="5dd74-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="5dd74-307">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="5dd74-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="5dd74-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="5dd74-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="5dd74-309">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="5dd74-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="5dd74-310">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="5dd74-311">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="5dd74-312">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="5dd74-312">Environment variable key</span></span> | <span data-ttu-id="5dd74-313">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="5dd74-313">Converted configuration key</span></span> | <span data-ttu-id="5dd74-314">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="5dd74-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5dd74-315">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5dd74-316">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5dd74-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5dd74-317">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="5dd74-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5dd74-318">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5dd74-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5dd74-319">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5dd74-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5dd74-320">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5dd74-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5dd74-321">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5dd74-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="5dd74-322">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-322">JSON configuration provider</span></span>

<span data-ttu-id="5dd74-323"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、JSON ファイルのキーと値のペアから構成が読み込みまれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="5dd74-324">オーバーロードでは、次の指定ができます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-324">Overloads can specify:</span></span>

* <span data-ttu-id="5dd74-325">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="5dd74-325">Whether the file is optional.</span></span>
* <span data-ttu-id="5dd74-326">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="5dd74-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="5dd74-327">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="5dd74-328">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-328">The preceding code:</span></span>

* <span data-ttu-id="5dd74-329">次のオプションを使用して、*MyConfig.json* ファイルを読み込むように JSON 構成プロバイダーを構成します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="5dd74-330">`optional: true`:ファイルは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="5dd74-331">`reloadOnChange: true` は、次のとおりです。変更が保存されると、ファイルが再読み込みされます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="5dd74-332">*MyConfig.json* ファイルの前に[既定の構成プロバイダー](#default)を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="5dd74-333">[環境変数構成プロバイダー](#evcp) および [コマンド ライン構成プロバイダー](#clcp)を含む、既定の構成プロバイダーでの *MyConfig.json* ファイルのオーバーライドの設定。</span><span class="sxs-lookup"><span data-stu-id="5dd74-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5dd74-334">通常は、[環境変数構成プロバイダー](#evcp)および[コマンドライン構成プロバイダー](#clcp)で設定されている値をオーバーライドするカスタム JSON ファイルは***必要ありません***。</span><span class="sxs-lookup"><span data-stu-id="5dd74-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5dd74-335">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="5dd74-336">上記のコードでは、*MyConfig.json* と *MyConfig*.`Environment`.*json* ファイルの設定は：</span><span class="sxs-lookup"><span data-stu-id="5dd74-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="5dd74-337">*appsettings.json* と *appsettings*.`Environment`.*json* ファイルの設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="5dd74-338">[環境変数の構成プロバイダー](#evcp)と[コマンドライン構成プロバイダー](#clcp)の設定によってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5dd74-339">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)には、次の *MyConfig.json* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="5dd74-340">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="5dd74-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="5dd74-341">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-341">File configuration provider</span></span>

<span data-ttu-id="5dd74-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="5dd74-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="5dd74-343">以下の構成プロバイダーは `FileConfigurationProvider` から派生したものです：</span><span class="sxs-lookup"><span data-stu-id="5dd74-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="5dd74-344">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="5dd74-345">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="5dd74-346">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="5dd74-347">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-347">INI configuration provider</span></span>

<span data-ttu-id="5dd74-348"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="5dd74-349">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="5dd74-350">上記のコードでは、*MyIniConfig.ini* と *MyIniConfig*.`Environment`.*ini* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="5dd74-351">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="5dd74-352">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5dd74-353">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyIniConfig* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="5dd74-354">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="5dd74-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="5dd74-355">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-355">XML configuration provider</span></span>

<span data-ttu-id="5dd74-356"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="5dd74-357">次のコードは、すべての構成プロバイダーをクリアし、いくつかの構成プロバイダーを追加します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="5dd74-358">上記のコードでは、*MyXMLFile.xml* と *MyXMLFile*.`Environment`.*xml* ファイルの設定は、以下の設定によってオーバーライドされます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="5dd74-359">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="5dd74-360">[コマンドライン構成プロバイダー](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5dd74-361">[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) には、次の *MyXMLFile.xml* ファイルが含まれます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="5dd74-362">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の次のコードでは、上記の構成設定のいくつかが表示されます:</span><span class="sxs-lookup"><span data-stu-id="5dd74-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-363">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="5dd74-364">以下のコードでは、前の構成ファイルを読み取って、キーと値を表示します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-365">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="5dd74-366">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5dd74-367">key:attribute</span><span class="sxs-lookup"><span data-stu-id="5dd74-367">key:attribute</span></span>
* <span data-ttu-id="5dd74-368">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="5dd74-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="5dd74-369">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="5dd74-370"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="5dd74-371">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-371">The key is the file name.</span></span> <span data-ttu-id="5dd74-372">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-372">The value contains the file's contents.</span></span> <span data-ttu-id="5dd74-373">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="5dd74-374">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="5dd74-375">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="5dd74-376">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="5dd74-377">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="5dd74-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="5dd74-378">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="5dd74-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="5dd74-379">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="5dd74-380">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="5dd74-381">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="5dd74-382">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-382">Memory configuration provider</span></span>

<span data-ttu-id="5dd74-383"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="5dd74-384">次のコードでは、構成システムにメモリコ レクションが追加されています：</span><span class="sxs-lookup"><span data-stu-id="5dd74-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="5dd74-385">[サンプルのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) の以下のコードでは、上記の構成設定のいくつかが表示されます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-386">上記のコードでは、[既定の構成プロバイダー](#default)の後に `config.AddInMemoryCollection(Dict)` が追加されています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="5dd74-387">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="5dd74-388">構成プロバイダーの順序付けの例については、「[JSON 構成プロバイダー](#jcp)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="5dd74-389">`MemoryConfigurationProvider` を使用した別の例については、[配列をバインド](#boa)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="5dd74-390">GetValue</span><span class="sxs-lookup"><span data-stu-id="5dd74-390">GetValue</span></span>

<span data-ttu-id="5dd74-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを使用して、構成から単一の値を抽出し、それを指定した型に変換します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-392">上記のコードでは、`NumberKey` が構成中に見つからない場合には `99` の既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-392">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="5dd74-393">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="5dd74-393">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="5dd74-394">以下の例では、次の *MySubsection.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-394">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="5dd74-395">以下のコードでは、*MySubsection セクション*を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-395">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="5dd74-396">GetSection</span><span class="sxs-lookup"><span data-stu-id="5dd74-396">GetSection</span></span>

<span data-ttu-id="5dd74-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) は、指定されたサブセクションのキーを持つ構成サブセクションを返します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="5dd74-398">次のコードは、`section1` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-398">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-399">次のコードは、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-399">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-400">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-400">`GetSection` never returns `null`.</span></span> <span data-ttu-id="5dd74-401">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-401">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="5dd74-402">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-402">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="5dd74-403"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="5dd74-404">GetChildren と Exists</span><span class="sxs-lookup"><span data-stu-id="5dd74-404">GetChildren and Exists</span></span>

<span data-ttu-id="5dd74-405">次のコードは、[IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) を呼び出し、`section2:subsection0` の値を返します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-405">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-406">上記のコードは、[ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を呼び出し、セクションが存在することを確認します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-406">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="5dd74-407">配列をバインドする</span><span class="sxs-lookup"><span data-stu-id="5dd74-407">Bind an array</span></span>

<span data-ttu-id="5dd74-408">[ConfigurationBinder. Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) は、構成キーの配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-408">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5dd74-409">数値のキー セグメントを公開する配列形式は、すべて [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-409">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="5dd74-410">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)の *MyArray.json* について考えます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-410">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="5dd74-411">次のコードでは、*MyArray.json* を構成プロバイダーに追加します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-411">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="5dd74-412">次のコードでは、構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-412">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-413">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="5dd74-414">上記の出力では、インデックス3の値が `value40` になります。これは *MyArray. json* の `"4": "value40",` に対応しています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-414">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="5dd74-415">このバインドされた配列インデックスは連続的であり、構成キーインデックスにバインドされていません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-415">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="5dd74-416">構成バインダーは、バインドされたオブジェクトに null 値をバインドしたり、null エントリを作成したりはできません</span><span class="sxs-lookup"><span data-stu-id="5dd74-416">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="5dd74-417">次のコードでは、<xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドで `array:entries` 構成を読み込みます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-417">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="5dd74-418">次のコードでは、`arrayDict` `Dictionary` の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-418">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-419">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-419">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="5dd74-420">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-420">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="5dd74-421">配列を含む構成データがバインドされている場合、構成キーの配列インデックスは、オブジェクト作成時の構成データの反復のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-421">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="5dd74-422">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-422">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="5dd74-423">インデックス&num;3 に不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、インデックス&num;3のキーと値のペアを読み取る構成プロバイダーによってサプライできます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-423">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="5dd74-424">サンプルダウンロードの、次の *Value3.json* ファイルについて考えます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-424">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="5dd74-425">次のコードには、*Value3.json* と `arrayDict` `Dictionary` の構成が含まれています：</span><span class="sxs-lookup"><span data-stu-id="5dd74-425">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="5dd74-426">次のコードは、上記の構成を読み取り、値を表示します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-426">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="5dd74-427">上記のコードは、次の出力を返します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-427">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="5dd74-428">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-428">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="5dd74-429">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-429">Custom configuration provider</span></span>

<span data-ttu-id="5dd74-430">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-430">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="5dd74-431">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5dd74-431">The provider has the following characteristics:</span></span>

* <span data-ttu-id="5dd74-432">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-432">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="5dd74-433">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-433">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="5dd74-434">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-434">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="5dd74-435">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-435">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="5dd74-436">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-436">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="5dd74-437">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-437">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="5dd74-438">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-438">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5dd74-439">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-439">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5dd74-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5dd74-441"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-441">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5dd74-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5dd74-443"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-443">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5dd74-444">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-444">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="5dd74-445">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-445">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="5dd74-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5dd74-447">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-447">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5dd74-448">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-448">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5dd74-449">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-449">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="5dd74-450">起動時の構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="5dd74-450">Access configuration in Startup</span></span>

<span data-ttu-id="5dd74-451">次のコードでは `Startup` メソッドの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-451">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="5dd74-452">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-452">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="5dd74-453">Razor Pages の構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="5dd74-453">Access configuration in Razor Pages</span></span>

<span data-ttu-id="5dd74-454">次のコードでは、Razor ページの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-454">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="5dd74-455">MVC ビューファイルの構成へのアクセス</span><span class="sxs-lookup"><span data-stu-id="5dd74-455">Access configuration in a MVC view file</span></span>

<span data-ttu-id="5dd74-456">次のコードでは、MVC ビューの構成データが表示されます：</span><span class="sxs-lookup"><span data-stu-id="5dd74-456">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="5dd74-457">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="5dd74-457">Host versus app configuration</span></span>

<span data-ttu-id="5dd74-458">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="5dd74-459">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5dd74-460">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="5dd74-461">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="5dd74-462">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="5dd74-463">既定のホスト構成</span><span class="sxs-lookup"><span data-stu-id="5dd74-463">Default host configuration</span></span>

<span data-ttu-id="5dd74-464">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="5dd74-465">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5dd74-466">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5dd74-467">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5dd74-468">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5dd74-469">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="5dd74-470">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="5dd74-471">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="5dd74-472">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="5dd74-473">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="5dd74-474">その他の構成</span><span class="sxs-lookup"><span data-stu-id="5dd74-474">Other configuration</span></span>

<span data-ttu-id="5dd74-475">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="5dd74-476">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="5dd74-477">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="5dd74-478"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="5dd74-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="5dd74-479">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="5dd74-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="5dd74-480">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="5dd74-481">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="5dd74-482">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="5dd74-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="5dd74-483"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="5dd74-484">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5dd74-485">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5dd74-485">Additional resources</span></span>

* [<span data-ttu-id="5dd74-486">構成のソースコード</span><span class="sxs-lookup"><span data-stu-id="5dd74-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5dd74-487">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="5dd74-488">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="5dd74-489">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5dd74-489">Azure Key Vault</span></span>
* <span data-ttu-id="5dd74-490">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5dd74-490">Azure App Configuration</span></span>
* <span data-ttu-id="5dd74-491">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="5dd74-491">Command-line arguments</span></span>
* <span data-ttu-id="5dd74-492">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="5dd74-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="5dd74-493">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="5dd74-493">Directory files</span></span>
* <span data-ttu-id="5dd74-494">環境変数</span><span class="sxs-lookup"><span data-stu-id="5dd74-494">Environment variables</span></span>
* <span data-ttu-id="5dd74-495">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="5dd74-495">In-memory .NET objects</span></span>
* <span data-ttu-id="5dd74-496">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="5dd74-496">Settings files</span></span>

<span data-ttu-id="5dd74-497">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5dd74-498">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="5dd74-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="5dd74-499">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="5dd74-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="5dd74-500">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="5dd74-501">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5dd74-502">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5dd74-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="5dd74-503">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="5dd74-503">Host versus app configuration</span></span>

<span data-ttu-id="5dd74-504">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="5dd74-505">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5dd74-506">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="5dd74-507">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="5dd74-508">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="5dd74-509">その他の構成</span><span class="sxs-lookup"><span data-stu-id="5dd74-509">Other configuration</span></span>

<span data-ttu-id="5dd74-510">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="5dd74-511">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="5dd74-512">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="5dd74-513"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="5dd74-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="5dd74-514">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="5dd74-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="5dd74-515">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="5dd74-516">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="5dd74-517">既定の構成</span><span class="sxs-lookup"><span data-stu-id="5dd74-517">Default configuration</span></span>

<span data-ttu-id="5dd74-518">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="5dd74-519">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="5dd74-520">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="5dd74-521">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="5dd74-522">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5dd74-523">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5dd74-524">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5dd74-525">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5dd74-526">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="5dd74-527">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="5dd74-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5dd74-528">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="5dd74-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5dd74-529">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="5dd74-530">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="5dd74-531">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="5dd74-532">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="5dd74-532">Security</span></span>

<span data-ttu-id="5dd74-533">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="5dd74-534">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="5dd74-535">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="5dd74-536">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="5dd74-537">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="5dd74-538"><xref:security/app-secrets> &ndash; 環境変数を使用して機密データを格納する場合に関するアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-538"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="5dd74-539">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="5dd74-540">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="5dd74-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="5dd74-542">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="5dd74-543">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="5dd74-543">Hierarchical configuration data</span></span>

<span data-ttu-id="5dd74-544">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="5dd74-545">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="5dd74-546">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="5dd74-547">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="5dd74-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-548">section0:key0</span></span>
* <span data-ttu-id="5dd74-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-549">section0:key1</span></span>
* <span data-ttu-id="5dd74-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-550">section1:key0</span></span>
* <span data-ttu-id="5dd74-551">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-551">section1:key1</span></span>

<span data-ttu-id="5dd74-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="5dd74-553">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="5dd74-554">規約</span><span class="sxs-lookup"><span data-stu-id="5dd74-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="5dd74-555">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-555">Sources and providers</span></span>

<span data-ttu-id="5dd74-556">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="5dd74-557">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="5dd74-558">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="5dd74-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5dd74-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="5dd74-561">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="5dd74-562">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="5dd74-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="5dd74-563">キー</span><span class="sxs-lookup"><span data-stu-id="5dd74-563">Keys</span></span>

<span data-ttu-id="5dd74-564">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="5dd74-565">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-565">Keys are case-insensitive.</span></span> <span data-ttu-id="5dd74-566">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="5dd74-567">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="5dd74-568">階層キー</span><span class="sxs-lookup"><span data-stu-id="5dd74-568">Hierarchical keys</span></span>
  * <span data-ttu-id="5dd74-569">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-569">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="5dd74-570">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-570">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="5dd74-571">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-571">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="5dd74-572">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-572">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5dd74-573">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-573">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="5dd74-574"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-574">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5dd74-575">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-575">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="5dd74-576">値</span><span class="sxs-lookup"><span data-stu-id="5dd74-576">Values</span></span>

<span data-ttu-id="5dd74-577">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-577">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="5dd74-578">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-578">Values are strings.</span></span>
* <span data-ttu-id="5dd74-579">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-579">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="5dd74-580">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-580">Providers</span></span>

<span data-ttu-id="5dd74-581">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-581">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="5dd74-582">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-582">Provider</span></span> | <span data-ttu-id="5dd74-583">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-583">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="5dd74-584">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="5dd74-584">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="5dd74-585">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5dd74-585">Azure Key Vault</span></span> |
| <span data-ttu-id="5dd74-586">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="5dd74-586">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="5dd74-587">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5dd74-587">Azure App Configuration</span></span> |
| [<span data-ttu-id="5dd74-588">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-588">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="5dd74-589">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="5dd74-589">Command-line parameters</span></span> |
| [<span data-ttu-id="5dd74-590">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-590">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="5dd74-591">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="5dd74-591">Custom source</span></span> |
| [<span data-ttu-id="5dd74-592">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-592">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="5dd74-593">環境変数</span><span class="sxs-lookup"><span data-stu-id="5dd74-593">Environment variables</span></span> |
| [<span data-ttu-id="5dd74-594">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-594">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="5dd74-595">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="5dd74-595">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="5dd74-596">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-596">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="5dd74-597">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="5dd74-597">Directory files</span></span> |
| [<span data-ttu-id="5dd74-598">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-598">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="5dd74-599">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="5dd74-599">In-memory collections</span></span> |
| <span data-ttu-id="5dd74-600">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="5dd74-600">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="5dd74-601">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="5dd74-601">File in the user profile directory</span></span> |

<span data-ttu-id="5dd74-602">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-602">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="5dd74-603">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-603">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="5dd74-604">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-604">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="5dd74-605">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5dd74-605">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="5dd74-606">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="5dd74-606">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="5dd74-607">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5dd74-607">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="5dd74-608">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="5dd74-608">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="5dd74-609">環境変数</span><span class="sxs-lookup"><span data-stu-id="5dd74-609">Environment variables</span></span>
1. <span data-ttu-id="5dd74-610">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="5dd74-610">Command-line arguments</span></span>

<span data-ttu-id="5dd74-611">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-611">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="5dd74-612">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-612">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5dd74-613">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-613">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="5dd74-614">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="5dd74-614">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="5dd74-615">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-615">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="5dd74-616">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5dd74-616">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5dd74-617">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-617">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="5dd74-618">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="5dd74-618">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="5dd74-619">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-619">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="5dd74-620">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="5dd74-620">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="5dd74-621">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-621">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="5dd74-622">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="5dd74-622">Consume configuration during app startup</span></span>

<span data-ttu-id="5dd74-623">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-623">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5dd74-624">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-624">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="5dd74-625">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-625">Command-line Configuration Provider</span></span>

<span data-ttu-id="5dd74-626"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-626">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="5dd74-627">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-627">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5dd74-628">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-628">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="5dd74-629">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-629">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5dd74-630">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-630">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5dd74-631">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="5dd74-631">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5dd74-632">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-632">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5dd74-633">環境変数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-633">Environment variables.</span></span>

<span data-ttu-id="5dd74-634">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-634">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="5dd74-635">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-635">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="5dd74-636">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-636">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="5dd74-637">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-637">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="5dd74-638">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-638">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5dd74-639">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-639">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="5dd74-640">**例**</span><span class="sxs-lookup"><span data-stu-id="5dd74-640">**Example**</span></span>

<span data-ttu-id="5dd74-641">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-641">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="5dd74-642">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-642">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="5dd74-643">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-643">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="5dd74-644">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-644">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5dd74-645">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-645">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="5dd74-646">引数</span><span class="sxs-lookup"><span data-stu-id="5dd74-646">Arguments</span></span>

<span data-ttu-id="5dd74-647">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-647">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="5dd74-648">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-648">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="5dd74-649">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="5dd74-649">Key prefix</span></span>               | <span data-ttu-id="5dd74-650">例</span><span class="sxs-lookup"><span data-stu-id="5dd74-650">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="5dd74-651">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="5dd74-651">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="5dd74-652">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="5dd74-652">Two dashes (`--`)</span></span>        | <span data-ttu-id="5dd74-653">`--CommandLineKey2=value2`、`--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="5dd74-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="5dd74-654">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="5dd74-654">Forward slash (`/`)</span></span>      | <span data-ttu-id="5dd74-655">`/CommandLineKey3=value3`、`/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="5dd74-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="5dd74-656">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-656">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="5dd74-657">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="5dd74-657">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="5dd74-658">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="5dd74-658">Switch mappings</span></span>

<span data-ttu-id="5dd74-659">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-659">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="5dd74-660"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-660">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="5dd74-661">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-661">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="5dd74-662">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-662">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="5dd74-663">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-663">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="5dd74-664">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="5dd74-664">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="5dd74-665">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-665">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="5dd74-666">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-666">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="5dd74-667">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-667">Create a switch mappings dictionary.</span></span> <span data-ttu-id="5dd74-668">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-668">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="5dd74-669">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-669">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="5dd74-670">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-670">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="5dd74-671">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-671">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5dd74-672">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-672">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="5dd74-673">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-673">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="5dd74-674">Key</span><span class="sxs-lookup"><span data-stu-id="5dd74-674">Key</span></span>       | <span data-ttu-id="5dd74-675">[値]</span><span class="sxs-lookup"><span data-stu-id="5dd74-675">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="5dd74-676">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-676">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="5dd74-677">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-677">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="5dd74-678">Key</span><span class="sxs-lookup"><span data-stu-id="5dd74-678">Key</span></span>               | <span data-ttu-id="5dd74-679">[値]</span><span class="sxs-lookup"><span data-stu-id="5dd74-679">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="5dd74-680">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-680">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="5dd74-681"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-681">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="5dd74-682">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-682">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5dd74-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="5dd74-684">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-684">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="5dd74-685">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-685">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="5dd74-686">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-686">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5dd74-687">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-687">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5dd74-688">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="5dd74-688">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="5dd74-689">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="5dd74-689">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5dd74-690">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-690">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5dd74-691">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-691">Command-line arguments.</span></span>

<span data-ttu-id="5dd74-692">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-692">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="5dd74-693">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-693">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="5dd74-694">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-694">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="5dd74-695">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-695">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="5dd74-696">**例**</span><span class="sxs-lookup"><span data-stu-id="5dd74-696">**Example**</span></span>

<span data-ttu-id="5dd74-697">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-697">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="5dd74-698">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-698">Run the sample app.</span></span> <span data-ttu-id="5dd74-699">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-699">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5dd74-700">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-700">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="5dd74-701">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-701">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="5dd74-702">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-702">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="5dd74-703">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-703">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="5dd74-704">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-704">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="5dd74-705">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="5dd74-705">Prefixes</span></span>

<span data-ttu-id="5dd74-706">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-706">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="5dd74-707">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-707">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="5dd74-708">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-708">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="5dd74-709">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-709">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="5dd74-710">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-710">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5dd74-711">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="5dd74-711">**Connection string prefixes**</span></span>

<span data-ttu-id="5dd74-712">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-712">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="5dd74-713">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-713">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="5dd74-714">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="5dd74-714">Connection string prefix</span></span> | <span data-ttu-id="5dd74-715">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-715">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="5dd74-716">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-716">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="5dd74-717">MySQL</span><span class="sxs-lookup"><span data-stu-id="5dd74-717">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="5dd74-718">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="5dd74-718">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="5dd74-719">SQL Server</span><span class="sxs-lookup"><span data-stu-id="5dd74-719">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="5dd74-720">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="5dd74-720">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="5dd74-721">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-721">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="5dd74-722">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-722">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="5dd74-723">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="5dd74-723">Environment variable key</span></span> | <span data-ttu-id="5dd74-724">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="5dd74-724">Converted configuration key</span></span> | <span data-ttu-id="5dd74-725">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="5dd74-725">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5dd74-726">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-726">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5dd74-727">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5dd74-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5dd74-728">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="5dd74-728">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5dd74-729">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5dd74-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5dd74-730">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5dd74-730">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5dd74-731">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5dd74-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5dd74-732">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5dd74-732">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="5dd74-733">**例**</span><span class="sxs-lookup"><span data-stu-id="5dd74-733">**Example**</span></span>

<span data-ttu-id="5dd74-734">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-734">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="5dd74-735">名前 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="5dd74-735">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="5dd74-736">数値 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="5dd74-736">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="5dd74-737">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-737">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="5dd74-738">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-738">File Configuration Provider</span></span>

<span data-ttu-id="5dd74-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="5dd74-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="5dd74-740">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-740">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="5dd74-741">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-741">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="5dd74-742">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-742">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="5dd74-743">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-743">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="5dd74-744">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-744">INI Configuration Provider</span></span>

<span data-ttu-id="5dd74-745"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-745">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="5dd74-746">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-746">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5dd74-747">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-747">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="5dd74-748">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-748">Overloads permit specifying:</span></span>

* <span data-ttu-id="5dd74-749">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="5dd74-749">Whether the file is optional.</span></span>
* <span data-ttu-id="5dd74-750">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="5dd74-750">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5dd74-751">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-751">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5dd74-752">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-752">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5dd74-753">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="5dd74-753">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="5dd74-754">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-754">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5dd74-755">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-755">section0:key0</span></span>
* <span data-ttu-id="5dd74-756">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-756">section0:key1</span></span>
* <span data-ttu-id="5dd74-757">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="5dd74-757">section1:subsection:key</span></span>
* <span data-ttu-id="5dd74-758">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="5dd74-758">section2:subsection0:key</span></span>
* <span data-ttu-id="5dd74-759">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="5dd74-759">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="5dd74-760">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-760">JSON Configuration Provider</span></span>

<span data-ttu-id="5dd74-761"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-761">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="5dd74-762">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-762">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5dd74-763">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-763">Overloads permit specifying:</span></span>

* <span data-ttu-id="5dd74-764">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="5dd74-764">Whether the file is optional.</span></span>
* <span data-ttu-id="5dd74-765">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="5dd74-765">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5dd74-766">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-766">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5dd74-767">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-767">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5dd74-768">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-768">The method is called to load configuration from:</span></span>

* <span data-ttu-id="5dd74-769">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-769">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="5dd74-770">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-770">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="5dd74-771">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-771">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="5dd74-772">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-772">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5dd74-773">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-773">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5dd74-774">環境変数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-774">Environment variables.</span></span>
* <span data-ttu-id="5dd74-775">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-775">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5dd74-776">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="5dd74-776">Command-line arguments.</span></span>

<span data-ttu-id="5dd74-777">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-777">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="5dd74-778">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-778">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="5dd74-779">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-779">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5dd74-780">**例**</span><span class="sxs-lookup"><span data-stu-id="5dd74-780">**Example**</span></span>

<span data-ttu-id="5dd74-781">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-781">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="5dd74-782">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-782">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="5dd74-783">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-783">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="5dd74-784">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-784">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="5dd74-785">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-785">Run the sample app.</span></span> <span data-ttu-id="5dd74-786">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-786">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5dd74-787">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-787">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="5dd74-788">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-788">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="5dd74-789">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-789">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="5dd74-790">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-790">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="5dd74-791">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-791">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="5dd74-792">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-792">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="5dd74-793">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="5dd74-793">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="5dd74-794">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-794">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="5dd74-795">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-795">XML Configuration Provider</span></span>

<span data-ttu-id="5dd74-796"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-796">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="5dd74-797">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-797">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5dd74-798">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-798">Overloads permit specifying:</span></span>

* <span data-ttu-id="5dd74-799">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="5dd74-799">Whether the file is optional.</span></span>
* <span data-ttu-id="5dd74-800">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="5dd74-800">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5dd74-801">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-801">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5dd74-802">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-802">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="5dd74-803">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-803">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="5dd74-804">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-804">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5dd74-805">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-805">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="5dd74-806">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-806">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5dd74-807">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-807">section0:key0</span></span>
* <span data-ttu-id="5dd74-808">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-808">section0:key1</span></span>
* <span data-ttu-id="5dd74-809">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-809">section1:key0</span></span>
* <span data-ttu-id="5dd74-810">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-810">section1:key1</span></span>

<span data-ttu-id="5dd74-811">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-811">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="5dd74-812">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-812">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5dd74-813">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-813">section:section0:key:key0</span></span>
* <span data-ttu-id="5dd74-814">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-814">section:section0:key:key1</span></span>
* <span data-ttu-id="5dd74-815">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-815">section:section1:key:key0</span></span>
* <span data-ttu-id="5dd74-816">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-816">section:section1:key:key1</span></span>

<span data-ttu-id="5dd74-817">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-817">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="5dd74-818">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-818">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5dd74-819">key:attribute</span><span class="sxs-lookup"><span data-stu-id="5dd74-819">key:attribute</span></span>
* <span data-ttu-id="5dd74-820">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="5dd74-820">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="5dd74-821">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-821">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="5dd74-822"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-822">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="5dd74-823">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-823">The key is the file name.</span></span> <span data-ttu-id="5dd74-824">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-824">The value contains the file's contents.</span></span> <span data-ttu-id="5dd74-825">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-825">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="5dd74-826">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-826">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="5dd74-827">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-827">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="5dd74-828">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-828">Overloads permit specifying:</span></span>

* <span data-ttu-id="5dd74-829">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="5dd74-829">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="5dd74-830">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="5dd74-830">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="5dd74-831">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-831">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="5dd74-832">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-832">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="5dd74-833">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="5dd74-834">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-834">Memory Configuration Provider</span></span>

<span data-ttu-id="5dd74-835"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-835">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="5dd74-836">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-836">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5dd74-837">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-837">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="5dd74-838">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-838">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="5dd74-839">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-839">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="5dd74-840">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-840">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="5dd74-841">GetValue</span><span class="sxs-lookup"><span data-stu-id="5dd74-841">GetValue</span></span>

<span data-ttu-id="5dd74-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 指定したキーを持つ構成から単一の値を抽出し、指定したコレクション以外の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="5dd74-843">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-843">An overload accepts a default value.</span></span>

<span data-ttu-id="5dd74-844">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-844">The following example:</span></span>

* <span data-ttu-id="5dd74-845">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-845">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="5dd74-846">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-846">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="5dd74-847">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-847">Types the value as an `int`.</span></span>
* <span data-ttu-id="5dd74-848">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-848">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="5dd74-849">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="5dd74-849">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="5dd74-850">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-850">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="5dd74-851">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-851">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="5dd74-852">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-852">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="5dd74-853">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-853">section0:key0</span></span>
* <span data-ttu-id="5dd74-854">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-854">section0:key1</span></span>
* <span data-ttu-id="5dd74-855">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-855">section1:key0</span></span>
* <span data-ttu-id="5dd74-856">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-856">section1:key1</span></span>
* <span data-ttu-id="5dd74-857">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-857">section2:subsection0:key0</span></span>
* <span data-ttu-id="5dd74-858">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-858">section2:subsection0:key1</span></span>
* <span data-ttu-id="5dd74-859">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="5dd74-859">section2:subsection1:key0</span></span>
* <span data-ttu-id="5dd74-860">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="5dd74-860">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="5dd74-861">GetSection</span><span class="sxs-lookup"><span data-stu-id="5dd74-861">GetSection</span></span>

<span data-ttu-id="5dd74-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="5dd74-863">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-863">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="5dd74-864">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-864">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="5dd74-865">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-865">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="5dd74-866">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-866">`GetSection` never returns `null`.</span></span> <span data-ttu-id="5dd74-867">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-867">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="5dd74-868">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-868">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="5dd74-869"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="5dd74-870">GetChildren</span><span class="sxs-lookup"><span data-stu-id="5dd74-870">GetChildren</span></span>

<span data-ttu-id="5dd74-871">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-871">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="5dd74-872">既存</span><span class="sxs-lookup"><span data-stu-id="5dd74-872">Exists</span></span>

<span data-ttu-id="5dd74-873">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-873">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="5dd74-874">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-874">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="5dd74-875">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="5dd74-875">Bind to an object graph</span></span>

<span data-ttu-id="5dd74-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="5dd74-877">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-877">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="5dd74-878">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="5dd74-878">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="5dd74-879">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-879">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="5dd74-880">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-880">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="5dd74-881">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-881">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="5dd74-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 指定された型をバインドして返します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="5dd74-883">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-883">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="5dd74-884">次のコードは、上記の例で `Get<T>` を使用する方法を示します：</span><span class="sxs-lookup"><span data-stu-id="5dd74-884">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5dd74-885">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="5dd74-885">Bind an array to a class</span></span>

<span data-ttu-id="5dd74-886">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="5dd74-886">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="5dd74-887"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="5dd74-887">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5dd74-888">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-888">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="5dd74-889">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-889">Binding is provided by convention.</span></span> <span data-ttu-id="5dd74-890">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-890">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="5dd74-891">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="5dd74-891">**In-memory array processing**</span></span>

<span data-ttu-id="5dd74-892">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-892">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="5dd74-893">Key</span><span class="sxs-lookup"><span data-stu-id="5dd74-893">Key</span></span>             | <span data-ttu-id="5dd74-894">[値]</span><span class="sxs-lookup"><span data-stu-id="5dd74-894">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5dd74-895">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="5dd74-895">array:entries:0</span></span> | <span data-ttu-id="5dd74-896">value0</span><span class="sxs-lookup"><span data-stu-id="5dd74-896">value0</span></span> |
| <span data-ttu-id="5dd74-897">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="5dd74-897">array:entries:1</span></span> | <span data-ttu-id="5dd74-898">value1</span><span class="sxs-lookup"><span data-stu-id="5dd74-898">value1</span></span> |
| <span data-ttu-id="5dd74-899">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="5dd74-899">array:entries:2</span></span> | <span data-ttu-id="5dd74-900">value2</span><span class="sxs-lookup"><span data-stu-id="5dd74-900">value2</span></span> |
| <span data-ttu-id="5dd74-901">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="5dd74-901">array:entries:4</span></span> | <span data-ttu-id="5dd74-902">value4</span><span class="sxs-lookup"><span data-stu-id="5dd74-902">value4</span></span> |
| <span data-ttu-id="5dd74-903">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="5dd74-903">array:entries:5</span></span> | <span data-ttu-id="5dd74-904">value5</span><span class="sxs-lookup"><span data-stu-id="5dd74-904">value5</span></span> |

<span data-ttu-id="5dd74-905">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-905">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="5dd74-906">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-906">The array skips a value for index &num;3.</span></span> <span data-ttu-id="5dd74-907">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-907">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="5dd74-908">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-908">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="5dd74-909">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-909">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="5dd74-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用することもできます。これにより、コードがよりコンパクトになります：</span><span class="sxs-lookup"><span data-stu-id="5dd74-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="5dd74-911">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-911">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="5dd74-912">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="5dd74-912">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5dd74-913">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="5dd74-913">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5dd74-914">0</span><span class="sxs-lookup"><span data-stu-id="5dd74-914">0</span></span>                            | <span data-ttu-id="5dd74-915">value0</span><span class="sxs-lookup"><span data-stu-id="5dd74-915">value0</span></span>                       |
| <span data-ttu-id="5dd74-916">1</span><span class="sxs-lookup"><span data-stu-id="5dd74-916">1</span></span>                            | <span data-ttu-id="5dd74-917">value1</span><span class="sxs-lookup"><span data-stu-id="5dd74-917">value1</span></span>                       |
| <span data-ttu-id="5dd74-918">2</span><span class="sxs-lookup"><span data-stu-id="5dd74-918">2</span></span>                            | <span data-ttu-id="5dd74-919">value2</span><span class="sxs-lookup"><span data-stu-id="5dd74-919">value2</span></span>                       |
| <span data-ttu-id="5dd74-920">3</span><span class="sxs-lookup"><span data-stu-id="5dd74-920">3</span></span>                            | <span data-ttu-id="5dd74-921">value4</span><span class="sxs-lookup"><span data-stu-id="5dd74-921">value4</span></span>                       |
| <span data-ttu-id="5dd74-922">4</span><span class="sxs-lookup"><span data-stu-id="5dd74-922">4</span></span>                            | <span data-ttu-id="5dd74-923">value5</span><span class="sxs-lookup"><span data-stu-id="5dd74-923">value5</span></span>                       |

<span data-ttu-id="5dd74-924">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-924">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="5dd74-925">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-925">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="5dd74-926">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-926">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="5dd74-927">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-927">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="5dd74-928">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-928">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="5dd74-929">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-929">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="5dd74-930">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="5dd74-930">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="5dd74-931">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-931">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="5dd74-932">Key</span><span class="sxs-lookup"><span data-stu-id="5dd74-932">Key</span></span>             | <span data-ttu-id="5dd74-933">[値]</span><span class="sxs-lookup"><span data-stu-id="5dd74-933">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5dd74-934">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="5dd74-934">array:entries:3</span></span> | <span data-ttu-id="5dd74-935">value3</span><span class="sxs-lookup"><span data-stu-id="5dd74-935">value3</span></span> |

<span data-ttu-id="5dd74-936">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-936">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="5dd74-937">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="5dd74-937">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5dd74-938">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="5dd74-938">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5dd74-939">0</span><span class="sxs-lookup"><span data-stu-id="5dd74-939">0</span></span>                            | <span data-ttu-id="5dd74-940">value0</span><span class="sxs-lookup"><span data-stu-id="5dd74-940">value0</span></span>                       |
| <span data-ttu-id="5dd74-941">1</span><span class="sxs-lookup"><span data-stu-id="5dd74-941">1</span></span>                            | <span data-ttu-id="5dd74-942">value1</span><span class="sxs-lookup"><span data-stu-id="5dd74-942">value1</span></span>                       |
| <span data-ttu-id="5dd74-943">2</span><span class="sxs-lookup"><span data-stu-id="5dd74-943">2</span></span>                            | <span data-ttu-id="5dd74-944">value2</span><span class="sxs-lookup"><span data-stu-id="5dd74-944">value2</span></span>                       |
| <span data-ttu-id="5dd74-945">3</span><span class="sxs-lookup"><span data-stu-id="5dd74-945">3</span></span>                            | <span data-ttu-id="5dd74-946">value3</span><span class="sxs-lookup"><span data-stu-id="5dd74-946">value3</span></span>                       |
| <span data-ttu-id="5dd74-947">4</span><span class="sxs-lookup"><span data-stu-id="5dd74-947">4</span></span>                            | <span data-ttu-id="5dd74-948">value4</span><span class="sxs-lookup"><span data-stu-id="5dd74-948">value4</span></span>                       |
| <span data-ttu-id="5dd74-949">5</span><span class="sxs-lookup"><span data-stu-id="5dd74-949">5</span></span>                            | <span data-ttu-id="5dd74-950">value5</span><span class="sxs-lookup"><span data-stu-id="5dd74-950">value5</span></span>                       |

<span data-ttu-id="5dd74-951">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="5dd74-951">**JSON array processing**</span></span>

<span data-ttu-id="5dd74-952">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-952">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="5dd74-953">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="5dd74-953">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="5dd74-954">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-954">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="5dd74-955">Key</span><span class="sxs-lookup"><span data-stu-id="5dd74-955">Key</span></span>                     | <span data-ttu-id="5dd74-956">[値]</span><span class="sxs-lookup"><span data-stu-id="5dd74-956">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="5dd74-957">json_array:key</span><span class="sxs-lookup"><span data-stu-id="5dd74-957">json_array:key</span></span>          | <span data-ttu-id="5dd74-958">valueA</span><span class="sxs-lookup"><span data-stu-id="5dd74-958">valueA</span></span> |
| <span data-ttu-id="5dd74-959">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="5dd74-959">json_array:subsection:0</span></span> | <span data-ttu-id="5dd74-960">valueB</span><span class="sxs-lookup"><span data-stu-id="5dd74-960">valueB</span></span> |
| <span data-ttu-id="5dd74-961">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="5dd74-961">json_array:subsection:1</span></span> | <span data-ttu-id="5dd74-962">valueC</span><span class="sxs-lookup"><span data-stu-id="5dd74-962">valueC</span></span> |
| <span data-ttu-id="5dd74-963">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="5dd74-963">json_array:subsection:2</span></span> | <span data-ttu-id="5dd74-964">valueD</span><span class="sxs-lookup"><span data-stu-id="5dd74-964">valueD</span></span> |

<span data-ttu-id="5dd74-965">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-965">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="5dd74-966">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-966">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="5dd74-967">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-967">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="5dd74-968">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="5dd74-968">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="5dd74-969">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="5dd74-969">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="5dd74-970">0</span><span class="sxs-lookup"><span data-stu-id="5dd74-970">0</span></span>                                   | <span data-ttu-id="5dd74-971">valueB</span><span class="sxs-lookup"><span data-stu-id="5dd74-971">valueB</span></span>                              |
| <span data-ttu-id="5dd74-972">1</span><span class="sxs-lookup"><span data-stu-id="5dd74-972">1</span></span>                                   | <span data-ttu-id="5dd74-973">valueC</span><span class="sxs-lookup"><span data-stu-id="5dd74-973">valueC</span></span>                              |
| <span data-ttu-id="5dd74-974">2</span><span class="sxs-lookup"><span data-stu-id="5dd74-974">2</span></span>                                   | <span data-ttu-id="5dd74-975">valueD</span><span class="sxs-lookup"><span data-stu-id="5dd74-975">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="5dd74-976">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5dd74-976">Custom configuration provider</span></span>

<span data-ttu-id="5dd74-977">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-977">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="5dd74-978">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5dd74-978">The provider has the following characteristics:</span></span>

* <span data-ttu-id="5dd74-979">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-979">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="5dd74-980">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-980">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="5dd74-981">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-981">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="5dd74-982">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-982">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="5dd74-983">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="5dd74-983">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="5dd74-984">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-984">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="5dd74-985">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-985">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5dd74-986">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-986">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5dd74-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5dd74-988"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-988">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5dd74-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5dd74-990"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-990">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5dd74-991">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-991">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="5dd74-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5dd74-993">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="5dd74-993">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5dd74-994">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5dd74-994">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5dd74-995">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-995">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="5dd74-996">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="5dd74-996">Access configuration during startup</span></span>

<span data-ttu-id="5dd74-997">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="5dd74-997">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5dd74-998">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-998">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="5dd74-999">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-999">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="5dd74-1000">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="5dd74-1000">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="5dd74-1001">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="5dd74-1001">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="5dd74-1002">Razor ページ:</span><span class="sxs-lookup"><span data-stu-id="5dd74-1002">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="5dd74-1003">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="5dd74-1003">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="5dd74-1004">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="5dd74-1004">Add configuration from an external assembly</span></span>

<span data-ttu-id="5dd74-1005"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="5dd74-1005">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="5dd74-1006">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5dd74-1006">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5dd74-1007">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5dd74-1007">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
