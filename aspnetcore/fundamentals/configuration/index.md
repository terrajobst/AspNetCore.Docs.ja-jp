---
title: ASP.NET Core の構成
author: guardrex
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 3351ab743ce38b78b1c5857e52020fdeda12cbe7
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855815"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="9d754-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="9d754-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="9d754-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9d754-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9d754-105">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="9d754-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="9d754-106">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d754-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="9d754-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9d754-107">Azure Key Vault</span></span>
* <span data-ttu-id="9d754-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9d754-108">Azure App Configuration</span></span>
* <span data-ttu-id="9d754-109">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="9d754-109">Command-line arguments</span></span>
* <span data-ttu-id="9d754-110">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="9d754-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="9d754-111">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="9d754-111">Directory files</span></span>
* <span data-ttu-id="9d754-112">環境変数</span><span class="sxs-lookup"><span data-stu-id="9d754-112">Environment variables</span></span>
* <span data-ttu-id="9d754-113">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="9d754-113">In-memory .NET objects</span></span>
* <span data-ttu-id="9d754-114">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="9d754-114">Settings files</span></span>

<span data-ttu-id="9d754-115">一般的な構成プロバイダーのシナリオに向けた構成パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-115">Configuration packages for common configuration provider scenarios are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="9d754-116">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="9d754-116">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="9d754-117">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="9d754-117">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="9d754-118">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="9d754-118">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="9d754-119">オプション パターンの使用について詳しくは、「<xref:fundamentals/configuration/options>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d754-119">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9d754-120">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9d754-120">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9d754-121">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="9d754-121">Host versus app configuration</span></span>

