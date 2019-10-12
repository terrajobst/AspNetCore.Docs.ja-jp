---
title: ASP.NET Core データ保護の構成
author: rick-anderson
description: ASP.NET Core でデータ保護を構成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 380293f650c9548c286f98c0447c7ed08b918f2a
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007381"
---
# <a name="configure-aspnet-core-data-protection"></a>ASP.NET Core データ保護の構成

データ保護システムが初期化されると、運用環境に基づいて[既定の設定](xref:security/data-protection/configuration/default-settings)が適用されます。 これらの設定は、通常、1台のコンピューターで実行されるアプリに適しています。 開発者が既定の設定を変更する場合があります。

* アプリは複数のコンピューターに分散されています。
* コンプライアンス上の理由で。

これらのシナリオでは、データ保護システムに豊富な構成 API が用意されています。

> [!WARNING]
> 構成ファイルと同様に、データ保護キーリングは適切なアクセス許可を使用して保護する必要があります。 保存時のキーの暗号化は選択できますが、攻撃者によって新しいキーが作成されるのを防ぐことはできません。 その結果、アプリのセキュリティが影響を受けます。 データ保護を使用して構成されたストレージの場所は、構成ファイルを保護する場合と同様に、アプリ自体に限定されます。 たとえば、キーリングをディスクに保存する場合は、ファイルシステムのアクセス許可を使用します。 Web アプリが実行されている id のみが、そのディレクトリへの読み取り、書き込み、および作成のアクセス権を持っていることを確認します。 Azure Blob Storage を使用する場合は、web アプリのみが Blob ストア内の新しいエントリの読み取り、書き込み、または作成を行うことができます。
>
> 拡張メソッド[Adddataprotection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection)は[IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)を返します。 `IDataProtectionBuilder` は、データ保護オプションを構成するために連結できる拡張メソッドを公開します。

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)にキーを格納するには、`Startup` クラスの[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)を使用してシステムを構成します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

キーリングのストレージの場所を設定します (たとえば、 [Persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage))。 @No__t-0 を呼び出すと、キーリングストレージの場所を含む自動データ保護設定を無効にする[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)が実装されるため、場所を設定する必要があります。 前の例では、Azure Blob Storage を使用してキーリングを永続化しています。 詳細については、「[Key storage providers」を参照してください。Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage) に関するページをご覧ください。 キーリングを[Persistkeystofilesystem](xref:security/data-protection/implementation/key-storage-providers#file-system)でローカルに永続化することもできます。

@No__t-0 はキーの暗号化に使用される key vault キー識別子です。 たとえば、key vault で作成されたキー (`contosokeyvault` の `dataprotection`) には、キー識別子 `https://contosokeyvault.vault.azure.net/keys/dataprotection/` があります。 キーコンテナーへの**ラップ解除キー**と**ラップキー**のアクセス許可をアプリに提供します。

`ProtectKeysWithAzureKeyVault` のオーバーロード:

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_)では、 [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)を使用して、データ保護システムが key vault を使用できるようにします。
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, string, string, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_)では、データ保護システムでキーコンテナーを使用できるようにするために、`ClientId` と[X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)を使用できます。
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, string, string, string)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_)では、データ保護システムでキーコンテナーを使用できるようにするために、`ClientId` と `ClientSecret` を使用できます。

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

*% Localappdata%* の既定の場所ではなく UNC 共有にキーを格納するには、 [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)を使用してシステムを構成します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> キーの保存場所を変更すると、DPAPI が適切な暗号化メカニズムであるかどうかがわからないため、保存されているキーは自動的に暗号化されなくなります。

## <a name="protectkeyswith"></a>ProtectKeysWith\*

[ProtectKeysWith @ no__t](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions)構成 api のいずれかを呼び出すことにより、保存時のキーを保護するようにシステムを構成できます。 次の例では、UNC 共有にキーを格納し、特定の x.509 証明書を使用して保存中のキーを暗号化します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2.1 以降では、ファイルから読み込まれた証明書などの[ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)に[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)を提供できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

その他の例と組み込みのキー暗号化メカニズムの詳細については、「保存[時のキーの暗号化](xref:security/data-protection/implementation/key-encryption-at-rest)」を参照してください。

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

ASP.NET Core 2.1 以降では、 [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)で[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)証明書の配列を使用して、証明書をローテーションし、保存時のキーを復号化することができます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

既定の90日ではなく、14日間のキー有効期間を使用するようにシステムを構成するには、 [Setdefaultkeylifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)を使用します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

