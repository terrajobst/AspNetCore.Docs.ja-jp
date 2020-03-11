---
title: ASP.NET Core でのコア暗号化の拡張性
author: rick-anderson
description: I認証 Ated暗号化機能、I認証 ated、Tordescriptor、I認証 Ated暗号化、およびトップレベルファクトリについて説明します。
ms.author: riande
ms.date: 08/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: a5f651e3313cc579b995b45905826a5bffcc241c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653546"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="e2113-103">ASP.NET Core でのコア暗号化の拡張性</span><span class="sxs-lookup"><span data-stu-id="e2113-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="e2113-104">次のインターフェイスのいずれかを実装する型がスレッド セーフにする必要があります複数の呼び出し元の。</span><span class="sxs-lookup"><span data-stu-id="e2113-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="e2113-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="e2113-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="e2113-106">**Icryptographic atedcryptographic**インターフェイスは、暗号化サブシステムの基本的な構成要素です。</span><span class="sxs-lookup"><span data-stu-id="e2113-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="e2113-107">一般に、キーごとに1つの icryptographic Atedcryptographic が存在し、Icryptographic Atedcryptographic インスタンスは暗号化操作を実行するために必要なすべての暗号化キーマテリアルとアルゴリズム情報をラップします。</span><span class="sxs-lookup"><span data-stu-id="e2113-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="e2113-108">その名前が示すように、型は認証された暗号化および復号化サービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="e2113-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="e2113-109">次の2つの Api を公開します。</span><span class="sxs-lookup"><span data-stu-id="e2113-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="e2113-110">Encrypt メソッドは、読んだりプレーンテキストと認証タグを含む blob を返します。</span><span class="sxs-lookup"><span data-stu-id="e2113-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="e2113-111">認証タグは、追加の認証データ (AAD) を含んでいる必要がありますが、AAD 自体は最終的なペイロードから回復できない必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2113-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="e2113-112">暗号化解除メソッドは認証タグを検証し、解読されたペイロードを返します。</span><span class="sxs-lookup"><span data-stu-id="e2113-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="e2113-113">すべてのエラー (System.argumentnullexception と類似点を除く) は、System.security.cryptography.cryptographicexception> に homogenized する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2113-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="e2113-114">I認証 Ated暗号化インスタンス自体には、実際にキーマテリアルが含まれている必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e2113-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="e2113-115">たとえば、実装は、すべての操作について HSM に委任できます。</span><span class="sxs-lookup"><span data-stu-id="e2113-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="e2113-116">I認証の暗号化機能を作成する方法</span><span class="sxs-lookup"><span data-stu-id="e2113-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="e2113-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e2113-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e2113-118">**I認証 Ated暗号化 Torfactory**インターフェイスは、 [i認証 ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成する方法を認識する型を表します。</span><span class="sxs-lookup"><span data-stu-id="e2113-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="e2113-119">その API は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e2113-119">Its API is as follows.</span></span>

* <span data-ttu-id="e2113-120">Create暗号化 Torinstance (IKey key): Ikey Ated暗号化機能</span><span class="sxs-lookup"><span data-stu-id="e2113-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="e2113-121">次のコードサンプルのように、特定の IKey インスタンスに対して、Create暗号化 Torinstance メソッドによって作成された認証済みの暗号化は、同等と見なされる必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2113-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="e2113-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e2113-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e2113-123">**I認証 atedの Tordescriptor**インターフェイスは、 [i認証 ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成する方法を認識する型を表します。</span><span class="sxs-lookup"><span data-stu-id="e2113-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="e2113-124">その API は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e2113-124">Its API is as follows.</span></span>

* <span data-ttu-id="e2113-125">Create暗号化 Torinstance (): I認証 Ated暗号化機能</span><span class="sxs-lookup"><span data-stu-id="e2113-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="e2113-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="e2113-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="e2113-127">I認証 Ated暗号化機能と同様に、I認証 Atedatedtordescriptor のインスタンスは、特定のキーをラップすることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="e2113-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="e2113-128">つまり、次のコードサンプルのように、任意の Iauthenticated Atedのインスタンスメソッドによって作成された認証済み暗号化は同等と見なされる必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2113-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="e2113-129">I認証 Atedの Tordescriptor (ASP.NET Core 2.x のみ)</span><span class="sxs-lookup"><span data-stu-id="e2113-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="e2113-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e2113-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e2113-131">**I認証 Atedの Tordescriptor**インターフェイスは、それ自体を XML にエクスポートする方法を認識している型を表します。</span><span class="sxs-lookup"><span data-stu-id="e2113-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="e2113-132">その API は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e2113-132">Its API is as follows.</span></span>

