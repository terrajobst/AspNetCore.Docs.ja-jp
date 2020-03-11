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
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="3d98e-103">ASP.NET Core でのキー管理の拡張性</span><span class="sxs-lookup"><span data-stu-id="3d98e-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="3d98e-104">このセクションを読む前に、「[キー管理](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)」セクションをお読みください。このセクションでは、これらの api の基本的な概念について説明します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="3d98e-105">次のインターフェイスのいずれかを実装する型がスレッド セーフにする必要があります複数の呼び出し元の。</span><span class="sxs-lookup"><span data-stu-id="3d98e-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="3d98e-106">Key</span><span class="sxs-lookup"><span data-stu-id="3d98e-106">Key</span></span>

<span data-ttu-id="3d98e-107">`IKey` インターフェイスは、cryptosystem のキーの基本的な表現です。</span><span class="sxs-lookup"><span data-stu-id="3d98e-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="3d98e-108">用語のキーは、「暗号化キー マテリアル」の文字どおりの意味ではなく、抽象という意味では、ここに使用されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="3d98e-109">キーには、次のプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="3d98e-109">A key has the following properties:</span></span>

* <span data-ttu-id="3d98e-110">アクティブ化、作成、および有効期限の日付</span><span class="sxs-lookup"><span data-stu-id="3d98e-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="3d98e-111">失効状態</span><span class="sxs-lookup"><span data-stu-id="3d98e-111">Revocation status</span></span>

