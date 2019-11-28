::: moniker range=">= aspnetcore-3.0"
Visual Studio に次のダイアログが表示されます。

![このプロジェクトは SSL を使用するように構成されています。 ブラウザーに SSL 警告を表示しないようにするために、ASP.NET Core によって生成された自己署名証明書を信頼することを選択できます。 ASP.NET Core SSL 証明書を信頼しますか。](~/getting-started/_static/trustCert-3x.png)

ASP.NET Core SSL 証明書を信頼する場合は、 **[はい]** を選択します。

次のダイアログが表示されます。

![セキュリティ警告のダイアログ](~/getting-started/_static/cert.png)

開発証明書を信頼することに同意する場合は、 **[はい]** を選択します。

詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください
::: moniker-end

::: moniker range="< aspnetcore-3.0"
Visual Studio に次のダイアログが表示されます。

![このプロジェクトは SSL を使用するように構成されています。 ブラウザーに SSL 警告を表示しないようにするために、IIS Express によって生成された自己署名証明書を信頼することを選択できます。 IIS Express SSL 証明書を信頼しますか。](~/getting-started/_static/trustCert.png)

IIS Express SSL 証明書を信頼する場合、 **[はい]** を選択します。

次のダイアログが表示されます。

![セキュリティ警告のダイアログ](~/getting-started/_static/cert.png)

開発証明書を信頼することに同意する場合は、 **[はい]** を選択します。

詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください
::: moniker-end