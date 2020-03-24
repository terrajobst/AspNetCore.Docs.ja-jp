---
title: ASP.NET Core Blazor WebAssembly をセキュリティで保護する
author: guardrex
description: シングル ページ アプリケーション (SPA) として Blazor WebAssemlby アプリをセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: a65d47e55960d6e7bfeb672c0a1e6a7a305ad7ee
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989485"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly をセキュリティで保護する

作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor WebAssembly アプリは、シングル ページ アプリケーション (SPA) と同じ方法でセキュリティ保護されます。 ユーザーを SPA で認証する方法はいくつかありますが、最も一般的で包括的な方法は、[Open ID Connect (OIDC)](https://openid.net/connect/) などの [oAuth 2.0 プロトコル](https://oauth.net/)に基づく実装を使用することです。

## <a name="authentication-library"></a>認証ライブラリ

Blazor WebAssembly は、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリを介して OIDC を使用したアプリの認証と認可をサポートしています。 ライブラリには、ASP.NET Core バックエンドに対してシームレスに認証を行うための一連のプリミティブが用意されています。 ライブラリは ASP.NET Core の ID を [Identity Server](https://identityserver.io/) 上に構築された API 認可サポートと統合します。 このライブラリは、OpenID Provider (OP) と呼ばれる OIDC をサポートするすべてのサードパーティ ID プロバイダー (IP) に対して認証できます。

Blazor WebAssembly の認証のサポートは、基になる認証プロトコルの詳細を処理するために使用される、*oidc-client.js* ライブラリの上に構築されています。

SameSite Cookie の使用など、SPA を認証するためのその他のオプションも存在します。 ただし、Blazor WebAssembly のエンジニアリング設計は、Blazor WebAssembly アプリでの認証に最適なオプションとして oAuth および OIDC にも採用されています。 [JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) に基づく[トークンベースの認証](xref:security/anti-request-forgery#token-based-authentication)は、機能とセキュリティ上の理由により、[Cookie ベースの認証](xref:security/anti-request-forgery#cookie-based-authentication) に基づいて選択されています。

* トークンベースのプロトコルを使用すると、トークンがすべての要求で送信されないため、攻撃対象領域が小さくなります。
* サーバー エンドポイントでは、トークンが明示的に送信されるため、[クロスサイト リクエスト フォージェリ (CSRF)](xref:security/anti-request-forgery) に対する保護が必要ありません。 これにより、MVC または Razor ページのアプリと共に、Blazor WebAssembly アプリをホストすることができます。
* トークンの権限は Cookie よりも狭くなります。 たとえば、該当する機能が明示的に実装されていない限り、トークンを使用してユーザー アカウントを管理したり、ユーザーのパスワードを変更したりできません。
* トークンの有効期間は短く、既定では 1 時間です。これにより、攻撃時間が制限されます。 トークンは、いつでも取り消すことができます。
* 自己完結型の JWT は、クライアントとサーバーの認証プロセスを保証します。 たとえば、クライアントには、受信したトークンが正当であり、指定した認証プロセスの一環として出力されたことを検出して検証する手段があります。 サード パーティが認証プロセスの途中でトークンを切り替えようとすると、クライアントは切り替えられたトークンを検出して使用しないようにすることができます。
* oAuth および OIDC を使用したトークンは、アプリが安全であることを確認するために、正しく動作しているユーザー エージェントに依存しません。
* OAuth や OIDC などのトークンベースのプロトコルでは、同じセキュリティ特性のセットを使用して、ホストされたアプリとスタンドアロンのアプリの認証と認可を行うことができます。

## <a name="authentication-process-with-oidc"></a>OIDC を使用した認証プロセス

`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリには、OIDC を使用した認証と認可を実装するためのいくつかのプリミティブが用意されています。 大まかにいうと、認証は次のようにして行われます。

* 匿名ユーザーがログイン ボタンを選択する、または [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性が適用されたページを要求すると、そのユーザーがアプリのログイン ページ (`/authentication/login`) にリダイレクトされます。
* ログイン ページで、認証ライブラリが承認エンドポイントへのリダイレクトを準備します。 承認エンドポイントは、Blazor WebAssembly の外部にあり、別のオリジンでホストすることができます。 エンドポイントは、ユーザーが認証されているかどうかを判断し、応答として 1 つ以上のトークンを発行する役割を担います。 認証ライブラリは、認証応答を受信するためのログイン コールバックを提供します。
  * ユーザーが認証されていない場合、そのユーザーは基になる認証システム (通常は ASP.NET Core の ID) にリダイレクトされます。
  * ユーザーが既に認証されている場合、承認エンドポイントは適切なトークンを生成し、ブラウザーをログイン コールバック エンドポイント (`/authentication/login-callback`) にリダイレクトします。
* Blazor WebAssembly アプリでログイン コールバック エンドポイント (`/authentication/login-callback`) が読み込まれると、認証応答が処理されます。
  * 認証プロセスが正常に完了した場合、ユーザーは認証され、必要に応じてユーザーが要求した元の保護された URL に戻されます。
  * 何らかの理由で認証プロセスが失敗した場合、ユーザーはログイン失敗ページ (`/authentication/login-failed`) に送信され、エラーが表示されます。
