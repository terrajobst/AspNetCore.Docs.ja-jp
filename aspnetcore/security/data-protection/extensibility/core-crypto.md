---
title: ASP.NET Core での主要な暗号化の拡張性
author: rick-anderson
description: IAuthenticatedEncryptor、IAuthenticatedEncryptorDescriptor、IAuthenticatedEncryptorDescriptorDeserializer、および最上位レベルのファクトリについて説明します。
ms.author: riande
ms.date: 08/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: a5f651e3313cc579b995b45905826a5bffcc241c
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814701"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="60d03-103">ASP.NET Core での主要な暗号化の拡張性</span><span class="sxs-lookup"><span data-stu-id="60d03-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="60d03-104">次のインターフェイスのいずれかを実装する型がスレッド セーフにする必要があります複数の呼び出し元の。</span><span class="sxs-lookup"><span data-stu-id="60d03-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="60d03-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="60d03-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="60d03-106">**IAuthenticatedEncryptor**インターフェイスは、暗号化サブシステムの基本的なビルディング ブロックです。</span><span class="sxs-lookup"><span data-stu-id="60d03-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="60d03-107">通常は、1 つのキーの 1 つの IAuthenticatedEncryptor と IAuthenticatedEncryptor インスタンスがすべての暗号化キー マテリアルと暗号化操作の実行に必要なアルゴリズムの情報をラップします。</span><span class="sxs-lookup"><span data-stu-id="60d03-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="60d03-108">その名前からわかるように、型は、認証済みの暗号化と復号化サービスを提供する責任を負います。</span><span class="sxs-lookup"><span data-stu-id="60d03-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="60d03-109">これは、次の 2 つの Api を公開します。</span><span class="sxs-lookup"><span data-stu-id="60d03-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="60d03-110">Encrypt メソッドでは、したり、プレーン テキストと、認証タグが含まれる blob を返します。</span><span class="sxs-lookup"><span data-stu-id="60d03-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="60d03-111">認証タグは、AAD 自体は最終的なペイロードから回復可能な必要はありませんが、追加の認証済みデータ (AAD) を網羅する必要があります。</span><span class="sxs-lookup"><span data-stu-id="60d03-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="60d03-112">復号化メソッドは、認証タグを検証し、解読はペイロードを返します。</span><span class="sxs-lookup"><span data-stu-id="60d03-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="60d03-113">すべてのエラー (ArgumentNullException 以外と似ています) は、CryptographicException に homogenized する必要があります。</span><span class="sxs-lookup"><span data-stu-id="60d03-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="60d03-114">IAuthenticatedEncryptor インスタンス自体実際には、キー マテリアルを格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="60d03-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="60d03-115">など、実装は、すべての操作の HSM に委任でした。</span><span class="sxs-lookup"><span data-stu-id="60d03-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="60d03-116">IAuthenticatedEncryptor を作成する方法</span><span class="sxs-lookup"><span data-stu-id="60d03-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="60d03-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="60d03-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="60d03-118">**IAuthenticatedEncryptorFactory**インターフェイスが作成する方法を認識している型を表す、 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンス。</span><span class="sxs-lookup"><span data-stu-id="60d03-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="60d03-119">その API は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="60d03-119">Its API is as follows.</span></span>

* <span data-ttu-id="60d03-120">CreateEncryptorInstance (IKey キー)。IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="60d03-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="60d03-121">それと同等と見なす必要があります、CreateEncryptorInstance メソッドによって作成された任意の認証済み受け付け IKey の特定のインスタンスの、以下のコード サンプル。</span><span class="sxs-lookup"><span data-stu-id="60d03-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="60d03-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="60d03-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="60d03-123">**IAuthenticatedEncryptorDescriptor**インターフェイスが作成する方法を認識している型を表す、 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンス。</span><span class="sxs-lookup"><span data-stu-id="60d03-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="60d03-124">その API は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="60d03-124">Its API is as follows.</span></span>

* <span data-ttu-id="60d03-125">CreateEncryptorInstance() :IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="60d03-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="60d03-126">ExportToXml() :XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="60d03-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="60d03-127">IAuthenticatedEncryptor のような IAuthenticatedEncryptorDescriptor のインスタンスが 1 つの特定のキーをラップすると見なされます。</span><span class="sxs-lookup"><span data-stu-id="60d03-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="60d03-128">つまり、IAuthenticatedEncryptorDescriptor の特定のインスタンスの CreateEncryptorInstance メソッドによって作成された任意の認証済み受け付けする必要がありますと見なされるようにそれと同等の以下のコード サンプル。</span><span class="sxs-lookup"><span data-stu-id="60d03-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="60d03-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x のみ)</span><span class="sxs-lookup"><span data-stu-id="60d03-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="60d03-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="60d03-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="60d03-131">**IAuthenticatedEncryptorDescriptor**インターフェイス自体を XML にエクスポートする方法を認識している型を表します。</span><span class="sxs-lookup"><span data-stu-id="60d03-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="60d03-132">その API は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="60d03-132">Its API is as follows.</span></span>

