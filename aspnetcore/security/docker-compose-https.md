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
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>HTTPS 経由での docker Compose を使用したASP.NETコア イメージのホスティング


ASP.NETコアは[デフォルトで HTTPS](/aspnet/core/security/enforcing-ssl)を使用します。 [HTTPS は](https://en.wikipedia.org/wiki/HTTPS)、信頼、ID、および暗号化のために[証明書](https://en.wikipedia.org/wiki/Public_key_certificate)に依存します。

このドキュメントでは、HTTPS で事前に構築されたコンテナー イメージを実行する方法について説明します。

開発シナリオについては[、「httpS 経由での docker を使用したASP.NETコア アプリケーション](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)の開発」を参照してください。

このサンプルには[、Docker 17.06](https://docs.docker.com/release-notes/docker-ce)以降の[Docker クライアント](https://www.docker.com/products/docker)が必要です。

## <a name="prerequisites"></a>前提条件

このドキュメントの手順の一部には[、.NET Core 2.2 SDK](https://dotnet.microsoft.com/download)以降が必要です。

## <a name="certificates"></a>証明書

ドメインの[運用ホスティング](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)には[、証明機関](https://wikipedia.org/wiki/Certificate_authority)からの証明書が必要です。 [Let's Encrypt](https://letsencrypt.org/)は、無料の証明書を提供する証明機関です。

このドキュメントでは、事前に作成されたイメージを ホストするための[自己署名](https://wikipedia.org/wiki/Self-signed_certificate)開発`localhost`証明書を使用します。 手順は、実稼働証明書の使用に似ています。

運用証明書の場合:

* この`dotnet dev-certs`ツールは必須ではありません。
* 証明書は、手順で使用されている場所に保存する必要はありません。 証明書は、サイト ディレクトリの外部の任意の場所に格納します。

次のセクションに含まれる手順は`volumes`*、docker-compose.yml*のプロパティを使用して、コンテナーに証明書をマウントします。 `COPY` *Dockerfile*のコマンドを使用してコンテナー イメージに証明書を追加することもできますが、これはお勧めできません。 次の理由により、証明書をイメージにコピーすることはお勧めできません。

* 開発者証明書を使用してテストする場合、同じイメージを使用することは困難です。
* 本番証明書を使用してホスティングに同じイメージを使用することは困難です。
* 証明書の漏洩の重大なリスクがあります。

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>ドッカー構成を使用した https サポートを使用したコンテナーの開始

オペレーティング システムの構成に応じ、次の手順を実行します。

### <a name="windows-using-linux-containers"></a>Linux コンテナを使用するウィンドウ

証明書を生成し、ローカル マシンを構成します。

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

上記のコマンドで、パスワードで`{ password here }`置き換えます。

次の内容を含む_ドッカー構成.debug.yml_ファイルを作成します。

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
docker compose ファイルで指定されたパスワードは、証明書に使用されるパスワードと一致する必要があります。

HTTPS 用に構成されたASP.NETコアでコンテナーを開始します。

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS または Linux

証明書を生成し、ローカル マシンを構成します。

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`は macOS と Windows でのみサポートされています。 ディストリビューションでサポートされている方法で Linux 上の証明書を信頼する必要があります。 ブラウザで証明書を信頼する必要がある可能性があります。

上記のコマンドで、パスワードで`{ password here }`置き換えます。

次の内容を含む_ドッカー構成.debug.yml_ファイルを作成します。

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
docker compose ファイルで指定されたパスワードは、証明書に使用されるパスワードと一致する必要があります。

HTTPS 用に構成されたASP.NETコアでコンテナーを開始します。

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows コンテナを使用するウィンドウ

証明書を生成し、ローカル マシンを構成します。

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

上記のコマンドで、パスワードで`{ password here }`置き換えます。

次の内容を含む_ドッカー構成.debug.yml_ファイルを作成します。

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
docker compose ファイルで指定されたパスワードは、証明書に使用されるパスワードと一致する必要があります。

HTTPS 用に構成されたASP.NETコアでコンテナーを開始します。

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
