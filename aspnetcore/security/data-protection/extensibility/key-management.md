---
title: ASP.NET Core でのキー管理の拡張性
author: rick-anderson
description: ASP.NET Core データ保護キー管理の拡張性について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654260"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>ASP.NET Core でのキー管理の拡張性

> [!TIP]
> このセクションを読む前に、「[キー管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)」セクションをお読みください。このセクションでは、これらの api の基本的な概念について説明します。

> [!WARNING]
> 次のインターフェイスのいずれかを実装する型がスレッド セーフにする必要があります複数の呼び出し元の。

## <a name="key"></a>Key

`IKey` インターフェイスは、cryptosystem のキーの基本的な表現です。 用語のキーは、「暗号化キー マテリアル」の文字どおりの意味ではなく、抽象という意味では、ここに使用されます。 キーには、次のプロパティがあります。

* アクティブ化、作成、および有効期限の日付

* 失効状態

* キー識別子 (GUID)

::: moniker range=">= aspnetcore-2.0"

さらに、`IKey` は、このキーに関連付けられている[I認証 Ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成するために使用できる `CreateEncryptor` メソッドを公開します。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

さらに、`IKey` は、このキーに関連付けられている[I認証 Ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成するために使用できる `CreateEncryptorInstance` メソッドを公開します。

::: moniker-end

> [!NOTE]
> `IKey` インスタンスから未加工の暗号化マテリアルを取得する API はありません。

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager` インターフェイスは、一般的なキーの格納、取得、および操作を行うオブジェクトを表します。 次の 3 つの高度な操作が公開しています。

* 新しいキーを作成し、ストレージに保存します。

* 記憶域からすべてのキーを取得します。

* 1 つまたは複数のキーを取り消すし、記憶域に失効情報を保持します。

>[!WARNING]
> `IKeyManager` の作成は非常に高度なタスクであり、ほとんどの開発者はこれを試みることはできません。 代わりに、ほとんどの開発者は、 [Xmlkeymanager](#xmlkeymanager)クラスによって提供される機能を活用する必要があります。

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager` 型は、`IKeyManager`のインボックスの具象実装です。 キー エスクローおよび保存時のキーの暗号化を含むいくつかの便利な機能を提供します。 このシステムのキーは、XML 要素 (具体的には[XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) として表されます。

`XmlKeyManager` は、タスクを実行する過程で、他のいくつかのコンポーネントに依存します。

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`、新しいキーによって使用されるアルゴリズムを指定します。

* `IXmlRepository`は、ストレージ内でのキーの保存場所を制御します。

* `IXmlEncryptor` [省略可能]。保存時にキーを暗号化できます。

* `IKeyEscrowSink` [省略可能]。キーエスクローサービスを提供します。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`は、ストレージ内でのキーの保存場所を制御します。

* `IXmlEncryptor` [省略可能]。保存時にキーを暗号化できます。

* `IKeyEscrowSink` [省略可能]。キーエスクローサービスを提供します。

::: moniker-end

これらのコンポーネントが `XmlKeyManager`内でどのように結線されるかを示す概略図を次に示します。

::: moniker range=">= aspnetcore-2.0"

![キーの作成](key-management/_static/keycreation2.png)

*キーの作成/CreateNewKey*

`CreateNewKey`の実装では、`AlgorithmConfiguration` コンポーネントを使用して一意の `IAuthenticatedEncryptorDescriptor`が作成され、その後 XML としてシリアル化されます。 キー エスクロー シンクが存在する場合は、長期的なストレージ、未処理の XML を (暗号化されていない) が、シンクに提供されます。 暗号化されていない XML は、(必要に応じて) `IXmlEncryptor` を通じて実行され、暗号化された XML ドキュメントを生成します。 この暗号化されたドキュメントは、`IXmlRepository`を使用して長期的なストレージに保存されます。 (`IXmlEncryptor` が構成されていない場合は、暗号化されていないドキュメントが `IXmlRepository`に保持されます)。

![キーの取得](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![キーの作成](key-management/_static/keycreation1.png)

*キーの作成/CreateNewKey*

`CreateNewKey`の実装では、`IAuthenticatedEncryptorConfiguration` コンポーネントを使用して一意の `IAuthenticatedEncryptorDescriptor`が作成され、その後 XML としてシリアル化されます。 キー エスクロー シンクが存在する場合は、長期的なストレージ、未処理の XML を (暗号化されていない) が、シンクに提供されます。 暗号化されていない XML は、(必要に応じて) `IXmlEncryptor` を通じて実行され、暗号化された XML ドキュメントを生成します。 この暗号化されたドキュメントは、`IXmlRepository`を使用して長期的なストレージに保存されます。 (`IXmlEncryptor` が構成されていない場合は、暗号化されていないドキュメントが `IXmlRepository`に保持されます)。

![キーの取得](key-management/_static/keyretrieval1.png)

::: moniker-end

*キーの取得/GetAllKeys*

`GetAllKeys`の実装では、キーと失効を表す XML ドキュメントが、基になる `IXmlRepository`から読み取られます。 これらのドキュメントが暗号化されている場合、システムは自動的に復号化します。 `XmlKeyManager` は、ドキュメントを逆シリアル化するための適切な `IAuthenticatedEncryptorDescriptorDeserializer` インスタンスを `IAuthenticatedEncryptorDescriptor` インスタンスに作成してから、個々の `IKey` インスタンスにラップします。 この `IKey` インスタンスのコレクションが呼び出し元に返されます。

特定の XML 要素の詳細については、「[キーストレージ形式」ドキュメント](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)を参照してください。

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository` インターフェイスは、バッキングストアに対して XML を永続化したり、XML を取得したりできる型を表します。 2 つの Api が公開しています。

* `GetAllElements`:`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

`IXmlRepository` の実装では、それらを渡す XML を解析する必要はありません。 上位のレイヤーを生成して、ドキュメントの解析について心配を不透明として XML ドキュメントを処理させている必要があります。

`IXmlRepository`を実装する具象型には、次の4つが組み込まれています。

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

詳細については、「[キー記憶域プロバイダー」ドキュメント](xref:security/data-protection/implementation/key-storage-providers)を参照してください。

別のバッキングストア (たとえば、Azure Table Storage) を使用する場合は、カスタム `IXmlRepository` を登録するのが適切です。

既定のリポジトリアプリケーション全体を変更するには、カスタム `IXmlRepository` インスタンスを登録します。

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a>IXmlEncryptor

`IXmlEncryptor` インターフェイスは、プレーンテキストの XML 要素を暗号化できる型を表します。 1 つの API を公開しています。

* (XElement plaintextElement) の暗号化: EncryptedXmlInfo

シリアル化された `IAuthenticatedEncryptorDescriptor` に "encryption が必要" とマークされている要素が含まれている場合、`XmlKeyManager` は、構成された `IXmlEncryptor`の `Encrypt` メソッドを使用してこれらの要素を実行します。これにより、プレーンテキスト要素ではなく、読んだり要素が `IXmlRepository`に保持されます。 `Encrypt` メソッドの出力は、`EncryptedXmlInfo` オブジェクトです。 このオブジェクトは、結果の読んだり `XElement` と、対応する要素の暗号を解除するために使用できる `IXmlDecryptor` を表す型の両方を含むラッパーです。

`IXmlEncryptor`を実装する具象型には、次の4つが組み込まれています。

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor 機能](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

詳細については、保存[時のキーの暗号化](xref:security/data-protection/implementation/key-encryption-at-rest)に関するドキュメントを参照してください。

既定のキー暗号化メカニズムのアプリケーション全体を変更するには、カスタム `IXmlEncryptor` インスタンスを登録します。

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a>IXmlDecryptor

`IXmlDecryptor` インターフェイスは、`IXmlEncryptor`によって読んだりされた `XElement` の暗号化を解除する方法を認識している型を表します。 1 つの API を公開しています。

* (XElement encryptedElement) を暗号化解除: XElement

`Decrypt` メソッドは、`IXmlEncryptor.Encrypt`によって実行される暗号化を元に戻します。 一般に、各具象 `IXmlEncryptor` 実装には、対応する具象 `IXmlDecryptor` 実装があります。

`IXmlDecryptor` を実装する型には、次の2つのパブリックコンストラクターのいずれかが必要です。

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> コンストラクターに渡される `IServiceProvider` は null でもかまいません。

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink` インターフェイスは、機密情報のエスクローを実行できる型を表します。 シリアル化された記述子に機密情報 (暗号化マテリアルなど) が含まれている可能性があることを思い出してください。これは、 [IXmlEncryptor](#ixmlencryptor)型が最初に導入されたものです。 ただし、事故が発生して、キー リングが削除できるは、または破損します。

エスクローエスケープハッチを使用すると、構成済みの[IXmlEncryptor](#ixmlencryptor)によって変換される前に、未処理のシリアル化された XML にアクセスできます。 インターフェイスは、1 つの API を公開します。

* ストア (Guid keyId、XElement 要素)

ビジネスポリシーと一貫性のあるセキュリティで保護された方法で提供される要素を処理するには、`IKeyEscrowSink` の実装が必要です。 可能な実装の1つとして、エスクローシンクが、証明書の秘密キーがエスクローされている既知の企業の x.509 証明書を使用して XML 要素を暗号化することが考えられます。`CertificateXmlEncryptor` の種類は、これを支援することができます。 `IKeyEscrowSink` 実装は、指定された要素を適切に永続化する役割も担います。

既定では、エスクローメカニズムは有効になっていませんが、サーバー管理者は[グローバルに構成](xref:security/data-protection/configuration/machine-wide-policy)することができます。 また、次のサンプルに示すように、`IDataProtectionBuilder.AddKeyEscrowSink` メソッドを使用してプログラムで構成することもできます。 `AddKeyEscrowSink` メソッドのオーバーロードでは、`IKeyEscrowSink` インスタンスはシングルトンとして使用されるため、`IServiceCollection.AddSingleton` と `IServiceCollection.AddInstance` のオーバーロードがミラー化されます。 複数の `IKeyEscrowSink` インスタンスが登録されている場合は、キーの生成時にそれぞれが呼び出されます。そのため、キーを複数のメカニズムに同時にエスクローことができます。

`IKeyEscrowSink` インスタンスからマテリアルを読み取る API はありません。 これはエスクロー メカニズムの設計理論と一致します。 キー マテリアルを信頼された証明機関にアクセスできるように意図したものと独自 escrowed マテリアルへのアクセスを必要はありません、アプリケーション自体がない信頼された証明機関であるためです。

次のサンプルコードは、"CONTOSODomain Admins" のメンバーだけが回復できるように、キーがエスクローされている `IKeyEscrowSink` を作成して登録する方法を示しています。

> [!NOTE]
> このサンプルを実行する Windows 8 のドメインに参加していることが必要]、[Windows Server 2012 マシン、およびドメイン コント ローラーが Windows Server 2012 またはそれ以降にする必要があります。

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
