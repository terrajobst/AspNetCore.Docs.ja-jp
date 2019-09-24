---
title: ASP.NET Core の構成
author: guardrex
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 357a3d89648086f0329cd16bc9d72863df9bdcd6
ms.sourcegitcommit: 8a36be1bfee02eba3b07b7a86085ec25c38bae6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71217786"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="afb8f-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="afb8f-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="afb8f-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="afb8f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="afb8f-105">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="afb8f-106">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="afb8f-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="afb8f-107">Azure Key Vault</span></span>
* <span data-ttu-id="afb8f-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="afb8f-108">Azure App Configuration</span></span>
* <span data-ttu-id="afb8f-109">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="afb8f-109">Command-line arguments</span></span>
* <span data-ttu-id="afb8f-110">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="afb8f-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="afb8f-111">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="afb8f-111">Directory files</span></span>
* <span data-ttu-id="afb8f-112">環境変数</span><span class="sxs-lookup"><span data-stu-id="afb8f-112">Environment variables</span></span>
* <span data-ttu-id="afb8f-113">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="afb8f-113">In-memory .NET objects</span></span>
* <span data-ttu-id="afb8f-114">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="afb8f-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afb8f-115">一般的な構成プロバイダーのシナリオの構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、フレームワークによって暗黙的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="afb8f-116">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="afb8f-117">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="afb8f-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="afb8f-118">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="afb8f-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="afb8f-119">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="afb8f-120">詳細については、<xref:fundamentals/configuration/options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="afb8f-121">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="afb8f-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="afb8f-122">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="afb8f-122">Host versus app configuration</span></span>

<span data-ttu-id="afb8f-123">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="afb8f-124">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="afb8f-125">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="afb8f-126">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="afb8f-127">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="afb8f-128">既定の構成</span><span class="sxs-lookup"><span data-stu-id="afb8f-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afb8f-129">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="afb8f-130">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="afb8f-131">[汎用ホスト](xref:fundamentals/host/generic-host)を使用するアプリに以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="afb8f-132">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="afb8f-133">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="afb8f-134">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="afb8f-135">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="afb8f-136">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="afb8f-137">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="afb8f-138">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="afb8f-139">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="afb8f-140">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="afb8f-141">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="afb8f-141">Enable IIS integration.</span></span>
* <span data-ttu-id="afb8f-142">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="afb8f-143">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="afb8f-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="afb8f-144">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="afb8f-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="afb8f-145">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="afb8f-146">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="afb8f-147">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="afb8f-148">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="afb8f-149">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="afb8f-150">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="afb8f-151">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="afb8f-152">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="afb8f-153">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="afb8f-154">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="afb8f-155">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="afb8f-156">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="afb8f-157">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="afb8f-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="afb8f-158">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="afb8f-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="afb8f-159">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="afb8f-160">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="afb8f-161">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="afb8f-162">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="afb8f-162">Security</span></span>

<span data-ttu-id="afb8f-163">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="afb8f-164">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="afb8f-165">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="afb8f-166">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="afb8f-167">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="afb8f-168"><xref:security/app-secrets> &ndash; には、環境変数を使用して機密データを格納する場合に関するアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="afb8f-169">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="afb8f-170">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="afb8f-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="afb8f-172">詳細については、<xref:security/key-vault-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="afb8f-173">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="afb8f-173">Hierarchical configuration data</span></span>

<span data-ttu-id="afb8f-174">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="afb8f-175">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="afb8f-176">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="afb8f-177">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="afb8f-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-178">section0:key0</span></span>
* <span data-ttu-id="afb8f-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-179">section0:key1</span></span>
* <span data-ttu-id="afb8f-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-180">section1:key0</span></span>
* <span data-ttu-id="afb8f-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-181">section1:key1</span></span>

<span data-ttu-id="afb8f-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="afb8f-183">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="afb8f-184">規約</span><span class="sxs-lookup"><span data-stu-id="afb8f-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="afb8f-185">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-185">Sources and providers</span></span>

<span data-ttu-id="afb8f-186">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="afb8f-187">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="afb8f-188">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="afb8f-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="afb8f-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> を挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

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

<span data-ttu-id="afb8f-191">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="afb8f-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="afb8f-192">キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-192">Keys</span></span>

<span data-ttu-id="afb8f-193">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="afb8f-194">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-194">Keys are case-insensitive.</span></span> <span data-ttu-id="afb8f-195">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="afb8f-196">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="afb8f-197">階層キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-197">Hierarchical keys</span></span>
  * <span data-ttu-id="afb8f-198">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="afb8f-199">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="afb8f-200">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="afb8f-201">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="afb8f-202">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="afb8f-203"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="afb8f-204">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="afb8f-205">値</span><span class="sxs-lookup"><span data-stu-id="afb8f-205">Values</span></span>

