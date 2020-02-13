---
title: ASP.NET から ASP.NET Core 2.0 への移行
author: isaac2004
description: 既存の ASP.NET MVC または Web API アプリケーションを ASP.NET Core 2.0 に移行するためのガイダンスを受信します。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
uid: migration/mvc2
ms.openlocfilehash: 11bd3b948afaedc675ac4249099969382683f653
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172440"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a>ASP.NET から ASP.NET Core 2.0 への移行

著者: [Isaac Levin](https://isaaclevin.com)

この記事は、ASP.NET アプリケーションを ASP.NET Core 2.0 に移行するための参考ガイドです。

## <a name="prerequisites"></a>前提条件

.Net のダウンロードから、次の**いずれか**をインストールし[ます。 Windows](https://www.microsoft.com/net/download/windows):

* .NET Core SDK
* Windows 用 Visual Studio
  * **ASP.NET および Web の開発**ワークロード
  * **.NET Core クロスプラットフォームの開発**ワークロード

## <a name="target-frameworks"></a>ターゲット フレームワーク

ASP.NET Core 2.0 プロジェクトを使うと、開発者は、.NET Core と .NET Framework のどちらか一方または両方を対象にして柔軟に開発できます。 最も適切なターゲット フレームワークの決定については、「[サーバー アプリ用 .NET Core と .NET Framework の選択](/dotnet/standard/choosing-core-framework-server)」をご覧ください。

.NET Framework を対象にする場合は、プロジェクトで個々の NuGet パッケージを参照する必要があります。

.NET Core を対象にすると、ASP.NET Core 2.0 [メタパッケージ](xref:fundamentals/metapackage)のおかげで、さまざまな明示的パッケージ参照をしなくて済みます。 `Microsoft.AspNetCore.All` メタパッケージをプロジェクトにインストールします。

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

メタパッケージを使うと、メタパッケージ内で参照されているパッケージはアプリでは展開されません。 .NET Core ランタイム ストアにはこれらのアセットが含まれており、パフォーマンス向上のためにプリコンパイルされています。 詳細については、「<xref:fundamentals/metapackage>」を参照してください。

## <a name="project-structure-differences"></a>プロジェクトの構造の違い

*.csproj* ファイルの形式は、ASP.NET Core では簡素化されています。 いくつかの重要な変更は次のとおりです。

* ファイルがプロジェクトの一部と見なされるためにファイルを明示的に含める必要はありません。 これにより、大規模なチームで作業する場合に XML のマージが競合するリスクが軽減されます。
* 他のプロジェクトを GUID で参照することはなくなり、ファイルの読みやすさが向上します。
* Visual Studio でアンロードせずにファイルを編集することができます。

  ![Visual Studio 2017 の CSPROJ の編集コンテキスト メニュー オプション](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Global.asax ファイルの置換

ASP.NET Core では、アプリをブートストラップする新しいメカニズムが導入されました。 ASP.NET アプリケーションのエントリ ポイントは、*Global.asax* ファイルです。 ルート構成、フィルター、領域の登録などのタスクは、*Global.asax* ファイルで処理されます。

[!code-csharp[](samples/globalasax-sample.cs)]

このアプローチでは、アプリケーションとその展開先のサーバーが、実装を妨げるような方法で結合されます。 結合を切り離すため、複数のフレームワークを一緒に使うさらにクリーンな方法を提供する [OWIN](https://owin.org/) が導入されました。 OWIN は、必要なモジュールのみを追加するためのパイプラインを提供します。 ホスティング環境は、[Startup](xref:fundamentals/startup) 関数を取得して、サービスとアプリの要求パイプラインを構成します。 `Startup` は、ミドルウェアのセットをアプリケーションに登録します。 アプリケーションは、要求ごとに、既存のハンドラーのセットに対するリンク リストのヘッド ポインターを指定して、各ミドルウェア コンポーネントを呼び出します。 各ミドルウェア コンポーネントは、要求処理パイプラインに 1 つ以上のハンドラーを追加できます。 これは、新しいリストのヘッドであるハンドラーへの参照を返すことによって行われます。 各ハンドラーは、リスト内の次のハンドラーを記憶して呼び出します。 ASP.NET Core では、アプリケーションへのエントリ ポイントは `Startup` であり、*Global.asax* に依存する必要はなくなりました。 .NET Framework で OWIN を使うときは、パイプラインとして次のようなものを使います。

[!code-csharp[](samples/webapi-owin.cs)]

これにより既定のルートが構成され、既定では Json 経由の XmlSerialization です。 必要に応じて、このパイプラインに他のミドルウェアを追加します (サービスの読み込み、構成設定、静的ファイルなど)。

ASP.NET Core は同様のアプローチを使いますが、エントリを処理するために OWIN には依存しません。 代わりに、(コンソール アプリケーションと同じように) *Program.cs* の `Main` メソッドを通して行われ、そこから `Startup` が読み込まれます。

[!code-csharp[](samples/program.cs)]

`Startup` は、`Configure` メソッドを含む必要があります。 `Configure` では、必要なミドルウェアをパイプラインに追加します。 (既定の Web サイト テンプレートからの) 次の例では、複数の拡張メソッドを使って、以下をサポートするパイプラインが構成されています。

* [BrowserLink](https://vswebessentials.com/features/browserlink)
* エラー ページ
* 静的ファイル
* ASP.NET Core MVC
* ID

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

ホストとアプリケーションは切り離されており、将来別のプラットフォームに柔軟に移動できます。

ASP.NET Core のスタートアップとミドルウェアの詳細については、「<xref:fundamentals/startup>」を参照してください。

## <a name="storing-configurations"></a>保存の構成

ASP.NET では保存の設定がサポートされています。 これらの設定は、たとえば、アプリケーションが展開された環境のサポートに使われます。 一般的な方法は、すべてのカスタム キー/値ペアを、`<appSettings>`Web.config*ファイルの* セクションに保存するというものでした。

[!code-xml[](samples/webconfig-sample.xml)]

アプリケーションでは、`ConfigurationManager.AppSettings` 名前空間内の `System.Configuration` コレクションを使ってこれらの設定を読み取ります。

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core では、アプリケーションの構成データを任意のファイルに保存し、ミドルウェアのブートストラップの一部として読み込むことができます。 プロジェクト テンプレートで使われる既定のファイルは、*appsettings.json* です。

[!code-json[](samples/appsettings-sample.json)]

アプリケーション内部の `IConfiguration` のインスタンスにこのファイルを読み込むには、*Startup.cs* が使われます。

[!code-csharp[](samples/startup-builder.cs)]

アプリでは、`Configuration` から読み取って設定を取得します。

[!code-csharp[](samples/read-appsettings.cs)]

このアプローチにはプロセスをより堅牢にする拡張機能があります。たとえば、[依存性の注入](xref:fundamentals/dependency-injection) (DI) を使ってサービスとこれらの値を読み込むことができます。 DI アプローチは、厳密に型指定された構成オブジェクトのセットを提供します。

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

**注:** ASP.NET Core 構成の詳細なリファレンスについては、「<xref:fundamentals/configuration/index>」を参照してください。

## <a name="native-dependency-injection"></a>ネイティブな依存性の注入

大規模で拡張性の高いアプリケーションを構築するときの重要な目標は、コンポーネントとサービスの疎な結合です。 [依存関係の挿入](xref:fundamentals/dependency-injection)は、これを実現するための一般的な手法であり、ASP.NET Core のネイティブコンポーネントです。

ASP.NET アプリケーションでは、開発者はサードパーティのライブラリを使用して依存関係の挿入を実装します。 [Unity](https://github.com/unitycontainer/unity) はそのようなライブラリの 1 つであり、Microsoft Patterns & Practices によって提供されます。

Unity で依存関係の挿入を設定する例として、`UnityContainer`をラップする `IDependencyResolver` が実装されています。

[!code-csharp[](samples/sample8.cs)]

`UnityContainer` のインスタンスを作成し、サービスを登録して、`HttpConfiguration` の依存関係リゾルバーをコンテナー用の `UnityResolver` の新しいインスタンスに設定します。

[!code-csharp[](samples/sample9.cs)]

必要な場所に `IProductRepository` を挿入します。

[!code-csharp[](samples/sample5.cs)]

依存関係の挿入は ASP.NET Core の一部であるため、`Startup.ConfigureServices`にサービスを追加できます。

[!code-csharp[](samples/configure-services.cs)]

Unity でそうであったように、リポジトリは任意の場所に挿入できます。

ASP.NET Core での依存関係の挿入の詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。

## <a name="serving-static-files"></a>静的ファイルの提供

Web 開発の重要な部分は、静的なクライアント側アセットを提供する機能です。 静的なファイルの最も一般的な例は、HTML、CSS、Javascript、およびイメージです。 これらのファイルは、アプリ (または CDN) の公開された場所に保存され、要求によって読み込めるように参照される必要があります。 このプロセスは、ASP.NET Core で変更されました。

ASP.NET では、静的ファイルはさまざまなディレクトリに保存され、ビューで参照されます。

ASP.NET Core では、構成が変更されていない限り、静的ファイルは "Web ルート" ( *&lt;コンテンツ ルート&gt;/wwwroot*) に保存されます。 ファイルは、`UseStaticFiles` から `Startup.Configure` 拡張メソッドを呼び出すことによって、要求パイプラインに読み込まれます。

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

**注:** .NET Framework を対象にする場合は、NuGet パッケージ `Microsoft.AspNetCore.StaticFiles` をインストールします。

たとえば、*wwwroot/images* フォルダー内のイメージ アセットには、ブラウザーから `http://<app>/images/<imageFileName>` などの場所でアクセスできます。

**注:** ASP.NET Core での静的ファイルの提供に関する詳細なリファレンスについては、「<xref:fundamentals/static-files>」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [.NET Core にライブラリを移植する](/dotnet/core/porting/libraries)
