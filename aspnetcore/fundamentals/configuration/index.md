---
title: ASP.NET Core の構成
author: guardrex
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 5723295c70f8d893f758ca5dc87180c6b707f493
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994157"
---
# <a name="configuration-in-aspnet-core"></a>ASP.NET Core の構成

作成者: [Luke Latham](https://github.com/guardrex)

ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。 構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。

* Azure Key Vault
* Azure App Configuration
* コマンド ライン引数
* カスタム プロバイダー (インストール済みまたは作成済み)
* ディレクトリ ファイル
* 環境変数
* メモリ内 .NET オブジェクト
* 設定ファイル

::: moniker range=">= aspnetcore-3.0"

一般的な構成プロバイダーのシナリオの構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、フレームワークによって暗黙的に含まれます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

一般的な構成プロバイダーのシナリオに向けた構成パッケージ ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) は、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。

::: moniker-end

以下とサンプル アプリのコード例では、<xref:Microsoft.Extensions.Configuration> 名前空間を使います。

```csharp
using Microsoft.Extensions.Configuration;
```

"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。 オプションでは、クラスを使用して関連する設定のグループを表します。 詳細については、<xref:fundamentals/configuration/options> を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="host-versus-app-configuration"></a>ホストとアプリの構成

アプリを構成して起動する前に、"*ホスト*" を構成して起動します。 ホストはアプリの起動と有効期間の管理を担当します。 アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。 ホスト構成のキーと値のペアも、アプリの構成に含まれます。 ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、「<xref:fundamentals/index#host>」をご覧ください。

## <a name="default-configuration"></a>既定の構成

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出します。 `CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。

[汎用ホスト](xref:fundamentals/host/generic-host)を使用するアプリに以下が適用されます。 [Web ホスト](xref:fundamentals/host/web-host)を使用する場合の既定の構成の詳細については、[このトピックの ASP.NET Core 2.2 バージョン](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)を参照してください。

* ホストの構成は、次から提供されます。
  * [環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `DOTNET_` (`DOTNET_ENVIRONMENT` など) が付いた環境変数。 構成のキーと値のペアが読み込まれるときに、プレフィックス (`DOTNET_`) は削除されます。
  * [コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。
* Web ホストの既定の構成が確立されます (`ConfigureWebHostDefaults`)。
  * Kestrel は Web サーバーとして使用され、アプリの構成プロバイダーを使用して構成されます。
  * Host Filtering Middleware を追加します。
  * `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 環境変数が `true` に設定されている場合は、Forwarded Headers Middleware を追加します。
  * IIS 統合を有効にします。
