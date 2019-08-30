---
title: ASP.NET Core でのファイル プロバイダー
author: guardrex
description: ASP.NET Core がファイル プロバイダーを使用してファイル システムへのアクセスを抽象化する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 44c439dce893d486668bf8ac3f20cdf7952c5186
ms.sourcegitcommit: 0774a61a3a6c1412a7da0e7d932dc60c506441fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70059089"
---
# <a name="file-providers-in-aspnet-core"></a>ASP.NET Core でのファイル プロバイダー

作成者: [Steve Smith](https://ardalis.com/)、[Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core は、ファイル プロバイダーを使用してファイル システムへのアクセスを抽象化します。 ファイル プロバイダーは、ASP.NET Core フレームワークの全体で使用されます。

* `IWebHostEnvironment` では、アプリのコンテンツ ルートと Web ルートを `IFileProvider` 型として公開します。
* [静的ファイル ミドルウェア](xref:fundamentals/static-files)では、ファイル プロバイダーを使用して静的なファイルを見つけます。
* [Razor](xref:mvc/views/razor) では、ファイル プロバイダーを使用してページとビューを見つけます。
* .NET Core Tooling では、ファイル プロバイダーと glob パターンを使用して、公開するファイルを指定します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="file-provider-interfaces"></a>ファイル プロバイダーのインターフェイス

プライマリ インターフェイスは <xref:Microsoft.Extensions.FileProviders.IFileProvider> です。 `IFileProvider` では次のためのメソッドが公開されます。

* ファイルの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。
* ディレクトリの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。
* 変更通知を設定します (<xref:Microsoft.Extensions.Primitives.IChangeToken> を使用)。

`IFileInfo` ではファイルを操作するためのメソッドとプロパティが提供されます。

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (バイト単位)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> の日付

[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) メソッドを使用してファイルから読み取ることができます。

サンプル アプリでは、[依存関係の挿入](xref:fundamentals/dependency-injection)を介してアプリ全体で使用するために、`Startup.ConfigureServices` でファイル プロバイダーを構成する方法を示します。

## <a name="file-provider-implementations"></a>ファイル プロバイダーの実装

利用できる `IFileProvider` の実装は 3 つあります。

| 実装 | 説明 |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | システムの物理ファイルにアクセスするために、物理プロバイダーが使用されます。 |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | アセンブリに埋め込まれているファイルにアクセスするために、マニフェストが埋め込まれたプロバイダーが使用されます。 |
| [CompositeFileProvider](#compositefileprovider) | コンポジット プロパイダーは、その他の 1 つまたは複数のプロバイダーからのファイルおよびディレクトリに対するアクセスを結合する場合に使用されます。 |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> は、物理ファイル システムへのアクセス許可を提供します。 `PhysicalFileProvider` では、<xref:System.IO.File?displayProperty=fullName> 型が使用され (物理プロバイダーの場合)、すべてのパスのスコープが 1 つのディレクトリとその子ディレクトリに設定されます。 このスコープ設定により、指定されたディレクトリとその子ディレクトリを除くファイル システムにアクセスできなくなります。 `PhysicalFileProvider` を作成して使用する最も一般的なシナリオは、[依存関係の挿入](xref:fundamentals/dependency-injection)を通してコンストラクターで `IFileProvider` を要求する場合です。

このプロバイダーを直接インスタンス化するときは、ディレクトリ パスが要求され、そのプロバイダーを使用して行われるすべての要求のベース パスとして機能します。

次のコードでは、`PhysicalFileProvider` の作成方法と、これを使ってディレクトリのコンテンツとファイル情報を取得する方法が示されます。

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

前の例の型は次のとおりです。

* `provider` は `IFileProvider` です。
* `contents` は `IDirectoryContents` です。
* `fileInfo` は `IFileInfo` です。

ファイル プロバイダーを使用して、`applicationRoot` で指定したディレクトリ全体を反復処理したり、`GetFileInfo` を呼び出してファイル情報を取得したりできます。 ファイル プロバイダーは、`applicationRoot` ディレクトリの外部にはアクセスできません。

サンプル アプリの `Startup.ConfigureServices` クラスでは、[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) を使用してプロバイダーを作成しています。

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> は、アセンブリに埋め込まれたファイルにアクセスするために使用されます。 `ManifestEmbeddedFileProvider` では、アセンブリにコンパイルされたマニフェストを使用して、埋め込まれたファイルの元のパスを再構築します。

[Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) パッケージに対するプロジェクトに、パッケージ参照を追加します。

埋め込みファイルのマニフェストを生成するには、`<GenerateEmbeddedFilesManifest>` プロパティを `true` に設定します。 [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) を使用して埋め込むファイルを指定します。

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

[glob パターン](#glob-patterns)を使用して、アセンブリに埋め込むファイルを 1 つまたは複数指定します。

サンプル アプリでは `ManifestEmbeddedFileProvider` を作成して、現在実行しているアセンブリをそのコンストラクターに渡します。

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

追加のオーバーロードを使用すると、次のことが可能になります。

* 相対ファイル パスを指定します。
* ファイルのスコープを最終変更日に設定します。
* 埋め込みファイルのマニフェストを含む埋め込みリソースに名前を付けます。

| オーバーロード | 説明 |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | 必要に応じて相対パスのパラメーター `root` を指定できます。 `root` を指定して、<xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> の呼び出しのスコープを指定したパス以下のリソースに設定します。 |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 必要に応じて、相対パス パラメーター `root` および日付パラメーター `lastModified` (<xref:System.DateTimeOffset>) を指定できます。 `lastModified` の日付では、<xref:Microsoft.Extensions.FileProviders.IFileProvider> によって返される <xref:Microsoft.Extensions.FileProviders.IFileInfo> インスタンスの最終更新日のスコープを設定します。 |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 必要に応じて、相対パス `root`、日付 `lastModified`、`manifestName` パラメーターを指定できます。 `manifestName` は、マニフェストを含む埋め込みリソースの名前を表します。 |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> は、`IFileProvider` インスタンスを結合し、複数のプロバイダーからのファイルを操作するための 1 つのインターフェイスを公開します。 `CompositeFileProvider` を作成する場合、1 つまたは複数の `IFileProvider` インスタンスをそのコンストラクターに渡します。

サンプル アプリでは、`PhysicalFileProvider` と `ManifestEmbeddedFileProvider` が、アプリのサービス コンテナーに登録されている `CompositeFileProvider` にファイルを提供します。

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>変更の監視

[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) メソッドによって、1 つまたは複数のファイルやディレクトリに変更がないかどうか監視するシナリオが提供されます。 `Watch` にはパス文字列を指定できます。ここでは、[glob パターン](#glob-patterns)を使用して複数のファイルを指定できます。 `Watch` では <xref:Microsoft.Extensions.Primitives.IChangeToken> が返されます。 変更トークンでは次のものが公開されます。

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; このプロパティを調べることで、変更があったかどうかを判断できます。
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 指定したパス文字列に対して変更が検出されたときに呼び出されます。 各変更トークンは、1 つの変更への応答として、関連付けられたコールバックを呼び出すのみです。 定数の監視を有効にするには、<xref:System.Threading.Tasks.TaskCompletionSource`1> を使用するか (以下を参照)、変更への応答として `IChangeToken` インスタンスを再作成します。

サンプル アプリでは、*WatchConsole* コンソール アプリは、テキスト ファイルが変更されるたびにメッセージを表示するように構成されています。

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Docker コンテナーやネットワーク共有など、一部のファイル システムは、変更通知を確実に送信しない可能性があります。 `DOTNET_USE_POLLING_FILE_WATCHER` 環境変数を `1` または `true` に設定して、変更がないかどうか、4 秒ごとにファイル システムをポーリングして確認します (構成不可)。

## <a name="glob-patterns"></a>glob パターン

ファイル システム パスは、"*glob (または globbing) パターン*" と呼ばれるワイルドカード パターンを使用します。 これらのパターンを使用して、ファイルのグループを指定します。 2 つのワイルドカード文字は、`*` と `**` です。

**`*`**  
現在のフォルダー レベルにある任意の要素、任意のファイル名、または任意のファイル拡張子を照合します。 照合はファイル パス内の `/` 文字および `.` 文字によって終了します。

**`**`**  
複数のディレクトリ レベルにわたって任意の要素を照合します。 ディレクトリ階層内の多数のファイルを再帰的に照合する場合に使用できます。

**glob パターンの例**

**`directory/file.txt`**  
特定のディレクトリ内の特定のファイルを照合します。

**`directory/*.txt`**  
特定のディレクトリ内の *.txt* 拡張子を持つすべてのファイルを照合します。

**`directory/*/appsettings.json`**  
*directory* フォルダーのちょうど 1 つ下のレベルにあるディレクトリ内のすべての `appsettings.json` ファイルを照合します。

**`directory/**/*.txt`**  
*directory* フォルダーの下の任意の場所にある、 *.txt* 拡張子を持つすべてのファイルを照合します。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core は、ファイル プロバイダーを使用してファイル システムへのアクセスを抽象化します。 ファイル プロバイダーは、ASP.NET Core フレームワークの全体で使用されます。

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> では、アプリのコンテンツ ルートと Web ルートを `IFileProvider` 型として公開します。
* [静的ファイル ミドルウェア](xref:fundamentals/static-files)では、ファイル プロバイダーを使用して静的なファイルを見つけます。
* [Razor](xref:mvc/views/razor) では、ファイル プロバイダーを使用してページとビューを見つけます。
* .NET Core Tooling では、ファイル プロバイダーと glob パターンを使用して、公開するファイルを指定します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="file-provider-interfaces"></a>ファイル プロバイダーのインターフェイス

プライマリ インターフェイスは <xref:Microsoft.Extensions.FileProviders.IFileProvider> です。 `IFileProvider` では次のためのメソッドが公開されます。

* ファイルの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。
* ディレクトリの情報を取得します (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。
* 変更通知を設定します (<xref:Microsoft.Extensions.Primitives.IChangeToken> を使用)。

`IFileInfo` ではファイルを操作するためのメソッドとプロパティが提供されます。

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (バイト単位)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> の日付

[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) メソッドを使用してファイルから読み取ることができます。

サンプル アプリでは、[依存関係の挿入](xref:fundamentals/dependency-injection)を介してアプリ全体で使用するために、`Startup.ConfigureServices` でファイル プロバイダーを構成する方法を示します。

## <a name="file-provider-implementations"></a>ファイル プロバイダーの実装

利用できる `IFileProvider` の実装は 3 つあります。

| 実装 | 説明 |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | システムの物理ファイルにアクセスするために、物理プロバイダーが使用されます。 |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | アセンブリに埋め込まれているファイルにアクセスするために、マニフェストが埋め込まれたプロバイダーが使用されます。 |
| [CompositeFileProvider](#compositefileprovider) | コンポジット プロパイダーは、その他の 1 つまたは複数のプロバイダーからのファイルおよびディレクトリに対するアクセスを結合する場合に使用されます。 |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> は、物理ファイル システムへのアクセス許可を提供します。 `PhysicalFileProvider` では、<xref:System.IO.File?displayProperty=fullName> 型が使用され (物理プロバイダーの場合)、すべてのパスのスコープが 1 つのディレクトリとその子ディレクトリに設定されます。 このスコープ設定により、指定されたディレクトリとその子ディレクトリを除くファイル システムにアクセスできなくなります。 `PhysicalFileProvider` を作成して使用する最も一般的なシナリオは、[依存関係の挿入](xref:fundamentals/dependency-injection)を通してコンストラクターで `IFileProvider` を要求する場合です。

このプロバイダーを直接インスタンス化するときは、ディレクトリ パスが要求され、そのプロバイダーを使用して行われるすべての要求のベース パスとして機能します。

次のコードでは、`PhysicalFileProvider` の作成方法と、これを使ってディレクトリのコンテンツとファイル情報を取得する方法が示されます。

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

前の例の型は次のとおりです。

* `provider` は `IFileProvider` です。
* `contents` は `IDirectoryContents` です。
* `fileInfo` は `IFileInfo` です。

ファイル プロバイダーを使用して、`applicationRoot` で指定したディレクトリ全体を反復処理したり、`GetFileInfo` を呼び出してファイル情報を取得したりできます。 ファイル プロバイダーは、`applicationRoot` ディレクトリの外部にはアクセスできません。

サンプル アプリの `Startup.ConfigureServices` クラスでは、[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) を使用してプロバイダーを作成しています。

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> は、アセンブリに埋め込まれたファイルにアクセスするために使用されます。 `ManifestEmbeddedFileProvider` では、アセンブリにコンパイルされたマニフェストを使用して、埋め込まれたファイルの元のパスを再構築します。

埋め込みファイルのマニフェストを生成するには、`<GenerateEmbeddedFilesManifest>` プロパティを `true` に設定します。 [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) を使用して埋め込むファイルを指定します。

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

[glob パターン](#glob-patterns)を使用して、アセンブリに埋め込むファイルを 1 つまたは複数指定します。

サンプル アプリでは `ManifestEmbeddedFileProvider` を作成して、現在実行しているアセンブリをそのコンストラクターに渡します。

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

追加のオーバーロードを使用すると、次のことが可能になります。

* 相対ファイル パスを指定します。
* ファイルのスコープを最終変更日に設定します。
* 埋め込みファイルのマニフェストを含む埋め込みリソースに名前を付けます。

| オーバーロード | 説明 |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | 必要に応じて相対パスのパラメーター `root` を指定できます。 `root` を指定して、<xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> の呼び出しのスコープを指定したパス以下のリソースに設定します。 |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 必要に応じて、相対パス パラメーター `root` および日付パラメーター `lastModified` (<xref:System.DateTimeOffset>) を指定できます。 `lastModified` の日付では、<xref:Microsoft.Extensions.FileProviders.IFileProvider> によって返される <xref:Microsoft.Extensions.FileProviders.IFileInfo> インスタンスの最終更新日のスコープを設定します。 |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 必要に応じて、相対パス `root`、日付 `lastModified`、`manifestName` パラメーターを指定できます。 `manifestName` は、マニフェストを含む埋め込みリソースの名前を表します。 |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> は、`IFileProvider` インスタンスを結合し、複数のプロバイダーからのファイルを操作するための 1 つのインターフェイスを公開します。 `CompositeFileProvider` を作成する場合、1 つまたは複数の `IFileProvider` インスタンスをそのコンストラクターに渡します。

サンプル アプリでは、`PhysicalFileProvider` と `ManifestEmbeddedFileProvider` が、アプリのサービス コンテナーに登録されている `CompositeFileProvider` にファイルを提供します。

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>変更の監視

[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) メソッドによって、1 つまたは複数のファイルやディレクトリに変更がないかどうか監視するシナリオが提供されます。 `Watch` にはパス文字列を指定できます。ここでは、[glob パターン](#glob-patterns)を使用して複数のファイルを指定できます。 `Watch` では <xref:Microsoft.Extensions.Primitives.IChangeToken> が返されます。 変更トークンでは次のものが公開されます。

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; このプロパティを調べることで、変更があったかどうかを判断できます。
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 指定したパス文字列に対して変更が検出されたときに呼び出されます。 各変更トークンは、1 つの変更への応答として、関連付けられたコールバックを呼び出すのみです。 定数の監視を有効にするには、<xref:System.Threading.Tasks.TaskCompletionSource`1> を使用するか (以下を参照)、変更への応答として `IChangeToken` インスタンスを再作成します。

サンプル アプリでは、*WatchConsole* コンソール アプリは、テキスト ファイルが変更されるたびにメッセージを表示するように構成されています。

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Docker コンテナーやネットワーク共有など、一部のファイル システムは、変更通知を確実に送信しない可能性があります。 `DOTNET_USE_POLLING_FILE_WATCHER` 環境変数を `1` または `true` に設定して、変更がないかどうか、4 秒ごとにファイル システムをポーリングして確認します (構成不可)。

## <a name="glob-patterns"></a>glob パターン

ファイル システム パスは、"*glob (または globbing) パターン*" と呼ばれるワイルドカード パターンを使用します。 これらのパターンを使用して、ファイルのグループを指定します。 2 つのワイルドカード文字は、`*` と `**` です。

**`*`**  
現在のフォルダー レベルにある任意の要素、任意のファイル名、または任意のファイル拡張子を照合します。 照合はファイル パス内の `/` 文字および `.` 文字によって終了します。

**`**`**  
複数のディレクトリ レベルにわたって任意の要素を照合します。 ディレクトリ階層内の多数のファイルを再帰的に照合する場合に使用できます。

**glob パターンの例**

**`directory/file.txt`**  
特定のディレクトリ内の特定のファイルを照合します。

**`directory/*.txt`**  
特定のディレクトリ内の *.txt* 拡張子を持つすべてのファイルを照合します。

**`directory/*/appsettings.json`**  
*directory* フォルダーのちょうど 1 つ下のレベルにあるディレクトリ内のすべての `appsettings.json` ファイルを照合します。

**`directory/**/*.txt`**  
*directory* フォルダーの下の任意の場所にある、 *.txt* 拡張子を持つすべてのファイルを照合します。

::: moniker-end
