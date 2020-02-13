---
title: ASP.NET Core の構成
author: guardrex
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: d0ef670aa0ac4960318f86ea7888b9eab71f17fd
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171895"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="9e4f6-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="9e4f6-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="9e4f6-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9e4f6-105">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="9e4f6-106">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="9e4f6-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9e4f6-107">Azure Key Vault</span></span>
* <span data-ttu-id="9e4f6-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9e4f6-108">Azure App Configuration</span></span>
* <span data-ttu-id="9e4f6-109">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-109">Command-line arguments</span></span>
* <span data-ttu-id="9e4f6-110">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="9e4f6-111">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="9e4f6-111">Directory files</span></span>
* <span data-ttu-id="9e4f6-112">環境変数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-112">Environment variables</span></span>
* <span data-ttu-id="9e4f6-113">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="9e4f6-113">In-memory .NET objects</span></span>
* <span data-ttu-id="9e4f6-114">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="9e4f6-114">Settings files</span></span>

<span data-ttu-id="9e4f6-115">一般的な構成プロバイダーのシナリオの構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、フレームワークによって暗黙的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

<span data-ttu-id="9e4f6-116">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-116">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="9e4f6-117">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-117">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="9e4f6-118">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-118">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="9e4f6-119">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-119">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9e4f6-120">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-120">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9e4f6-121">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="9e4f6-121">Host versus app configuration</span></span>

<span data-ttu-id="9e4f6-122">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-122">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9e4f6-123">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-123">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9e4f6-124">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-124">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9e4f6-125">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-125">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="9e4f6-126">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-126">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="9e4f6-127">その他の構成</span><span class="sxs-lookup"><span data-stu-id="9e4f6-127">Other configuration</span></span>

<span data-ttu-id="9e4f6-128">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-128">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="9e4f6-129">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-129">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="9e4f6-130">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-130">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="9e4f6-131"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="9e4f6-131">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="9e4f6-132">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-132">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="9e4f6-133">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-133">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="9e4f6-134">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-134">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="9e4f6-135">既定の構成</span><span class="sxs-lookup"><span data-stu-id="9e4f6-135">Default configuration</span></span>

<span data-ttu-id="9e4f6-136">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-136">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="9e4f6-137">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-137">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="9e4f6-138">[汎用ホスト](xref:fundamentals/host/generic-host)を使用するアプリに以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-138">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="9e4f6-139">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-139">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="9e4f6-140">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-140">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9e4f6-141">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-141">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9e4f6-142">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-142">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9e4f6-143">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-143">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9e4f6-144">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-144">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="9e4f6-145">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-145">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="9e4f6-146">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-146">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="9e4f6-147">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-147">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="9e4f6-148">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-148">Enable IIS integration.</span></span>
* <span data-ttu-id="9e4f6-149">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-149">App configuration is provided from:</span></span>
  * <span data-ttu-id="9e4f6-150">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-150">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9e4f6-151">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-151">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9e4f6-152">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-152">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="9e4f6-153">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-153">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="9e4f6-154">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-154">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="9e4f6-155">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="9e4f6-155">Security</span></span>

<span data-ttu-id="9e4f6-156">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-156">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="9e4f6-157">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-157">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="9e4f6-158">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-158">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9e4f6-159">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-159">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9e4f6-160">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-160">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="9e4f6-161"><xref:security/app-secrets> &ndash; 環境変数を使用して機密データを格納する場合に関するアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-161"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="9e4f6-162">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-162">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="9e4f6-163">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-163">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="9e4f6-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="9e4f6-165">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-165">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9e4f6-166">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="9e4f6-166">Hierarchical configuration data</span></span>

<span data-ttu-id="9e4f6-167">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-167">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9e4f6-168">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-168">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="9e4f6-169">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-169">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="9e4f6-170">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-170">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="9e4f6-171">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-171">section0:key0</span></span>
* <span data-ttu-id="9e4f6-172">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-172">section0:key1</span></span>
* <span data-ttu-id="9e4f6-173">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-173">section1:key0</span></span>
* <span data-ttu-id="9e4f6-174">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-174">section1:key1</span></span>

<span data-ttu-id="9e4f6-175"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-175"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9e4f6-176">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-176">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="9e4f6-177">規約</span><span class="sxs-lookup"><span data-stu-id="9e4f6-177">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="9e4f6-178">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-178">Sources and providers</span></span>

<span data-ttu-id="9e4f6-179">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-179">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="9e4f6-180">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-180">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="9e4f6-181">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-181">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="9e4f6-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9e4f6-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="9e4f6-184">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-184">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="9e4f6-185">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-185">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="9e4f6-186">キー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-186">Keys</span></span>

<span data-ttu-id="9e4f6-187">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-187">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="9e4f6-188">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-188">Keys are case-insensitive.</span></span> <span data-ttu-id="9e4f6-189">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-189">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9e4f6-190">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-190">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="9e4f6-191">階層キー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-191">Hierarchical keys</span></span>
  * <span data-ttu-id="9e4f6-192">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-192">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9e4f6-193">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-193">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9e4f6-194">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-194">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="9e4f6-195">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-195">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9e4f6-196">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-196">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9e4f6-197"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-197">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9e4f6-198">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-198">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="9e4f6-199">値</span><span class="sxs-lookup"><span data-stu-id="9e4f6-199">Values</span></span>

<span data-ttu-id="9e4f6-200">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-200">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="9e4f6-201">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-201">Values are strings.</span></span>
* <span data-ttu-id="9e4f6-202">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-202">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="9e4f6-203">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-203">Providers</span></span>

