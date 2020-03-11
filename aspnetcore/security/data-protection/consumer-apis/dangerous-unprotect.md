---
title: キーが取り消されたペイロードの保護解除 ASP.NET Core
author: rick-anderson
description: ASP.NET Core アプリで、失効した後のキーで保護されたデータの保護を解除する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654596"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="7a73a-103">キーが取り消されたペイロードの保護解除 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a73a-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="7a73a-104">ASP.NET Core データ保護 Api は、主に機密ペイロードの永続的な永続化のためのものではありません。</span><span class="sxs-lookup"><span data-stu-id="7a73a-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="7a73a-105">[WINDOWS CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx)や[Azure Rights Management](/rights-management/)などのその他のテクノロジは、無期限のストレージのシナリオに適しています。また、強力なキー管理機能も備えています。</span><span class="sxs-lookup"><span data-stu-id="7a73a-105">Other technologies like [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="7a73a-106">ただし、社外秘データの長期的な保護には、ASP.NET Core データ保護 Api を使用した開発者の禁止はありません。</span><span class="sxs-lookup"><span data-stu-id="7a73a-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="7a73a-107">キーはキーリングから削除されることはないため、`IDataProtector.Unprotect` は、キーが使用可能で有効である限り、常に既存のペイロードを回復できます。</span><span class="sxs-lookup"><span data-stu-id="7a73a-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="7a73a-108">ただし、この場合、`IDataProtector.Unprotect` によって例外がスローされるため、取り消されたキーで保護されているデータの保護を解除しようとすると、問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="7a73a-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="7a73a-109">これは、短期間または一時的なペイロード (認証トークンなど) では問題になる可能性があります。これらの種類のペイロードはシステムによって簡単に再作成でき、少なくともサイトビジターが再度ログインする必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="7a73a-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="7a73a-110">しかし、永続化されたペイロードの場合は `Unprotect` スローされると、許容できないデータ損失につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7a73a-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="7a73a-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="7a73a-111">IPersistedDataProtector</span></span>

<span data-ttu-id="7a73a-112">失効したキーがある場合でもペイロードを保護できないようにするためのシナリオをサポートするために、データ保護システムには `IPersistedDataProtector` の種類が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7a73a-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="7a73a-113">`IPersistedDataProtector`のインスタンスを取得するには、通常の方法で `IDataProtector` のインスタンスを取得し、`IDataProtector` を `IPersistedDataProtector`にキャストします。</span><span class="sxs-lookup"><span data-stu-id="7a73a-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="7a73a-114">すべての `IDataProtector` インスタンスを `IPersistedDataProtector`にキャストすることはできません。</span><span class="sxs-lookup"><span data-stu-id="7a73a-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="7a73a-115">開発者は、 C#無効なキャストによって発生するランタイム例外を回避するために as 演算子または同様の方法を使用する必要があり、エラーケースを適切に処理できるように準備する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a73a-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="7a73a-116">`IPersistedDataProtector` は、次の API サーフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="7a73a-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="7a73a-117">この API は、(バイト配列として) 保護されたペイロードを取得し、保護されていないペイロードを返します。</span><span class="sxs-lookup"><span data-stu-id="7a73a-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="7a73a-118">文字列ベースのオーバーロードはありません。</span><span class="sxs-lookup"><span data-stu-id="7a73a-118">There's no string-based overload.</span></span> <span data-ttu-id="7a73a-119">2つの出力パラメーターは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7a73a-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="7a73a-120">`requiresMigration`: このペイロードを保護するために使用されるキーがアクティブな既定のキーではなくなった場合 (たとえば、このペイロードの保護に使用されるキーが古く、キーのローリング操作が行われたため)、true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7a73a-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="7a73a-121">呼び出し元は、ビジネスニーズに応じてペイロードを再保護することを検討できます。</span><span class="sxs-lookup"><span data-stu-id="7a73a-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="7a73a-122">`wasRevoked`: このペイロードを保護するために使用されたキーが失効した場合、は true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7a73a-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="7a73a-123">`DangerousUnprotect` メソッドに `ignoreRevocationErrors: true` を渡すときは、細心の注意を払ってください。</span><span class="sxs-lookup"><span data-stu-id="7a73a-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="7a73a-124">このメソッドを呼び出した後、`wasRevoked` 値が true の場合、このペイロードの保護に使用されるキーは取り消され、ペイロードの信頼性は suspect として扱われる必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a73a-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="7a73a-125">この場合、信頼されていない web クライアントによって送信されるのではなく、セキュリティで保護されたデータベースからのものであることを保証している場合は、保護されていないペイロードでの操作を続行します。</span><span class="sxs-lookup"><span data-stu-id="7a73a-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
