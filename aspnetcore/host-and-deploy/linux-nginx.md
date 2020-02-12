---
title: Nginx 搭載の Linux で ASP.NET Core をホストする
author: guardrex
description: Ubuntu 16.04 でリバース プロキシとして Nginx をセットアップし、Kestrel で実行している ASP.NET Core Web アプリに HTTP トラフィックを転送する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 7f17be1d883e8cce375487aa39f4d1ebbe8a95f4
ms.sourcegitcommit: bd896935e91236e03241f75e6534ad6debcecbbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77044867"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Nginx 搭載の Linux で ASP.NET Core をホストする

[Sourabh Shirhatti](https://twitter.com/sshirhatti) による投稿

このガイドでは、Ubuntu 16.04 サーバーで本稼働対応の ASP.NET Core 環境をセットアップする方法について説明します。 これらの手順は、Ubuntu のより新しいバージョンで動作する可能性が高いですが、手順はまだ新しいバージョンでテストされていません。

ASP.NET Core でサポートされている他の Linux ディストリビューションについて詳しくは、「[Linux における .NET Core の前提条件](/dotnet/core/linux-prerequisites)」をご覧ください。

> [!NOTE]
> Ubuntu 14.04 の場合、Kestrel プロセスを監視するためのソリューションとして *supervisord* が推奨されます。 *systemd* は Ubuntu 14.04 ではご利用いただけません。 Ubuntu 14.04 の手順については、[このトピックの前のバージョンを参照してください](https://github.com/aspnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)。

このガイドでは:

* リバース プロキシ サーバーの背後に既存の ASP.NET Core アプリを配置します。
* Kestrel Web サーバーに要求を転送するようにリバース プロキシ サーバーを設定します。
* Web アプリを起動時にデーモンとして実行します。
* Web アプリの再起動に役立つようにプロセス管理ツールを構成します。

## <a name="prerequisites"></a>必須コンポーネント

1. Ubuntu 16.04 サーバーへのアクセスと sudo 特権が与えられた標準ユーザー アカウント。
1. サーバーへの .NET Core ランタイムのインストール。
   1. 「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページにアクセスします。
   1. プレビューでない最新の .NET Core バージョンを選択します。
   1. **[Run apps - Runtime]\(アプリの実行 - ランタイム\)** の下の表でプレビューでない最新のランタイムをダウンロードします。
   1. Linux の 「**パッケージ マネージャーの手順**」 リンクを選択し、ご利用の Ubuntu バージョン用の Ubuntu 手順に従います。
1. 既存の ASP.NET Core アプリ。

共有フレームワークをアップグレードした後の任意の時点で、サーバーによってホストされている ASP.NET Core アプリを再起動します。

## <a name="publish-and-copy-over-the-app"></a>アプリを介して発行およびコピーする

[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)用にアプリを構成します。

アプリがローカル環境で実行されていて、セキュリティで保護された接続 (HTTPS) を行うように構成されていない場合は、次の方法のいずれかを採用します。

* セキュリティで保護されたローカル接続を処理するようにアプリを構成します。 詳しくは、「[HTTPS の構成](#https-configuration)」セクションをご覧ください。
* *Properties/launchSettings.json* ファイルの `applicationUrl` プロパティから `https://localhost:5001` を削除します (ある場合)。

開発環境から [dotnet publish](/dotnet/core/tools/dotnet-publish) を実行し、サーバー上で実行できるディレクトリ (たとえば、*bin/Release/&lt;target_framework_moniker&gt;/publish*) にアプリをパッケージします。

```dotnetcli
dotnet publish --configuration Release
```

サーバーで .NET Core ランタイムを管理しない場合、アプリは[独立した展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行することもできます。

組織のワークフローに統合されているツール (SCP や SFTP など) を使用して、サーバーに ASP.NET Core アプリをコピーします。 Web アプリは一般的に *var* ディレクトリの下に配置されます (たとえば、*var/www/helloapp*)。

> [!NOTE]
> 運用展開シナリオの場合、継続的インテグレーション ワークフローが、アプリの発行処理とサーバーへの資産のコピーを行います。

アプリをテストします。

1. コマンド ラインから `dotnet <app_assembly>.dll` アプリを実行します。
1. ブラウザーで、`http://<serveraddress>:<port>` に移動し、アプリが Linux のローカルで動作することを検証します。

## <a name="configure-a-reverse-proxy-server"></a>リバース プロキシ サーバーを構成する

リバース プロキシは、動的 Web アプリを提供するための一般的な仕組みです。 リバース プロキシは HTTP 要求を終了させ、ASP.NET Core アプリに転送します。

### <a name="use-a-reverse-proxy-server"></a>リバース プロキシ サーバーを利用する

Kestrel は、ASP.NET Core から動的なコンテンツを提供するのに役立ちます。 ただし、Web サーバーとしての機能は、IIS、Apache、Nginx などのサーバーと比べると制限されます。 リバース プロキシ サーバーは、静的コンテンツ サービス、要求のキャッシュ、要求の圧縮、HTTP サーバーからの HTTPS 終了などの作業の負荷を軽減します。 リバース プロキシ サーバーは専用コンピューター上に置かれることもあれば、HTTP サーバーと並んで展開されることもあります。

このガイドの目的のために、単一インスタンスの Nginx が使用されます。 HTTP サーバーと並んで、同じサーバー上で実行されます。 要件に応じて、別のセットアップを選択することも可能です。

要求はリバース プロキシによって転送されます。そのため、[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) パッケージの [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) を使用します。 リダイレクト URI とその他のセキュリティ ポリシーを正しく機能させるために、このミドルウェアは、`X-Forwarded-Proto` ヘッダーを利用して、`Request.Scheme` を更新します。

認証、リンクの生成、リダイレクト、および地理的位置情報など、スキームに依存するすべてのコンポーネントは、Forwarded Headers Middleware の呼び出し後に配置する必要があります。 一般的な規則として、Forwarded Headers Middleware は、診断およびエラー処理ミドルウェアを除くその他のミドルウェアより前に実行される必要があります。 この順序により、転送されるヘッダー情報に依存するミドルウェアが処理にヘッダー値を使用できます。

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> や同様の認証スキーム ミドルウェアを呼び出す前に、`Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> メソッドを呼び出します。 ミドルウェアを構成して、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

ミドルウェアに対して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が指定されていない場合、転送される既定のヘッダーは `None` です。

標準 localhost アドレス (127.0.0.1) など、ループバック アドレス (127.0.0.0/8、[::1]) 上で実行するプロキシは、既定で信頼されます。 組織内のその他の信頼されているプロキシまたはネットワークによってインターネットと Web サーバーの間の要求が処理される場合は、それらを、<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> を使用して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> または <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> のリストに追加します。 次の例では、IP アドレス 10.0.0.100 にある信頼されているプロキシ サーバーが `Startup.ConfigureServices` 内の Forwarded Headers Middleware `KnownProxies` に追加されます。

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

詳細については、「<xref:host-and-deploy/proxy-load-balancer>」を参照してください。

### <a name="install-nginx"></a>Nginx をインストールする

`apt-get` を利用し、Nginx をインストールします。 インストーラーにより *systemd* init スクリプトが作成されます。このスクリプトがシステム起動時に Nginx をデーモンとして実行します。 「[Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)」 (Nginx: 公式 Debian/Ubuntu パッケージ) にある Ubuntu 用のインストールの指示に従います。

> [!NOTE]
> オプションの Nginx モジュールが必要な場合、Nginx をソースからビルドする必要がある場合があります。

Nginx は初めてのインストールとなるので、次を実行して明示的に起動します。

```bash
sudo service nginx start
```

ブラウザーで Nginx の既定のランディング ページが表示されることを確認します。 ランディング ページは `http://<server_IP_address>/index.nginx-debian.html` からアクセスできます。

### <a name="configure-nginx"></a>Nginx を構成する

Nginx をリバース プロキシとして構成し、ASP.NET Core アプリに要求を転送するには、 */etc/nginx/sites-available/default* を変更します。 テキスト エディターで開き、中身を次のものに変更します。

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

SignalR Websocket に依存する Blazor サーバー アプリの場合、`Connection` ヘッダーを設定する方法については、「<xref:host-and-deploy/blazor/server#linux-with-nginx>」を参照してください。

`server_name` が一致しない場合、Nginx では既定のサーバーが使用されます。 既定のサーバーが定義されていない場合、構成ファイルの最初のサーバーが既定のサーバーとなります。 ベスト プラクティスとして、構成ファイルで 444 の状態コードを返す既定のサーバーを具体的に追加します。 既定のサーバーの構成例は次のとおりです。

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

上記の構成ファイルと既定のサーバーでは、Nginx は、ホスト ヘッダー `example.com` または `*.example.com` で、ポート 80 でパブリック トラフィックを受け入れます。 これらのホストと一致しない要求は、Kestrel に転送されません。 Nginx は一致する要求を Kestrel (`http://localhost:5000`) に転送します。 詳細については、「[How nginx processes a request](https://nginx.org/docs/http/request_processing.html)」(Nginx が要求を処理する方法) をご覧ください。 Kestrel の IP/ポートを変更するには、[Kestrel のエンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するセクションを参照してください。

> [!WARNING]
> 適切な [server_name directive](https://nginx.org/docs/http/server_names.html) を指定しないと、アプリにセキュリティ上の脆弱性が生じます。 親ドメイン全体を制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.example.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。 詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。

Nginx の構成を確立したら、`sudo nginx -t` を実行して構成ファイルの構文を確認します。 構成ファイルがテストに合格したら、`sudo nginx -s reload` を実行することで、強制的に Nginx に変更を反映させます。

アプリをサーバーで直接実行するには、次を実行します。

1. アプリのディレクトリに移動します。
1. アプリを実行する:`dotnet <app_assembly.dll>`。ここで、`app_assembly.dll` はアプリのアセンブリ ファイル名です。

アプリがサーバーで実行されているにも関わらず、インターネット上で応答がない場合、サーバーのファイアウォールを確認し、ポート 80 が開かれていることを確認します。 Azure Ubuntu VM を使用している場合、受信ポート 80 のトラフィックを有効にするネットワーク セキュリティ グループ (NSG) 規則を追加します。 送信トラフィックは、受信規則が有効になると自動的に生成されるので、送信ポート 80 規則を有効にする必要はありません。

アプリのテストが終了したら、コマンド プロンプトで `Ctrl+C` を使用してアプリをシャットダウンします。

## <a name="monitor-the-app"></a>アプリを監視する

サーバーは、`http://<serveraddress>:80` に対する要求を Kestrel で実行されている ASP.NET Core アプリ (`http://127.0.0.1:5000`) に転送するようにセットアップされました。 ただし、Nginx は Kestrel プロセスを管理するようには設定されていません。 *systemd* を使用してサービス ファイルを作成し、基になる Web アプリを起動して監視できます。 *systemd* は init システムであり、プロセスを起動、停止、管理するためのさまざまな高性能機能を提供します。 

### <a name="create-the-service-file"></a>サービス ファイルを作成する

次のように、サービス定義ファイルを作成します。

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

アプリのサービス ファイルの例を次に示します。

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

前の例では、サービスを管理するユーザーは `User` オプションによって指定されています。 ユーザー (`www-data`) が存在し、アプリのファイルの適切な所有権を持っている必要があります。

アプリが最初の割り込み信号を受信してからシャットダウンするのを待機する期間を構成するには、`TimeoutStopSec` を使用します。 この期間内にアプリがシャットダウンしない場合は、SIGKILL を発行してアプリを終了します。 タイムアウトを無効にするには、値として、単位なしの秒数 (`150` など)、期間の値 (`2min 30s` など)、または `infinity` を指定します。 `TimeoutStopSec` は、既定ではマネージャー構成ファイル (*systemd-system.conf*、*system.conf.d*、*systemd-user.conf*、*user.conf.d*) 内の `DefaultTimeoutStopSec` の値に設定されます。 ほとんどのディストリビューションにおいて、タイムアウトの既定値は 90 秒となります。

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux のファイル システムは大文字と小文字を区別します。 ASPNETCORE_ENVIRONMENT を "Production" に設定すると、構成ファイル *appsettings.Production.json* が検索されます。*appsettings.production.json* ではありません。

構成プロバイダーが環境変数を読み取れるようにするために、一部の値 (たとえば SQL の接続文字列) をエスケープする必要があります。 次のコマンドを使用して、構成ファイルで使用するために適切にエスケープされた値を生成します。

```console
systemd-escape "<value-to-escape>"
```

コロン (`:`) 区切り記号は、環境変数の名前ではサポートされていません。 コロンの代わりに 2 つのアンダースコア (`__`) を使用します。 環境変数が構成に読み取られるときに、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)によって 2 つのアンダースコアがコロンに変換されます。 次の例では、接続文字列キー `ConnectionStrings:DefaultConnection` はサービス定義ファイルでは `ConnectionStrings__DefaultConnection` と設定されています。

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

ファイルを保存し、サービスを有効にします。

```bash
sudo systemctl enable kestrel-helloapp.service
```

サービスを起動し、動作を確認します。

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

リバース プロキシが構成され、Kestrel は systemd 経由で管理されます。これで Web アプリは完全に構成され、`http://localhost` でローカル コンピューター上のブラウザーからアクセスできます。 妨げとなるファイアウォールがなければ、リモート コンピューターからもアクセスできます。 応答ヘッダーを調べると、ASP.NET Core アプリが Kestrel によってサービス提供されていることが `Server` ヘッダーに示されています。

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>ログを表示する

Kestrel を利用する Web アプリは `systemd` を使用して管理されるため、すべてのイベントとプロセスが記録され、中心的ジャーナルが生成されます。 ただし、このジャーナルには、`systemd` が管理するすべてのサービスとプロセスのすべてのエントリが含まれます。 `kestrel-helloapp.service` 固有の項目を表示するには、次のコマンドを使用します。

```bash
sudo journalctl -fu kestrel-helloapp.service
```

さらに絞り込むには、時間オプションとして `--since today`、`--until 1 hour ago`、あるいはこの 2 つの組み合わせを利用すれば、返されるエントリの数を減らすことができます。

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>データの保護

[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証ミドルウェア (Cookie ミドルウェアなど) やクロスサイト リクエスト フォージェリ (CSRF) 保護を含む、いくつかの ASP.NET Core [ ミドルウェア](xref:fundamentals/middleware/index)で使用されます。 データ保護 API がユーザーのコードから呼び出されない場合でも、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。 データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。

キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。

* すべての cookie ベースの認証トークンは無効になります。
* ユーザーは、次回の要求時に再度サインインする必要があります。
* キーリングで保護されているデータは、いずれも復号化できなくなります。 これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。

キー リングを永続化して暗号化するようにデータ保護を構成する場合は、次を参照してください。

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>要求ヘッダー フィールドが長すぎます

プロキシ サーバーの既定の設定では、通常、プラットフォームに応じて、要求ヘッダー フィールドが 4 K または 8 K に制限されます。 アプリでは、既定値よりも長いフィールドが必要になる場合があります (たとえば、[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用するアプリ)。 長いフィールドが必要な場合は、プロキシ サーバーの既定の設定に対して調整が必要になります。 適用する値は、シナリオによって異なります。 詳細については、ご利用のサーバーのドキュメントを参照してください。

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> 必要な場合を除いて、プロキシ バッファーの既定値を増やさないでください。 これらの値を増やすと、悪意のあるユーザーによるバッファー オーバーラン (オーバーフロー) とサービス拒否 (DoS) の攻撃のリスクが増加します。

## <a name="secure-the-app"></a>アプリをセキュリティで保護する

### <a name="enable-apparmor"></a>AppArmor を有効にする

Linux Security Modules (LSM) は、Linux 2.6 以降の Linux カーネルに含まれるフレームワークです。 LSM は、セキュリティ モジュールのさまざまな実装に対応しています。 [AppArmor](https://wiki.ubuntu.com/AppArmor) は Mandatory Access Control システムを実装する LSM です。このシステムは、プログラムのリソース範囲を限定できます。 AppArmor が有効であり、正しく構成されていることを確認します。

### <a name="configure-the-firewall"></a>ファイアウォールを構成する

使用されていないすべての外部ポートを閉じます。 Uncomplicated firewall (ufw) は `iptables` のフロント エンドとなり、ファイアウォールを構成するための CLI が提供されます。

> [!WARNING]
> ファイアウォールが正しく構成されていない場合、システム全体にアクセスできません。 正しい SSH ポートを指定しないと、SSH を使用してシステムに接続する場合に、そのシステムから事実上閉め出されることになります。 既定のポートは 22 です。 詳細については、[ufw の概要](https://help.ubuntu.com/community/UFW)と[マニュアル](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)を参照してください。

`ufw` をインストールし、必要なすべてのポートでトラフィックを許可するように構成します。

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Nginx をセキュリティで保護する

#### <a name="change-the-nginx-response-name"></a>Nginx 応答名を変更する

*src/http/ngx_http_header_filter_module.c* を編集します。

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>構成オプション

必要なその他のモジュールでサーバーを構成します。 アプリのセキュリティを強化するために、[ModSecurity](https://www.modsecurity.org/) のような Web アプリのファイアウォールの使用を検討してください。

#### <a name="https-configuration"></a>HTTPS の構成

**セキュリティで保護された (HTTPS) ローカル接続用にアプリを構成する**

[dotnet run](/dotnet/core/tools/dotnet-run) コマンドでは、アプリの *Properties/launchSettings.json* ファイルが使用されます。このファイルでは、`applicationUrl` プロパティによって提供される URL でリッスンするように、アプリが構成されます (例: `https://localhost:5001; http://localhost:5000`)。

次のいずれかの方法を使用して、`dotnet run` コマンド用の開発または開発環境 (Visual Studio Code の F5 または Ctrl + F5 キー) で証明書を使用するように、アプリを構成します。

* [構成から既定の証明書を置き換える](xref:fundamentals/servers/kestrel#configuration) (*推奨*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**セキュリティで保護された (HTTPS) クライアント接続用にリバース プロキシを構成する**

* 信頼できる証明機関 (CA) が発行した、有効な証明書を指定することで、ポート `443` で HTTPS トラフィックを待ち受けるようにサーバーを構成します。

* 次の */etc/nginx/nginx.conf* ファイルで示されているプラクティスの一部を採用することで、セキュリティを強化します。 たとえば、強力な暗号を選択したり、HTTP 経由のすべてのトラフィックを HTTPS にリダイレクトしたりします。

* `HTTP Strict-Transport-Security` (HSTS) ヘッダーを追加すると、クライアントが行う後続のすべての要求が HTTPS 経由になります。

* HSTS ヘッダーを追加しないでください。今後 HTTPS を利用できないことがあれば、適切な `max-age` を選択してください。

*/etc/nginx/proxy.conf* 構成ファイルを追加します。

[!code-nginx[](linux-nginx/proxy.conf)]

*/etc/nginx/nginx.conf* 構成ファイルを編集します。 この例では、1 つの構成ファイルに `http` セクションと `server` セクションの両方が含まれています。

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Nginx をクリックジャッキングから守る

[クリックジャッキング](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)は "*UI 着せ替え攻撃*" とも呼ばれ、Web サイトの訪問者を騙して現在訪れているものとは異なるページのリンクやボタンをクリックさせる悪意のある攻撃です。 サイトをセキュリティで保護するには、`X-FRAME-OPTIONS` を使います。

クリックジャッキング攻撃を軽減するには、次の手順に従います。

1. *nginx.conf* ファイルを編集します。

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   行 `add_header X-Frame-Options "SAMEORIGIN";` を追加します。
1. ファイルを保存します。
1. Nginx を再起動します。

#### <a name="mime-type-sniffing"></a>MIME タイプ スニッフィング

このヘッダーは応答コンテンツの種類をオーバーライドしないようにブラウザーに指示するので、ほとんどのブラウザーで MIME スニッフィングが阻止されます。 `nosniff` オプションを指定すると、サーバーがコンテンツを "text/html" と読むとき、ブラウザーはそれを "text/html" として表示します。

*nginx.conf* ファイルを編集します。

```bash
sudo nano /etc/nginx/nginx.conf
```

行 `add_header X-Content-Type-Options "nosniff";` を追加し、ファイルを保存し、Nginx を再起動します。

## <a name="additional-nginx-suggestions"></a>Nginx に関するその他の推奨事項

サーバー上で共有フレームワークをアップグレードしたら、サーバーによってホストされている ASP.NET Core アプリを再起動します。

## <a name="additional-resources"></a>その他の技術情報

* [Linux における .NET Core の前提条件](/dotnet/core/linux-prerequisites)
* [Nginx: バイナリ リリース: 公式 Debian/Ubuntu パッケージ](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX: 転送されるヘッダーの使用](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
