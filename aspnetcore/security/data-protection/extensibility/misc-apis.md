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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="1f6be-103">その他の ASP.NET Core データ保護 Api</span><span class="sxs-lookup"><span data-stu-id="1f6be-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="1f6be-104">次のインターフェイスのいずれかを実装する型がスレッド セーフにする必要があります複数の呼び出し元の。</span><span class="sxs-lookup"><span data-stu-id="1f6be-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="1f6be-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="1f6be-105">ISecret</span></span>

<span data-ttu-id="1f6be-106">`ISecret` インターフェイスは、暗号化キーマテリアルなどのシークレット値を表します。</span><span class="sxs-lookup"><span data-stu-id="1f6be-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="1f6be-107">次の API サーフェイスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f6be-107">It contains the following API surface:</span></span>

* <span data-ttu-id="1f6be-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="1f6be-108">`Length`: `int`</span></span>

* <span data-ttu-id="1f6be-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="1f6be-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="1f6be-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="1f6be-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="1f6be-111">`WriteSecretIntoBuffer` メソッドは、指定されたバッファーに生のシークレット値を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f6be-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="1f6be-112">この API が `byte[]` を直接返すのではなく、パラメーターとしてバッファーを受け取る理由は、呼び出し元がバッファーオブジェクトをピン留めして、管理対象のガベージコレクターに対して秘密の露出を制限する機会を与えることです。</span><span class="sxs-lookup"><span data-stu-id="1f6be-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="1f6be-113">`Secret` 型は `ISecret` の具象実装で、シークレット値はインプロセスメモリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="1f6be-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="1f6be-114">Windows プラットフォームでは、シークレット値は[CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)を使用して暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="1f6be-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
