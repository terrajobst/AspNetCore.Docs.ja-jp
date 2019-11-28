---
title: ASP.NET Core Blazor WebAssembly をホストしてデプロイする
author: guardrex
description: ASP.NET Core、Content Delivery Networks (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 0fcefc3f1e51beb7cc29aef6dd4f4b8557e61965
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963641"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly をホストしてデプロイする

作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)を使用すると、次のことが行われます。

* Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。
* アプリがブラウザー UI スレッド上で直接実行されます。

次の展開戦略がサポートされています。

* Blazor アプリは、ASP.NET Core アプリによって提供されます。 この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。
* Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。 この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで示されます。これには、Blazor WebAssembly アプリを IIS サブアプリとしてホストする方法についての情報が含まれています。

## <a name="rewrite-urls-for-correct-routing"></a>正しいルーティングのために URL を書き換える

Blazor WebAssembly アプリ内のページ コンポーネントに対するルーティング要求は、Blazor サーバー (ホストされているアプリ) でのルーティング要求のように単純なものではありません。 2 つのコンポーネントを含む Blazor WebAssembly を考えてみましょう。

* *Main.razor* &ndash; アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。
* *About.razor* &ndash; `About` コンポーネント。

アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:

1. ブラウザーにより要求が送信されます。
1. 既定のページ (通常は *index.html*) が返されます。
1. *index.html* によりアプリがブートストラップされます。
1. Blazor のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。

Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。 " *Blazor WebAssembly アプリ*" 内にある内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。 要求は、ルーターによって内部的に処理されます。

ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。 アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。

ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する *index.html* ページへのすべての要求を、書き換える必要があります。 *index.html* が返されると、アプリの Blazor ルーターがそれを受け取り、正しいリソースで応答します。

