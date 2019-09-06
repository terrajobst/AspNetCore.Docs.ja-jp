---
title: ASP.NET Core での HTTP.sys Web サーバーの実装
author: guardrex
description: Windows 上の ASP.NET Core 用 Web サーバーである HTTP.sys について説明します。 HTTP.sys は、Http.sys カーネル モード ドライバーに基づいて構築された、IIS なしで直接インターネットに接続するために使用できる Kestrel の代替製品です。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: b9adbdd83b3c4e1eeaadcf99fa3ee6cb41f67f8e
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310506"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="e1a35-104">ASP.NET Core での HTTP.sys Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="e1a35-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="e1a35-105">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e1a35-105">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e1a35-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) は、Windows 上でのみ動作する [ASP.NET Core 用 Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="e1a35-107">HTTP.sys は [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代替製品であり、Kestrel では提供されていない機能がいくつか用意されています。</span><span class="sxs-lookup"><span data-stu-id="e1a35-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e1a35-108">HTTP.sys は [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と互換性がなく、IIS や IIS Express で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="e1a35-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="e1a35-109">HTTP.sys は、次の機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="e1a35-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="e1a35-110">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="e1a35-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="e1a35-111">ポート共有</span><span class="sxs-lookup"><span data-stu-id="e1a35-111">Port sharing</span></span>
* <span data-ttu-id="e1a35-112">SNI を使用する HTTPS</span><span class="sxs-lookup"><span data-stu-id="e1a35-112">HTTPS with SNI</span></span>
* <span data-ttu-id="e1a35-113">HTTP/2 over TLS (Windows 10 以降)</span><span class="sxs-lookup"><span data-stu-id="e1a35-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="e1a35-114">直接ファイル伝送</span><span class="sxs-lookup"><span data-stu-id="e1a35-114">Direct file transmission</span></span>
* <span data-ttu-id="e1a35-115">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="e1a35-115">Response caching</span></span>
* <span data-ttu-id="e1a35-116">WebSocket (Windows 8 以降)</span><span class="sxs-lookup"><span data-stu-id="e1a35-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="e1a35-117">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="e1a35-117">Supported Windows versions:</span></span>

* <span data-ttu-id="e1a35-118">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="e1a35-118">Windows 7 or later</span></span>
* <span data-ttu-id="e1a35-119">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="e1a35-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e1a35-120">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e1a35-120">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="e1a35-121">HTTP.sys を使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="e1a35-121">When to use HTTP.sys</span></span>

<span data-ttu-id="e1a35-122">HTTP.sys は、次のような展開に適しています。</span><span class="sxs-lookup"><span data-stu-id="e1a35-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="e1a35-123">IIS を使用せず、インターネットに直接サーバーを公開する必要がある。</span><span class="sxs-lookup"><span data-stu-id="e1a35-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="e1a35-125">内部の展開で、[Windows 認証](xref:security/authentication/windowsauth)などの、Kestrel では使用できない機能が要求されている。</span><span class="sxs-lookup"><span data-stu-id="e1a35-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="e1a35-127">HTTP.sys は、さまざまな種類の攻撃を防ぎ、フル機能の Web サーバーとして堅牢性、セキュリティ、スケーラビリティを提供する、成熟したテクノロジです。</span><span class="sxs-lookup"><span data-stu-id="e1a35-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="e1a35-128">IIS 自体が、HTTP.sys 上で HTTP リスナーとして実行されています。</span><span class="sxs-lookup"><span data-stu-id="e1a35-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e1a35-129">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="e1a35-129">HTTP/2 support</span></span>

