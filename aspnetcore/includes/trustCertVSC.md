* 次のコマンドを実行し、HTTPS 開発証明書を信頼します。

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  上記のコマンドは、Linux では機能しません。 証明書を信頼する方法については、Linux ディストリビューションのドキュメントを参照してください。

  上記のコマンドでは、次のダイアログが表示されます。

  ![セキュリティ警告のダイアログ](~/getting-started/_static/cert.png)

* 開発証明書を信頼することに同意する場合は、 **[はい]** を選択します。

  詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください
  
