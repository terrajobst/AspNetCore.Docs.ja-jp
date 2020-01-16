---
title: ASP.NET Core の構成
author: guardrex
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 09ef06f179e34cd7f4f04ac30c3b5dd95d058244
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75951890"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="0be0b-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="0be0b-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="0be0b-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0be0b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0be0b-105">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="0be0b-106">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="0be0b-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0be0b-107">Azure Key Vault</span></span>
* <span data-ttu-id="0be0b-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="0be0b-108">Azure App Configuration</span></span>
* <span data-ttu-id="0be0b-109">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="0be0b-109">Command-line arguments</span></span>
* <span data-ttu-id="0be0b-110">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="0be0b-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="0be0b-111">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="0be0b-111">Directory files</span></span>
* <span data-ttu-id="0be0b-112">環境変数</span><span class="sxs-lookup"><span data-stu-id="0be0b-112">Environment variables</span></span>
* <span data-ttu-id="0be0b-113">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="0be0b-113">In-memory .NET objects</span></span>
* <span data-ttu-id="0be0b-114">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="0be0b-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0be0b-115">一般的な構成プロバイダーのシナリオの構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、フレームワークによって暗黙的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0be0b-116">一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="0be0b-117">以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。</span><span class="sxs-lookup"><span data-stu-id="0be0b-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="0be0b-118">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="0be0b-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="0be0b-119">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="0be0b-120">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="0be0b-121">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0be0b-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="0be0b-122">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="0be0b-122">Host versus app configuration</span></span>

