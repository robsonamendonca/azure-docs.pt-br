---
title: Problemas conhecidos e soluções
titleSuffix: Azure Machine Learning
description: Obtenha uma lista dos problemas conhecidos, soluções alternativas e solução de problemas para Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 93015da810f163a48529704e69e1747ac1aec401
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889388"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Problemas conhecidos e solução de problemas Azure Machine Learning

Este artigo ajuda você a encontrar e corrigir erros ou falhas que você pode encontrar ao usar o Azure Machine Learning.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Às vezes, pode ser útil fornecer informações de diagnóstico ao pedir ajuda. Para ver alguns logs: 
1. Visite [Azure Machine Learning Studio](https://ml.azure.com). 
1. No lado esquerdo, selecione **experimento** 
1. Selecione um experimento.
1. Selecione uma execução.
1. Na parte superior, selecione **saídas + logs**.

> [!NOTE]
> Azure Machine Learning registra as informações de uma variedade de fontes durante o treinamento, como AutoML ou o contêiner do Docker que executa o trabalho de treinamento. Muitos desses logs não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles podem ser capazes de usar esses logs durante a solução de problemas.


## <a name="resource-quotas"></a>Cotas de recursos

Saiba mais sobre as [cotas de recursos](how-to-manage-quotas.md) que você pode encontrar ao trabalhar com o Azure Machine Learning.

## <a name="installation-and-import"></a>Instalação e importação
                           
* **Instalação de Pip: não há garantia de que as dependências sejam consistentes com a instalação de linha única**: 

   Essa é uma limitação conhecida do Pip, pois não tem um resolvedor de dependências em funcionamento quando você instala o como uma única linha. A primeira dependência exclusiva é a única que ela examina. 

   No código `azure-ml-datadrift` a seguir e `azureml-train-automl` são instalados usando uma instalação de PIP de linha única. 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   Para este exemplo, digamos `azure-ml-datadrift` que o requer a versão > 1,0 `azureml-train-automl` e requer a versão < 1,2. Se a versão mais recente `azure-ml-datadrift` do for 1,3, ambos os pacotes serão atualizados para 1,3, independentemente do `azureml-train-automl` requisito de pacote para uma versão mais antiga. 

   Para garantir que as versões apropriadas sejam instaladas para seus pacotes, instale o usando várias linhas, como no código a seguir. A ordem não é um problema aqui, pois Pip explicitamente faz downgrade como parte da próxima chamada de linha. Portanto, as dependências de versão apropriadas são aplicadas.
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Explicação pacote não guarateed a ser instalado ao instalar o azureml-Train-automl-Client:** 
   
   Ao executar uma automl remota executar com a explicação do modelo habilitada, você verá uma mensagem de erro dizendo "" Instale o azureml-explique-Package para obter explicações do modelo. " Esse é um problema conhecido e, como alternativa, siga uma das etapas abaixo:
  
  1. Instale o azureml-explique-Model localmente.
   ```
      pip install azureml-explain-model
   ```
  2. Desabilite inteiramente o recurso de explicação, passando model_explainability = false na configuração automl.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Erros do panda: normalmente vistos durante o experimento do AutoML:**
   
   Ao configurar manualmente seu environmnet usando Pip, você observará erros de atributo (especialmente de pandas) devido à instalação de versões de pacote sem suporte. Para evitar esses erros, [Instale o SDK do AutoML usando o automl_setup. cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md):
   
    1. Abra um prompt do Anaconda e clone o repositório do GitHub para um conjunto de blocos de anotações de exemplo.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. CD para a pasta como usar-azureml/Automated-Machine Learning em que os blocos de anotações de exemplo foram extraídos e, em seguida, executados:
    
    ```bash
    automl_setup
    ```
  
* **Mensagem de erro: não é possível desinstalar 'PyYAML'**

    Azure Machine Learning SDK para Python: PyYAML é um `distutils` projeto instalado. Portanto, não é possível determinar com precisão quais arquivos pertencem a ele no caso de uma desinstalação parcial. Para continuar a instalação do SDK ignorando esse erro, use:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Falha do databricks ao instalar pacotes**

    A instalação do SDK do Azure Machine Learning falha em Azure Databricks quando mais pacotes são instalados. Alguns pacotes, como `psutil`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão da biblioteca. Esse problema está relacionado ao databricks e não ao SDK do Azure Machine Learning. Você também pode experimentar esse problema com outras bibliotecas. Exemplo:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Como alternativa, você pode usar scripts de inicialização se continuar enfrentando problemas de instalação com bibliotecas do Python. Essa abordagem não tem suporte oficial. Para obter mais informações, consulte [scripts de inicialização no escopo do cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Erro de importação do databricks: não é possível importar o nome ' timedelta ' de ' pandas. _libs. tslibs '**: se você vir esse erro ao usar o aprendizado de máquina automatizado, execute as duas linhas a seguir em seu bloco de anotações:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Erro de importação do databricks: nenhum módulo chamado ' pandas. Core. Indexes '**: se você vir esse erro ao usar o aprendizado de máquina automatizado:

    1. Execute este comando para instalar dois pacotes no cluster de Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Desanexe e anexe novamente o cluster ao seu bloco de anotações.
    
    Se essas etapas não resolverem o problema, tente reiniciar o cluster.

* **Databricks FailToSendFeather**: se você vir um `FailToSendFeather` erro ao ler dados no cluster Azure Databricks, consulte as seguintes soluções:
    
    * Atualize `azureml-sdk[automl]` o pacote para a versão mais recente.
    * Adicione `azureml-dataprep` a versão 1.1.8 ou superior.
    * Adicione `pyarrow` a versão 0,11 ou superior.
    
## <a name="create-and-manage-workspaces"></a>Crie e gerencie os workspaces

> [!WARNING]
> Não há suporte para mover o espaço de trabalho Azure Machine Learning para uma assinatura diferente ou mover a assinatura proprietária para um novo locatário. Isso pode causar erros.

* **Portal do Azure**: se você for diretamente para exibir seu espaço de trabalho em um link de compartilhamento no SDK ou no portal, você não poderá exibir a página de **visão geral** normal com as informações de assinatura na extensão. Você também não poderá alternar para outro workspace. Se você precisar exibir outro espaço de trabalho, vá diretamente para [Azure Machine Learning Studio](https://ml.azure.com) e pesquise o nome do espaço de trabalho.

## <a name="set-up-your-environment"></a>Configure seu ambiente

* **Problemas ao criar AmlCompute**: há uma chance rara de que alguns usuários que criaram seu espaço de trabalho Azure Machine Learning do portal do Azure antes da versão GA talvez não possam criar AmlCompute nesse espaço de trabalho. Você pode gerar uma solicitação de suporte em relação ao serviço ou criar um novo espaço de trabalho por meio do portal ou do SDK para desbloquear-se imediatamente.

## <a name="work-with-data"></a>Trabalhar com dados

### <a name="overloaded-azurefile-storage"></a>Armazenamento do Azurefile sobrecarregado

Se você receber um erro `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, aplique as seguintes soluções alternativas.

Se você estiver usando o compartilhamento de arquivos para outras cargas de trabalho, como a transferência de dados, a recomendação será usar BLOBs para que o compartilhamento de arquivos esteja livre para ser usado para o envio de execuções. Você também pode dividir a carga de trabalho entre dois espaços diferentes.

### <a name="passing-data-as-input"></a>Passando dados como entrada

*  **TypeError: fileNotFound: não há tal arquivo ou diretório**: esse erro ocorrerá se o caminho do arquivo fornecido não for onde o arquivo está localizado. Você precisa certificar-se de que a maneira como você se refere ao arquivo é consistente com o local em que você montou seu conjunto de seu destino de computação. Para garantir um estado determinístico, é recomendável usar o caminho abstrato ao montar um conjunto de um DataSet para um destino de computação. Por exemplo, no código a seguir, montamos o DataSet na raiz do sistema de arquivos do destino de computação `/tmp`,. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Se você não incluir a barra "/" à esquerda, será necessário prefixar o diretório de trabalho, por `/mnt/batch/.../tmp/dataset` exemplo, no destino de computação para indicar onde você deseja que o conjunto de os seja montado.

### <a name="data-labeling-projects"></a>Projetos de rotulamento de dados

|Problema  |Resolução  |
|---------|---------|
|Somente conjuntos de linhas criados em armazenamentos de BLOBs podem ser usados     |  Essa é uma limitação conhecida da versão atual.       |
|Após a criação, o projeto mostrará "Inicializando" por um longo tempo     | Atualize manualmente a página. A inicialização deve continuar em aproximadamente 20 pontos de extremidade por segundo. A falta de atualização autoatualizada é um problema conhecido.         |
|Ao revisar imagens, as imagens rotuladas recentemente não são mostradas     |   Para carregar todas as imagens rotuladas, escolha o **primeiro** botão. O **primeiro** botão o levará de volta à frente da lista, mas carregará todos os dados rotulados.      |
|Pressionar a tecla ESC enquanto rotular para detecção de objeto cria um rótulo de tamanho zero no canto superior esquerdo. O envio de rótulos nesse estado falha.     |   Exclua o rótulo clicando na marca de cruz ao lado dele.  |

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

Problemas conhecidos:

* **Tempo de preparação de computação longa**: pode ser de alguns minutos ou mais, quando você se conecta pela primeira vez ou cria um destino de computação. 

## <a name="train-models"></a>Treinar modelos

* **ModuleErrors (nenhum módulo chamado)**: se você estiver executando o ModuleErrors ao enviar experimentos no Azure ml, significa que o script de treinamento está esperando que um pacote seja instalado, mas não adicionado. Depois de fornecer o nome do pacote, o Azure ML instalará o pacote no ambiente usado para sua execução de treinamento. 

    Se você estiver usando [estimadores](concept-azure-machine-learning-architecture.md#estimators) para enviar experimentos, poderá especificar um nome de pacote `pip_packages` por `conda_packages` meio do parâmetro ou no estimador com base em qual fonte você deseja instalar o pacote. Você também pode especificar um arquivo yml com todas as suas dependências usando `conda_dependencies_file`ou listar todos os seus requisitos de Pip `pip_requirements_file` em um arquivo txt usando o parâmetro. Se você tiver seu próprio objeto de ambiente do Azure ML que deseja substituir a imagem padrão usada pelo estimador, você poderá especificar esse ambiente por meio `environment` do parâmetro do Construtor estimador.

    O Azure ML também fornece avaliadores específicos da estrutura para Tensorflow, PyTorch, chainer e SKLearn. Usar esses estimadores garantirá que as principais dependências da estrutura sejam instaladas em seu nome no ambiente usado para treinamento. Você tem a opção de especificar dependências extras, conforme descrito acima. 
 
    As imagens do Docker mantidas pelo Azure ML e seu conteúdo podem ser vistos em [contêineres do AzureML](https://github.com/Azure/AzureML-Containers).
    As dependências específicas à estrutura são listadas no respectivo Framework Documentation- [Chainr](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Se você considerar que um pacote específico é comum o suficiente para ser adicionado em ambientes e imagens mantidas do Azure ML, gere um problema do GitHub nos [contêineres do AzureML](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (nome não definido), AttributeError (objeto sem atributo)**: essa exceção deve vir de seus scripts de treinamento. Você pode examinar os arquivos de log de portal do Azure para obter mais informações sobre o nome específico não definido ou erro de atributo. No SDK, você pode usar `run.get_details()` para examinar a mensagem de erro. Isso também listará todos os arquivos de log gerados para sua execução. Certifique-se de dar uma olhada no script de treinamento e corrija o erro antes de reenviar sua execução. 

* O **Horovod foi desligado**: na maioria dos casos, se você encontrar "AbortedError: Horovod foi desligado", essa exceção significa que houve uma exceção subjacente em um dos processos que fizeram com que o Horovod fosse desligado. Cada classificação no trabalho MPI obtém seu próprio arquivo de log dedicado no Azure ML. Esses logs são nomeados `70_driver_logs`. No caso de treinamento distribuído, os nomes de log têm o sufixo `_rank` para facilitar a diferenciação dos logs. Para localizar o erro exato que fez com que o Horovod fosse desligado, percorra todos os arquivos de log `Traceback` e procure no final dos arquivos de driver_log. Um desses arquivos fornecerá a exceção subjacente real. 

* **Execução ou teste de exclusão**: os experimentos podem ser arquivados usando o método [experimento. Archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) ou da exibição guia do experimento no cliente Azure Machine Learning Studio por meio do botão "arquivo de teste". Essa ação oculta o experimento de consultas de lista e exibições, mas não a exclui.

    Atualmente, não há suporte para a exclusão permanente de experimentos ou execuções individuais. Para obter mais informações sobre como excluir ativos de espaço de trabalho, consulte [exportar ou excluir seus dados de espaço de trabalho do serviço Machine Learning](how-to-export-delete-data.md).

* O **documento de métrica é muito grande**: Azure Machine Learning tem limites internos sobre o tamanho dos objetos de métrica que podem ser registrados de uma só vez a partir de uma execução de treinamento. Se você encontrar um erro "o documento de métrica é muito grande" ao registrar em log uma métrica com valor de lista, tente dividir a lista em partes menores, por exemplo:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internamente, o Azure ML concatena os blocos com o mesmo nome de métrica em uma lista contígua.

## <a name="automated-machine-learning"></a>Machine Learning automatizado

* **Fluxo de tensor**: o Machine Learning automatizado atualmente não dá suporte à versão 1,13 do fluxo do tensor. A instalação desta versão fará com que as dependências do pacote parem de funcionar. Estamos trabalhando para corrigir esse problema em uma versão futura.

* **Gráficos de experimento**: os gráficos de classificação binária (recall de precisão, Roc, curva de lucro, etc.) mostrados em iterações de experimento de ml automatizadas não são renderizados corretamente na interface do usuário desde 4/12. Atualmente, as plotagens de gráfico estão mostrando resultados inversos, onde os modelos de melhor desempenho são mostrados com resultados mais baixos. Uma resolução está sob investigação.

* **Databricks cancelar uma execução automatizada do Machine Learning**: quando você usa recursos automatizados de aprendizado de máquina no Azure Databricks, para cancelar uma execução e iniciar uma nova execução de experimento, reinicie o cluster Azure Databricks.

* **Databricks >10 iterações para o aprendizado de máquina automatizado**: em configurações automatizadas do Machine Learning, se você tiver mais de `show_output` 10 `False` iterações, defina como ao enviar a execução.

* **Widget do databricks para o SDK do Azure Machine Learning e o aprendizado de máquina automatizado**: o widget do sdk do Azure Machine Learning não tem suporte em um bloco de anotações do databricks porque os notebooks não podem analisar widgets HTML. Você pode exibir o widget no portal usando este código Python na célula Azure Databricks notebook:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Implantar e fornecer modelos

Execute estas ações para os seguintes erros:

|Erro  | Resolução  |
|---------|---------|
|Falha na criação da imagem ao implantar o serviço Web     |  Adicionar "pynacl = = 1.2.1" como uma dependência Pip ao arquivo Conda para configuração de imagem       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Altere a SKU para VMs usadas em sua implantação para uma que tenha mais memória. |
|Falha de FPGA     |  Não será possível implantar modelos em FPGAs até que você tenha solicitado e recebido aprovação para cota de FPGA. Para solicitar acesso, preencha o formulário de solicitação de cota: https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Atualizando Azure Machine Learning componentes no cluster AKS

As atualizações para Azure Machine Learning componentes instalados em um cluster do serviço kubernetes do Azure devem ser aplicadas manualmente. 

Você pode aplicar essas atualizações desanexando o cluster do espaço de trabalho Azure Machine Learning e anexando novamente o cluster ao espaço de trabalho. Se o TLS estiver habilitado no cluster, será necessário fornecer o certificado TLS/SSL e a chave privada ao anexar novamente o cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Se você não tiver mais o certificado TLS/SSL e a chave privada, ou se estiver usando um certificado gerado pelo Azure Machine Learning, poderá recuperar os arquivos antes de desanexar o cluster conectando-se ao cluster `kubectl` usando e recuperando o `azuremlfessl`segredo.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos no formato codificado em base-64. Você precisará de base-64 decodificar `cert.pem` os `key.pem` componentes e dos segredos antes de fornecer a `attach_config.enable_ssl`eles. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>WebServices em falhas do serviço kubernetes do Azure

Muitas falhas de WebService no serviço kubernetes do Azure podem ser depuradas conectando-se `kubectl`ao cluster usando. Você pode obter o `kubeconfig.json` para um cluster do serviço kubernetes do Azure executando

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Erros de autenticação

Se executar uma operação de gerenciamento em um destino de computação de um trabalho remoto, você receberá um dos seguintes erros:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, você receberá um erro se tentar criar ou anexar um destino de computação de um Pipeline de ML que é enviado para execução remota.
