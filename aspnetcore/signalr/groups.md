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
# <a name="manage-users-and-groups-in-signalr"></a>SignalR におけるユーザーとグループの管理

作成者: [Brennan Conroy](https://github.com/BrennanConroy)

SignalR では、特定のユーザーに関連付けられているすべての接続に送信するだけでなく接続の名前付きグループにメッセージを許可します。

[サンプルコードの表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>SignalR におけるユーザー

SignalR では、特定のユーザーに関連付けられているすべての接続にメッセージを送信できます。 既定では、SignalR はユーザー識別子として接続に関連付けられた`ClaimsPrincipal`の`ClaimTypes.NameIdentifier`を使用します。 1 人のユーザーには、SignalR のアプリに複数の接続を持つことができます。 たとえば、ユーザーは自分のデスクトップと同様に携帯電話でも接続することができます。 各デバイスは別々の SignalR 接続を持ちますが、それらはすべて同じユーザーに関連付けられています。 メッセージがユーザーに送信されると、ユーザーに関連付けられているすべての接続でメッセージを受信します。 接続のユーザー識別子には、ハブの`Context.UserIdentifier`プロパティからアクセスすることができます。

次の例で示すように、ハブメソッドで`User`関数にユーザー識別子を渡すことによって、特定のユーザーにメッセージを送信します。

> [!NOTE]
> ユーザー識別子は、大文字小文字を区別します。

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>SignalR におけるグループ

グループは、名前に関連付けられている接続のコレクションです。 グループ内のすべての接続にメッセージを送信できます。 グループはアプリケーションによって管理されるため、単一または複数の接続に送信するお勧めの方法です。 接続は、複数のグループのメンバーであることができます。 これにより、グループは各部屋をグループとして表現できるチャットアプリケーションのようなものに理想的です。 `AddToGroupAsync`および`RemoveFromGroupAsync`メソッドを使用してグループに接続を追加または削除することができます。

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

接続が再接続されると、グループメンバーシップは保持されません。 接続が再確立されたときに、グループに再度参加する必要があります。アプリケーションが複数のサーバーにスケーリングされる場合にこの情報は使用ではないため、グループのメンバーをカウントすることはできません。

グループの使用中にリソースへのアクセスを保護するには、ASP.NET Core の [認証と承認](xref:signalr/authn-and-authz) 機能を使用します。 グループに対して資格情報が有効な場合にのみユーザーをグループに追加すると、そのグループに送信されたメッセージは承認されたユーザーにのみ届きます。 ただし、グループは、セキュリティ機能ではありません。 認証要求には、有効期限と取り消しなど、グループにはない機能があります。 グループにアクセスするユーザーのアクセス許可が取り消された場合は、手動で検出しグループから削除する必要があります。

> [!NOTE]
> グループ名は大文字小文字を区別します。

## <a name="related-resources"></a>関連資料

* [開始するには](xref:tutorials/signalr)
* [ハブ](xref:signalr/hubs)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