既定では、データ保護システムは、同じ物理キーリポジトリを共有している場合でも、[コンテンツのルート](xref:fundamentals/index#content-root)パスに基づいて、アプリを相互に分離します。 これにより、アプリは互いの保護されたペイロードを認識できなくなります。

保護されたペイロードをアプリ間で共有するには:

* 同じ値を使用して、各アプリで <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> を構成します。
* アプリ全体で同じバージョンのデータ保護 API スタックを使用します。 アプリのプロジェクトファイルで、次の**いずれか**を実行します。
  * [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を介して、同じ共有フレームワークのバージョンを参照します。
  * 同じ[データ保護パッケージ](xref:security/data-protection/introduction#package-layout)のバージョンを参照します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

場合によっては、有効期限が近づいたときにキーを自動的にロールする (新しいキーを作成する) 必要はありません。 この例の1つは、プライマリとセカンダリの関係で設定されたアプリです。プライマリアプリのみがキー管理の問題を担い、セカンダリアプリはキーリングの読み取り専用ビューを持っているだけです。 @No__t-0 でシステムを構成することによって、キーリングを読み取り専用として処理するようにセカンダリアプリを構成できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>アプリケーションごとの分離

データ保護システムが ASP.NET Core ホストによって提供されると、アプリが同じワーカープロセスアカウントで実行され、同じマスターキーマテリアルを使用している場合でも、アプリが相互に自動的に分離されます。 これは、System.web の `<machineKey>` 要素の IsolateApps 修飾子に似ています。

分離メカニズムは、ローカルコンピューター上の各アプリを一意のテナントとして考慮することによって機能します。したがって、特定のアプリに対してルートされる @no__t 0 には、アプリ ID が識別子として自動的に含まれます。 アプリの一意の ID は、アプリの物理パスです。

* IIS でホストされているアプリの場合、一意の ID はアプリの IIS 物理パスです。 アプリが web ファーム環境に配置されている場合、IIS 環境が web ファーム内のすべてのコンピューターで同じように構成されていることを前提として、この値は安定しています。
* [Kestrel サーバー](xref:fundamentals/servers/index#kestrel)で実行されている自己ホスト型アプリの場合、一意の ID はディスク上のアプリへの物理パスです。

一意の識別子は、個別のアプリとコンピューター自体の両方で @ no__t がリセットされるように設計されています。

この分離メカニズムは、アプリが悪意のあるものではないことを前提としています。 悪意のあるアプリは、同じワーカープロセスアカウントで実行されている他のアプリに常に影響を与える可能性があります。 アプリが相互に信頼されていない共有ホスティング環境では、ホスティングプロバイダーはアプリ間の OS レベルの分離を保証するための手順を実行する必要があります。これには、アプリの基になるキーリポジトリの分離も含まれます。

データ保護システムが ASP.NET Core ホストによって提供されない場合 (たとえば、@no__t 0 の具象型を使用してインスタンス化する場合)、アプリの分離は既定で無効になります。 アプリの分離が無効になっている場合、同じキーマテリアルによってサポートされるすべてのアプリは、適切な[目的](xref:security/data-protection/consumer-apis/purpose-strings)で提供されている限り、ペイロードを共有できます。 この環境でアプリの分離を提供するには、構成オブジェクトで[Setapplicationname](#setapplicationname)メソッドを呼び出し、アプリごとに一意の名前を指定します。

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>UseCryptographicAlgorithms を使用したアルゴリズムの変更

データ保護スタックでは、新しく生成されたキーによって使用される既定のアルゴリズムを変更することができます。 これを行う最も簡単な方法は、構成コールバックから[UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms)を呼び出すことです。

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

既定の EncryptionAlgorithm は AES-256-CBC、既定の ValidationAlgorithm は HMACSHA256 です。 システム管理者は、[コンピューター全体のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を使用して既定のポリシーを設定できますが、`UseCryptographicAlgorithms` を明示的に呼び出すと、既定のポリシーが上書きされます。

@No__t-0 を呼び出すと、定義済みの組み込みリストから目的のアルゴリズムを指定できます。 アルゴリズムの実装について心配する必要はありません。 上記のシナリオでは、Windows で実行されている場合、データ保護システムは AES の CNG 実装を使用しようとします。 それ以外の場合は[、管理された](/dotnet/api/system.security.cryptography.aes)system.object クラスにフォールバックします。

[UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)の呼び出しを使用して、手動で実装を指定できます。

> [!TIP]
> アルゴリズムを変更しても、キーリング内の既存のキーには影響しません。 新たに生成されたキーにのみ影響します。

### <a name="specifying-custom-managed-algorithms"></a>カスタムマネージアルゴリズムの指定

::: moniker range=">= aspnetcore-2.0"

カスタムマネージアルゴリズムを指定するには、次の実装の種類を指す[Managed認証 Ated暗号化の構成](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration)インスタンスを作成します。

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

カスタムマネージアルゴリズムを指定するには、実装の種類を指す[Managedauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings)インスタンスを作成します。

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

一般に、@no__t 0Type プロパティは[Symmetricalgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm)と[KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)の具象的でインスタンス化された (パブリックのパラメーター化されていない) 実装を指す必要がありますが、システムによっては `typeof(Aes)` などの値が効率化.

> [!NOTE]
> SymmetricAlgorithm のキーの長さは、≥128ビットおよびブロックサイズ≥64ビットである必要があります。また、PKCS #7 パディングによる CBC モードの暗号化をサポートする必要があります。 KeyedHashAlgorithm のダイジェストサイズは > = 128 ビットである必要があり、ハッシュアルゴリズムのダイジェストの長さと同じ長さのキーをサポートする必要があります。 KeyedHashAlgorithm は、必ずしも HMAC である必要はありません。

### <a name="specifying-custom-windows-cng-algorithms"></a>カスタム Windows CNG アルゴリズムの指定

::: moniker range=">= aspnetcore-2.0"

HMAC 検証で CBC モード暗号化を使用してカスタム Windows CNG アルゴリズムを指定するには、アルゴリズム情報を含む[CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration)インスタンスを作成します。

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

HMAC 検証で CBC モード暗号化を使用してカスタム Windows CNG アルゴリズムを指定するには、アルゴリズム情報を含む[CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings)インスタンスを作成します。

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> 対称ブロック暗号アルゴリズムのキーの長さは > = 128 ビット、ブロックサイズ > = 64 ビットである必要があります。また、PKCS #7 パディングによる CBC モードの暗号化をサポートしている必要があります。 ハッシュアルゴリズムのダイジェストサイズは > = 128 ビットである必要があり、BCRYPT @ no__t-0ALG @ no__t-1HANDLE @ no__t-2HMAC @ no__t-3FLAG フラグを使用して開くことがサポートされている必要があります。 指定されたアルゴリズムの既定のプロバイダーを使用するには、@no__t 0Provider プロパティを null に設定できます。 詳細については、 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)のドキュメントを参照してください。

::: moniker range=">= aspnetcore-2.0"

検証で Galois/カウンタモードの暗号化を使用してカスタム Windows CNG アルゴリズムを指定するには、アルゴリズム情報を含む[CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration)インスタンスを作成します。

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

検証で Galois/カウンタモードの暗号化を使用してカスタム Windows CNG アルゴリズムを指定するには、アルゴリズム情報を含む[CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings)インスタンスを作成します。

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> 対称ブロック暗号アルゴリズムのキーの長さは > = 128 ビット、ブロックサイズは正確に128ビットである必要があり、GCM 暗号化をサポートしている必要があります。 指定されたアルゴリズムの既定のプロバイダーを使用するには、 [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider)プロパティを null に設定します。 詳細については、 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)のドキュメントを参照してください。

### <a name="specifying-other-custom-algorithms"></a>その他のカスタムアルゴリズムの指定

データ保護システムは、ファーストクラスの API として公開されていませんが、ほとんどすべての種類のアルゴリズムを指定できるように拡張されています。 たとえば、ハードウェアセキュリティモジュール (HSM) に含まれるすべてのキーを保持し、コア暗号化および復号化ルーチンのカスタム実装を提供することができます。 詳細については、「[コア暗号化機能拡張](xref:security/data-protection/extensibility/core-crypto)の[IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor)」を参照してください。

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Docker コンテナーでホストするときのキーの永続化

[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/)コンテナーでホストする場合は、次のいずれかの方法でキーを管理する必要があります。

* 共有ボリュームやホストによってマウントされたボリュームなど、コンテナーの有効期間を超えて永続化される Docker ボリュームのフォルダー。
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)や[Redis](https://redis.io/)などの外部プロバイダー。

## <a name="persisting-keys-with-redis"></a>Redis でのキーの永続化

キーを格納するには、 [Redis データの永続](/azure/azure-cache-for-redis/cache-how-to-premium-persistence)化をサポートする redis のバージョンのみを使用する必要があります。 [Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction)は永続的で、キーを格納するために使用できます。 詳細については、次を参照してください。[この GitHub の問題](https://github.com/aspnet/AspNetCore/issues/13476)します。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