* <span data-ttu-id="e2113-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="e2113-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="e2113-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e2113-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="e2113-135">XML シリアル化</span><span class="sxs-lookup"><span data-stu-id="e2113-135">XML Serialization</span></span>

<span data-ttu-id="e2113-136">I認証 Atedの暗号化機能と I認証機能の主な違いは、記述子が暗号化を作成して有効な引数で指定する方法を認識していることです。</span><span class="sxs-lookup"><span data-stu-id="e2113-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="e2113-137">実装が SymmetricAlgorithm と KeyedHashAlgorithm に依存している I認証の暗号化について考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="e2113-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="e2113-138">暗号化機能は、これらの型を使用することを意味しますが、これらの型がどこから取得されたのかは必ずしも把握していないので、アプリケーションを再起動した場合に自身を再作成する方法について、適切な説明を記述することはできません。</span><span class="sxs-lookup"><span data-stu-id="e2113-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="e2113-139">記述子は、この上で上位のレベルとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e2113-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="e2113-140">この記述子は、暗号化機能のインスタンスを作成する方法 (たとえば、必要なアルゴリズムを作成する方法を知っている) を認識しているので、アプリケーションのリセット後に暗号化機能のインスタンスを再作成できるように、その情報を XML 形式でシリアル化することができます。</span><span class="sxs-lookup"><span data-stu-id="e2113-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="e2113-141">記述子は、ExportToXml ルーチンを使用してシリアル化できます。</span><span class="sxs-lookup"><span data-stu-id="e2113-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="e2113-142">このルーチンは、2つのプロパティを含む XmlSerializedDescriptorInfo を返します。これは、記述子の XElement 表現と、対応する XElement でこの記述子をやり直すするために使用できる[I認証 Ated Tordescriptor デシリアライザー](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer)を表す型です。</span><span class="sxs-lookup"><span data-stu-id="e2113-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="e2113-143">シリアル化された記述子には、暗号化キーマテリアルなどの機密情報が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="e2113-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="e2113-144">データ保護システムには、ストレージに保存する前に、情報を暗号化するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="e2113-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="e2113-145">これを活用するために、記述子は、属性名が "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>")、値が "true" である機密情報を含む要素をマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2113-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="e2113-146">この属性を設定するためのヘルパー API があります。</span><span class="sxs-lookup"><span data-stu-id="e2113-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="e2113-147">MarkAsRequiresEncryption () という名前空間にある XElement () を呼び出します。このメソッドは、AspNetCore です。</span><span class="sxs-lookup"><span data-stu-id="e2113-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="e2113-148">シリアル化された記述子に機密情報が含まれていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="e2113-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="e2113-149">HSM に暗号化キーが格納されている場合は、もう一度考慮してください。</span><span class="sxs-lookup"><span data-stu-id="e2113-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="e2113-150">HSM はプレーンテキスト形式でマテリアルを公開しないため、シリアル化時にキーマテリアルを書き込むことはできません。</span><span class="sxs-lookup"><span data-stu-id="e2113-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="e2113-151">代わりに、記述子はキーのラップされたバージョン (HSM がこの方法でエクスポートを許可している場合)、またはキーに固有の一意の識別子を書き込む場合があります。</span><span class="sxs-lookup"><span data-stu-id="e2113-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="e2113-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="e2113-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="e2113-153">**IXElement Ated暗号化 Tordescriptor デシリアライザー**インターフェイスは、I認証 Ated暗号化 tordescriptor インスタンスをから逆シリアル化する方法を認識する型を表します。</span><span class="sxs-lookup"><span data-stu-id="e2113-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="e2113-154">1つのメソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="e2113-154">It exposes a single method:</span></span>

