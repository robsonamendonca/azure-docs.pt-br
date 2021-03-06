---
title: Configurar o Cofre de Chaves do Azure com a rotação de chaves e auditoria de ponta a ponta | Microsoft Docs
description: Use este guia de instruções para ajudá-lo a configurar a rotação de chaves e monitorar os logs do cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a5aaef50f12bfec89cf5e883ed6b1c85fa984ad6
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995955"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurar o Azure Key Vault com a rotação de chaves e auditoria

## <a name="introduction"></a>Introdução

Depois de ter um cofre de chaves, você pode começar a usá-lo para armazenar chaves e segredos. Seus aplicativos não precisam manter suas chaves ou segredos, mas podem solicitá-los do cofre conforme necessário. Um cofre de chaves permite que você atualize chaves e segredos sem afetar o comportamento do seu aplicativo, o que abre uma amplitude de possibilidades para o gerenciamento de chave e segredo.

Este artigo explica como implementar uma rotação agendada de chaves de conta de armazenamento, monitorar os logs de auditoria do cofre de chaves e gerar alertas quando solicitações inesperadas são feitas. 

Primeiro, você deve criar um cofre de chaves usando o método de sua escolha:

- [Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](quick-create-cli.md)
- [Definir e recuperar um segredo de Azure Key Vault usando Azure PowerShell](quick-create-powershell.md)
- [Definir e recuperar um segredo de Azure Key Vault usando portal do Azure](quick-create-portal.md)


## <a name="store-a-secret"></a>Armazenar um segredo

Para permitir que um aplicativo recupere um segredo do Cofre de Chaves, primeiro crie o segredo e carregue-o no cofre.

Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:

```powershell
Connect-AzAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha para sua conta do Azure. O PowerShell obterá todas as assinaturas que estão associadas a essa conta. O PowerShell usa a primeira por padrão.

Se tiver várias assinaturas, você terá que especificar a que foi usada para criar o cofre de chaves. Digite o seguinte para ver as assinaturas de sua conta:

```powershell
Get-AzSubscription
```

Para especificar a assinatura associada ao cofre de chaves que será registrada, digite:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Como este artigo demonstra como armazenar uma chave de conta de armazenamento como um segredo, você deve obter essa chave de conta de armazenamento.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de recuperar seu segredo (nesse caso, a chave da conta de armazenamento), você deve converter essa chave em uma cadeia de caracteres segura e, em seguida, criar um segredo com esse valor em seu cofre de chaves.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Em seguida, obtenha o URI do segredo que você criou. Você precisará desse URI em uma etapa posterior para chamar o cofre de chaves e recuperar seu segredo. Execute o seguinte comando do PowerShell e anote o valor da ID, que é o URI do segredo:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurar o aplicativo

Agora que você tem um segredo armazenado, você pode usar o código para recuperá-lo e usá-lo depois de executar mais algumas etapas.

Primeiro, você deve registrar seu aplicativo com Azure Active Directory. Em seguida, informe Key Vault as informações do aplicativo para que ele possa permitir solicitações do seu aplicativo.

> [!NOTE]
> O aplicativo deve ser criado no mesmo locatário do Azure Active Directory que o cofre de chaves.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito. Defina sua sessão do portal para o locatário do Azure AD desejado.
1. Pesquise **Azure Active Directory** e selecione-o. Em **Gerenciar**, selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Em **Registrar um aplicativo**, insira um nome de aplicativo significativo para exibir aos usuários.
1. Especifique quem pode usar o aplicativo, da seguinte maneira:

    | Tipos de conta com suporte | Descrição |
    |-------------------------|-------------|
    | **Contas somente neste diretório organizacional** | Selecione esta opção se você está criando um aplicativo de linha de negócios (LOB). Essa opção não estará disponível se você não estiver registrando o aplicativo em um diretório.<br><br>Essa opção mapeia para o único locatário somente do Azure AD.<br><br>Essa é a opção padrão, a menos que você esteja registrando o aplicativo fora de um diretório. Quando o aplicativo é registrado fora de um diretório, o padrão é contas da Microsoft pessoais e de vários locatários do Azure AD. |
    | **Contas em qualquer diretório organizacional** | Selecione essa opção se você deseja direcionar para todos os clientes comerciais e educacionais.<br><br>Essa opção mapeia para vários locatários somente do Azure AD.<br><br>Se você registrou o aplicativo como único locatário somente do Azure AD, pode atualizá-lo para ser multilocatário e voltar a ser locatário único na página **Autenticação**. |
    | **Contas em qualquer diretório organizacional e contas pessoais da Microsoft** | Selecione essa opção a fim de direcionar para o conjunto mais amplo de clientes.<br><br>Essa opção mapeia para contas da Microsoft pessoais e multilocatário do Azure AD.<br><br>Se você registrou o aplicativo como contas da Microsoft pessoais e multilocatário do Azure AD, não poderá alterar essa configuração na interface do usuário. Em vez disso, use o editor de manifesto do aplicativo para alterar os tipos de conta com suporte. |

1. Em **URI de redirecionamento (opcional)** , selecione o tipo de aplicativo que você está criando: **Web** ou **Cliente público (móvel e desktop)** . EM seguida, insira o URI de redirecionamento ou a URL de resposta para seu aplicativo.

    * Para aplicativos Web, informe a URL base do aplicativo. Por exemplo, `https://localhost:31544` pode ser uma URL para um aplicativo Web em execução no seu computador local. Os usuários usariam essa URL para entrar em um aplicativo cliente Web.
    * Para aplicativos cliente públicos, informe o URI usado pelo Azure AD para retornar respostas de token. Insira um valor específico para o aplicativo, por exemplo, `myapp://auth`.

