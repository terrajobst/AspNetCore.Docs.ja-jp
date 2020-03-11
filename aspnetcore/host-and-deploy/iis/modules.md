---
title: ASP.NET Core での IIS モジュール
author: rick-anderson
description: ASP.NET Core アプリに対してアクティブおよび非アクティブな IIS モジュールについて、さらに IIS モジュールの管理方法についてを把握します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 0f13ef3eb1da03960ef1fa54d33532b6ebbdc128
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646646"
---
# <a name="iis-modules-with-aspnet-core"></a>ASP.NET Core での IIS モジュール

一部のネイティブ IIS モジュールとすべての IIS マネージド モジュールでは、ASP.NET Core アプリに対する要求を処理することはできません。 多くの場合、ASP.NET Core には、IIS のネイティブ モジュールおよびマネージド モジュールによって処理されるシナリオの代替が用意されています。

## <a name="native-modules"></a>ネイティブ モジュール

次の表は、ASP.NET Core アプリおよび ASP.NET Core モジュールで機能するネイティブ IIS モジュールを示します。

| Module | ASP.NET Core アプリで機能するか | ASP.NET Core のオプション |
| --- | :---: | --- |
| **匿名認証**<br>`AnonymousAuthenticationModule`                                  | はい | |
| **基本認証**<br>`BasicAuthenticationModule`                                          | はい | |
| **クライアント証明書マッピング認証**<br>`CertificateMappingAuthenticationModule`      | はい | |
| **CGI**<br>`CgiModule`                                                                           | いいえ  | |
| **構成検証**<br>`ConfigurationValidationModule`                                  | はい | |
| **HTTP エラー**<br>`CustomErrorModule`                                                           | いいえ  | [状態コード ページ ミドルウェア](xref:fundamentals/error-handling#usestatuscodepages) |
| **カスタム ログ**<br>`CustomLoggingModule`                                                      | はい | |
| **既定のドキュメント**<br>`DefaultDocumentModule`                                                  | いいえ  | [既定のファイル ミドルウェア](xref:fundamentals/static-files#serve-a-default-document) |
| **ダイジェスト認証**<br>`DigestAuthenticationModule`                                        | はい | |
| **ディレクトリの参照**<br>`DirectoryListingModule`                                               | いいえ  | [ディレクトリ参照ミドルウェア](xref:fundamentals/static-files#enable-directory-browsing) |
| **動的圧縮**<br>`DynamicCompressionModule`                                            | はい | [応答圧縮ミドルウェア](xref:performance/response-compression) |
| **失敗した要求のトレース**<br>`FailedRequestsTracingModule`                                     | はい | [ASP.NET Core のログ](xref:fundamentals/logging/index#tracesource-provider) |
| **ファイル キャッシュ**<br>`FileCacheModule`                                                            | いいえ  | [応答キャッシュ ミドルウェア](xref:performance/caching/middleware) |
| **HTTP キャッシュ**<br>`HttpCacheModule`                                                            | いいえ  | [応答キャッシュ ミドルウェア](xref:performance/caching/middleware) |
| **HTTP ログ**<br>`HttpLoggingModule`                                                          | はい | [ASP.NET Core のログ](xref:fundamentals/logging/index) |
| **HTTP リダイレクト**<br>`HttpRedirectionModule`                                                  | はい | [URL リライト ミドルウェア](xref:fundamentals/url-rewriting) |
| **HTTP トレース**<br>`TracingModule`                                                              | はい | |
| **IIS クライアント証明書マッピング認証**<br>`IISCertificateMappingAuthenticationModule` | はい | |
| **IP およびドメインの制限**<br>`IpRestrictionModule`                                          | はい | |
| **ISAPI フィルター**<br>`IsapiFilterModule`                                                         | はい | [ミドルウェア](xref:fundamentals/middleware/index) |
| **ISAPI**<br>`IsapiModule`                                                                       | はい | [ミドルウェア](xref:fundamentals/middleware/index) |
| **プロトコル サポート**<br>`ProtocolSupportModule`                                                  | はい | |
| **要求のフィルタリング**<br>`RequestFilteringModule`                                                | はい | [URL リライト ミドルウェア`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **要求監視**<br>`RequestMonitorModule`                                                    | はい | |
| **URL リライト**&#8224;<br>`RewriteModule`                                                      | はい | [URL リライト ミドルウェア](xref:fundamentals/url-rewriting) |
| **サーバー側インクルード**<br>`ServerSideIncludeModule`                                            | いいえ  | |
| **静的圧縮**<br>`StaticCompressionModule`                                              | いいえ  | [応答圧縮ミドルウェア](xref:performance/response-compression) |
| **静的コンテンツ**<br>`StaticFileModule`                                                         | いいえ  | [静的ファイル ミドルウェア](xref:fundamentals/static-files) |
| **トークン キャッシュ**<br>`TokenCacheModule`                                                          | はい | |
| **URI キャッシュ**<br>`UriCacheModule`                                                              | はい | |
| **URL 認証**<br>`UrlAuthorizationModule`                                                | はい | [ASP.NET Core ID](xref:security/authentication/identity) |
| **Windows 認証**<br>`WindowsAuthenticationModule`                                      | はい | |

&#8224; URL リライト モジュールの `isFile` および `isDirectory` 一致タイプは、[ディレクトリ構造](xref:host-and-deploy/directory-structure)の変更のため、ASP.NET Core アプリでは動作しません。

## <a name="managed-modules"></a>マネージド モジュール

アプリ プールの .NET CLR バージョンが **[マネージ コードなし]** に設定されている場合、マネージド モジュールはホストされた ASP.NET Core アプリでは機能 "*しません*"。 ASP.NET Core では、いくつかのケースにおいてミドルウェアの代替機能が提供されています。

| Module                  | ASP.NET Core のオプション |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| FileAuthorization       | |
| FormsAuthentication     | [Cookie 認証ミドルウェア](xref:security/authentication/cookie) |
| OutputCache             | [応答キャッシュ ミドルウェア](xref:performance/caching/middleware) |
| Profile                 | |
| RoleManager             | |
| ScriptModule-4.0        | |
| セッション                 | [セッション ミドルウェア](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [URL リライト ミドルウェア](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4.0    | [ASP.NET Core ID](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>IIS マネージャー アプリケーションの変更

IIS マネージャーを使って設定を構成すると、アプリの *web.config* ファイルが変更されます。 アプリを展開し、*web.config* を含めた場合、IIS マネージャーを使って行われた変更は、展開される *web.config* ファイルによって上書きされます。 サーバーの *web.config* ファイルを変更する場合は、サーバー上の更新された *web.config* ファイルをローカル プロジェクトにすぐにコピーしてください。

## <a name="disabling-iis-modules"></a>IIS モジュールの無効化

アプリに対して無効にする必要がある IIS モジュールがサーバー レベルで構成されている場合、アプリの *web.config* ファイルへの追加によってモジュールを無効にすることができます。 モジュールをそのまま残し、構成の設定を使って非アクティブにするか (使用可能な場合)、アプリからモジュールを削除します。

### <a name="module-deactivation"></a>モジュールの非アクティブ化

多くのモジュールには、アプリからモジュールを削除せずに無効にすることができる構成設定が用意されています。 これは、モジュールを非アクティブ化する最も簡単ですばやい方法です。 たとえば、HTTP リダイレクト モジュールは、*web.config* の `<httpRedirect>` 要素を使って無効にできます。

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

構成設定を使ってモジュールを無効にする方法について詳しくは、[IIS \<system.webServer >](/iis/configuration/system.webServer/) の "*子要素*" に関するセクションのリンクに従ってください。

### <a name="module-removal"></a>モジュールの削除

*web.config* の設定を使ってモジュールを削除する場合は、最初に、モジュールのロックを解除し、*web.config* の `<modules>` セクションのロックを解除します。

1. サーバー レベルでモジュールのロックを解除します。 IIS マネージャーの **[接続]** サイド バーで IIS サーバーを選びます。 **[IIS]** 領域で **[モジュール]** を開きます。 一覧でモジュールを選びます。 右側の **[アクション]** サイド バーで、 **[ロック解除]** を選びます。 モジュールのアクション エントリが **[ロック]** と表示される場合、モジュールのロックは既に解除されています。必要な操作はありません。 後で *web.config* から削除する予定のモジュールをできるだけ多くロック解除します。

2. *web.config* の `<modules>` セクションを指定しないでアプリを展開します。最初に IIS マネージャーでセクションをロック解除せずに、`<modules>` セクションを含む *web.config* を使ってアプリを展開した場合、セクションのロックを解除しようとすると Configuration Manager で例外がスローされます。 したがって、`<modules>` セクションを指定しないでアプリを展開します。

3. *web.config* の `<modules>` セクションのロックを解除します。 **[接続]** サイド バーの **[サイト]** で Web サイトを選びます。 **[管理]** 領域で**構成エディター**を開きます。 ナビゲーション コントロールを使って、`system.webServer/modules` セクションを選びます。 右側の **[アクション]** サイド バーで、セクションの **[ロック解除]** を選びます。 モジュール セクションのアクション エントリが **[セクションのロック]** と表示される場合、モジュール セクションのロックは既に解除されています。必要な操作はありません。

4. アプリのローカル *web.config* ファイルに `<modules>` セクションを追加するとき、`<remove>` 要素を指定するとアプリからモジュールが取り除かれます。 複数のモジュールを削除するには、複数の `<remove>` 要素を追加します。 サーバー上で *web.config* を変更した場合は、すぐにプロジェクトのローカルな *web.config* ファイルに対して同じ変更を行ってください。 この手法でモジュールを削除すると、サーバー上の他のアプリでのモジュールの使用に影響がありません。

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

*web.config* を利用して IIS Express のモジュールを追加または削除するには、`<modules>` セクションのロックを解除するには、*applicationHost.config* を変更します。

1. *{APPLICATION ROOT}\\.vs\config\applicationhost.config* を開きます。

1. IIS モジュールの `<section>` 要素を見つけ、`overrideModeDefault` を `Deny` から `Allow` に変更します。

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. `<location path="" overrideMode="Allow"><system.webServer><modules>` セクションを見つけます。 削除するモジュールに対して、`lockItem` を `true` から `false` に設定します。 次の例では、CGI モジュールのロックが解除されています。

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. `<modules>` セクションと個々のモジュールのロックが解除されたら、IIS Express でアプリを実行するためのアプリの *web.config* ファイルを利用し、IIS モジュールを自由に追加したり、削除したりできます。

IIS モジュールは、*Appcmd.exe* を使って削除することもできます。 コマンドで `MODULE_NAME` と `APPLICATION_NAME` を指定します。

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

たとえば、Default Web Site から `DynamicCompressionModule` を削除するには、次のようにします。

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>最小限のモジュール構成

ASP.NET Core アプリを実行するために必要なモジュールは、匿名認証モジュールと ASP.NET Core モジュールだけです。

URI キャッシュ モジュール (`UriCacheModule`) により、IIS は URL レベルで Web サイトの構成をキャッシュできます。 このモジュールがない場合、同じ URL が繰り返し要求された場合でも、IIS はすべての要求で構成を読み取って解析する必要があります。 すべての要求で構成を解析すると、パフォーマンスが大幅に低下します。 *URI キャッシュ モジュールはホストされた ASP.NET Core アプリを実行するために厳密には必要ありませんが、すべての ASP.NET Core の展開で URI キャッシュ モジュールを有効にすることをお勧めします。*

HTTP キャッシュ モジュール (`HttpCacheModule`) は、IIS 出力キャッシュと共に、HTTP.sys キャッシュ内のアイテムをキャッシュするためのロジックも実装します。 このモジュールがないと、コンテンツはカーネル モードでキャッシュされなくなり、キャッシュ プロファイルは無視されます。 HTTP キャッシュ モジュールを削除すると、通常、パフォーマンスとリソースの使用に悪影響があります。 *HTTP キャッシュ モジュールはホストされた ASP.NET Core アプリを実行するために厳密には必要ありませんが、すべての ASP.NET Core の展開で HTTP キャッシュ モジュールを有効にすることをお勧めします。*

## <a name="additional-resources"></a>その他の技術情報

* [IIS アーキテクチャの概要: IIS のモジュール](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [IIS モジュールの概要](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [IIS 7.0 のロールとモジュールのカスタマイズ](https://technet.microsoft.com/library/cc627313.aspx)
* [IIS \<system.webServer>](/iis/configuration/system.webServer/)
