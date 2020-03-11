---
title: ASP.NET Core での開発におけるアプリシークレットの安全な保存
author: rick-anderson
description: ASP.NET Core アプリの開発中に、機密情報をアプリシークレットとして格納および取得する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: security/app-secrets
ms.openlocfilehash: c3f165164f3c95e8c0aab773f3731429ae224bd9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654692"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET Core での開発におけるアプリシークレットの安全な保存

[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、 [Scott addie](https://github.com/scottaddie)

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

このドキュメントでは、開発用コンピューターでの ASP.NET Core アプリの開発時に機密データを格納および取得する方法について説明します。 パスワードやその他の機密データをソースコードに格納しないでください。 運用環境のシークレットは、開発またはテストには使用しないでください。 シークレットはアプリと一緒にデプロイしないでください。 代わりに、環境変数、Azure Key Vault などの制御された方法を使用して、運用環境でシークレットを使用できるようにする必要があります。[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)を使用して、Azure テストおよび運用シークレットを格納し、保護することができます。

## <a name="environment-variables"></a>環境変数

環境変数は、コードまたはローカル構成ファイルにアプリシークレットを格納しないようにするために使用されます。 環境変数は、以前に指定したすべての構成ソースの構成値を上書きします。

::: moniker range="<= aspnetcore-1.1"

`Startup` コンストラクターで <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> を呼び出して、環境変数の値の読み取りを構成します。

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

**個々のユーザーアカウント**のセキュリティが有効になっている ASP.NET Core web アプリを考えてみましょう。 既定のデータベース接続文字列は、`DefaultConnection`キーを持つプロジェクトの*appsettings*ファイルに含まれています。 既定の接続文字列は LocalDB 用であり、ユーザーモードで実行され、パスワードは必要ありません。 アプリの展開中に、`DefaultConnection` キー値を環境変数の値でオーバーライドできます。 環境変数には、機密性の高い資格情報を持つ完全な接続文字列が格納される場合があります。

> [!WARNING]
> 環境変数は通常、暗号化されていないプレーンテキストで格納されます。 コンピューターまたはプロセスが侵害された場合、信頼されていない相手から環境変数にアクセスできます。 ユーザーシークレットが漏えいしないようにするための追加の手段が必要な場合があります。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>シークレットマネージャー

Secret Manager ツールは、ASP.NET Core プロジェクトの開発中に機微なデータを保存します。 このコンテキストでは、機密データの一部がアプリシークレットになります。 アプリシークレットは、プロジェクトツリーとは別の場所に格納されます。 アプリシークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクト間で共有されています。 アプリシークレットがソース管理にチェックインされていません。

> [!WARNING]
> シークレットマネージャーツールは、保存されているシークレットを暗号化せず、信頼されたストアとして扱わないでください。 開発目的でのみ使用されます。 キーと値は、ユーザープロファイルディレクトリの JSON 構成ファイルに格納されます。

## <a name="how-the-secret-manager-tool-works"></a>Secret Manager ツールの動作

Secret Manager ツールは、値の格納場所や方法などの実装の詳細を抽象化します。 このツールは、実装の詳細を把握していなくても使用できます。 値は、ローカルコンピューター上のシステムで保護されたユーザープロファイルフォルダー内の JSON 構成ファイルに格納されます。

# <a name="windows"></a>[Windows](#tab/windows)

ファイルシステムのパス:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

ファイルシステムのパス:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

上記のファイルパスで、`<user_secrets_id>` を *.csproj*ファイルで指定された `UserSecretsId` の値に置き換えます。

シークレットマネージャーツールで保存したデータの場所または形式に依存するコードは記述しないでください。 これらの実装の詳細は変更される可能性があります。 たとえば、シークレット値は暗号化されませんが、将来の可能性があります。

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a>シークレットマネージャーツールをインストールする

Secret Manager ツールは、.NET Core SDK 2.1.300 以降の .NET Core CLI にバンドルされています。 2\.1.300 の前にバージョンを .NET Core SDK には、ツールのインストールが必要です。

> [!TIP]
> コマンドシェルから `dotnet --version` を実行して、インストールされている .NET Core SDK バージョン番号を確認します。

使用されている .NET Core SDK にツールが含まれている場合は、次のような警告が表示されます。

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

ASP.NET Core プロジェクトに[SecretManager](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet パッケージをインストールします。 例 :

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

コマンドシェルで次のコマンドを実行して、ツールのインストールを検証します。

```dotnetcli
dotnet user-secrets -h
```

Secret Manager ツールには、使用法、オプション、およびコマンドヘルプのサンプルが表示されます。

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> *.Csproj*ファイルの `DotNetCliToolReference` 要素で定義されているツールを実行するには、 *.csproj*ファイルと同じディレクトリに存在する必要があります。

::: moniker-end

## <a name="enable-secret-storage"></a>シークレットストレージを有効にする

Secret Manager ツールは、ユーザープロファイルに格納されているプロジェクト固有の構成設定を操作します。

::: moniker range=">= aspnetcore-3.0"

Secret Manager ツールには、.NET Core SDK 3.0.100 以降の `init` コマンドが含まれています。 ユーザーシークレットを使用するには、プロジェクトディレクトリで次のコマンドを実行します。

```dotnetcli
dotnet user-secrets init
```

上記のコマンドは、 *.csproj*ファイルの `PropertyGroup` 内に `UserSecretsId` 要素を追加します。 既定では `UserSecretsId` の内部テキストは GUID です。 内部テキストは任意ですが、プロジェクトに固有です。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ユーザーシークレットを使用するには、 *.csproj*ファイルの `PropertyGroup` 内に `UserSecretsId` 要素を定義します。 `UserSecretsId` の内部テキストは任意ですが、プロジェクトに固有です。 開発者は通常、`UserSecretsId`の GUID を生成します。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> Visual Studio で、ソリューションエクスプローラーでプロジェクトを右クリックし、コンテキストメニューから **[ユーザーシークレットの管理]** を選択します。 このジェスチャは、GUID で設定された `UserSecretsId` 要素を .csproj ファイルに追加し*ます*。

## <a name="set-a-secret"></a>シークレットを設定する

キーとその値で構成されるアプリシークレットを定義します。 シークレットは、プロジェクトの `UserSecretsId` 値に関連付けられます。 たとえば、 *.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

前の例では、コロンは、`Movies` が `ServiceApiKey` プロパティを持つオブジェクトリテラルであることを示しています。

シークレットマネージャーツールは、他のディレクトリからも使用できます。 `--project` オプションを使用して、 *.csproj*ファイルが存在するファイルシステムパスを指定します。 例 :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio での JSON 構造のフラット化

Visual Studio の **[ユーザーシークレットの管理]** ジェスチャは、テキストエディターで*シークレットの json*ファイルを開きます。 *シークレット*の内容を、格納されるキーと値のペアで置き換えます。 例 :

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 構造体は、`dotnet user-secrets remove` または `dotnet user-secrets set`を使用した変更後にフラット化されます。 たとえば、`dotnet user-secrets remove "Movies:ConnectionString"` を実行すると、`Movies` オブジェクトリテラルが折りたたまれます。 変更後のファイルは次のようになります。

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>複数のシークレットを設定する

シークレットのバッチは、JSON を `set` コマンドにパイプすることによって設定できます。 次の例では、*入力の json*ファイルの内容をパイプ処理して `set` コマンドを実行します。

# <a name="windows"></a>[Windows](#tab/windows)

コマンドシェルを開き、次のコマンドを実行します。

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

コマンドシェルを開き、次のコマンドを実行します。

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>シークレットにアクセスします。

[ASP.NET Core 構成 API](xref:fundamentals/configuration/index)は、シークレットマネージャーのシークレットへのアクセスを提供します。

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

プロジェクトが .NET Framework 対象である場合は、 [Microsoft. Extensions. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet パッケージをインストールします。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core 2.0 以降では、プロジェクトが <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> を呼び出して、構成済みの既定値でホストの新しいインスタンスを初期化すると、開発モードでユーザーシークレットの構成ソースが自動的に追加されます。 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> が <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>されている場合、<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 呼び出し `CreateDefaultBuilder` ます。

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

`CreateDefaultBuilder` が呼び出されない場合は、`Startup` コンストラクターで <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> を呼び出すことによって、ユーザーシークレット構成ソースを明示的に追加します。 次の例に示すように、アプリが開発環境で実行されている場合にのみ `AddUserSecrets` を呼び出します。

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[Microsoft. extension. Configuration. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet パッケージをインストールします。

`Startup` コンストラクターで <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> の呼び出しを使用して、ユーザーシークレット構成ソースを追加します。

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

ユーザーシークレットは、`Configuration` API を使用して取得できます。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a>シークレットを POCO にマップする

オブジェクトリテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計するのに役立ちます。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

上記のシークレットを POCO にマップするには、`Configuration` API の[オブジェクトグラフバインド](xref:fundamentals/configuration/index#bind-to-an-object-graph)機能を使用します。 次のコードは、カスタム `MovieSettings` POCO にバインドし、`ServiceApiKey` プロパティ値にアクセスします。

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

`Movies:ConnectionString` と `Movies:ServiceApiKey` シークレットは `MovieSettings`の各プロパティにマップされます。

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>シークレットを使用した文字列の置換

プレーンテキストでのパスワードの保存は安全ではありません。 たとえば、 *appsettings*に格納されているデータベース接続文字列には、指定されたユーザーのパスワードを含めることができます。

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

より安全な方法は、パスワードをシークレットとして保存することです。 例 :

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password` キーと値のペアを、 *appsettings*の接続文字列から削除します。 例 :

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

シークレットの値を <xref:System.Data.SqlClient.SqlConnectionStringBuilder> オブジェクトの <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> プロパティに設定して、接続文字列を完成させることができます。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a>シークレットを一覧表示する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。

```dotnetcli
dotnet user-secrets list
```

次のような出力が表示されます。

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

前の例では、キー名のコロンは、 *json*内のオブジェクト階層を表しています。

## <a name="remove-a-single-secret"></a>1つのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

アプリの*シークレットの json*ファイルが変更され、`MoviesConnectionString` キーに関連付けられているキーと値のペアが削除されました。

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` を実行すると、次のメッセージが表示されます。

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>すべてのシークレットを削除する

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。

```dotnetcli
dotnet user-secrets clear
```

アプリのすべてのユーザーシークレットが、*シークレットの json*ファイルから削除されています。

```json
{}
```

`dotnet user-secrets list` を実行すると、次のメッセージが表示されます。

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>その他のリソース

* IIS からシークレットマネージャーにアクセスする方法については、[この問題](https://github.com/dotnet/AspNetCore.Docs/issues/16328)を参照してください。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
