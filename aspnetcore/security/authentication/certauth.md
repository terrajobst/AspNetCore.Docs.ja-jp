---
title: ASP.NET Core で証明書認証を構成する
author: blowdart
description: IIS と http.sys の ASP.NET Core で証明書認証を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 08/19/2019
uid: security/authentication/certauth
ms.openlocfilehash: 1e646aabb4e384e6906575e7beaa680e91f968a0
ms.sourcegitcommit: e5d4768aaf85703effb4557a520d681af8284e26
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616579"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="98b67-103">ASP.NET Core で証明書認証を構成する</span><span class="sxs-lookup"><span data-stu-id="98b67-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="98b67-104">`Microsoft.AspNetCore.Authentication.Certificate` には、ASP.NET Core の[証明書認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)と同様の実装が含まれています。</span><span class="sxs-lookup"><span data-stu-id="98b67-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="98b67-105">証明書の認証は、TLS レベルで実行されますが、その前に ASP.NET Core になります。</span><span class="sxs-lookup"><span data-stu-id="98b67-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="98b67-106">より正確には、これは証明書を検証し、その証明書を `ClaimsPrincipal`に解決できるイベントを提供する認証ハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="98b67-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="98b67-107">証明書認証用に[ホストを構成](#configure-your-host-to-require-certificates)します。これは、IIS、Kestrel、Azure Web Apps など、使用している他のすべてのものになります。</span><span class="sxs-lookup"><span data-stu-id="98b67-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="98b67-108">プロキシとロードバランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="98b67-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="98b67-109">証明書認証は、主に、プロキシまたはロードバランサーがクライアントとサーバー間のトラフィックを処理しない場合に使用されるステートフルシナリオです。</span><span class="sxs-lookup"><span data-stu-id="98b67-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="98b67-110">プロキシまたはロードバランサーが使用されている場合、証明書の認証はプロキシまたはロードバランサーの場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="98b67-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="98b67-111">認証を処理します。</span><span class="sxs-lookup"><span data-stu-id="98b67-111">Handles the authentication.</span></span>
* <span data-ttu-id="98b67-112">認証情報に対して動作するユーザー認証情報をアプリに渡します (要求ヘッダーなど)。</span><span class="sxs-lookup"><span data-stu-id="98b67-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="98b67-113">プロキシとロードバランサーを使用する環境での証明書認証の代わりに、OpenID Connect (OIDC) を使用したフェデレーションサービス (ADFS) Active Directory ます。</span><span class="sxs-lookup"><span data-stu-id="98b67-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="98b67-114">作業開始</span><span class="sxs-lookup"><span data-stu-id="98b67-114">Get started</span></span>

