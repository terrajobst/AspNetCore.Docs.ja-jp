---
title: ASP.NET Core の認証の概要
author: mjrousos
description: ASP.NET Core での認証について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
uid: security/authentication/index
ms.openlocfilehash: 404904ecfa30d1fe7e47f0daaa423ddd6f1b06e8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434331"
---
# <a name="overview-of-aspnet-core-authentication"></a>ASP.NET Core の認証の概要

作成者: [Mike Rousos](https://github.com/mjrousos)

認証は、ユーザーの ID を突き止めるプロセスです。 [認可](xref:security/authorization/introduction)は、ユーザーがリソースにアクセスできるかどうかを判断するプロセスです。 ASP.NET Core では、認証は `IAuthenticationService` によって処理されます。これは、認証[ミドルウェア](xref:fundamentals/middleware/index)によって使用されます。 認証サービスは、登録済みの認証ハンドラーを使用して、認証関連のアクションを完了します。 認証関連のアクションの例を次に示します。

* ユーザーを認証する。
* 認証されていないユーザーが制限されたリソースにアクセスしようとしたときに応答する。

登録済みの認証ハンドラーとその構成オプションは、"スキーム" と呼ばれます。

認証方式は `Startup.ConfigureServices` に認証サービスを登録することによって指定されます。

* `services.AddAuthentication` の呼び出しの後にスキーム固有の拡張メソッド (`AddJwtBearer` や `AddCookie` など) を呼び出すことによって行います。 これらの拡張メソッドは [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) を使用してスキームを適切な設定で登録します。
* あまり一般的ではありませんが、[AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) を直接呼び出す方法もあります。

たとえば、次のコードでは、Cookie と JWT ベアラー認証スキームの認証サービスとハンドラーが登録されます。

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

`AddAuthentication` パラメーター `JwtBearerDefaults.AuthenticationScheme` は、特定のスキームが要求されていない場合に既定で使用されるスキームの名前です。

複数のスキームが使用される場合、認可ポリシー (または認可属性) で、ユーザーの認証時に使用する[認証スキーム (複数可) を指定する](xref:security/authorization/limitingidentitybyscheme)ことができます。 上記の例では、Cookie 認証スキームを使用する場合に名前を指定しています (既定では `CookieAuthenticationDefaults.AuthenticationScheme` ですが、`AddCookie` を呼び出すときに別の名前を指定することもできます)。

場合によっては、`AddAuthentication` の呼び出しが、他の拡張メソッドによって自動的に行われます。 たとえば、[ASP.NET Core の ID](xref:security/authentication/identity) を使用する場合、`AddAuthentication` が内部的に呼び出されます。

認証ミドルウェアは、アプリの `Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 拡張メソッドを呼び出すことによって `IApplicationBuilder` に追加されます。 `UseAuthentication` を呼び出すと、以前に登録された認証スキームを使用するミドルウェアが登録されます。 認証されているユーザーに依存するすべてのミドルウェアの前に `UseAuthentication` を呼び出します。 エンドポイント ルーティングを使用する場合は、次のタイミングで `UseAuthentication` の呼び出しを行う必要があります。

* ルート情報が認証の決定に使用できるように、`UseRouting` の後。
* エンドポイントにアクセスする前にユーザーが認証されるように、`UseEndpoints` の前。

## <a name="authentication-concepts"></a>認証の概念

### <a name="authentication-scheme"></a>認証スキーム

認証スキームは、次のものに対応する名前です。

* 認証ハンドラー。
* ハンドラーの特定のインスタンスを構成するためのオプション。

スキームは、関連付けられたハンドラーの認証、チャレンジ、禁止動作を参照するためのメカニズムとして役立ちます。 たとえば、認証ポリシーでは、スキーム名を使用して、ユーザーの認証に使用する認証スキーム (またはスキーム) を指定できます。 認証を構成する場合は、既定の認証スキームを指定するのが一般的です。 リソースが特定のスキームを要求しない限り、既定のスキームが使用されます。 また、次のことも可能です。

* 認証、チャレンジ、禁止アクションに使用する別の既定のスキームを指定する。
* [ポリシー スキーム](xref:security/authentication/policyschemes)を使用して、複数のスキームを 1 つに結合する。

### <a name="authentication-handler"></a>認証ハンドラー

認証ハンドラーは:

* スキームの動作を実装する型です。
* <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> または <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> から派生しています。
* ユーザーを認証するための主要な役割を担います。

認証スキームの構成と受信要求コンテキストに基づき、認証ハンドラーは:

* 認証が成功した場合に、ユーザーの ID を表す <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> オブジェクトを構築します。
* 認証に失敗した場合は、'no result' または 'failure' を返します。
* ユーザーがリソースにアクセスしようとするときのチャレンジおよび禁止アクションの方法を用意しています。
  * アクセスが許可されていない (禁止)。
  * 認証されていない (チャレンジ)。

### <a name="authenticate"></a>Authenticate

認証スキームの認証アクションは、要求コンテキストに基づいてユーザーの ID を構築する役割を担います。 認証が成功したかどうかを示す <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> を返します。成功した場合は、認証チケットに含まれるユーザーの ID です。 [https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview](<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>) をご覧ください。 認証の例を以下に示します。

* Cookie からユーザーの ID を構築する Cookie 認証スキーム。
* ユーザーの ID を構築するための JWT ベアラー トークンを逆シリアル化し、検証する JWT ベアラー スキーム。

### <a name="challenge"></a>課題

認証チャレンジは、認証されていないユーザーが認証を必要とするエンドポイントを要求したときに、認可によって呼び出されます。 認証チャレンジが発行されるのは、匿名ユーザーが制限されたリソースを要求した場合や、ログイン リンクをクリックした場合などです。 認可では、指定された認証スキームを使用してチャレンジが呼び出されます。何も指定されていない場合は、既定値が使用されます。 [https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview](<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>) をご覧ください。 認証チャレンジの例を次に示します。

* ログイン ページにユーザーをリダイレクトする Cookie 認証スキーム。
* `www-authenticate: bearer` ヘッダーを持つ 401 結果を返す JWT ベアラースキーム。

チャレンジ アクションでは、要求されたリソースへのアクセスに使用する認証メカニズムがユーザーに通知されます。

### <a name="forbid"></a>禁止

認証されたユーザーがアクセスを許可されていないリソースにアクセスしようとすると、認可によって認証スキームの禁止アクションが呼び出されます。 [https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview](<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>) をご覧ください。 認証の禁止の例を次に示します。
* アクセスが禁止されていることを示すページにユーザーをリダイレクトする Cookie 認証スキーム。
* 403 結果を返す JWT ベアラースキーム。
* ユーザーがリソースへのアクセスを要求できるページにリダイレクトするカスタム認証スキーム。

禁止アクションを使用すると、ユーザーは次のことを知ることができます。

* 認証されている。
* 要求されたリソースにアクセスすることは許可されていない。

チャレンジと禁止の違いについては、次のリンクを参照してください。

* [運用リソースハンドラーを使用したチャレンジと禁止](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)。
* [チャレンジと禁止の違い](xref:security/authorization/secure-data#challenge)。

## <a name="authentication-providers-per-tenant"></a>テナントごとの認証プロバイダー

ASP.NET Core フレームワークには、マルチテナント認証用の組み込みソリューションは用意されていません。
お客様がこれを、組み込みの機能を使用して独自に作成することは確かに可能ですが、Microsoft ではこの目的のために [Orchard Core](https://www.orchardcore.net/) を検討することをお勧めします。

Orchard Core とは:

* ASP.NET Core を使用して構築された、オープンソースの、モジュール形式かつマルチテナントのアプリ フレームワークです。
* そのアプリ フレームワークの上に構築されたコンテンツ管理システム (CMS) です。

テナントごとの認証プロバイダーの例については、[Orchard Core](https://github.com/OrchardCMS/OrchardCore) のソースを参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
