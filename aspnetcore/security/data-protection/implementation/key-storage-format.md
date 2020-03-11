---
title: ASP.NET Core のキー格納形式
author: rick-anderson
description: ASP.NET Core データ保護のキーストレージ形式の実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654998"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="b6d20-103">ASP.NET Core のキー格納形式</span><span class="sxs-lookup"><span data-stu-id="b6d20-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="b6d20-104">オブジェクトは、XML 形式で保存されます。</span><span class="sxs-lookup"><span data-stu-id="b6d20-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="b6d20-105">キーストレージの既定のディレクトリは%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span><span class="sxs-lookup"><span data-stu-id="b6d20-105">The default directory for key storage is %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span></span>

## <a name="the-key-element"></a><span data-ttu-id="b6d20-106">\<キー > 要素</span><span class="sxs-lookup"><span data-stu-id="b6d20-106">The \<key> element</span></span>

<span data-ttu-id="b6d20-107">キーは、キーリポジトリの最上位レベルのオブジェクトとして存在します。</span><span class="sxs-lookup"><span data-stu-id="b6d20-107">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="b6d20-108">規則によるキーのファイル名は**キー {guid} .xml です**。ここで、{guid} はキーの id です。</span><span class="sxs-lookup"><span data-stu-id="b6d20-108">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="b6d20-109">このようなファイルには1つのキーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b6d20-109">Each such file contains a single key.</span></span> <span data-ttu-id="b6d20-110">ファイルの形式は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b6d20-110">The format of the file is as follows.</span></span>

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

<span data-ttu-id="b6d20-111">\<key > 要素には、次の属性と子要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b6d20-111">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="b6d20-112">キー id。この値は権限のあるものとして扱われます。ファイル名は、人間が読みやすくするための単なる言えです。</span><span class="sxs-lookup"><span data-stu-id="b6d20-112">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="b6d20-113">\<キー > 要素のバージョン。現在は1で修正されています。</span><span class="sxs-lookup"><span data-stu-id="b6d20-113">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="b6d20-114">キーの作成、アクティブ化、および有効期限。</span><span class="sxs-lookup"><span data-stu-id="b6d20-114">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="b6d20-115">このキーに含まれる認証済み暗号化の実装に関する情報を格納している \<記述子 > 要素。</span><span class="sxs-lookup"><span data-stu-id="b6d20-115">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="b6d20-116">上の例では、キーの id は {80732141-ec8f-4b80-af9c-c4d2d1ff8901} であり、2015年3月19日に作成され、アクティブ化され、90日の有効期間があります。</span><span class="sxs-lookup"><span data-stu-id="b6d20-116">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="b6d20-117">(場合によっては、ライセンス認証日が、この例のように作成日よりも少し前になることがあります。</span><span class="sxs-lookup"><span data-stu-id="b6d20-117">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="b6d20-118">これは、Api が動作し、実際には無害であることが原因です)。</span><span class="sxs-lookup"><span data-stu-id="b6d20-118">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="b6d20-119">\<記述子 > 要素</span><span class="sxs-lookup"><span data-stu-id="b6d20-119">The \<descriptor> element</span></span>

<span data-ttu-id="b6d20-120">外部 \<記述子の > 要素には、deserializerType 属性が含まれています。これは、I認証 Ated Tordescriptor デシリアライザーを実装する型のアセンブリ修飾名です。</span><span class="sxs-lookup"><span data-stu-id="b6d20-120">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="b6d20-121">この型は、内部 \<記述子 > 要素を読み取り、内に格納されている情報を解析します。</span><span class="sxs-lookup"><span data-stu-id="b6d20-121">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="b6d20-122">\<記述子の > 要素の特定の形式は、キーによってカプセル化される認証済み暗号化の実装によって異なります。また、各デシリアライザー型では、この形式が若干異なることが想定されています。</span><span class="sxs-lookup"><span data-stu-id="b6d20-122">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="b6d20-123">ただし、一般に、この要素にはアルゴリズム情報 (名前、型、Oid、または類似) とシークレットキーマテリアルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b6d20-123">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="b6d20-124">上の例では、記述子は、このキーが AES-256-CBC encryption + HMACSHA256 検証をラップすることを指定しています。</span><span class="sxs-lookup"><span data-stu-id="b6d20-124">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="b6d20-125">\<encryptedSecret > 要素</span><span class="sxs-lookup"><span data-stu-id="b6d20-125">The \<encryptedSecret> element</span></span>

<span data-ttu-id="b6d20-126">暗号化された形式の秘密キーマテリアルを含む **&lt;encryptedsecret&gt;** 要素は、保存[時のシークレットの暗号化が有効になっ](xref:security/data-protection/implementation/key-encryption-at-rest)ている場合に存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b6d20-126">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="b6d20-127">属性 `decryptorType` は、 [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)を実装する型のアセンブリ修飾名です。</span><span class="sxs-lookup"><span data-stu-id="b6d20-127">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="b6d20-128">この型は、内部 **&lt;encryptedKey&gt;** 要素を読み取り、復号化して元のプレーンテキストを回復する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="b6d20-128">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="b6d20-129">`<descriptor>`と同様に、`<encryptedSecret>` 要素の特定の形式は、使用されている保存時の暗号化メカニズムに依存します。</span><span class="sxs-lookup"><span data-stu-id="b6d20-129">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="b6d20-130">上の例では、コメントごとに Windows DPAPI を使用してマスターキーが暗号化されています。</span><span class="sxs-lookup"><span data-stu-id="b6d20-130">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="b6d20-131">\<失効 > 要素</span><span class="sxs-lookup"><span data-stu-id="b6d20-131">The \<revocation> element</span></span>

<span data-ttu-id="b6d20-132">失効は、キーリポジトリの最上位レベルのオブジェクトとして存在します。</span><span class="sxs-lookup"><span data-stu-id="b6d20-132">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="b6d20-133">慣例により、失効はファイル名の**失効-{timestamp} .xml** (特定の日付より前のすべてのキーを取り消す場合) または**失効-{guid} .xml** (特定のキーを取り消す場合) を持ちます。</span><span class="sxs-lookup"><span data-stu-id="b6d20-133">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="b6d20-134">各ファイルには、1つの \<失効 > 要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b6d20-134">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="b6d20-135">個々のキーの失効の場合、ファイルの内容は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b6d20-135">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="b6d20-136">この場合、指定されたキーのみが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="b6d20-136">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="b6d20-137">ただし、キー id が "\*" の場合、次の例のように、作成日が指定した失効日より前のすべてのキーが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="b6d20-137">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="b6d20-138">\<reason > 要素は、システムによって読み取られません。</span><span class="sxs-lookup"><span data-stu-id="b6d20-138">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="b6d20-139">これは、ユーザーが判読できる失効の理由を格納するための便利な場所です。</span><span class="sxs-lookup"><span data-stu-id="b6d20-139">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
