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
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>ASP.NET Core での主要な暗号化の拡張性

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> 次のインターフェイスのいずれかを実装する型がスレッド セーフにする必要があります複数の呼び出し元の。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

**IAuthenticatedEncryptor**インターフェイスは、暗号化サブシステムの基本的なビルディング ブロックです。 通常は、1 つのキーの 1 つの IAuthenticatedEncryptor と IAuthenticatedEncryptor インスタンスがすべての暗号化キー マテリアルと暗号化操作の実行に必要なアルゴリズムの情報をラップします。

その名前からわかるように、型は、認証済みの暗号化と復号化サービスを提供する責任を負います。 これは、次の 2 つの Api を公開します。

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Encrypt メソッドでは、したり、プレーン テキストと、認証タグが含まれる blob を返します。 認証タグは、AAD 自体は最終的なペイロードから回復可能な必要はありませんが、追加の認証済みデータ (AAD) を網羅する必要があります。 復号化メソッドは、認証タグを検証し、解読はペイロードを返します。 すべてのエラー (ArgumentNullException 以外と似ています) は、CryptographicException に homogenized する必要があります。

> [!NOTE]
> IAuthenticatedEncryptor インスタンス自体実際には、キー マテリアルを格納する必要はありません。 など、実装は、すべての操作の HSM に委任でした。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>IAuthenticatedEncryptor を作成する方法

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorFactory**インターフェイスが作成する方法を認識している型を表す、 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンス。 その API は次のとおりです。

* CreateEncryptorInstance (IKey キー)。IAuthenticatedEncryptor

それと同等と見なす必要があります、CreateEncryptorInstance メソッドによって作成された任意の認証済み受け付け IKey の特定のインスタンスの、以下のコード サンプル。

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorDescriptor**インターフェイスが作成する方法を認識している型を表す、 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンス。 その API は次のとおりです。

* CreateEncryptorInstance() :IAuthenticatedEncryptor

* ExportToXml() :XmlSerializedDescriptorInfo

IAuthenticatedEncryptor のような IAuthenticatedEncryptorDescriptor のインスタンスが 1 つの特定のキーをラップすると見なされます。 つまり、IAuthenticatedEncryptorDescriptor の特定のインスタンスの CreateEncryptorInstance メソッドによって作成された任意の認証済み受け付けする必要がありますと見なされるようにそれと同等の以下のコード サンプル。

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x のみ)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorDescriptor**インターフェイス自体を XML にエクスポートする方法を認識している型を表します。 その API は次のとおりです。

* ExportToXml() :XmlSerializedDescriptorInfo

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML シリアル化

IAuthenticatedEncryptor と IAuthenticatedEncryptorDescriptor の主な違いは、記述子が暗号化機能を作成し、有効な引数を指定する方法を知っていることです。 実装は SymmetricAlgorithm と KeyedHashAlgorithm を IAuthenticatedEncryptor を検討してください。 暗号化機能のジョブは、これらの型を使用するが、必ずしもを認識していないから、これらの型が付属していたため、アプリケーションが再起動した場合、それ自体を再作成する方法の適切な説明を実際に書き込むことはできません。 記述子は、これより高いレベルとして機能します。 記述子は、暗号化機能のインスタンスを作成する方法を知っているため (たとえば、方法を確認する必要なアルゴリズムを作成する)、アプリケーションをリセットした後、暗号化機能のインスタンスが再作成できるように XML 形式では、その知識をシリアル化ができます。

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

記述子は、その ExportToXml ルーチンを使用してシリアル化することができます。 このルーチンは、2 つのプロパティを含む、XmlSerializedDescriptorInfo を返します記述子の型を表す XElement 表現、 [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer)できます。この記述子の指定、対応する XElement を再生するために使用します。

シリアル化された記述子は、暗号化キー マテリアルなどの機密情報を含めることができます。 データ保護システムがストレージに永続化される前に、情報を暗号化するための組み込みサポートしています。 これを活用する記述子が機微な情報で属性名"requiresEncryption"を含む要素をマークします。 (xmlns"<http://schemas.asp.net/2015/03/dataProtection>")、"true"の値。