IIS サーバーに展開する場合は、アプリの発行される *web.config* ファイルで URL Rewrite Module を使用できます。 詳細については、「[IIS](#iis)」セクションを参照してください。

## <a name="hosted-deployment-with-aspnet-core"></a>ASP.NET Core でのホストされた展開

"*ホストされたデプロイ*" により、Blazor WebAssembly アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。

Blazor アプリは、発行された出力に ASP.NET Core アプリと共に含まれているため、2 つのアプリを一緒にデプロイすることができます。 ASP.NET Core アプリをホストできる Web サーバーが必要です。 ホストされているデプロイの場合、Visual Studio には **Blazor WebAssembly App** プロジェクト テンプレートが含まれており ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorwasm` テンプレート)、 **[ホスト]** オプションが選択されています。

ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。

Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。

## <a name="standalone-deployment"></a>スタンドアロン展開

"*スタンドアロン デプロイ*" により、Blazor WebAssembly アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。 任意の静的ファイル サーバーで Blazor アプリを提供できます。

スタンドアロン展開の資産は *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* フォルダーに発行されます。

### <a name="iis"></a>IIS

IIS は、Blazor アプリ対応の静的ファイル サーバーです。 Blazor をホストするよう IIS を構成する方法については、「[IIS で静的 Web サイトを構築する](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」を参照してください。

発行された資産は、 */bin/Release/<ターゲット フレームワーク>/publish* フォルダーに作成されます。 *publish*フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。

#### <a name="webconfig"></a>web.config

Blazor プロジェクトが発行されると、*web.config* ファイルが以下の IIS 構成で作成されます。

* 各ファイル拡張子に対して設定される MIME の種類は次のとおりです。
  * *.dll* &ndash; `application/octet-stream`
  * *.json* &ndash; `application/json`
  * *.wasm* &ndash; `application/wasm`
  * *.woff* &ndash; `application/font-woff`
  * *.woff2* &ndash; `application/font-woff`
* 次の MIME の種類に対しては、HTTP 圧縮が有効にされます。
  * `application/octet-stream`
  * `application/wasm`
* URL Rewrite Module のルールが確立されます。
  * アプリの静的なアセットが存在するサブディレクトリ ( *<アセンブリ名>/dist/<要求されたパス>* ) が提供されます。
  * ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント ( *<アセンブリ名>/dist/index.html*) にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。

#### <a name="install-the-url-rewrite-module"></a>URL リライト モジュールをインストールする

[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。 このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。 モジュールは、IIS Web サイトからダウンロードする必要があります。 Web Platform Installer を使用してモジュールをインストールします。

1. ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。 英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。 その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。
1. インストーラーをサーバーにコピーします。 インストーラーを実行します。 **[インストール]** ボタンを選択して、ライセンス条項に同意します。 インストールが完了した後、サーバーの再起動は必要はありません。

#### <a name="configure-the-website"></a>Web サイトを構成する

Web サイトの**物理パス**をアプリのフォルダーに設定します。 フォルダーには次のものが含まれます。

* *web.config* ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。
* アプリの静的なアセット フォルダー。

#### <a name="host-as-an-iis-sub-app"></a>IIS サブアプリとしてホストする

スタンドアロン アプリが IIS サブアプリとしてホストされている場合は、次のいずれかを実行します。

* 継承された ASP.NET Core モジュール ハンドラーを無効にします。

  Blazor アプリの発行された *web.config* ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* `inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

ハンドラーの削除または継承の無効化は、[アプリの基本パスの構成](xref:host-and-deploy/blazor/index#app-base-path)に加えて行われます。 IIS でサブアプリを構成するときに、アプリの *index.html* ファイル内のアプリのベース パスを、使用している IIS エイリアスに設定します。

#### <a name="troubleshooting"></a>トラブルシューティング

Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。 モジュールがインストールされていない場合、IIS では *web.config* ファイルを解析できません。 これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。

IIS への展開に関するトラブルシューティングの詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。

### <a name="azure-storage"></a>Azure ストレージ

[Azure Storage](/azure/storage/) の静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。 カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。

ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:

* **インデックス ドキュメント名**を `index.html` に設定します。
* **エラー ドキュメント パス**を `index.html` に設定します。 Razor コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。 このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。 *index.html* BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。

詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。

### <a name="nginx"></a>Nginx

以下に示す *nginx.conf* ファイルは、Nginx が対応するファイルをディスク上で見つけられないときは *index.html* ファイルを送信するよう Nginx を構成する方法を示すために、簡素化したものです。

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。

### <a name="nginx-in-docker"></a>Docker での Nginx

Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。 *nginx.config* ファイルをコンテナーにコピーするように、Dockerfile を更新します。

次の例に示すように、1 つの行を Dockerfile に追加します。

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Blazor WebAssembly アプリを CentOS 7 以降にデプロイするには:

1. Apache 構成ファイルを作成します。 次の例は、簡略化された構成ファイル (*blazorapp.config*) です。

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType aplication/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Apache 構成ファイルを `/etc/httpd/conf.d/` ディレクトリ (CentOS 7 の既定の Apache 構成ディレクトリ) に配置します。

1. アプリのファイルを `/var/www/blazorapp` ディレクトリ (構成ファイルで `DocumentRoot` に指定された場所) に配置します。

1. Apache サービスを再起動します。

詳細については、「[mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)」と「[mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)」を参照してください。

### <a name="github-pages"></a>GitHub ページ

URL の書き換えを処理するために、*404.html* ファイルを、要求を *index.html* ページにリダイレクトするスクリプトと共に追加します。 コミュニティが提供する実装例については、「[Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/)」(GitHub ページ用の単一ページ アプリ) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) を参照してください。 コミュニティのアプローチの使用例については、[GitHub 上の blazor-demo/blazor-demo.github.io に関するページ](https://github.com/blazor-demo/blazor-demo.github.io) ([ライブ サイト](https://blazor-demo.github.io/)) を参照してください。

組織のサイトではなくプロジェクトのサイトを使用している場合、*index.html*内の `<base>` タグを追加または更新します。 `href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `my-repository/`) に設定します。

## <a name="host-configuration-values"></a>ホストの構成値

[Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)では、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。

### <a name="content-root"></a>コンテンツ ルート

`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます ([コンテンツ ルート](xref:fundamentals/index#content-root))。 次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。

* コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。 アプリのディレクトリから、次のコマンドを実行します。

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* **IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。 この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。 Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>パス ベース

`--pathbase` 引数により、ルート以外の相対 URL パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。 次の例では、`/relative-URL-path` はアプリのパス ベースです。 詳細については、「[アプリのベースパス](xref:host-and-deploy/blazor/index#app-base-path)」を参照してください。

> [!IMPORTANT]
> `<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。 `<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。

* コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。 アプリのディレクトリから、次のコマンドを実行します。

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* **IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。 この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。 Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL

`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。

* コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。 アプリのディレクトリから、次のコマンドを実行します。

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* **IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。 この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。 Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>リンカーを構成する

Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各ビルド上で実行されます。 アセンブリのリンクはビルドで制御できます。 詳細については、<xref:host-and-deploy/blazor/configure-linker> を参照してください。