* <span data-ttu-id="e2113-155">ImportFromXml (XElement 要素): i Ated暗号化 Tordescriptor</span><span class="sxs-lookup"><span data-stu-id="e2113-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="e2113-156">ImportFromXml メソッドは、 [ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml)によって返された XElement を受け取り、元の i ated暗号化された tordescriptor と同等のものを作成します。</span><span class="sxs-lookup"><span data-stu-id="e2113-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="e2113-157">I認証を実装する型は、次の2つのパブリックコンストラクターのいずれかを持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2113-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="e2113-158">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="e2113-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="e2113-159">.ctor()</span><span class="sxs-lookup"><span data-stu-id="e2113-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="e2113-160">コンストラクターに渡される IServiceProvider は null でもかまいません。</span><span class="sxs-lookup"><span data-stu-id="e2113-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="e2113-161">トップレベルのファクトリ</span><span class="sxs-lookup"><span data-stu-id="e2113-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="e2113-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e2113-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e2113-163">**AlgorithmConfiguration**クラスは、 [i Ated暗号化 tordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンスを作成する方法を認識する型を表します。</span><span class="sxs-lookup"><span data-stu-id="e2113-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="e2113-164">1つの API を公開します。</span><span class="sxs-lookup"><span data-stu-id="e2113-164">It exposes a single API.</span></span>

* <span data-ttu-id="e2113-165">CreateNewDescriptor (): I認証 Ated暗号化 Tordescriptor</span><span class="sxs-lookup"><span data-stu-id="e2113-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="e2113-166">AlgorithmConfiguration はトップレベルファクトリと考えることができます。</span><span class="sxs-lookup"><span data-stu-id="e2113-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="e2113-167">構成はテンプレートとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e2113-167">The configuration serves as a template.</span></span> <span data-ttu-id="e2113-168">アルゴリズム情報をラップします (たとえば、この構成では、AES-128-GCM マスターキーを使用して記述子が生成されます) が、特定のキーにはまだ関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="e2113-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="e2113-169">CreateNewDescriptor が呼び出されると、この呼び出しに対してのみ新しいキーマテリアルが作成され、このキーマテリアルをラップする新しい i Ated Tordescriptor が生成され、マテリアルを使用するために必要なアルゴリズム情報が生成されます。</span><span class="sxs-lookup"><span data-stu-id="e2113-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="e2113-170">キーマテリアルはソフトウェアで作成でき (また、メモリ内に保持されます)、HSM 内で作成して保持することができます。</span><span class="sxs-lookup"><span data-stu-id="e2113-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="e2113-171">重要な点は、CreateNewDescriptor への2回の呼び出しでは、同等の I認証 Ated Tordescriptor インスタンスを作成しないことです。</span><span class="sxs-lookup"><span data-stu-id="e2113-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="e2113-172">AlgorithmConfiguration 型は、キー作成ルーチン ([自動キーローリング](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)など) のエントリポイントとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e2113-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="e2113-173">今後のすべてのキーの実装を変更するには、KeyManagementOptions で認証の Ated暗号化 Torconfiguration プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="e2113-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="e2113-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e2113-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e2113-175">**I認証 atedの Torconfiguration**インターフェイスは、 [I認証 Ated暗号化 tordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンスを作成する方法を認識する型を表します。</span><span class="sxs-lookup"><span data-stu-id="e2113-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="e2113-176">1つの API を公開します。</span><span class="sxs-lookup"><span data-stu-id="e2113-176">It exposes a single API.</span></span>

* <span data-ttu-id="e2113-177">CreateNewDescriptor (): I認証 Ated暗号化 Tordescriptor</span><span class="sxs-lookup"><span data-stu-id="e2113-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="e2113-178">I認証の暗号化の構成は、トップレベルのファクトリと考えてください。</span><span class="sxs-lookup"><span data-stu-id="e2113-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="e2113-179">構成はテンプレートとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e2113-179">The configuration serves as a template.</span></span> <span data-ttu-id="e2113-180">アルゴリズム情報をラップします (たとえば、この構成では、AES-128-GCM マスターキーを使用して記述子が生成されます) が、特定のキーにはまだ関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="e2113-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="e2113-181">CreateNewDescriptor が呼び出されると、この呼び出しに対してのみ新しいキーマテリアルが作成され、このキーマテリアルをラップする新しい i Ated Tordescriptor が生成され、マテリアルを使用するために必要なアルゴリズム情報が生成されます。</span><span class="sxs-lookup"><span data-stu-id="e2113-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="e2113-182">キーマテリアルはソフトウェアで作成でき (また、メモリ内に保持されます)、HSM 内で作成して保持することができます。</span><span class="sxs-lookup"><span data-stu-id="e2113-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="e2113-183">重要な点は、CreateNewDescriptor への2回の呼び出しでは、同等の I認証 Ated Tordescriptor インスタンスを作成しないことです。</span><span class="sxs-lookup"><span data-stu-id="e2113-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="e2113-184">I認証 Ated暗号化の構成の種類は、キー作成ルーチン ([自動キーローリング](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)など) のエントリポイントとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e2113-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="e2113-185">今後のすべてのキーの実装を変更するには、サービスコンテナーにシングルトン I認証 Ated暗号化構成を登録します。</span><span class="sxs-lookup"><span data-stu-id="e2113-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