<span data-ttu-id="e1a35-130">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリに対して有効になります。</span><span class="sxs-lookup"><span data-stu-id="e1a35-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="e1a35-131">Windows Server 2016/Windows 10 以降</span><span class="sxs-lookup"><span data-stu-id="e1a35-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="e1a35-132">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="e1a35-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="e1a35-133">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="e1a35-133">TLS 1.2 or later connection</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e1a35-134">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e1a35-135">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/1.1` を報告します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="e1a35-136">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="e1a35-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e1a35-137">Http/2 接続が確立されない場合、接続は http/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="e1a35-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="e1a35-138">Windows の今後のリリースで、HTTP.sys で HTTP/2 を無効にする機能を含む HTTP/2 構成フラグが使用可能になる予定です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="e1a35-139">Kerberos を使用したカーネル モード認証</span><span class="sxs-lookup"><span data-stu-id="e1a35-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="e1a35-140">HTTP.sys では、Kerberos 認証プロトコルを使用したカーネル モード認証に処理が委任されます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="e1a35-141">Kerberos および HTTP.sys ではユーザー モード認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="e1a35-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="e1a35-142">Active Directory から取得され、クライアントによって、ユーザーを認証するサーバーに転送される Kerberos トークン/チケットを暗号化解除するには、コンピューター アカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1a35-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="e1a35-143">アプリのユーザーではなく、ホストのサービス プリンシパル名 (SPN) を登録します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="e1a35-144">HTTP.sys の使用方法</span><span class="sxs-lookup"><span data-stu-id="e1a35-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="e1a35-145">HTTP.sys を使用するように ASP.NET Core アプリを構成する</span><span class="sxs-lookup"><span data-stu-id="e1a35-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e1a35-146">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) を使用する場合は、プロジェクト ファイルのパッケージ参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e1a35-146">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="e1a35-147">`Microsoft.AspNetCore.App` メタパッケージを使用しない場合は、[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) にパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-147">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

::: moniker-end

<span data-ttu-id="e1a35-148">ホストを構築するときに <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 拡張メソッドを呼び出し、必要な <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> を指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-148">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="e1a35-149">次の例では、既定値にオプションを設定しています。</span><span class="sxs-lookup"><span data-stu-id="e1a35-149">The following example sets options to their default values:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

::: moniker-end

