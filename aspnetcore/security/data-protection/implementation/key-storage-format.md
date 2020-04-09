---
title: ASP.NETコアのキーストレージ形式
author: rick-anderson
description: ASP.NETコア データ保護キーストレージ形式の実装の詳細を説明します。
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976938"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="05737-103">ASP.NETコアのキーストレージ形式</span><span class="sxs-lookup"><span data-stu-id="05737-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="05737-104">オブジェクトは、XML 表現で保存されます。</span><span class="sxs-lookup"><span data-stu-id="05737-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="05737-105">キーストレージのデフォルトディレクトリは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="05737-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="05737-106">ウィンドウ: \*%ローカルアプリケーションデータ%\ASP.NET\データ保護キー\*</span><span class="sxs-lookup"><span data-stu-id="05737-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="05737-107">macOS / Linux: *$HOME/.aspnet/データ保護キー*</span><span class="sxs-lookup"><span data-stu-id="05737-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="05737-108">\<キー>要素</span><span class="sxs-lookup"><span data-stu-id="05737-108">The \<key> element</span></span>

<span data-ttu-id="05737-109">キーは、キー リポジトリ内のトップレベル オブジェクトとして存在します。</span><span class="sxs-lookup"><span data-stu-id="05737-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="05737-110">規則によってキーのファイル名**キーを持っている{guid}.xml、{guid}** はキーの ID です。</span><span class="sxs-lookup"><span data-stu-id="05737-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="05737-111">このようなファイルには、それぞれ 1 つのキーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="05737-111">Each such file contains a single key.</span></span> <span data-ttu-id="05737-112">ファイルの形式は以下の通りです。</span><span class="sxs-lookup"><span data-stu-id="05737-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="05737-113">キー \<>要素には、次の属性と子要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="05737-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="05737-114">キー ID。この値は、権限ありとして扱われます。ファイル名は、人間の読みやすさのための単に素晴らしいです。</span><span class="sxs-lookup"><span data-stu-id="05737-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="05737-115">\<キー>要素のバージョンは、現在 1 に固定されています。</span><span class="sxs-lookup"><span data-stu-id="05737-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="05737-116">キーの作成、アクティベーション、および有効期限。</span><span class="sxs-lookup"><span data-stu-id="05737-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="05737-117">この\<キーに含まれる認証済みの暗号化実装に関する情報を含む、記述子>要素。</span><span class="sxs-lookup"><span data-stu-id="05737-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="05737-118">上記の例では、キーの id は {80732141-ec8f-4b80-af9c-c4d2d1ff8901} であり、2015 年 3 月 19 日に作成およびアクティブ化され、有効期間は 90 日です。</span><span class="sxs-lookup"><span data-stu-id="05737-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="05737-119">(この例のように、アクティベーション日が作成日より少し前になることがあります。</span><span class="sxs-lookup"><span data-stu-id="05737-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="05737-120">これは、API の動作の仕組みに起因し、実際には無害です。</span><span class="sxs-lookup"><span data-stu-id="05737-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="05737-121">記述子\<>要素</span><span class="sxs-lookup"><span data-stu-id="05737-121">The \<descriptor> element</span></span>

<span data-ttu-id="05737-122">要素>\<外部記述子には、IAuthenticatedEncryptorDescriptorDeserializer を実装する型のアセンブリ修飾名である属性デシリアライザー型が含まれています。</span><span class="sxs-lookup"><span data-stu-id="05737-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="05737-123">この型は、内部\<記述子>要素を読み取り、内部に含まれる情報を解析する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="05737-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="05737-124">記述子>要素の\<特定の形式は、キーによってカプセル化された認証済み暗号化ツールの実装に依存し、デシリアライザーの各型は、このためにわずかに異なる形式を期待します。</span><span class="sxs-lookup"><span data-stu-id="05737-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="05737-125">ただし、一般に、この要素にはアルゴリズム情報 (名前、型、OID、または類似) と秘密キーマテリアルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="05737-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="05737-126">上記の例では、記述子は、このキーが AES-256-CBC 暗号化 + HMACSHA256 検証をラップすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="05737-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="05737-127">\<暗号化されたシークレット>要素</span><span class="sxs-lookup"><span data-stu-id="05737-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="05737-128">秘密**&lt;鍵&gt;** の暗号化形式を含む EncryptedSecret 要素は[、保管時の秘密の暗号化が有効になっている場合に](xref:security/data-protection/implementation/key-encryption-at-rest)存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="05737-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="05737-129">属性`decryptorType`は[、IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)を実装する型のアセンブリ修飾名です。</span><span class="sxs-lookup"><span data-stu-id="05737-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="05737-130">この型は、内部**&lt;の暗号化されたKey&gt;** 要素を読み取り、元のプレーンテキストを回復するためにそれを復号化します。</span><span class="sxs-lookup"><span data-stu-id="05737-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="05737-131">と同様`<descriptor>`に、`<encryptedSecret>`要素の特定の形式は、使用されている保管時の暗号化メカニズムによって異なります。</span><span class="sxs-lookup"><span data-stu-id="05737-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="05737-132">上記の例では、マスター キーは、コメントごとに Windows DPAPI を使用して暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="05737-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="05737-133">失効\<>要素</span><span class="sxs-lookup"><span data-stu-id="05737-133">The \<revocation> element</span></span>

<span data-ttu-id="05737-134">失効は、キー リポジトリ内の最上位レベルのオブジェクトとして存在します。</span><span class="sxs-lookup"><span data-stu-id="05737-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="05737-135">慣例によって、失効ファイルの**失効-{timestamp}.xml(** 特定の日付より前のすべてのキーを取り消すための)または**失効-{guid}.xml(特定の**キーを取り消すための)があります。</span><span class="sxs-lookup"><span data-stu-id="05737-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="05737-136">各ファイルには、>\<要素が 1 つ含まれています。</span><span class="sxs-lookup"><span data-stu-id="05737-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="05737-137">個々のキーの取り消しの場合、ファイルの内容は以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="05737-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="05737-138">この場合、指定されたキーのみが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="05737-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="05737-139">ただし、キー ID が "\*" の場合、次の例のように、作成日が指定の失効日より前のすべてのキーは取り消されます。</span><span class="sxs-lookup"><span data-stu-id="05737-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="05737-140">要素\<>がシステムによって読み取られることはありません理由。</span><span class="sxs-lookup"><span data-stu-id="05737-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="05737-141">これは、単に取り消しのための人間が読める理由を格納するのに便利な場所です。</span><span class="sxs-lookup"><span data-stu-id="05737-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
