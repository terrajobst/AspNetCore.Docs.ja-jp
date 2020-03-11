---
title: その他の ASP.NET Core データ保護 Api
author: rick-anderson
description: ASP.NET Core Data Protection ISecret インターフェイスについて説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654356"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>その他の ASP.NET Core データ保護 Api

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> 次のインターフェイスのいずれかを実装する型がスレッド セーフにする必要があります複数の呼び出し元の。

## <a name="isecret"></a>ISecret

`ISecret` インターフェイスは、暗号化キーマテリアルなどのシークレット値を表します。 次の API サーフェイスが含まれています。

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer` メソッドは、指定されたバッファーに生のシークレット値を設定します。 この API が `byte[]` を直接返すのではなく、パラメーターとしてバッファーを受け取る理由は、呼び出し元がバッファーオブジェクトをピン留めして、管理対象のガベージコレクターに対して秘密の露出を制限する機会を与えることです。

`Secret` 型は `ISecret` の具象実装で、シークレット値はインプロセスメモリに格納されます。 Windows プラットフォームでは、シークレット値は[CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)を使用して暗号化されます。
