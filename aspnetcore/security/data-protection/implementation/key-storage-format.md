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
# <a name="key-storage-format-in-aspnet-core"></a>ASP.NETコアのキーストレージ形式

<a name="data-protection-implementation-key-storage-format"></a>

オブジェクトは、XML 表現で保存されます。 キーストレージのデフォルトディレクトリは次のとおりです。

* ウィンドウ: *%ローカルアプリケーションデータ%\ASP.NET\データ保護キー\*
* macOS / Linux: *$HOME/.aspnet/データ保護キー*

## <a name="the-key-element"></a>\<キー>要素

キーは、キー リポジトリ内のトップレベル オブジェクトとして存在します。 規則によってキーのファイル名**キーを持っている{guid}.xml、{guid}** はキーの ID です。 このようなファイルには、それぞれ 1 つのキーが含まれています。 ファイルの形式は以下の通りです。

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

キー \<>要素には、次の属性と子要素が含まれます。

* キー ID。この値は、権限ありとして扱われます。ファイル名は、人間の読みやすさのための単に素晴らしいです。

* \<キー>要素のバージョンは、現在 1 に固定されています。

* キーの作成、アクティベーション、および有効期限。

* この\<キーに含まれる認証済みの暗号化実装に関する情報を含む、記述子>要素。

上記の例では、キーの id は {80732141-ec8f-4b80-af9c-c4d2d1ff8901} であり、2015 年 3 月 19 日に作成およびアクティブ化され、有効期間は 90 日です。 (この例のように、アクティベーション日が作成日より少し前になることがあります。 これは、API の動作の仕組みに起因し、実際には無害です。

## <a name="the-descriptor-element"></a>記述子\<>要素

要素>\<外部記述子には、IAuthenticatedEncryptorDescriptorDeserializer を実装する型のアセンブリ修飾名である属性デシリアライザー型が含まれています。 この型は、内部\<記述子>要素を読み取り、内部に含まれる情報を解析する役割を担います。

記述子>要素の\<特定の形式は、キーによってカプセル化された認証済み暗号化ツールの実装に依存し、デシリアライザーの各型は、このためにわずかに異なる形式を期待します。 ただし、一般に、この要素にはアルゴリズム情報 (名前、型、OID、または類似) と秘密キーマテリアルが含まれます。 上記の例では、記述子は、このキーが AES-256-CBC 暗号化 + HMACSHA256 検証をラップすることを指定します。

## <a name="the-encryptedsecret-element"></a>\<暗号化されたシークレット>要素

秘密**&lt;鍵&gt;** の暗号化形式を含む EncryptedSecret 要素は[、保管時の秘密の暗号化が有効になっている場合に](xref:security/data-protection/implementation/key-encryption-at-rest)存在する可能性があります。 属性`decryptorType`は[、IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)を実装する型のアセンブリ修飾名です。 この型は、内部**&lt;の暗号化されたKey&gt;** 要素を読み取り、元のプレーンテキストを回復するためにそれを復号化します。

と同様`<descriptor>`に、`<encryptedSecret>`要素の特定の形式は、使用されている保管時の暗号化メカニズムによって異なります。 上記の例では、マスター キーは、コメントごとに Windows DPAPI を使用して暗号化されます。

## <a name="the-revocation-element"></a>失効\<>要素

失効は、キー リポジトリ内の最上位レベルのオブジェクトとして存在します。 慣例によって、失効ファイルの**失効-{timestamp}.xml(** 特定の日付より前のすべてのキーを取り消すための)または**失効-{guid}.xml(特定の**キーを取り消すための)があります。 各ファイルには、>\<要素が 1 つ含まれています。

個々のキーの取り消しの場合、ファイルの内容は以下のようになります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

この場合、指定されたキーのみが取り消されます。 ただし、キー ID が "*" の場合、次の例のように、作成日が指定の失効日より前のすべてのキーは取り消されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

要素\<>がシステムによって読み取られることはありません理由。 これは、単に取り消しのための人間が読める理由を格納するのに便利な場所です。