<span data-ttu-id="e1a35-150">HTTP.sys の追加の構成は、[レジストリ設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)を通じて処理されます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-150">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

   <span data-ttu-id="e1a35-151">**HTTP.sys オプション**</span><span class="sxs-lookup"><span data-stu-id="e1a35-151">**HTTP.sys options**</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="e1a35-152">プロパティ</span><span class="sxs-lookup"><span data-stu-id="e1a35-152">Property</span></span> | <span data-ttu-id="e1a35-153">説明</span><span class="sxs-lookup"><span data-stu-id="e1a35-153">Description</span></span> | <span data-ttu-id="e1a35-154">既定値</span><span class="sxs-lookup"><span data-stu-id="e1a35-154">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="e1a35-155">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="e1a35-155">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="e1a35-156">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-156">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="e1a35-157">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="e1a35-157">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="e1a35-158">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-158">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="e1a35-159">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="e1a35-159">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="e1a35-160">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-160">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="e1a35-161">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-161">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="e1a35-162">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-162">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="e1a35-163">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="e1a35-163">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="e1a35-164">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-164">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="e1a35-165">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-165">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="e1a35-166">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1a35-166">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="e1a35-167">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-167">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="e1a35-168">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="e1a35-168">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="e1a35-169">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-169">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="e1a35-170">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-170">Use `-1` for infinite.</span></span> <span data-ttu-id="e1a35-171">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-171">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="e1a35-172">(無制限)</span><span class="sxs-lookup"><span data-stu-id="e1a35-172">(unlimited)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="e1a35-173">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-173">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="e1a35-174">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="e1a35-174">30000000 bytes</span></span><br><span data-ttu-id="e1a35-175">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="e1a35-175">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="e1a35-176">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="e1a35-176">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="e1a35-177">1000</span><span class="sxs-lookup"><span data-stu-id="e1a35-177">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="e1a35-178">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-178">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="e1a35-179">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="e1a35-179">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="e1a35-180">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-180">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="e1a35-181">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-181">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="e1a35-182">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-182">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="e1a35-183">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 要求のエンティティ本体が到着するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-183">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="e1a35-184">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-184">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="e1a35-185">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 接続で許容されるアイドル時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-185">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="e1a35-186">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 応答の最小の送信率です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-186">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="e1a35-187">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-187">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="e1a35-188">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-188">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="e1a35-189">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-189">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="e1a35-190">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-190">These may be modified at any time prior to disposing the listener.</span></span> |  |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="e1a35-191">プロパティ</span><span class="sxs-lookup"><span data-stu-id="e1a35-191">Property</span></span> | <span data-ttu-id="e1a35-192">説明</span><span class="sxs-lookup"><span data-stu-id="e1a35-192">Description</span></span> | <span data-ttu-id="e1a35-193">既定値</span><span class="sxs-lookup"><span data-stu-id="e1a35-193">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="e1a35-194">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="e1a35-194">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="e1a35-195">`HttpContext.Request.Body` および `HttpContext.Response.Body` に対して、入力/出力の同期を許可するかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-195">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="e1a35-196">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="e1a35-196">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="e1a35-197">匿名要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-197">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="e1a35-198">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="e1a35-198">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="e1a35-199">許可される認証方式を指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-199">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="e1a35-200">リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-200">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="e1a35-201">値は [AuthenticationSchemes 列挙型](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) (`Basic`、`Kerberos`、`Negotiate`、`None`、および `NTLM`) によって指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-201">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="e1a35-202">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="e1a35-202">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="e1a35-203">対象となるヘッダーを持つ応答に対して、[カーネル モード](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)のキャッシュを試行します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-203">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="e1a35-204">`Set-Cookie`、`Vary`、または `Pragma` ヘッダーを含む応答は対象外です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-204">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="e1a35-205">応答は、`public` である `Cache-Control` ヘッダーと `shared-max-age` または `max-age` の値のいずれかを含むか、または `Expires` ヘッダーを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1a35-205">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="e1a35-206">同時受け入れの最大数です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-206">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="e1a35-207">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="e1a35-207">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="e1a35-208">受け入れるコンカレント接続の最大数です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-208">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="e1a35-209">無限にするには、`-1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-209">Use `-1` for infinite.</span></span> <span data-ttu-id="e1a35-210">コンピューター全体のレジストリ設定を使用するには、`null` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-210">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="e1a35-211">(無制限)</span><span class="sxs-lookup"><span data-stu-id="e1a35-211">(unlimited)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="e1a35-212">「<a href="#maxrequestbodysize">MaxRequestBodySize</a>」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-212">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="e1a35-213">30000000 バイト</span><span class="sxs-lookup"><span data-stu-id="e1a35-213">30000000 bytes</span></span><br><span data-ttu-id="e1a35-214">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="e1a35-214">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="e1a35-215">キューに置くことができる要求の最大数。</span><span class="sxs-lookup"><span data-stu-id="e1a35-215">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="e1a35-216">1000</span><span class="sxs-lookup"><span data-stu-id="e1a35-216">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="e1a35-217">応答本文の書き込みがクライアントの接続の切断によって失敗した場合、例外をスローするか、または正常に完了するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-217">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="e1a35-218">(正常に完了する)</span><span class="sxs-lookup"><span data-stu-id="e1a35-218">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="e1a35-219">HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 構成を公開します。これはレジストリでも構成できます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-219">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="e1a35-220">各設定に関する既定値などの詳細については、API のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-220">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="e1a35-221">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP サーバー API が Keep-Alive 接続でエンティティ本体をドレインするまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-221">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="e1a35-222">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 要求のエンティティ本体が到着するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-222">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="e1a35-223">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP サーバー API が要求ヘッダーを解析するまでに許容される時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-223">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="e1a35-224">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 接続で許容されるアイドル時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-224">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="e1a35-225">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 応答の最小の送信率です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-225">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="e1a35-226">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 要求が、アプリにピック アップされるまでに要求キューの中に留まっていられる時間です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-226">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="e1a35-227">HTTP.sys に登録する <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> を指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-227">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="e1a35-228">最も便利なのは [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) です。これを使用して、コレクションにプレフィックスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-228">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="e1a35-229">これらは、リスナーを破棄する前ならいつでも変更できます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-229">These may be modified at any time prior to disposing the listener.</span></span> |  |

::: moniker-end

<a name="maxrequestbodysize"></a>

