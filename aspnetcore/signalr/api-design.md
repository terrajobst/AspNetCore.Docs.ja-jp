---
title: SignalR の API の設計に関する考慮事項
author: anurse
description: アプリのバージョン間で互換性のための SignalR の Api を設計する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/api-design
ms.openlocfilehash: 3f17bf055b793e8fc91fbcc15f668928ca261f77
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897809"
---
# <a name="signalr-api-design-considerations"></a><span data-ttu-id="33952-103">SignalR の API の設計に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="33952-103">SignalR API design considerations</span></span>

<span data-ttu-id="33952-104">作成者: [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="33952-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="33952-105">この記事では、SignalR ベースの API を構築するためのガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="33952-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="33952-106">カスタム オブジェクトのパラメーターを使用して、下位互換性を確保するには</span><span class="sxs-lookup"><span data-stu-id="33952-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="33952-107">(クライアントまたはサーバー上の) SignalR ハブ メソッドにパラメーターの追加することは、 *互換性に影響がある変更* です。</span><span class="sxs-lookup"><span data-stu-id="33952-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="33952-108">これは、適切な数のパラメーターのないメソッドを呼び出そうとすると、以前のクライアント/サーバーはエラーが発生することを意味します。</span><span class="sxs-lookup"><span data-stu-id="33952-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="33952-109">ただし、カスタムオブジェクトのパラメーターにプロパティを追加することは互換性に影響が**ありません**。</span><span class="sxs-lookup"><span data-stu-id="33952-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="33952-110">クライアントまたはサーバー上の変更に対応する互換性のある API を設計するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="33952-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="33952-111">たとえば、次のようにサーバー側 API があるとします。</span><span class="sxs-lookup"><span data-stu-id="33952-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="33952-112">JavaScript クライアントは、このメソッドを使用してを呼び出す`invoke`次のようにします。</span><span class="sxs-lookup"><span data-stu-id="33952-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="33952-113">後でサーバー メソッドに 2 番目のパラメーターを追加する場合、古いクライアントは、このパラメーターの値を指定しません。</span><span class="sxs-lookup"><span data-stu-id="33952-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="33952-114">例:</span><span class="sxs-lookup"><span data-stu-id="33952-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="33952-115">古いクライアントがこのメソッドを呼び出す際は、このようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="33952-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="33952-116">サーバーでは、このようなログ メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="33952-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="33952-117">古いクライアントでは、1 つのパラメーターのみ送信されますが、新しいサーバー API には 2 つのパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="33952-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="33952-118">カスタム オブジェクトをパラメーターとして使用すると柔軟性が向上します。</span><span class="sxs-lookup"><span data-stu-id="33952-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="33952-119">カスタム オブジェクトを使用して元の API のデザインを変更しましょう。</span><span class="sxs-lookup"><span data-stu-id="33952-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="33952-120">ここで、クライアントはメソッドの呼び出しにオブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="33952-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="33952-121">パラメーターを追加する代わりに、`TotalLengthRequest` オブジェクトにプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="33952-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="33952-122">古いクライアントが 1 つのパラメーターを送信するときは、`Param2` プロパティは `null` になります。</span><span class="sxs-lookup"><span data-stu-id="33952-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="33952-123">`Param2` が `null` であることをチェックして古いクライアントから送信されたメッセージであることを検出し、既定値を適用することができます。</span><span class="sxs-lookup"><span data-stu-id="33952-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="33952-124">新しいクライアントは、両方のパラメーターを送信できます。</span><span class="sxs-lookup"><span data-stu-id="33952-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="33952-125">同じ手法では、クライアントで定義されているメソッドに対して機能します。</span><span class="sxs-lookup"><span data-stu-id="33952-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="33952-126">サーバー側からは、カスタム オブジェクトを送信できます。</span><span class="sxs-lookup"><span data-stu-id="33952-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="33952-127">クライアント側では、`Message` パラメーターを使用するのではなく、プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="33952-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="33952-128">後からメッセージ送信者をペイロードに追加する場合は、オブジェクトにプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="33952-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="33952-129">古いクライアントには `Sender` 値が必要ないためそれを無視します。</span><span class="sxs-lookup"><span data-stu-id="33952-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="33952-130">新しいクライアントは新しいプロパティを読み取るために更新して、それを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="33952-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="33952-131">この場合は、新しいクライアントは `Sender` の値を提供しない古いサーバーに対してトレラントです。</span><span class="sxs-lookup"><span data-stu-id="33952-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="33952-132">古いサーバーは `Sender` の値を提供しないため、クライアントはアクセスする前に存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="33952-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
