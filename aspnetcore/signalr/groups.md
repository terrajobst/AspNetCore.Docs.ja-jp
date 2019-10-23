---
title: SignalR におけるユーザーとグループの管理
author: bradygaster
description: ASP.NET Core SignalR のユーザーとグループの管理の概要です。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 180f8b4551eea39cc340bf1d250f4575cb5f71ed
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087427"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="ec8a0-103">SignalR におけるユーザーとグループの管理</span><span class="sxs-lookup"><span data-stu-id="ec8a0-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="ec8a0-104">作成者: [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="ec8a0-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="ec8a0-105">SignalR では、特定のユーザーに関連付けられているすべての接続に送信するだけでなく接続の名前付きグループにメッセージを許可します。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="ec8a0-106">[サンプル コードの表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(ダウンロードする方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ec8a0-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="ec8a0-107">SignalR におけるユーザー</span><span class="sxs-lookup"><span data-stu-id="ec8a0-107">Users in SignalR</span></span>

<span data-ttu-id="ec8a0-108">SignalR では、特定のユーザーに関連付けられているすべての接続にメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-108">SignalR allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="ec8a0-109">既定では、SignalR はユーザー識別子として接続に関連付けられた`ClaimsPrincipal`の`ClaimTypes.NameIdentifier`を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="ec8a0-110">1 人のユーザーには、SignalR のアプリに複数の接続を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="ec8a0-111">たとえば、ユーザーは自分のデスクトップと同様に携帯電話でも接続することができます。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="ec8a0-112">各デバイスは別々の SignalR 接続を持ちますが、それらはすべて同じユーザーに関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="ec8a0-113">メッセージがユーザーに送信されると、ユーザーに関連付けられているすべての接続でメッセージを受信します。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="ec8a0-114">接続のユーザー識別子には、ハブの`Context.UserIdentifier`プロパティからアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="ec8a0-115">次の例で示すように、ハブメソッドで`User`関数にユーザー識別子を渡すことによって、特定のユーザーにメッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="ec8a0-116">ユーザー識別子は、大文字小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="ec8a0-117">SignalR におけるグループ</span><span class="sxs-lookup"><span data-stu-id="ec8a0-117">Groups in SignalR</span></span>

<span data-ttu-id="ec8a0-118">グループは、名前に関連付けられている接続のコレクションです。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="ec8a0-119">グループ内のすべての接続にメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="ec8a0-120">グループはアプリケーションによって管理されるため、単一または複数の接続に送信するお勧めの方法です。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="ec8a0-121">接続は、複数のグループのメンバーであることができます。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="ec8a0-122">これにより、グループは各部屋をグループとして表現できるチャットアプリケーションのようなものに最適です。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="ec8a0-123">`AddToGroupAsync`および`RemoveFromGroupAsync`メソッドを使用してグループに接続を追加または削除することができます。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="ec8a0-124">接続が再接続されると、グループメンバーシップは保持されません。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="ec8a0-125">接続が再確立されたときに、グループに再度参加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="ec8a0-126">アプリケーションが複数のサーバーにスケーリングされる場合にこの情報は使用ではないため、グループのメンバーをカウントすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="ec8a0-127">グループの使用中にリソースへのアクセスを保護するには、ASP.NET Core の [認証と承認](xref:signalr/authn-and-authz) 機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="ec8a0-128">グループに対して資格情報が有効な場合にのみユーザーをグループに追加すると、そのグループに送信されたメッセージは承認されたユーザーにのみ届きます。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="ec8a0-129">ただし、グループは、セキュリティ機能ではありません。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-129">However, groups are not a security feature.</span></span> <span data-ttu-id="ec8a0-130">認証要求には、有効期限と取り消しなど、グループにはない機能があります。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="ec8a0-131">グループにアクセスするユーザーのアクセス許可が取り消された場合は、手動で検出しグループから削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="ec8a0-132">グループ名は大文字小文字を区別します。</span><span class="sxs-lookup"><span data-stu-id="ec8a0-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="ec8a0-133">関連資料</span><span class="sxs-lookup"><span data-stu-id="ec8a0-133">Related resources</span></span>

* [<span data-ttu-id="ec8a0-134">開始するには</span><span class="sxs-lookup"><span data-stu-id="ec8a0-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ec8a0-135">ハブ</span><span class="sxs-lookup"><span data-stu-id="ec8a0-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ec8a0-136">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="ec8a0-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