<span data-ttu-id="e1a35-230">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="e1a35-230">**MaxRequestBodySize**</span></span>

<span data-ttu-id="e1a35-231">要求本文の最大許容サイズ (バイト単位) です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-231">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="e1a35-232">`null` に設定する場合、要求本文の最大サイズは制限されません。</span><span class="sxs-lookup"><span data-stu-id="e1a35-232">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="e1a35-233">この制限は、アップグレード済みの接続 (常に無制限) には影響しません。</span><span class="sxs-lookup"><span data-stu-id="e1a35-233">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="e1a35-234">1 つの `IActionResult` に対する ASP.NET Core MVC アプリの制限をオーバーライドする方法として、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e1a35-234">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="e1a35-235">アプリが要求の読み取りを開始した後に、アプリが要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-235">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="e1a35-236">`IsReadOnly` プロパティを使用して、`MaxRequestBodySize` プロパティが読み取り専用状態にあるかどうか、つまり制限を構成するには遅すぎるかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-236">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="e1a35-237">要求ごとにアプリで <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> をオーバーライドする必要がある場合は、<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="e1a35-237">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

::: moniker-end

<span data-ttu-id="e1a35-238">Visual Studio を使用する場合は、アプリが IIS または IIS Express を実行するように構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-238">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="e1a35-239">Visual Studio では、既定の起動プロファイルは IIS Express 用です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-239">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="e1a35-240">プロジェクトをコンソール アプリとして実行するには、次のスクリーン ショットに示すように、選択したプロファイルを手動で変更します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-240">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![コンソール アプリのプロファイルを選択する](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="e1a35-242">Windows Server を構成する</span><span class="sxs-lookup"><span data-stu-id="e1a35-242">Configure Windows Server</span></span>