<span data-ttu-id="98b67-115">HTTPS 証明書を取得して適用し、証明書を要求するように[ホストを構成](#configure-your-host-to-require-certificates)します。</span><span class="sxs-lookup"><span data-stu-id="98b67-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="98b67-116">Web アプリで、`Microsoft.AspNetCore.Authentication.Certificate` パッケージへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="98b67-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="98b67-117">次に、`Startup.ConfigureServices` メソッドで、`services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` をオプションと共に呼び出し、要求と共に送信されるクライアント証明書に対して補助的な検証を行うための `OnCertificateValidated` のデリゲートを提供します。</span><span class="sxs-lookup"><span data-stu-id="98b67-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="98b67-118">その情報を `ClaimsPrincipal` に変換し、`context.Principal` プロパティに設定します。</span><span class="sxs-lookup"><span data-stu-id="98b67-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="98b67-119">認証が失敗した場合、このハンドラーは `401 (Unauthorized)`ではなく `403 (Forbidden)` 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="98b67-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="98b67-120">これは、最初の TLS 接続中に認証が行われるということです。</span><span class="sxs-lookup"><span data-stu-id="98b67-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="98b67-121">ハンドラーに到達するまでには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="98b67-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="98b67-122">匿名接続から証明書を使用して接続をアップグレードする方法はありません。</span><span class="sxs-lookup"><span data-stu-id="98b67-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="98b67-123">また、`Startup.Configure` メソッドに `app.UseAuthentication();` を追加します。</span><span class="sxs-lookup"><span data-stu-id="98b67-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="98b67-124">それ以外の場合、HttpContext は証明書から作成された `ClaimsPrincipal` に設定されません。</span><span class="sxs-lookup"><span data-stu-id="98b67-124">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="98b67-125">(例:</span><span class="sxs-lookup"><span data-stu-id="98b67-125">For example:</span></span>

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

<span data-ttu-id="98b67-126">前の例では、証明書認証を追加する既定の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="98b67-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="98b67-127">ハンドラーは、共通の証明書のプロパティを使用してユーザープリンシパルを構築します。</span><span class="sxs-lookup"><span data-stu-id="98b67-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="98b67-128">証明書の検証の構成</span><span class="sxs-lookup"><span data-stu-id="98b67-128">Configure certificate validation</span></span>

<span data-ttu-id="98b67-129">`CertificateAuthenticationOptions` ハンドラーには、証明書に対して実行する必要のある検証の最小値である組み込みの検証がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="98b67-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="98b67-130">これらの各設定は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="98b67-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="98b67-131">AllowedCertificateTypes = チェーン、自己署名、またはすべて (チェーン |自己署名済み)</span><span class="sxs-lookup"><span data-stu-id="98b67-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="98b67-132">このチェックでは、適切な証明書の種類のみが許可されていることが検証されます。</span><span class="sxs-lookup"><span data-stu-id="98b67-132">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="98b67-133">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="98b67-133">ValidateCertificateUse</span></span>

<span data-ttu-id="98b67-134">このチェックでは、クライアントが提示した証明書にクライアント認証の拡張キー使用法 (EKU) があること、またはまったく Eku がないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="98b67-134">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="98b67-135">仕様として、EKU が指定されていない場合は、すべての Eku が有効と見なされます。</span><span class="sxs-lookup"><span data-stu-id="98b67-135">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="98b67-136">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="98b67-136">ValidateValidityPeriod</span></span>

<span data-ttu-id="98b67-137">このチェックでは、証明書が有効期間内であることを検証します。</span><span class="sxs-lookup"><span data-stu-id="98b67-137">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="98b67-138">要求が発生するたびに、ハンドラーは、提示されたときに有効だった証明書が現在のセッション中に期限切れにならないようにします。</span><span class="sxs-lookup"><span data-stu-id="98b67-138">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="98b67-139">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="98b67-139">RevocationFlag</span></span>

<span data-ttu-id="98b67-140">チェーン内のどの証明書の失効を確認するかを指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="98b67-140">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="98b67-141">失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="98b67-141">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="98b67-142">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="98b67-142">RevocationMode</span></span>

<span data-ttu-id="98b67-143">失効確認の実行方法を指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="98b67-143">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="98b67-144">オンラインチェックを指定すると、証明機関への接続中に長い遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="98b67-144">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="98b67-145">失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="98b67-145">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="98b67-146">特定のパスでのみ証明書を要求するようにアプリを構成することはできますか。</span><span class="sxs-lookup"><span data-stu-id="98b67-146">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="98b67-147">これはできません。</span><span class="sxs-lookup"><span data-stu-id="98b67-147">This isn't possible.</span></span> <span data-ttu-id="98b67-148">証明書の交換は、HTTPS メッセージ交換の開始時に行われます。これは、その接続で最初の要求を受信する前にサーバーによって行われるため、要求フィールドに基づいてスコープを設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="98b67-148">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="98b67-149">ハンドラーイベント</span><span class="sxs-lookup"><span data-stu-id="98b67-149">Handler events</span></span>

<span data-ttu-id="98b67-150">ハンドラーには、次の2つのイベントがあります。</span><span class="sxs-lookup"><span data-stu-id="98b67-150">The handler has two events:</span></span>

* <span data-ttu-id="98b67-151">`OnAuthenticationFailed` &ndash;、認証中に例外が発生した場合に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="98b67-151">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="98b67-152">証明書が検証され、検証が渡され、既定のプリンシパルが作成された後に呼び出される `OnCertificateValidated` &ndash;。</span><span class="sxs-lookup"><span data-stu-id="98b67-152">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="98b67-153">このイベントを使用すると、独自の検証を実行したり、プリンシパルを補強したり置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="98b67-153">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="98b67-154">例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="98b67-154">For examples include:</span></span>
  * <span data-ttu-id="98b67-155">証明書がサービスに対して認識されているかどうかを確認しています。</span><span class="sxs-lookup"><span data-stu-id="98b67-155">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="98b67-156">独自のプリンシパルを構築します。</span><span class="sxs-lookup"><span data-stu-id="98b67-156">Constructing your own principal.</span></span> <span data-ttu-id="98b67-157">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="98b67-157">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="98b67-158">受信証明書が追加の検証に合わない場合は、失敗の理由で `context.Fail("failure reason")` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="98b67-158">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="98b67-159">実際の機能の場合は、データベースまたはその他の種類のユーザーストアに接続する依存関係の挿入に登録されているサービスを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="98b67-159">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="98b67-160">デリゲートに渡されたコンテキストを使用してサービスにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="98b67-160">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="98b67-161">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="98b67-161">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="98b67-162">概念的には、証明書の検証は承認に関する問題です。</span><span class="sxs-lookup"><span data-stu-id="98b67-162">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="98b67-163">たとえば、`OnCertificateValidated`内部ではなく、承認ポリシーの発行者や拇印などのチェックを追加することは、まったく可能です。</span><span class="sxs-lookup"><span data-stu-id="98b67-163">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="98b67-164">証明書を要求するようにホストを構成する</span><span class="sxs-lookup"><span data-stu-id="98b67-164">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="98b67-165">Kestrel</span><span class="sxs-lookup"><span data-stu-id="98b67-165">Kestrel</span></span>

<span data-ttu-id="98b67-166">*Program.cs*で、次のように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="98b67-166">In *Program.cs*, configure Kestrel as follows:</span></span>

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

### <a name="iis"></a><span data-ttu-id="98b67-167">IIS</span><span class="sxs-lookup"><span data-stu-id="98b67-167">IIS</span></span>

<span data-ttu-id="98b67-168">IIS マネージャーで、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="98b67-168">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="98b67-169">**[接続]** タブからサイトを選択します。</span><span class="sxs-lookup"><span data-stu-id="98b67-169">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="98b67-170">**[機能ビュー]** ウィンドウで、 **[SSL 設定]** オプションをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="98b67-170">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="98b67-171">**[SSL が必要]** チェックボックスをオンにし、 **[クライアント証明書]** セクションの **[必須]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="98b67-171">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS でのクライアント証明書の設定](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="98b67-173">Azure とカスタム web プロキシ</span><span class="sxs-lookup"><span data-stu-id="98b67-173">Azure and custom web proxies</span></span>

<span data-ttu-id="98b67-174">証明書転送ミドルウェアを構成する方法については、[ホストを参照し、ドキュメントを展開](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)してください。</span><span class="sxs-lookup"><span data-stu-id="98b67-174">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
