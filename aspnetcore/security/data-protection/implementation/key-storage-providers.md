---
title: ASP.NET Core のキー記憶域プロバイダー
author: rick-anderson
description: ASP.NET Core の主要な記憶域プロバイダーと、キーの格納場所を構成する方法について説明します。
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: ec746f383c18ccc7b60c614c990f7577d2d52a20
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74052845"
---
# <a name="key-storage-providers-in-aspnet-core"></a>ASP.NET Core のキー記憶域プロバイダー

データ保護システムでは、暗号化キーの保存先を決定するために、[既定で検出メカニズム](xref:security/data-protection/configuration/default-settings)が使用されます。 開発者は、既定の検出メカニズムを上書きし、場所を手動で指定できます。

> [!WARNING]
> 明示的なキーの保存場所を指定した場合、データ保護システムは解除の既定のキー暗号化メカニズムを使用するので、保存時にキーが暗号化されなくなります。 運用環境のデプロイで[は、明示的なキー暗号化メカニズム](xref:security/data-protection/implementation/key-encryption-at-rest)を追加で指定することをお勧めします。

## <a name="file-system"></a>ファイル システム

ファイルシステムベースのキーリポジトリを構成するには、次に示すように、 [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)構成ルーチンを呼び出します。 キーを格納するリポジトリを指す[DirectoryInfo](/dotnet/api/system.io.directoryinfo)を指定します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo` は、ローカルコンピューター上のディレクトリをポイントするか、ネットワーク共有上のフォルダーを指すことができます。 ローカルコンピューター上のディレクトリを指している場合 (つまり、このリポジトリを使用するためにアクセスが必要なのはローカルコンピューター上のアプリのみです)、windows [DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (windows) を使用して保存時のキーを暗号化することを検討してください。 それ以外の場合は、 [x.509 証明書](xref:security/data-protection/implementation/key-encryption-at-rest)を使用して保存時のキーを暗号化することを検討してください。

## <a name="azure-storage"></a>Azure ストレージ

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)パッケージを使用すると、Azure Blob Storage にデータ保護キーを格納できます。 キーは、web アプリの複数のインスタンス間で共有できます。 アプリは、認証 cookie または CSRF 保護を複数のサーバーで共有できます。

Azure Blob Storage プロバイダーを構成するには、 [Persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)オーバーロードのいずれかを呼び出します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Web アプリが Azure サービスとして実行されている場合は、認証トークンを自動的に作成でき[ます。](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

サービス[間認証の構成の詳細については、こちらを](/azure/key-vault/service-to-service-authentication)参照してください。

## <a name="redis"></a>Cache

::: moniker range=">= aspnetcore-2.2"

[StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/)パッケージは、Redis cache にデータ保護キーを格納することを許可します。 キーは、web アプリの複数のインスタンス間で共有できます。 アプリは、認証 cookie または CSRF 保護を複数のサーバーで共有できます。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/)パッケージを使用すると、redis cache にデータ保護キーを格納できます。 キーは、web アプリの複数のインスタンス間で共有できます。 アプリは、認証 cookie または CSRF 保護を複数のサーバーで共有できます。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Redis でを構成するには、 [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis)オーバーロードのいずれかを呼び出します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Redis でを構成するには、 [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis)オーバーロードのいずれかを呼び出します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

詳細については、以下のトピックを参照してください。

* [StackExchange. Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis Cache](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [aspnet/DataProtection のサンプル](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>レジストリ

**Windows の展開にのみ適用されます。**

場合によっては、アプリケーションにファイルシステムへの書き込みアクセス権がないことがあります。 アプリが仮想サービスアカウント ( *w3wp.exe のアプリプール id など)* として実行されているシナリオについて考えてみましょう。 このような場合、管理者は、サービスアカウント id によってアクセス可能なレジストリキーをプロビジョニングできます。 次に示すように、 [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension メソッドを呼び出します。 暗号化キーを格納する場所を指す[RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey)を指定します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> Rest でのキーの暗号化には[WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest)を使用することをお勧めします。

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

[AspNetCore コア](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)パッケージは、Entity Framework Core を使用してデータベースにデータ保護キーを格納するためのメカニズムを提供します。 `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet パッケージは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)の一部ではなく、プロジェクトファイルに追加する必要があります。

このパッケージでは、web アプリの複数のインスタンス間でキーを共有できます。

EF Core プロバイダーを構成するには、 [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)メソッドを呼び出します。

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

ジェネリックパラメーターの `TContext`は[Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)から継承し、 [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)を実装する必要があります。

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

`DataProtectionKeys` テーブルを作成します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**パッケージマネージャーコンソール**(PMC) ウィンドウで、次のコマンドを実行します。

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コマンドシェルで次のコマンドを実行します。

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` は、前のコードサンプルで定義されている `DbContext` です。 別の名前の `DbContext` を使用している場合は、`DbContext` 名を `MyKeysContext`に置き換えます。

`DataProtectionKeys` クラス/エンティティは、次の表に示す構造を採用しています。

| プロパティ/フィールド | CLR 型 | SQL 型              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`、PK、not null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`、null |
| `Xml`          | `string` | `nvarchar(MAX)`、null |

::: moniker-end

## <a name="custom-key-repository"></a>カスタムキーリポジトリ

インボックス機構が適切でない場合、開発者はカスタム[IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)を提供することで、独自のキー永続化メカニズムを指定できます。
