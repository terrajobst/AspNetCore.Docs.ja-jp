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
# <a name="key-storage-format-in-aspnet-core"></a>ASP.NET Core のキー格納形式

<a name="data-protection-implementation-key-storage-format"></a>

オブジェクトは、XML 形式で保存されます。 キーストレージの既定のディレクトリは%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.

## <a name="the-key-element"></a>\<キー > 要素

キーは、キーリポジトリの最上位レベルのオブジェクトとして存在します。 規則によるキーのファイル名は**キー {guid} .xml です**。ここで、{guid} はキーの id です。 このようなファイルには1つのキーが含まれています。 ファイルの形式は次のとおりです。

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

\<key > 要素には、次の属性と子要素が含まれています。

* キー id。この値は権限のあるものとして扱われます。ファイル名は、人間が読みやすくするための単なる言えです。

* \<キー > 要素のバージョン。現在は1で修正されています。

* キーの作成、アクティブ化、および有効期限。

* このキーに含まれる認証済み暗号化の実装に関する情報を格納している \<記述子 > 要素。

上の例では、キーの id は {80732141-ec8f-4b80-af9c-c4d2d1ff8901} であり、2015年3月19日に作成され、アクティブ化され、90日の有効期間があります。 (場合によっては、ライセンス認証日が、この例のように作成日よりも少し前になることがあります。 これは、Api が動作し、実際には無害であることが原因です)。

## <a name="the-descriptor-element"></a>\<記述子 > 要素

外部 \<記述子の > 要素には、deserializerType 属性が含まれています。これは、I認証 Ated Tordescriptor デシリアライザーを実装する型のアセンブリ修飾名です。 この型は、内部 \<記述子 > 要素を読み取り、内に格納されている情報を解析します。

\<記述子の > 要素の特定の形式は、キーによってカプセル化される認証済み暗号化の実装によって異なります。また、各デシリアライザー型では、この形式が若干異なることが想定されています。 ただし、一般に、この要素にはアルゴリズム情報 (名前、型、Oid、または類似) とシークレットキーマテリアルが含まれます。 上の例では、記述子は、このキーが AES-256-CBC encryption + HMACSHA256 検証をラップすることを指定しています。

## <a name="the-encryptedsecret-element"></a>\<encryptedSecret > 要素

暗号化された形式の秘密キーマテリアルを含む **&lt;encryptedsecret&gt;** 要素は、保存[時のシークレットの暗号化が有効になっ](xref:security/data-protection/implementation/key-encryption-at-rest)ている場合に存在する可能性があります。 属性 `decryptorType` は、 [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)を実装する型のアセンブリ修飾名です。 この型は、内部 **&lt;encryptedKey&gt;** 要素を読み取り、復号化して元のプレーンテキストを回復する役割を担います。

`<descriptor>`と同様に、`<encryptedSecret>` 要素の特定の形式は、使用されている保存時の暗号化メカニズムに依存します。 上の例では、コメントごとに Windows DPAPI を使用してマスターキーが暗号化されています。

## <a name="the-revocation-element"></a>\<失効 > 要素

失効は、キーリポジトリの最上位レベルのオブジェクトとして存在します。 慣例により、失効はファイル名の**失効-{timestamp} .xml** (特定の日付より前のすべてのキーを取り消す場合) または**失効-{guid} .xml** (特定のキーを取り消す場合) を持ちます。 各ファイルには、1つの \<失効 > 要素が含まれています。

個々のキーの失効の場合、ファイルの内容は次のようになります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

この場合、指定されたキーのみが取り消されます。 ただし、キー id が "*" の場合、次の例のように、作成日が指定した失効日より前のすべてのキーが取り消されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

\<reason > 要素は、システムによって読み取られません。 これは、ユーザーが判読できる失効の理由を格納するための便利な場所です。