* <span data-ttu-id="60d03-133">ExportToXml() :XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="60d03-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="60d03-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="60d03-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="60d03-135">XML シリアル化</span><span class="sxs-lookup"><span data-stu-id="60d03-135">XML Serialization</span></span>

<span data-ttu-id="60d03-136">IAuthenticatedEncryptor と IAuthenticatedEncryptorDescriptor の主な違いは、記述子が暗号化機能を作成し、有効な引数を指定する方法を知っていることです。</span><span class="sxs-lookup"><span data-stu-id="60d03-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="60d03-137">実装は SymmetricAlgorithm と KeyedHashAlgorithm を IAuthenticatedEncryptor を検討してください。</span><span class="sxs-lookup"><span data-stu-id="60d03-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="60d03-138">暗号化機能のジョブは、これらの型を使用するが、必ずしもを認識していないから、これらの型が付属していたため、アプリケーションが再起動した場合、それ自体を再作成する方法の適切な説明を実際に書き込むことはできません。</span><span class="sxs-lookup"><span data-stu-id="60d03-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="60d03-139">記述子は、これより高いレベルとして機能します。</span><span class="sxs-lookup"><span data-stu-id="60d03-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="60d03-140">記述子は、暗号化機能のインスタンスを作成する方法を知っているため (たとえば、方法を確認する必要なアルゴリズムを作成する)、アプリケーションをリセットした後、暗号化機能のインスタンスが再作成できるように XML 形式では、その知識をシリアル化ができます。</span><span class="sxs-lookup"><span data-stu-id="60d03-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="60d03-141">記述子は、その ExportToXml ルーチンを使用してシリアル化することができます。</span><span class="sxs-lookup"><span data-stu-id="60d03-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="60d03-142">このルーチンは、2 つのプロパティを含む、XmlSerializedDescriptorInfo を返します記述子の型を表す XElement 表現、 [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer)できます。この記述子の指定、対応する XElement を再生するために使用します。</span><span class="sxs-lookup"><span data-stu-id="60d03-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="60d03-143">シリアル化された記述子は、暗号化キー マテリアルなどの機密情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="60d03-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="60d03-144">データ保護システムがストレージに永続化される前に、情報を暗号化するための組み込みサポートしています。</span><span class="sxs-lookup"><span data-stu-id="60d03-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="60d03-145">これを活用する記述子が機微な情報で属性名"requiresEncryption"を含む要素をマークします。 (xmlns"<http://schemas.asp.net/2015/03/dataProtection>")、"true"の値。</span><span class="sxs-lookup"><span data-stu-id="60d03-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="60d03-146">この属性を設定するためのヘルパー API があります。</span><span class="sxs-lookup"><span data-stu-id="60d03-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="60d03-147">XElement.MarkAsRequiresEncryption() Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel を名前空間にある拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="60d03-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="60d03-148">シリアル化された記述子が機密情報を格納しない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="60d03-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="60d03-149">HSM に格納されている暗号化キーの大文字と小文字をもう一度考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="60d03-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="60d03-150">記述子は、HSM がプレーン テキスト形式の内容を公開しないため自体をシリアル化するとき、キー マテリアルを書き込むことはできません。</span><span class="sxs-lookup"><span data-stu-id="60d03-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="60d03-151">代わりに、記述子 (HSM では、この方法でエクスポートが可能) の場合、キーまたはキーを HSM の一意の識別子のキーでラップされたバージョンが書き込まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="60d03-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="60d03-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="60d03-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="60d03-153">**IAuthenticatedEncryptorDescriptorDeserializer**インターフェイスは、XElement から IAuthenticatedEncryptorDescriptor インスタンスを逆シリアル化する方法を認識する型を表します。</span><span class="sxs-lookup"><span data-stu-id="60d03-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="60d03-154">1 つのメソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="60d03-154">It exposes a single method:</span></span>

