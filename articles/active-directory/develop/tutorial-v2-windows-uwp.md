---
title: Introdução à plataforma de identidade da Microsoft para UWP | Azure
description: Como aplicativos da UWP (Plataforma Universal do Windows) podem chamar uma API que requer tokens de acesso do ponto de extremidade da plataforma de identidade da Microsoft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0dc70aa67a1414c08ec70e2e034f4ab12b194c0a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535937"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Chamar a API do Microsoft Graph de um aplicativo da Plataforma Universal do Windows (XAML)

> [!div renderon="docs"]

Este guia explica como um aplicativo nativo da UWP (Plataforma Universal do Windows) pode solicitar um token de acesso. Em seguida, o aplicativo chama a API do Microsoft Graph. O guia também se aplica a outras APIs que requerem tokens de acesso do ponto de extremidade da plataforma de identidade da Microsoft.

No final deste guia, seu aplicativo chama uma API protegida usando contas pessoais. Os exemplos são outlook.com, live.com e outros. Seu aplicativo também chama contas corporativas e de estudante de qualquer empresa ou organização que tem um Azure AD (Azure Active Directory).

>[!NOTE]
> Este guia requer o Visual Studio com o desenvolvimento da Plataforma Universal do Windows instalado. Consulte [Prepare-se para começar](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para obter instruções de como baixar e configurar o Visual Studio para desenvolver aplicativos da Plataforma Universal do Windows.

>[!NOTE]
> Se você é novo na plataforma de identidade da Microsoft, recomendamos que comece com o [início rápido Chamar a API do Microsoft Graph de um aplicativo UWP (Plataforma Universal do Windows)](quickstart-v2-uwp.md).

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Este guia cria um aplicativo da UWP de exemplo que consulta a API do Microsoft Graph. Para esse cenário, um token é adicionado às solicitações HTTP usando o cabeçalho de Autorização. A MSAL (Biblioteca de Autenticação da Microsoft) lida com as aquisições e a renovações de tokens.

## <a name="nuget-packages"></a>Pacotes NuGet

Este guia usa o seguinte pacote NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de Autenticação da Microsoft|

## <a name="set-up-your-project"></a>Configurar o seu projeto

Esta seção fornece instruções passo a passo para integrar um aplicativo .NET da área de trabalho do Windows (XAML) com Entrar com a Microsoft. Em seguida, o aplicativo pode consultar APIs Web que exigem um token, como a API do Microsoft Graph.

Este guia cria um aplicativo que exibe um botão que consulta a API do Graph e um botão para sair. Ele também exibe caixas de texto que contêm os resultados das chamadas.

> [!NOTE]
> Prefere baixar este projeto de exemplo do Visual Studio em vez de criá-lo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) e vá para a etapa [registro de aplicativo](#register-your-application "etapa de registro de aplicativo") para configurar o exemplo de código antes de executá-lo.

### <a name="create-your-application"></a>Criar o aplicativo

1. Abra o Visual Studio e selecione **Criar um projeto**.
1. Em **Criar um novo projeto**, escolha **Aplicativo em Branco (Universal do Windows)**  para C# e selecione **Avançar**.
1. Em **Configurar seu novo projeto**, nomeie o aplicativo e selecione **Criar**.
1. Se solicitado, em **Novo Projeto da Plataforma Universal do Windows**, selecione qualquer versão para **Destino** e **Mínima** e selecione **OK**.

   ![Versões mínima e de destino](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Adicionar a Biblioteca de Autenticação da Microsoft ao projeto

1. No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.
1. Copie e cole o seguinte comando na janela **Console do Gerenciador de Pacotes**:

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Esse comando instala a [Biblioteca de Autenticação da Microsoft](https://aka.ms/msal-net). A MSAL adquire, armazena em cache e atualiza os tokens de usuário que acessam APIs protegidas pela plataforma de identidade da Microsoft.

### <a name="create-your-applications-ui"></a>Criar a interface do usuário do aplicativo

O Visual Studio cria *MainPage.xaml* como parte de seu modelo de projeto. Abra esse arquivo e, em seguida, substitua o nó **Grade** do aplicativo pelo código a seguir:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Usar a MSAL para obter um token para a API do Microsoft Graph

Esta seção mostra como usar a MSAL para obter um token para a API do Microsoft Graph. Faça alterações no arquivo *MainPage.xaml.cs*.

1. Em *MainPage.xaml.cs*, adicione as seguintes referências:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Substitua a classe `MainPage` pelo seguinte código:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        public IPublicClientApplication PublicClientApp { get; }

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente<a name="more-information"></a>

O método `AcquireTokenInteractive` resulta em uma janela que solicita a conexão do usuário. Os aplicativos geralmente exigem que os usuários entrem interativamente na primeira vez para acessar um recurso protegido. Eles também podem precisar entrar quando uma operação silenciosa para adquirir um token falhar. Por exemplo, quando a senha do usuário expirou.

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `AcquireTokenSilent` manipula as aquisições e renovações de tokens sem nenhuma interação do usuário. Após `AcquireTokenInteractive` ser executado pela primeira vez e solicitar credenciais ao usuário, use o método `AcquireTokenSilent` para solicitar tokens para chamadas posteriores. Esse método adquire os tokens silenciosamente. A MSAL trata do armazenamento em cache e da renovação do token.

Por fim, o método `AcquireTokenSilent` falhará. A falha pode ser decorrente de os usuários terem se desconectado ou alterado a senha em outro dispositivo. Quando a MSAL detecta que o problema requer uma ação interativa, ela dispara uma exceção `MsalUiRequiredException`. O aplicativo pode tratar essa exceção de duas maneiras:

* Seu aplicativo chama `AcquireTokenInteractive` imediatamente. Essa chamada resulta na solicitação de entrada do usuário. Normalmente, use essa abordagem para aplicativos online em que não há nenhum conteúdo offline disponível para o usuário. O exemplo gerado por essa instalação orientada segue esse padrão. Você pode vê-lo em ação na primeira vez que executar o exemplo.

   Como nenhum usuário usou o aplicativo, `accounts.FirstOrDefault()` conterá um valor nulo e lançará uma exceção `MsalUiRequiredException`.

   Em seguida, o código no exemplo manipula a exceção chamando `AcquireTokenInteractive`. Essa chamada resulta na solicitação de entrada do usuário.

* Seu aplicativo apresenta uma indicação visual para os usuários de eles precisam se conectar. Em seguida, eles podem selecionar o momento certo para entrar. O aplicativo pode tentar `AcquireTokenSilent` novamente mais tarde. Use essa abordagem quando os usuários puderem usar outras funcionalidades do aplicativo sem interrupções. Um exemplo é quando o conteúdo offline está disponível no aplicativo. Nesse caso, os usuários podem decidir quando desejam se conectar. O aplicativo pode tentar `AcquireTokenSilent` novamente após a rede ter ficado não disponível temporariamente.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chamar a API do Microsoft Graph usando o token obtido recentemente

Adicione o seguinte novo método a *MainPage.xaml.cs*:

   ```csharp
   /// <summary>
   /// Perform an HTTP GET request to a URL using an HTTP Authorization header
   /// </summary>
   /// <param name="url">The URL</param>
   /// <param name="token">The token</param>
   /// <returns>String containing the results of the GET operation</returns>
   public async Task<string> GetHttpContentWithToken(string url, string token)
   {
       var httpClient = new System.Net.Http.HttpClient();
       System.Net.Http.HttpResponseMessage response;
       try
       {
           var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
           // Add the token in Authorization header
           request.Headers.Authorization =
             new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
           response = await httpClient.SendAsync(request);
           var content = await response.Content.ReadAsStringAsync();
           return content;
       }
       catch (Exception ex)
        {
           return ex.ToString();
       }
    }
   ```

 Esse método faz uma solicitação `GET` à API do Graph usando um cabeçalho `Authorization`.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

Neste aplicativo de exemplo, o método `GetHttpContentWithToken` faz uma solicitação HTTP `GET` em um recurso protegido que exige um token. Depois o método retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no cabeçalho de **Autorização HTTP**. Para este exemplo, o recurso é o ponto de extremidade **me** da API do Microsoft Graph – que exibe as informações de perfil do usuário.

### <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

Para desconectar o usuário, adicione o seguinte método a *MainPage.xaml.cs*:

   ```csharp
   /// <summary>
   /// Sign out the current user
   /// </summary>
   private async void SignOutButton_Click(object sender, RoutedEventArgs e)
   {
       IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
       IAccount firstAccount = accounts.FirstOrDefault();

       try
       {
           await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
               ResultText.Text = "User has signed out";
               this.CallGraphButton.Visibility = Visibility.Visible;
                   this.SignOutButton.Visibility = Visibility.Collapsed;
               });
           }
           catch (MsalException ex)
           {
               ResultText.Text = $"Error signing out user: {ex.Message}";
           }
       }
   ```

> [!NOTE]
> MSAL.NET usa métodos assíncronos para adquirir tokens ou manipular contas. Você precisa dar suporte a ações da interface do usuário no thread da IU. Esse é o motivo para a chamada `Dispatcher.RunAsync` e as precauções para chamar `ConfigureAwait(false)`.

#### <a name="more-information-about-signing-out"></a>Mais informações sobre sair<a name="more-information-on-sign-out"></a>

O método `SignOutButton_Click` remove o usuário do cache do usuário da MSAL. Esse método informa efetivamente a MSAL para esquecer o usuário atual. Uma solicitação futura para adquirir um token só terá êxito se for interativa.

O aplicativo neste exemplo oferece suporte a um único usuário. A MSAL dá suporte a cenários em que o usuário pode se conectar a mais de uma conta. Um exemplo é um aplicativo de email em que um usuário tem diversas contas.

### <a name="display-basic-token-information"></a>Exibir informações básicas de token

Adicione o seguinte método a *MainPage.xaml.cs* para exibir informações básicas sobre o token:

   ```csharp
   /// <summary>
   /// Display basic information contained in the token. Needs to be called from the UI thread.
   /// </summary>
   private void DisplayBasicTokenInfo(AuthenticationResult authResult)
   {
       TokenInfoText.Text = "";
       if (authResult != null)
       {
           TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
           TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
       }
   }
   ```

#### <a name="more-information"></a>Mais informações<a name="more-information-1"></a>

Os tokens de ID adquiridos usando **OpenID Connect** também contêm um pequeno subconjunto de informações pertinentes ao usuário. `DisplayBasicTokenInfo` exibe informações básicas contidas no token. Essas informações incluem o nome de exibição e a ID do usuário. Elas também incluem a data de validade do token e a cadeia de caracteres que representa o token de acesso em si. Caso selecione o botão **Chamar API do Microsoft Graph** diversas vezes, você verá que o mesmo token foi reutilizado em solicitações posteriores. Veja também que a data de expiração é estendida quando a MSAL decide que é a hora de renovar o token.

### <a name="display-message"></a>Exibir mensagem

Adicione o seguinte novo método a *MainPage.xaml.cs*:

   ```csharp
   /// <summary>
   /// Displays a message in the ResultText. Can be called from any thread.
   /// </summary>
   private async Task DisplayMessageAsync(string message)
   {
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
            () =>
            {
                ResultText.Text = message;
            });
        }
   ```

## <a name="register-your-application"></a>Registre seu aplicativo

Agora, você precisa registrar seu aplicativo:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory** > **Registros de aplicativo**.
1. Selecione **Novo registro**. Insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, *Aplicativo-UWP-chamando-MSGraph*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox)** e, em seguida, selecione **Registrar** para continuar.
1. Na página de visão geral, localize o valor da **ID do Aplicativo (cliente)** e copie-o. Volte para o Visual Studio, abra *MainPage.xaml.cs* e substitua o valor de `ClientId` por esse valor.

Configurar a autenticação para o aplicativo:

1. De volta ao [portal do Azure](https://portal.azure.com), em **Gerenciar**, selecione **Autenticação**.
1. Na seção **URIs de Redirecionamento** | **URIs de redirecionamento sugeridos para clientes públicos (móvel, área de trabalho)** , marque **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Clique em **Salvar**.

Configurar permissões da API para seu aplicativo:

1. Em **Gerenciar**, selecione **Permissões de API**.
1. Selecione **Adicionar uma permissão** e, em seguida, verifique se você selecionou **APIs da Microsoft**.
1. Selecione **Microsoft Graph**.
1. Selecione **Permissões delegadas**, pesquise por *User.Read* e verifique se **User.Read** está selecionado.
1. Se tiver feito alterações, selecione **Adicionar permissões** para salvá-las.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Habilitar a autenticação integrada em domínios federados (opcional)

Para habilitar a Autenticação Integrada do Windows quando usada com um domínio federado do Azure AD, o manifesto do aplicativo deverá habilitar recursos adicionais. Volte para seu aplicativo no Visual Studio.

1. Abra *Package.appxmanifest*.
1. Selecione **Funcionalidade** e habilite as seguintes configurações:

   * **Autenticação de Empresa**
   * **Redes Privadas (Cliente e Servidor)**
   * **Certificados Compartilhados de Usuário**

> [!IMPORTANT]
> A [Autenticação Integrada do Windows](https://aka.ms/msal-net-iwa) não está configurada por padrão nesse exemplo. Os aplicativos que solicitam funcionalidades de `Enterprise Authentication` ou `Shared User Certificates` exigem um nível mais alto de verificação pela Microsoft Store. Além disso, nem todos os desenvolvedores desejam executar o nível mais alto de verificação. Habilite essa configuração somente se você precisar da Autenticação Integrada do Windows com um domínio federado do Azure AD.

## <a name="test-your-code"></a>Testar seu código

Para testar o aplicativo, selecione F5 para executar o projeto no Visual Studio. A janela principal será exibida:

![Interface do usuário do aplicativo](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Quando estiver pronto para testar, selecione **Chamar a API do Microsoft Graph**. Em seguida, use uma conta organizacional do Azure AD ou uma conta Microsoft, como live.com ou outlook.com, para entrar. Na primeira vez que o usuário executa isso, o aplicativo exibe uma janela solicitando que ele entre.

### <a name="consent"></a>Consentimento

Na primeira vez que você entrar no aplicativo, será apresentada uma tela de consentimento semelhante à a seguir. Selecione **Sim** para consentir explicitamente com o acesso:

![Tela de consentimento de acesso](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Resultados esperados

São exibidas as informações de perfil do usuário retornadas pela chamada à API do Microsoft Graph na tela **Resultados da Chamada à API**:

![Tela Resultados da Chamada à API](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Também serão exibidas informações básicas sobre o token adquirido por meio de `AcquireTokenInteractive` ou `AcquireTokenSilent` na caixa **Informações de Token**:

|Propriedade  |Formatar  |Descrição |
|---------|---------|---------|
|`Username` |`user@domain.com` |É o nome de usuário que identifica o usuário.|
|`Token Expires` |`DateTime` |A hora em que o token expira. MSAL estende a data de validade renovando o token, conforme necessário.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo `user.read` para ler um perfil do usuário. Esse escopo é adicionado, por padrão, a cada aplicativo que é registrado no Portal de Registro de Aplicativo. Outras APIs do Microsoft Graph e APIs personalizadas do servidor de back-end podem exigir escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo `Calendars.Read` para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada `Calendars.Read` às informações de registro de aplicativo. Em seguida, adicione o escopo `Calendars.Read` à chamada `acquireTokenSilent`.

> [!NOTE]
> Talvez os usuários precisem fornecer autorizações adicionais à medida que o número de escopos aumenta.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="issue-1"></a>Problema 1

Você recebe uma das seguintes mensagens de erro ao entrar em seu aplicativo em um domínio federado do Azure AD:

* Nenhum certificado do cliente válido encontrado na solicitação.
* Nenhum certificado válido encontrado no repositório de certificados do usuário.
* Tente novamente escolhendo um método de autenticação diferente.

Causa: Os recursos corporativos e de certificado não estão habilitados.

Solução: Siga as etapas em [Habilitar autenticação integrada em domínios federados (opcional)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2

Você habilita a [autenticação integrada em domínios federados](#enable-integrated-authentication-on-federated-domains-optional) e tenta usar o Windows Hello em um computador com Windows 10 para entrar em um ambiente com autenticação multifator configurada. A lista de certificados é apresentada. No entanto, se você optar por usar seu PIN, a janela PIN nunca é apresentada.

Causa: Esse problema é uma limitação conhecida do agente de autenticação da Web em aplicativos UWP executando na área de trabalho do Windows 10. Ele funciona corretamente no Windows 10 Mobile.

Solução alternativa: Selecione **Entrar com outras opções**. Depois selecione **Entrar com nome de usuário e senha**. Selecione **Forneça sua senha**. Depois passe pelo processo de autenticação de telefone.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
