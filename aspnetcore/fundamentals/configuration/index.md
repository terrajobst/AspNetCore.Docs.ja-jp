---
title: ASP.NET Core の構成
author: guardrex
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 9f0ad2791e504a0ff46daad07054b6bf909a546a
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634073"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8e8a1-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="8e8a1-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8e8a1-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8e8a1-105">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8e8a1-106">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8e8a1-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e8a1-107">Azure Key Vault</span></span>
* <span data-ttu-id="8e8a1-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8e8a1-108">Azure App Configuration</span></span>
* <span data-ttu-id="8e8a1-109">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8e8a1-109">Command-line arguments</span></span>
* <span data-ttu-id="8e8a1-110">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8e8a1-111">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="8e8a1-111">Directory files</span></span>
* <span data-ttu-id="8e8a1-112">環境変数</span><span class="sxs-lookup"><span data-stu-id="8e8a1-112">Environment variables</span></span>
* <span data-ttu-id="8e8a1-113">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="8e8a1-113">In-memory .NET objects</span></span>
* <span data-ttu-id="8e8a1-114">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="8e8a1-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e8a1-115">一般的な構成プロバイダーのシナリオの構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、フレームワークによって暗黙的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8e8a1-116">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="8e8a1-117">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8e8a1-118">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8e8a1-119">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8e8a1-120">詳細については、<xref:fundamentals/configuration/options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8e8a1-121">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8e8a1-122">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="8e8a1-122">Host versus app configuration</span></span>

<span data-ttu-id="8e8a1-123">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8e8a1-124">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8e8a1-125">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8e8a1-126">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8e8a1-127">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8e8a1-128">既定の構成</span><span class="sxs-lookup"><span data-stu-id="8e8a1-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e8a1-129">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8e8a1-130">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8e8a1-131">[汎用ホスト](xref:fundamentals/host/generic-host)を使用するアプリに以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="8e8a1-132">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="8e8a1-133">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8e8a1-134">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8e8a1-135">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8e8a1-136">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8e8a1-137">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8e8a1-138">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8e8a1-139">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8e8a1-140">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8e8a1-141">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-141">Enable IIS integration.</span></span>
* <span data-ttu-id="8e8a1-142">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="8e8a1-143">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8e8a1-144">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8e8a1-145">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8e8a1-146">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8e8a1-147">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8e8a1-148">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8e8a1-149">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8e8a1-150">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8e8a1-151">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8e8a1-152">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8e8a1-153">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8e8a1-154">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8e8a1-155">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8e8a1-156">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="8e8a1-157">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8e8a1-158">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8e8a1-159">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8e8a1-160">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8e8a1-161">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="8e8a1-162">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="8e8a1-162">Security</span></span>

<span data-ttu-id="8e8a1-163">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8e8a1-164">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8e8a1-165">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8e8a1-166">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8e8a1-167">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8e8a1-168"><xref:security/app-secrets> &ndash; には、環境変数を使用して機密データを格納する場合に関するアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8e8a1-169">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8e8a1-170">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8e8a1-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8e8a1-172">詳細については、<xref:security/key-vault-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8e8a1-173">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="8e8a1-173">Hierarchical configuration data</span></span>

<span data-ttu-id="8e8a1-174">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8e8a1-175">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="8e8a1-176">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8e8a1-177">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8e8a1-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-178">section0:key0</span></span>
* <span data-ttu-id="8e8a1-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-179">section0:key1</span></span>
* <span data-ttu-id="8e8a1-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-180">section1:key0</span></span>
* <span data-ttu-id="8e8a1-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-181">section1:key1</span></span>

<span data-ttu-id="8e8a1-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8e8a1-183">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8e8a1-184">規約</span><span class="sxs-lookup"><span data-stu-id="8e8a1-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8e8a1-185">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-185">Sources and providers</span></span>

<span data-ttu-id="8e8a1-186">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8e8a1-187">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8e8a1-188">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8e8a1-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8e8a1-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> を挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

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

<span data-ttu-id="8e8a1-191">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8e8a1-192">キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-192">Keys</span></span>

<span data-ttu-id="8e8a1-193">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8e8a1-194">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-194">Keys are case-insensitive.</span></span> <span data-ttu-id="8e8a1-195">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8e8a1-196">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="8e8a1-197">階層キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-197">Hierarchical keys</span></span>
  * <span data-ttu-id="8e8a1-198">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8e8a1-199">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8e8a1-200">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8e8a1-201">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8e8a1-202">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8e8a1-203"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8e8a1-204">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8e8a1-205">値</span><span class="sxs-lookup"><span data-stu-id="8e8a1-205">Values</span></span>