* <span data-ttu-id="60d03-155">ImportFromXml (XElement 要素)。IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="60d03-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="60d03-156">ImportFromXml メソッドには、返された XElement [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml)元 IAuthenticatedEncryptorDescriptor に相当するを作成します。</span><span class="sxs-lookup"><span data-stu-id="60d03-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="60d03-157">IAuthenticatedEncryptorDescriptorDeserializer を実装する型は、次の 2 つのパブリック コンス トラクターのいずれかが必要です。</span><span class="sxs-lookup"><span data-stu-id="60d03-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="60d03-158">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="60d03-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="60d03-159">.ctor()</span><span class="sxs-lookup"><span data-stu-id="60d03-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="60d03-160">コンス トラクターに渡される IServiceProvider を null にすることがあります。</span><span class="sxs-lookup"><span data-stu-id="60d03-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="60d03-161">最上位レベルのファクトリ</span><span class="sxs-lookup"><span data-stu-id="60d03-161">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="60d03-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="60d03-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="60d03-163">**AlgorithmConfiguration**クラスを作成する方法を認識している型を表す[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンス。</span><span class="sxs-lookup"><span data-stu-id="60d03-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="60d03-164">これは、1 つの API を公開します。</span><span class="sxs-lookup"><span data-stu-id="60d03-164">It exposes a single API.</span></span>

* <span data-ttu-id="60d03-165">CreateNewDescriptor() :IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="60d03-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="60d03-166">AlgorithmConfiguration の最上位レベルのファクトリとして考えます。</span><span class="sxs-lookup"><span data-stu-id="60d03-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="60d03-167">構成は、テンプレートとして機能します。</span><span class="sxs-lookup"><span data-stu-id="60d03-167">The configuration serves as a template.</span></span> <span data-ttu-id="60d03-168">アルゴリズムの情報をラップします (例: この構成は AES 128-GCM のマスター _ キーで記述子を作成します)、特定のキーに関連付けされていないが、します。</span><span class="sxs-lookup"><span data-stu-id="60d03-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="60d03-169">CreateNewDescriptor が呼び出され、この呼び出しでは、専用の最新のキー マテリアルを作成し、新しい IAuthenticatedEncryptorDescriptor が生成されるときに、このキー マテリアルとアルゴリズムにマテリアルを使用するために必要な情報をラップします。</span><span class="sxs-lookup"><span data-stu-id="60d03-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="60d03-170">キー マテリアルでしたするソフトウェアで作成 (メモリに保持されている) を作成し、HSM および具合内で保持されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="60d03-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="60d03-171">重要なポイントは、CreateNewDescriptor への 2 つの呼び出しでは同等 IAuthenticatedEncryptorDescriptor インスタンスは作成しません。</span><span class="sxs-lookup"><span data-stu-id="60d03-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="60d03-172">など、AlgorithmConfiguration 型をキーの作成のルーチンのエントリ ポイントとして機能[自動のキーをローリング](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)します。</span><span class="sxs-lookup"><span data-stu-id="60d03-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="60d03-173">将来のすべてのキーの実装を変更するには、KeyManagementOptions で AuthenticatedEncryptorConfiguration プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="60d03-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="60d03-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="60d03-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="60d03-175">**IAuthenticatedEncryptorConfiguration**インターフェイスが作成する方法を認識している型を表す[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンス。</span><span class="sxs-lookup"><span data-stu-id="60d03-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="60d03-176">これは、1 つの API を公開します。</span><span class="sxs-lookup"><span data-stu-id="60d03-176">It exposes a single API.</span></span>

* <span data-ttu-id="60d03-177">CreateNewDescriptor() :IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="60d03-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="60d03-178">IAuthenticatedEncryptorConfiguration の最上位レベルのファクトリとして考えます。</span><span class="sxs-lookup"><span data-stu-id="60d03-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="60d03-179">構成は、テンプレートとして機能します。</span><span class="sxs-lookup"><span data-stu-id="60d03-179">The configuration serves as a template.</span></span> <span data-ttu-id="60d03-180">アルゴリズムの情報をラップします (例: この構成は AES 128-GCM のマスター _ キーで記述子を作成します)、特定のキーに関連付けされていないが、します。</span><span class="sxs-lookup"><span data-stu-id="60d03-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="60d03-181">CreateNewDescriptor が呼び出され、この呼び出しでは、専用の最新のキー マテリアルを作成し、新しい IAuthenticatedEncryptorDescriptor が生成されるときに、このキー マテリアルとアルゴリズムにマテリアルを使用するために必要な情報をラップします。</span><span class="sxs-lookup"><span data-stu-id="60d03-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="60d03-182">キー マテリアルでしたするソフトウェアで作成 (メモリに保持されている) を作成し、HSM および具合内で保持されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="60d03-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="60d03-183">重要なポイントは、CreateNewDescriptor への 2 つの呼び出しでは同等 IAuthenticatedEncryptorDescriptor インスタンスは作成しません。</span><span class="sxs-lookup"><span data-stu-id="60d03-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="60d03-184">など、IAuthenticatedEncryptorConfiguration 型をキーの作成のルーチンのエントリ ポイントとして機能[自動のキーをローリング](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)します。</span><span class="sxs-lookup"><span data-stu-id="60d03-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="60d03-185">将来のすべてのキーの実装を変更するには、サービス コンテナーにシングルトン IAuthenticatedEncryptorConfiguration を登録します。</span><span class="sxs-lookup"><span data-stu-id="60d03-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