<span data-ttu-id="afb8f-206">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="afb8f-207">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="afb8f-207">Values are strings.</span></span>
* <span data-ttu-id="afb8f-208">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="afb8f-209">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-209">Providers</span></span>

<span data-ttu-id="afb8f-210">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="afb8f-211">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-211">Provider</span></span> | <span data-ttu-id="afb8f-212">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="afb8f-213">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="afb8f-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="afb8f-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="afb8f-214">Azure Key Vault</span></span> |
| <span data-ttu-id="afb8f-215">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="afb8f-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="afb8f-216">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="afb8f-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="afb8f-217">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="afb8f-218">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="afb8f-218">Command-line parameters</span></span> |
| [<span data-ttu-id="afb8f-219">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="afb8f-220">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="afb8f-220">Custom source</span></span> |
| [<span data-ttu-id="afb8f-221">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="afb8f-222">環境変数</span><span class="sxs-lookup"><span data-stu-id="afb8f-222">Environment variables</span></span> |
| [<span data-ttu-id="afb8f-223">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="afb8f-224">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="afb8f-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="afb8f-225">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="afb8f-226">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="afb8f-226">Directory files</span></span> |
| [<span data-ttu-id="afb8f-227">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="afb8f-228">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="afb8f-228">In-memory collections</span></span> |
| <span data-ttu-id="afb8f-229">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="afb8f-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="afb8f-230">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="afb8f-230">File in the user profile directory</span></span> |

<span data-ttu-id="afb8f-231">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="afb8f-232">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="afb8f-233">基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="afb8f-234">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="afb8f-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="afb8f-235">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="afb8f-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="afb8f-236">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="afb8f-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="afb8f-237">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="afb8f-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="afb8f-238">環境変数</span><span class="sxs-lookup"><span data-stu-id="afb8f-238">Environment variables</span></span>
1. <span data-ttu-id="afb8f-239">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="afb8f-239">Command-line arguments</span></span>

<span data-ttu-id="afb8f-240">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="afb8f-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="afb8f-241">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="afb8f-242">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="afb8f-243">ConfigureHostConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="afb8f-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="afb8f-244">ホスト ビルダーを構成するには、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出し、構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="afb8f-245">`ConfigureHostConfiguration` は、後でビルド プロセスに使用する <xref:Microsoft.Extensions.Hosting.IHostEnvironment> を初期化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="afb8f-246">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="afb8f-247">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="afb8f-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="afb8f-248">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

::: moniker-end

## <a name="configureappconfiguration"></a><span data-ttu-id="afb8f-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="afb8f-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="afb8f-250">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="afb8f-251">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="afb8f-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="afb8f-252">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="afb8f-253">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="afb8f-253">Consume configuration during app startup</span></span>

<span data-ttu-id="afb8f-254">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-254">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="afb8f-255">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-255">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="afb8f-256">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-256">Command-line Configuration Provider</span></span>

<span data-ttu-id="afb8f-257"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-257">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="afb8f-258">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-258">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="afb8f-259">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-259">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="afb8f-260">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-260">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="afb8f-261">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-261">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="afb8f-262">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="afb8f-262">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="afb8f-263">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-263">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="afb8f-264">環境変数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-264">Environment variables.</span></span>

<span data-ttu-id="afb8f-265">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-265">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="afb8f-266">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-266">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="afb8f-267">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-267">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="afb8f-268">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-268">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="afb8f-269">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-269">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="afb8f-270">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-270">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="afb8f-271">**例**</span><span class="sxs-lookup"><span data-stu-id="afb8f-271">**Example**</span></span>

<span data-ttu-id="afb8f-272">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-272">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="afb8f-273">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-273">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="afb8f-274">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-274">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="afb8f-275">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-275">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="afb8f-276">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-276">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="afb8f-277">引数</span><span class="sxs-lookup"><span data-stu-id="afb8f-277">Arguments</span></span>

<span data-ttu-id="afb8f-278">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-278">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="afb8f-279">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-279">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="afb8f-280">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="afb8f-280">Key prefix</span></span>               | <span data-ttu-id="afb8f-281">例</span><span class="sxs-lookup"><span data-stu-id="afb8f-281">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="afb8f-282">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="afb8f-282">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="afb8f-283">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="afb8f-283">Two dashes (`--`)</span></span>        | <span data-ttu-id="afb8f-284">`--CommandLineKey2=value2`、 `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="afb8f-284">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="afb8f-285">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="afb8f-285">Forward slash (`/`)</span></span>      | <span data-ttu-id="afb8f-286">`/CommandLineKey3=value3`、 `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="afb8f-286">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="afb8f-287">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-287">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="afb8f-288">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="afb8f-288">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="afb8f-289">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="afb8f-289">Switch mappings</span></span>

<span data-ttu-id="afb8f-290">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-290">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="afb8f-291"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-291">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="afb8f-292">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-292">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="afb8f-293">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-293">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="afb8f-294">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="afb8f-294">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="afb8f-295">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="afb8f-295">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="afb8f-296">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-296">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="afb8f-297">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-297">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="afb8f-298">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-298">Create a switch mappings dictionary.</span></span> <span data-ttu-id="afb8f-299">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-299">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="afb8f-300">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-300">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="afb8f-301">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-301">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="afb8f-302">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-302">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="afb8f-303">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-303">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="afb8f-304">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-304">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="afb8f-305">キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-305">Key</span></span>       | <span data-ttu-id="afb8f-306">[値]</span><span class="sxs-lookup"><span data-stu-id="afb8f-306">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="afb8f-307">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-307">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="afb8f-308">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-308">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="afb8f-309">キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-309">Key</span></span>               | <span data-ttu-id="afb8f-310">[値]</span><span class="sxs-lookup"><span data-stu-id="afb8f-310">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="afb8f-311">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-311">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="afb8f-312"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-312">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="afb8f-313">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-313">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="afb8f-314">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure Portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-314">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="afb8f-315">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-315">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afb8f-316">新しいホスト ビルダーが[汎用ホスト](xref:fundamentals/host/generic-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `DOTNET_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-316">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="afb8f-317">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-317">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="afb8f-318">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="afb8f-319">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="afb8f-320">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-320">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="afb8f-321">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="afb8f-321">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="afb8f-322">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="afb8f-322">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="afb8f-323">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-323">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="afb8f-324">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-324">Command-line arguments.</span></span>

<span data-ttu-id="afb8f-325">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-325">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="afb8f-326">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-326">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="afb8f-327">追加の環境変数からアプリの構成を指定する必要がある場合は、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、そのプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-327">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call additional providers here as needed.
    // Call AddEnvironmentVariables last if you need to allow
    // environment variables to override values from other 
    // providers.
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
}
```

<span data-ttu-id="afb8f-328">**例**</span><span class="sxs-lookup"><span data-stu-id="afb8f-328">**Example**</span></span>

<span data-ttu-id="afb8f-329">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-329">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="afb8f-330">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-330">Run the sample app.</span></span> <span data-ttu-id="afb8f-331">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-331">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="afb8f-332">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-332">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="afb8f-333">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-333">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="afb8f-334">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-334">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="afb8f-335">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-335">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="afb8f-336">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-336">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="afb8f-337">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="afb8f-337">Prefixes</span></span>

<span data-ttu-id="afb8f-338">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-338">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="afb8f-339">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-339">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="afb8f-340">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-340">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="afb8f-341">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-341">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="afb8f-342">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-342">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="afb8f-343">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="afb8f-343">**Connection string prefixes**</span></span>

<span data-ttu-id="afb8f-344">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-344">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="afb8f-345">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-345">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="afb8f-346">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="afb8f-346">Connection string prefix</span></span> | <span data-ttu-id="afb8f-347">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-347">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="afb8f-348">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-348">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="afb8f-349">MySQL</span><span class="sxs-lookup"><span data-stu-id="afb8f-349">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="afb8f-350">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="afb8f-350">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="afb8f-351">SQL Server</span><span class="sxs-lookup"><span data-stu-id="afb8f-351">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="afb8f-352">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="afb8f-352">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="afb8f-353">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-353">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="afb8f-354">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-354">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="afb8f-355">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-355">Environment variable key</span></span> | <span data-ttu-id="afb8f-356">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-356">Converted configuration key</span></span> | <span data-ttu-id="afb8f-357">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="afb8f-357">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="afb8f-358">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-358">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="afb8f-359">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="afb8f-359">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="afb8f-360">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="afb8f-360">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="afb8f-361">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="afb8f-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="afb8f-362">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="afb8f-362">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="afb8f-363">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="afb8f-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="afb8f-364">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="afb8f-364">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="afb8f-365">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-365">File Configuration Provider</span></span>

<span data-ttu-id="afb8f-366"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="afb8f-366"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="afb8f-367">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="afb8f-367">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="afb8f-368">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-368">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="afb8f-369">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-369">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="afb8f-370">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-370">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="afb8f-371">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-371">INI Configuration Provider</span></span>

<span data-ttu-id="afb8f-372"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-372">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="afb8f-373">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-373">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="afb8f-374">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-374">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="afb8f-375">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="afb8f-376">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="afb8f-376">Whether the file is optional.</span></span>
* <span data-ttu-id="afb8f-377">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="afb8f-377">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="afb8f-378">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-378">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="afb8f-379">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-379">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="afb8f-380">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-380">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="afb8f-381">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="afb8f-381">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="afb8f-382">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-382">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="afb8f-383">section0:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-383">section0:key0</span></span>
* <span data-ttu-id="afb8f-384">section0:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-384">section0:key1</span></span>
* <span data-ttu-id="afb8f-385">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="afb8f-385">section1:subsection:key</span></span>
* <span data-ttu-id="afb8f-386">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="afb8f-386">section2:subsection0:key</span></span>
* <span data-ttu-id="afb8f-387">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="afb8f-387">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="afb8f-388">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-388">JSON Configuration Provider</span></span>

<span data-ttu-id="afb8f-389"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-389">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="afb8f-390">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-390">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="afb8f-391">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-391">Overloads permit specifying:</span></span>

* <span data-ttu-id="afb8f-392">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="afb8f-392">Whether the file is optional.</span></span>
* <span data-ttu-id="afb8f-393">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="afb8f-393">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="afb8f-394">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-394">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="afb8f-395">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-395">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="afb8f-396">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-396">The method is called to load configuration from:</span></span>

* <span data-ttu-id="afb8f-397">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-397">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="afb8f-398">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-398">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="afb8f-399">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-399">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="afb8f-400">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-400">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="afb8f-401">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-401">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="afb8f-402">環境変数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-402">Environment variables.</span></span>
* <span data-ttu-id="afb8f-403">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-403">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="afb8f-404">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="afb8f-404">Command-line arguments.</span></span>

<span data-ttu-id="afb8f-405">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-405">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="afb8f-406">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-406">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="afb8f-407">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-407">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="afb8f-408">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-408">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="afb8f-409">**例**</span><span class="sxs-lookup"><span data-stu-id="afb8f-409">**Example**</span></span>

<span data-ttu-id="afb8f-410">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="afb8f-411">構成は、*appsettings.json* および *appsettings.{Environment}.json* から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="afb8f-412">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-412">Run the sample app.</span></span> <span data-ttu-id="afb8f-413">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="afb8f-414">出力に、環境に応じて、表に示す構成のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="afb8f-415">ログの構成キーでは、階層の区切り文字としてコロン (`:`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="afb8f-416">キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-416">Key</span></span>                        | <span data-ttu-id="afb8f-417">開発時の値</span><span class="sxs-lookup"><span data-stu-id="afb8f-417">Development Value</span></span> | <span data-ttu-id="afb8f-418">運用時の値</span><span class="sxs-lookup"><span data-stu-id="afb8f-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="afb8f-419">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="afb8f-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="afb8f-420">情報</span><span class="sxs-lookup"><span data-stu-id="afb8f-420">Information</span></span>       | <span data-ttu-id="afb8f-421">情報</span><span class="sxs-lookup"><span data-stu-id="afb8f-421">Information</span></span>      |
| <span data-ttu-id="afb8f-422">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="afb8f-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="afb8f-423">情報</span><span class="sxs-lookup"><span data-stu-id="afb8f-423">Information</span></span>       | <span data-ttu-id="afb8f-424">情報</span><span class="sxs-lookup"><span data-stu-id="afb8f-424">Information</span></span>      |
| <span data-ttu-id="afb8f-425">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="afb8f-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="afb8f-426">デバッグ</span><span class="sxs-lookup"><span data-stu-id="afb8f-426">Debug</span></span>             | <span data-ttu-id="afb8f-427">Error</span><span class="sxs-lookup"><span data-stu-id="afb8f-427">Error</span></span>            |
| <span data-ttu-id="afb8f-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="afb8f-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="afb8f-429">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-429">XML Configuration Provider</span></span>

<span data-ttu-id="afb8f-430"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="afb8f-431">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="afb8f-432">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="afb8f-433">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="afb8f-433">Whether the file is optional.</span></span>
* <span data-ttu-id="afb8f-434">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="afb8f-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="afb8f-435">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="afb8f-436">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="afb8f-437">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="afb8f-438">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="afb8f-439">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-439">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="afb8f-440">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-440">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="afb8f-441">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-441">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="afb8f-442">section0:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-442">section0:key0</span></span>
* <span data-ttu-id="afb8f-443">section0:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-443">section0:key1</span></span>
* <span data-ttu-id="afb8f-444">section1:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-444">section1:key0</span></span>
* <span data-ttu-id="afb8f-445">section1:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-445">section1:key1</span></span>

<span data-ttu-id="afb8f-446">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-446">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="afb8f-447">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-447">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="afb8f-448">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-448">section:section0:key:key0</span></span>
* <span data-ttu-id="afb8f-449">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-449">section:section0:key:key1</span></span>
* <span data-ttu-id="afb8f-450">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-450">section:section1:key:key0</span></span>
* <span data-ttu-id="afb8f-451">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-451">section:section1:key:key1</span></span>

<span data-ttu-id="afb8f-452">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-452">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="afb8f-453">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-453">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="afb8f-454">key:attribute</span><span class="sxs-lookup"><span data-stu-id="afb8f-454">key:attribute</span></span>
* <span data-ttu-id="afb8f-455">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="afb8f-455">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="afb8f-456">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-456">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="afb8f-457"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-457">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="afb8f-458">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="afb8f-458">The key is the file name.</span></span> <span data-ttu-id="afb8f-459">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-459">The value contains the file's contents.</span></span> <span data-ttu-id="afb8f-460">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-460">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="afb8f-461">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-461">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="afb8f-462">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-462">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="afb8f-463">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-463">Overloads permit specifying:</span></span>

* <span data-ttu-id="afb8f-464">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="afb8f-464">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="afb8f-465">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="afb8f-465">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="afb8f-466">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-466">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="afb8f-467">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-467">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="afb8f-468">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-468">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<span data-ttu-id="afb8f-469">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-469">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

## <a name="memory-configuration-provider"></a><span data-ttu-id="afb8f-470">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-470">Memory Configuration Provider</span></span>

<span data-ttu-id="afb8f-471"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-471">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="afb8f-472">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-472">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="afb8f-473">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-473">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="afb8f-474">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-474">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="afb8f-475">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-475">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="afb8f-476">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-476">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="afb8f-477">GetValue</span><span class="sxs-lookup"><span data-stu-id="afb8f-477">GetValue</span></span>

<span data-ttu-id="afb8f-478">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) によって、指定したキーで構成から値が抽出され、それが指定した型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-478">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="afb8f-479">オーバーロードを使用すると、キーが見つからない場合に既定値を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-479">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="afb8f-480">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="afb8f-480">The following example:</span></span>

* <span data-ttu-id="afb8f-481">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-481">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="afb8f-482">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-482">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="afb8f-483">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="afb8f-483">Types the value as an `int`.</span></span>
* <span data-ttu-id="afb8f-484">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-484">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="afb8f-485">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="afb8f-485">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="afb8f-486">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-486">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="afb8f-487">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-487">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="afb8f-488">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-488">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="afb8f-489">section0:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-489">section0:key0</span></span>
* <span data-ttu-id="afb8f-490">section0:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-490">section0:key1</span></span>
* <span data-ttu-id="afb8f-491">section1:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-491">section1:key0</span></span>
* <span data-ttu-id="afb8f-492">section1:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-492">section1:key1</span></span>
* <span data-ttu-id="afb8f-493">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-493">section2:subsection0:key0</span></span>
* <span data-ttu-id="afb8f-494">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-494">section2:subsection0:key1</span></span>
* <span data-ttu-id="afb8f-495">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="afb8f-495">section2:subsection1:key0</span></span>
* <span data-ttu-id="afb8f-496">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="afb8f-496">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="afb8f-497">GetSection</span><span class="sxs-lookup"><span data-stu-id="afb8f-497">GetSection</span></span>

<span data-ttu-id="afb8f-498">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-498">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="afb8f-499">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-499">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="afb8f-500">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-500">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="afb8f-501">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-501">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="afb8f-502">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-502">`GetSection` never returns `null`.</span></span> <span data-ttu-id="afb8f-503">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-503">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="afb8f-504">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-504">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="afb8f-505"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-505">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="afb8f-506">GetChildren</span><span class="sxs-lookup"><span data-stu-id="afb8f-506">GetChildren</span></span>

<span data-ttu-id="afb8f-507">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-507">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="afb8f-508">既存</span><span class="sxs-lookup"><span data-stu-id="afb8f-508">Exists</span></span>

<span data-ttu-id="afb8f-509">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-509">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="afb8f-510">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-510">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="afb8f-511">クラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="afb8f-511">Bind to a class</span></span>

<span data-ttu-id="afb8f-512">"*オプション パターン*" を使用して、関連する設定のグループを表すクラスに構成をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-512">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="afb8f-513">詳細については、<xref:fundamentals/configuration/options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-513">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="afb8f-514">構成値は文字列として返されますが、<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> を呼び出すことで [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) オブジェクトを構築できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-514">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="afb8f-515">サンプル アプリには `Starship` モデル (*Models/Starship.cs*) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-515">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="afb8f-516">サンプル アプリが JSON 構成プロバイダーを使用して構成を読み込むときに、*starship.json* ファイルの `starship` セクションで構成が作成されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-516">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="afb8f-517">次の構成のキーと値のペアが作成されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-517">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="afb8f-518">キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-518">Key</span></span>                   | <span data-ttu-id="afb8f-519">[値]</span><span class="sxs-lookup"><span data-stu-id="afb8f-519">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="afb8f-520">starship:name</span><span class="sxs-lookup"><span data-stu-id="afb8f-520">starship:name</span></span>         | <span data-ttu-id="afb8f-521">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="afb8f-521">USS Enterprise</span></span>                                    |
| <span data-ttu-id="afb8f-522">starship:registry</span><span class="sxs-lookup"><span data-stu-id="afb8f-522">starship:registry</span></span>     | <span data-ttu-id="afb8f-523">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="afb8f-523">NCC-1701</span></span>                                          |
| <span data-ttu-id="afb8f-524">starship:class</span><span class="sxs-lookup"><span data-stu-id="afb8f-524">starship:class</span></span>        | <span data-ttu-id="afb8f-525">Constitution</span><span class="sxs-lookup"><span data-stu-id="afb8f-525">Constitution</span></span>                                      |
| <span data-ttu-id="afb8f-526">starship:length</span><span class="sxs-lookup"><span data-stu-id="afb8f-526">starship:length</span></span>       | <span data-ttu-id="afb8f-527">304.8</span><span class="sxs-lookup"><span data-stu-id="afb8f-527">304.8</span></span>                                             |
| <span data-ttu-id="afb8f-528">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="afb8f-528">starship:commissioned</span></span> | <span data-ttu-id="afb8f-529">False</span><span class="sxs-lookup"><span data-stu-id="afb8f-529">False</span></span>                                             |
| <span data-ttu-id="afb8f-530">trademark</span><span class="sxs-lookup"><span data-stu-id="afb8f-530">trademark</span></span>             | <span data-ttu-id="afb8f-531">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="afb8f-531">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="afb8f-532">サンプル アプリは `starship` キーを使用して `GetSection` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-532">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="afb8f-533">`starship` のキーと値のペアは分離されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-533">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="afb8f-534">`Starship` クラスのインスタンスを渡すサブセクションで、`Bind` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-534">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="afb8f-535">インスタンスの値をバインドした後、インスタンスがレンダリング用のプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-535">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="afb8f-536">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="afb8f-536">Bind to an object graph</span></span>

<span data-ttu-id="afb8f-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="afb8f-538">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="afb8f-538">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="afb8f-539">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-539">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="afb8f-540">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-540">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="afb8f-541">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-541">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="afb8f-542">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) によってバインドされ、指定した型が返されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-542">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="afb8f-543">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="afb8f-543">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="afb8f-544">次のコードは、前の例と共に `Get<T>` を使用する方法を示しています。これにより、バインドされたインスタンスを、表示用に使用するプロパティに直接割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-544">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="afb8f-545">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="afb8f-545">Bind an array to a class</span></span>

<span data-ttu-id="afb8f-546">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="afb8f-546">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="afb8f-547"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="afb8f-547">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="afb8f-548">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-548">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="afb8f-549">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-549">Binding is provided by convention.</span></span> <span data-ttu-id="afb8f-550">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-550">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="afb8f-551">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="afb8f-551">**In-memory array processing**</span></span>

<span data-ttu-id="afb8f-552">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-552">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="afb8f-553">キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-553">Key</span></span>             | <span data-ttu-id="afb8f-554">[値]</span><span class="sxs-lookup"><span data-stu-id="afb8f-554">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="afb8f-555">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="afb8f-555">array:entries:0</span></span> | <span data-ttu-id="afb8f-556">value0</span><span class="sxs-lookup"><span data-stu-id="afb8f-556">value0</span></span> |
| <span data-ttu-id="afb8f-557">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="afb8f-557">array:entries:1</span></span> | <span data-ttu-id="afb8f-558">value1</span><span class="sxs-lookup"><span data-stu-id="afb8f-558">value1</span></span> |
| <span data-ttu-id="afb8f-559">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="afb8f-559">array:entries:2</span></span> | <span data-ttu-id="afb8f-560">value2</span><span class="sxs-lookup"><span data-stu-id="afb8f-560">value2</span></span> |
| <span data-ttu-id="afb8f-561">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="afb8f-561">array:entries:4</span></span> | <span data-ttu-id="afb8f-562">value4</span><span class="sxs-lookup"><span data-stu-id="afb8f-562">value4</span></span> |
| <span data-ttu-id="afb8f-563">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="afb8f-563">array:entries:5</span></span> | <span data-ttu-id="afb8f-564">value5</span><span class="sxs-lookup"><span data-stu-id="afb8f-564">value5</span></span> |

<span data-ttu-id="afb8f-565">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-565">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

<span data-ttu-id="afb8f-566">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="afb8f-566">The array skips a value for index &num;3.</span></span> <span data-ttu-id="afb8f-567">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-567">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="afb8f-568">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-568">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="afb8f-569">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-569">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="afb8f-570">また、[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用して、コードをよりコンパクトにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-570">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="afb8f-571">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-571">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="afb8f-572">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="afb8f-572">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="afb8f-573">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="afb8f-573">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="afb8f-574">0</span><span class="sxs-lookup"><span data-stu-id="afb8f-574">0</span></span>                            | <span data-ttu-id="afb8f-575">value0</span><span class="sxs-lookup"><span data-stu-id="afb8f-575">value0</span></span>                       |
| <span data-ttu-id="afb8f-576">1</span><span class="sxs-lookup"><span data-stu-id="afb8f-576">1</span></span>                            | <span data-ttu-id="afb8f-577">value1</span><span class="sxs-lookup"><span data-stu-id="afb8f-577">value1</span></span>                       |
| <span data-ttu-id="afb8f-578">2</span><span class="sxs-lookup"><span data-stu-id="afb8f-578">2</span></span>                            | <span data-ttu-id="afb8f-579">value2</span><span class="sxs-lookup"><span data-stu-id="afb8f-579">value2</span></span>                       |
| <span data-ttu-id="afb8f-580">3</span><span class="sxs-lookup"><span data-stu-id="afb8f-580">3</span></span>                            | <span data-ttu-id="afb8f-581">value4</span><span class="sxs-lookup"><span data-stu-id="afb8f-581">value4</span></span>                       |
| <span data-ttu-id="afb8f-582">4</span><span class="sxs-lookup"><span data-stu-id="afb8f-582">4</span></span>                            | <span data-ttu-id="afb8f-583">value5</span><span class="sxs-lookup"><span data-stu-id="afb8f-583">value5</span></span>                       |

<span data-ttu-id="afb8f-584">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-584">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="afb8f-585">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-585">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="afb8f-586">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-586">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="afb8f-587">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-587">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="afb8f-588">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-588">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="afb8f-589">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-589">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="afb8f-590">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="afb8f-590">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="afb8f-591">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-591">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="afb8f-592">キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-592">Key</span></span>             | <span data-ttu-id="afb8f-593">[値]</span><span class="sxs-lookup"><span data-stu-id="afb8f-593">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="afb8f-594">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="afb8f-594">array:entries:3</span></span> | <span data-ttu-id="afb8f-595">value3</span><span class="sxs-lookup"><span data-stu-id="afb8f-595">value3</span></span> |

<span data-ttu-id="afb8f-596">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-596">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="afb8f-597">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="afb8f-597">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="afb8f-598">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="afb8f-598">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="afb8f-599">0</span><span class="sxs-lookup"><span data-stu-id="afb8f-599">0</span></span>                            | <span data-ttu-id="afb8f-600">value0</span><span class="sxs-lookup"><span data-stu-id="afb8f-600">value0</span></span>                       |
| <span data-ttu-id="afb8f-601">1</span><span class="sxs-lookup"><span data-stu-id="afb8f-601">1</span></span>                            | <span data-ttu-id="afb8f-602">value1</span><span class="sxs-lookup"><span data-stu-id="afb8f-602">value1</span></span>                       |
| <span data-ttu-id="afb8f-603">2</span><span class="sxs-lookup"><span data-stu-id="afb8f-603">2</span></span>                            | <span data-ttu-id="afb8f-604">value2</span><span class="sxs-lookup"><span data-stu-id="afb8f-604">value2</span></span>                       |
| <span data-ttu-id="afb8f-605">3</span><span class="sxs-lookup"><span data-stu-id="afb8f-605">3</span></span>                            | <span data-ttu-id="afb8f-606">value3</span><span class="sxs-lookup"><span data-stu-id="afb8f-606">value3</span></span>                       |
| <span data-ttu-id="afb8f-607">4</span><span class="sxs-lookup"><span data-stu-id="afb8f-607">4</span></span>                            | <span data-ttu-id="afb8f-608">value4</span><span class="sxs-lookup"><span data-stu-id="afb8f-608">value4</span></span>                       |
| <span data-ttu-id="afb8f-609">5</span><span class="sxs-lookup"><span data-stu-id="afb8f-609">5</span></span>                            | <span data-ttu-id="afb8f-610">value5</span><span class="sxs-lookup"><span data-stu-id="afb8f-610">value5</span></span>                       |

<span data-ttu-id="afb8f-611">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="afb8f-611">**JSON array processing**</span></span>

<span data-ttu-id="afb8f-612">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-612">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="afb8f-613">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="afb8f-613">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="afb8f-614">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-614">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="afb8f-615">キー</span><span class="sxs-lookup"><span data-stu-id="afb8f-615">Key</span></span>                     | <span data-ttu-id="afb8f-616">[値]</span><span class="sxs-lookup"><span data-stu-id="afb8f-616">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="afb8f-617">json_array:key</span><span class="sxs-lookup"><span data-stu-id="afb8f-617">json_array:key</span></span>          | <span data-ttu-id="afb8f-618">valueA</span><span class="sxs-lookup"><span data-stu-id="afb8f-618">valueA</span></span> |
| <span data-ttu-id="afb8f-619">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="afb8f-619">json_array:subsection:0</span></span> | <span data-ttu-id="afb8f-620">valueB</span><span class="sxs-lookup"><span data-stu-id="afb8f-620">valueB</span></span> |
| <span data-ttu-id="afb8f-621">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="afb8f-621">json_array:subsection:1</span></span> | <span data-ttu-id="afb8f-622">valueC</span><span class="sxs-lookup"><span data-stu-id="afb8f-622">valueC</span></span> |
| <span data-ttu-id="afb8f-623">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="afb8f-623">json_array:subsection:2</span></span> | <span data-ttu-id="afb8f-624">valueD</span><span class="sxs-lookup"><span data-stu-id="afb8f-624">valueD</span></span> |

<span data-ttu-id="afb8f-625">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-625">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="afb8f-626">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-626">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="afb8f-627">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-627">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="afb8f-628">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="afb8f-628">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="afb8f-629">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="afb8f-629">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="afb8f-630">0</span><span class="sxs-lookup"><span data-stu-id="afb8f-630">0</span></span>                                   | <span data-ttu-id="afb8f-631">valueB</span><span class="sxs-lookup"><span data-stu-id="afb8f-631">valueB</span></span>                              |
| <span data-ttu-id="afb8f-632">1</span><span class="sxs-lookup"><span data-stu-id="afb8f-632">1</span></span>                                   | <span data-ttu-id="afb8f-633">valueC</span><span class="sxs-lookup"><span data-stu-id="afb8f-633">valueC</span></span>                              |
| <span data-ttu-id="afb8f-634">2</span><span class="sxs-lookup"><span data-stu-id="afb8f-634">2</span></span>                                   | <span data-ttu-id="afb8f-635">valueD</span><span class="sxs-lookup"><span data-stu-id="afb8f-635">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="afb8f-636">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="afb8f-636">Custom configuration provider</span></span>

<span data-ttu-id="afb8f-637">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-637">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="afb8f-638">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="afb8f-638">The provider has the following characteristics:</span></span>

* <span data-ttu-id="afb8f-639">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-639">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="afb8f-640">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-640">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="afb8f-641">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-641">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="afb8f-642">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-642">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="afb8f-643">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="afb8f-643">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="afb8f-644">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-644">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afb8f-645">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-645">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="afb8f-646">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-646">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="afb8f-647">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-647">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="afb8f-648"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-648">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="afb8f-649">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-649">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="afb8f-650"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-650">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="afb8f-651">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-651">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="afb8f-652">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-652">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="afb8f-653">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-653">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="afb8f-654">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-654">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="afb8f-655">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-655">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="afb8f-656">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-656">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="afb8f-657">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-657">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="afb8f-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="afb8f-659"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-659">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="afb8f-660">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-660">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="afb8f-661"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-661">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="afb8f-662">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-662">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="afb8f-663">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-663">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="afb8f-664">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="afb8f-664">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="afb8f-665">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="afb8f-665">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="afb8f-666">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-666">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="afb8f-667">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="afb8f-667">Access configuration during startup</span></span>

<span data-ttu-id="afb8f-668">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="afb8f-668">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="afb8f-669">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-669">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="afb8f-670">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-670">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="afb8f-671">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="afb8f-671">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="afb8f-672">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="afb8f-672">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="afb8f-673">Razor ページ: </span><span class="sxs-lookup"><span data-stu-id="afb8f-673">In a Razor Pages page:</span></span>

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

<span data-ttu-id="afb8f-674">MVC ビュー: </span><span class="sxs-lookup"><span data-stu-id="afb8f-674">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="afb8f-675">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="afb8f-675">Add configuration from an external assembly</span></span>

<span data-ttu-id="afb8f-676"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="afb8f-676">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="afb8f-677">詳細については、<xref:fundamentals/configuration/platform-specific-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afb8f-677">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="afb8f-678">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="afb8f-678">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
