---
title: ASP.NET Core での証明書の認証を構成します。
author: blowdart
description: ASP.NET Core で IIS と HTTP.sys の証明書認証を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 06/11/2019
uid: security/authentication/certauth
ms.openlocfilehash: 8609c58265340da1d618135795915d6c49e750a3
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538718"
---
# <a name="overview"></a>概要

`Microsoft.AspNetCore.Authentication.Certificate` ような実装を含む[証明書認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)ASP.NET Core 用です。 証明書の認証は、ASP.NET Core にこれまで到達する前に、長い、TLS レベルで行われます。 証明書を検証し、しにその証明書を解決できるイベントを提供する認証ハンドラーをより正確には、これは、`ClaimsPrincipal`します。 

[ホストを構成する](#configure-your-host-to-require-certificates)証明書の認証は IIS、Kestrel、Azure Web Apps、またはあらゆる要素を使用します。

## <a name="get-started"></a>作業開始

HTTPS 証明書を取得、それを適用し、 [、ホストを構成する](#configure-your-host-to-require-certificates)証明書を要求します。

Web アプリへの参照を追加、`Microsoft.AspNetCore.Authentication.Certificate`パッケージ。 次に、`Startup.Configure`メソッドを呼び出します`app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);`オプションをご提供のデリゲート`OnCertificateValidated`要求と共に送信されるクライアント証明書の補助、検証を実行します。 その情報を有効にする、`ClaimsPrincipal`に設定し、`context.Principal`プロパティ。

認証が失敗したかどうか、このハンドラーを返します、`403 (Forbidden)`応答ではなく、`401 (Unauthorized)`想像どおり、します。 理由は、初期の TLS 接続中に、認証が発生することです。 ハンドラーに到達するまででは遅すぎます。 証明書のいずれかに匿名接続から接続をアップグレードする方法はありません。

追加も`app.UseAuthentication();`で、`Startup.Configure`メソッド。 それ以外の場合、HttpContext.User は設定されません`ClaimsPrincipal`証明書から作成します。 例えば:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

前の例では、既定の証明書認証を追加する方法を示します。 ハンドラーは、一般的な証明書のプロパティを使用して、ユーザー プリンシパルを作成します。

## <a name="configure-certificate-validation"></a>証明書の検証を構成します。

`CertificateAuthenticationOptions`ハンドラーがいくつか組み込みの検証は、最小の検証証明書でを実行する必要があります。 これらの各設定は、既定で有効です。

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = さまざま、またはすべてのチェーン (チェーン |さまざま)

このチェックは、適切な証明書の種類のみが許可されていることを検証します。

### <a name="validatecertificateuse"></a>ValidateCertificateUse

このチェックは、クライアントによって提示された証明書が、クライアント認証用のキーの使用 (EKU)、またはなしの Eku をすべての拡張を検証します。 ように、仕様と答えると、EKU が指定されていない場合、すべての Eku が有効と見なされます。

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

このチェックでは、その有効期間内で証明書が検証します。 要求ごとに、ハンドラーにより、ことが現在のセッション中に表示されたときに有効であった証明書が期限切れです。

### <a name="revocationflag"></a>RevocationFlag

チェーンの証明書は失効のチェックを指定するフラグ。

失効のチェックが実行されるは、証明書がルート証明書にチェーンされたときにのみです。

### <a name="revocationmode"></a>revocationMode

失効チェックを実行する方法を指定するフラグ。

オンラインのチェックを指定すると、証明機関には接続中に長時間の遅延が発生することができます。

失効のチェックが実行されるは、証明書がルート証明書にチェーンされたときにのみです。

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>特定のパスでのみ証明書を必要とするアプリを構成できますか。

これは、ことはできません。 HTTPS メッセージ交換の開始日は、そのサーバーが要求しているフィールドに基づくスコープにことはできませんので、その接続で、最初の要求が受信されるまで、証明書の交換を実行してください。

## <a name="handler-events"></a>イベントのハンドラー

ハンドラーでは、2 つのイベントがあります。

* `OnAuthenticationFailed` &ndash; 例外が発生した認証時に、対応することができる場合に呼び出されます。
* `OnCertificateValidated` &ndash; 証明書が検証される検証に合格し、既定のプリンシパルが作成された後に呼び出されます。 このイベントを使用すると、独自の検証を実行し、補強またはプリンシパルを置換できます。 例が含まれます。
  * サービスに証明書が既知のかどうかを決定します。
  * 独自のプリンシパルを作成します。 `Startup.ConfigureServices` での次の例を検討してください。

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

受信証明書は、追加の検証を満たしていない場合は、呼び出す`context.Fail("failure reason")`エラー理由を使用します。

実際の機能は、おそらくたいデータベースやその他のユーザー ストアに接続する依存関係の挿入に登録されているサービスを呼び出します。 デリゲートに渡されたコンテキストを使用して、サービスにアクセスします。 `Startup.ConfigureServices` での次の例を検討してください。

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

概念的には、証明書の検証は、承認問題です。 チェックを追加すると、たとえば、発行者または内部ではなく、承認のポリシーで拇印`OnCertificateValidated`は差し支えありません。

## <a name="configure-your-host-to-require-certificates"></a>証明書を要求するように、ホストを構成します。

### <a name="kestrel"></a>Kestrel

*Program.cs*、次のように Kestrel を構成します。

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel(options =>
        {
            options.ConfigureHttpsDefaults(opt => 
                opt.ClientCertificateMode = 
                    ClientCertificateMode.RequireCertificate);
        })
        .Build();
```

### <a name="iis"></a>IIS

IIS マネージャーで次の手順を完了するには。

1. サイトを選択、**接続**タブ。
1. ダブルクリックして、 **SSL 設定**オプション、**機能ビュー**ウィンドウ。
1. チェック、 **SSL が必要** チェック ボックスを選択し、**を必要と**オプション ボタンをクリック、**クライアント証明書**セクション。

![IIS でクライアント証明書の設定](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure とカスタム web プロキシ

参照してください、[ホストし、ドキュメントを配置](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)ミドルウェアを転送する証明書を構成するためです。