* アプリの構成は、次から提供されます。
  * [ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。
  * [ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。
  * エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。
  * [環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。
  * [コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。 `CreateDefaultBuilder` により、次の順序でアプリの既定の構成が提供されます。

[Web ホスト](xref:fundamentals/host/web-host)を使用するアプリには、以下が適用されます。 [汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合の既定の構成の詳細については、[このトピックの最新バージョン](xref:fundamentals/configuration/index)を参照してください。

* ホストの構成は、次から提供されます。
  * [環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。 構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。
  * [コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。
* アプリの構成は、次から提供されます。
  * [ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。
  * [ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。
  * エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。
  * [環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。
  * [コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。

::: moniker-end

## <a name="security"></a>セキュリティ

機密性の高い構成データをセキュリティで保護するには、次の方法を採用します。

* 構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。
* 開発環境やテスト環境では運用シークレットを使用しないでください。
* プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。

詳細については、次のトピックを参照してください。

* <xref:fundamentals/environments>
* <xref:security/app-secrets> &ndash; には、環境変数を使用して機密データを格納する場合に関するアドバイスが記載されています。 シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。 ファイル構成プロバイダーについては、このトピックの後半で説明します。

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) では、ASP.NET Core アプリのアプリのシークレットが安全に保存されます。 詳細については、<xref:security/key-vault-configuration> を参照してください。

## <a name="hierarchical-configuration-data"></a>階層的な構成データ

構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。

次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。

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

ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。 セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。

* section0:key0
* section0:key1
* section1:key0
* section1:key1

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。 これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。

## <a name="conventions"></a>規約

### <a name="sources-and-providers"></a>ソースとプロバイダー

アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。

変更の検出を実装する構成プロバイダーは、基になる設定が変更された場合に構成を再読み込みする機能を備えています。 たとえば、ファイル構成プロバイダー (このトピックで後から説明します) と[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)では、変更の検出を実装します。

<xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。 <xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> を挿入して、クラスの構成を取得することができます。

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

構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。

### <a name="keys"></a>キー

構成キーでは、次の規則が採用されます。

* キーでは、大文字と小文字は区別されません。 たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。
* 同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。
* 階層キー
  * 構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。
  * 環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。 二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに自動的に変換されます。
  * Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。 コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換える必要があります。
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。 配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。

### <a name="values"></a>値

構成値では、次の規則が採用されます。

* 値は文字列です。
* Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。

## <a name="providers"></a>プロバイダー

ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。

| プロバイダー | &hellip; から構成を提供します。 |
| -------- | ----------------------------------- |
| [Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック) | Azure Key Vault |
| [Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure のドキュメント) | Azure App Configuration |
| [コマンド ライン構成プロバイダー](#command-line-configuration-provider) | コマンド ライン パラメーター |
| [カスタム構成プロバイダー](#custom-configuration-provider) | カスタム ソース |
| [環境変数構成プロバイダー](#environment-variables-configuration-provider) | 環境変数 |
| [ファイル構成プロバイダー](#file-configuration-provider) | ファイル (INI、JSON、XML) |
| [ファイルごとのキーの構成プロバイダー](#key-per-file-configuration-provider) | ディレクトリ ファイル |
| [メモリ構成プロバイダー](#memory-configuration-provider) | メモリ内コレクション |
| [ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック) | ユーザー プロファイル ディレクトリ内のファイル |

アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。 このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。 基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。

一般的な一連の構成プロバイダーは次のとおりです。

1. ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境のみ)
1. 環境変数
1. コマンド ライン引数

コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。

この一連のプロバイダーは、`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化するときに使用されます。 詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a>ConfigureHostConfiguration を使用してホスト ビルダーを構成する

ホスト ビルダーを構成するには、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出し、構成を指定します。 `ConfigureHostConfiguration` は、後でビルド プロセスに使用する <xref:Microsoft.Extensions.Hosting.IHostEnvironment> を初期化するために使用されます。 `ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration((hostingContext, config) =>
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

## <a name="configure-the-host-builder-with-useconfiguration"></a>UseConfiguration を使用してホスト ビルダーを構成する

ホスト ビルダーを構成するには、構成を使用するホスト ビルダー上で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。

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

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

ホストをビルドするときに `ConfigureAppConfiguration` を呼び出し、`CreateDefaultBuilder` によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a>前の構成をコマンドライン引数でオーバーライドする

コマンドライン引数でオーバーライドできるアプリ構成を指定するには、最後に `AddCommandLine` を呼び出します。

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="consume-configuration-during-app-startup"></a>アプリの起動時に構成を使用する

`ConfigureAppConfiguration` のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。 詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。

## <a name="command-line-configuration-provider"></a>コマンド ライン構成プロバイダー

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。

コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。

`CreateDefaultBuilder(string [])` が呼び出されると、`AddCommandLine` が自動的に呼び出されます。 詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。

`CreateDefaultBuilder` では次のものも読み込まれます。

* *appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。
* 開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。
* 環境変数。

`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。 実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。

`CreateDefaultBuilder` は、ホストが作成されるときに機能します。 そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。

ASP.NET Core テンプレートに基づくアプリの場合、`AddCommandLine` は `CreateDefaultBuilder` によって既に呼び出されています。 さらに構成プロバイダーを追加し、コマンドライン引数を使用してそれらのプロバイダーの構成をオーバーライドする機能を維持するには、アプリの追加プロバイダーを `ConfigureAppConfiguration` で呼び出し、最後に `AddCommandLine` を呼び出します。

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**例**

サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。

1. プロジェクトのディレクトリでコマンド プロンプトを開きます。
1. `dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。
1. アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。
1. 出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。

### <a name="arguments"></a>引数

値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。 等号 (`CommandLineKey=` など) が使用されている場合、値は必要ありません。

| キーのプレフィックス               | 例                                                |
| ------------------------ | ------------------------------------------------------ |
| プレフィックスなし                | `CommandLineKey1=value1`                               |
| 2 つのダッシュ (`--`)        | `--CommandLineKey2=value2`、 `--CommandLineKey2 value2` |
| スラッシュ (`/`)      | `/CommandLineKey3=value3`、 `/CommandLineKey3 value3`   |

同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。

コマンドの例:

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>スイッチ マッピング

スイッチ マッピングでは、キー名の交換ロジックが許可されます。 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定することができます。

スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。 コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。 スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。

スイッチ マッピング ディクショナリ キーの規則:

* スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。
* スイッチ マッピング ディクショナリに重複キーを含めることはできません。

スイッチ マッピング ディクショナリを作成します。 次の例では、2 つのスイッチ マッピングが作成されます。

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

ホストが構築されたら、スイッチ マッピング ディクショナリを使用して `AddCommandLine` を呼び出します。

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

スイッチ マッピングを使用するアプリでは、`CreateDefaultBuilder` への呼び出しで引数を渡すことはできません。 `CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。 ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。

スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。

| キー       | [値]             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

上記のコマンドを実行すると、次の表に示す値が構成に含まれます。

| キー               | [値]    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>環境変数構成プロバイダー

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。

環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure Portal で設定できます。 詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。

::: moniker range=">= aspnetcore-3.0"

新しいホスト ビルダーが[汎用ホスト](xref:fundamentals/host/generic-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `DOTNET_` で始まる環境変数が読み込まれます。 詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

新しいホスト ビルダーが [Web ホスト](xref:fundamentals/host/web-host)で初期化され、`CreateDefaultBuilder` が呼び出されると、`AddEnvironmentVariables` が使用され、[ホスト構成](#host-versus-app-configuration)の `ASPNETCORE_` で始まる環境変数が読み込まれます。 詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。

::: moniker-end

`CreateDefaultBuilder` では次のものも読み込まれます。

* プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。
* *appsettings.json* および *appsettings.{Environment}.json* ファイルの省略可能な構成。
* 開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。
* コマンド ライン引数。

ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。 この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。

追加の環境変数からアプリの構成を指定する必要がある場合は、`ConfigureAppConfiguration` のアプリの追加プロバイダーを呼び出し、そのプレフィックスを含む `AddEnvironmentVariables` を呼び出します。

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

**例**

サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。

1. サンプル アプリを実行します。 アプリに対して `http://localhost:5000` でブラウザーを開きます。
1. 出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。 値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。

アプリによってレンダリングされる環境変数の一覧を短く保つために、アプリでは環境変数がフィルター処理されます。 サンプル アプリの *Pages/Index.cshtml.cs* ファイルを参照してください。

アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>プレフィックス

アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。 たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。

ホストビルダーが作成されると、環境変数によってホスト構成が指定されます。 これらの環境変数に使用されるプレフィックスの詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。

**接続文字列のプレフィックス**

構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。 表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。

| 接続文字列のプレフィックス | プロバイダー |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | カスタム プロバイダー |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:

* 環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。
* データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。

| 環境変数キー | 変換された構成キー | プロバイダーの構成エントリ                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | 構成エントリは作成されません。                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | キー: `ConnectionStrings:<KEY>_ProviderName`:<br>値: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | キー: `ConnectionStrings:<KEY>_ProviderName`:<br>値: `System.Data.SqlClient`  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | キー: `ConnectionStrings:<KEY>_ProviderName`:<br>値: `System.Data.SqlClient`  |

## <a name="file-configuration-provider"></a>ファイル構成プロバイダー

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。 次の構成プロバイダーは、特定のファイルの種類専用です。

* [INI 構成プロバイダー](#ini-configuration-provider)
* [JSON 構成プロバイダー](#json-configuration-provider)
* [XML 構成プロバイダー](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI 構成プロバイダー

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。

INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。

INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。

オーバーロードによって次のものを指定できます。

* ファイルを省略可能かどうか。
* ファイルが変更された場合に構成を再度読み込むかどうか。
* ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。

ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。

INI 構成ファイルの汎用的な例:

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

前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>JSON 構成プロバイダー

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。

JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。

オーバーロードによって次のものを指定できます。

* ファイルを省略可能かどうか。
* ファイルが変更された場合に構成を再度読み込むかどうか。
* ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。

`CreateDefaultBuilder` を使用して新しいホスト ビルダーを初期化すると、`AddJsonFile` が自動的に 2 回呼び出されます。 このメソッドは、次から構成を読み込むために呼び出されます。

* *appsettings.json* &ndash; このファイルが最初に読み取られます。 ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。
* *appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。

詳細については、「[既定の構成](#default-configuration)」セクションを参照してください。

`CreateDefaultBuilder` では次のものも読み込まれます。

* 環境変数。
* 開発環境の[ユーザー シークレット (Secret Manager)](xref:security/app-secrets)。
* コマンド ライン引数。

JSON 構成プロバイダーが最初に確立されます。 このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。

ホストのビルド時に `ConfigureAppConfiguration` を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。

**例**

サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。 構成は、*appsettings.json* および *appsettings.{Environment}.json* から読み込まれます。

1. サンプル アプリを実行します。 アプリに対して `http://localhost:5000` でブラウザーを開きます。
1. 出力に、環境に応じて、表に示す構成のキーと値のペアが含まれていることを観察します。 ログの構成キーでは、階層の区切り文字としてコロン (`:`) が使用されます。

| キー                        | 開発時の値 | 運用時の値 |
| -------------------------- | :---------------: | :--------------: |
| Logging:LogLevel:System    | 情報       | 情報      |
| Logging:LogLevel:Microsoft | 情報       | 情報      |
| Logging:LogLevel:Default   | デバッグ             | Error            |
| AllowedHosts               | *                 | *                |

### <a name="xml-configuration-provider"></a>XML 構成プロバイダー

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。

XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。

オーバーロードによって次のものを指定できます。

* ファイルを省略可能かどうか。
* ファイルが変更された場合に構成を再度読み込むかどうか。
* ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。

構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。 ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。

ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。

XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。

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

前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。

* section0:key0
* section0:key1
* section1:key0
* section1:key1

同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。

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

前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

値を指定するために属性を使用できます。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>ファイルごとのキーの構成プロバイダー

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。 キーはファイル名です。 値にはファイルのコンテンツが含まれます。 ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。

ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。 ファイルに対する `directoryPath` は、絶対パスである必要があります。

オーバーロードによって次のものを指定できます。

* ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。
* ディレクトリを省略可能かどうか、またディレクトリへのパス。

アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。 たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。

ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。

## <a name="memory-configuration-provider"></a>メモリ構成プロバイダー

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。

メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。

構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。

ホストをビルドするときに `ConfigureAppConfiguration` を呼び出して、アプリの構成を指定します。

次の例では、構成ディクショナリが作成されます。

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

ディクショナリは、構成を指定するために `AddInMemoryCollection` の呼び出しと共に使用されます。

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) によって、指定したキーで構成から値が抽出され、それが指定した型に変換されます。 オーバーロードを使用すると、キーが見つからない場合に既定値を指定することができます。

次のような例です。

* キー `NumberKey` の文字列値を構成から抽出します。 `NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。
* 値の型は `int` です。
* `NumberConfig` プロパティの値をページで使用するために格納します。

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

## <a name="getsection-getchildren-and-exists"></a>GetSection、GetChildren、Exists

以下の例では、次の JSON ファイルについて考えます。 4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。

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

ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。

`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。

```csharp
var configSection = _config.GetSection("section1");
```

`configSection` には値はなく、キーとパスのみが含まれます。

同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` が `null` を返すことはありません。 一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。

`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。

### <a name="getchildren"></a>GetChildren

`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>既存

[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。

## <a name="bind-to-a-class"></a>クラスにバインドする

"*オプション パターン*" を使用して、関連する設定のグループを表すクラスに構成をバインドすることができます。 詳細については、<xref:fundamentals/configuration/options> を参照してください。

構成値は文字列として返されますが、<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> を呼び出すことで [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) オブジェクトを構築できます。

サンプル アプリには `Starship` モデル (*Models/Starship.cs*) が含まれます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

サンプル アプリが JSON 構成プロバイダーを使用して構成を読み込むときに、*starship.json* ファイルの `starship` セクションで構成が作成されます。

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

次の構成のキーと値のペアが作成されます。

| キー                   | [値]                                             |
| --------------------- | ------------------------------------------------- |
| starship:name         | USS Enterprise                                    |
| starship:registry     | NCC-1701                                          |
| starship:class        | Constitution                                      |
| starship:length       | 304.8                                             |
| starship:commissioned | False                                             |
| trademark             | Paramount Pictures Corp. https://www.paramount.com |

サンプル アプリは `starship` キーを使用して `GetSection` を呼び出します。 `starship` のキーと値のペアは分離されます。 `Starship` クラスのインスタンスを渡すサブセクションで、`Bind` メソッドが呼び出されます。 インスタンスの値をバインドした後、インスタンスがレンダリング用のプロパティに割り当てられます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a>オブジェクト グラフにバインドする

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。

サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。 バインドされたインスタンスは、表示のためにプロパティに割り当てられます。

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) によってバインドされ、指定した型が返されます。 `Get<T>` は `Bind` を使用するよりも便利です。 次のコードは、前の例と共に `Get<T>` を使用する方法を示しています。これにより、バインドされたインスタンスを、表示用に使用するプロパティに直接割り当てることができます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a>配列をクラスにバインドする

*サンプル アプリは、このセクションで説明する概念を示しています。*

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。 数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。

> [!NOTE]
> バインドは慣例に従って指定されます。 カスタム構成プロバイダーが配列のバインドを実装する必要はありません。

**メモリ内配列の処理**

次の表に示す構成のキーと値について考えます。

| キー             | [値]  |
| :-------------: | :----: |
| 配列:エントリ:0 | value0 |
| 配列:エントリ:1 | value1 |
| 配列:エントリ:2 | value2 |
| 配列:エントリ:4 | value4 |
| 配列:エントリ:5 | value5 |

これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

配列は、インデックス &num;3 の値をスキップします。 構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。

サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

構成データはオブジェクトにバインドされます。

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

また、[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用して、コードをよりコンパクトにすることもできます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。

| `ArrayExample.Entries` インデックス | `ArrayExample.Entries` 値 |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value4                       |
| 4                            | value5                       |

バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。 配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。 構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。

インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。 不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

`ConfigureAppConfiguration`の場合:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

表に示すキーと値のペアが構成に読み込まれます。

| キー             | [値]  |
| :-------------: | :----: |
| array:entries:3 | value3 |

JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。

| `ArrayExample.Entries` インデックス | `ArrayExample.Entries` 値 |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**JSON 配列の処理**

JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。 次の構成ファイルにおいて、`subsection` は配列です。

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。

| キー                     | [値]  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。 サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。

| `JsonArrayExample.Subsection` インデックス | `JsonArrayExample.Subsection` 値 |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>カスタム構成プロバイダー

サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。

プロバイダーの特徴は次のとおりです。

* EF のメモリ内データベースは、デモンストレーションのために使用されます。 接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。
* プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。 プロバイダーは、キー単位でデータベースを照会しません。
* 変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。

データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。

::: moniker range=">= aspnetcore-3.0"

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。 データベースが空だった場合、構成プロバイダーはこれを初期化します。

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。 データベースが空だった場合、構成プロバイダーはこれを初期化します。

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

## <a name="access-configuration-during-startup"></a>起動中に構成にアクセスする

`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。 `Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。

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

起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Razor Pages ページまたは MVC ビューで構成にアクセスする

Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。

Razor ページ:

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

MVC ビュー:

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

## <a name="add-configuration-from-an-external-assembly"></a>外部アセンブリから構成を追加する

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。 詳細については、<xref:fundamentals/configuration/platform-specific-configuration> を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/configuration/options>
