---
title: ASP.NET Core の静的ファイル
author: rick-anderson
description: 静的ファイルを提供したり、それをセキュリティで保護したりする方法、および ASP.NET Core Web アプリで静的ファイルをホストするミドルウェアの動作を構成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 00bab51cb411552c884f85fa63d42d0691b401b1
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717274"
---
# <a name="static-files-in-aspnet-core"></a>ASP.NET Core の静的ファイル

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Scott Addie](https://twitter.com/Scott_Addie)

HTML、CSS、画像、JavaScript などの静的ファイルは、ASP.NET Core アプリにより直接クライアントに提供される資産です。 これらのファイルを提供するには、いくつかの構成が必要です。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="serve-static-files"></a>静的ファイルの提供

静的ファイルは、プロジェクトの [Web ルート](xref:fundamentals/index#web-root) ディレクトリ内に格納されています。 既定のディレクトリは *{content root}/wwwroot* ですが、[UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) メソッドを使用して変更できます。 詳細については、「[コンテンツ ルート](xref:fundamentals/index#content-root)」および「[Web ルート](xref:fundamentals/index#web-root)」を参照してください。

アプリの Web ホストでは、コンテンツのルート ディレクトリが把握されている必要があります。

::: moniker range=">= aspnetcore-2.0"

`WebHost.CreateDefaultBuilder` メソッドでは、コンテンツのルートが現在のディレクトリに設定されます。

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

`Program.Main` 内で [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) が呼び出され、コンテンツのルートが現在のディレクトリに設定されます。

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

静的ファイルには、[Web ルート](xref:fundamentals/index#web-root)に対する相対パスを使用してアクセスできます。 たとえば、**Web アプリケーション** プロジェクト テンプレートには、*wwwroot* フォルダー内の次のいくつかフォルダーが含まれています。

* **wwwroot**
  * **css**
  * **images**
  * **js**

*images* サブフォルダーのファイルにアクセスするための URI は、*http://\<server_address>/images/\<image_file_name>* です。 たとえば、 *http://localhost:9189/images/banner3.svg* です。

::: moniker range=">= aspnetcore-2.1"

.NET Framework を対象としている場合、プロジェクトに [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) パッケージを追加します。 .NET Core をターゲットとする場合、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)にこのパッケージが含まれます。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

.NET Framework を対象としている場合、プロジェクトに [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) パッケージを追加します。 .NET Core をターゲットとする場合、[Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)にこのパッケージが含まれます。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

プロジェクトに、[Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) パッケージを追加します。

::: moniker-end

静的ファイル サービスの提供を可能にする、[ミドルウェア](xref:fundamentals/middleware/index)を構成します。

### <a name="serve-files-inside-of-web-root"></a>Web ルート内のファイルの提供

`Startup.Configure` 内の [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) メソッドを呼び出します。

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

`UseStaticFiles` メソッドのパラメーターなしのオーバーロードによって、[Web ルート](xref:fundamentals/index#web-root)内のファイルが提供可能とマークされます。 次のマークアップは、*wwwroot/images/banner1.svg* を参照します。

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

上記のコードでは、チルダ文字 `~/` が [Web ルート](xref:fundamentals/index#web-root)を指します。

### <a name="serve-files-outside-of-web-root"></a>Web ルート外のファイルの提供

提供する静的ファイルが [Web ルート](xref:fundamentals/index#web-root)の外にあるディレクトリ階層について考えます。

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*

静的ファイル ミドルウェアを次のように構成すると、要求で *banner1.svg* ファイルにアクセスできます。

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

前述のコードでは、*MyStaticFiles* ディレクトリ階層は、*StaticFiles* URI セグメントでパブリックに公開されています。 *http://\<server_address>/StaticFiles/images/banner1.svg* に対する要求によって、*banner1.svg* ファイルが提供されます。

次のマークアップは、*MyStaticFiles/images/banner1.svg* を参照します。

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>HTTP 応答ヘッダーの設定

[StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) オブジェクトを使用すると、HTTP 応答ヘッダーを設定できます。 [Web ルート](xref:fundamentals/index#web-root)から提供される静的ファイルの構成に加えて、次のコードで `Cache-Control` ヘッダーを設定します。

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

[HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) メソッドは、[Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) パッケージにあります。

これらのファイルは、開発環境で 10 分間 (600 秒) パブリックにキャッシュ可能でした。

![Cache-Control ヘッダーが追加された応答ヘッダー](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>静的ファイルの承認

静的ファイル ミドルウェアでは、承認の確認は行いません。 *wwwroot* の下にあるものも含め、このミドルウェアによって提供されるすべてのファイルは、一般に公開されます。 承認に基づいてファイルを提供するには:

* *wwwroot* や静的ファイル ミドルウェアがアクセスできる任意のディレクトリの外にファイルを配置します。
* 承認が適用されるアクション メソッドを使用して提供するようにします。 [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) オブジェクトが返されます。

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>ディレクトリ参照の有効化

ディレクトリ参照では、Web アプリのユーザーが、ディレクトリ一覧および、指定したディレクトリ内のファイルを参照できるようになります。 ディレクトリ参照は、セキュリティ上の理由から既定で無効になっています (「[注意点](#considerations)」を参照)。 `Startup.Configure` で [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) メソッドを呼び出すと、ディレクトリの参照を有効にできます。

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

`Startup.ConfigureServices` から [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) メソッドを呼び出すと、必要なサービスを追加することができます。

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

上記のコードでは、URL *http://\<server_address>/MyImages* が使用され、各ファイルおよびフォルダーへのリンクを含む *wwwroot/images* フォルダーのディレクトリが参照できるようになります。

![ディレクトリ参照](static-files/_static/dir-browse.png)

参照を有効にした場合のセキュリティ上のリスクについては、「[注意点](#considerations)」を参照してください。

次の例の 2 つの `UseStaticFiles` 呼び出しを確認してください。 最初の呼び出しにより、*wwwroot* フォルダー内の静的ファイルが提供されます。 2 番目の呼び出しにより、URL *http://\<server_address>/MyImages* が使用され、*wwwroot/images* フォルダーのディレクトリ参照が有効になります。

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>既定のドキュメントの提供

設定された既定のホーム ページは、サイトを訪問するユーザーの論理的な開始点となります。 ユーザーが URI を完全修飾しない場合に既定のページを提供するには、`Startup.Configure` から [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) メソッドを呼び出します。

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> 既定のファイルを提供するには、`UseStaticFiles` の前に `UseDefaultFiles` が呼び出される必要があります。 `UseDefaultFiles` は、ファイルを実際には提供しない URL リライターです。 ファイルを提供するには、`UseStaticFiles` を使用して静的ファイル ミドルウェアを有効にします。

`UseDefaultFiles` を使用し、以下のフォルダーを検索します。

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

一覧で見つかった最初のファイルは、要求で完全修飾 URI が使用されたかのように提供されます。 ブラウザー URL は、要求された URI を反映し続けます。

次のコードによって、既定のファイル名が *mydefault.html* に変更されます。

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> は、`UseStaticFiles`、`UseDefaultFiles`、およびオプションとして `UseDirectoryBrowser` の機能を兼ね備えています。

次のコードによって、静的ファイルと既定ファイルが提供できるようになります。 ディレクトリ参照は有効にしません。

```csharp
app.UseFileServer();
```

次のコードは、ディレクトリ参照が有効になった、パラメーターなしのオーバーロード時に構築されます。

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

次のディレクトリ階層があるとします。

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

次のコードにより、`MyStaticFiles` への静的ファイル、既定ファイル、ディレクトリ参照が有効になります。

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`EnableDirectoryBrowsing` プロパティの値が `true` であるとき、`AddDirectoryBrowser` を呼び出す必要があります。

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

このファイル階層と前述のコードを使用すると、URL は次のように解決されます。

| URI            |                             応答  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

*MyStaticFiles* ディレクトリに既定の名前のファイルが存在しない場合、*http://\<server_address>/StaticFiles* によってクリック可能なリンクを含むディレクトリの一覧が返されます。

![静的ファイルの一覧](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> および <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> では、`http://{SERVER ADDRESS}/StaticFiles` (末尾のスラッシュなし) から `http://{SERVER ADDRESS}/StaticFiles/` (末尾のスラッシュあり) へのクライアント側リダイレクトが実行されます。 *StaticFiles* ディレクトリ内の相対 URL は、末尾のスラッシュがないと無効です。

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

[FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) クラスには、MIME コンテンツ タイプへのファイル拡張子のマッピングを行う `Mappings` プロパティが含まれます。 次の例では、いくつかのファイル拡張子は、既知の MIME タイプに登録されています。 *.rtf* 拡張子は置換され、 *.mp4* は削除されています。

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

「[MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml)」 (MIME コンテンツ タイプ) を参照してください。

## <a name="non-standard-content-types"></a>非標準のコンテンツ タイプ

静的ファイル ミドルウェアでは、約 400 個の既知のファイル コンテンツ タイプが認識されます。 ユーザーがファイルの種類が不明なファイルを要求した場合、静的ファイル ミドルウェアでその要求がパイプラインの次のミドルウェアに渡されます。 ミドルウェアで要求が処理されない場合、*404 見つかりません* という応答が返されます。 ディレクトリ参照が有効になっている場合、ディレクトリ一覧にファイルへのリンクが表示されます。

次のコードによって、不明なタイプを提供できるようにし、不明なファイルをイメージとしてレンダリングします。

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

上記のコードでは、不明なコンテンツ タイプ ファイルに対する要求は、イメージとして返されます。

> [!WARNING]
> [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) を有効にすると、セキュリティ上リスクとなります。 これは既定では無効で、使用は推奨されていません。 非標準の拡張子のファイルを提供する場合、より安全な代替となるのは、[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) です。

## <a name="serve-files-from-multiple-locations"></a>複数の場所からファイルを提供する

`UseStaticFiles` と `UseFileServer` の既定では、*wwwroot* をポイントするファイル プロバイダーが作成されます。 他のファイル プロバイダーを使用する `UseStaticFiles` および `UseFileServer` の追加インスタンスを作成して、他の場所からファイルを提供することができます。 詳細については、次を参照してください。[この GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/15578)します。

### <a name="considerations"></a>注意事項

> [!WARNING]
> `UseDirectoryBrowser` と `UseStaticFiles` では、機密データが漏洩することがあります。 本番では、ディレクトリ参照を無効にすることが、強く推奨されます。 `UseStaticFiles` や `UseDirectoryBrowser` でどのディレクトリが有効になっているか、慎重にご確認ください。 ディレクトリ全体とそのサブディレクトリが、パブリックにアクセス可能になります。 ファイルは、パブリックに提供するのに適した、 *\<content_root>/wwwroot* などの専用ディレクトリに格納します。 これらのファイルは、MVC ビュー、Razor ページ (2.x のみ)、構成ファイルなどとは別にします。

* `UseDirectoryBrowser` と `UseStaticFiles` で公開されるコンテンツの URL では、大文字と小文字が区別され、基になるファイル システムの文字制限の影響を受けます。 たとえば、Windows では大文字小文字は区別されますが、macOS と Linux ではされません。

* IIS でホストされている ASP.NET Core アプリは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を使用して、静的ファイルの要求を含む、すべての要求をアプリに転送します。 IIS 静的ファイル ハンドラーは使用されません。 モジュールによって処理される前に、これで要求が処理されることはありません。

* IIS マネージャーで次の手順を実行し、サーバーまたは Web サイト レベルで IIS の静的ファイル ハンドラーを削除します。
    1. **[モジュール]** 機能に移動します。
    1. 一覧の **[StaticFileModule]** を選択します。
    1. **[アクション]** サイドバーで、 **[削除]** をクリックします。

> [!WARNING]
> IIS の静的ファイル ハンドラーが有効になっており、**かつ**、ASP.NET Core モジュールが正しく構成されていない場合、静的ファイルにサービスが提供されます。 これは、たとえば、*web.config* ファイルが配置されていない場合などで発生します。

* アプリ プロジェクトの [Web ルート](xref:fundamentals/index#web-root)の外に、コード ファイル ( *.cs* と *.cshtml* を含む) を配置します。 これにより、アプリのクライアント側コンテンツとサーバー ベースのコードの間で、論理的な分離が作成されます。 これによって、サーバー側のコードが漏洩するのを防ぎます。

## <a name="additional-resources"></a>その他の技術情報

* [ミドルウェア](xref:fundamentals/middleware/index)
* [ASP.NET Core の概要](xref:index)
