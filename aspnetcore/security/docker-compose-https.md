---
title: HTTPS で構成ASP.NETドッカーを使用してコンテナー内のコア イメージをホストします。
author: ravipal
description: HTTPS 経由での Docker Compose を使用してASP.NETコア イメージをホストする方法を学びます
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381810"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="f6b2d-103">HTTPS 経由での docker Compose を使用したASP.NETコア イメージのホスティング</span><span class="sxs-lookup"><span data-stu-id="f6b2d-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="f6b2d-104">ASP.NETコアは[デフォルトで HTTPS](/aspnet/core/security/enforcing-ssl)を使用します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="f6b2d-105">[HTTPS は](https://en.wikipedia.org/wiki/HTTPS)、信頼、ID、および暗号化のために[証明書](https://en.wikipedia.org/wiki/Public_key_certificate)に依存します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="f6b2d-106">このドキュメントでは、HTTPS で事前に構築されたコンテナー イメージを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="f6b2d-107">開発シナリオについては[、「httpS 経由での docker を使用したASP.NETコア アプリケーション](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)の開発」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="f6b2d-108">このサンプルには[、Docker 17.06](https://docs.docker.com/release-notes/docker-ce)以降の[Docker クライアント](https://www.docker.com/products/docker)が必要です。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f6b2d-109">前提条件</span><span class="sxs-lookup"><span data-stu-id="f6b2d-109">Prerequisites</span></span>

<span data-ttu-id="f6b2d-110">このドキュメントの手順の一部には[、.NET Core 2.2 SDK](https://dotnet.microsoft.com/download)以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="f6b2d-111">証明書</span><span class="sxs-lookup"><span data-stu-id="f6b2d-111">Certificates</span></span>

<span data-ttu-id="f6b2d-112">ドメインの[運用ホスティング](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)には[、証明機関](https://wikipedia.org/wiki/Certificate_authority)からの証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="f6b2d-113">[Let's Encrypt](https://letsencrypt.org/)は、無料の証明書を提供する証明機関です。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="f6b2d-114">このドキュメントでは、事前に作成されたイメージを ホストするための[自己署名](https://wikipedia.org/wiki/Self-signed_certificate)開発`localhost`証明書を使用します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="f6b2d-115">手順は、実稼働証明書の使用に似ています。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="f6b2d-116">運用証明書の場合:</span><span class="sxs-lookup"><span data-stu-id="f6b2d-116">For production certificates:</span></span>

* <span data-ttu-id="f6b2d-117">この`dotnet dev-certs`ツールは必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="f6b2d-118">証明書は、手順で使用されている場所に保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="f6b2d-119">証明書は、サイト ディレクトリの外部の任意の場所に格納します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="f6b2d-120">次のセクションに含まれる手順は`volumes`*、docker-compose.yml*のプロパティを使用して、コンテナーに証明書をマウントします。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="f6b2d-121">`COPY` *Dockerfile*のコマンドを使用してコンテナー イメージに証明書を追加することもできますが、これはお勧めできません。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="f6b2d-122">次の理由により、証明書をイメージにコピーすることはお勧めできません。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="f6b2d-123">開発者証明書を使用してテストする場合、同じイメージを使用することは困難です。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="f6b2d-124">本番証明書を使用してホスティングに同じイメージを使用することは困難です。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="f6b2d-125">証明書の漏洩の重大なリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="f6b2d-126">ドッカー構成を使用した https サポートを使用したコンテナーの開始</span><span class="sxs-lookup"><span data-stu-id="f6b2d-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="f6b2d-127">オペレーティング システムの構成に応じ、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="f6b2d-128">Linux コンテナを使用するウィンドウ</span><span class="sxs-lookup"><span data-stu-id="f6b2d-128">Windows using Linux containers</span></span>

<span data-ttu-id="f6b2d-129">証明書を生成し、ローカル マシンを構成します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="f6b2d-130">上記のコマンドで、パスワードで`{ password here }`置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="f6b2d-131">次の内容を含む_ドッカー構成.debug.yml_ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="f6b2d-132">docker compose ファイルで指定されたパスワードは、証明書に使用されるパスワードと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="f6b2d-133">HTTPS 用に構成されたASP.NETコアでコンテナーを開始します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="f6b2d-134">macOS または Linux</span><span class="sxs-lookup"><span data-stu-id="f6b2d-134">macOS or Linux</span></span>

<span data-ttu-id="f6b2d-135">証明書を生成し、ローカル マシンを構成します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="f6b2d-136">`dotnet dev-certs https --trust`は macOS と Windows でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="f6b2d-137">ディストリビューションでサポートされている方法で Linux 上の証明書を信頼する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-137">You need to trust certificates on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="f6b2d-138">ブラウザで証明書を信頼する必要がある可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="f6b2d-139">上記のコマンドで、パスワードで`{ password here }`置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="f6b2d-140">次の内容を含む_ドッカー構成.debug.yml_ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="f6b2d-141">docker compose ファイルで指定されたパスワードは、証明書に使用されるパスワードと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="f6b2d-142">HTTPS 用に構成されたASP.NETコアでコンテナーを開始します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="f6b2d-143">Windows コンテナを使用するウィンドウ</span><span class="sxs-lookup"><span data-stu-id="f6b2d-143">Windows using Windows containers</span></span>

<span data-ttu-id="f6b2d-144">証明書を生成し、ローカル マシンを構成します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="f6b2d-145">上記のコマンドで、パスワードで`{ password here }`置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="f6b2d-146">次の内容を含む_ドッカー構成.debug.yml_ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="f6b2d-147">docker compose ファイルで指定されたパスワードは、証明書に使用されるパスワードと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="f6b2d-148">HTTPS 用に構成されたASP.NETコアでコンテナーを開始します。</span><span class="sxs-lookup"><span data-stu-id="f6b2d-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