>[!TIP]
> この属性を設定するためのヘルパー API があります。 XElement.MarkAsRequiresEncryption() Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel を名前空間にある拡張メソッドを呼び出します。

シリアル化された記述子が機密情報を格納しない場合もあります。 HSM に格納されている暗号化キーの大文字と小文字をもう一度考えてみましょう。 記述子は、HSM がプレーン テキスト形式の内容を公開しないため自体をシリアル化するとき、キー マテリアルを書き込むことはできません。 代わりに、記述子 (HSM では、この方法でエクスポートが可能) の場合、キーまたはキーを HSM の一意の識別子のキーでラップされたバージョンが書き込まれる可能性があります。

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

**IAuthenticatedEncryptorDescriptorDeserializer**インターフェイスは、XElement から IAuthenticatedEncryptorDescriptor インスタンスを逆シリアル化する方法を認識する型を表します。 1 つのメソッドを公開します。

* ImportFromXml (XElement 要素)。IAuthenticatedEncryptorDescriptor

ImportFromXml メソッドには、返された XElement [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml)元 IAuthenticatedEncryptorDescriptor に相当するを作成します。

IAuthenticatedEncryptorDescriptorDeserializer を実装する型は、次の 2 つのパブリック コンス トラクターのいずれかが必要です。

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> コンス トラクターに渡される IServiceProvider を null にすることがあります。

## <a name="the-top-level-factory"></a>最上位レベルのファクトリ

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**AlgorithmConfiguration**クラスを作成する方法を認識している型を表す[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンス。 これは、1 つの API を公開します。

* CreateNewDescriptor() :IAuthenticatedEncryptorDescriptor

AlgorithmConfiguration の最上位レベルのファクトリとして考えます。 構成は、テンプレートとして機能します。 アルゴリズムの情報をラップします (例: この構成は AES 128-GCM のマスター _ キーで記述子を作成します)、特定のキーに関連付けされていないが、します。

CreateNewDescriptor が呼び出され、この呼び出しでは、専用の最新のキー マテリアルを作成し、新しい IAuthenticatedEncryptorDescriptor が生成されるときに、このキー マテリアルとアルゴリズムにマテリアルを使用するために必要な情報をラップします。 キー マテリアルでしたするソフトウェアで作成 (メモリに保持されている) を作成し、HSM および具合内で保持されている可能性があります。 重要なポイントは、CreateNewDescriptor への 2 つの呼び出しでは同等 IAuthenticatedEncryptorDescriptor インスタンスは作成しません。

など、AlgorithmConfiguration 型をキーの作成のルーチンのエントリ ポイントとして機能[自動のキーをローリング](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)します。 将来のすべてのキーの実装を変更するには、KeyManagementOptions で AuthenticatedEncryptorConfiguration プロパティを設定します。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorConfiguration**インターフェイスが作成する方法を認識している型を表す[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンス。 これは、1 つの API を公開します。

* CreateNewDescriptor() :IAuthenticatedEncryptorDescriptor

IAuthenticatedEncryptorConfiguration の最上位レベルのファクトリとして考えます。 構成は、テンプレートとして機能します。 アルゴリズムの情報をラップします (例: この構成は AES 128-GCM のマスター _ キーで記述子を作成します)、特定のキーに関連付けされていないが、します。

CreateNewDescriptor が呼び出され、この呼び出しでは、専用の最新のキー マテリアルを作成し、新しい IAuthenticatedEncryptorDescriptor が生成されるときに、このキー マテリアルとアルゴリズムにマテリアルを使用するために必要な情報をラップします。 キー マテリアルでしたするソフトウェアで作成 (メモリに保持されている) を作成し、HSM および具合内で保持されている可能性があります。 重要なポイントは、CreateNewDescriptor への 2 つの呼び出しでは同等 IAuthenticatedEncryptorDescriptor インスタンスは作成しません。

など、IAuthenticatedEncryptorConfiguration 型をキーの作成のルーチンのエントリ ポイントとして機能[自動のキーをローリング](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)します。 将来のすべてのキーの実装を変更するには、サービス コンテナーにシングルトン IAuthenticatedEncryptorConfiguration を登録します。

---