<span data-ttu-id="0be0b-123">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="0be0b-124">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="0be0b-125">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="0be0b-126">ホスト構成のキーと値のペアも、アプリの構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="0be0b-127">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="0be0b-128">既定の構成</span><span class="sxs-lookup"><span data-stu-id="0be0b-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0be0b-129">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="0be0b-130">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="0be0b-131">[汎用ホスト](xref:fundamentals/host/generic-host)を使用するアプリに以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="0be0b-132">[Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="0be0b-133">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="0be0b-134">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="0be0b-135">構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="0be0b-136">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="0be0b-137">Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="0be0b-138">Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="0be0b-139">Host Filtering Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="0be0b-140">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="0be0b-141">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="0be0b-141">Enable IIS integration.</span></span>
* <span data-ttu-id="0be0b-142">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="0be0b-143">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="0be0b-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="0be0b-144">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="0be0b-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="0be0b-145">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="0be0b-146">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="0be0b-147">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0be0b-148">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="0be0b-149">`CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="0be0b-150">[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="0be0b-151">[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="0be0b-152">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="0be0b-153">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="0be0b-154">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="0be0b-155">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="0be0b-156">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="0be0b-157">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="0be0b-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="0be0b-158">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="0be0b-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="0be0b-159">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="0be0b-160">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="0be0b-161">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="0be0b-162">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="0be0b-162">Security</span></span>

<span data-ttu-id="0be0b-163">機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="0be0b-164">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="0be0b-165">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="0be0b-166">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="0be0b-167">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="0be0b-168"><xref:security/app-secrets> &ndash; 環境変数を使用して機密データを格納する場合に関するアドバイスが記載されています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="0be0b-169">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="0be0b-170">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="0be0b-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="0be0b-172">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="0be0b-173">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="0be0b-173">Hierarchical configuration data</span></span>

<span data-ttu-id="0be0b-174">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="0be0b-175">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="0be0b-176">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="0be0b-177">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="0be0b-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-178">section0:key0</span></span>
* <span data-ttu-id="0be0b-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-179">section0:key1</span></span>
* <span data-ttu-id="0be0b-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-180">section1:key0</span></span>
* <span data-ttu-id="0be0b-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-181">section1:key1</span></span>

<span data-ttu-id="0be0b-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="0be0b-183">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="0be0b-184">規約</span><span class="sxs-lookup"><span data-stu-id="0be0b-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="0be0b-185">ソースとプロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-185">Sources and providers</span></span>

<span data-ttu-id="0be0b-186">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="0be0b-187">変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="0be0b-188">たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="0be0b-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0be0b-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> または MVC <xref:Microsoft.AspNetCore.Mvc.Controller> に挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="0be0b-191">次の例では、構成値にアクセスするために `_config` フィールドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-191">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="0be0b-192">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="0be0b-192">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="0be0b-193">キー</span><span class="sxs-lookup"><span data-stu-id="0be0b-193">Keys</span></span>

<span data-ttu-id="0be0b-194">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-194">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="0be0b-195">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-195">Keys are case-insensitive.</span></span> <span data-ttu-id="0be0b-196">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-196">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="0be0b-197">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-197">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="0be0b-198">階層キー</span><span class="sxs-lookup"><span data-stu-id="0be0b-198">Hierarchical keys</span></span>
  * <span data-ttu-id="0be0b-199">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-199">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="0be0b-200">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-200">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="0be0b-201">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-201">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="0be0b-202">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-202">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="0be0b-203">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-203">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="0be0b-204"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-204">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="0be0b-205">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-205">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="0be0b-206">値</span><span class="sxs-lookup"><span data-stu-id="0be0b-206">Values</span></span>

<span data-ttu-id="0be0b-207">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-207">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="0be0b-208">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-208">Values are strings.</span></span>
* <span data-ttu-id="0be0b-209">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-209">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="0be0b-210">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-210">Providers</span></span>

<span data-ttu-id="0be0b-211">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-211">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="0be0b-212">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-212">Provider</span></span> | <span data-ttu-id="0be0b-213">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-213">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="0be0b-214">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="0be0b-214">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="0be0b-215">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0be0b-215">Azure Key Vault</span></span> |
| <span data-ttu-id="0be0b-216">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント)</span><span class="sxs-lookup"><span data-stu-id="0be0b-216">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="0be0b-217">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="0be0b-217">Azure App Configuration</span></span> |
| [<span data-ttu-id="0be0b-218">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-218">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="0be0b-219">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="0be0b-219">Command-line parameters</span></span> |
| [<span data-ttu-id="0be0b-220">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-220">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="0be0b-221">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="0be0b-221">Custom source</span></span> |
| [<span data-ttu-id="0be0b-222">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-222">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="0be0b-223">環境変数</span><span class="sxs-lookup"><span data-stu-id="0be0b-223">Environment variables</span></span> |
| [<span data-ttu-id="0be0b-224">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-224">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="0be0b-225">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="0be0b-225">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="0be0b-226">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-226">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="0be0b-227">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="0be0b-227">Directory files</span></span> |
| [<span data-ttu-id="0be0b-228">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-228">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="0be0b-229">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="0be0b-229">In-memory collections</span></span> |
| <span data-ttu-id="0be0b-230">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="0be0b-230">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="0be0b-231">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="0be0b-231">File in the user profile directory</span></span> |

<span data-ttu-id="0be0b-232">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-232">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="0be0b-233">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-233">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="0be0b-234">アプリで必要とされる、基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-234">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="0be0b-235">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0be0b-235">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="0be0b-236">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="0be0b-236">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="0be0b-237">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0be0b-237">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="0be0b-238">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)</span><span class="sxs-lookup"><span data-stu-id="0be0b-238">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="0be0b-239">環境変数</span><span class="sxs-lookup"><span data-stu-id="0be0b-239">Environment variables</span></span>
1. <span data-ttu-id="0be0b-240">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="0be0b-240">Command-line arguments</span></span>

<span data-ttu-id="0be0b-241">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-241">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="0be0b-242">この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-242">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0be0b-243">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-243">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="0be0b-244">ConfigureHostConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="0be0b-244">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="0be0b-245">ホスト ビルダーを構成するには、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出し、構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-245">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="0be0b-246">`ConfigureHostConfiguration` は、後でビルド プロセスに使用する <xref:Microsoft.Extensions.Hosting.IHostEnvironment> を初期化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-246">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="0be0b-247">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-247">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="0be0b-248">UseConfiguration を使用してホスト ビルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="0be0b-248">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="0be0b-249">ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-249">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="0be0b-250">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="0be0b-250">ConfigureAppConfiguration</span></span>

<span data-ttu-id="0be0b-251">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-251">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="0be0b-252">前の構成をコマンドライン引数でオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="0be0b-252">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="0be0b-253">コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-253">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="0be0b-254">CreateDefaultBuilder によって追加されたプロバイダーの削除</span><span class="sxs-lookup"><span data-stu-id="0be0b-254">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="0be0b-255">`CreateDefaultBuilder` によって追加されたプロバイダーを削除するには、最初に [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) の [クリア](/dotnet/api/system.collections.generic.icollection-1.clear) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-255">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="0be0b-256">アプリの起動時に構成を使用する</span><span class="sxs-lookup"><span data-stu-id="0be0b-256">Consume configuration during app startup</span></span>

<span data-ttu-id="0be0b-257">`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-257">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0be0b-258">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-258">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="0be0b-259">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-259">Command-line Configuration Provider</span></span>

<span data-ttu-id="0be0b-260"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-260">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="0be0b-261">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-261">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0be0b-262">`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-262">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="0be0b-263">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-263">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="0be0b-264">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-264">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="0be0b-265">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="0be0b-265">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="0be0b-266">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-266">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="0be0b-267">環境変数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-267">Environment variables.</span></span>

<span data-ttu-id="0be0b-268">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-268">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="0be0b-269">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-269">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="0be0b-270">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-270">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="0be0b-271">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-271">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="0be0b-272">ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-272">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0be0b-273">さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-273">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="0be0b-274">**例**</span><span class="sxs-lookup"><span data-stu-id="0be0b-274">**Example**</span></span>

<span data-ttu-id="0be0b-275">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-275">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="0be0b-276">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-276">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="0be0b-277">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-277">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="0be0b-278">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-278">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="0be0b-279">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-279">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="0be0b-280">引数</span><span class="sxs-lookup"><span data-stu-id="0be0b-280">Arguments</span></span>

<span data-ttu-id="0be0b-281">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-281">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="0be0b-282">等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-282">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="0be0b-283">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="0be0b-283">Key prefix</span></span>               | <span data-ttu-id="0be0b-284">例</span><span class="sxs-lookup"><span data-stu-id="0be0b-284">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="0be0b-285">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="0be0b-285">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="0be0b-286">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="0be0b-286">Two dashes (`--`)</span></span>        | <span data-ttu-id="0be0b-287">`--CommandLineKey2=value2`、`--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="0be0b-287">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="0be0b-288">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="0be0b-288">Forward slash (`/`)</span></span>      | <span data-ttu-id="0be0b-289">`/CommandLineKey3=value3`、`/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="0be0b-289">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="0be0b-290">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-290">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="0be0b-291">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="0be0b-291">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="0be0b-292">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="0be0b-292">Switch mappings</span></span>

<span data-ttu-id="0be0b-293">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-293">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="0be0b-294"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-294">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="0be0b-295">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-295">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="0be0b-296">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-296">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="0be0b-297">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-297">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="0be0b-298">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="0be0b-298">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="0be0b-299">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-299">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="0be0b-300">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-300">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="0be0b-301">スイッチ マッピング ディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-301">Create a switch mappings dictionary.</span></span> <span data-ttu-id="0be0b-302">次の例では、2 つのスイッチ マッピングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-302">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="0be0b-303">ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-303">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="0be0b-304">スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-304">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="0be0b-305">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-305">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0be0b-306">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-306">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="0be0b-307">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-307">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="0be0b-308">Key</span><span class="sxs-lookup"><span data-stu-id="0be0b-308">Key</span></span>       | <span data-ttu-id="0be0b-309">[値]</span><span class="sxs-lookup"><span data-stu-id="0be0b-309">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="0be0b-310">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-310">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="0be0b-311">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-311">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="0be0b-312">Key</span><span class="sxs-lookup"><span data-stu-id="0be0b-312">Key</span></span>               | <span data-ttu-id="0be0b-313">[値]</span><span class="sxs-lookup"><span data-stu-id="0be0b-313">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="0be0b-314">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-314">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="0be0b-315"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-315">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="0be0b-316">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-316">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="0be0b-317">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-317">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="0be0b-318">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-318">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0be0b-319">新しいホスト ビルダーが[汎用ホスト](xref:fundamentals/host/generic-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `DOTNET_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-319">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="0be0b-320">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-320">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0be0b-321">新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-321">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="0be0b-322">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-322">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="0be0b-323">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-323">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="0be0b-324">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="0be0b-324">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="0be0b-325">*appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。</span><span class="sxs-lookup"><span data-stu-id="0be0b-325">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="0be0b-326">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-326">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="0be0b-327">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-327">Command-line arguments.</span></span>

<span data-ttu-id="0be0b-328">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-328">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="0be0b-329">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-329">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="0be0b-330">追加の環境変数からアプリの構成を指定するには、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、次のプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-330">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="0be0b-331">`AddEnvironmentVariables` を最後に呼び出して、指定されたプレフィックスを持つ環境変数が他のプロバイダーの値をオーバーライドできるようにします。</span><span class="sxs-lookup"><span data-stu-id="0be0b-331">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="0be0b-332">**例**</span><span class="sxs-lookup"><span data-stu-id="0be0b-332">**Example**</span></span>

<span data-ttu-id="0be0b-333">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-333">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="0be0b-334">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-334">Run the sample app.</span></span> <span data-ttu-id="0be0b-335">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-335">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="0be0b-336">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-336">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="0be0b-337">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-337">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="0be0b-338">アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-338">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="0be0b-339">サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-339">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="0be0b-340">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-340">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="0be0b-341">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="0be0b-341">Prefixes</span></span>

<span data-ttu-id="0be0b-342">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-342">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="0be0b-343">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-343">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="0be0b-344">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-344">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="0be0b-345">ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-345">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="0be0b-346">これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-346">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="0be0b-347">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="0be0b-347">**Connection string prefixes**</span></span>

<span data-ttu-id="0be0b-348">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-348">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="0be0b-349">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-349">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="0be0b-350">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="0be0b-350">Connection string prefix</span></span> | <span data-ttu-id="0be0b-351">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-351">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="0be0b-352">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-352">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="0be0b-353">MySQL</span><span class="sxs-lookup"><span data-stu-id="0be0b-353">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="0be0b-354">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="0be0b-354">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="0be0b-355">SQL Server</span><span class="sxs-lookup"><span data-stu-id="0be0b-355">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="0be0b-356">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="0be0b-356">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="0be0b-357">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-357">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="0be0b-358">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-358">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="0be0b-359">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="0be0b-359">Environment variable key</span></span> | <span data-ttu-id="0be0b-360">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="0be0b-360">Converted configuration key</span></span> | <span data-ttu-id="0be0b-361">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="0be0b-361">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="0be0b-362">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-362">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="0be0b-363">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="0be0b-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="0be0b-364">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="0be0b-364">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="0be0b-365">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="0be0b-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="0be0b-366">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="0be0b-366">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="0be0b-367">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="0be0b-367">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="0be0b-368">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="0be0b-368">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="0be0b-369">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-369">File Configuration Provider</span></span>

<span data-ttu-id="0be0b-370"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="0be0b-370"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="0be0b-371">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-371">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="0be0b-372">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-372">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="0be0b-373">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-373">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="0be0b-374">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-374">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="0be0b-375">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-375">INI Configuration Provider</span></span>

<span data-ttu-id="0be0b-376"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-376">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="0be0b-377">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-377">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0be0b-378">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-378">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="0be0b-379">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-379">Overloads permit specifying:</span></span>

* <span data-ttu-id="0be0b-380">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="0be0b-380">Whether the file is optional.</span></span>
* <span data-ttu-id="0be0b-381">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="0be0b-381">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="0be0b-382">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-382">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="0be0b-383">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-383">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="0be0b-384">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="0be0b-384">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="0be0b-385">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-385">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="0be0b-386">section0:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-386">section0:key0</span></span>
* <span data-ttu-id="0be0b-387">section0:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-387">section0:key1</span></span>
* <span data-ttu-id="0be0b-388">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="0be0b-388">section1:subsection:key</span></span>
* <span data-ttu-id="0be0b-389">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="0be0b-389">section2:subsection0:key</span></span>
* <span data-ttu-id="0be0b-390">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="0be0b-390">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="0be0b-391">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-391">JSON Configuration Provider</span></span>

<span data-ttu-id="0be0b-392"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-392">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="0be0b-393">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-393">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0be0b-394">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-394">Overloads permit specifying:</span></span>

* <span data-ttu-id="0be0b-395">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="0be0b-395">Whether the file is optional.</span></span>
* <span data-ttu-id="0be0b-396">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="0be0b-396">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="0be0b-397">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-397">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="0be0b-398">`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-398">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="0be0b-399">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-399">The method is called to load configuration from:</span></span>

* <span data-ttu-id="0be0b-400">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-400">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="0be0b-401">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-401">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="0be0b-402">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-402">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="0be0b-403">詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-403">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="0be0b-404">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-404">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="0be0b-405">環境変数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-405">Environment variables.</span></span>
* <span data-ttu-id="0be0b-406">開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-406">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="0be0b-407">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="0be0b-407">Command-line arguments.</span></span>

<span data-ttu-id="0be0b-408">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-408">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="0be0b-409">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-409">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="0be0b-410">ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-410">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="0be0b-411">**例**</span><span class="sxs-lookup"><span data-stu-id="0be0b-411">**Example**</span></span>

<span data-ttu-id="0be0b-412">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-412">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="0be0b-413">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-413">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="0be0b-414">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-414">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="0be0b-415">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-415">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="0be0b-416">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-416">Run the sample app.</span></span> <span data-ttu-id="0be0b-417">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-417">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="0be0b-418">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-418">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="0be0b-419">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-419">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="0be0b-420">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-420">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="0be0b-421">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-421">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="0be0b-422">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-422">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="0be0b-423">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-423">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="0be0b-424">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="0be0b-424">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="0be0b-425">キー `Logging:LogLevel:Default` のログ レベルは `Information` です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-425">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="0be0b-426">`AddJsonFile` の最初の呼び出しでは、*appsettings.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-426">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="0be0b-427">`AddJsonFile` の 2 回目の呼び出しでは、*appsettings.{Environment}.json* から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-427">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="0be0b-428">サンプル アプリの *appsettings.Development.json* では、次のファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-428">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="0be0b-429">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-429">Run the sample app.</span></span> <span data-ttu-id="0be0b-430">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-430">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="0be0b-431">出力には、アプリの環境に基づく構成のキーと値のペアが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-431">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="0be0b-432">開発環境でアプリを実行する場合、キー `Logging:LogLevel:Default` のログ レベルは `Debug` です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-432">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="0be0b-433">運用環境でもう一度サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-433">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="0be0b-434">*Properties/launchSettings.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-434">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="0be0b-435">`ConfigurationSample` プロファイルで、`ASPNETCORE_ENVIRONMENT` 環境変数の値を `Production` に変更します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-435">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="0be0b-436">ファイルを保存し、コマンド シェルで `dotnet run` を使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-436">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="0be0b-437">*appsettings.Development.json* の設定では、*appsettings.json* の設定がオーバーライドされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="0be0b-437">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="0be0b-438">キー `Logging:LogLevel:Default` のログ レベルは `Warning` です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-438">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

::: moniker-end

### <a name="xml-configuration-provider"></a><span data-ttu-id="0be0b-439">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-439">XML Configuration Provider</span></span>

<span data-ttu-id="0be0b-440"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-440">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="0be0b-441">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-441">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0be0b-442">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-442">Overloads permit specifying:</span></span>

* <span data-ttu-id="0be0b-443">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="0be0b-443">Whether the file is optional.</span></span>
* <span data-ttu-id="0be0b-444">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="0be0b-444">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="0be0b-445">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-445">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="0be0b-446">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-446">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="0be0b-447">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-447">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="0be0b-448">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-448">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="0be0b-449">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-449">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="0be0b-450">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-450">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="0be0b-451">section0:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-451">section0:key0</span></span>
* <span data-ttu-id="0be0b-452">section0:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-452">section0:key1</span></span>
* <span data-ttu-id="0be0b-453">section1:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-453">section1:key0</span></span>
* <span data-ttu-id="0be0b-454">section1:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-454">section1:key1</span></span>

<span data-ttu-id="0be0b-455">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-455">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="0be0b-456">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-456">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="0be0b-457">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-457">section:section0:key:key0</span></span>
* <span data-ttu-id="0be0b-458">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-458">section:section0:key:key1</span></span>
* <span data-ttu-id="0be0b-459">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-459">section:section1:key:key0</span></span>
* <span data-ttu-id="0be0b-460">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-460">section:section1:key:key1</span></span>

<span data-ttu-id="0be0b-461">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-461">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="0be0b-462">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-462">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="0be0b-463">key:attribute</span><span class="sxs-lookup"><span data-stu-id="0be0b-463">key:attribute</span></span>
* <span data-ttu-id="0be0b-464">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="0be0b-464">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="0be0b-465">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-465">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="0be0b-466"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-466">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="0be0b-467">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-467">The key is the file name.</span></span> <span data-ttu-id="0be0b-468">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-468">The value contains the file's contents.</span></span> <span data-ttu-id="0be0b-469">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-469">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="0be0b-470">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-470">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="0be0b-471">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-471">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="0be0b-472">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-472">Overloads permit specifying:</span></span>

* <span data-ttu-id="0be0b-473">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="0be0b-473">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="0be0b-474">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="0be0b-474">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="0be0b-475">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-475">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="0be0b-476">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-476">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="0be0b-477">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-477">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="0be0b-478">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-478">Memory Configuration Provider</span></span>

<span data-ttu-id="0be0b-479"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-479">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="0be0b-480">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-480">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="0be0b-481">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-481">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="0be0b-482">ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-482">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="0be0b-483">次の例では、構成ディクショナリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-483">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="0be0b-484">ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-484">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="0be0b-485">GetValue</span><span class="sxs-lookup"><span data-stu-id="0be0b-485">GetValue</span></span>

<span data-ttu-id="0be0b-486">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) によって、指定したキーで構成から単一の値が抽出され、それが指定したコレクション以外の型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-486">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="0be0b-487">オーバーロードは、既定値を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-487">An overload accepts a default value.</span></span>

<span data-ttu-id="0be0b-488">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-488">The following example:</span></span>

* <span data-ttu-id="0be0b-489">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-489">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="0be0b-490">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-490">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="0be0b-491">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-491">Types the value as an `int`.</span></span>
* <span data-ttu-id="0be0b-492">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-492">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="0be0b-493">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="0be0b-493">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="0be0b-494">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-494">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="0be0b-495">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-495">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="0be0b-496">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-496">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="0be0b-497">section0:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-497">section0:key0</span></span>
* <span data-ttu-id="0be0b-498">section0:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-498">section0:key1</span></span>
* <span data-ttu-id="0be0b-499">section1:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-499">section1:key0</span></span>
* <span data-ttu-id="0be0b-500">section1:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-500">section1:key1</span></span>
* <span data-ttu-id="0be0b-501">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-501">section2:subsection0:key0</span></span>
* <span data-ttu-id="0be0b-502">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-502">section2:subsection0:key1</span></span>
* <span data-ttu-id="0be0b-503">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="0be0b-503">section2:subsection1:key0</span></span>
* <span data-ttu-id="0be0b-504">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="0be0b-504">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="0be0b-505">GetSection</span><span class="sxs-lookup"><span data-stu-id="0be0b-505">GetSection</span></span>

<span data-ttu-id="0be0b-506">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-506">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="0be0b-507">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-507">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="0be0b-508">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-508">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="0be0b-509">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-509">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="0be0b-510">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-510">`GetSection` never returns `null`.</span></span> <span data-ttu-id="0be0b-511">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-511">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="0be0b-512">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-512">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="0be0b-513"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-513">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="0be0b-514">GetChildren</span><span class="sxs-lookup"><span data-stu-id="0be0b-514">GetChildren</span></span>

<span data-ttu-id="0be0b-515">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-515">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="0be0b-516">既存</span><span class="sxs-lookup"><span data-stu-id="0be0b-516">Exists</span></span>

<span data-ttu-id="0be0b-517">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-517">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="0be0b-518">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-518">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="0be0b-519">クラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="0be0b-519">Bind to a class</span></span>

<span data-ttu-id="0be0b-520">"*オプション パターン*" を使用して、関連する設定のグループを表すクラスに構成をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-520">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="0be0b-521">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-521">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="0be0b-522">構成値は文字列として返されますが、<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> を呼び出すことで [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) オブジェクトを構築できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-522">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="0be0b-523">サンプル アプリには `Starship` モデル (*Models/Starship.cs*) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-523">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0be0b-524">サンプル アプリが JSON 構成プロバイダーを使用して構成を読み込むときに、*starship.json* ファイルの `starship` セクションで構成が作成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-524">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="0be0b-525">次の構成のキーと値のペアが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-525">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="0be0b-526">Key</span><span class="sxs-lookup"><span data-stu-id="0be0b-526">Key</span></span>                   | <span data-ttu-id="0be0b-527">[値]</span><span class="sxs-lookup"><span data-stu-id="0be0b-527">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="0be0b-528">starship:name</span><span class="sxs-lookup"><span data-stu-id="0be0b-528">starship:name</span></span>         | <span data-ttu-id="0be0b-529">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="0be0b-529">USS Enterprise</span></span>                                    |
| <span data-ttu-id="0be0b-530">starship:registry</span><span class="sxs-lookup"><span data-stu-id="0be0b-530">starship:registry</span></span>     | <span data-ttu-id="0be0b-531">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="0be0b-531">NCC-1701</span></span>                                          |
| <span data-ttu-id="0be0b-532">starship:class</span><span class="sxs-lookup"><span data-stu-id="0be0b-532">starship:class</span></span>        | <span data-ttu-id="0be0b-533">Constitution</span><span class="sxs-lookup"><span data-stu-id="0be0b-533">Constitution</span></span>                                      |
| <span data-ttu-id="0be0b-534">starship:length</span><span class="sxs-lookup"><span data-stu-id="0be0b-534">starship:length</span></span>       | <span data-ttu-id="0be0b-535">304.8</span><span class="sxs-lookup"><span data-stu-id="0be0b-535">304.8</span></span>                                             |
| <span data-ttu-id="0be0b-536">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="0be0b-536">starship:commissioned</span></span> | <span data-ttu-id="0be0b-537">False</span><span class="sxs-lookup"><span data-stu-id="0be0b-537">False</span></span>                                             |
| <span data-ttu-id="0be0b-538">trademark</span><span class="sxs-lookup"><span data-stu-id="0be0b-538">trademark</span></span>             | <span data-ttu-id="0be0b-539">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="0be0b-539">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="0be0b-540">サンプル アプリは `starship` キーを使用して `GetSection` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-540">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="0be0b-541">`starship` のキーと値のペアは分離されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-541">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="0be0b-542">`Starship` クラスのインスタンスを渡すサブセクションで、`Bind` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-542">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="0be0b-543">インスタンスの値をバインドした後、インスタンスがレンダリング用のプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-543">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="0be0b-544">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="0be0b-544">Bind to an object graph</span></span>

<span data-ttu-id="0be0b-545"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-545"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="0be0b-546">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="0be0b-546">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0be0b-547">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-547">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="0be0b-548">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-548">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="0be0b-549">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-549">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="0be0b-550">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) によってバインドされ、指定した型が返されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-550">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="0be0b-551">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-551">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="0be0b-552">次のコードは、前の例と共に `Get<T>` を使用する方法を示しています。これにより、バインドされたインスタンスを、表示用に使用するプロパティに直接割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-552">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="0be0b-553">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="0be0b-553">Bind an array to a class</span></span>

<span data-ttu-id="0be0b-554">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="0be0b-554">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="0be0b-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="0be0b-555">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="0be0b-556">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-556">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="0be0b-557">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-557">Binding is provided by convention.</span></span> <span data-ttu-id="0be0b-558">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-558">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="0be0b-559">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="0be0b-559">**In-memory array processing**</span></span>

<span data-ttu-id="0be0b-560">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-560">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="0be0b-561">Key</span><span class="sxs-lookup"><span data-stu-id="0be0b-561">Key</span></span>             | <span data-ttu-id="0be0b-562">[値]</span><span class="sxs-lookup"><span data-stu-id="0be0b-562">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="0be0b-563">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="0be0b-563">array:entries:0</span></span> | <span data-ttu-id="0be0b-564">value0</span><span class="sxs-lookup"><span data-stu-id="0be0b-564">value0</span></span> |
| <span data-ttu-id="0be0b-565">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="0be0b-565">array:entries:1</span></span> | <span data-ttu-id="0be0b-566">value1</span><span class="sxs-lookup"><span data-stu-id="0be0b-566">value1</span></span> |
| <span data-ttu-id="0be0b-567">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="0be0b-567">array:entries:2</span></span> | <span data-ttu-id="0be0b-568">value2</span><span class="sxs-lookup"><span data-stu-id="0be0b-568">value2</span></span> |
| <span data-ttu-id="0be0b-569">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="0be0b-569">array:entries:4</span></span> | <span data-ttu-id="0be0b-570">value4</span><span class="sxs-lookup"><span data-stu-id="0be0b-570">value4</span></span> |
| <span data-ttu-id="0be0b-571">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="0be0b-571">array:entries:5</span></span> | <span data-ttu-id="0be0b-572">value5</span><span class="sxs-lookup"><span data-stu-id="0be0b-572">value5</span></span> |

<span data-ttu-id="0be0b-573">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-573">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="0be0b-574">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="0be0b-574">The array skips a value for index &num;3.</span></span> <span data-ttu-id="0be0b-575">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-575">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="0be0b-576">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-576">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0be0b-577">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-577">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="0be0b-578">また、[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用して、コードをよりコンパクトにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-578">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="0be0b-579">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-579">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="0be0b-580">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="0be0b-580">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="0be0b-581">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="0be0b-581">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="0be0b-582">0</span><span class="sxs-lookup"><span data-stu-id="0be0b-582">0</span></span>                            | <span data-ttu-id="0be0b-583">value0</span><span class="sxs-lookup"><span data-stu-id="0be0b-583">value0</span></span>                       |
| <span data-ttu-id="0be0b-584">1</span><span class="sxs-lookup"><span data-stu-id="0be0b-584">1</span></span>                            | <span data-ttu-id="0be0b-585">value1</span><span class="sxs-lookup"><span data-stu-id="0be0b-585">value1</span></span>                       |
| <span data-ttu-id="0be0b-586">2</span><span class="sxs-lookup"><span data-stu-id="0be0b-586">2</span></span>                            | <span data-ttu-id="0be0b-587">value2</span><span class="sxs-lookup"><span data-stu-id="0be0b-587">value2</span></span>                       |
| <span data-ttu-id="0be0b-588">3</span><span class="sxs-lookup"><span data-stu-id="0be0b-588">3</span></span>                            | <span data-ttu-id="0be0b-589">value4</span><span class="sxs-lookup"><span data-stu-id="0be0b-589">value4</span></span>                       |
| <span data-ttu-id="0be0b-590">4</span><span class="sxs-lookup"><span data-stu-id="0be0b-590">4</span></span>                            | <span data-ttu-id="0be0b-591">value5</span><span class="sxs-lookup"><span data-stu-id="0be0b-591">value5</span></span>                       |

<span data-ttu-id="0be0b-592">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-592">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="0be0b-593">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-593">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="0be0b-594">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-594">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="0be0b-595">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-595">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="0be0b-596">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-596">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="0be0b-597">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-597">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="0be0b-598">`ConfigureAppConfiguration`の場合:</span><span class="sxs-lookup"><span data-stu-id="0be0b-598">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="0be0b-599">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-599">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="0be0b-600">Key</span><span class="sxs-lookup"><span data-stu-id="0be0b-600">Key</span></span>             | <span data-ttu-id="0be0b-601">[値]</span><span class="sxs-lookup"><span data-stu-id="0be0b-601">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="0be0b-602">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="0be0b-602">array:entries:3</span></span> | <span data-ttu-id="0be0b-603">value3</span><span class="sxs-lookup"><span data-stu-id="0be0b-603">value3</span></span> |

<span data-ttu-id="0be0b-604">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-604">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="0be0b-605">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="0be0b-605">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="0be0b-606">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="0be0b-606">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="0be0b-607">0</span><span class="sxs-lookup"><span data-stu-id="0be0b-607">0</span></span>                            | <span data-ttu-id="0be0b-608">value0</span><span class="sxs-lookup"><span data-stu-id="0be0b-608">value0</span></span>                       |
| <span data-ttu-id="0be0b-609">1</span><span class="sxs-lookup"><span data-stu-id="0be0b-609">1</span></span>                            | <span data-ttu-id="0be0b-610">value1</span><span class="sxs-lookup"><span data-stu-id="0be0b-610">value1</span></span>                       |
| <span data-ttu-id="0be0b-611">2</span><span class="sxs-lookup"><span data-stu-id="0be0b-611">2</span></span>                            | <span data-ttu-id="0be0b-612">value2</span><span class="sxs-lookup"><span data-stu-id="0be0b-612">value2</span></span>                       |
| <span data-ttu-id="0be0b-613">3</span><span class="sxs-lookup"><span data-stu-id="0be0b-613">3</span></span>                            | <span data-ttu-id="0be0b-614">value3</span><span class="sxs-lookup"><span data-stu-id="0be0b-614">value3</span></span>                       |
| <span data-ttu-id="0be0b-615">4</span><span class="sxs-lookup"><span data-stu-id="0be0b-615">4</span></span>                            | <span data-ttu-id="0be0b-616">value4</span><span class="sxs-lookup"><span data-stu-id="0be0b-616">value4</span></span>                       |
| <span data-ttu-id="0be0b-617">5</span><span class="sxs-lookup"><span data-stu-id="0be0b-617">5</span></span>                            | <span data-ttu-id="0be0b-618">value5</span><span class="sxs-lookup"><span data-stu-id="0be0b-618">value5</span></span>                       |

<span data-ttu-id="0be0b-619">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="0be0b-619">**JSON array processing**</span></span>

<span data-ttu-id="0be0b-620">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-620">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="0be0b-621">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="0be0b-621">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="0be0b-622">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-622">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="0be0b-623">Key</span><span class="sxs-lookup"><span data-stu-id="0be0b-623">Key</span></span>                     | <span data-ttu-id="0be0b-624">[値]</span><span class="sxs-lookup"><span data-stu-id="0be0b-624">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="0be0b-625">json_array:key</span><span class="sxs-lookup"><span data-stu-id="0be0b-625">json_array:key</span></span>          | <span data-ttu-id="0be0b-626">valueA</span><span class="sxs-lookup"><span data-stu-id="0be0b-626">valueA</span></span> |
| <span data-ttu-id="0be0b-627">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="0be0b-627">json_array:subsection:0</span></span> | <span data-ttu-id="0be0b-628">valueB</span><span class="sxs-lookup"><span data-stu-id="0be0b-628">valueB</span></span> |
| <span data-ttu-id="0be0b-629">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="0be0b-629">json_array:subsection:1</span></span> | <span data-ttu-id="0be0b-630">valueC</span><span class="sxs-lookup"><span data-stu-id="0be0b-630">valueC</span></span> |
| <span data-ttu-id="0be0b-631">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="0be0b-631">json_array:subsection:2</span></span> | <span data-ttu-id="0be0b-632">valueD</span><span class="sxs-lookup"><span data-stu-id="0be0b-632">valueD</span></span> |

<span data-ttu-id="0be0b-633">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-633">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0be0b-634">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-634">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="0be0b-635">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-635">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="0be0b-636">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="0be0b-636">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="0be0b-637">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="0be0b-637">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="0be0b-638">0</span><span class="sxs-lookup"><span data-stu-id="0be0b-638">0</span></span>                                   | <span data-ttu-id="0be0b-639">valueB</span><span class="sxs-lookup"><span data-stu-id="0be0b-639">valueB</span></span>                              |
| <span data-ttu-id="0be0b-640">1</span><span class="sxs-lookup"><span data-stu-id="0be0b-640">1</span></span>                                   | <span data-ttu-id="0be0b-641">valueC</span><span class="sxs-lookup"><span data-stu-id="0be0b-641">valueC</span></span>                              |
| <span data-ttu-id="0be0b-642">2</span><span class="sxs-lookup"><span data-stu-id="0be0b-642">2</span></span>                                   | <span data-ttu-id="0be0b-643">valueD</span><span class="sxs-lookup"><span data-stu-id="0be0b-643">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="0be0b-644">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0be0b-644">Custom configuration provider</span></span>

<span data-ttu-id="0be0b-645">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-645">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="0be0b-646">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0be0b-646">The provider has the following characteristics:</span></span>

* <span data-ttu-id="0be0b-647">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-647">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="0be0b-648">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-648">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="0be0b-649">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-649">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="0be0b-650">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-650">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="0be0b-651">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="0be0b-651">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="0be0b-652">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-652">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0be0b-653">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-653">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="0be0b-654">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-654">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="0be0b-655">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-655">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="0be0b-656"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-656">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="0be0b-657">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-657">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="0be0b-658"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-658">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="0be0b-659">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-659">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="0be0b-660">[構成キーでは大文字と小文字が区別されない](#keys)ため、データベースの初期化に使用されるディクショナリは、大文字と小文字を区別しない比較子 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) を使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-660">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="0be0b-661">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-661">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="0be0b-662">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-662">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="0be0b-663">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-663">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="0be0b-664">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-664">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0be0b-665">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-665">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="0be0b-666">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-666">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="0be0b-667">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-667">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="0be0b-668"><xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-668">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="0be0b-669">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-669">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="0be0b-670"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-670">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="0be0b-671">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-671">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="0be0b-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="0be0b-673">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="0be0b-673">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="0be0b-674">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="0be0b-674">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="0be0b-675">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-675">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="0be0b-676">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="0be0b-676">Access configuration during startup</span></span>

<span data-ttu-id="0be0b-677">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="0be0b-677">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0be0b-678">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-678">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="0be0b-679">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-679">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="0be0b-680">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="0be0b-680">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="0be0b-681">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="0be0b-681">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="0be0b-682">Razor ページ:</span><span class="sxs-lookup"><span data-stu-id="0be0b-682">In a Razor Pages page:</span></span>

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

<span data-ttu-id="0be0b-683">MVC ビュー:</span><span class="sxs-lookup"><span data-stu-id="0be0b-683">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="0be0b-684">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="0be0b-684">Add configuration from an external assembly</span></span>

<span data-ttu-id="0be0b-685"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="0be0b-685">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="0be0b-686">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0be0b-686">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0be0b-687">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0be0b-687">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