1. Ao terminar, selecione **Registrar**.

    ![Mostra a tela para registrar um novo aplicativo no portal do Azure](../media/new-app-registration.png)

O Azure AD atribui um aplicativo ou uma ID de cliente exclusiva ao seu aplicativo. O portal abre a página de **Visão geral** do seu aplicativo. Observe o valor da **ID do aplicativo (cliente)** .

Para adicionar funcionalidades ao aplicativo, você pode selecionar outras opções de configuração, incluindo identidade visual, certificados e segredos, permissões de API e muito mais.

![Exemplo da página de visão geral de um aplicativo registrado recentemente](../media//new-app-overview-page-expanded.png)

Antes de estabelecer qualquer chamada do seu aplicativo no cofre de chaves, você deve informar ao cofre de chaves sobre seu aplicativo e suas permissões. O comando a seguir usa o nome do cofre e a **ID do aplicativo (cliente)** do seu aplicativo Azure Active Directory para conceder ao aplicativo **obter** acesso ao cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Agora você está pronto para começar a criar suas chamadas de aplicativo. Em seu aplicativo, você deve instalar os pacotes NuGet necessários para interagir com Azure Key Vault e Azure Active Directory. No Visual Studio Package Manager Console, insira os comandos a seguir. Na elaboração deste artigo, a versão atual do pacote de Azure Active Directory é 3.10.305231913, portanto, confirme a versão mais recente e atualize conforme necessário.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No código do aplicativo, crie uma classe para manter o método de autenticação do Azure Active Directory. Neste exemplo, essa classe é chamada de **utils**. Adicione a instrução `using` a seguir:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para recuperar o token JWT do Azure Active Directory. Para fins de manutenção, talvez você queira mover os valores de cadeia de caracteres embutidos em código para sua configuração de aplicativo ou Web.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Adicione o código necessário para chamar o Cofre de Chaves e recuperar o valor do segredo. Primeiro, você deve adicionar a seguinte `using` instrução:

```csharp
using Microsoft.Azure.KeyVault;
```

Adicione as chamadas de método para invocar o Cofre de Chaves e recuperar o segredo. Nesse método, você fornece o URI de segredo que salvou na etapa anterior. Observe o uso do método **GetToken** da classe **utils** criada anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Ao executar o aplicativo, agora você deverá se autenticar no Azure Active Directory e recuperar o valor do segredo do Cofre de Chaves do Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotação de chaves usando a Automação do Azure

> [!IMPORTANT]
> Os runbooks de automação do Azure ainda exigem o `AzureRM` uso do módulo.

Agora você está pronto para configurar uma estratégia de rotação para os valores armazenados como segredos Key Vault. Os segredos podem ser girados de várias maneiras:

- Como parte de um processo manual
- Programaticamente usando chamadas à API
- Por meio de um script de automação do Azure

Para os fins deste artigo, você usará o PowerShell combinado com a automação do Azure para alterar a chave de acesso de uma conta de armazenamento do Azure. Em seguida, você atualizará um segredo do cofre de chaves com essa nova chave.

Para permitir que a automação do Azure defina valores secretos em seu cofre de chaves, você deve obter a ID do cliente para a conexão chamada **AzureRunAsConnection**. Essa conexão foi criada quando você estabeleceu a instância de automação do Azure. Para localizar essa ID, selecione **ativos** da sua instância de automação do Azure. A partir daí, selecione **conexões**e, em seguida, selecione a entidade de serviço **AzureRunAsConnection** . Anote o valor **ApplicationId** .

![ID do cliente da Automação do Azure](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Em **ativos**, selecione **módulos**. Selecione **Galeria**e, em seguida, pesquise e importe versões atualizadas de cada um dos seguintes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Na elaboração deste artigo, somente os módulos mencionados anteriormente precisaram ser atualizados para o script a seguir. Se seu trabalho de automação falhar, confirme que você importou todos os módulos necessários e suas dependências.

Depois de recuperar a ID do aplicativo para sua conexão de automação do Azure, você deve informar ao cofre da chave que esse aplicativo tem permissão para atualizar segredos em seu cofre. Use o seguinte comando do PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, selecione **Runbooks** em sua instância de automação do Azure e, em seguida, selecione **Adicionar runbook**. Selecione **criação rápida**. Nomeie seu runbook e selecione **PowerShell** como o tipo de runbook. Você pode adicionar uma descrição. Por fim, selecione **Criar**.

![Criar runbook](../media/keyvault-keyrotation/Create_Runbook.png)

Cole o seguinte script do PowerShell no painel do editor para o novo runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

No painel do editor, selecione **painel de teste** para testar o script. Depois que o script for executado sem erros, você poderá selecionar **publicar**e, em seguida, poderá aplicar uma agenda para o runbook no painel de configuração do runbook.

## <a name="key-vault-auditing-pipeline"></a>Pipeline de auditoria do Cofre de Chaves

Ao configurar um cofre de chaves, você pode ativar a auditoria para coletar logs de solicitações de acesso feitas para o cofre de chaves. Esses logs são armazenados em uma conta de armazenamento do Azure designada e podem ser extraídos, monitorados e analisados. O cenário a seguir usa o Azure functions, aplicativos lógicos do Azure e logs de auditoria de cofre de chaves para criar um pipeline que envia um email quando um aplicativo que não corresponde à ID do aplicativo do aplicativo Web recupera segredos do cofre.

Primeiro, você deve habilitar o log no cofre de chaves. Use os comandos do PowerShell a seguir. (Você pode ver os detalhes completos neste [artigo sobre o registro em log de chave-cofre](../general/logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Após a habilitação do registro em log, os logs de auditoria começam a ser armazenados na conta de armazenamento designada. Esses logs contêm eventos sobre como e quando os cofres de chaves são acessados e por quem.

> [!NOTE]
> Você pode acessar as informações de log 10 minutos após a operação do cofre de chaves. Ele geralmente estará disponível antes disso.

A próxima etapa é [criar uma fila do Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Essa fila é onde os logs de auditoria do cofre de chaves são enviados por push. Quando as mensagens de log de auditoria estão na fila, o aplicativo lógico as pega e age nelas. Crie uma instância do barramento de serviço com as seguintes etapas:

1. Crie um namespace do barramento de serviço (se você já tiver um que deseja usar, pule para a etapa 2).
2. Navegue até a instância do barramento de serviço no portal do Azure e selecione o namespace no qual você deseja criar a fila.
3. Selecione **criar um recurso** > **Enterprise Integration** > **barramento de serviço**e, em seguida, insira os detalhes necessários.
4. Localize as informações de conexão do barramento de serviço selecionando o namespace e, em seguida, selecionando **informações de conexão**. Você precisará dessas informações para a próxima seção.

Em seguida, [crie uma função do Azure](../../azure-functions/functions-create-first-azure-function.md) para sondar os logs do cofre de chaves na conta de armazenamento e selecionar novos eventos. Esta função será disparada em um agendamento.

Para criar um aplicativo de funções do Azure, selecione **criar um recurso**, pesquise no marketplace por **aplicativo de funções**e, em seguida, selecione **criar**. Durante a criação, você pode usar um plano de hospedagem existente ou criar um novo. Você também pode optar pela hospedagem dinâmica. Para obter mais informações sobre as opções de hospedagem para Azure Functions, consulte [como dimensionar Azure Functions](../../azure-functions/functions-scale.md).

Depois que o aplicativo de funções do Azure for criado, acesse-o e selecione o cenário do **temporizador** e **C\# ** para o idioma. Em seguida, selecione **criar esta função**.

![Folha de Início das Funções do Azure](../media/keyvault-keyrotation/Azure_Functions_Start.png)

Na guia **Desenvolver** , substitua o código de run.csx pelo seguinte:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Altere as variáveis no código anterior para apontar para sua conta de armazenamento na qual os logs do cofre de chaves são gravados, na instância do barramento de serviço que você criou anteriormente e no caminho específico para os logs de armazenamento do cofre de chaves.

A função obtém o arquivo de log mais recente da conta de armazenamento em que os logs do cofre de chaves são gravados, capta os eventos mais recentes do arquivo e os envia por push para uma fila do Barramento de Serviço. 

Como um único arquivo pode ter vários eventos, você deve criar um arquivo Sync. txt que a função também examina para determinar o carimbo de data/hora do último evento que foi selecionado. O uso desse arquivo garante que você não envie o mesmo evento por push várias vezes. 

O arquivo Sync. txt contém um carimbo de data/hora para o último evento encontrado. Quando os logs são carregados, eles devem ser classificados com base em seus carimbos de data/hora para garantir que eles sejam ordenados corretamente.

Para essa função, fazemos referência a algumas bibliotecas adicionais que não estão disponíveis prontamente no Azure Functions. Para incluir essas bibliotecas, precisamos Azure Functions para recebê-las usando o NuGet. Na caixa **código** , selecione **Exibir arquivos**.

![Opção "exibir arquivos"](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Adicione um arquivo chamado Project. JSON com o seguinte conteúdo:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Depois de selecionar **salvar**, Azure Functions baixará os binários necessários.

Mude para a guia **Integrar** e atribua ao parâmetro do temporizador um nome significativo para ser usado na função. No código anterior, a função espera que o temporizador seja chamado *MyTimer*. Especifique uma [expressão cron](../../app-service/webjobs-create.md#CreateScheduledCRON) para o temporizador da seguinte `0 * * * * *`maneira:. Essa expressão fará com que a função seja executada uma vez por minuto.

Na mesma guia **integrar** , adicione uma entrada do tipo armazenamento de **BLOBs do Azure**. Essa entrada apontará para o arquivo Sync. txt que contém o carimbo de data/hora do último evento examinado pela função. Essa entrada será acessada dentro da função usando o nome do parâmetro. No código anterior, a entrada do armazenamento de BLOBs do Azure espera que o nome do parâmetro seja *inputBlob*. Selecione a conta de armazenamento na qual o arquivo Sync. txt será localizado (pode ser a mesma conta de armazenamento ou outra). No campo caminho, forneça o caminho para o arquivo no formato `{container-name}/path/to/sync.txt`.

Adicione uma saída do tipo **armazenamento de BLOBs do Azure**. Essa saída apontará para o arquivo Sync. txt que você definiu na entrada. Essa saída é usada pela função para gravar o carimbo de data/hora do último evento examinado. O código anterior espera que esse parâmetro seja chamado de *outputBlob*.

Agora, a função está pronta. Volte para a guia **Desenvolver** e salve o código. Verifique a janela de saída em busca de quaisquer erros de compilação e corrija-os conforme necessário. Se o código for compilado, o código agora deverá verificar os logs do cofre de chaves a cada minuto e enviar por push todos os novos eventos para a fila do barramento de serviço definido. Você deve ver as informações de log gravadas na janela de log sempre que a função for disparada.

### <a name="azure-logic-app"></a>Aplicativo lógico do Azure

Em seguida, você deve criar um aplicativo lógico do Azure que pega os eventos que a função está enviando para a fila do barramento de serviço, analisa o conteúdo e envia um email com base em uma condição sendo correspondida.

[Crie um aplicativo lógico](../../logic-apps/quickstart-create-first-logic-app-workflow.md) selecionando **criar um recurso** > **Integration** > **aplicativo lógico**de integração.

Depois que o aplicativo lógico for criado, acesse-o e selecione **Editar**. No editor do aplicativo lógico, selecione **fila do barramento de serviço** e insira suas credenciais do barramento de serviço para conectá-lo à fila.

![Barramento de Serviço de aplicativo lógico do Azure](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selecione **Adicionar uma condição**. Na condição, alterne para o editor avançado e insira o código a seguir. Substitua *APP_ID* pela ID de aplicativo real do seu aplicativo Web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Essa expressão, essencialmente, retorna **false** se a *AppID* do evento de entrada (que é o corpo da mensagem do barramento de serviço) não é a *AppID* do aplicativo.

Agora, crie uma ação em **se não, não faça nada**.

![Escolher ação do aplicativo lógico do Azure](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, selecione **Office 365-enviar email**. Preencha os campos para criar um email a ser enviado quando a condição definida retornar **false**. Se você não tiver o Office 365, procure alternativas para obter os mesmos resultados.

Agora você tem um pipeline de ponta a ponta que procura novos logs de auditoria de cofre de chaves uma vez por minuto. Ele envia por push novos logs que encontra para uma fila do barramento de serviço. O aplicativo lógico é disparado quando uma nova mensagem chega na fila. Se o *AppID* dentro do evento não corresponder à ID do aplicativo de chamada, ele enviará um email.
