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
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>ASP.NET Core でのコア暗号化の拡張性

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> 次のインターフェイスのいずれかを実装する型がスレッド セーフにする必要があります複数の呼び出し元の。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

**Icryptographic atedcryptographic**インターフェイスは、暗号化サブシステムの基本的な構成要素です。 一般に、キーごとに1つの icryptographic Atedcryptographic が存在し、Icryptographic Atedcryptographic インスタンスは暗号化操作を実行するために必要なすべての暗号化キーマテリアルとアルゴリズム情報をラップします。

その名前が示すように、型は認証された暗号化および復号化サービスを提供します。 次の2つの Api を公開します。

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Encrypt メソッドは、読んだりプレーンテキストと認証タグを含む blob を返します。 認証タグは、追加の認証データ (AAD) を含んでいる必要がありますが、AAD 自体は最終的なペイロードから回復できない必要があります。 暗号化解除メソッドは認証タグを検証し、解読されたペイロードを返します。 すべてのエラー (System.argumentnullexception と類似点を除く) は、System.security.cryptography.cryptographicexception> に homogenized する必要があります。

> [!NOTE]
> I認証 Ated暗号化インスタンス自体には、実際にキーマテリアルが含まれている必要はありません。 たとえば、実装は、すべての操作について HSM に委任できます。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>I認証の暗号化機能を作成する方法

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**I認証 Ated暗号化 Torfactory**インターフェイスは、 [i認証 ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成する方法を認識する型を表します。 その API は次のとおりです。

* Create暗号化 Torinstance (IKey key): Ikey Ated暗号化機能

次のコードサンプルのように、特定の IKey インスタンスに対して、Create暗号化 Torinstance メソッドによって作成された認証済みの暗号化は、同等と見なされる必要があります。

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

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**I認証 atedの Tordescriptor**インターフェイスは、 [i認証 ated暗号化](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンスを作成する方法を認識する型を表します。 その API は次のとおりです。

* Create暗号化 Torinstance (): I認証 Ated暗号化機能

* ExportToXml() : XmlSerializedDescriptorInfo

I認証 Ated暗号化機能と同様に、I認証 Atedatedtordescriptor のインスタンスは、特定のキーをラップすることを前提としています。 つまり、次のコードサンプルのように、任意の Iauthenticated Atedのインスタンスメソッドによって作成された認証済み暗号化は同等と見なされる必要があります。

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>I認証 Atedの Tordescriptor (ASP.NET Core 2.x のみ)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**I認証 Atedの Tordescriptor**インターフェイスは、それ自体を XML にエクスポートする方法を認識している型を表します。 その API は次のとおりです。

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML シリアル化

I認証 Atedの暗号化機能と I認証機能の主な違いは、記述子が暗号化を作成して有効な引数で指定する方法を認識していることです。 実装が SymmetricAlgorithm と KeyedHashAlgorithm に依存している I認証の暗号化について考えてみましょう。 暗号化機能は、これらの型を使用することを意味しますが、これらの型がどこから取得されたのかは必ずしも把握していないので、アプリケーションを再起動した場合に自身を再作成する方法について、適切な説明を記述することはできません。 記述子は、この上で上位のレベルとして機能します。 この記述子は、暗号化機能のインスタンスを作成する方法 (たとえば、必要なアルゴリズムを作成する方法を知っている) を認識しているので、アプリケーションのリセット後に暗号化機能のインスタンスを再作成できるように、その情報を XML 形式でシリアル化することができます。

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

記述子は、ExportToXml ルーチンを使用してシリアル化できます。 このルーチンは、2つのプロパティを含む XmlSerializedDescriptorInfo を返します。これは、記述子の XElement 表現と、対応する XElement でこの記述子をやり直すするために使用できる[I認証 Ated Tordescriptor デシリアライザー](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer)を表す型です。

シリアル化された記述子には、暗号化キーマテリアルなどの機密情報が含まれている場合があります。 データ保護システムには、ストレージに保存する前に、情報を暗号化するためのサポートが組み込まれています。 これを活用するために、記述子は、属性名が "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>")、値が "true" である機密情報を含む要素をマークする必要があります。

>[!TIP]
> この属性を設定するためのヘルパー API があります。 MarkAsRequiresEncryption () という名前空間にある XElement () を呼び出します。このメソッドは、AspNetCore です。

シリアル化された記述子に機密情報が含まれていない場合もあります。 HSM に暗号化キーが格納されている場合は、もう一度考慮してください。 HSM はプレーンテキスト形式でマテリアルを公開しないため、シリアル化時にキーマテリアルを書き込むことはできません。 代わりに、記述子はキーのラップされたバージョン (HSM がこの方法でエクスポートを許可している場合)、またはキーに固有の一意の識別子を書き込む場合があります。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

**IXElement Ated暗号化 Tordescriptor デシリアライザー**インターフェイスは、I認証 Ated暗号化 tordescriptor インスタンスをから逆シリアル化する方法を認識する型を表します。 1つのメソッドを公開します。

* ImportFromXml (XElement 要素): i Ated暗号化 Tordescriptor

ImportFromXml メソッドは、 [ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml)によって返された XElement を受け取り、元の i ated暗号化された tordescriptor と同等のものを作成します。

I認証を実装する型は、次の2つのパブリックコンストラクターのいずれかを持つ必要があります。

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> コンストラクターに渡される IServiceProvider は null でもかまいません。

## <a name="the-top-level-factory"></a>トップレベルのファクトリ

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**AlgorithmConfiguration**クラスは、 [i Ated暗号化 tordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンスを作成する方法を認識する型を表します。 1つの API を公開します。

* CreateNewDescriptor (): I認証 Ated暗号化 Tordescriptor

AlgorithmConfiguration はトップレベルファクトリと考えることができます。 構成はテンプレートとして機能します。 アルゴリズム情報をラップします (たとえば、この構成では、AES-128-GCM マスターキーを使用して記述子が生成されます) が、特定のキーにはまだ関連付けられていません。

CreateNewDescriptor が呼び出されると、この呼び出しに対してのみ新しいキーマテリアルが作成され、このキーマテリアルをラップする新しい i Ated Tordescriptor が生成され、マテリアルを使用するために必要なアルゴリズム情報が生成されます。 キーマテリアルはソフトウェアで作成でき (また、メモリ内に保持されます)、HSM 内で作成して保持することができます。 重要な点は、CreateNewDescriptor への2回の呼び出しでは、同等の I認証 Ated Tordescriptor インスタンスを作成しないことです。

AlgorithmConfiguration 型は、キー作成ルーチン ([自動キーローリング](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)など) のエントリポイントとして機能します。 今後のすべてのキーの実装を変更するには、KeyManagementOptions で認証の Ated暗号化 Torconfiguration プロパティを設定します。

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**I認証 atedの Torconfiguration**インターフェイスは、 [I認証 Ated暗号化 tordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンスを作成する方法を認識する型を表します。 1つの API を公開します。

* CreateNewDescriptor (): I認証 Ated暗号化 Tordescriptor

I認証の暗号化の構成は、トップレベルのファクトリと考えてください。 構成はテンプレートとして機能します。 アルゴリズム情報をラップします (たとえば、この構成では、AES-128-GCM マスターキーを使用して記述子が生成されます) が、特定のキーにはまだ関連付けられていません。

CreateNewDescriptor が呼び出されると、この呼び出しに対してのみ新しいキーマテリアルが作成され、このキーマテリアルをラップする新しい i Ated Tordescriptor が生成され、マテリアルを使用するために必要なアルゴリズム情報が生成されます。 キーマテリアルはソフトウェアで作成でき (また、メモリ内に保持されます)、HSM 内で作成して保持することができます。 重要な点は、CreateNewDescriptor への2回の呼び出しでは、同等の I認証 Ated Tordescriptor インスタンスを作成しないことです。

I認証 Ated暗号化の構成の種類は、キー作成ルーチン ([自動キーローリング](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)など) のエントリポイントとして機能します。 今後のすべてのキーの実装を変更するには、サービスコンテナーにシングルトン I認証 Ated暗号化構成を登録します。

---
