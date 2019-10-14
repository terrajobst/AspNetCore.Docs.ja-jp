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
# <a name="signalr-api-design-considerations"></a><span data-ttu-id="6b52b-103">SignalR の API の設計に関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="6b52b-103">SignalR API design considerations</span></span>

<span data-ttu-id="6b52b-104">作成者: [Andrew Stanton-nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="6b52b-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="6b52b-105">この記事では、SignalR ベースの Api を構築するためのガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="6b52b-106">カスタム オブジェクトのパラメーターを使用して、下位互換性を確保するには</span><span class="sxs-lookup"><span data-stu-id="6b52b-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="6b52b-107">(クライアントまたはサーバー) 上の SignalR ハブ メソッドにパラメーターの追加は、*互換性に影響する変更*します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="6b52b-108">これは、適切な数のパラメーターのないメソッドを呼び出すしようとすると、以前のクライアント/サーバーのエラーが発生することを意味します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="6b52b-109">ただし、カスタム オブジェクトのパラメーターのプロパティを追加することは**いない**重大な変更。</span><span class="sxs-lookup"><span data-stu-id="6b52b-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="6b52b-110">クライアントまたはサーバー上の変更に対応する互換性のある Api を設計するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b52b-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="6b52b-111">たとえば、次のようにサーバー側 API があるとします。</span><span class="sxs-lookup"><span data-stu-id="6b52b-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="6b52b-112">JavaScript クライアントは、このメソッドを使用してを呼び出す`invoke`次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6b52b-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="6b52b-113">後でサーバー メソッドに 2 番目のパラメーターを追加する場合、古いクライアントは、このパラメーターの値を指定しません。</span><span class="sxs-lookup"><span data-stu-id="6b52b-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="6b52b-114">例:</span><span class="sxs-lookup"><span data-stu-id="6b52b-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="6b52b-115">古いクライアントは、このメソッドを呼び出す際は、このようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b52b-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="6b52b-116">サーバーで、このようなログ メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b52b-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="6b52b-117">古いクライアントでは、1 つのパラメーターのみ送信されるが、新しいサーバー API には 2 つのパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="6b52b-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="6b52b-118">カスタム オブジェクトをパラメーターとして使用して、柔軟性が。</span><span class="sxs-lookup"><span data-stu-id="6b52b-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="6b52b-119">カスタム オブジェクトを使用して元の API のデザインを変更しましょう。</span><span class="sxs-lookup"><span data-stu-id="6b52b-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="6b52b-120">次に、クライアントは、メソッドを呼び出すオブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="6b52b-121">パラメーターを追加するには、代わりにプロパティを追加、`TotalLengthRequest`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="6b52b-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="6b52b-122">古いクライアントが、追加の 1 つのパラメーターを送信するときに`Param2`プロパティが 0.020`null`します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="6b52b-123">チェックして、古いクライアントから送信されたメッセージを検出することができます、`Param2`の`null`し、既定値を適用します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="6b52b-124">新しいクライアントは、両方のパラメーターを送信できます。</span><span class="sxs-lookup"><span data-stu-id="6b52b-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="6b52b-125">同じ手法では、クライアントで定義されているメソッドに対して機能します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="6b52b-126">サーバー側からは、カスタム オブジェクトを送信できます。</span><span class="sxs-lookup"><span data-stu-id="6b52b-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="6b52b-127">アクセスするクライアント側で、`Message`パラメーターを使用するのではなく、プロパティ。</span><span class="sxs-lookup"><span data-stu-id="6b52b-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="6b52b-128">後で、メッセージの送信者をペイロードに追加する場合は、オブジェクトにプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="6b52b-129">以前のクライアントは必要はありません、`Sender`値であるため、それを無視します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="6b52b-130">新しいクライアントを新しいプロパティを更新して、受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="6b52b-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="6b52b-131">ここでは、新しいクライアントは提供しない以前のサーバーのトレラント、`Sender`値。</span><span class="sxs-lookup"><span data-stu-id="6b52b-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="6b52b-132">古いサーバーが提供されないため、`Sender`値をクライアントは、アクセスする前に存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b52b-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
