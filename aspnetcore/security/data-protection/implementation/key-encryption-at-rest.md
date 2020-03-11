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
# <a name="key-encryption-at-rest-in-aspnet-core"></a>ASP.NET Core での保存時のキーの暗号化

データ保護システムでは、暗号化キーを保存時に暗号化する方法を[既定で検出するメカニズム](xref:security/data-protection/configuration/default-settings)を使用します。 開発者は検出メカニズムをオーバーライドし、保存時のキーの暗号化方法を手動で指定できます。

> [!WARNING]
> 明示的なキーの[保存場所](xref:security/data-protection/implementation/key-storage-providers)を指定すると、データ保護システムによって、解除の既定のキー暗号化メカニズムが使用されます。 そのため、キーは保存時に暗号化されなくなりました。 運用環境のデプロイで[は、明示的なキー暗号化メカニズムを指定](xref:security/data-protection/implementation/key-encryption-at-rest)することをお勧めします。 保存時暗号化メカニズムのオプションについては、このトピックで説明します。

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)にキーを格納するには、`Startup` クラスで[ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault)を使用してシステムを構成します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

詳細については、「 [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault)」を参照してください。

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Windows の展開にのみ適用されます。**

Windows DPAPI が使用されている場合、キーマテリアルは[CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata)で暗号化されてから、ストレージに保存されます。 DPAPI は、現在のコンピューターの外部で読み取られることがないデータの適切な暗号化メカニズムです (ただし、これらのキーを Active Directory に戻すことはできますが[、「DPAPI とローミングプロファイル](https://support.microsoft.com/kb/309408/#6)」を参照してください)。 Rest 暗号化キーを構成するには、次のいずれかの[ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi)拡張メソッドを呼び出します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

パラメーターを使用せずに `ProtectKeysWithDpapi` を呼び出すと、現在の Windows ユーザーアカウントのみが、永続化されたキーリングを解読できます。 必要に応じて、(現在のユーザーアカウントだけでなく) コンピューター上のすべてのユーザーアカウントがキーリングを解読できるように指定することもできます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>X.509 証明書

アプリが複数のコンピューターに分散している場合は、共有の x.509 証明書をコンピューター全体に配布し、保存されているキーの暗号化に証明書を使用するようにホストされるアプリを構成すると便利な場合があります。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

.NET Framework の制限により、CAPI 秘密キーを持つ証明書のみがサポートされます。 これらの制限について考えられる回避策については、以下のコンテンツを参照してください。

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**このメカニズムは、Windows 8/Windows Server 2012 以降でのみ使用できます。**

Windows 8 以降では、Windows OS は DPAPI NG (CNG DPAPI とも呼ばれます) をサポートしています。 詳細については、「 [CNG DPAPI につい](/windows/desktop/SecCNG/cng-dpapi)て」を参照してください。

プリンシパルは、保護記述子の規則としてエンコードされます。 次の例では、 [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)を呼び出します。指定された SID を持つドメインに参加しているユーザーのみが、キーリングの暗号化を解除できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

`ProtectKeysWithDpapiNG`のパラメーターなしのオーバーロードもあります。 この便利な方法を使用して、"SID = {CURRENT_ACCOUNT_SID}" という規則を指定します。 *CURRENT_ACCOUNT_SID*は現在の Windows ユーザーアカウントの SID です。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

このシナリオでは、AD ドメインコントローラーは、DPAPI によって使用される暗号化キーを配布する役割を担います。 ターゲットユーザーは、ドメインに参加しているコンピューターから暗号化されたペイロードを解読できます (プロセスが id で実行されている場合)。

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Windows DPAPI を使用した証明書ベースの暗号化-NG

アプリが Windows 8.1/Windows Server 2012 R2 以降で実行されている場合は、Windows DPAPI-NG を使用して証明書ベースの暗号化を実行できます。 規則記述子文字列 "CERTIFICATE = HashId: THUMBPRINT" を使用します。ここで、*拇印*は証明書の16進数でエンコードされた SHA1 拇印です。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

キーの暗号を解除するには、このリポジトリでポイントされているすべてのアプリが Windows 8.1/Windows Server 2012 R2 以降で実行されている必要があります。

## <a name="custom-key-encryption"></a>カスタムキーの暗号化

インボックス機構が適切でない場合、開発者はカスタム[IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)を提供することで、独自のキー暗号化メカニズムを指定できます。