<span data-ttu-id="9e4f6-204">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-204">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9e4f6-205">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-205">Provider</span></span> | <span data-ttu-id="9e4f6-206">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-206">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="9e4f6-207">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-207">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="9e4f6-208">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9e4f6-208">Azure Key Vault</span></span> |
| <span data-ttu-id="9e4f6-209">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-209">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="9e4f6-210">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9e4f6-210">Azure App Configuration</span></span> |
| [<span data-ttu-id="9e4f6-211">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-211">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="9e4f6-212">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="9e4f6-212">Command-line parameters</span></span> |
| [<span data-ttu-id="9e4f6-213">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-213">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="9e4f6-214">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="9e4f6-214">Custom source</span></span> |
| [<span data-ttu-id="9e4f6-215">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-215">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="9e4f6-216">環境変数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-216">Environment variables</span></span> |
| [<span data-ttu-id="9e4f6-217">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-217">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="9e4f6-218">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-218">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="9e4f6-219">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-219">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="9e4f6-220">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="9e4f6-220">Directory files</span></span> |
| [<span data-ttu-id="9e4f6-221">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-221">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="9e4f6-222">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="9e4f6-222">In-memory collections</span></span> |
| <span data-ttu-id="9e4f6-223">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-223">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="9e4f6-224">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="9e4f6-224">File in the user profile directory</span></span> |

<span data-ttu-id="9e4f6-225">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-225">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="9e4f6-226">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-226">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="9e4f6-227">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-227">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="9e4f6-228">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-228">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9e4f6-229">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-229">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="9e4f6-230">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9e4f6-230">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="9e4f6-231">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-231">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="9e4f6-232">環境変数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-232">Environment variables</span></span>
1. <span data-ttu-id="9e4f6-233">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-233">Command-line arguments</span></span>

<span data-ttu-id="9e4f6-234">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-234">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9e4f6-235">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-235">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9e4f6-236">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-236">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="9e4f6-237">ConfigureHostConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="9e4f6-237">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="9e4f6-238">ホスト ビルダーを構成するには、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出し、構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-238">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="9e4f6-239">`ConfigureHostConfiguration` は、後でビルド プロセスに使用する <xref:Microsoft.Extensions.Hosting.IHostEnvironment> を初期化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-239">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="9e4f6-240">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-240">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

## <a name="configureappconfiguration"></a><span data-ttu-id="9e4f6-241">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9e4f6-241">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9e4f6-242">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-242">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="9e4f6-243">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-243">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="9e4f6-244">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-244">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="9e4f6-245">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="9e4f6-245">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="9e4f6-246">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-246">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="9e4f6-247">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="9e4f6-247">Consume configuration during app startup</span></span>

<span data-ttu-id="9e4f6-248">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-248">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9e4f6-249">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-249">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="9e4f6-250">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-250">Command-line Configuration Provider</span></span>

<span data-ttu-id="9e4f6-251"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-251">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="9e4f6-252">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-252">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-253">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-253">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="9e4f6-254">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-254">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9e4f6-255">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-255">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9e4f6-256">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-256">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9e4f6-257">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-257">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9e4f6-258">環境変数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-258">Environment variables.</span></span>

<span data-ttu-id="9e4f6-259">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-259">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="9e4f6-260">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-260">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="9e4f6-261">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-261">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="9e4f6-262">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-262">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="9e4f6-263">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-263">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9e4f6-264">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-264">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="9e4f6-265">**例**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-265">**Example**</span></span>

<span data-ttu-id="9e4f6-266">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-266">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="9e4f6-267">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-267">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="9e4f6-268">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-268">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="9e4f6-269">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-269">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9e4f6-270">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-270">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="9e4f6-271">引数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-271">Arguments</span></span>

<span data-ttu-id="9e4f6-272">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-272">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="9e4f6-273">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-273">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="9e4f6-274">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-274">Key prefix</span></span>               | <span data-ttu-id="9e4f6-275">例</span><span class="sxs-lookup"><span data-stu-id="9e4f6-275">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="9e4f6-276">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="9e4f6-276">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="9e4f6-277">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-277">Two dashes (`--`)</span></span>        | <span data-ttu-id="9e4f6-278">`--CommandLineKey2=value2`、`--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="9e4f6-279">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-279">Forward slash (`/`)</span></span>      | <span data-ttu-id="9e4f6-280">`/CommandLineKey3=value3`、`/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="9e4f6-281">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-281">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="9e4f6-282">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-282">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="9e4f6-283">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="9e4f6-283">Switch mappings</span></span>

<span data-ttu-id="9e4f6-284">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-284">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="9e4f6-285"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-285">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9e4f6-286">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-286">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9e4f6-287">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-287">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9e4f6-288">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-288">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9e4f6-289">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-289">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9e4f6-290">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-290">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="9e4f6-291">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-291">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9e4f6-292">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-292">Create a switch mappings dictionary.</span></span> <span data-ttu-id="9e4f6-293">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-293">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="9e4f6-294">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-294">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="9e4f6-295">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-295">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="9e4f6-296">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-296">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9e4f6-297">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-297">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="9e4f6-298">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-298">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="9e4f6-299">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-299">Key</span></span>       | <span data-ttu-id="9e4f6-300">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-300">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="9e4f6-301">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-301">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="9e4f6-302">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-302">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="9e4f6-303">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-303">Key</span></span>               | <span data-ttu-id="9e4f6-304">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-304">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="9e4f6-305">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-305">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="9e4f6-306"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-306">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="9e4f6-307">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-307">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9e4f6-308">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-308">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="9e4f6-309">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-309">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="9e4f6-310">新しいホスト ビルダーが[汎用ホスト](xref:fundamentals/host/generic-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `DOTNET_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-310">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="9e4f6-311">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-311">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9e4f6-312">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-312">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9e4f6-313">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-313">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="9e4f6-314">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-314">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9e4f6-315">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-315">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9e4f6-316">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-316">Command-line arguments.</span></span>

<span data-ttu-id="9e4f6-317">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-317">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="9e4f6-318">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-318">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="9e4f6-319">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-319">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="9e4f6-320">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-320">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="9e4f6-321">**例**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-321">**Example**</span></span>

<span data-ttu-id="9e4f6-322">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-322">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="9e4f6-323">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-323">Run the sample app.</span></span> <span data-ttu-id="9e4f6-324">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-324">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9e4f6-325">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-325">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="9e4f6-326">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-326">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="9e4f6-327">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-327">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="9e4f6-328">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-328">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="9e4f6-329">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-329">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="9e4f6-330">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-330">Prefixes</span></span>

<span data-ttu-id="9e4f6-331">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-331">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="9e4f6-332">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-332">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="9e4f6-333">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-333">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="9e4f6-334">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-334">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="9e4f6-335">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-335">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9e4f6-336">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-336">**Connection string prefixes**</span></span>

<span data-ttu-id="9e4f6-337">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-337">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9e4f6-338">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-338">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9e4f6-339">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-339">Connection string prefix</span></span> | <span data-ttu-id="9e4f6-340">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-340">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="9e4f6-341">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-341">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="9e4f6-342">MySQL</span><span class="sxs-lookup"><span data-stu-id="9e4f6-342">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="9e4f6-343">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="9e4f6-343">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="9e4f6-344">SQL Server</span><span class="sxs-lookup"><span data-stu-id="9e4f6-344">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="9e4f6-345">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-345">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9e4f6-346">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-346">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9e4f6-347">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-347">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9e4f6-348">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-348">Environment variable key</span></span> | <span data-ttu-id="9e4f6-349">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-349">Converted configuration key</span></span> | <span data-ttu-id="9e4f6-350">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="9e4f6-350">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9e4f6-351">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-351">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9e4f6-352">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-352">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9e4f6-353">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-353">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9e4f6-354">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-354">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9e4f6-355">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-355">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9e4f6-356">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-356">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9e4f6-357">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-357">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="9e4f6-358">**例**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-358">**Example**</span></span>

<span data-ttu-id="9e4f6-359">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-359">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="9e4f6-360">名前 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-360">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="9e4f6-361">数値 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-361">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="9e4f6-362">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-362">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="9e4f6-363">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-363">File Configuration Provider</span></span>

<span data-ttu-id="9e4f6-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9e4f6-365">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-365">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="9e4f6-366">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-366">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9e4f6-367">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-367">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="9e4f6-368">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-368">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9e4f6-369">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-369">INI Configuration Provider</span></span>

<span data-ttu-id="9e4f6-370"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-370">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9e4f6-371">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-371">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-372">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-372">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="9e4f6-373">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-373">Overloads permit specifying:</span></span>

* <span data-ttu-id="9e4f6-374">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-374">Whether the file is optional.</span></span>
* <span data-ttu-id="9e4f6-375">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-375">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9e4f6-376">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-376">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9e4f6-377">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-377">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9e4f6-378">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-378">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="9e4f6-379">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-379">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9e4f6-380">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-380">section0:key0</span></span>
* <span data-ttu-id="9e4f6-381">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-381">section0:key1</span></span>
* <span data-ttu-id="9e4f6-382">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-382">section1:subsection:key</span></span>
* <span data-ttu-id="9e4f6-383">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-383">section2:subsection0:key</span></span>
* <span data-ttu-id="9e4f6-384">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-384">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="9e4f6-385">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-385">JSON Configuration Provider</span></span>

<span data-ttu-id="9e4f6-386"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-386">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="9e4f6-387">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-387">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-388">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-388">Overloads permit specifying:</span></span>

* <span data-ttu-id="9e4f6-389">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-389">Whether the file is optional.</span></span>
* <span data-ttu-id="9e4f6-390">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-390">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9e4f6-391">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-391">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9e4f6-392">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-392">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9e4f6-393">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-393">The method is called to load configuration from:</span></span>

* <span data-ttu-id="9e4f6-394">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-394">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="9e4f6-395">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-395">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="9e4f6-396">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-396">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="9e4f6-397">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-397">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9e4f6-398">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-398">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9e4f6-399">環境変数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-399">Environment variables.</span></span>
* <span data-ttu-id="9e4f6-400">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-400">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9e4f6-401">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-401">Command-line arguments.</span></span>

<span data-ttu-id="9e4f6-402">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-402">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="9e4f6-403">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-403">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="9e4f6-404">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-404">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9e4f6-405">**例**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-405">**Example**</span></span>

<span data-ttu-id="9e4f6-406">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-406">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="9e4f6-407">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-407">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="9e4f6-408">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-408">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="9e4f6-409">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-409">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="9e4f6-410">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-410">Run the sample app.</span></span> <span data-ttu-id="9e4f6-411">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-411">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9e4f6-412">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-412">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="9e4f6-413">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-413">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="9e4f6-414">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-414">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="9e4f6-415">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-415">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="9e4f6-416">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-416">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="9e4f6-417">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-417">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="9e4f6-418">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-418">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="9e4f6-419">キー `Logging:LogLevel:Default` のログ レベルは `Information` です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-419">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="9e4f6-420">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-420">XML Configuration Provider</span></span>

<span data-ttu-id="9e4f6-421"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-421">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9e4f6-422">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-422">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-423">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-423">Overloads permit specifying:</span></span>

* <span data-ttu-id="9e4f6-424">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-424">Whether the file is optional.</span></span>
* <span data-ttu-id="9e4f6-425">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-425">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9e4f6-426">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-426">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9e4f6-427">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-427">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="9e4f6-428">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-428">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="9e4f6-429">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-429">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9e4f6-430">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-430">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="9e4f6-431">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-431">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9e4f6-432">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-432">section0:key0</span></span>
* <span data-ttu-id="9e4f6-433">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-433">section0:key1</span></span>
* <span data-ttu-id="9e4f6-434">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-434">section1:key0</span></span>
* <span data-ttu-id="9e4f6-435">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-435">section1:key1</span></span>

<span data-ttu-id="9e4f6-436">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-436">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="9e4f6-437">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-437">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9e4f6-438">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-438">section:section0:key:key0</span></span>
* <span data-ttu-id="9e4f6-439">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-439">section:section0:key:key1</span></span>
* <span data-ttu-id="9e4f6-440">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-440">section:section1:key:key0</span></span>
* <span data-ttu-id="9e4f6-441">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-441">section:section1:key:key1</span></span>

<span data-ttu-id="9e4f6-442">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-442">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9e4f6-443">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-443">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9e4f6-444">key:attribute</span><span class="sxs-lookup"><span data-stu-id="9e4f6-444">key:attribute</span></span>
* <span data-ttu-id="9e4f6-445">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="9e4f6-445">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9e4f6-446">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-446">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="9e4f6-447"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-447">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9e4f6-448">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-448">The key is the file name.</span></span> <span data-ttu-id="9e4f6-449">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-449">The value contains the file's contents.</span></span> <span data-ttu-id="9e4f6-450">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-450">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9e4f6-451">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-451">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9e4f6-452">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-452">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9e4f6-453">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-453">Overloads permit specifying:</span></span>

* <span data-ttu-id="9e4f6-454">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-454">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9e4f6-455">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-455">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9e4f6-456">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-456">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9e4f6-457">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-457">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9e4f6-458">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-458">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="9e4f6-459">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-459">Memory Configuration Provider</span></span>

<span data-ttu-id="9e4f6-460"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9e4f6-461">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-462">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="9e4f6-463">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-463">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="9e4f6-464">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-464">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="9e4f6-465">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-465">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="9e4f6-466">GetValue</span><span class="sxs-lookup"><span data-stu-id="9e4f6-466">GetValue</span></span>

<span data-ttu-id="9e4f6-467">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) によって、指定したキーで構成から単一の値が抽出され、それが指定したコレクション以外の型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-467">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="9e4f6-468">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-468">An overload accepts a default value.</span></span>

<span data-ttu-id="9e4f6-469">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-469">The following example:</span></span>

* <span data-ttu-id="9e4f6-470">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-470">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="9e4f6-471">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-471">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="9e4f6-472">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-472">Types the value as an `int`.</span></span>
* <span data-ttu-id="9e4f6-473">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-473">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9e4f6-474">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="9e4f6-474">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9e4f6-475">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-475">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="9e4f6-476">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-476">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="9e4f6-477">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-477">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="9e4f6-478">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-478">section0:key0</span></span>
* <span data-ttu-id="9e4f6-479">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-479">section0:key1</span></span>
* <span data-ttu-id="9e4f6-480">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-480">section1:key0</span></span>
* <span data-ttu-id="9e4f6-481">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-481">section1:key1</span></span>
* <span data-ttu-id="9e4f6-482">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-482">section2:subsection0:key0</span></span>
* <span data-ttu-id="9e4f6-483">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-483">section2:subsection0:key1</span></span>
* <span data-ttu-id="9e4f6-484">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-484">section2:subsection1:key0</span></span>
* <span data-ttu-id="9e4f6-485">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-485">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="9e4f6-486">GetSection</span><span class="sxs-lookup"><span data-stu-id="9e4f6-486">GetSection</span></span>

<span data-ttu-id="9e4f6-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="9e4f6-488">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="9e4f6-489">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="9e4f6-490">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="9e4f6-491">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9e4f6-492">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9e4f6-493">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9e4f6-494"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="9e4f6-495">GetChildren</span><span class="sxs-lookup"><span data-stu-id="9e4f6-495">GetChildren</span></span>

<span data-ttu-id="9e4f6-496">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="9e4f6-497">既存</span><span class="sxs-lookup"><span data-stu-id="9e4f6-497">Exists</span></span>

<span data-ttu-id="9e4f6-498">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="9e4f6-499">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="9e4f6-500">クラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-500">Bind to a class</span></span>

<span data-ttu-id="9e4f6-501">"*オプション パターン*" を使用して、関連する設定のグループを表すクラスに構成をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="9e4f6-502">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9e4f6-503">構成値は文字列として返されますが、<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> を呼び出すことで [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) オブジェクトを構築できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="9e4f6-504">バインダーは、指定された型のすべてのパブリックな読み取り/書き込みプロパティに値をバインドします。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-504">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="9e4f6-505">フィールドはバインド**されません**。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-505">Fields are **not** bound.</span></span>

<span data-ttu-id="9e4f6-506">サンプル アプリには `Starship` モデル (*Models/Starship.cs*) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-506">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="9e4f6-507">サンプル アプリが JSON 構成プロバイダーを使用して構成を読み込むときに、*starship.json* ファイルの `starship` セクションで構成が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-507">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

<span data-ttu-id="9e4f6-508">次の構成のキーと値のペアが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-508">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="9e4f6-509">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-509">Key</span></span>                   | <span data-ttu-id="9e4f6-510">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-510">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="9e4f6-511">starship:name</span><span class="sxs-lookup"><span data-stu-id="9e4f6-511">starship:name</span></span>         | <span data-ttu-id="9e4f6-512">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="9e4f6-512">USS Enterprise</span></span>                                    |
| <span data-ttu-id="9e4f6-513">starship:registry</span><span class="sxs-lookup"><span data-stu-id="9e4f6-513">starship:registry</span></span>     | <span data-ttu-id="9e4f6-514">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="9e4f6-514">NCC-1701</span></span>                                          |
| <span data-ttu-id="9e4f6-515">starship:class</span><span class="sxs-lookup"><span data-stu-id="9e4f6-515">starship:class</span></span>        | <span data-ttu-id="9e4f6-516">Constitution</span><span class="sxs-lookup"><span data-stu-id="9e4f6-516">Constitution</span></span>                                      |
| <span data-ttu-id="9e4f6-517">starship:length</span><span class="sxs-lookup"><span data-stu-id="9e4f6-517">starship:length</span></span>       | <span data-ttu-id="9e4f6-518">304.8</span><span class="sxs-lookup"><span data-stu-id="9e4f6-518">304.8</span></span>                                             |
| <span data-ttu-id="9e4f6-519">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="9e4f6-519">starship:commissioned</span></span> | <span data-ttu-id="9e4f6-520">False</span><span class="sxs-lookup"><span data-stu-id="9e4f6-520">False</span></span>                                             |
| <span data-ttu-id="9e4f6-521">trademark</span><span class="sxs-lookup"><span data-stu-id="9e4f6-521">trademark</span></span>             | <span data-ttu-id="9e4f6-522">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="9e4f6-522">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="9e4f6-523">サンプル アプリは `starship` キーを使用して `GetSection` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-523">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="9e4f6-524">`starship` のキーと値のペアは分離されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-524">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="9e4f6-525">`Starship` クラスのインスタンスを渡すサブセクションで、`Bind` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-525">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="9e4f6-526">インスタンスの値をバインドした後、インスタンスがレンダリング用のプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-526">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="9e4f6-527">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-527">Bind to an object graph</span></span>

<span data-ttu-id="9e4f6-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="9e4f6-529">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-529">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="9e4f6-530">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="9e4f6-531">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="9e4f6-532">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="9e4f6-533">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="9e4f6-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) によってバインドされ、指定した型が返されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="9e4f6-535">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="9e4f6-536">次のコードは、前の例と共に `Get<T>` を使用する方法を示しています。これにより、バインドされたインスタンスを、表示用に使用するプロパティに直接割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9e4f6-537">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-537">Bind an array to a class</span></span>

<span data-ttu-id="9e4f6-538">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="9e4f6-538">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="9e4f6-539"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-539">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9e4f6-540">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-540">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="9e4f6-541">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-541">Binding is provided by convention.</span></span> <span data-ttu-id="9e4f6-542">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-542">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="9e4f6-543">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-543">**In-memory array processing**</span></span>

<span data-ttu-id="9e4f6-544">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-544">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="9e4f6-545">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-545">Key</span></span>             | <span data-ttu-id="9e4f6-546">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-546">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9e4f6-547">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-547">array:entries:0</span></span> | <span data-ttu-id="9e4f6-548">value0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-548">value0</span></span> |
| <span data-ttu-id="9e4f6-549">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-549">array:entries:1</span></span> | <span data-ttu-id="9e4f6-550">value1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-550">value1</span></span> |
| <span data-ttu-id="9e4f6-551">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-551">array:entries:2</span></span> | <span data-ttu-id="9e4f6-552">value2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-552">value2</span></span> |
| <span data-ttu-id="9e4f6-553">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-553">array:entries:4</span></span> | <span data-ttu-id="9e4f6-554">value4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-554">value4</span></span> |
| <span data-ttu-id="9e4f6-555">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-555">array:entries:5</span></span> | <span data-ttu-id="9e4f6-556">value5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-556">value5</span></span> |

<span data-ttu-id="9e4f6-557">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-557">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="9e4f6-558">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-558">The array skips a value for index &num;3.</span></span> <span data-ttu-id="9e4f6-559">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-559">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="9e4f6-560">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-560">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="9e4f6-561">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-561">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="9e4f6-562">また、[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用して、コードをよりコンパクトにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-562">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="9e4f6-563">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-563">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="9e4f6-564">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-564">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9e4f6-565">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="9e4f6-565">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9e4f6-566">0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-566">0</span></span>                            | <span data-ttu-id="9e4f6-567">value0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-567">value0</span></span>                       |
| <span data-ttu-id="9e4f6-568">1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-568">1</span></span>                            | <span data-ttu-id="9e4f6-569">value1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-569">value1</span></span>                       |
| <span data-ttu-id="9e4f6-570">2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-570">2</span></span>                            | <span data-ttu-id="9e4f6-571">value2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-571">value2</span></span>                       |
| <span data-ttu-id="9e4f6-572">3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-572">3</span></span>                            | <span data-ttu-id="9e4f6-573">value4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-573">value4</span></span>                       |
| <span data-ttu-id="9e4f6-574">4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-574">4</span></span>                            | <span data-ttu-id="9e4f6-575">value5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-575">value5</span></span>                       |

<span data-ttu-id="9e4f6-576">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-576">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9e4f6-577">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-577">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9e4f6-578">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-578">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9e4f6-579">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-579">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="9e4f6-580">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-580">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="9e4f6-581">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-581">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="9e4f6-582">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-582">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="9e4f6-583">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-583">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="9e4f6-584">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-584">Key</span></span>             | <span data-ttu-id="9e4f6-585">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-585">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9e4f6-586">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-586">array:entries:3</span></span> | <span data-ttu-id="9e4f6-587">value3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-587">value3</span></span> |

<span data-ttu-id="9e4f6-588">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-588">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="9e4f6-589">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-589">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9e4f6-590">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="9e4f6-590">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9e4f6-591">0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-591">0</span></span>                            | <span data-ttu-id="9e4f6-592">value0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-592">value0</span></span>                       |
| <span data-ttu-id="9e4f6-593">1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-593">1</span></span>                            | <span data-ttu-id="9e4f6-594">value1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-594">value1</span></span>                       |
| <span data-ttu-id="9e4f6-595">2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-595">2</span></span>                            | <span data-ttu-id="9e4f6-596">value2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-596">value2</span></span>                       |
| <span data-ttu-id="9e4f6-597">3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-597">3</span></span>                            | <span data-ttu-id="9e4f6-598">value3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-598">value3</span></span>                       |
| <span data-ttu-id="9e4f6-599">4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-599">4</span></span>                            | <span data-ttu-id="9e4f6-600">value4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-600">value4</span></span>                       |
| <span data-ttu-id="9e4f6-601">5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-601">5</span></span>                            | <span data-ttu-id="9e4f6-602">value5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-602">value5</span></span>                       |

<span data-ttu-id="9e4f6-603">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-603">**JSON array processing**</span></span>

<span data-ttu-id="9e4f6-604">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-604">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="9e4f6-605">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-605">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="9e4f6-606">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-606">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="9e4f6-607">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-607">Key</span></span>                     | <span data-ttu-id="9e4f6-608">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-608">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="9e4f6-609">json_array:key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-609">json_array:key</span></span>          | <span data-ttu-id="9e4f6-610">valueA</span><span class="sxs-lookup"><span data-stu-id="9e4f6-610">valueA</span></span> |
| <span data-ttu-id="9e4f6-611">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-611">json_array:subsection:0</span></span> | <span data-ttu-id="9e4f6-612">valueB</span><span class="sxs-lookup"><span data-stu-id="9e4f6-612">valueB</span></span> |
| <span data-ttu-id="9e4f6-613">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-613">json_array:subsection:1</span></span> | <span data-ttu-id="9e4f6-614">valueC</span><span class="sxs-lookup"><span data-stu-id="9e4f6-614">valueC</span></span> |
| <span data-ttu-id="9e4f6-615">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-615">json_array:subsection:2</span></span> | <span data-ttu-id="9e4f6-616">valueD</span><span class="sxs-lookup"><span data-stu-id="9e4f6-616">valueD</span></span> |

<span data-ttu-id="9e4f6-617">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-617">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="9e4f6-618">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-618">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="9e4f6-619">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-619">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="9e4f6-620">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-620">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="9e4f6-621">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="9e4f6-621">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="9e4f6-622">0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-622">0</span></span>                                   | <span data-ttu-id="9e4f6-623">valueB</span><span class="sxs-lookup"><span data-stu-id="9e4f6-623">valueB</span></span>                              |
| <span data-ttu-id="9e4f6-624">1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-624">1</span></span>                                   | <span data-ttu-id="9e4f6-625">valueC</span><span class="sxs-lookup"><span data-stu-id="9e4f6-625">valueC</span></span>                              |
| <span data-ttu-id="9e4f6-626">2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-626">2</span></span>                                   | <span data-ttu-id="9e4f6-627">valueD</span><span class="sxs-lookup"><span data-stu-id="9e4f6-627">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="9e4f6-628">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-628">Custom configuration provider</span></span>

<span data-ttu-id="9e4f6-629">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-629">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9e4f6-630">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-630">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9e4f6-631">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-631">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9e4f6-632">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-632">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9e4f6-633">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-633">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9e4f6-634">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-634">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9e4f6-635">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-635">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9e4f6-636">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-636">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="9e4f6-637">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-637">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9e4f6-638">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-638">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9e4f6-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9e4f6-640"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-640">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9e4f6-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9e4f6-642"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-642">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9e4f6-643">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-643">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="9e4f6-644">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-644">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="9e4f6-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9e4f6-646">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-646">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9e4f6-647">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-647">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9e4f6-648">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-648">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="9e4f6-649">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-649">Access configuration during startup</span></span>

<span data-ttu-id="9e4f6-650">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-650">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9e4f6-651">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-651">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="9e4f6-652">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-652">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="9e4f6-653">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-653">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="9e4f6-654">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-654">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="9e4f6-655">Razor ページ:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-655">In a Razor Pages page:</span></span>

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

<span data-ttu-id="9e4f6-656">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-656">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9e4f6-657">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="9e4f6-657">Add configuration from an external assembly</span></span>

<span data-ttu-id="9e4f6-658"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-658">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9e4f6-659">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-659">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e4f6-660">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9e4f6-660">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e4f6-661">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-661">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="9e4f6-662">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-662">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="9e4f6-663">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9e4f6-663">Azure Key Vault</span></span>
* <span data-ttu-id="9e4f6-664">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9e4f6-664">Azure App Configuration</span></span>
* <span data-ttu-id="9e4f6-665">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-665">Command-line arguments</span></span>
* <span data-ttu-id="9e4f6-666">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-666">Custom providers (installed or created)</span></span>
* <span data-ttu-id="9e4f6-667">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="9e4f6-667">Directory files</span></span>
* <span data-ttu-id="9e4f6-668">環境変数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-668">Environment variables</span></span>
* <span data-ttu-id="9e4f6-669">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="9e4f6-669">In-memory .NET objects</span></span>
* <span data-ttu-id="9e4f6-670">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="9e4f6-670">Settings files</span></span>

<span data-ttu-id="9e4f6-671">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-671">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9e4f6-672">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-672">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="9e4f6-673">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-673">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="9e4f6-674">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-674">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="9e4f6-675">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-675">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9e4f6-676">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-676">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9e4f6-677">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="9e4f6-677">Host versus app configuration</span></span>

<span data-ttu-id="9e4f6-678">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-678">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9e4f6-679">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-679">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9e4f6-680">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-680">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9e4f6-681">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-681">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="9e4f6-682">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-682">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="9e4f6-683">その他の構成</span><span class="sxs-lookup"><span data-stu-id="9e4f6-683">Other configuration</span></span>

<span data-ttu-id="9e4f6-684">このトピックは、"*アプリの構成*" のみに関連しています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-684">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="9e4f6-685">ASP.NET Core アプリの実行とホストに関するその他の側面は、このトピックでは扱わない構成ファイルを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-685">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="9e4f6-686">*launch.json*/*launchSettings.json* は、開発環境用のツール構成ファイルです。以下で説明されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-686">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="9e4f6-687"><xref:fundamentals/environments#development>、</span><span class="sxs-lookup"><span data-stu-id="9e4f6-687">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="9e4f6-688">開発シナリオ用の ASP.NET Core アプリを構成するためにこのファイルが使用されている、ドキュメント セット全体。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-688">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="9e4f6-689">*web.config* はサーバー構成ファイルです。次のトピックで説明されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-689">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="9e4f6-690">以前のバージョンの ASP.NET からアプリの構成を移行する方法について詳しくは、<xref:migration/proper-to-2x/index#store-configurations> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-690">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="9e4f6-691">既定の構成</span><span class="sxs-lookup"><span data-stu-id="9e4f6-691">Default configuration</span></span>

<span data-ttu-id="9e4f6-692">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-692">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="9e4f6-693">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-693">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="9e4f6-694">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-694">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9e4f6-695">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-695">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="9e4f6-696">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-696">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9e4f6-697">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-697">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9e4f6-698">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-698">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9e4f6-699">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-699">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9e4f6-700">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-700">App configuration is provided from:</span></span>
  * <span data-ttu-id="9e4f6-701">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-701">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9e4f6-702">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-702">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9e4f6-703">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-703">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="9e4f6-704">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-704">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="9e4f6-705">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-705">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="9e4f6-706">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="9e4f6-706">Security</span></span>

<span data-ttu-id="9e4f6-707">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-707">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="9e4f6-708">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-708">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="9e4f6-709">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-709">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9e4f6-710">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-710">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9e4f6-711">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-711">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="9e4f6-712"><xref:security/app-secrets> &ndash; 環境変数を使用して機密データを格納する場合に関するアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-712"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="9e4f6-713">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-713">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="9e4f6-714">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-714">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="9e4f6-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="9e4f6-716">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-716">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9e4f6-717">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="9e4f6-717">Hierarchical configuration data</span></span>

<span data-ttu-id="9e4f6-718">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-718">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9e4f6-719">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-719">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="9e4f6-720">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-720">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="9e4f6-721">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-721">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="9e4f6-722">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-722">section0:key0</span></span>
* <span data-ttu-id="9e4f6-723">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-723">section0:key1</span></span>
* <span data-ttu-id="9e4f6-724">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-724">section1:key0</span></span>
* <span data-ttu-id="9e4f6-725">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-725">section1:key1</span></span>

<span data-ttu-id="9e4f6-726"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-726"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9e4f6-727">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-727">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="9e4f6-728">規約</span><span class="sxs-lookup"><span data-stu-id="9e4f6-728">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="9e4f6-729">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-729">Sources and providers</span></span>

<span data-ttu-id="9e4f6-730">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-730">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="9e4f6-731">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-731">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="9e4f6-732">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-732">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="9e4f6-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9e4f6-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="9e4f6-735">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-735">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="9e4f6-736">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-736">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="9e4f6-737">キー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-737">Keys</span></span>

<span data-ttu-id="9e4f6-738">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-738">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="9e4f6-739">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-739">Keys are case-insensitive.</span></span> <span data-ttu-id="9e4f6-740">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-740">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9e4f6-741">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-741">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="9e4f6-742">階層キー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-742">Hierarchical keys</span></span>
  * <span data-ttu-id="9e4f6-743">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-743">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9e4f6-744">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-744">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9e4f6-745">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-745">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="9e4f6-746">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-746">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9e4f6-747">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-747">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9e4f6-748"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-748">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9e4f6-749">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-749">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="9e4f6-750">値</span><span class="sxs-lookup"><span data-stu-id="9e4f6-750">Values</span></span>

<span data-ttu-id="9e4f6-751">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-751">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="9e4f6-752">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-752">Values are strings.</span></span>
* <span data-ttu-id="9e4f6-753">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-753">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="9e4f6-754">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-754">Providers</span></span>

<span data-ttu-id="9e4f6-755">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-755">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9e4f6-756">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-756">Provider</span></span> | <span data-ttu-id="9e4f6-757">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-757">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="9e4f6-758">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-758">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="9e4f6-759">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9e4f6-759">Azure Key Vault</span></span> |
| <span data-ttu-id="9e4f6-760">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-760">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="9e4f6-761">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9e4f6-761">Azure App Configuration</span></span> |
| [<span data-ttu-id="9e4f6-762">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-762">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="9e4f6-763">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="9e4f6-763">Command-line parameters</span></span> |
| [<span data-ttu-id="9e4f6-764">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-764">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="9e4f6-765">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="9e4f6-765">Custom source</span></span> |
| [<span data-ttu-id="9e4f6-766">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-766">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="9e4f6-767">環境変数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-767">Environment variables</span></span> |
| [<span data-ttu-id="9e4f6-768">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-768">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="9e4f6-769">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-769">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="9e4f6-770">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-770">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="9e4f6-771">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="9e4f6-771">Directory files</span></span> |
| [<span data-ttu-id="9e4f6-772">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-772">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="9e4f6-773">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="9e4f6-773">In-memory collections</span></span> |
| <span data-ttu-id="9e4f6-774">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-774">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="9e4f6-775">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="9e4f6-775">File in the user profile directory</span></span> |

<span data-ttu-id="9e4f6-776">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-776">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="9e4f6-777">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-777">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="9e4f6-778">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-778">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="9e4f6-779">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-779">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9e4f6-780">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-780">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="9e4f6-781">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9e4f6-781">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="9e4f6-782">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-782">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="9e4f6-783">環境変数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-783">Environment variables</span></span>
1. <span data-ttu-id="9e4f6-784">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-784">Command-line arguments</span></span>

<span data-ttu-id="9e4f6-785">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-785">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9e4f6-786">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-786">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9e4f6-787">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-787">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="9e4f6-788">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="9e4f6-788">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="9e4f6-789">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-789">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="9e4f6-790">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9e4f6-790">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9e4f6-791">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-791">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="9e4f6-792">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-792">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="9e4f6-793">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-793">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="9e4f6-794">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="9e4f6-794">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="9e4f6-795">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-795">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="9e4f6-796">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="9e4f6-796">Consume configuration during app startup</span></span>

<span data-ttu-id="9e4f6-797">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-797">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9e4f6-798">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-798">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="9e4f6-799">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-799">Command-line Configuration Provider</span></span>

<span data-ttu-id="9e4f6-800"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-800">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="9e4f6-801">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-801">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-802">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-802">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="9e4f6-803">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-803">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9e4f6-804">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-804">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9e4f6-805">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-805">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9e4f6-806">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-806">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9e4f6-807">環境変数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-807">Environment variables.</span></span>

<span data-ttu-id="9e4f6-808">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-808">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="9e4f6-809">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-809">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="9e4f6-810">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-810">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="9e4f6-811">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-811">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="9e4f6-812">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-812">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9e4f6-813">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-813">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="9e4f6-814">**例**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-814">**Example**</span></span>

<span data-ttu-id="9e4f6-815">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-815">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="9e4f6-816">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-816">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="9e4f6-817">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-817">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="9e4f6-818">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-818">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9e4f6-819">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-819">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="9e4f6-820">引数</span><span class="sxs-lookup"><span data-stu-id="9e4f6-820">Arguments</span></span>

<span data-ttu-id="9e4f6-821">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-821">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="9e4f6-822">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-822">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="9e4f6-823">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-823">Key prefix</span></span>               | <span data-ttu-id="9e4f6-824">例</span><span class="sxs-lookup"><span data-stu-id="9e4f6-824">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="9e4f6-825">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="9e4f6-825">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="9e4f6-826">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-826">Two dashes (`--`)</span></span>        | <span data-ttu-id="9e4f6-827">`--CommandLineKey2=value2`、`--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="9e4f6-828">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="9e4f6-828">Forward slash (`/`)</span></span>      | <span data-ttu-id="9e4f6-829">`/CommandLineKey3=value3`、`/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="9e4f6-830">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-830">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="9e4f6-831">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-831">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="9e4f6-832">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="9e4f6-832">Switch mappings</span></span>

<span data-ttu-id="9e4f6-833">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-833">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="9e4f6-834"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-834">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9e4f6-835">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-835">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9e4f6-836">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-836">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9e4f6-837">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-837">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9e4f6-838">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-838">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9e4f6-839">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-839">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="9e4f6-840">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-840">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9e4f6-841">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-841">Create a switch mappings dictionary.</span></span> <span data-ttu-id="9e4f6-842">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-842">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="9e4f6-843">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-843">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="9e4f6-844">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-844">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="9e4f6-845">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-845">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9e4f6-846">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-846">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="9e4f6-847">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-847">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="9e4f6-848">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-848">Key</span></span>       | <span data-ttu-id="9e4f6-849">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-849">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="9e4f6-850">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-850">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="9e4f6-851">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-851">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="9e4f6-852">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-852">Key</span></span>               | <span data-ttu-id="9e4f6-853">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-853">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="9e4f6-854">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-854">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="9e4f6-855"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-855">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="9e4f6-856">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-856">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9e4f6-857">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-857">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="9e4f6-858">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-858">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="9e4f6-859">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-859">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="9e4f6-860">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-860">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9e4f6-861">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-861">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9e4f6-862">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-862">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="9e4f6-863">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-863">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9e4f6-864">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-864">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9e4f6-865">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-865">Command-line arguments.</span></span>

<span data-ttu-id="9e4f6-866">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-866">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="9e4f6-867">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-867">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="9e4f6-868">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-868">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="9e4f6-869">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-869">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="9e4f6-870">**例**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-870">**Example**</span></span>

<span data-ttu-id="9e4f6-871">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-871">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="9e4f6-872">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-872">Run the sample app.</span></span> <span data-ttu-id="9e4f6-873">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-873">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9e4f6-874">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-874">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="9e4f6-875">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-875">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="9e4f6-876">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-876">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="9e4f6-877">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-877">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="9e4f6-878">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-878">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="9e4f6-879">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-879">Prefixes</span></span>

<span data-ttu-id="9e4f6-880">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-880">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="9e4f6-881">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-881">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="9e4f6-882">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-882">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="9e4f6-883">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-883">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="9e4f6-884">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-884">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9e4f6-885">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-885">**Connection string prefixes**</span></span>

<span data-ttu-id="9e4f6-886">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-886">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9e4f6-887">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-887">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9e4f6-888">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-888">Connection string prefix</span></span> | <span data-ttu-id="9e4f6-889">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-889">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="9e4f6-890">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-890">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="9e4f6-891">MySQL</span><span class="sxs-lookup"><span data-stu-id="9e4f6-891">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="9e4f6-892">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="9e4f6-892">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="9e4f6-893">SQL Server</span><span class="sxs-lookup"><span data-stu-id="9e4f6-893">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="9e4f6-894">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-894">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9e4f6-895">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-895">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9e4f6-896">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-896">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9e4f6-897">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-897">Environment variable key</span></span> | <span data-ttu-id="9e4f6-898">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-898">Converted configuration key</span></span> | <span data-ttu-id="9e4f6-899">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="9e4f6-899">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9e4f6-900">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-900">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9e4f6-901">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-901">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9e4f6-902">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-902">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9e4f6-903">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-903">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9e4f6-904">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-904">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="9e4f6-905">キー: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-905">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9e4f6-906">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-906">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="9e4f6-907">**例**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-907">**Example**</span></span>

<span data-ttu-id="9e4f6-908">サーバー上にカスタム接続文字列環境変数が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-908">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="9e4f6-909">名前 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-909">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="9e4f6-910">数値 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="9e4f6-910">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="9e4f6-911">`IConfiguration` が挿入され、`_config` という名前のフィールドに割り当てられた場合は、次の値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-911">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="9e4f6-912">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-912">File Configuration Provider</span></span>

<span data-ttu-id="9e4f6-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9e4f6-914">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-914">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="9e4f6-915">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-915">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9e4f6-916">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-916">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="9e4f6-917">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-917">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9e4f6-918">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-918">INI Configuration Provider</span></span>

<span data-ttu-id="9e4f6-919"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-919">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9e4f6-920">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-920">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-921">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-921">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="9e4f6-922">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-922">Overloads permit specifying:</span></span>

* <span data-ttu-id="9e4f6-923">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-923">Whether the file is optional.</span></span>
* <span data-ttu-id="9e4f6-924">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-924">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9e4f6-925">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-925">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9e4f6-926">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-926">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9e4f6-927">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-927">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="9e4f6-928">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-928">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9e4f6-929">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-929">section0:key0</span></span>
* <span data-ttu-id="9e4f6-930">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-930">section0:key1</span></span>
* <span data-ttu-id="9e4f6-931">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-931">section1:subsection:key</span></span>
* <span data-ttu-id="9e4f6-932">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-932">section2:subsection0:key</span></span>
* <span data-ttu-id="9e4f6-933">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-933">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="9e4f6-934">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-934">JSON Configuration Provider</span></span>

<span data-ttu-id="9e4f6-935"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-935">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="9e4f6-936">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-936">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-937">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-937">Overloads permit specifying:</span></span>

* <span data-ttu-id="9e4f6-938">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-938">Whether the file is optional.</span></span>
* <span data-ttu-id="9e4f6-939">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-939">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9e4f6-940">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-940">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9e4f6-941">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-941">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9e4f6-942">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-942">The method is called to load configuration from:</span></span>

* <span data-ttu-id="9e4f6-943">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-943">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="9e4f6-944">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-944">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="9e4f6-945">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-945">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="9e4f6-946">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-946">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9e4f6-947">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-947">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9e4f6-948">環境変数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-948">Environment variables.</span></span>
* <span data-ttu-id="9e4f6-949">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-949">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9e4f6-950">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-950">Command-line arguments.</span></span>

<span data-ttu-id="9e4f6-951">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-951">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="9e4f6-952">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-952">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="9e4f6-953">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-953">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9e4f6-954">**例**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-954">**Example**</span></span>

<span data-ttu-id="9e4f6-955">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-955">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="9e4f6-956">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-956">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="9e4f6-957">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-957">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="9e4f6-958">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-958">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="9e4f6-959">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-959">Run the sample app.</span></span> <span data-ttu-id="9e4f6-960">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-960">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9e4f6-961">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-961">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="9e4f6-962">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-962">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="9e4f6-963">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-963">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="9e4f6-964">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-964">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="9e4f6-965">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-965">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="9e4f6-966">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-966">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="9e4f6-967">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-967">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="9e4f6-968">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-968">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="9e4f6-969">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-969">XML Configuration Provider</span></span>

<span data-ttu-id="9e4f6-970"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-970">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9e4f6-971">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-971">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-972">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-972">Overloads permit specifying:</span></span>

* <span data-ttu-id="9e4f6-973">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-973">Whether the file is optional.</span></span>
* <span data-ttu-id="9e4f6-974">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-974">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9e4f6-975">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-975">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9e4f6-976">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-976">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="9e4f6-977">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-977">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="9e4f6-978">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-978">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9e4f6-979">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-979">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="9e4f6-980">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-980">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9e4f6-981">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-981">section0:key0</span></span>
* <span data-ttu-id="9e4f6-982">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-982">section0:key1</span></span>
* <span data-ttu-id="9e4f6-983">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-983">section1:key0</span></span>
* <span data-ttu-id="9e4f6-984">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-984">section1:key1</span></span>

<span data-ttu-id="9e4f6-985">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-985">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="9e4f6-986">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-986">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9e4f6-987">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-987">section:section0:key:key0</span></span>
* <span data-ttu-id="9e4f6-988">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-988">section:section0:key:key1</span></span>
* <span data-ttu-id="9e4f6-989">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-989">section:section1:key:key0</span></span>
* <span data-ttu-id="9e4f6-990">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-990">section:section1:key:key1</span></span>

<span data-ttu-id="9e4f6-991">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-991">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9e4f6-992">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-992">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9e4f6-993">key:attribute</span><span class="sxs-lookup"><span data-stu-id="9e4f6-993">key:attribute</span></span>
* <span data-ttu-id="9e4f6-994">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="9e4f6-994">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9e4f6-995">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-995">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="9e4f6-996"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-996">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9e4f6-997">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-997">The key is the file name.</span></span> <span data-ttu-id="9e4f6-998">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-998">The value contains the file's contents.</span></span> <span data-ttu-id="9e4f6-999">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-999">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9e4f6-1000">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1000">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9e4f6-1001">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1001">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9e4f6-1002">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1002">Overloads permit specifying:</span></span>

* <span data-ttu-id="9e4f6-1003">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1003">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9e4f6-1004">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1004">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9e4f6-1005">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1005">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9e4f6-1006">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1006">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9e4f6-1007">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1007">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="9e4f6-1008">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1008">Memory Configuration Provider</span></span>

<span data-ttu-id="9e4f6-1009"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1009">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9e4f6-1010">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1010">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9e4f6-1011">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1011">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="9e4f6-1012">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1012">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="9e4f6-1013">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1013">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="9e4f6-1014">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1014">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="9e4f6-1015">GetValue</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1015">GetValue</span></span>

<span data-ttu-id="9e4f6-1016">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) によって、指定したキーで構成から単一の値が抽出され、それが指定したコレクション以外の型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1016">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="9e4f6-1017">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1017">An overload accepts a default value.</span></span>

<span data-ttu-id="9e4f6-1018">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1018">The following example:</span></span>

* <span data-ttu-id="9e4f6-1019">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1019">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="9e4f6-1020">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1020">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="9e4f6-1021">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1021">Types the value as an `int`.</span></span>
* <span data-ttu-id="9e4f6-1022">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1022">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9e4f6-1023">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1023">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9e4f6-1024">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1024">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="9e4f6-1025">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1025">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="9e4f6-1026">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1026">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="9e4f6-1027">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1027">section0:key0</span></span>
* <span data-ttu-id="9e4f6-1028">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1028">section0:key1</span></span>
* <span data-ttu-id="9e4f6-1029">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1029">section1:key0</span></span>
* <span data-ttu-id="9e4f6-1030">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1030">section1:key1</span></span>
* <span data-ttu-id="9e4f6-1031">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1031">section2:subsection0:key0</span></span>
* <span data-ttu-id="9e4f6-1032">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1032">section2:subsection0:key1</span></span>
* <span data-ttu-id="9e4f6-1033">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1033">section2:subsection1:key0</span></span>
* <span data-ttu-id="9e4f6-1034">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1034">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="9e4f6-1035">GetSection</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1035">GetSection</span></span>

<span data-ttu-id="9e4f6-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="9e4f6-1037">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1037">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="9e4f6-1038">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1038">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="9e4f6-1039">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1039">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="9e4f6-1040">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1040">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9e4f6-1041">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1041">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9e4f6-1042">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1042">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9e4f6-1043"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1043">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="9e4f6-1044">GetChildren</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1044">GetChildren</span></span>

<span data-ttu-id="9e4f6-1045">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1045">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="9e4f6-1046">既存</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1046">Exists</span></span>

<span data-ttu-id="9e4f6-1047">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1047">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="9e4f6-1048">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1048">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="9e4f6-1049">クラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1049">Bind to a class</span></span>

<span data-ttu-id="9e4f6-1050">"*オプション パターン*" を使用して、関連する設定のグループを表すクラスに構成をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1050">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="9e4f6-1051">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1051">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9e4f6-1052">構成値は文字列として返されますが、<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> を呼び出すことで [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) オブジェクトを構築できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1052">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="9e4f6-1053">バインダーは、指定された型のすべてのパブリックな読み取り/書き込みプロパティに値をバインドします。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1053">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="9e4f6-1054">フィールドはバインド**されません**。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1054">Fields are **not** bound.</span></span>

<span data-ttu-id="9e4f6-1055">サンプル アプリには `Starship` モデル (*Models/Starship.cs*) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1055">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="9e4f6-1056">サンプル アプリが JSON 構成プロバイダーを使用して構成を読み込むときに、*starship.json* ファイルの `starship` セクションで構成が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1056">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="9e4f6-1057">次の構成のキーと値のペアが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1057">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="9e4f6-1058">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1058">Key</span></span>                   | <span data-ttu-id="9e4f6-1059">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1059">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="9e4f6-1060">starship:name</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1060">starship:name</span></span>         | <span data-ttu-id="9e4f6-1061">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1061">USS Enterprise</span></span>                                    |
| <span data-ttu-id="9e4f6-1062">starship:registry</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1062">starship:registry</span></span>     | <span data-ttu-id="9e4f6-1063">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1063">NCC-1701</span></span>                                          |
| <span data-ttu-id="9e4f6-1064">starship:class</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1064">starship:class</span></span>        | <span data-ttu-id="9e4f6-1065">Constitution</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1065">Constitution</span></span>                                      |
| <span data-ttu-id="9e4f6-1066">starship:length</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1066">starship:length</span></span>       | <span data-ttu-id="9e4f6-1067">304.8</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1067">304.8</span></span>                                             |
| <span data-ttu-id="9e4f6-1068">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1068">starship:commissioned</span></span> | <span data-ttu-id="9e4f6-1069">False</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1069">False</span></span>                                             |
| <span data-ttu-id="9e4f6-1070">trademark</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1070">trademark</span></span>             | <span data-ttu-id="9e4f6-1071">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1071">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="9e4f6-1072">サンプル アプリは `starship` キーを使用して `GetSection` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1072">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="9e4f6-1073">`starship` のキーと値のペアは分離されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1073">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="9e4f6-1074">`Starship` クラスのインスタンスを渡すサブセクションで、`Bind` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1074">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="9e4f6-1075">インスタンスの値をバインドした後、インスタンスがレンダリング用のプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1075">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="9e4f6-1076">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1076">Bind to an object graph</span></span>

<span data-ttu-id="9e4f6-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="9e4f6-1078">単純なオブジェクトをバインドする場合と同様に、パブリックな読み取り/書き込みプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1078">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="9e4f6-1079">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1079">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="9e4f6-1080">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1080">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="9e4f6-1081">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1081">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="9e4f6-1082">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1082">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="9e4f6-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) によってバインドされ、指定した型が返されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="9e4f6-1084">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1084">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="9e4f6-1085">次のコードは、前の例と共に `Get<T>` を使用する方法を示しています。これにより、バインドされたインスタンスを、表示用に使用するプロパティに直接割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1085">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9e4f6-1086">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1086">Bind an array to a class</span></span>

<span data-ttu-id="9e4f6-1087">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1087">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="9e4f6-1088"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1088">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9e4f6-1089">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1089">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="9e4f6-1090">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1090">Binding is provided by convention.</span></span> <span data-ttu-id="9e4f6-1091">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1091">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="9e4f6-1092">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1092">**In-memory array processing**</span></span>

<span data-ttu-id="9e4f6-1093">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1093">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="9e4f6-1094">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1094">Key</span></span>             | <span data-ttu-id="9e4f6-1095">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1095">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9e4f6-1096">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1096">array:entries:0</span></span> | <span data-ttu-id="9e4f6-1097">value0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1097">value0</span></span> |
| <span data-ttu-id="9e4f6-1098">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1098">array:entries:1</span></span> | <span data-ttu-id="9e4f6-1099">value1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1099">value1</span></span> |
| <span data-ttu-id="9e4f6-1100">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1100">array:entries:2</span></span> | <span data-ttu-id="9e4f6-1101">value2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1101">value2</span></span> |
| <span data-ttu-id="9e4f6-1102">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1102">array:entries:4</span></span> | <span data-ttu-id="9e4f6-1103">value4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1103">value4</span></span> |
| <span data-ttu-id="9e4f6-1104">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1104">array:entries:5</span></span> | <span data-ttu-id="9e4f6-1105">value5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1105">value5</span></span> |

<span data-ttu-id="9e4f6-1106">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1106">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="9e4f6-1107">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1107">The array skips a value for index &num;3.</span></span> <span data-ttu-id="9e4f6-1108">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1108">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="9e4f6-1109">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1109">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="9e4f6-1110">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1110">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="9e4f6-1111">また、[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用して、コードをよりコンパクトにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1111">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="9e4f6-1112">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1112">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="9e4f6-1113">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1113">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9e4f6-1114">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1114">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9e4f6-1115">0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1115">0</span></span>                            | <span data-ttu-id="9e4f6-1116">value0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1116">value0</span></span>                       |
| <span data-ttu-id="9e4f6-1117">1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1117">1</span></span>                            | <span data-ttu-id="9e4f6-1118">value1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1118">value1</span></span>                       |
| <span data-ttu-id="9e4f6-1119">2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1119">2</span></span>                            | <span data-ttu-id="9e4f6-1120">value2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1120">value2</span></span>                       |
| <span data-ttu-id="9e4f6-1121">3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1121">3</span></span>                            | <span data-ttu-id="9e4f6-1122">value4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1122">value4</span></span>                       |
| <span data-ttu-id="9e4f6-1123">4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1123">4</span></span>                            | <span data-ttu-id="9e4f6-1124">value5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1124">value5</span></span>                       |

<span data-ttu-id="9e4f6-1125">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1125">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9e4f6-1126">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1126">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9e4f6-1127">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1127">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9e4f6-1128">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1128">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="9e4f6-1129">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1129">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="9e4f6-1130">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1130">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="9e4f6-1131">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1131">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="9e4f6-1132">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1132">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="9e4f6-1133">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1133">Key</span></span>             | <span data-ttu-id="9e4f6-1134">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1134">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="9e4f6-1135">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1135">array:entries:3</span></span> | <span data-ttu-id="9e4f6-1136">value3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1136">value3</span></span> |

<span data-ttu-id="9e4f6-1137">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1137">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="9e4f6-1138">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1138">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9e4f6-1139">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1139">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="9e4f6-1140">0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1140">0</span></span>                            | <span data-ttu-id="9e4f6-1141">value0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1141">value0</span></span>                       |
| <span data-ttu-id="9e4f6-1142">1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1142">1</span></span>                            | <span data-ttu-id="9e4f6-1143">value1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1143">value1</span></span>                       |
| <span data-ttu-id="9e4f6-1144">2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1144">2</span></span>                            | <span data-ttu-id="9e4f6-1145">value2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1145">value2</span></span>                       |
| <span data-ttu-id="9e4f6-1146">3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1146">3</span></span>                            | <span data-ttu-id="9e4f6-1147">value3</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1147">value3</span></span>                       |
| <span data-ttu-id="9e4f6-1148">4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1148">4</span></span>                            | <span data-ttu-id="9e4f6-1149">value4</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1149">value4</span></span>                       |
| <span data-ttu-id="9e4f6-1150">5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1150">5</span></span>                            | <span data-ttu-id="9e4f6-1151">value5</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1151">value5</span></span>                       |

<span data-ttu-id="9e4f6-1152">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1152">**JSON array processing**</span></span>

<span data-ttu-id="9e4f6-1153">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1153">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="9e4f6-1154">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1154">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="9e4f6-1155">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1155">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="9e4f6-1156">Key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1156">Key</span></span>                     | <span data-ttu-id="9e4f6-1157">[値]</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1157">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="9e4f6-1158">json_array:key</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1158">json_array:key</span></span>          | <span data-ttu-id="9e4f6-1159">valueA</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1159">valueA</span></span> |
| <span data-ttu-id="9e4f6-1160">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1160">json_array:subsection:0</span></span> | <span data-ttu-id="9e4f6-1161">valueB</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1161">valueB</span></span> |
| <span data-ttu-id="9e4f6-1162">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1162">json_array:subsection:1</span></span> | <span data-ttu-id="9e4f6-1163">valueC</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1163">valueC</span></span> |
| <span data-ttu-id="9e4f6-1164">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1164">json_array:subsection:2</span></span> | <span data-ttu-id="9e4f6-1165">valueD</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1165">valueD</span></span> |

<span data-ttu-id="9e4f6-1166">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1166">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="9e4f6-1167">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1167">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="9e4f6-1168">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1168">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="9e4f6-1169">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1169">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="9e4f6-1170">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1170">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="9e4f6-1171">0</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1171">0</span></span>                                   | <span data-ttu-id="9e4f6-1172">valueB</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1172">valueB</span></span>                              |
| <span data-ttu-id="9e4f6-1173">1</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1173">1</span></span>                                   | <span data-ttu-id="9e4f6-1174">valueC</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1174">valueC</span></span>                              |
| <span data-ttu-id="9e4f6-1175">2</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1175">2</span></span>                                   | <span data-ttu-id="9e4f6-1176">valueD</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1176">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="9e4f6-1177">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1177">Custom configuration provider</span></span>

<span data-ttu-id="9e4f6-1178">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1178">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9e4f6-1179">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1179">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9e4f6-1180">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1180">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9e4f6-1181">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1181">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9e4f6-1182">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1182">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9e4f6-1183">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1183">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9e4f6-1184">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1184">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9e4f6-1185">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1185">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="9e4f6-1186">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1186">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9e4f6-1187">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1187">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9e4f6-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9e4f6-1189"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1189">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9e4f6-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9e4f6-1191"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1191">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9e4f6-1192">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1192">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="9e4f6-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9e4f6-1194">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1194">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9e4f6-1195">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1195">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9e4f6-1196">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1196">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="9e4f6-1197">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1197">Access configuration during startup</span></span>

<span data-ttu-id="9e4f6-1198">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1198">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9e4f6-1199">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1199">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="9e4f6-1200">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1200">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="9e4f6-1201">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1201">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="9e4f6-1202">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1202">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="9e4f6-1203">Razor ページ:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1203">In a Razor Pages page:</span></span>

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

<span data-ttu-id="9e4f6-1204">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1204">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9e4f6-1205">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1205">Add configuration from an external assembly</span></span>

<span data-ttu-id="9e4f6-1206"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1206">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9e4f6-1207">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1207">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e4f6-1208">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9e4f6-1208">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
