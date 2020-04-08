---
title: Apache 搭載の Linux で ASP.NET Core をホストする
author: rick-anderson
description: CentOS 上にリバース プロキシ サーバーとして Apache をセットアップし、Kestrel 上で実行されている ASP.NET Core Web アプリに HTTP トラフィックを転送する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 02/05/2020
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 3a3edd961b08c1952e6ded8038ed7ada381c54b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78646640"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="b40d9-103">Apache 搭載の Linux で ASP.NET Core をホストする</span><span class="sxs-lookup"><span data-stu-id="b40d9-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="b40d9-104">作成者: [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="b40d9-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="b40d9-105">このガイドでは、[CentOS 7](https://www.centos.org/) 上にリバース プロキシ サーバーとして [Apache](https://httpd.apache.org/) をセットアップし、[Kestrel](xref:fundamentals/servers/kestrel) サーバー上で実行されている ASP.NET Core Web アプリに HTTP トラフィックをリダイレクトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="b40d9-106">[mod_proxy 拡張機能](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html)および関連するモジュールは、サーバーのリバース プロキシを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b40d9-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b40d9-107">Prerequisites</span></span>

* <span data-ttu-id="b40d9-108">CentOS 7 を実行しているサーバーと、sudo 特権を持つ標準ユーザー アカウント。</span><span class="sxs-lookup"><span data-stu-id="b40d9-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="b40d9-109">サーバーへの .NET Core ランタイムのインストール。</span><span class="sxs-lookup"><span data-stu-id="b40d9-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="b40d9-110">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="b40d9-111">プレビューでない最新の .NET Core バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="b40d9-112">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** の下の表でプレビューでない最新のランタイムをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="b40d9-113">Linux の 「**パッケージ マネージャーの手順**」 リンクを選択し、CentOS の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="b40d9-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="b40d9-114">既存の ASP.NET Core アプリ。</span><span class="sxs-lookup"><span data-stu-id="b40d9-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="b40d9-115">共有フレームワークをアップグレードした後の任意の時点で、サーバーによってホストされている ASP.NET Core アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="b40d9-116">アプリを介して発行およびコピーする</span><span class="sxs-lookup"><span data-stu-id="b40d9-116">Publish and copy over the app</span></span>

<span data-ttu-id="b40d9-117">[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)用にアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="b40d9-118">アプリがローカル環境で実行されていて、セキュリティで保護された接続 (HTTPS) を行うように構成されていない場合は、次の方法のいずれかを採用します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="b40d9-119">セキュリティで保護されたローカル接続を処理するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="b40d9-120">詳しくは、「[HTTPS の構成](#https-configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b40d9-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="b40d9-121">*Properties/launchSettings.json* ファイルの `applicationUrl` プロパティから `https://localhost:5001` を削除します (ある場合)。</span><span class="sxs-lookup"><span data-stu-id="b40d9-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="b40d9-122">開発環境から [dotnet publish](/dotnet/core/tools/dotnet-publish) を実行し、サーバー上で実行できるディレクトリ (たとえば、*bin/Release/&lt;target_framework_moniker&gt;/publish*) にアプリをパッケージします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="b40d9-123">サーバーで .NET Core ランタイムを管理しない場合、アプリは[独立した展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行することもできます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="b40d9-124">組織のワークフローに統合されているツール (SCP や SFTP など) を使用して、サーバーに ASP.NET Core アプリをコピーします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="b40d9-125">Web アプリは一般的に *var* ディレクトリの下に配置されます (たとえば、*var/www/helloapp*)。</span><span class="sxs-lookup"><span data-stu-id="b40d9-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="b40d9-126">運用展開シナリオの場合、継続的インテグレーション ワークフローが、アプリの発行処理とサーバーへの資産のコピーを行います。</span><span class="sxs-lookup"><span data-stu-id="b40d9-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="b40d9-127">プロキシ サーバーを構成する</span><span class="sxs-lookup"><span data-stu-id="b40d9-127">Configure a proxy server</span></span>

<span data-ttu-id="b40d9-128">リバース プロキシは、動的 Web アプリを提供するための一般的な仕組みです。</span><span class="sxs-lookup"><span data-stu-id="b40d9-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="b40d9-129">リバース プロキシは HTTP 要求を終了させ、ASP.NET アプリに転送します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="b40d9-130">プロキシ サーバーは、クライアント要求を処理せずに他のサーバーに転送するサーバーです。</span><span class="sxs-lookup"><span data-stu-id="b40d9-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="b40d9-131">リバース プロキシは、一般的に任意のクライアントに代わって固定の送信先に転送します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="b40d9-132">このガイドでは、Kestrel が ASP.NET Core アプリを提供しているものと同じサーバー上で実行されるリバース プロキシとして Apache を構成します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="b40d9-133">要求はリバース プロキシによって転送されます。そのため、[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) パッケージの [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="b40d9-134">リダイレクト URI とその他のセキュリティ ポリシーを正しく機能させるために、このミドルウェアは、`X-Forwarded-Proto` ヘッダーを利用して、`Request.Scheme` を更新します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="b40d9-135">認証、リンクの生成、リダイレクト、および地理的位置情報など、スキームに依存するすべてのコンポーネントは、Forwarded Headers Middleware の呼び出し後に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="b40d9-136">一般的な規則として、Forwarded Headers Middleware は、診断およびエラー処理ミドルウェアを除くその他のミドルウェアより前に実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-136">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="b40d9-137">この順序により、転送されるヘッダー情報に依存するミドルウェアが処理にヘッダー値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-137">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="b40d9-138"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> や同様の認証スキーム ミドルウェアを呼び出す前に、`Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-138">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="b40d9-139">ミドルウェアを構成して、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-139">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="b40d9-140">ミドルウェアに対して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が指定されていない場合、転送される既定のヘッダーは `None` です。</span><span class="sxs-lookup"><span data-stu-id="b40d9-140">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="b40d9-141">標準 localhost アドレス (127.0.0.1) など、ループバック アドレス (127.0.0.0/8、[::1]) 上で実行するプロキシは、既定で信頼されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-141">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="b40d9-142">組織内のその他の信頼されているプロキシまたはネットワークによってインターネットと Web サーバーの間の要求が処理される場合は、それらを、<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> を使用して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> または <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> のリストに追加します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-142">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="b40d9-143">次の例では、IP アドレス 10.0.0.100 にある信頼されているプロキシ サーバーが `Startup.ConfigureServices` 内の Forwarded Headers Middleware `KnownProxies` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-143">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="b40d9-144">詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40d9-144">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="b40d9-145">Apache をインストールする</span><span class="sxs-lookup"><span data-stu-id="b40d9-145">Install Apache</span></span>

<span data-ttu-id="b40d9-146">CentOS パッケージを最新の安定したバージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-146">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="b40d9-147">1 つの `yum` コマンドで、CentOS に Apache Web サーバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-147">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="b40d9-148">コマンド実行後の出力例:</span><span class="sxs-lookup"><span data-stu-id="b40d9-148">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="b40d9-149">この例では、CentOS 7 のバージョンが 64 ビットなので、出力は httpd.86_64 を反映しています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-149">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="b40d9-150">Apache がインストールされている場所を確認するには、コマンド プロンプトから `whereis httpd` を実行します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-150">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="b40d9-151">Apache を構成する</span><span class="sxs-lookup"><span data-stu-id="b40d9-151">Configure Apache</span></span>

<span data-ttu-id="b40d9-152">Apache の構成ファイルは、`/etc/httpd/conf.d/` ディレクトリ内にあります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-152">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="b40d9-153">`/etc/httpd/conf.modules.d/` 内のモジュール構成ファイルに加え、拡張子が *.conf* のファイルがアルファベット順で処理されます。このディレクトリには、モジュールの読み込みに必要な構成ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-153">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="b40d9-154">アプリ用に *helloapp.conf* という名前の構成ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-154">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

<span data-ttu-id="b40d9-155">`VirtualHost` ブロックは、サーバー上の 1 つまたは複数のファイルに複数回出現することができます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-155">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="b40d9-156">上記の構成ファイルでは、Apache はポート 80 でパブリック トラフィックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-156">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="b40d9-157">ドメイン `www.example.com` が提供されており、別名 `*.example.com` は同じ Web サイトに解決されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-157">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="b40d9-158">詳しくは、「[名前ベースのバーチャル ホスト](https://httpd.apache.org/docs/current/vhosts/name-based.html)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b40d9-158">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="b40d9-159">要求は、ルートにおいて、127.0.0.1 にあるサーバーのポート 5000 にプロキシされます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-159">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="b40d9-160">双方向通信の場合は、`ProxyPass` と `ProxyPassReverse` が必要です。</span><span class="sxs-lookup"><span data-stu-id="b40d9-160">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="b40d9-161">Kestrel の IP/ポートを変更するには、[Kestrel のエンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40d9-161">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="b40d9-162">**VirtualHost** ブロックで適切な [ServerName ディレクティブ](https://httpd.apache.org/docs/current/mod/core.html#servername)を指定しないと、アプリにセキュリティ上の脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-162">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="b40d9-163">親ドメイン全体を制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.example.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="b40d9-163">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="b40d9-164">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40d9-164">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="b40d9-165">`ErrorLog` および `CustomLog` ディレクティブを使って、`VirtualHost` ごとにログを構成できます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-165">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="b40d9-166">`ErrorLog` は、サーバーがエラーをログに記録する場所です。`CustomLog` には、ログ ファイルのファイル名と形式を設定します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-166">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="b40d9-167">この例では、要求の情報がログに記録される場所です。</span><span class="sxs-lookup"><span data-stu-id="b40d9-167">In this case, this is where request information is logged.</span></span> <span data-ttu-id="b40d9-168">1 つの要求につき 1 行が記録されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-168">There's one line for each request.</span></span>

<span data-ttu-id="b40d9-169">ファイルを保存し、構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-169">Save the file and test the configuration.</span></span> <span data-ttu-id="b40d9-170">すべてに合格すると、応答は `Syntax [OK]` になります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-170">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="b40d9-171">Apache を再起動します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-171">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="b40d9-172">アプリを監視する</span><span class="sxs-lookup"><span data-stu-id="b40d9-172">Monitor the app</span></span>

<span data-ttu-id="b40d9-173">これで、`http://localhost:80` に対して行われた要求を Kestrel で実行されている ASP.NET Core アプリ (`http://127.0.0.1:5000`) に転送するように Apache が設定されました。</span><span class="sxs-lookup"><span data-stu-id="b40d9-173">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="b40d9-174">ただし、Apache は Kestrel プロセスを管理するようには設定されていません。</span><span class="sxs-lookup"><span data-stu-id="b40d9-174">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="b40d9-175">*systemd* を使って、基礎 Web アプリを起動して監視するサービス ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-175">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="b40d9-176">*systemd* は init システムであり、プロセスを起動、停止、管理するためのさまざまな高性能機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-176">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="b40d9-177">サービス ファイルを作成する</span><span class="sxs-lookup"><span data-stu-id="b40d9-177">Create the service file</span></span>

<span data-ttu-id="b40d9-178">次のように、サービス定義ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-178">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="b40d9-179">アプリのサービス ファイルの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-179">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="b40d9-180">前の例では、サービスを管理するユーザーは `User` オプションによって指定されています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-180">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="b40d9-181">ユーザー (`apache`) が存在し、アプリのファイルの適切な所有権を持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-181">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="b40d9-182">アプリが最初の割り込み信号を受信してからシャットダウンするのを待機する期間を構成するには、`TimeoutStopSec` を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-182">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="b40d9-183">この期間内にアプリがシャットダウンしない場合は、SIGKILL を発行してアプリを終了します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-183">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="b40d9-184">タイムアウトを無効にするには、値として、単位なしの秒数 (`150` など)、期間の値 (`2min 30s` など)、または `infinity` を指定します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-184">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="b40d9-185">`TimeoutStopSec` は、既定ではマネージャー構成ファイル (*systemd-system.conf*、*system.conf.d*、*systemd-user.conf*、*user.conf.d*) 内の `DefaultTimeoutStopSec` の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-185">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="b40d9-186">ほとんどのディストリビューションにおいて、タイムアウトの既定値は 90 秒となります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-186">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="b40d9-187">構成プロバイダーが環境変数を読み取れるようにするために、一部の値 (たとえば SQL の接続文字列) をエスケープする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-187">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="b40d9-188">次のコマンドを使用して、構成ファイルで使用するために適切にエスケープされた値を生成します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-188">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="b40d9-189">コロン (`:`) 区切り記号は、環境変数の名前ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="b40d9-189">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="b40d9-190">コロンの代わりに 2 つのアンダースコア (`__`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-190">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="b40d9-191">環境変数が構成に読み取られるときに、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)によって 2 つのアンダースコアがコロンに変換されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-191">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="b40d9-192">次の例では、接続文字列キー `ConnectionStrings:DefaultConnection` はサービス定義ファイルでは `ConnectionStrings__DefaultConnection` と設定されています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-192">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="b40d9-193">ファイルを保存し、サービスを有効にします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-193">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="b40d9-194">サービスを起動し、動作を確認します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-194">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="b40d9-195">リバース プロキシが構成され、Kestrel は *systemd* 経由で管理されます。これで Web アプリは完全に構成され、`http://localhost` でローカル コンピューター上のブラウザーからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-195">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="b40d9-196">応答ヘッダーを調べると、**Server** ヘッダーでは ASP.NET Core アプリが Kestrel によって提供されていることが示されています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-196">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="b40d9-197">ログを表示する</span><span class="sxs-lookup"><span data-stu-id="b40d9-197">View logs</span></span>

<span data-ttu-id="b40d9-198">Kestrel を使う Web アプリは *systemd* を使って管理されるため、イベントとプロセスは中央のジャーナルに記録されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-198">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="b40d9-199">ただし、このジャーナルには、*systemd* によって管理されるすべてのサービスとプロセスのエントリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-199">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="b40d9-200">`kestrel-helloapp.service` 固有の項目を表示するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-200">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="b40d9-201">時間フィルタリングの場合は、コマンドで時間のオプションを指定します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-201">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="b40d9-202">たとえば、現在の日付でフィルター処理するには `--since today` を使い、前の 1 時間のエントリを参照するには `--until 1 hour ago` を使います。</span><span class="sxs-lookup"><span data-stu-id="b40d9-202">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="b40d9-203">詳しくは、[journalctl の man ページ](https://www.unix.com/man-page/centos/1/journalctl/)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b40d9-203">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="b40d9-204">データの保護</span><span class="sxs-lookup"><span data-stu-id="b40d9-204">Data protection</span></span>

<span data-ttu-id="b40d9-205">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証ミドルウェア (Cookie ミドルウェアなど) やクロスサイト リクエスト フォージェリ (CSRF) 保護を含む、いくつかの ASP.NET Core [ ミドルウェア](xref:fundamentals/middleware/index)で使用されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-205">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="b40d9-206">データ保護 API がユーザーのコードから呼び出されない場合でも、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-206">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="b40d9-207">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-207">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="b40d9-208">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-208">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="b40d9-209">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-209">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="b40d9-210">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-210">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="b40d9-211">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-211">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="b40d9-212">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-212">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="b40d9-213">キー リングを永続化して暗号化するようにデータ保護を構成する場合は、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40d9-213">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="b40d9-214">アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="b40d9-214">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="b40d9-215">ファイアウォールを構成する</span><span class="sxs-lookup"><span data-stu-id="b40d9-215">Configure firewall</span></span>

<span data-ttu-id="b40d9-216">*Firewalld* は、ネットワーク ゾーンをサポートするファイアウォールを管理するための動的デーモンです。</span><span class="sxs-lookup"><span data-stu-id="b40d9-216">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="b40d9-217">ポートとパケットのフィルター処理は、iptables によって引き続き管理できます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-217">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="b40d9-218">*Firewalld* は既定でインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-218">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="b40d9-219">`yum` を使ってパッケージをインストールしたり、インストールされていることを確認したりできます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-219">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="b40d9-220">アプリに必要なポートのみを開くには、`firewalld` を使います。</span><span class="sxs-lookup"><span data-stu-id="b40d9-220">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="b40d9-221">この場合、ポート 80 と 443 が使用されています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-221">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="b40d9-222">次のコマンドは、ポート 80 と 443 が永続的に開かれるように設定します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-222">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="b40d9-223">ファイアウォールの設定を再度読み込みます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-223">Reload the firewall settings.</span></span> <span data-ttu-id="b40d9-224">既定のゾーンで使用できるサービスとポートを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-224">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="b40d9-225">オプションは `firewall-cmd -h` を調べることで使用できます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-225">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="b40d9-226">HTTPS の構成</span><span class="sxs-lookup"><span data-stu-id="b40d9-226">HTTPS configuration</span></span>

<span data-ttu-id="b40d9-227">**セキュリティで保護された (HTTPS) ローカル接続用にアプリを構成する**</span><span class="sxs-lookup"><span data-stu-id="b40d9-227">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="b40d9-228">[dotnet run](/dotnet/core/tools/dotnet-run) コマンドでは、アプリの *Properties/launchSettings.json* ファイルが使用されます。このファイルでは、`applicationUrl` プロパティによって提供される URL でリッスンするように、アプリが構成されます (例: `https://localhost:5001;http://localhost:5000`)。</span><span class="sxs-lookup"><span data-stu-id="b40d9-228">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="b40d9-229">次のいずれかの方法を使用して、`dotnet run` コマンド用の開発または開発環境 (Visual Studio Code の F5 または Ctrl + F5 キー) で証明書を使用するように、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-229">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="b40d9-230">[構成から既定の証明書を置き換える](xref:fundamentals/servers/kestrel#configuration) (*推奨*)</span><span class="sxs-lookup"><span data-stu-id="b40d9-230">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="b40d9-231">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="b40d9-231">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="b40d9-232">**セキュリティで保護された (HTTPS) クライアント接続用にリバース プロキシを構成する**</span><span class="sxs-lookup"><span data-stu-id="b40d9-232">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="b40d9-233">HTTPS 用に Apache を構成するには、*mod_ssl* モジュールを使います。</span><span class="sxs-lookup"><span data-stu-id="b40d9-233">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="b40d9-234">*httpd* モジュールがインストールされていると、*mod_ssl* モジュールもインストールされています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-234">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="b40d9-235">インストールされていない場合は、`yum` を使って構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-235">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="b40d9-236">HTTPS を強制するには、`mod_rewrite` モジュールをインストールして URL の書き換えを有効にします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-236">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="b40d9-237">*helloapp.conf* ファイルを変更して、URL の書き換えを有効にし、ポート 443 での通信をセキュリティで保護します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-237">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="b40d9-238">この例では、ローカルで生成された証明書を使います。</span><span class="sxs-lookup"><span data-stu-id="b40d9-238">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="b40d9-239">**SSLCertificateFile** は、ドメイン名のプライマリ証明書ファイルです。</span><span class="sxs-lookup"><span data-stu-id="b40d9-239">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="b40d9-240">**SSLCertificateKeyFile** は、CSR の作成時に生成されるキー ファイルです。</span><span class="sxs-lookup"><span data-stu-id="b40d9-240">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="b40d9-241">**SSLCertificateChainFile** は、証明機関から提供された中間証明書ファイル (存在する場合) です。</span><span class="sxs-lookup"><span data-stu-id="b40d9-241">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="b40d9-242">ファイルを保存し、構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-242">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="b40d9-243">Apache を再起動します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-243">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="b40d9-244">Apache に関するその他の推奨事項</span><span class="sxs-lookup"><span data-stu-id="b40d9-244">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="b40d9-245">共有されたフレームワークの更新プログラムを使用してアプリを再起動する</span><span class="sxs-lookup"><span data-stu-id="b40d9-245">Restart apps with shared framework updates</span></span>

<span data-ttu-id="b40d9-246">サーバー上で共有フレームワークをアップグレードしたら、サーバーによってホストされている ASP.NET Core アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-246">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="b40d9-247">その他のヘッダー</span><span class="sxs-lookup"><span data-stu-id="b40d9-247">Additional headers</span></span>

<span data-ttu-id="b40d9-248">悪意のある攻撃から防御するために、変更または追加する必要があるヘッダーがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-248">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="b40d9-249">必ず `mod_headers` モジュールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-249">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="b40d9-250">Apache をクリックジャッキング攻撃から保護する</span><span class="sxs-lookup"><span data-stu-id="b40d9-250">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="b40d9-251">[クリックジャッキング](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)は "*UI 着せ替え攻撃*" とも呼ばれ、Web サイトの訪問者を騙して現在訪れているものとは異なるページのリンクやボタンをクリックさせる悪意のある攻撃です。</span><span class="sxs-lookup"><span data-stu-id="b40d9-251">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="b40d9-252">サイトをセキュリティで保護するには、`X-FRAME-OPTIONS` を使います。</span><span class="sxs-lookup"><span data-stu-id="b40d9-252">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="b40d9-253">クリックジャッキング攻撃を軽減するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="b40d9-253">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="b40d9-254">*httpd.conf* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-254">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="b40d9-255">行 `Header append X-FRAME-OPTIONS "SAMEORIGIN"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-255">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="b40d9-256">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-256">Save the file.</span></span>
1. <span data-ttu-id="b40d9-257">Apache を再起動します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-257">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="b40d9-258">MIME タイプ スニッフィング</span><span class="sxs-lookup"><span data-stu-id="b40d9-258">MIME-type sniffing</span></span>

<span data-ttu-id="b40d9-259">`X-Content-Type-Options` ヘッダーは、Internet Explorer を "*MIME スニッフィング*" から防ぎます (ファイルの内容からファイルの `Content-Type` を判断します)。</span><span class="sxs-lookup"><span data-stu-id="b40d9-259">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="b40d9-260">サーバーが `nosniff` オプションを指定して `Content-Type` ヘッダーを `text/html` に設定すると、Internet Explorer はファイルの内容に関係なく `text/html` として内容をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-260">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="b40d9-261">*httpd.conf* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-261">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="b40d9-262">行 `Header set X-Content-Type-Options "nosniff"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-262">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="b40d9-263">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-263">Save the file.</span></span> <span data-ttu-id="b40d9-264">Apache を再起動します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-264">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="b40d9-265">負荷分散</span><span class="sxs-lookup"><span data-stu-id="b40d9-265">Load Balancing</span></span>

<span data-ttu-id="b40d9-266">この例では、同じインスタンス コンピューターに CentOS 7 と Kestrel をインストールし、Apache をセットアップおよび構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-266">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="b40d9-267">単一障害点を持たないように、*mod_proxy_balancer* を使い、**VirtualHost** を変更することで、Apache プロキシ サーバーの背後で複数インスタンスの Web アプを管理できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b40d9-267">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="b40d9-268">次に示す構成ファイルでは、ポート 5001 上で実行するように `helloapp` の追加インスタンスを設定しています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-268">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="b40d9-269">*Proxy* セクションは、2 メンバーのバランサー構成を使って *byrequests* を負荷分散するように設定されています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-269">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="b40d9-270">速度の制限</span><span class="sxs-lookup"><span data-stu-id="b40d9-270">Rate Limits</span></span>

<span data-ttu-id="b40d9-271">*httpd* モジュールに含まれる *mod_ratelimit* を使って、クライアントの帯域幅を制限できます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-271">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="b40d9-272">次のファイルの例では、ルートの場所の下での帯域幅を 600 KB/秒に制限しています。</span><span class="sxs-lookup"><span data-stu-id="b40d9-272">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="b40d9-273">要求ヘッダー フィールドが長すぎます</span><span class="sxs-lookup"><span data-stu-id="b40d9-273">Long request header fields</span></span>

<span data-ttu-id="b40d9-274">プロキシ サーバーの既定の設定では、通常、要求ヘッダー フィールドが 8190 バイトに制限されます。</span><span class="sxs-lookup"><span data-stu-id="b40d9-274">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="b40d9-275">アプリでは、既定値よりも長いフィールドが必要になる場合があります (たとえば、[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用するアプリ)。</span><span class="sxs-lookup"><span data-stu-id="b40d9-275">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="b40d9-276">長いフィールドが必要な場合は、プロキシ サーバーの [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) ディレクティブに対して調整が必要になります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-276">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="b40d9-277">適用する値は、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="b40d9-277">The value to apply depends on the scenario.</span></span> <span data-ttu-id="b40d9-278">詳細については、ご利用のサーバーのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40d9-278">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="b40d9-279">必要な場合を除いて、`LimitRequestFieldSize` の既定値を増やさないでください。</span><span class="sxs-lookup"><span data-stu-id="b40d9-279">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="b40d9-280">値を増やすと、悪意のあるユーザーによるバッファー オーバーラン (オーバーフロー) とサービス拒否 (DoS) の攻撃のリスクが増加します。</span><span class="sxs-lookup"><span data-stu-id="b40d9-280">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b40d9-281">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b40d9-281">Additional resources</span></span>

* [<span data-ttu-id="b40d9-282">Linux における .NET Core の前提条件</span><span class="sxs-lookup"><span data-stu-id="b40d9-282">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