* <span data-ttu-id="3d98e-112">キー識別子 (GUID)</span><span class="sxs-lookup"><span data-stu-id="3d98e-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="3d98e-113">さらに、`IKey` は、このキーに関連付けられている[I認証 Ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成するために使用できる `CreateEncryptor` メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3d98e-114">さらに、`IKey` は、このキーに関連付けられている[I認証 Ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成するために使用できる `CreateEncryptorInstance` メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3d98e-115">`IKey` インスタンスから未加工の暗号化マテリアルを取得する API はありません。</span><span class="sxs-lookup"><span data-stu-id="3d98e-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="3d98e-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="3d98e-116">IKeyManager</span></span>

<span data-ttu-id="3d98e-117">`IKeyManager` インターフェイスは、一般的なキーの格納、取得、および操作を行うオブジェクトを表します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="3d98e-118">次の 3 つの高度な操作が公開しています。</span><span class="sxs-lookup"><span data-stu-id="3d98e-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="3d98e-119">新しいキーを作成し、ストレージに保存します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="3d98e-120">記憶域からすべてのキーを取得します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-120">Get all keys from storage.</span></span>

* <span data-ttu-id="3d98e-121">1 つまたは複数のキーを取り消すし、記憶域に失効情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="3d98e-122">`IKeyManager` の作成は非常に高度なタスクであり、ほとんどの開発者はこれを試みることはできません。</span><span class="sxs-lookup"><span data-stu-id="3d98e-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="3d98e-123">代わりに、ほとんどの開発者は、 [Xmlkeymanager](#xmlkeymanager)クラスによって提供される機能を活用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3d98e-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="3d98e-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="3d98e-124">XmlKeyManager</span></span>

<span data-ttu-id="3d98e-125">`XmlKeyManager` 型は、`IKeyManager`のインボックスの具象実装です。</span><span class="sxs-lookup"><span data-stu-id="3d98e-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="3d98e-126">キー エスクローおよび保存時のキーの暗号化を含むいくつかの便利な機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="3d98e-127">このシステムのキーは、XML 要素 (具体的には[XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) として表されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="3d98e-128">`XmlKeyManager` は、タスクを実行する過程で、他のいくつかのコンポーネントに依存します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="3d98e-129">`AlgorithmConfiguration`、新しいキーによって使用されるアルゴリズムを指定します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="3d98e-130">`IXmlRepository`は、ストレージ内でのキーの保存場所を制御します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="3d98e-131">`IXmlEncryptor` [省略可能]。保存時にキーを暗号化できます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="3d98e-132">`IKeyEscrowSink` [省略可能]。キーエスクローサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="3d98e-133">`IXmlRepository`は、ストレージ内でのキーの保存場所を制御します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="3d98e-134">`IXmlEncryptor` [省略可能]。保存時にキーを暗号化できます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="3d98e-135">`IKeyEscrowSink` [省略可能]。キーエスクローサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="3d98e-136">これらのコンポーネントが `XmlKeyManager`内でどのように結線されるかを示す概略図を次に示します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![キーの作成](key-management/_static/keycreation2.png)

<span data-ttu-id="3d98e-138">*キーの作成/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="3d98e-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="3d98e-139">`CreateNewKey`の実装では、`AlgorithmConfiguration` コンポーネントを使用して一意の `IAuthenticatedEncryptorDescriptor`が作成され、その後 XML としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="3d98e-140">キー エスクロー シンクが存在する場合は、長期的なストレージ、未処理の XML を (暗号化されていない) が、シンクに提供されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="3d98e-141">暗号化されていない XML は、(必要に応じて) `IXmlEncryptor` を通じて実行され、暗号化された XML ドキュメントを生成します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="3d98e-142">この暗号化されたドキュメントは、`IXmlRepository`を使用して長期的なストレージに保存されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="3d98e-143">(`IXmlEncryptor` が構成されていない場合は、暗号化されていないドキュメントが `IXmlRepository`に保持されます)。</span><span class="sxs-lookup"><span data-stu-id="3d98e-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![キーの取得](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![キーの作成](key-management/_static/keycreation1.png)

<span data-ttu-id="3d98e-146">*キーの作成/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="3d98e-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="3d98e-147">`CreateNewKey`の実装では、`IAuthenticatedEncryptorConfiguration` コンポーネントを使用して一意の `IAuthenticatedEncryptorDescriptor`が作成され、その後 XML としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="3d98e-148">キー エスクロー シンクが存在する場合は、長期的なストレージ、未処理の XML を (暗号化されていない) が、シンクに提供されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="3d98e-149">暗号化されていない XML は、(必要に応じて) `IXmlEncryptor` を通じて実行され、暗号化された XML ドキュメントを生成します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="3d98e-150">この暗号化されたドキュメントは、`IXmlRepository`を使用して長期的なストレージに保存されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="3d98e-151">(`IXmlEncryptor` が構成されていない場合は、暗号化されていないドキュメントが `IXmlRepository`に保持されます)。</span><span class="sxs-lookup"><span data-stu-id="3d98e-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![キーの取得](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="3d98e-153">*キーの取得/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="3d98e-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="3d98e-154">`GetAllKeys`の実装では、キーと失効を表す XML ドキュメントが、基になる `IXmlRepository`から読み取られます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="3d98e-155">これらのドキュメントが暗号化されている場合、システムは自動的に復号化します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="3d98e-156">`XmlKeyManager` は、ドキュメントを逆シリアル化するための適切な `IAuthenticatedEncryptorDescriptorDeserializer` インスタンスを `IAuthenticatedEncryptorDescriptor` インスタンスに作成してから、個々の `IKey` インスタンスにラップします。</span><span class="sxs-lookup"><span data-stu-id="3d98e-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="3d98e-157">この `IKey` インスタンスのコレクションが呼び出し元に返されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="3d98e-158">特定の XML 要素の詳細については、「[キーストレージ形式」ドキュメント](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3d98e-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="3d98e-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="3d98e-159">IXmlRepository</span></span>

<span data-ttu-id="3d98e-160">`IXmlRepository` インターフェイスは、バッキングストアに対して XML を永続化したり、XML を取得したりできる型を表します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="3d98e-161">2 つの Api が公開しています。</span><span class="sxs-lookup"><span data-stu-id="3d98e-161">It exposes two APIs:</span></span>

* <span data-ttu-id="3d98e-162">`GetAllElements`:`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="3d98e-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="3d98e-163">`IXmlRepository` の実装では、それらを渡す XML を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="3d98e-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="3d98e-164">上位のレイヤーを生成して、ドキュメントの解析について心配を不透明として XML ドキュメントを処理させている必要があります。</span><span class="sxs-lookup"><span data-stu-id="3d98e-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="3d98e-165">`IXmlRepository`を実装する具象型には、次の4つが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="3d98e-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="3d98e-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="3d98e-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="3d98e-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="3d98e-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="3d98e-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="3d98e-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="3d98e-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="3d98e-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="3d98e-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="3d98e-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="3d98e-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="3d98e-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="3d98e-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="3d98e-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="3d98e-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="3d98e-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="3d98e-174">詳細については、「[キー記憶域プロバイダー」ドキュメント](xref:security/data-protection/implementation/key-storage-providers)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3d98e-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="3d98e-175">別のバッキングストア (たとえば、Azure Table Storage) を使用する場合は、カスタム `IXmlRepository` を登録するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="3d98e-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="3d98e-176">既定のリポジトリアプリケーション全体を変更するには、カスタム `IXmlRepository` インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="3d98e-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="3d98e-177">IXmlEncryptor</span></span>

<span data-ttu-id="3d98e-178">`IXmlEncryptor` インターフェイスは、プレーンテキストの XML 要素を暗号化できる型を表します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="3d98e-179">1 つの API を公開しています。</span><span class="sxs-lookup"><span data-stu-id="3d98e-179">It exposes a single API:</span></span>

* <span data-ttu-id="3d98e-180">(XElement plaintextElement) の暗号化: EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="3d98e-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="3d98e-181">シリアル化された `IAuthenticatedEncryptorDescriptor` に "encryption が必要" とマークされている要素が含まれている場合、`XmlKeyManager` は、構成された `IXmlEncryptor`の `Encrypt` メソッドを使用してこれらの要素を実行します。これにより、プレーンテキスト要素ではなく、読んだり要素が `IXmlRepository`に保持されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="3d98e-182">`Encrypt` メソッドの出力は、`EncryptedXmlInfo` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="3d98e-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="3d98e-183">このオブジェクトは、結果の読んだり `XElement` と、対応する要素の暗号を解除するために使用できる `IXmlDecryptor` を表す型の両方を含むラッパーです。</span><span class="sxs-lookup"><span data-stu-id="3d98e-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="3d98e-184">`IXmlEncryptor`を実装する具象型には、次の4つが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="3d98e-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="3d98e-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="3d98e-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="3d98e-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="3d98e-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="3d98e-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="3d98e-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="3d98e-188">NullXmlEncryptor 機能</span><span class="sxs-lookup"><span data-stu-id="3d98e-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="3d98e-189">詳細については、保存[時のキーの暗号化](xref:security/data-protection/implementation/key-encryption-at-rest)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3d98e-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="3d98e-190">既定のキー暗号化メカニズムのアプリケーション全体を変更するには、カスタム `IXmlEncryptor` インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="3d98e-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="3d98e-191">IXmlDecryptor</span></span>

<span data-ttu-id="3d98e-192">`IXmlDecryptor` インターフェイスは、`IXmlEncryptor`によって読んだりされた `XElement` の暗号化を解除する方法を認識している型を表します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="3d98e-193">1 つの API を公開しています。</span><span class="sxs-lookup"><span data-stu-id="3d98e-193">It exposes a single API:</span></span>

* <span data-ttu-id="3d98e-194">(XElement encryptedElement) を暗号化解除: XElement</span><span class="sxs-lookup"><span data-stu-id="3d98e-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="3d98e-195">`Decrypt` メソッドは、`IXmlEncryptor.Encrypt`によって実行される暗号化を元に戻します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="3d98e-196">一般に、各具象 `IXmlEncryptor` 実装には、対応する具象 `IXmlDecryptor` 実装があります。</span><span class="sxs-lookup"><span data-stu-id="3d98e-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="3d98e-197">`IXmlDecryptor` を実装する型には、次の2つのパブリックコンストラクターのいずれかが必要です。</span><span class="sxs-lookup"><span data-stu-id="3d98e-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="3d98e-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="3d98e-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="3d98e-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="3d98e-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="3d98e-200">コンストラクターに渡される `IServiceProvider` は null でもかまいません。</span><span class="sxs-lookup"><span data-stu-id="3d98e-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="3d98e-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="3d98e-201">IKeyEscrowSink</span></span>

<span data-ttu-id="3d98e-202">`IKeyEscrowSink` インターフェイスは、機密情報のエスクローを実行できる型を表します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="3d98e-203">シリアル化された記述子に機密情報 (暗号化マテリアルなど) が含まれている可能性があることを思い出してください。これは、 [IXmlEncryptor](#ixmlencryptor)型が最初に導入されたものです。</span><span class="sxs-lookup"><span data-stu-id="3d98e-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="3d98e-204">ただし、事故が発生して、キー リングが削除できるは、または破損します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="3d98e-205">エスクローエスケープハッチを使用すると、構成済みの[IXmlEncryptor](#ixmlencryptor)によって変換される前に、未処理のシリアル化された XML にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="3d98e-206">インターフェイスは、1 つの API を公開します。</span><span class="sxs-lookup"><span data-stu-id="3d98e-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="3d98e-207">ストア (Guid keyId、XElement 要素)</span><span class="sxs-lookup"><span data-stu-id="3d98e-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="3d98e-208">ビジネスポリシーと一貫性のあるセキュリティで保護された方法で提供される要素を処理するには、`IKeyEscrowSink` の実装が必要です。</span><span class="sxs-lookup"><span data-stu-id="3d98e-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="3d98e-209">可能な実装の1つとして、エスクローシンクが、証明書の秘密キーがエスクローされている既知の企業の x.509 証明書を使用して XML 要素を暗号化することが考えられます。`CertificateXmlEncryptor` の種類は、これを支援することができます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="3d98e-210">`IKeyEscrowSink` 実装は、指定された要素を適切に永続化する役割も担います。</span><span class="sxs-lookup"><span data-stu-id="3d98e-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="3d98e-211">既定では、エスクローメカニズムは有効になっていませんが、サーバー管理者は[グローバルに構成](xref:security/data-protection/configuration/machine-wide-policy)することができます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="3d98e-212">また、次のサンプルに示すように、`IDataProtectionBuilder.AddKeyEscrowSink` メソッドを使用してプログラムで構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="3d98e-213">`AddKeyEscrowSink` メソッドのオーバーロードでは、`IKeyEscrowSink` インスタンスはシングルトンとして使用されるため、`IServiceCollection.AddSingleton` と `IServiceCollection.AddInstance` のオーバーロードがミラー化されます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="3d98e-214">複数の `IKeyEscrowSink` インスタンスが登録されている場合は、キーの生成時にそれぞれが呼び出されます。そのため、キーを複数のメカニズムに同時にエスクローことができます。</span><span class="sxs-lookup"><span data-stu-id="3d98e-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="3d98e-215">`IKeyEscrowSink` インスタンスからマテリアルを読み取る API はありません。</span><span class="sxs-lookup"><span data-stu-id="3d98e-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="3d98e-216">これはエスクロー メカニズムの設計理論と一致します。 キー マテリアルを信頼された証明機関にアクセスできるように意図したものと独自 escrowed マテリアルへのアクセスを必要はありません、アプリケーション自体がない信頼された証明機関であるためです。</span><span class="sxs-lookup"><span data-stu-id="3d98e-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="3d98e-217">次のサンプルコードは、"CONTOSODomain Admins" のメンバーだけが回復できるように、キーがエスクローされている `IKeyEscrowSink` を作成して登録する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3d98e-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="3d98e-218">このサンプルを実行する Windows 8 のドメインに参加していることが必要]、[Windows Server 2012 マシン、およびドメイン コント ローラーが Windows Server 2012 またはそれ以降にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="3d98e-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
