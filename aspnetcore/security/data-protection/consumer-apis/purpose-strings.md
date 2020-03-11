---
title: ASP.NET Core の目的の文字列
author: rick-anderson
description: ASP.NET Core データ保護 Api で目的の文字列を使用する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 4c85423f8de7e4b784ae1bb304a884541df251b6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654002"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="d207d-103">ASP.NET Core の目的の文字列</span><span class="sxs-lookup"><span data-stu-id="d207d-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="d207d-104">`IDataProtectionProvider` を使用するコンポーネントは、`CreateProtector` メソッドに一意の*目的*のパラメーターを渡す必要があります。</span><span class="sxs-lookup"><span data-stu-id="d207d-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="d207d-105">目的の*パラメーター*は、暗号化コンシューマー間の分離を提供するため、データ保護システムのセキュリティに固有のものです。これは、ルート暗号化キーが同じ場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="d207d-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="d207d-106">コンシューマーが目的を指定する場合は、目的の文字列をルート暗号化キーと共に使用して、そのコンシューマーに固有の暗号化サブキーを派生させます。</span><span class="sxs-lookup"><span data-stu-id="d207d-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="d207d-107">これにより、アプリケーション内の他のすべての暗号化コンシューマーからコンシューマーが分離されます。その他のコンポーネントはペイロードを読み取ることができず、他のコンポーネントのペイロードを読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="d207d-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="d207d-108">また、この分離により、コンポーネントに対する攻撃のすべての実行可能なカテゴリがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d207d-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![目的の図の例](purpose-strings/_static/purposes.png)

<span data-ttu-id="d207d-110">上の図では、`IDataProtector` インスタンス A と B は、互いのペイロードを読み取る**ことができません**。</span><span class="sxs-lookup"><span data-stu-id="d207d-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="d207d-111">目的の文字列は、シークレットである必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d207d-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="d207d-112">これは、他の適切に動作するコンポーネントが同じ目的の文字列を提供することがないという意味で、単に一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d207d-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="d207d-113">データ保護 Api を使用するコンポーネントの名前空間と型名を使用することは、経験則として適しています。実際には、この情報は競合しません。</span><span class="sxs-lookup"><span data-stu-id="d207d-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="d207d-114">ベアラートークンを処理する Contoso で作成されたコンポーネントは、目的の文字列として BearerToken を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="d207d-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="d207d-115">または、さらに優れた方法として、BearerToken を目的の文字列として使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d207d-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="d207d-116">バージョン番号を追加することで、将来のバージョンで BearerToken を目的として使用できるようになります。また、ペイロードの場合に限り、異なるバージョンが相互に完全に分離されます。</span><span class="sxs-lookup"><span data-stu-id="d207d-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="d207d-117">`CreateProtector` する目的のパラメーターは文字列の配列であるため、上記のパラメーターを `[ "Contoso.Security.BearerToken", "v1" ]`として指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d207d-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="d207d-118">これにより、目的の階層を確立し、データ保護システムでマルチテナントシナリオの可能性を開くことができます。</span><span class="sxs-lookup"><span data-stu-id="d207d-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="d207d-119">コンポーネントは、信頼されていないユーザー入力を目的のチェーンの入力の唯一のソースにすることを許可しないでください。</span><span class="sxs-lookup"><span data-stu-id="d207d-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="d207d-120">たとえば、セキュリティで保護されたメッセージを格納するコンポーネントの Contoso. Messaging. SecureMessage を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="d207d-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="d207d-121">セキュリティで保護されたメッセージングコンポーネントが `CreateProtector([ username ])`を呼び出す場合、悪意のあるユーザーが、`CreateProtector([ "Contoso.Security.BearerToken" ])`を呼び出すためにコンポーネントを取得しようとすると、ユーザー名が "BearerToken" のアカウントを作成する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d207d-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="d207d-122">メッセージングコンポーネントに適した目的のチェーンが `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`され、適切な分離が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d207d-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="d207d-123">によって提供される分離と `IDataProtectionProvider`、`IDataProtector`、および目的の動作は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d207d-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="d207d-124">指定された `IDataProtectionProvider` オブジェクトの場合、`CreateProtector` メソッドは、それを作成した `IDataProtectionProvider` オブジェクトと、メソッドに渡された目的のパラメーターの両方に一意に関連付けられた `IDataProtector` オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="d207d-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="d207d-125">目的のパラメーターを null にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="d207d-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="d207d-126">(目的が配列として指定されている場合は、配列の長さが0ではなく、配列のすべての要素が null 以外でなければならないことを意味します)。空の文字列の目的は技術的には許可されますが、推奨されません。</span><span class="sxs-lookup"><span data-stu-id="d207d-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="d207d-127">同じ順序で (序数の比較子を使用して) 同じ文字列が含まれている場合にのみ、2つの目的の引数が等価になります。</span><span class="sxs-lookup"><span data-stu-id="d207d-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="d207d-128">単一の目的の引数は、対応する単一要素の目的の配列に相当します。</span><span class="sxs-lookup"><span data-stu-id="d207d-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="d207d-129">2つの `IDataProtector` オブジェクトは、同等の目的のパラメーターを持つ同等の `IDataProtectionProvider` オブジェクトから作成された場合にのみ、等しいと見なされます。</span><span class="sxs-lookup"><span data-stu-id="d207d-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="d207d-130">指定された `IDataProtector` オブジェクトの場合、`Unprotect(protectedData)` を呼び出すと、同等の `IDataProtector` オブジェクトの `protectedData := Protect(unprotectedData)` 場合にのみ、元の `unprotectedData` が返されます。</span><span class="sxs-lookup"><span data-stu-id="d207d-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="d207d-131">コンポーネントによっては、他のコンポーネントと競合していることがわかっている目的の文字列が意図的に選択されている場合は考慮されません。</span><span class="sxs-lookup"><span data-stu-id="d207d-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="d207d-132">このようなコンポーネントは、本質的に悪意のあるものと見なされます。このシステムは、悪意のあるコードがワーカープロセス内で既に実行されている場合にセキュリティ保証を提供するためのものではありません。</span><span class="sxs-lookup"><span data-stu-id="d207d-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