1. <span data-ttu-id="e1a35-243">アプリに対して開くポートを決めたら、[Windows ファイアウォール](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)か [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell コマンドレットを使用して、トラフィックが HTTP.sys に到達できるようにファイアウォールのポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-243">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="e1a35-244">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-244">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e1a35-245">Azure VM に展開する場合は、[ネットワーク セキュリティ グループ](/azure/virtual-machines/windows/nsg-quickstart-portal)内でポートを開きます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-245">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="e1a35-246">次のコマンドとアプリの構成では、ポート 443 を使用します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-246">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e1a35-247">必要に応じて、X.509 証明書を取得してインストールします。</span><span class="sxs-lookup"><span data-stu-id="e1a35-247">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="e1a35-248">Windows の場合は、[New-SelfSignedCertificate PowerShell コマンドレット](/powershell/module/pkiclient/new-selfsignedcertificate)を使用して自己署名証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-248">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="e1a35-249">サポート対象外の例については、[UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-249">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="e1a35-250">自己署名証明書か CA 署名証明書のいずれかをサーバーの **Local Machine** > **Personal** ストアにインストールします。</span><span class="sxs-lookup"><span data-stu-id="e1a35-250">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="e1a35-251">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)である場合は、.NET Core、.NET Framework、またはその両方 (アプリが .NET Framework をターゲットとする .NET Core アプリである場合) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="e1a35-251">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="e1a35-252">**.NET Core** &ndash; アプリで .NET Core が必要な場合は、[.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから **.NET Core Runtime** インストーラーを取得して実行します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-252">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="e1a35-253">サーバーに SDK 全体をインストールしないでください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-253">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="e1a35-254">**.NET Framework** &ndash; アプリで .NET Framework が必要な場合は、[.NET Framework のインストール ガイド](/dotnet/framework/install/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-254">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="e1a35-255">必要な .NET Framework をインストールします。</span><span class="sxs-lookup"><span data-stu-id="e1a35-255">Install the required .NET Framework.</span></span> <span data-ttu-id="e1a35-256">最新の .NET Framework のインストーラーは [.NET Core のダウンロード](https://dotnet.microsoft.com/download) ページから入手できます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-256">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="e1a35-257">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、アプリの展開内にランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e1a35-257">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="e1a35-258">サーバーにフレームワークをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e1a35-258">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="e1a35-259">アプリに URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-259">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="e1a35-260">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-260">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e1a35-261">URL プレフィックスとポートを構成するには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="e1a35-261">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="e1a35-262">`urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="e1a35-262">`urls` command-line argument</span></span>
   * <span data-ttu-id="e1a35-263">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="e1a35-263">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="e1a35-264">次のコード例は、サーバーのローカル IP アドレス `10.0.0.4` を使ってポート 443 上で <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> を使う方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="e1a35-264">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

::: moniker range=">= aspnetcore-3.0"

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

::: moniker-end

   <span data-ttu-id="e1a35-265">`UrlPrefixes` の利点は、プレフィックスの形式が正しくなかった場合、すぐにエラー メッセージが生成されることです。</span><span class="sxs-lookup"><span data-stu-id="e1a35-265">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="e1a35-266">`UrlPrefixes` の設定は `UseUrls`/`urls`/`ASPNETCORE_URLS` の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="e1a35-266">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="e1a35-267">したがって、`UseUrls`、`urls`、および `ASPNETCORE_URLS` 環境変数の利点は、Kestrel と HTTP.sys を簡単に切り替えられることです。</span><span class="sxs-lookup"><span data-stu-id="e1a35-267">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="e1a35-268">HTTP.sys では、[HTTP サーバー API の UrlPrefix 文字列形式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-268">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="e1a35-269">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="e1a35-269">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e1a35-270">最上位のワイルドカードのバインドを使用すると、アプリにセキュリティの脆弱性が生じます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-270">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="e1a35-271">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-271">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e1a35-272">ワイルドカードではなく、明示的なホスト名か IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-272">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="e1a35-273">親ドメイン全体を制御する場合、サブドメインのワイルドカードのバインド (たとえば、`*.mysub.com`) がセキュリティ リスクになることはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="e1a35-273">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e1a35-274">詳細については、[RFC 7230:セクション 5.4:ホスト](https://tools.ietf.org/html/rfc7230#section-5.4)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-274">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="e1a35-275">サーバーで URL プレフィックスを事前登録します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-275">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="e1a35-276">HTTP.sys を構成するための組み込みツールは、*netsh.exe* です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-276">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="e1a35-277">*netsh.exe* を使用して、URL プレフィックスを予約し、X.509 証明書を割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-277">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="e1a35-278">ツールを使用するには管理者特権が必要です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-278">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="e1a35-279">*netsh.exe* ツールを使用して、アプリ用に URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-279">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="e1a35-280">`<URL>` &ndash; 完全修飾 URL (Uniform Resource Locator)。</span><span class="sxs-lookup"><span data-stu-id="e1a35-280">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="e1a35-281">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-281">Don't use a wildcard binding.</span></span> <span data-ttu-id="e1a35-282">有効なホスト名かローカル IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-282">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="e1a35-283">"*URL の末尾にはスラッシュが必要です。* "</span><span class="sxs-lookup"><span data-stu-id="e1a35-283">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="e1a35-284">`<USER>` &ndash; ユーザーまたはユーザー グループの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-284">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="e1a35-285">次の例では、サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-285">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="e1a35-286">URL が登録されると、ツールから `URL reservation successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="e1a35-286">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="e1a35-287">登録済みの URL を削除するには、`delete urlacl` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-287">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="e1a35-288">サーバーで X.509 証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-288">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="e1a35-289">*netsh.exe* ツールを使用して、アプリ用の証明書を登録します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-289">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="e1a35-290">`<IP>` &ndash; バインド用のローカル IP アドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-290">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="e1a35-291">ワイルドカードのバインドは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-291">Don't use a wildcard binding.</span></span> <span data-ttu-id="e1a35-292">有効な IP アドレスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-292">Use a valid IP address.</span></span>
   * <span data-ttu-id="e1a35-293">`<PORT>` &ndash; バインド用のポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-293">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="e1a35-294">`<THUMBPRINT>` &ndash; X.509 証明書の拇印です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-294">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="e1a35-295">`<GUID>` &ndash; 情報提供を目的として開発者によって生成された、アプリを表す GUID です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-295">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="e1a35-296">参照用に、この GUID をパッケージ タグとしてアプリに格納します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-296">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="e1a35-297">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e1a35-297">In Visual Studio:</span></span>
     * <span data-ttu-id="e1a35-298">**ソリューション エクスプローラー**内でアプリを右クリックし、 **[プロパティ]** をクリックして、アプリのプロジェクト プロパティを開きます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-298">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="e1a35-299">**[パッケージ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-299">Select the **Package** tab.</span></span>
     * <span data-ttu-id="e1a35-300">作成した GUID を **[タグ]** フィールドに入力します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-300">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="e1a35-301">Visual Studio を使用しない場合:</span><span class="sxs-lookup"><span data-stu-id="e1a35-301">When not using Visual Studio:</span></span>
     * <span data-ttu-id="e1a35-302">アプリのプロジェクト ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-302">Open the app's project file.</span></span>
     * <span data-ttu-id="e1a35-303">作成した GUID を指定した `<PackageTags>` プロパティを、新規または既存の `<PropertyGroup>` に追加します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-303">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="e1a35-304">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-304">In the following example:</span></span>

   * <span data-ttu-id="e1a35-305">サーバーのローカル IP アドレスは `10.0.0.4` です。</span><span class="sxs-lookup"><span data-stu-id="e1a35-305">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="e1a35-306">オンラインのランダム GUID ジェネレーターによって、`appid` の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-306">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="e1a35-307">証明書が登録されると、ツールから `SSL Certificate successfully added` という応答があります。</span><span class="sxs-lookup"><span data-stu-id="e1a35-307">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="e1a35-308">証明書の登録を削除するには、`delete sslcert` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-308">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="e1a35-309">以下は、*netsh.exe* のリファレンス ドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="e1a35-309">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="e1a35-310">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (ハイパーテキスト転送プロトコル (HTTP) 用の Netsh コマンド)</span><span class="sxs-lookup"><span data-stu-id="e1a35-310">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="e1a35-311">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (UrlPrefix 文字列)</span><span class="sxs-lookup"><span data-stu-id="e1a35-311">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="e1a35-312">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-312">Run the app.</span></span>

   <span data-ttu-id="e1a35-313">1024 より大きいポート番号で (HTTPS ではなく) HTTP を使用して localhost にバインドする場合、アプリの実行に管理者権限は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e1a35-313">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="e1a35-314">その他の構成の場合 (たとえば、ローカル IP アドレスを使用する場合やポート 443 にバインドする場合)、管理者権限でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-314">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="e1a35-315">サーバーのパブリック IP アドレスでアプリが応答します。</span><span class="sxs-lookup"><span data-stu-id="e1a35-315">The app responds at the server's public IP address.</span></span> <span data-ttu-id="e1a35-316">この例では、サーバーは自身のパブリック IP アドレス `104.214.79.47` でインターネットからアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-316">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="e1a35-317">この例では開発証明書が使用されています。</span><span class="sxs-lookup"><span data-stu-id="e1a35-317">A development certificate is used in this example.</span></span> <span data-ttu-id="e1a35-318">証明書が信頼できないというブラウザーの警告がバイパスされた後に、ページが安全に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e1a35-318">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![読み込まれたアプリのインデックス ページを表示するブラウザー ウィンドウ](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e1a35-320">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="e1a35-320">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e1a35-321">インターネットや企業ネットワークからの要求とやりとりする HTTP.sys でホストされるアプリの場合、プロキシ サーバーやロード バランサーの背後でホストするとき、追加の構成が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="e1a35-321">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="e1a35-322">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1a35-322">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1a35-323">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e1a35-323">Additional resources</span></span>

* [<span data-ttu-id="e1a35-324">HTTP.sys を使用して Windows 認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="e1a35-324">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="e1a35-325">HTTP サーバー API</span><span class="sxs-lookup"><span data-stu-id="e1a35-325">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="e1a35-326">aspnet/HttpSysServer GitHub リポジトリ (ソース コード)</span><span class="sxs-lookup"><span data-stu-id="e1a35-326">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="e1a35-327">ホスト</span><span class="sxs-lookup"><span data-stu-id="e1a35-327">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>
