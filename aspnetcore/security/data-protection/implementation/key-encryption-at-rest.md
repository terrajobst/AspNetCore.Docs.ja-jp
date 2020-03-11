---
title: ASP.NET Core での保存時のキーの暗号化
author: rick-anderson
description: 保存時のデータ保護キーの暗号化 ASP.NET Core の実装の詳細について説明します。
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651632"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a><span data-ttu-id="a2c70-103">ASP.NET Core での保存時のキーの暗号化</span><span class="sxs-lookup"><span data-stu-id="a2c70-103">Key encryption at rest in ASP.NET Core</span></span>

<span data-ttu-id="a2c70-104">データ保護システムでは、暗号化キーを保存時に暗号化する方法を[既定で検出するメカニズム](xref:security/data-protection/configuration/default-settings)を使用します。</span><span class="sxs-lookup"><span data-stu-id="a2c70-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="a2c70-105">開発者は検出メカニズムをオーバーライドし、保存時のキーの暗号化方法を手動で指定できます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="a2c70-106">明示的なキーの[保存場所](xref:security/data-protection/implementation/key-storage-providers)を指定すると、データ保護システムによって、解除の既定のキー暗号化メカニズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="a2c70-107">そのため、キーは保存時に暗号化されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="a2c70-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="a2c70-108">運用環境のデプロイで[は、明示的なキー暗号化メカニズムを指定](xref:security/data-protection/implementation/key-encryption-at-rest)することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a2c70-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="a2c70-109">保存時暗号化メカニズムのオプションについては、このトピックで説明します。</span><span class="sxs-lookup"><span data-stu-id="a2c70-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="a2c70-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a2c70-110">Azure Key Vault</span></span>

<span data-ttu-id="a2c70-111">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)にキーを格納するには、`Startup` クラスで[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)を使用してシステムを構成します。</span><span class="sxs-lookup"><span data-stu-id="a2c70-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="a2c70-112">詳細については、「 [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a2c70-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="a2c70-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="a2c70-113">Windows DPAPI</span></span>

<span data-ttu-id="a2c70-114">**Windows の展開にのみ適用されます。**</span><span class="sxs-lookup"><span data-stu-id="a2c70-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="a2c70-115">Windows DPAPI が使用されている場合、キーマテリアルは[CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata)で暗号化されてから、ストレージに保存されます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="a2c70-116">DPAPI は、現在のコンピューターの外部で読み取られることがないデータの適切な暗号化メカニズムです (ただし、これらのキーを Active Directory に戻すことはできますが[、「DPAPI とローミングプロファイル](https://support.microsoft.com/kb/309408/#6)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="a2c70-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="a2c70-117">Rest 暗号化キーを構成するには、次のいずれかの[ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi)拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a2c70-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="a2c70-118">パラメーターを使用せずに `ProtectKeysWithDpapi` を呼び出すと、現在の Windows ユーザーアカウントのみが、永続化されたキーリングを解読できます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="a2c70-119">必要に応じて、(現在のユーザーアカウントだけでなく) コンピューター上のすべてのユーザーアカウントがキーリングを解読できるように指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="a2c70-120">X.509 証明書</span><span class="sxs-lookup"><span data-stu-id="a2c70-120">X.509 certificate</span></span>

<span data-ttu-id="a2c70-121">アプリが複数のコンピューターに分散している場合は、共有の x.509 証明書をコンピューター全体に配布し、保存されているキーの暗号化に証明書を使用するようにホストされるアプリを構成すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="a2c70-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="a2c70-122">.NET Framework の制限により、CAPI 秘密キーを持つ証明書のみがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="a2c70-123">これらの制限について考えられる回避策については、以下のコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a2c70-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="a2c70-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="a2c70-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="a2c70-125">**このメカニズムは、Windows 8/Windows Server 2012 以降でのみ使用できます。**</span><span class="sxs-lookup"><span data-stu-id="a2c70-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="a2c70-126">Windows 8 以降では、Windows OS は DPAPI NG (CNG DPAPI とも呼ばれます) をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a2c70-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="a2c70-127">詳細については、「 [CNG DPAPI につい](/windows/desktop/SecCNG/cng-dpapi)て」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a2c70-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="a2c70-128">プリンシパルは、保護記述子の規則としてエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="a2c70-129">次の例では、 [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)を呼び出します。指定された SID を持つドメインに参加しているユーザーのみが、キーリングの暗号化を解除できます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="a2c70-130">`ProtectKeysWithDpapiNG`のパラメーターなしのオーバーロードもあります。</span><span class="sxs-lookup"><span data-stu-id="a2c70-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="a2c70-131">この便利な方法を使用して、"SID = {CURRENT_ACCOUNT_SID}" という規則を指定します。 *CURRENT_ACCOUNT_SID*は現在の Windows ユーザーアカウントの SID です。</span><span class="sxs-lookup"><span data-stu-id="a2c70-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="a2c70-132">このシナリオでは、AD ドメインコントローラーは、DPAPI によって使用される暗号化キーを配布する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="a2c70-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="a2c70-133">ターゲットユーザーは、ドメインに参加しているコンピューターから暗号化されたペイロードを解読できます (プロセスが id で実行されている場合)。</span><span class="sxs-lookup"><span data-stu-id="a2c70-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="a2c70-134">Windows DPAPI を使用した証明書ベースの暗号化-NG</span><span class="sxs-lookup"><span data-stu-id="a2c70-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="a2c70-135">アプリが Windows 8.1/Windows Server 2012 R2 以降で実行されている場合は、Windows DPAPI-NG を使用して証明書ベースの暗号化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="a2c70-136">規則記述子文字列 "CERTIFICATE = HashId: THUMBPRINT" を使用します。ここで、*拇印*は証明書の16進数でエンコードされた SHA1 拇印です。</span><span class="sxs-lookup"><span data-stu-id="a2c70-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="a2c70-137">キーの暗号を解除するには、このリポジトリでポイントされているすべてのアプリが Windows 8.1/Windows Server 2012 R2 以降で実行されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2c70-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="a2c70-138">カスタムキーの暗号化</span><span class="sxs-lookup"><span data-stu-id="a2c70-138">Custom key encryption</span></span>

<span data-ttu-id="a2c70-139">インボックス機構が適切でない場合、開発者はカスタム[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)を提供することで、独自のキー暗号化メカニズムを指定できます。</span><span class="sxs-lookup"><span data-stu-id="a2c70-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
