---
title: ASP.NET Core で証明書認証を構成する
author: blowdart
description: IIS と http.sys の ASP.NET Core で証明書認証を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 11/05/2019
uid: security/authentication/certauth
ms.openlocfilehash: 081935e6e6248b5fe9b7bf4cd966dc73761d2ec1
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634050"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>ASP.NET Core で証明書認証を構成する

`Microsoft.AspNetCore.Authentication.Certificate` には、ASP.NET Core の[証明書認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)と同様の実装が含まれています。 証明書の認証は、TLS レベルで実行されますが、その前に ASP.NET Core になります。 より正確には、これは証明書を検証し、その証明書を `ClaimsPrincipal`に解決できるイベントを提供する認証ハンドラーです。 

証明書認証用に[ホストを構成](#configure-your-host-to-require-certificates)します。これは、IIS、Kestrel、Azure Web Apps など、使用している他のすべてのものになります。

## <a name="proxy-and-load-balancer-scenarios"></a>プロキシとロードバランサーのシナリオ

証明書認証は、主に、プロキシまたはロードバランサーがクライアントとサーバー間のトラフィックを処理しない場合に使用されるステートフルシナリオです。 プロキシまたはロードバランサーが使用されている場合、証明書の認証はプロキシまたはロードバランサーの場合にのみ機能します。

* 認証を処理します。
* 認証情報に対して動作するユーザー認証情報をアプリに渡します (要求ヘッダーなど)。

プロキシとロードバランサーを使用する環境での証明書認証の代わりに、OpenID Connect (OIDC) を使用したフェデレーションサービス (ADFS) Active Directory ます。

## <a name="get-started"></a>作業開始

HTTPS 証明書を取得して適用し、証明書を要求するように[ホストを構成](#configure-your-host-to-require-certificates)します。

Web アプリで、`Microsoft.AspNetCore.Authentication.Certificate` パッケージへの参照を追加します。 次に、`Startup.ConfigureServices` メソッドで、`services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` をオプションと共に呼び出し、要求と共に送信されるクライアント証明書に対して補助的な検証を行うための `OnCertificateValidated` のデリゲートを提供します。 その情報を `ClaimsPrincipal` に変換し、`context.Principal` プロパティに設定します。

認証が失敗した場合、このハンドラーは `401 (Unauthorized)`ではなく `403 (Forbidden)` 応答を返します。 これは、最初の TLS 接続中に認証が行われるということです。 ハンドラーに到達するまでには遅すぎます。 匿名接続から証明書を使用して接続をアップグレードする方法はありません。

また、`Startup.Configure` メソッドに `app.UseAuthentication();` を追加します。 それ以外の場合、HttpContext は証明書から作成された `ClaimsPrincipal` に設定されません。 (例:

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

前の例では、証明書認証を追加する既定の方法を示しています。 ハンドラーは、共通の証明書のプロパティを使用してユーザープリンシパルを構築します。

## <a name="configure-certificate-validation"></a>証明書の検証の構成

`CertificateAuthenticationOptions` ハンドラーには、証明書に対して実行する必要のある検証の最小値である組み込みの検証がいくつかあります。 これらの各設定は既定で有効になっています。

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = チェーン、自己署名、またはすべて (チェーン |自己署名済み)

このチェックでは、適切な証明書の種類のみが許可されていることが検証されます。

### <a name="validatecertificateuse"></a>ValidateCertificateUse

このチェックでは、クライアントが提示した証明書にクライアント認証の拡張キー使用法 (EKU) があること、またはまったく Eku がないことを検証します。 仕様として、EKU が指定されていない場合は、すべての Eku が有効と見なされます。

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

このチェックでは、証明書が有効期間内であることを検証します。 要求が発生するたびに、ハンドラーは、提示されたときに有効だった証明書が現在のセッション中に期限切れにならないようにします。

### <a name="revocationflag"></a>RevocationFlag

チェーン内のどの証明書の失効を確認するかを指定するフラグ。

失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。

### <a name="revocationmode"></a>RevocationMode

失効確認の実行方法を指定するフラグ。

オンラインチェックを指定すると、証明機関への接続中に長い遅延が発生する可能性があります。

失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>特定のパスでのみ証明書を要求するようにアプリを構成することはできますか。

これはできません。 証明書の交換は、HTTPS メッセージ交換の開始時に行われます。これは、その接続で最初の要求を受信する前にサーバーによって行われるため、要求フィールドに基づいてスコープを設定することはできません。

## <a name="handler-events"></a>ハンドラーイベント

ハンドラーには、次の2つのイベントがあります。

* `OnAuthenticationFailed` &ndash;、認証中に例外が発生した場合に呼び出されます。
* 証明書が検証され、検証が渡され、既定のプリンシパルが作成された後に呼び出される `OnCertificateValidated` &ndash;。 このイベントを使用すると、独自の検証を実行したり、プリンシパルを補強したり置き換えることができます。 例を次に示します。
  * 証明書がサービスに対して認識されているかどうかを確認しています。
  * 独自のプリンシパルを構築します。 `Startup.ConfigureServices` での次の例を検討してください。

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

受信証明書が追加の検証に合わない場合は、失敗の理由で `context.Fail("failure reason")` を呼び出します。

実際の機能の場合は、データベースまたはその他の種類のユーザーストアに接続する依存関係の挿入に登録されているサービスを呼び出す必要があります。 デリゲートに渡されたコンテキストを使用してサービスにアクセスします。 `Startup.ConfigureServices` での次の例を検討してください。

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

概念的には、証明書の検証は承認に関する問題です。 たとえば、`OnCertificateValidated`内部ではなく、承認ポリシーの発行者や拇印などのチェックを追加することは、まったく可能です。

## <a name="configure-your-host-to-require-certificates"></a>証明書を要求するようにホストを構成する

### <a name="kestrel"></a>Kestrel

*Program.cs*で、次のように Kestrel を構成します。

```csharp

public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                    webBuilder.ConfigureKestrel(o =>
                    {
                        o.ConfigureHttpsDefaults(o => o.ClientCertificateMode = ClientCertificateMode.RequireCertificate);
                    });
                });
}
```

### <a name="iis"></a>IIS

IIS マネージャーで、次の手順を実行します。

1. **[接続]** タブからサイトを選択します。
1. **[機能ビュー]** ウィンドウで、 **[SSL 設定]** オプションをダブルクリックします。
1. **[SSL が必要]** チェックボックスをオンにし、 **[クライアント証明書]** セクションの **[必須]** オプションを選択します。

![IIS でのクライアント証明書の設定](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure とカスタム web プロキシ

証明書転送ミドルウェアを構成する方法については、[ホストを参照し、ドキュメントを展開](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)してください。
