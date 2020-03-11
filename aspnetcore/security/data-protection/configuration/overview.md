---
title: ASP.NET Core データ保護の構成
author: rick-anderson
description: ASP.NET Core でデータ保護を構成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: c0846aca4bb663b1d562ab0c877fefba02da460f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654590"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="e583e-103">ASP.NET Core データ保護の構成</span><span class="sxs-lookup"><span data-stu-id="e583e-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="e583e-104">データ保護システムが初期化されると、運用環境に基づいて[既定の設定](xref:security/data-protection/configuration/default-settings)が適用されます。</span><span class="sxs-lookup"><span data-stu-id="e583e-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="e583e-105">これらの設定は、通常、1台のコンピューターで実行されるアプリに適しています。</span><span class="sxs-lookup"><span data-stu-id="e583e-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="e583e-106">開発者が既定の設定を変更する場合があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="e583e-107">アプリは複数のコンピューターに分散されています。</span><span class="sxs-lookup"><span data-stu-id="e583e-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="e583e-108">コンプライアンス上の理由で。</span><span class="sxs-lookup"><span data-stu-id="e583e-108">For compliance reasons.</span></span>

<span data-ttu-id="e583e-109">これらのシナリオでは、データ保護システムに豊富な構成 API が用意されています。</span><span class="sxs-lookup"><span data-stu-id="e583e-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="e583e-110">構成ファイルと同様に、データ保護キーリングは適切なアクセス許可を使用して保護する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="e583e-111">保存時のキーの暗号化は選択できますが、攻撃者によって新しいキーが作成されるのを防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="e583e-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="e583e-112">その結果、アプリのセキュリティが影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="e583e-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="e583e-113">データ保護を使用して構成されたストレージの場所は、構成ファイルを保護する場合と同様に、アプリ自体に限定されます。</span><span class="sxs-lookup"><span data-stu-id="e583e-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="e583e-114">たとえば、キーリングをディスクに保存する場合は、ファイルシステムのアクセス許可を使用します。</span><span class="sxs-lookup"><span data-stu-id="e583e-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="e583e-115">Web アプリが実行されている id のみが、そのディレクトリへの読み取り、書き込み、および作成のアクセス権を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e583e-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="e583e-116">Azure Blob Storage を使用する場合は、web アプリのみが Blob ストア内の新しいエントリの読み取り、書き込み、または作成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e583e-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="e583e-117">拡張メソッド[Adddataprotection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection)は[IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)を返します。</span><span class="sxs-lookup"><span data-stu-id="e583e-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="e583e-118">`IDataProtectionBuilder` は、連結してデータ保護オプションを構成する拡張メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="e583e-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e583e-119">この記事で使用するデータ保護拡張機能には、次の NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="e583e-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="e583e-120">AspNetCore (AzureStorage)</span><span class="sxs-lookup"><span data-stu-id="e583e-120">Microsoft.AspNetCore.DataProtection.AzureStorage</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [<span data-ttu-id="e583e-121">AspNetCore をインポートします。</span><span class="sxs-lookup"><span data-stu-id="e583e-121">Microsoft.AspNetCore.DataProtection.AzureKeyVault</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="e583e-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="e583e-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="e583e-123">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)にキーを格納するには、`Startup` クラスで[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)を使用してシステムを構成します。</span><span class="sxs-lookup"><span data-stu-id="e583e-123">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="e583e-124">キーリングのストレージの場所を設定します (たとえば、 [Persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage))。</span><span class="sxs-lookup"><span data-stu-id="e583e-124">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="e583e-125">`ProtectKeysWithAzureKeyVault` を呼び出すと、キーリングストレージの場所を含む自動データ保護設定を無効にする[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)が実装されるため、場所を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-125">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="e583e-126">前の例では、Azure Blob Storage を使用してキーリングを永続化しています。</span><span class="sxs-lookup"><span data-stu-id="e583e-126">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="e583e-127">詳細については、「[キー記憶域プロバイダー: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e583e-127">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="e583e-128">キーリングを[Persistkeystofilesystem](xref:security/data-protection/implementation/key-storage-providers#file-system)でローカルに永続化することもできます。</span><span class="sxs-lookup"><span data-stu-id="e583e-128">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="e583e-129">`keyIdentifier` は、キーの暗号化に使用される key vault キー識別子です。</span><span class="sxs-lookup"><span data-stu-id="e583e-129">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="e583e-130">たとえば、`contosokeyvault` 内の `dataprotection` という名前の key vault で作成されたキーには `https://contosokeyvault.vault.azure.net/keys/dataprotection/`キー識別子があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-130">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="e583e-131">キーコンテナーへの**ラップ解除キー**と**ラップキー**のアクセス許可をアプリに提供します。</span><span class="sxs-lookup"><span data-stu-id="e583e-131">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="e583e-132">`ProtectKeysWithAzureKeyVault` のオーバーロード:</span><span class="sxs-lookup"><span data-stu-id="e583e-132">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="e583e-133">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_)では、 [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)を使用して、データ保護システムが key vault を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="e583e-133">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="e583e-134">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, string, string, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_)では、データ保護システムが key vault を使用できるようにするために、`ClientId` と[X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-134">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="e583e-135">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, string, string, string)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_)では、データ保護システムがキーコンテナーを使用できるようにするために、`ClientId` と `ClientSecret` を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-135">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="e583e-136">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="e583e-136">PersistKeysToFileSystem</span></span>

<span data-ttu-id="e583e-137">*% Localappdata%* の既定の場所ではなく UNC 共有にキーを格納するには、 [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)を使用してシステムを構成します。</span><span class="sxs-lookup"><span data-stu-id="e583e-137">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="e583e-138">キーの保存場所を変更すると、DPAPI が適切な暗号化メカニズムであるかどうかがわからないため、保存されているキーは自動的に暗号化されなくなります。</span><span class="sxs-lookup"><span data-stu-id="e583e-138">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="e583e-139">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="e583e-139">ProtectKeysWith\*</span></span>

<span data-ttu-id="e583e-140">[ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions)構成 api のいずれかを呼び出すことにより、保存時のキーを保護するようにシステムを構成できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-140">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="e583e-141">次の例では、UNC 共有にキーを格納し、特定の x.509 証明書を使用して保存中のキーを暗号化します。</span><span class="sxs-lookup"><span data-stu-id="e583e-141">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e583e-142">ASP.NET Core 2.1 以降では、ファイルから読み込まれた証明書などの[ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)に[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)を提供できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-142">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

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

<span data-ttu-id="e583e-143">その他の例と組み込みのキー暗号化メカニズムの詳細については、「保存[時のキーの暗号化](xref:security/data-protection/implementation/key-encryption-at-rest)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e583e-143">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="e583e-144">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="e583e-144">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="e583e-145">ASP.NET Core 2.1 以降では、 [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)で[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)証明書の配列を使用して、証明書をローテーションし、保存時のキーを復号化することができます。</span><span class="sxs-lookup"><span data-stu-id="e583e-145">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

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

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="e583e-146">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="e583e-146">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="e583e-147">既定の90日ではなく、14日間のキー有効期間を使用するようにシステムを構成するには、 [Setdefaultkeylifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e583e-147">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="e583e-148">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="e583e-148">SetApplicationName</span></span>

<span data-ttu-id="e583e-149">既定では、データ保護システムは、同じ物理キーリポジトリを共有している場合でも、[コンテンツのルート](xref:fundamentals/index#content-root)パスに基づいて、アプリを相互に分離します。</span><span class="sxs-lookup"><span data-stu-id="e583e-149">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="e583e-150">これにより、アプリは互いの保護されたペイロードを認識できなくなります。</span><span class="sxs-lookup"><span data-stu-id="e583e-150">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="e583e-151">保護されたペイロードをアプリ間で共有するには:</span><span class="sxs-lookup"><span data-stu-id="e583e-151">To share protected payloads among apps:</span></span>

* <span data-ttu-id="e583e-152">各アプリで同じ値を使用して <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> を構成します。</span><span class="sxs-lookup"><span data-stu-id="e583e-152">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="e583e-153">アプリ全体で同じバージョンのデータ保護 API スタックを使用します。</span><span class="sxs-lookup"><span data-stu-id="e583e-153">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="e583e-154">アプリのプロジェクトファイルで、次の**いずれか**を実行します。</span><span class="sxs-lookup"><span data-stu-id="e583e-154">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="e583e-155">[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を介して、同じ共有フレームワークのバージョンを参照します。</span><span class="sxs-lookup"><span data-stu-id="e583e-155">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="e583e-156">同じ[データ保護パッケージ](xref:security/data-protection/introduction#package-layout)のバージョンを参照します。</span><span class="sxs-lookup"><span data-stu-id="e583e-156">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="e583e-157">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="e583e-157">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="e583e-158">場合によっては、有効期限が近づいたときにキーを自動的にロールする (新しいキーを作成する) 必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e583e-158">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="e583e-159">この例の1つは、プライマリとセカンダリの関係で設定されたアプリです。プライマリアプリのみがキー管理の問題を担い、セカンダリアプリはキーリングの読み取り専用ビューを持っているだけです。</span><span class="sxs-lookup"><span data-stu-id="e583e-159">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="e583e-160">次のよう <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>にシステムを構成することによって、キーリングを読み取り専用として処理するようにセカンダリアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-160">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="e583e-161">アプリケーションごとの分離</span><span class="sxs-lookup"><span data-stu-id="e583e-161">Per-application isolation</span></span>

<span data-ttu-id="e583e-162">データ保護システムが ASP.NET Core ホストによって提供されると、アプリが同じワーカープロセスアカウントで実行され、同じマスターキーマテリアルを使用している場合でも、アプリが相互に自動的に分離されます。</span><span class="sxs-lookup"><span data-stu-id="e583e-162">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="e583e-163">これは、System.web の `<machineKey>` 要素の IsolateApps 修飾子に似ています。</span><span class="sxs-lookup"><span data-stu-id="e583e-163">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="e583e-164">分離メカニズムは、ローカルコンピューター上の各アプリを一意のテナントと見なすことによって機能します。したがって、特定のアプリをルートとする <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> は、アプリ ID を識別子として自動的に含みます。</span><span class="sxs-lookup"><span data-stu-id="e583e-164">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="e583e-165">アプリの一意の ID は、アプリの物理パスです。</span><span class="sxs-lookup"><span data-stu-id="e583e-165">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="e583e-166">IIS でホストされているアプリの場合、一意の ID はアプリの IIS 物理パスです。</span><span class="sxs-lookup"><span data-stu-id="e583e-166">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="e583e-167">アプリが web ファーム環境に配置されている場合、IIS 環境が web ファーム内のすべてのコンピューターで同じように構成されていることを前提として、この値は安定しています。</span><span class="sxs-lookup"><span data-stu-id="e583e-167">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="e583e-168">[Kestrel サーバー](xref:fundamentals/servers/index#kestrel)で実行されている自己ホスト型アプリの場合、一意の ID はディスク上のアプリへの物理パスです。</span><span class="sxs-lookup"><span data-stu-id="e583e-168">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="e583e-169">一意の識別子は、個別のアプリとコンピューター自体の両方&mdash;リセットされるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="e583e-169">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="e583e-170">この分離メカニズムは、アプリが悪意のあるものではないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="e583e-170">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="e583e-171">悪意のあるアプリは、同じワーカープロセスアカウントで実行されている他のアプリに常に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-171">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="e583e-172">アプリが相互に信頼されていない共有ホスティング環境では、ホスティングプロバイダーはアプリ間の OS レベルの分離を保証するための手順を実行する必要があります。これには、アプリの基になるキーリポジトリの分離も含まれます。</span><span class="sxs-lookup"><span data-stu-id="e583e-172">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="e583e-173">データ保護システムが ASP.NET Core ホストによって提供されていない場合 (たとえば、`DataProtectionProvider` 具象型でインスタンス化した場合)、アプリの分離は既定で無効になっています。</span><span class="sxs-lookup"><span data-stu-id="e583e-173">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="e583e-174">アプリの分離が無効になっている場合、同じキーマテリアルによってサポートされるすべてのアプリは、適切な[目的](xref:security/data-protection/consumer-apis/purpose-strings)で提供されている限り、ペイロードを共有できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-174">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="e583e-175">この環境でアプリの分離を提供するには、構成オブジェクトで[Setapplicationname](#setapplicationname)メソッドを呼び出し、アプリごとに一意の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="e583e-175">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="e583e-176">UseCryptographicAlgorithms を使用したアルゴリズムの変更</span><span class="sxs-lookup"><span data-stu-id="e583e-176">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="e583e-177">データ保護スタックでは、新しく生成されたキーによって使用される既定のアルゴリズムを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="e583e-177">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="e583e-178">これを行う最も簡単な方法は、構成コールバックから[UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms)を呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="e583e-178">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

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

<span data-ttu-id="e583e-179">既定の EncryptionAlgorithm は AES-256-CBC、既定の ValidationAlgorithm は HMACSHA256 です。</span><span class="sxs-lookup"><span data-stu-id="e583e-179">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="e583e-180">システム管理者は、[コンピューター全体のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を使用して既定のポリシーを設定できますが、`UseCryptographicAlgorithms` を明示的に呼び出すと、既定のポリシーが上書きされます。</span><span class="sxs-lookup"><span data-stu-id="e583e-180">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="e583e-181">`UseCryptographicAlgorithms` を呼び出すと、定義済みの組み込みリストから目的のアルゴリズムを指定できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-181">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="e583e-182">アルゴリズムの実装について心配する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e583e-182">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="e583e-183">上記のシナリオでは、Windows で実行されている場合、データ保護システムは AES の CNG 実装を使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="e583e-183">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="e583e-184">それ以外の場合は[、管理された](/dotnet/api/system.security.cryptography.aes)system.object クラスにフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="e583e-184">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="e583e-185">[UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)の呼び出しを使用して、手動で実装を指定できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-185">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="e583e-186">アルゴリズムを変更しても、キーリング内の既存のキーには影響しません。</span><span class="sxs-lookup"><span data-stu-id="e583e-186">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="e583e-187">新たに生成されたキーにのみ影響します。</span><span class="sxs-lookup"><span data-stu-id="e583e-187">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="e583e-188">カスタムマネージアルゴリズムの指定</span><span class="sxs-lookup"><span data-stu-id="e583e-188">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e583e-189">カスタムマネージアルゴリズムを指定するには、次の実装の種類を指す[Managed認証 Ated暗号化の構成](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration)インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e583e-189">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="e583e-190">カスタムマネージアルゴリズムを指定するには、実装の種類を指す[Managedauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings)インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e583e-190">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="e583e-191">一般に、\*型のプロパティは、 [Symmetricalgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm)と[KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)の具象的なインスタンス化された (パブリックのパラメーター化されていない) 実装を指す必要があります。ただし、システムによって特別なケースによって `typeof(Aes)` などの一部の値が便宜的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e583e-191">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="e583e-192">SymmetricAlgorithm のキーの長さは、≥128ビットおよびブロックサイズ≥64ビットである必要があります。また、PKCS #7 パディングによる CBC モードの暗号化をサポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-192">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="e583e-193">KeyedHashAlgorithm のダイジェストサイズは > = 128 ビットである必要があり、ハッシュアルゴリズムのダイジェストの長さと同じ長さのキーをサポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-193">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="e583e-194">KeyedHashAlgorithm は、必ずしも HMAC である必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e583e-194">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="e583e-195">カスタム Windows CNG アルゴリズムの指定</span><span class="sxs-lookup"><span data-stu-id="e583e-195">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e583e-196">HMAC 検証で CBC モード暗号化を使用してカスタム Windows CNG アルゴリズムを指定するには、アルゴリズム情報を含む[CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration)インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e583e-196">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="e583e-197">HMAC 検証で CBC モード暗号化を使用してカスタム Windows CNG アルゴリズムを指定するには、アルゴリズム情報を含む[CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings)インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e583e-197">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="e583e-198">対称ブロック暗号アルゴリズムのキーの長さは > = 128 ビット、ブロックサイズ > = 64 ビットである必要があります。また、PKCS #7 パディングによる CBC モードの暗号化をサポートしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-198">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="e583e-199">ハッシュアルゴリズムのダイジェストサイズは > = 128 ビットである必要があり、BCRYPT\_ALG\_ハンドル\_HMAC\_フラグフラグを使用して開くことがサポートされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-199">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="e583e-200">指定されたアルゴリズムの既定のプロバイダーを使用するには、\*プロバイダーのプロパティを null に設定できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-200">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="e583e-201">詳細については、 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e583e-201">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e583e-202">検証で Galois/カウンタモードの暗号化を使用してカスタム Windows CNG アルゴリズムを指定するには、アルゴリズム情報を含む[CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration)インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e583e-202">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="e583e-203">検証で Galois/カウンタモードの暗号化を使用してカスタム Windows CNG アルゴリズムを指定するには、アルゴリズム情報を含む[CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings)インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e583e-203">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="e583e-204">対称ブロック暗号アルゴリズムのキーの長さは > = 128 ビット、ブロックサイズは正確に128ビットである必要があり、GCM 暗号化をサポートしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-204">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="e583e-205">指定されたアルゴリズムの既定のプロバイダーを使用するには、 [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider)プロパティを null に設定します。</span><span class="sxs-lookup"><span data-stu-id="e583e-205">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="e583e-206">詳細については、 [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e583e-206">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="e583e-207">その他のカスタムアルゴリズムの指定</span><span class="sxs-lookup"><span data-stu-id="e583e-207">Specifying other custom algorithms</span></span>

<span data-ttu-id="e583e-208">データ保護システムは、ファーストクラスの API として公開されていませんが、ほとんどすべての種類のアルゴリズムを指定できるように拡張されています。</span><span class="sxs-lookup"><span data-stu-id="e583e-208">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="e583e-209">たとえば、ハードウェアセキュリティモジュール (HSM) に含まれるすべてのキーを保持し、コア暗号化および復号化ルーチンのカスタム実装を提供することができます。</span><span class="sxs-lookup"><span data-stu-id="e583e-209">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="e583e-210">詳細については、「[コア暗号化機能拡張](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor)の[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e583e-210">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="e583e-211">Docker コンテナーでホストするときのキーの永続化</span><span class="sxs-lookup"><span data-stu-id="e583e-211">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="e583e-212">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/)コンテナーでホストする場合は、次のいずれかの方法でキーを管理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-212">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="e583e-213">共有ボリュームやホストによってマウントされたボリュームなど、コンテナーの有効期間を超えて永続化される Docker ボリュームのフォルダー。</span><span class="sxs-lookup"><span data-stu-id="e583e-213">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="e583e-214">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)や[Redis](https://redis.io/)などの外部プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="e583e-214">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="e583e-215">Redis でのキーの永続化</span><span class="sxs-lookup"><span data-stu-id="e583e-215">Persisting keys with Redis</span></span>

<span data-ttu-id="e583e-216">キーを格納するには、 [Redis データの永続](/azure/azure-cache-for-redis/cache-how-to-premium-persistence)化をサポートする redis のバージョンのみを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e583e-216">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="e583e-217">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction)は永続的で、キーを格納するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="e583e-217">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="e583e-218">詳細については、[こちらの GitHub の問題](https://github.com/dotnet/AspNetCore/issues/13476)のページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e583e-218">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e583e-219">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e583e-219">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