<span data-ttu-id="8e8a1-206">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8e8a1-207">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-207">Values are strings.</span></span>
* <span data-ttu-id="8e8a1-208">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8e8a1-209">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-209">Providers</span></span>

<span data-ttu-id="8e8a1-210">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8e8a1-211">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-211">Provider</span></span> | <span data-ttu-id="8e8a1-212">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="8e8a1-213">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="8e8a1-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e8a1-214">Azure Key Vault</span></span> |
| <span data-ttu-id="8e8a1-215">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="8e8a1-216">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8e8a1-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="8e8a1-217">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="8e8a1-218">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="8e8a1-218">Command-line parameters</span></span> |
| [<span data-ttu-id="8e8a1-219">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8e8a1-220">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="8e8a1-220">Custom source</span></span> |
| [<span data-ttu-id="8e8a1-221">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="8e8a1-222">環境変数</span><span class="sxs-lookup"><span data-stu-id="8e8a1-222">Environment variables</span></span> |
| [<span data-ttu-id="8e8a1-223">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8e8a1-224">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="8e8a1-225">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8e8a1-226">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="8e8a1-226">Directory files</span></span> |
| [<span data-ttu-id="8e8a1-227">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8e8a1-228">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="8e8a1-228">In-memory collections</span></span> |
| <span data-ttu-id="8e8a1-229">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="8e8a1-230">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="8e8a1-230">File in the user profile directory</span></span> |

<span data-ttu-id="8e8a1-231">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8e8a1-232">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="8e8a1-233">基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="8e8a1-234">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8e8a1-235">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8e8a1-236">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8e8a1-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8e8a1-237">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8e8a1-238">環境変数</span><span class="sxs-lookup"><span data-stu-id="8e8a1-238">Environment variables</span></span>
1. <span data-ttu-id="8e8a1-239">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="8e8a1-239">Command-line arguments</span></span>

<span data-ttu-id="8e8a1-240">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8e8a1-241">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8e8a1-242">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="8e8a1-243">ConfigureHostConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="8e8a1-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="8e8a1-244">ホスト ビルダーを構成するには、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出し、構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="8e8a1-245">`ConfigureHostConfiguration` は、後でビルド プロセスに使用する <xref:Microsoft.Extensions.Hosting.IHostEnvironment> を初期化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="8e8a1-246">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8e8a1-247">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="8e8a1-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8e8a1-248">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="8e8a1-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8e8a1-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8e8a1-250">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8e8a1-251">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="8e8a1-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8e8a1-252">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8e8a1-253">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="8e8a1-253">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8e8a1-254">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-254">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8e8a1-255">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="8e8a1-255">Consume configuration during app startup</span></span>

<span data-ttu-id="8e8a1-256">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-256">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8e8a1-257">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-257">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8e8a1-258">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-258">Command-line Configuration Provider</span></span>

<span data-ttu-id="8e8a1-259"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-259">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8e8a1-260">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-260">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8e8a1-261">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-261">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8e8a1-262">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-262">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8e8a1-263">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-263">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8e8a1-264">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-264">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8e8a1-265">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-265">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8e8a1-266">環境変数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-266">Environment variables.</span></span>

<span data-ttu-id="8e8a1-267">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-267">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8e8a1-268">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-268">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8e8a1-269">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-269">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8e8a1-270">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-270">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8e8a1-271">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-271">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8e8a1-272">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-272">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8e8a1-273">**例**</span><span class="sxs-lookup"><span data-stu-id="8e8a1-273">**Example**</span></span>

<span data-ttu-id="8e8a1-274">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-274">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8e8a1-275">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-275">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8e8a1-276">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-276">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8e8a1-277">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-277">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8e8a1-278">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-278">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8e8a1-279">引数</span><span class="sxs-lookup"><span data-stu-id="8e8a1-279">Arguments</span></span>

<span data-ttu-id="8e8a1-280">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-280">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8e8a1-281">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-281">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8e8a1-282">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8e8a1-282">Key prefix</span></span>               | <span data-ttu-id="8e8a1-283">例</span><span class="sxs-lookup"><span data-stu-id="8e8a1-283">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="8e8a1-284">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="8e8a1-284">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="8e8a1-285">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-285">Two dashes (`--`)</span></span>        | <span data-ttu-id="8e8a1-286">`--CommandLineKey2=value2`、 `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="8e8a1-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="8e8a1-287">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="8e8a1-287">Forward slash (`/`)</span></span>      | <span data-ttu-id="8e8a1-288">`/CommandLineKey3=value3`、 `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="8e8a1-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="8e8a1-289">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-289">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8e8a1-290">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-290">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8e8a1-291">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="8e8a1-291">Switch mappings</span></span>

<span data-ttu-id="8e8a1-292">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-292">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8e8a1-293"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-293">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8e8a1-294">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-294">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8e8a1-295">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-295">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8e8a1-296">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-296">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8e8a1-297">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-297">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8e8a1-298">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-298">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8e8a1-299">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-299">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8e8a1-300">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-300">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8e8a1-301">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-301">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8e8a1-302">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-302">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8e8a1-303">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-303">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8e8a1-304">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-304">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8e8a1-305">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-305">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8e8a1-306">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-306">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8e8a1-307">キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-307">Key</span></span>       | <span data-ttu-id="8e8a1-308">[値]</span><span class="sxs-lookup"><span data-stu-id="8e8a1-308">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="8e8a1-309">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-309">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8e8a1-310">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-310">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8e8a1-311">キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-311">Key</span></span>               | <span data-ttu-id="8e8a1-312">[値]</span><span class="sxs-lookup"><span data-stu-id="8e8a1-312">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8e8a1-313">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-313">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8e8a1-314"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-314">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8e8a1-315">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-315">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8e8a1-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure Portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8e8a1-317">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-317">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e8a1-318">新しいホスト ビルダーが[汎用ホスト](xref:fundamentals/host/generic-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `DOTNET_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8e8a1-319">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8e8a1-320">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-320">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8e8a1-321">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-321">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="8e8a1-322">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-322">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8e8a1-323">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-323">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8e8a1-324">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-324">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8e8a1-325">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-325">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8e8a1-326">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-326">Command-line arguments.</span></span>

<span data-ttu-id="8e8a1-327">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-327">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8e8a1-328">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-328">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8e8a1-329">追加の環境変数からアプリの構成を指定する必要がある場合は、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、そのプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-329">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

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

<span data-ttu-id="8e8a1-330">**例**</span><span class="sxs-lookup"><span data-stu-id="8e8a1-330">**Example**</span></span>

<span data-ttu-id="8e8a1-331">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-331">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8e8a1-332">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-332">Run the sample app.</span></span> <span data-ttu-id="8e8a1-333">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-333">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8e8a1-334">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-334">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8e8a1-335">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-335">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8e8a1-336">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-336">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8e8a1-337">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-337">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8e8a1-338">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-338">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8e8a1-339">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="8e8a1-339">Prefixes</span></span>

<span data-ttu-id="8e8a1-340">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-340">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8e8a1-341">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-341">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8e8a1-342">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-342">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8e8a1-343">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-343">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8e8a1-344">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-344">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8e8a1-345">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="8e8a1-345">**Connection string prefixes**</span></span>

<span data-ttu-id="8e8a1-346">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-346">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8e8a1-347">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-347">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8e8a1-348">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="8e8a1-348">Connection string prefix</span></span> | <span data-ttu-id="8e8a1-349">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-349">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8e8a1-350">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-350">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8e8a1-351">MySQL</span><span class="sxs-lookup"><span data-stu-id="8e8a1-351">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8e8a1-352">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="8e8a1-352">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8e8a1-353">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8e8a1-353">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8e8a1-354">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-354">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8e8a1-355">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-355">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8e8a1-356">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-356">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8e8a1-357">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-357">Environment variable key</span></span> | <span data-ttu-id="8e8a1-358">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-358">Converted configuration key</span></span> | <span data-ttu-id="8e8a1-359">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="8e8a1-359">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8e8a1-360">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-360">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8e8a1-361">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="8e8a1-362">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8e8a1-362">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8e8a1-363">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="8e8a1-364">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8e8a1-364">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8e8a1-365">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="8e8a1-366">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8e8a1-366">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="8e8a1-367">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-367">File Configuration Provider</span></span>

<span data-ttu-id="8e8a1-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8e8a1-369">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-369">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8e8a1-370">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-370">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8e8a1-371">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-371">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8e8a1-372">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-372">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8e8a1-373">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-373">INI Configuration Provider</span></span>

<span data-ttu-id="8e8a1-374"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-374">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8e8a1-375">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-375">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8e8a1-376">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-376">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8e8a1-377">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-377">Overloads permit specifying:</span></span>

* <span data-ttu-id="8e8a1-378">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-378">Whether the file is optional.</span></span>
* <span data-ttu-id="8e8a1-379">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-379">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8e8a1-380">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-380">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8e8a1-381">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8e8a1-382">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-382">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="8e8a1-383">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-383">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8e8a1-384">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-384">section0:key0</span></span>
* <span data-ttu-id="8e8a1-385">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-385">section0:key1</span></span>
* <span data-ttu-id="8e8a1-386">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="8e8a1-386">section1:subsection:key</span></span>
* <span data-ttu-id="8e8a1-387">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="8e8a1-387">section2:subsection0:key</span></span>
* <span data-ttu-id="8e8a1-388">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="8e8a1-388">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8e8a1-389">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-389">JSON Configuration Provider</span></span>

<span data-ttu-id="8e8a1-390"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-390">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8e8a1-391">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-391">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8e8a1-392">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-392">Overloads permit specifying:</span></span>

* <span data-ttu-id="8e8a1-393">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-393">Whether the file is optional.</span></span>
* <span data-ttu-id="8e8a1-394">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-394">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8e8a1-395">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-395">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8e8a1-396">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-396">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8e8a1-397">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-397">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8e8a1-398">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-398">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="8e8a1-399">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-399">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8e8a1-400">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-400">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8e8a1-401">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-401">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8e8a1-402">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-402">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8e8a1-403">環境変数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-403">Environment variables.</span></span>
* <span data-ttu-id="8e8a1-404">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-404">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8e8a1-405">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-405">Command-line arguments.</span></span>

<span data-ttu-id="8e8a1-406">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-406">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8e8a1-407">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-407">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8e8a1-408">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-408">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8e8a1-409">**例**</span><span class="sxs-lookup"><span data-stu-id="8e8a1-409">**Example**</span></span>

<span data-ttu-id="8e8a1-410">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="8e8a1-411">構成は、*appsettings.json* および *appsettings.{Environment}.json* から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="8e8a1-412">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-412">Run the sample app.</span></span> <span data-ttu-id="8e8a1-413">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8e8a1-414">出力に、環境に応じて、表に示す構成のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="8e8a1-415">ログの構成キーでは、階層の区切り文字としてコロン (`:`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="8e8a1-416">キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-416">Key</span></span>                        | <span data-ttu-id="8e8a1-417">開発時の値</span><span class="sxs-lookup"><span data-stu-id="8e8a1-417">Development Value</span></span> | <span data-ttu-id="8e8a1-418">運用時の値</span><span class="sxs-lookup"><span data-stu-id="8e8a1-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="8e8a1-419">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="8e8a1-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="8e8a1-420">情報</span><span class="sxs-lookup"><span data-stu-id="8e8a1-420">Information</span></span>       | <span data-ttu-id="8e8a1-421">情報</span><span class="sxs-lookup"><span data-stu-id="8e8a1-421">Information</span></span>      |
| <span data-ttu-id="8e8a1-422">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="8e8a1-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="8e8a1-423">情報</span><span class="sxs-lookup"><span data-stu-id="8e8a1-423">Information</span></span>       | <span data-ttu-id="8e8a1-424">情報</span><span class="sxs-lookup"><span data-stu-id="8e8a1-424">Information</span></span>      |
| <span data-ttu-id="8e8a1-425">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="8e8a1-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="8e8a1-426">デバッグ</span><span class="sxs-lookup"><span data-stu-id="8e8a1-426">Debug</span></span>             | <span data-ttu-id="8e8a1-427">Error</span><span class="sxs-lookup"><span data-stu-id="8e8a1-427">Error</span></span>            |
| <span data-ttu-id="8e8a1-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="8e8a1-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="8e8a1-429">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-429">XML Configuration Provider</span></span>

<span data-ttu-id="8e8a1-430"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8e8a1-431">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8e8a1-432">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="8e8a1-433">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-433">Whether the file is optional.</span></span>
* <span data-ttu-id="8e8a1-434">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8e8a1-435">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8e8a1-436">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8e8a1-437">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8e8a1-438">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8e8a1-439">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-439">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="8e8a1-440">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8e8a1-441">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-441">section0:key0</span></span>
* <span data-ttu-id="8e8a1-442">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-442">section0:key1</span></span>
* <span data-ttu-id="8e8a1-443">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-443">section1:key0</span></span>
* <span data-ttu-id="8e8a1-444">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-444">section1:key1</span></span>

<span data-ttu-id="8e8a1-445">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-445">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="8e8a1-446">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-446">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8e8a1-447">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-447">section:section0:key:key0</span></span>
* <span data-ttu-id="8e8a1-448">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-448">section:section0:key:key1</span></span>
* <span data-ttu-id="8e8a1-449">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-449">section:section1:key:key0</span></span>
* <span data-ttu-id="8e8a1-450">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-450">section:section1:key:key1</span></span>

<span data-ttu-id="8e8a1-451">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-451">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8e8a1-452">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-452">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8e8a1-453">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8e8a1-453">key:attribute</span></span>
* <span data-ttu-id="8e8a1-454">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8e8a1-454">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8e8a1-455">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-455">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8e8a1-456"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-456">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8e8a1-457">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-457">The key is the file name.</span></span> <span data-ttu-id="8e8a1-458">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-458">The value contains the file's contents.</span></span> <span data-ttu-id="8e8a1-459">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-459">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8e8a1-460">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-460">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8e8a1-461">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-461">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8e8a1-462">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-462">Overloads permit specifying:</span></span>

* <span data-ttu-id="8e8a1-463">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-463">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8e8a1-464">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-464">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8e8a1-465">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-465">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8e8a1-466">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-466">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8e8a1-467">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-467">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8e8a1-468">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-468">Memory Configuration Provider</span></span>

<span data-ttu-id="8e8a1-469"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-469">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8e8a1-470">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-470">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8e8a1-471">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-471">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8e8a1-472">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-472">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8e8a1-473">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-473">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8e8a1-474">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-474">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8e8a1-475">GetValue</span><span class="sxs-lookup"><span data-stu-id="8e8a1-475">GetValue</span></span>

<span data-ttu-id="8e8a1-476">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) によって、指定したキーで構成から単一の値が抽出され、それが指定したコレクション以外の型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-476">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8e8a1-477">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-477">An overload accepts a default value.</span></span>

<span data-ttu-id="8e8a1-478">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-478">The following example:</span></span>

* <span data-ttu-id="8e8a1-479">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-479">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8e8a1-480">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-480">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8e8a1-481">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-481">Types the value as an `int`.</span></span>
* <span data-ttu-id="8e8a1-482">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-482">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8e8a1-483">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="8e8a1-483">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8e8a1-484">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-484">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8e8a1-485">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-485">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="8e8a1-486">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-486">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8e8a1-487">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-487">section0:key0</span></span>
* <span data-ttu-id="8e8a1-488">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-488">section0:key1</span></span>
* <span data-ttu-id="8e8a1-489">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-489">section1:key0</span></span>
* <span data-ttu-id="8e8a1-490">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-490">section1:key1</span></span>
* <span data-ttu-id="8e8a1-491">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-491">section2:subsection0:key0</span></span>
* <span data-ttu-id="8e8a1-492">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-492">section2:subsection0:key1</span></span>
* <span data-ttu-id="8e8a1-493">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-493">section2:subsection1:key0</span></span>
* <span data-ttu-id="8e8a1-494">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-494">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8e8a1-495">GetSection</span><span class="sxs-lookup"><span data-stu-id="8e8a1-495">GetSection</span></span>

<span data-ttu-id="8e8a1-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8e8a1-497">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-497">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8e8a1-498">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-498">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8e8a1-499">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-499">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8e8a1-500">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-500">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8e8a1-501">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-501">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8e8a1-502">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-502">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8e8a1-503"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-503">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8e8a1-504">GetChildren</span><span class="sxs-lookup"><span data-stu-id="8e8a1-504">GetChildren</span></span>

<span data-ttu-id="8e8a1-505">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-505">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8e8a1-506">既存</span><span class="sxs-lookup"><span data-stu-id="8e8a1-506">Exists</span></span>

<span data-ttu-id="8e8a1-507">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-507">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8e8a1-508">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-508">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="8e8a1-509">クラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="8e8a1-509">Bind to a class</span></span>

<span data-ttu-id="8e8a1-510">"*オプション パターン*" を使用して、関連する設定のグループを表すクラスに構成をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-510">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="8e8a1-511">詳細については、<xref:fundamentals/configuration/options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-511">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8e8a1-512">構成値は文字列として返されますが、<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> を呼び出すことで [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) オブジェクトを構築できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-512">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="8e8a1-513">サンプル アプリには `Starship` モデル (*Models/Starship.cs*) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-513">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8e8a1-514">サンプル アプリが JSON 構成プロバイダーを使用して構成を読み込むときに、*starship.json* ファイルの `starship` セクションで構成が作成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-514">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="8e8a1-515">次の構成のキーと値のペアが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-515">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="8e8a1-516">キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-516">Key</span></span>                   | <span data-ttu-id="8e8a1-517">[値]</span><span class="sxs-lookup"><span data-stu-id="8e8a1-517">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="8e8a1-518">starship:name</span><span class="sxs-lookup"><span data-stu-id="8e8a1-518">starship:name</span></span>         | <span data-ttu-id="8e8a1-519">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="8e8a1-519">USS Enterprise</span></span>                                    |
| <span data-ttu-id="8e8a1-520">starship:registry</span><span class="sxs-lookup"><span data-stu-id="8e8a1-520">starship:registry</span></span>     | <span data-ttu-id="8e8a1-521">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="8e8a1-521">NCC-1701</span></span>                                          |
| <span data-ttu-id="8e8a1-522">starship:class</span><span class="sxs-lookup"><span data-stu-id="8e8a1-522">starship:class</span></span>        | <span data-ttu-id="8e8a1-523">Constitution</span><span class="sxs-lookup"><span data-stu-id="8e8a1-523">Constitution</span></span>                                      |
| <span data-ttu-id="8e8a1-524">starship:length</span><span class="sxs-lookup"><span data-stu-id="8e8a1-524">starship:length</span></span>       | <span data-ttu-id="8e8a1-525">304.8</span><span class="sxs-lookup"><span data-stu-id="8e8a1-525">304.8</span></span>                                             |
| <span data-ttu-id="8e8a1-526">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="8e8a1-526">starship:commissioned</span></span> | <span data-ttu-id="8e8a1-527">False</span><span class="sxs-lookup"><span data-stu-id="8e8a1-527">False</span></span>                                             |
| <span data-ttu-id="8e8a1-528">trademark</span><span class="sxs-lookup"><span data-stu-id="8e8a1-528">trademark</span></span>             | <span data-ttu-id="8e8a1-529">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="8e8a1-529">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="8e8a1-530">サンプル アプリは `starship` キーを使用して `GetSection` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-530">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="8e8a1-531">`starship` のキーと値のペアは分離されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-531">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="8e8a1-532">`Starship` クラスのインスタンスを渡すサブセクションで、`Bind` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-532">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="8e8a1-533">インスタンスの値をバインドした後、インスタンスがレンダリング用のプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-533">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8e8a1-534">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="8e8a1-534">Bind to an object graph</span></span>

<span data-ttu-id="8e8a1-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="8e8a1-536">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-536">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8e8a1-537">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-537">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="8e8a1-538">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-538">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8e8a1-539">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-539">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8e8a1-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) によってバインドされ、指定した型が返されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8e8a1-541">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-541">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8e8a1-542">次のコードは、前の例と共に `Get<T>` を使用する方法を示しています。これにより、バインドされたインスタンスを、表示用に使用するプロパティに直接割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-542">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8e8a1-543">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="8e8a1-543">Bind an array to a class</span></span>

<span data-ttu-id="8e8a1-544">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="8e8a1-544">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8e8a1-545"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-545">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8e8a1-546">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-546">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8e8a1-547">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-547">Binding is provided by convention.</span></span> <span data-ttu-id="8e8a1-548">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-548">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8e8a1-549">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="8e8a1-549">**In-memory array processing**</span></span>

<span data-ttu-id="8e8a1-550">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-550">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8e8a1-551">キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-551">Key</span></span>             | <span data-ttu-id="8e8a1-552">[値]</span><span class="sxs-lookup"><span data-stu-id="8e8a1-552">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8e8a1-553">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-553">array:entries:0</span></span> | <span data-ttu-id="8e8a1-554">value0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-554">value0</span></span> |
| <span data-ttu-id="8e8a1-555">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-555">array:entries:1</span></span> | <span data-ttu-id="8e8a1-556">value1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-556">value1</span></span> |
| <span data-ttu-id="8e8a1-557">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="8e8a1-557">array:entries:2</span></span> | <span data-ttu-id="8e8a1-558">value2</span><span class="sxs-lookup"><span data-stu-id="8e8a1-558">value2</span></span> |
| <span data-ttu-id="8e8a1-559">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="8e8a1-559">array:entries:4</span></span> | <span data-ttu-id="8e8a1-560">value4</span><span class="sxs-lookup"><span data-stu-id="8e8a1-560">value4</span></span> |
| <span data-ttu-id="8e8a1-561">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="8e8a1-561">array:entries:5</span></span> | <span data-ttu-id="8e8a1-562">value5</span><span class="sxs-lookup"><span data-stu-id="8e8a1-562">value5</span></span> |

<span data-ttu-id="8e8a1-563">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-563">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="8e8a1-564">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-564">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8e8a1-565">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-565">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8e8a1-566">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-566">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8e8a1-567">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-567">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8e8a1-568">また、[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用して、コードをよりコンパクトにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-568">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="8e8a1-569">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-569">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8e8a1-570">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="8e8a1-570">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8e8a1-571">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="8e8a1-571">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8e8a1-572">0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-572">0</span></span>                            | <span data-ttu-id="8e8a1-573">value0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-573">value0</span></span>                       |
| <span data-ttu-id="8e8a1-574">1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-574">1</span></span>                            | <span data-ttu-id="8e8a1-575">value1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-575">value1</span></span>                       |
| <span data-ttu-id="8e8a1-576">2</span><span class="sxs-lookup"><span data-stu-id="8e8a1-576">2</span></span>                            | <span data-ttu-id="8e8a1-577">value2</span><span class="sxs-lookup"><span data-stu-id="8e8a1-577">value2</span></span>                       |
| <span data-ttu-id="8e8a1-578">3</span><span class="sxs-lookup"><span data-stu-id="8e8a1-578">3</span></span>                            | <span data-ttu-id="8e8a1-579">value4</span><span class="sxs-lookup"><span data-stu-id="8e8a1-579">value4</span></span>                       |
| <span data-ttu-id="8e8a1-580">4</span><span class="sxs-lookup"><span data-stu-id="8e8a1-580">4</span></span>                            | <span data-ttu-id="8e8a1-581">value5</span><span class="sxs-lookup"><span data-stu-id="8e8a1-581">value5</span></span>                       |

<span data-ttu-id="8e8a1-582">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-582">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8e8a1-583">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-583">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8e8a1-584">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-584">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8e8a1-585">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-585">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8e8a1-586">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-586">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8e8a1-587">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-587">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8e8a1-588">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-588">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8e8a1-589">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-589">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8e8a1-590">キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-590">Key</span></span>             | <span data-ttu-id="8e8a1-591">[値]</span><span class="sxs-lookup"><span data-stu-id="8e8a1-591">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8e8a1-592">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="8e8a1-592">array:entries:3</span></span> | <span data-ttu-id="8e8a1-593">value3</span><span class="sxs-lookup"><span data-stu-id="8e8a1-593">value3</span></span> |

<span data-ttu-id="8e8a1-594">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-594">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8e8a1-595">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="8e8a1-595">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8e8a1-596">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="8e8a1-596">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8e8a1-597">0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-597">0</span></span>                            | <span data-ttu-id="8e8a1-598">value0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-598">value0</span></span>                       |
| <span data-ttu-id="8e8a1-599">1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-599">1</span></span>                            | <span data-ttu-id="8e8a1-600">value1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-600">value1</span></span>                       |
| <span data-ttu-id="8e8a1-601">2</span><span class="sxs-lookup"><span data-stu-id="8e8a1-601">2</span></span>                            | <span data-ttu-id="8e8a1-602">value2</span><span class="sxs-lookup"><span data-stu-id="8e8a1-602">value2</span></span>                       |
| <span data-ttu-id="8e8a1-603">3</span><span class="sxs-lookup"><span data-stu-id="8e8a1-603">3</span></span>                            | <span data-ttu-id="8e8a1-604">value3</span><span class="sxs-lookup"><span data-stu-id="8e8a1-604">value3</span></span>                       |
| <span data-ttu-id="8e8a1-605">4</span><span class="sxs-lookup"><span data-stu-id="8e8a1-605">4</span></span>                            | <span data-ttu-id="8e8a1-606">value4</span><span class="sxs-lookup"><span data-stu-id="8e8a1-606">value4</span></span>                       |
| <span data-ttu-id="8e8a1-607">5</span><span class="sxs-lookup"><span data-stu-id="8e8a1-607">5</span></span>                            | <span data-ttu-id="8e8a1-608">value5</span><span class="sxs-lookup"><span data-stu-id="8e8a1-608">value5</span></span>                       |

<span data-ttu-id="8e8a1-609">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="8e8a1-609">**JSON array processing**</span></span>

<span data-ttu-id="8e8a1-610">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-610">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8e8a1-611">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-611">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="8e8a1-612">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-612">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8e8a1-613">キー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-613">Key</span></span>                     | <span data-ttu-id="8e8a1-614">[値]</span><span class="sxs-lookup"><span data-stu-id="8e8a1-614">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="8e8a1-615">json_array:key</span><span class="sxs-lookup"><span data-stu-id="8e8a1-615">json_array:key</span></span>          | <span data-ttu-id="8e8a1-616">valueA</span><span class="sxs-lookup"><span data-stu-id="8e8a1-616">valueA</span></span> |
| <span data-ttu-id="8e8a1-617">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-617">json_array:subsection:0</span></span> | <span data-ttu-id="8e8a1-618">valueB</span><span class="sxs-lookup"><span data-stu-id="8e8a1-618">valueB</span></span> |
| <span data-ttu-id="8e8a1-619">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-619">json_array:subsection:1</span></span> | <span data-ttu-id="8e8a1-620">valueC</span><span class="sxs-lookup"><span data-stu-id="8e8a1-620">valueC</span></span> |
| <span data-ttu-id="8e8a1-621">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="8e8a1-621">json_array:subsection:2</span></span> | <span data-ttu-id="8e8a1-622">valueD</span><span class="sxs-lookup"><span data-stu-id="8e8a1-622">valueD</span></span> |

<span data-ttu-id="8e8a1-623">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-623">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8e8a1-624">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-624">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8e8a1-625">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-625">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8e8a1-626">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="8e8a1-626">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8e8a1-627">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="8e8a1-627">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="8e8a1-628">0</span><span class="sxs-lookup"><span data-stu-id="8e8a1-628">0</span></span>                                   | <span data-ttu-id="8e8a1-629">valueB</span><span class="sxs-lookup"><span data-stu-id="8e8a1-629">valueB</span></span>                              |
| <span data-ttu-id="8e8a1-630">1</span><span class="sxs-lookup"><span data-stu-id="8e8a1-630">1</span></span>                                   | <span data-ttu-id="8e8a1-631">valueC</span><span class="sxs-lookup"><span data-stu-id="8e8a1-631">valueC</span></span>                              |
| <span data-ttu-id="8e8a1-632">2</span><span class="sxs-lookup"><span data-stu-id="8e8a1-632">2</span></span>                                   | <span data-ttu-id="8e8a1-633">valueD</span><span class="sxs-lookup"><span data-stu-id="8e8a1-633">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="8e8a1-634">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="8e8a1-634">Custom configuration provider</span></span>

<span data-ttu-id="8e8a1-635">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-635">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8e8a1-636">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-636">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8e8a1-637">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-637">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8e8a1-638">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-638">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8e8a1-639">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-639">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8e8a1-640">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-640">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8e8a1-641">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-641">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8e8a1-642">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-642">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e8a1-643">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-643">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8e8a1-644">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-644">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8e8a1-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8e8a1-646"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-646">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8e8a1-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8e8a1-648"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-648">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8e8a1-649">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-649">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8e8a1-650">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-650">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8e8a1-651">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-651">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8e8a1-652">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-652">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8e8a1-653">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-653">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8e8a1-654">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-654">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8e8a1-655">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-655">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8e8a1-656">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-656">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8e8a1-657">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-657">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8e8a1-658"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-658">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8e8a1-659">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-659">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8e8a1-660"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-660">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8e8a1-661">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-661">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8e8a1-662">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-662">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8e8a1-663">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-663">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8e8a1-664">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-664">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8e8a1-665">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-665">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8e8a1-666">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="8e8a1-666">Access configuration during startup</span></span>

<span data-ttu-id="8e8a1-667">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-667">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8e8a1-668">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-668">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="8e8a1-669">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-669">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="8e8a1-670">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="8e8a1-670">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8e8a1-671">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-671">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8e8a1-672">Razor ページ:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-672">In a Razor Pages page:</span></span>

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

<span data-ttu-id="8e8a1-673">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="8e8a1-673">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8e8a1-674">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="8e8a1-674">Add configuration from an external assembly</span></span>

<span data-ttu-id="8e8a1-675"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-675">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8e8a1-676">詳細については、<xref:fundamentals/configuration/platform-specific-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8e8a1-676">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e8a1-677">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8e8a1-677">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