<span data-ttu-id="9d754-122">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="9d754-122">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9d754-123">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="9d754-123">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9d754-124">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="9d754-124">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9d754-125">ホストの構成のキーと値のペアは、アプリのグローバル構成の一部となります。</span><span class="sxs-lookup"><span data-stu-id="9d754-125">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="9d754-126">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、[ホスト](xref:fundamentals/index#host)に関する説明を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-126">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see [The host](xref:fundamentals/index#host).</span></span>

## <a name="default-configuration"></a><span data-ttu-id="9d754-127">既定の構成</span><span class="sxs-lookup"><span data-stu-id="9d754-127">Default configuration</span></span>

<span data-ttu-id="9d754-128">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-128">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="9d754-129"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-129"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

* <span data-ttu-id="9d754-130">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-130">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9d754-131">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="9d754-131">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9d754-132">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-132">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9d754-133">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="9d754-133">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9d754-134">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-134">App configuration is provided from:</span></span>
  * <span data-ttu-id="9d754-135">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="9d754-135">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9d754-136">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="9d754-136">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9d754-137">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9d754-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="9d754-138">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="9d754-138">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9d754-139">カスタム プレフィックスが使用されている場合 (たとえば、`PREFIX_` と `.AddEnvironmentVariables(prefix: "PREFIX_")`)、構成のキーと値のペアが読み込まれるときにプレフィックスが削除されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-139">If a custom prefix is used (for example, `PREFIX_` with `.AddEnvironmentVariables(prefix: "PREFIX_")`), the prefix is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9d754-140">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="9d754-140">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="9d754-141">これらの構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="9d754-141">The configuration providers are explained later in this topic.</span></span> <span data-ttu-id="9d754-142">ホストと <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> の詳細については、<xref:fundamentals/host/web-host#set-up-a-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-142">For more information on the host and <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="security"></a><span data-ttu-id="9d754-143">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="9d754-143">Security</span></span>

<span data-ttu-id="9d754-144">次のベスト プラクティスを採用します。</span><span class="sxs-lookup"><span data-stu-id="9d754-144">Adopt the following best practices:</span></span>

* <span data-ttu-id="9d754-145">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="9d754-145">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="9d754-146">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="9d754-146">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9d754-147">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="9d754-147">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9d754-148">[複数の環境を使用する方法](xref:fundamentals/environments)や、[シークレット マネージャーでの開発中のアプリ シークレットの安全な格納](xref:security/app-secrets)の管理 (機密データを格納するための環境変数の使用に関するアドバイスを含む) について、さらに学習することができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-148">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="9d754-149">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="9d754-149">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="9d754-150">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="9d754-150">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="9d754-151">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) は、アプリのシークレットを安全に格納するための 1 つのオプションです。</span><span class="sxs-lookup"><span data-stu-id="9d754-151">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="9d754-152">詳細については、<xref:security/key-vault-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-152">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9d754-153">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="9d754-153">Hierarchical configuration data</span></span>

<span data-ttu-id="9d754-154">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-154">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9d754-155">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="9d754-155">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="9d754-156">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-156">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="9d754-157">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-157">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="9d754-158">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-158">section0:key0</span></span>
* <span data-ttu-id="9d754-159">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-159">section0:key1</span></span>
* <span data-ttu-id="9d754-160">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-160">section1:key0</span></span>
* <span data-ttu-id="9d754-161">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-161">section1:key1</span></span>

<span data-ttu-id="9d754-162"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-162"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9d754-163">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="9d754-163">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span> <span data-ttu-id="9d754-164">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-164">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="conventions"></a><span data-ttu-id="9d754-165">規約</span><span class="sxs-lookup"><span data-stu-id="9d754-165">Conventions</span></span>

<span data-ttu-id="9d754-166">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9d754-166">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="9d754-167">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="9d754-167">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="9d754-168">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="9d754-168">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="9d754-169"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-169"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9d754-170"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> を挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-170"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    // The _config local variable is used to obtain configuration 
    // throughout the class.
}
```

<span data-ttu-id="9d754-171">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="9d754-171">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="9d754-172">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-172">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="9d754-173">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="9d754-173">Keys are case-insensitive.</span></span> <span data-ttu-id="9d754-174">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="9d754-174">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9d754-175">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="9d754-175">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="9d754-176">階層キー</span><span class="sxs-lookup"><span data-stu-id="9d754-176">Hierarchical keys</span></span>
  * <span data-ttu-id="9d754-177">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="9d754-177">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9d754-178">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="9d754-178">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9d754-179">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに変換されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-179">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="9d754-180">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9d754-180">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9d754-181">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d754-181">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9d754-182"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9d754-182">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9d754-183">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="9d754-183">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="9d754-184">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-184">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="9d754-185">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="9d754-185">Values are strings.</span></span>
* <span data-ttu-id="9d754-186">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="9d754-186">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="9d754-187">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-187">Providers</span></span>

<span data-ttu-id="9d754-188">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="9d754-188">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9d754-189">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-189">Provider</span></span> | <span data-ttu-id="9d754-190">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="9d754-190">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="9d754-191">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="9d754-191">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="9d754-192">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9d754-192">Azure Key Vault</span></span> |
| <span data-ttu-id="9d754-193">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="9d754-193">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="9d754-194">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9d754-194">Azure App Configuration</span></span> |
| [<span data-ttu-id="9d754-195">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-195">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="9d754-196">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="9d754-196">Command-line parameters</span></span> |
| [<span data-ttu-id="9d754-197">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-197">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="9d754-198">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="9d754-198">Custom source</span></span> |
| [<span data-ttu-id="9d754-199">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-199">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="9d754-200">環境変数</span><span class="sxs-lookup"><span data-stu-id="9d754-200">Environment variables</span></span> |
| [<span data-ttu-id="9d754-201">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-201">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="9d754-202">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="9d754-202">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="9d754-203">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-203">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="9d754-204">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="9d754-204">Directory files</span></span> |
| [<span data-ttu-id="9d754-205">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-205">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="9d754-206">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="9d754-206">In-memory collections</span></span> |
| <span data-ttu-id="9d754-207">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="9d754-207">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="9d754-208">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="9d754-208">File in the user profile directory</span></span> |

<span data-ttu-id="9d754-209">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9d754-209">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="9d754-210">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="9d754-210">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="9d754-211">基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="9d754-211">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="9d754-212">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9d754-212">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9d754-213">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="9d754-213">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="9d754-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9d754-214">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="9d754-215">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境の場合のみ)</span><span class="sxs-lookup"><span data-stu-id="9d754-215">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="9d754-216">環境変数</span><span class="sxs-lookup"><span data-stu-id="9d754-216">Environment variables</span></span>
1. <span data-ttu-id="9d754-217">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="9d754-217">Command-line arguments</span></span>

<span data-ttu-id="9d754-218">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="9d754-218">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9d754-219">この一連のプロバイダーは、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して新しい <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を初期化するときに配置されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-219">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="9d754-220">詳細については、「[Web ホスト: ホストを設定する](xref:fundamentals/host/web-host#set-up-a-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-220">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

## <a name="configureappconfiguration"></a><span data-ttu-id="9d754-221">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9d754-221">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9d754-222">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出し、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-222">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

<span data-ttu-id="9d754-223"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-223">Configuration supplied to the app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9d754-224">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-224">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="9d754-225">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-225">Command-line Configuration Provider</span></span>

<span data-ttu-id="9d754-226"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-226">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="9d754-227">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-227">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9d754-228"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> で新しい <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を初期化すると、自動的に `AddCommandLine` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-228">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="9d754-229">詳細については、「[Web ホスト: ホストを設定する](xref:fundamentals/host/web-host#set-up-a-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-229">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="9d754-230">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-230">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9d754-231">*appsettings.json* および *appsettings.{Environment}.json* からのオプションの構成。</span><span class="sxs-lookup"><span data-stu-id="9d754-231">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="9d754-232">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境の場合)。</span><span class="sxs-lookup"><span data-stu-id="9d754-232">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="9d754-233">環境変数。</span><span class="sxs-lookup"><span data-stu-id="9d754-233">Environment variables.</span></span>

<span data-ttu-id="9d754-234">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="9d754-234">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="9d754-235">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="9d754-235">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="9d754-236">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="9d754-236">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="9d754-237">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-237">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="9d754-238">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-238">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="9d754-239">`AddCommandLine` は既に `CreateDefaultBuilder` によって呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="9d754-239">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9d754-240">アプリの構成を指定して、引き続きコマンドラインの引数でその構成をオーバーライドできるようにする必要がある場合、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> でアプリの追加のプロバイダーを呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-240">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call other providers here and call AddCommandLine last.
                config.AddCommandLine(args);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="9d754-241"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-241">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    // Call additional providers here as needed.
    // Call AddCommandLine last to allow arguments to override other configuration.
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="9d754-242">**例**</span><span class="sxs-lookup"><span data-stu-id="9d754-242">**Example**</span></span>

<span data-ttu-id="9d754-243">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-243">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="9d754-244">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d754-244">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="9d754-245">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="9d754-245">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="9d754-246">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d754-246">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9d754-247">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="9d754-247">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="9d754-248">引数</span><span class="sxs-lookup"><span data-stu-id="9d754-248">Arguments</span></span>

<span data-ttu-id="9d754-249">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d754-249">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="9d754-250">等号を使用する場合は、値に null を指定できます (例: `CommandLineKey=`)。</span><span class="sxs-lookup"><span data-stu-id="9d754-250">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="9d754-251">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="9d754-251">Key prefix</span></span>               | <span data-ttu-id="9d754-252">例</span><span class="sxs-lookup"><span data-stu-id="9d754-252">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="9d754-253">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="9d754-253">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="9d754-254">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="9d754-254">Two dashes (`--`)</span></span>        | <span data-ttu-id="9d754-255">`--CommandLineKey2=value2`、 `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="9d754-255">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="9d754-256">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="9d754-256">Forward slash (`/`)</span></span>      | <span data-ttu-id="9d754-257">`/CommandLineKey3=value3`、 `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="9d754-257">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="9d754-258">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="9d754-258">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="9d754-259">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="9d754-259">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="9d754-260">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="9d754-260">Switch mappings</span></span>

<span data-ttu-id="9d754-261">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-261">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="9d754-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-262">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9d754-263">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-263">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9d754-264">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-264">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9d754-265">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="9d754-265">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9d754-266">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="9d754-266">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9d754-267">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d754-267">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="9d754-268">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="9d754-268">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9d754-269">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-269">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _switchMappings = 
        new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    // Do not pass the args to CreateDefaultBuilder
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder()
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddCommandLine(args, _switchMappings);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="9d754-270">前の例で示したように、スイッチ マッピングを使用する場合は `CreateDefaultBuilder` への呼び出しが引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="9d754-270">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="9d754-271">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="9d754-271">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9d754-272">引数にマップされたスイッチが含まれており、それが `CreateDefaultBuilder` に渡される場合は、その `AddCommandLine` プロバイダーは <xref:System.FormatException> で初期化に失敗します。</span><span class="sxs-lookup"><span data-stu-id="9d754-272">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="9d754-273">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-273">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="9d754-274">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-274">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="9d754-275">キー</span><span class="sxs-lookup"><span data-stu-id="9d754-275">Key</span></span>       | <span data-ttu-id="9d754-276">[値]</span><span class="sxs-lookup"><span data-stu-id="9d754-276">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="9d754-277">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9d754-277">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="9d754-278">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-278">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="9d754-279">キー</span><span class="sxs-lookup"><span data-stu-id="9d754-279">Key</span></span>               | <span data-ttu-id="9d754-280">[値]</span><span class="sxs-lookup"><span data-stu-id="9d754-280">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="9d754-281">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-281">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="9d754-282"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-282">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="9d754-283">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-283">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9d754-284">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure Portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-284">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="9d754-285">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-285">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="9d754-286">新しい <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> が初期化されるときに、[ホスト構成](#host-versus-app-configuration) の `ASPNETCORE_` でプレフィックスされている環境変数を読み込むために `AddEnvironmentVariables` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-286">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is initialized.</span></span> <span data-ttu-id="9d754-287">詳細については、「[Web ホスト: ホストを設定する](xref:fundamentals/host/web-host#set-up-a-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-287">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="9d754-288">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-288">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9d754-289">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="9d754-289">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="9d754-290">*appsettings.json* および *appsettings.{Environment}.json* からのオプションの構成。</span><span class="sxs-lookup"><span data-stu-id="9d754-290">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="9d754-291">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境の場合)。</span><span class="sxs-lookup"><span data-stu-id="9d754-291">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="9d754-292">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9d754-292">Command-line arguments.</span></span>

<span data-ttu-id="9d754-293">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-293">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="9d754-294">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-294">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="9d754-295">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-295">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="9d754-296">追加の環境変数からアプリの構成を指定する必要がある場合は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> のアプリの追加プロバイダーを呼び出し、そのプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-296">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call additional providers here as needed.
                // Call AddEnvironmentVariables last if you need to allow
                // environment variables to override values from other 
                // providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="9d754-297"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-297">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="9d754-298">**例**</span><span class="sxs-lookup"><span data-stu-id="9d754-298">**Example**</span></span>

<span data-ttu-id="9d754-299">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-299">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="9d754-300">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d754-300">Run the sample app.</span></span> <span data-ttu-id="9d754-301">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d754-301">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9d754-302">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="9d754-302">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="9d754-303">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="9d754-303">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="9d754-304">アプリによって表示される環境変数のリストを短くするために、アプリでは次で始まる環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-304">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="9d754-305">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="9d754-305">ASPNETCORE_</span></span>
* <span data-ttu-id="9d754-306">urls</span><span class="sxs-lookup"><span data-stu-id="9d754-306">urls</span></span>
* <span data-ttu-id="9d754-307">ログの記録</span><span class="sxs-lookup"><span data-stu-id="9d754-307">Logging</span></span>
* <span data-ttu-id="9d754-308">ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="9d754-308">ENVIRONMENT</span></span>
* <span data-ttu-id="9d754-309">contentRoot</span><span class="sxs-lookup"><span data-stu-id="9d754-309">contentRoot</span></span>
* <span data-ttu-id="9d754-310">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="9d754-310">AllowedHosts</span></span>
* <span data-ttu-id="9d754-311">applicationName</span><span class="sxs-lookup"><span data-stu-id="9d754-311">applicationName</span></span>
* <span data-ttu-id="9d754-312">CommandLine</span><span class="sxs-lookup"><span data-stu-id="9d754-312">CommandLine</span></span>

<span data-ttu-id="9d754-313">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="9d754-313">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="9d754-314">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="9d754-314">Prefixes</span></span>

<span data-ttu-id="9d754-315">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-315">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="9d754-316">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-316">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="9d754-317">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-317">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="9d754-318">静的な簡易メソッド `CreateDefaultBuilder` によって <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> が作成され、アプリのホストが確立されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-318">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="9d754-319">`WebHostBuilder` は、作成されるときに、`ASPNETCORE_` というプレフィックスが付いた環境変数からホストの構成を見つけます。</span><span class="sxs-lookup"><span data-stu-id="9d754-319">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

<span data-ttu-id="9d754-320">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="9d754-320">**Connection string prefixes**</span></span>

<span data-ttu-id="9d754-321">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="9d754-321">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9d754-322">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-322">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9d754-323">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="9d754-323">Connection string prefix</span></span> | <span data-ttu-id="9d754-324">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-324">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="9d754-325">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-325">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="9d754-326">MySQL</span><span class="sxs-lookup"><span data-stu-id="9d754-326">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="9d754-327">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="9d754-327">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="9d754-328">SQL Server</span><span class="sxs-lookup"><span data-stu-id="9d754-328">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="9d754-329">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="9d754-329">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9d754-330">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-330">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9d754-331">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="9d754-331">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9d754-332">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="9d754-332">Environment variable key</span></span> | <span data-ttu-id="9d754-333">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="9d754-333">Converted configuration key</span></span> | <span data-ttu-id="9d754-334">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="9d754-334">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="9d754-335">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="9d754-335">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="9d754-336">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9d754-336">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="9d754-337">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="9d754-337">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="9d754-338">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9d754-338">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="9d754-339">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9d754-339">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="9d754-340">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9d754-340">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="9d754-341">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9d754-341">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="9d754-342">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-342">File Configuration Provider</span></span>

<span data-ttu-id="9d754-343"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="9d754-343"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9d754-344">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="9d754-344">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="9d754-345">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-345">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9d754-346">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-346">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="9d754-347">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-347">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9d754-348">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-348">INI Configuration Provider</span></span>

<span data-ttu-id="9d754-349"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-349">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9d754-350">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-350">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9d754-351">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-351">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="9d754-352">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-352">Overloads permit specifying:</span></span>

* <span data-ttu-id="9d754-353">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="9d754-353">Whether the file is optional.</span></span>
* <span data-ttu-id="9d754-354">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="9d754-354">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9d754-355">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-355">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9d754-356">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-356">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddIniFile(
                    "config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="9d754-357">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d754-357">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="9d754-358">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-358">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-359"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-359">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddIniFile("config.ini", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="9d754-360">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d754-360">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="9d754-361">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-361">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-362">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="9d754-362">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="9d754-363">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-363">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9d754-364">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-364">section0:key0</span></span>
* <span data-ttu-id="9d754-365">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-365">section0:key1</span></span>
* <span data-ttu-id="9d754-366">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="9d754-366">section1:subsection:key</span></span>
* <span data-ttu-id="9d754-367">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="9d754-367">section2:subsection0:key</span></span>
* <span data-ttu-id="9d754-368">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="9d754-368">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="9d754-369">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-369">JSON Configuration Provider</span></span>

<span data-ttu-id="9d754-370"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-370">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="9d754-371">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-371">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9d754-372">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-372">Overloads permit specifying:</span></span>

* <span data-ttu-id="9d754-373">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="9d754-373">Whether the file is optional.</span></span>
* <span data-ttu-id="9d754-374">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="9d754-374">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9d754-375">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-375">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9d754-376"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> で新しい <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を初期化すると、自動的に `AddJsonFile` が 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-376">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="9d754-377">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-377">The method is called to load configuration from:</span></span>

* <span data-ttu-id="9d754-378">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9d754-378">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="9d754-379">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="9d754-379">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="9d754-380">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-380">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="9d754-381">詳細については、「[Web ホスト: ホストを設定する](xref:fundamentals/host/web-host#set-up-a-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-381">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="9d754-382">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-382">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9d754-383">環境変数。</span><span class="sxs-lookup"><span data-stu-id="9d754-383">Environment variables.</span></span>
* <span data-ttu-id="9d754-384">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境の場合)。</span><span class="sxs-lookup"><span data-stu-id="9d754-384">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="9d754-385">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9d754-385">Command-line arguments.</span></span>

<span data-ttu-id="9d754-386">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-386">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="9d754-387">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="9d754-387">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="9d754-388">ホストのビルド時に <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-388">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddJsonFile(
                    "config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="9d754-389">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d754-389">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="9d754-390">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-390">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-391"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-391">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("config.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="9d754-392">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d754-392">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="9d754-393">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-393">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-394">**例**</span><span class="sxs-lookup"><span data-stu-id="9d754-394">**Example**</span></span>

<span data-ttu-id="9d754-395">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-395">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="9d754-396">構成は、*appsettings.json* および *appsettings.{Environment}.json* から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-396">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="9d754-397">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9d754-397">Run the sample app.</span></span> <span data-ttu-id="9d754-398">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9d754-398">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9d754-399">出力に、環境に応じて、表に示す構成のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="9d754-399">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="9d754-400">ログの構成キーでは、階層の区切り文字としてコロン (`:`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-400">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="9d754-401">キー</span><span class="sxs-lookup"><span data-stu-id="9d754-401">Key</span></span>                        | <span data-ttu-id="9d754-402">開発時の値</span><span class="sxs-lookup"><span data-stu-id="9d754-402">Development Value</span></span> | <span data-ttu-id="9d754-403">運用時の値</span><span class="sxs-lookup"><span data-stu-id="9d754-403">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="9d754-404">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="9d754-404">Logging:LogLevel:System</span></span>    | <span data-ttu-id="9d754-405">情報</span><span class="sxs-lookup"><span data-stu-id="9d754-405">Information</span></span>       | <span data-ttu-id="9d754-406">情報</span><span class="sxs-lookup"><span data-stu-id="9d754-406">Information</span></span>      |
| <span data-ttu-id="9d754-407">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="9d754-407">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="9d754-408">情報</span><span class="sxs-lookup"><span data-stu-id="9d754-408">Information</span></span>       | <span data-ttu-id="9d754-409">情報</span><span class="sxs-lookup"><span data-stu-id="9d754-409">Information</span></span>      |
| <span data-ttu-id="9d754-410">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="9d754-410">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="9d754-411">デバッグ</span><span class="sxs-lookup"><span data-stu-id="9d754-411">Debug</span></span>             | <span data-ttu-id="9d754-412">Error</span><span class="sxs-lookup"><span data-stu-id="9d754-412">Error</span></span>            |
| <span data-ttu-id="9d754-413">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="9d754-413">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="9d754-414">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-414">XML Configuration Provider</span></span>

<span data-ttu-id="9d754-415"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-415">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9d754-416">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-416">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9d754-417">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-417">Overloads permit specifying:</span></span>

* <span data-ttu-id="9d754-418">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="9d754-418">Whether the file is optional.</span></span>
* <span data-ttu-id="9d754-419">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="9d754-419">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9d754-420">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-420">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9d754-421">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-421">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="9d754-422">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="9d754-422">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="9d754-423">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-423">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddXmlFile(
                    "config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="9d754-424">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d754-424">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="9d754-425">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-425">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-426"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-426">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="9d754-427">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d754-427">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="9d754-428">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-428">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-429">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-429">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="9d754-430">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-430">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9d754-431">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-431">section0:key0</span></span>
* <span data-ttu-id="9d754-432">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-432">section0:key1</span></span>
* <span data-ttu-id="9d754-433">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-433">section1:key0</span></span>
* <span data-ttu-id="9d754-434">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-434">section1:key1</span></span>

<span data-ttu-id="9d754-435">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="9d754-435">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="9d754-436">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-436">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9d754-437">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-437">section:section0:key:key0</span></span>
* <span data-ttu-id="9d754-438">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-438">section:section0:key:key1</span></span>
* <span data-ttu-id="9d754-439">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-439">section:section1:key:key0</span></span>
* <span data-ttu-id="9d754-440">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-440">section:section1:key:key1</span></span>

<span data-ttu-id="9d754-441">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-441">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9d754-442">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-442">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9d754-443">key:attribute</span><span class="sxs-lookup"><span data-stu-id="9d754-443">key:attribute</span></span>
* <span data-ttu-id="9d754-444">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="9d754-444">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9d754-445">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-445">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="9d754-446"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-446">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9d754-447">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="9d754-447">The key is the file name.</span></span> <span data-ttu-id="9d754-448">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-448">The value contains the file's contents.</span></span> <span data-ttu-id="9d754-449">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-449">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9d754-450">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-450">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9d754-451">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d754-451">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9d754-452">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-452">Overloads permit specifying:</span></span>

* <span data-ttu-id="9d754-453">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="9d754-453">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9d754-454">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="9d754-454">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9d754-455">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-455">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9d754-456">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-456">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9d754-457">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-457">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                var path = Path.Combine(
                    Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="9d754-458">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9d754-458">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="9d754-459">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-459">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-460"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-460">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
var config = new ConfigurationBuilder()
    .AddKeyPerFile(directoryPath: path, optional: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="9d754-461">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-461">Memory Configuration Provider</span></span>

<span data-ttu-id="9d754-462"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-462">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9d754-463">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-463">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9d754-464">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-464">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="9d754-465">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-465">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _dict = 
        new Dictionary<string, string>
        {
            {"MemoryCollectionKey1", "value1"},
            {"MemoryCollectionKey2", "value2"}
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddInMemoryCollection(_dict);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="9d754-466"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-466">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

var config = new ConfigurationBuilder()
    .AddInMemoryCollection(dict)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="getvalue"></a><span data-ttu-id="9d754-467">GetValue</span><span class="sxs-lookup"><span data-stu-id="9d754-467">GetValue</span></span>

<span data-ttu-id="9d754-468">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) によって、指定したキーで構成から値が抽出され、それが指定した型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-468">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="9d754-469">オーバーロードを使用すると、キーが見つからない場合に既定値を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-469">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="9d754-470">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="9d754-470">The following example:</span></span>

* <span data-ttu-id="9d754-471">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-471">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="9d754-472">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-472">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="9d754-473">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="9d754-473">Types the value as an `int`.</span></span>
* <span data-ttu-id="9d754-474">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="9d754-474">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9d754-475">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="9d754-475">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9d754-476">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="9d754-476">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="9d754-477">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9d754-477">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="9d754-478">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-478">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="9d754-479">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-479">section0:key0</span></span>
* <span data-ttu-id="9d754-480">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-480">section0:key1</span></span>
* <span data-ttu-id="9d754-481">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-481">section1:key0</span></span>
* <span data-ttu-id="9d754-482">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-482">section1:key1</span></span>
* <span data-ttu-id="9d754-483">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-483">section2:subsection0:key0</span></span>
* <span data-ttu-id="9d754-484">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-484">section2:subsection0:key1</span></span>
* <span data-ttu-id="9d754-485">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="9d754-485">section2:subsection1:key0</span></span>
* <span data-ttu-id="9d754-486">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="9d754-486">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="9d754-487">GetSection</span><span class="sxs-lookup"><span data-stu-id="9d754-487">GetSection</span></span>

<span data-ttu-id="9d754-488">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-488">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span> <span data-ttu-id="9d754-489">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-489">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-490">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-490">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="9d754-491">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-491">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="9d754-492">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-492">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="9d754-493">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="9d754-493">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9d754-494">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-494">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9d754-495">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="9d754-495">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9d754-496"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-496">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="9d754-497">GetChildren</span><span class="sxs-lookup"><span data-stu-id="9d754-497">GetChildren</span></span>

<span data-ttu-id="9d754-498">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-498">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="9d754-499">既存</span><span class="sxs-lookup"><span data-stu-id="9d754-499">Exists</span></span>

<span data-ttu-id="9d754-500">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="9d754-500">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="9d754-501">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="9d754-501">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="9d754-502">クラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="9d754-502">Bind to a class</span></span>

<span data-ttu-id="9d754-503">"*オプション パターン*" を使用して、関連する設定のグループを表すクラスに構成をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-503">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="9d754-504">詳細については、<xref:fundamentals/configuration/options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-504">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9d754-505">構成値は文字列として返されますが、<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> を呼び出すことで [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) オブジェクトを構築できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-505">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="9d754-506">`Bind` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-506">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-507">サンプル アプリには `Starship` モデル (*Models/Starship.cs*) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-507">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="9d754-508">サンプル アプリが JSON 構成プロバイダーを使用して構成を読み込むときに、*starship.json* ファイルの `starship` セクションで構成が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-508">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="9d754-509">次の構成のキーと値のペアが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-509">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="9d754-510">キー</span><span class="sxs-lookup"><span data-stu-id="9d754-510">Key</span></span>                   | <span data-ttu-id="9d754-511">[値]</span><span class="sxs-lookup"><span data-stu-id="9d754-511">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="9d754-512">starship:name</span><span class="sxs-lookup"><span data-stu-id="9d754-512">starship:name</span></span>         | <span data-ttu-id="9d754-513">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="9d754-513">USS Enterprise</span></span>                                    |
| <span data-ttu-id="9d754-514">starship:registry</span><span class="sxs-lookup"><span data-stu-id="9d754-514">starship:registry</span></span>     | <span data-ttu-id="9d754-515">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="9d754-515">NCC-1701</span></span>                                          |
| <span data-ttu-id="9d754-516">starship:class</span><span class="sxs-lookup"><span data-stu-id="9d754-516">starship:class</span></span>        | <span data-ttu-id="9d754-517">Constitution</span><span class="sxs-lookup"><span data-stu-id="9d754-517">Constitution</span></span>                                      |
| <span data-ttu-id="9d754-518">starship:length</span><span class="sxs-lookup"><span data-stu-id="9d754-518">starship:length</span></span>       | <span data-ttu-id="9d754-519">304.8</span><span class="sxs-lookup"><span data-stu-id="9d754-519">304.8</span></span>                                             |
| <span data-ttu-id="9d754-520">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="9d754-520">starship:commissioned</span></span> | <span data-ttu-id="9d754-521">False</span><span class="sxs-lookup"><span data-stu-id="9d754-521">False</span></span>                                             |
| <span data-ttu-id="9d754-522">trademark</span><span class="sxs-lookup"><span data-stu-id="9d754-522">trademark</span></span>             | <span data-ttu-id="9d754-523">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="9d754-523">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="9d754-524">サンプル アプリは `starship` キーを使用して `GetSection` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9d754-524">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="9d754-525">`starship` のキーと値のペアは分離されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-525">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="9d754-526">`Starship` クラスのインスタンスを渡すサブセクションで、`Bind` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-526">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="9d754-527">インスタンスの値をバインドした後、インスタンスがレンダリング用のプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="9d754-527">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

<span data-ttu-id="9d754-528">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-528">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="9d754-529">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="9d754-529">Bind to an object graph</span></span>

<span data-ttu-id="9d754-530"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-530"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="9d754-531">`Bind` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-531">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-532">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9d754-532">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="9d754-533">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-533">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="9d754-534">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9d754-534">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="9d754-535">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="9d754-535">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="9d754-536">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) によってバインドされ、指定した型が返されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-536">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="9d754-537">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="9d754-537">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="9d754-538">次のコードは、前の例と共に `Get<T>` を使用する方法を示しています。これにより、バインドされたインスタンスを、表示用に使用するプロパティに直接割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="9d754-538">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

<span data-ttu-id="9d754-539"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-539"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="9d754-540">`Get<T>` は、ASP.NET Core 1.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-540">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="9d754-541">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-541">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9d754-542">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="9d754-542">Bind an array to a class</span></span>

<span data-ttu-id="9d754-543">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="9d754-543">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="9d754-544"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9d754-544">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9d754-545">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="9d754-545">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span> <span data-ttu-id="9d754-546">`Bind` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-546">\`Bind\`\` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

> [!NOTE]
> <span data-ttu-id="9d754-547">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-547">Binding is provided by convention.</span></span> <span data-ttu-id="9d754-548">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9d754-548">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="9d754-549">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="9d754-549">**In-memory array processing**</span></span>

<span data-ttu-id="9d754-550">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="9d754-550">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="9d754-551">キー</span><span class="sxs-lookup"><span data-stu-id="9d754-551">Key</span></span>             | <span data-ttu-id="9d754-552">[値]</span><span class="sxs-lookup"><span data-stu-id="9d754-552">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9d754-553">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="9d754-553">array:entries:0</span></span> | <span data-ttu-id="9d754-554">value0</span><span class="sxs-lookup"><span data-stu-id="9d754-554">value0</span></span> |
| <span data-ttu-id="9d754-555">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="9d754-555">array:entries:1</span></span> | <span data-ttu-id="9d754-556">value1</span><span class="sxs-lookup"><span data-stu-id="9d754-556">value1</span></span> |
| <span data-ttu-id="9d754-557">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="9d754-557">array:entries:2</span></span> | <span data-ttu-id="9d754-558">value2</span><span class="sxs-lookup"><span data-stu-id="9d754-558">value2</span></span> |
| <span data-ttu-id="9d754-559">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="9d754-559">array:entries:4</span></span> | <span data-ttu-id="9d754-560">value4</span><span class="sxs-lookup"><span data-stu-id="9d754-560">value4</span></span> |
| <span data-ttu-id="9d754-561">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="9d754-561">array:entries:5</span></span> | <span data-ttu-id="9d754-562">value5</span><span class="sxs-lookup"><span data-stu-id="9d754-562">value5</span></span> |

<span data-ttu-id="9d754-563">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-563">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

<span data-ttu-id="9d754-564">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="9d754-564">The array skips a value for index &num;3.</span></span> <span data-ttu-id="9d754-565">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="9d754-565">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="9d754-566">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-566">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="9d754-567">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9d754-567">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="9d754-568">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="9d754-568">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9d754-569">また、[ConfigurationBinder.Get&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用して、コードをよりコンパクトにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9d754-569">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="9d754-570">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9d754-570">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="9d754-571">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="9d754-571">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9d754-572">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="9d754-572">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9d754-573">0</span><span class="sxs-lookup"><span data-stu-id="9d754-573">0</span></span>                            | <span data-ttu-id="9d754-574">value0</span><span class="sxs-lookup"><span data-stu-id="9d754-574">value0</span></span>                       |
| <span data-ttu-id="9d754-575">1</span><span class="sxs-lookup"><span data-stu-id="9d754-575">1</span></span>                            | <span data-ttu-id="9d754-576">value1</span><span class="sxs-lookup"><span data-stu-id="9d754-576">value1</span></span>                       |
| <span data-ttu-id="9d754-577">2</span><span class="sxs-lookup"><span data-stu-id="9d754-577">2</span></span>                            | <span data-ttu-id="9d754-578">value2</span><span class="sxs-lookup"><span data-stu-id="9d754-578">value2</span></span>                       |
| <span data-ttu-id="9d754-579">3</span><span class="sxs-lookup"><span data-stu-id="9d754-579">3</span></span>                            | <span data-ttu-id="9d754-580">value4</span><span class="sxs-lookup"><span data-stu-id="9d754-580">value4</span></span>                       |
| <span data-ttu-id="9d754-581">4</span><span class="sxs-lookup"><span data-stu-id="9d754-581">4</span></span>                            | <span data-ttu-id="9d754-582">value5</span><span class="sxs-lookup"><span data-stu-id="9d754-582">value5</span></span>                       |

<span data-ttu-id="9d754-583">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-583">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9d754-584">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-584">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9d754-585">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="9d754-585">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9d754-586">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-586">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="9d754-587">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="9d754-587">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="9d754-588">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="9d754-588">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="9d754-589"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>の場合:</span><span class="sxs-lookup"><span data-stu-id="9d754-589">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="9d754-590">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-590">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="9d754-591">キー</span><span class="sxs-lookup"><span data-stu-id="9d754-591">Key</span></span>             | <span data-ttu-id="9d754-592">[値]</span><span class="sxs-lookup"><span data-stu-id="9d754-592">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9d754-593">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="9d754-593">array:entries:3</span></span> | <span data-ttu-id="9d754-594">value3</span><span class="sxs-lookup"><span data-stu-id="9d754-594">value3</span></span> |

<span data-ttu-id="9d754-595">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9d754-595">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="9d754-596">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="9d754-596">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9d754-597">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="9d754-597">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9d754-598">0</span><span class="sxs-lookup"><span data-stu-id="9d754-598">0</span></span>                            | <span data-ttu-id="9d754-599">value0</span><span class="sxs-lookup"><span data-stu-id="9d754-599">value0</span></span>                       |
| <span data-ttu-id="9d754-600">1</span><span class="sxs-lookup"><span data-stu-id="9d754-600">1</span></span>                            | <span data-ttu-id="9d754-601">value1</span><span class="sxs-lookup"><span data-stu-id="9d754-601">value1</span></span>                       |
| <span data-ttu-id="9d754-602">2</span><span class="sxs-lookup"><span data-stu-id="9d754-602">2</span></span>                            | <span data-ttu-id="9d754-603">value2</span><span class="sxs-lookup"><span data-stu-id="9d754-603">value2</span></span>                       |
| <span data-ttu-id="9d754-604">3</span><span class="sxs-lookup"><span data-stu-id="9d754-604">3</span></span>                            | <span data-ttu-id="9d754-605">value3</span><span class="sxs-lookup"><span data-stu-id="9d754-605">value3</span></span>                       |
| <span data-ttu-id="9d754-606">4</span><span class="sxs-lookup"><span data-stu-id="9d754-606">4</span></span>                            | <span data-ttu-id="9d754-607">value4</span><span class="sxs-lookup"><span data-stu-id="9d754-607">value4</span></span>                       |
| <span data-ttu-id="9d754-608">5</span><span class="sxs-lookup"><span data-stu-id="9d754-608">5</span></span>                            | <span data-ttu-id="9d754-609">value5</span><span class="sxs-lookup"><span data-stu-id="9d754-609">value5</span></span>                       |

<span data-ttu-id="9d754-610">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="9d754-610">**JSON array processing**</span></span>

<span data-ttu-id="9d754-611">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-611">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="9d754-612">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="9d754-612">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="9d754-613">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9d754-613">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="9d754-614">キー</span><span class="sxs-lookup"><span data-stu-id="9d754-614">Key</span></span>                     | <span data-ttu-id="9d754-615">[値]</span><span class="sxs-lookup"><span data-stu-id="9d754-615">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="9d754-616">json_array:key</span><span class="sxs-lookup"><span data-stu-id="9d754-616">json_array:key</span></span>          | <span data-ttu-id="9d754-617">valueA</span><span class="sxs-lookup"><span data-stu-id="9d754-617">valueA</span></span> |
| <span data-ttu-id="9d754-618">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="9d754-618">json_array:subsection:0</span></span> | <span data-ttu-id="9d754-619">valueB</span><span class="sxs-lookup"><span data-stu-id="9d754-619">valueB</span></span> |
| <span data-ttu-id="9d754-620">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="9d754-620">json_array:subsection:1</span></span> | <span data-ttu-id="9d754-621">valueC</span><span class="sxs-lookup"><span data-stu-id="9d754-621">valueC</span></span> |
| <span data-ttu-id="9d754-622">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="9d754-622">json_array:subsection:2</span></span> | <span data-ttu-id="9d754-623">valueD</span><span class="sxs-lookup"><span data-stu-id="9d754-623">valueD</span></span> |

<span data-ttu-id="9d754-624">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-624">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="9d754-625">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="9d754-625">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="9d754-626">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-626">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="9d754-627">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="9d754-627">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="9d754-628">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="9d754-628">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="9d754-629">0</span><span class="sxs-lookup"><span data-stu-id="9d754-629">0</span></span>                                   | <span data-ttu-id="9d754-630">valueB</span><span class="sxs-lookup"><span data-stu-id="9d754-630">valueB</span></span>                              |
| <span data-ttu-id="9d754-631">1</span><span class="sxs-lookup"><span data-stu-id="9d754-631">1</span></span>                                   | <span data-ttu-id="9d754-632">valueC</span><span class="sxs-lookup"><span data-stu-id="9d754-632">valueC</span></span>                              |
| <span data-ttu-id="9d754-633">2</span><span class="sxs-lookup"><span data-stu-id="9d754-633">2</span></span>                                   | <span data-ttu-id="9d754-634">valueD</span><span class="sxs-lookup"><span data-stu-id="9d754-634">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="9d754-635">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9d754-635">Custom configuration provider</span></span>

<span data-ttu-id="9d754-636">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9d754-636">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9d754-637">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9d754-637">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9d754-638">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9d754-638">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9d754-639">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="9d754-639">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9d754-640">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9d754-640">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9d754-641">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="9d754-641">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9d754-642">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="9d754-642">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9d754-643">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="9d754-643">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="9d754-644">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="9d754-644">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9d754-645">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="9d754-645">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9d754-646">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="9d754-646">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9d754-647"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d754-647">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9d754-648">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="9d754-648">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9d754-649"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9d754-649">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9d754-650">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="9d754-650">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="9d754-651">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="9d754-651">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9d754-652">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="9d754-652">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9d754-653">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="9d754-653">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9d754-654">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9d754-654">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="9d754-655">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="9d754-655">Access configuration during startup</span></span>

<span data-ttu-id="9d754-656">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="9d754-656">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9d754-657">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9d754-657">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="9d754-658">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9d754-658">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="9d754-659">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="9d754-659">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="9d754-660">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="9d754-660">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="9d754-661">Razor ページ:</span><span class="sxs-lookup"><span data-stu-id="9d754-661">In a Razor Pages page:</span></span>

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

<span data-ttu-id="9d754-662">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="9d754-662">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9d754-663">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="9d754-663">Add configuration from an external assembly</span></span>

<span data-ttu-id="9d754-664"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="9d754-664">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9d754-665">詳細については、<xref:fundamentals/configuration/platform-specific-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9d754-665">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d754-666">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9d754-666">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* [<span data-ttu-id="9d754-667">Microsoft の構成について詳しく調べる</span><span class="sxs-lookup"><span data-stu-id="9d754-667">Deep Dive into Microsoft Configuration</span></span>](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)
