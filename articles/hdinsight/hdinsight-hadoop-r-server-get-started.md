---
title: "Introdução ao Servidor R no HDInsight | Microsoft Docs"
description: "Saiba como criar um Apache Spark no cluster HDInsight que inclui o Servidor R e, então, enviar um script R no cluster."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/13/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: bf5b1c0a6e76f712e0be1f16ed1a6b2ac78d68de
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="get-started-using-r-server-on-hdinsight"></a>Aprenda a usar o Servidor R no HDInsight

HDInsight inclui uma opção de R Server para ser integrada ao seu cluster HDInsight. Isso permite que scripts R usem o Spark e MapReduce para executar cálculos distribuídos. Neste documento, você aprende a criar um R Server no cluster do HDInsight e executar um script R que demonstra o uso do Spark para cálculos R distribuídos.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**: antes de começar este tutorial, você deve ter uma assinatura do Azure. Acesse o artigo [Obtenha uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) para obter mais informações.
* **Um cliente Secure Shell (SSH)**: um cliente SSH é usado para se conectar ao cluster HDInsight remotamente e executar comandos diretamente no cluster. Para obter mais informações, confira [Usar SSH com HDInsight.](hdinsight-hadoop-linux-use-ssh-unix.md)
* **Chaves SSH (opcional)**: você pode proteger a conta SSH usada para se conectar ao cluster usando uma senha ou uma chave pública. Usar uma senha é mais fácil e permite que você comece sem precisar criar um par de chaves pública/privada. No entanto, usar uma chave é mais seguro.

> [!NOTE]
> As etapas neste documento pressupõem que você esteja usando uma senha.


## <a name="automated-cluster-creation"></a>Criação de cluster automatizada

Você pode automatizar a criação de HDInsight R Servers usando modelos do Azure Resource Manager, SDK e também PowerShell.

* Para criar um R Server usando um modelo de gerenciamento de recursos do Azure, confira [Como implantar um cluster do HDInsight do R Server](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).
* Para criar um R Server usando o SDK do .NET, confira [Como criar clusters baseados em Linux no HDInsight usando o SDK do .NET.](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* Implantar servidor R usando o powershell confira o artigo [Como criando um R Server no HDInsight com o PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


## <a name="create-the-cluster-using-the-azure-portal"></a>Como criar o cluster usando o portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **NOVO**, **Inteligência + Análise** e, então, **HDInsight**.

    ![Imagem da criação de um novo cluster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. Na experiência **Criação rápida**, insira um nome para o cluster no campo **Nome do Cluster**. Se você tiver várias assinaturas do Azure, use a entrada **Assinatura** para selecionar aquela que deseja usar.

    ![Nome do cluster e seleções de assinatura](./media/hdinsight-getting-started-with-r/clustername.png)

4. Selecione **Tipo de cluster** para abrir a folha **Configuração de cluster**. Na folha **Configuração de Cluster** , selecione as seguintes opções:

   * **Tipo de Cluster**: Servidor R
   * **Versão**: selecione a versão do R Server a ser instalada no cluster. Selecione a versão mais recente para ter acesso aos recursos mais recentes. Outras versões estarão disponíveis se forem necessárias para compatibilidade. As notas de versão para cada uma das versões disponíveis podem ser encontradas [aqui](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes).
   * **Edição de comunidade RStudio para R Server**: o IDE e baseada em navegador é instalado por padrão no nó de borda.  Se você preferir não instalá-lo, desmarque a caixa de seleção. Se você optar por instalá-lo, encontrará a URL para acessar o logon do servidor do RStudio em uma folha de aplicativo do portal para o cluster após ele ter sido criado.

   Deixe as outras opções com os valores padrão e use o botão **Selecionar** para salvar o tipo de cluster.

   ![Captura de tela do tipo de folha do cluster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

5. Insira um **Nome de usuário de logon do cluster** e uma **Senha de logon do cluster**.

   Especifique um **Nome de Usuário SSH**.  O SSH é usado para se conectar remotamente ao cluster usando um cliente **SSH (Secure Shell)**. Você pode especificar o usuário SSH nesta caixa de diálogo ou depois que o cluster foi criado (guia de configuração do cluster). O Servidor R está configurado para esperar um **Nome de usuário SSH** de "remoteuser".  **Se você usar um nome de usuário diferente, precisará executar uma etapa adicional após a criação do cluster.**

   Deixe a caixa marcada para **Usar a mesma senha como logon do cluster** usar **SENHA** como o tipo de autenticação, a menos que você prefira o uso de uma chave pública.  Você precisará de um par de chaves pública/privada se quiser acessar o Servidor R no cluster por meio de um cliente remoto, por exemplo, RTVS, RStudio ou outro Desktop IDE. Observe que você precisará escolher uma senha SSH se instalar o RStudio Server Community Edition.     

   Para criar e usar um par de chaves pública/privada, desmarque **Usar a mesma senha como logon do cluster** e, em seguida, selecione **CHAVE PÚBLICA** e proceda conforme descrito a seguir.  Estas instruções pressupõem que você tenha o Cygwin com ssh-keygen ou equivalente instalado.

   * Gere um par de chaves pública/privada do prompt de comando em seu laptop:

   `ssh-keygen -t rsa -b 2048`

   * Siga os prompts para nomear um arquivo de chave e, em seguida, insira uma frase secreta para maior segurança. A tela deve ter uma aparência semelhante à seguinte:

   ![Linha de comando SSH no Windows](./media/hdinsight-getting-started-with-r/sshcmdline.png)

   * Isso cria um arquivo de chave privada e um arquivo de chave pública com o nome <nome-do-arquivo-de chave-privada>.pub, por exemplo, furiosa e furiosa.pub.

   ![Dir SSH](./media/hdinsight-getting-started-with-r/dir.png)

   * Em seguida, especifique o arquivo de chave pública (*.pub) ao atribuir as credenciais de cluster HDI e, por fim, confirme seu grupo de recursos e região, e selecione **Avançar**

   ![Folha Credenciais](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * Alterar permissões em keyfile particular em seu laptop

   `chmod 600 <private-key-filename>`

   * Usar o arquivo de chave privada com o SSH para logon remoto

   `ssh –i <private-key-filename> remoteuser@<hostname public ip>`

   ou como parte da definição de seu contexto de computação do Hadoop Spark para o R Server no cliente (consulte Usando o Microsoft R Server como um cliente Hadoop na seção [Criando um contexto de computação para o Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) do documento online [Introdução ao ScaleR no Apache Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)).

6. A criação rápida faz a sua transição para a folha **Armazenamento** para selecionar as configurações de conta de armazenamento a serem usadas para a localização primária do sistema de arquivos HDFS usado pelo cluster. Selecione uma conta nova ou existente do Armazenamento do Azure ou uma conta existente de armazenamento do Data Lake.

   1. Ao selecionar uma conta de armazenamento do Azure, será possível selecionar uma conta de armazenamento existente selecionando a opção **Selecionar conta de armazenamento** e selecionando a conta desejada. Ou você pode criar uma nova conta usando o link **Criar Nova** na seção **Selecionar uma conta de armazenamento**.

      > [!NOTE]
      > Ao selecionar **Nova**, você deve inserir um nome para a nova conta de armazenamento. Uma marca de seleção verde será exibida se o nome for aceito.

      O **Contêiner Padrão** usará o nome do cluster como padrão. Deixe-o como o valor.

      Se a opção nova conta de armazenamento for selecionada, a janela **Local** será exibida para que você selecione em qual região a nova conta de armazenamento será criada.  

         ![Folha de fonte de dados](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. O cluster e a fonte de dados padrão devem estar na mesma região.

   2. Se você optar pelo uso de um Data Lake Store existente, selecione a conta de armazenamento do ADLS a ser usada e adicione a identidade de adição de cluster ao seu cluster para permitir o acesso ao repositório. Para obter mais informações sobre este processo, examine [Criar um cluster do HDInsight com o Data Lake Store usando o Portal do Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

   Use o botão **Selecionar** para salvar a configuração da fonte de dados.


7. A folha **Resumo** será então exibida para validar todas as suas configurações. Aqui você pode alterar seu **Tamanho do cluster** para modificar o número de servidores no cluster e também especificar quaisquer **Ações de script** que você deseje executar. A menos que você saiba que precisa de um cluster maior, deixe o número de nós de trabalho com o padrão de `4`. O custo estimado do cluster será mostrado na folha.

   ![resumo do cluster](./media/hdinsight-getting-started-with-r/clustersummary.png)

   > [!NOTE]
   > Se necessário, redimensione o cluster posteriormente por meio do Portal (Cluster -> Configurações -> Dimensionar Cluster) para aumentar ou diminuir o número de nós de trabalho.  Isso pode ser útil para deixar o cluster ocioso quando não estiver em uso ou para adicionar capacidade para atender às necessidades das tarefas maiores.
   >
   >

    Alguns fatores para ter em mente ao dimensionar o cluster, os nós de dados e o nó de borda incluem:  

   * O desempenho da análise do Servidor R distribuída no Spark é proporcional ao número de nós de trabalho quando os dados são grandes.  

   * O desempenho da análise do Servidor R é linear ao tamanho dos dados que estão sendo analisados. Por exemplo:  

     * Para os tamanhos de dados pequenos e médios, o desempenho será melhor quando analisados em um contexto de computação local no nó de borda.  Para saber mais sobre os cenários nos quais os contextos de computação local e Spark funcionam melhor, veja as opções de contexto de computação para R Server no HDInsight.<br>
     * Se você fizer logon no nó de borda e executar seu script R, todas as funções, exceto pelas funções rx do ScaleR, serão executadas <strong>localmente</strong> no nó de borda, de modo que a memória e o número de núcleos do nó de borda deverão ser dimensionados adequadamente. O mesmo se aplica se você usar o Servidor R no HDI como contexto de computação remota do seu laptop.

     ![Folha de camadas de preços de nó](./media/hdinsight-getting-started-with-r/pricingtier.png)

     Use o botão **Selecionar** para salvar a configuração de preços do nó.

   Você observará que também há um link para **Baixar modelo e parâmetros**. Clicar nesse link exibirá scripts que podem ser usados para automatizar a criação de um cluster com a configuração selecionada. Esses scripts também estão disponíveis na entrada do Portal do Azure para seu cluster após sua criação.

   > [!NOTE]
   > Levará algum tempo para que o cluster seja criado, normalmente cerca de 20 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de criação.
   >
   >

## <a name="connect-to-rstudio-server"></a>Conectar-se ao RStudio Server

Se você optou por incluir o RStudio Server Community Edition em sua instalação, poderá acessar o logon do RStudio por dois métodos diferentes.

1. Um dos modos de fazer isso é acessando a URL abaixo (em que **CLUSTERNAME** é o nome do cluster que você criou):

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. Ou abrindo a entrada para o cluster no Portal do Azure, selecionando o link rápido para os Painéis do Servidor R e selecionando o Painel do R Studio:

     ![Acessar o painel do RStudio](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![Acessar o painel do RStudio](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > Não importa o método: na primeira vez que você fizer logon, você precisará autenticar duas vezes.  Na primeira autenticação, forneça a ID de usuário e senha de Administrador do cluster. Na segunda autenticação, forneça a ID de usuário e a senha do SSH. Os logons subsequentes exigirão apenas o a ID de usuário e a senha do SSH.

## <a name="connect-to-the-r-server-edge-node"></a>Conecte-se ao nó de borda do Servidor R

Conecte-se ao nó de borda do Servidor R do cluster HDInsight usando o SSH:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Você também pode encontrar o endereço `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` no portal do Azure ao selecionar seu cluster e, em seguida, **Todas as Configurações**, **Aplicativos** e **RServer**. Isso exibirá as informações do ponto de extremidade do SSH para o nó de borda.
>
> ![Imagem do ponto de extremidade do SSH para o nó de borda](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
>

Caso você tenha usado uma senha para proteger sua conta de usuário do SSH, ela será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`.

Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Uma vez conectado, você chegará em um prompt semelhante ao seguinte.

`username@ed00-myrser:~$`

## <a name="use-the-r-console"></a>Use o console R

1. Na sessão de SSH, use o seguinte comando para iniciar o console R.  

   ```
   R

   You will see output similar to the following.
   R version 3.2.2 (2015-08-14) -- "Fire Safety"
   Copyright (C) 2015 The R Foundation for Statistical Computing
   Platform: x86_64-pc-linux-gnu (64-bit)

   R is free software and comes with ABSOLUTELY NO WARRANTY.
   You are welcome to redistribute it under certain conditions.
   Type 'license()' or 'licence()' for distribution details.

   Natural language support but running in an English locale

   R is a collaborative project with many contributors.
   Type 'contributors()' for more information and
   'citation()' on how to cite R or R packages in publications.

   Type 'demo()' for some demos, 'help()' for on-line help, or
   'help.start()' for an HTML browser interface to help.
   Type 'q()' to quit R.

   Microsoft R Server version 8.0: an enhanced distribution of R
   Microsoft packages Copyright (C) 2016 Microsoft Corporation

   Type 'readme()' for release notes.
   >
   ```

2. No prompt `>` , você pode inserir o código R. O Servidor R inclui pacotes que permitem que você interaja com o Hadoop e execute cálculos distribuídos com facilidade. Por exemplo, use o seguinte comando para exibir a raiz do sistema de arquivos padrão para o cluster HDInsight.

`rxHadoopListFiles("/")`

Você também pode usar o endereçamento de estilo WASB.

`rxHadoopListFiles("wasbs:///")`

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Usando o Servidor R no HDI de uma instância remota do Microsoft R Server ou Microsoft R Client

Na seção acima sobre uso de pares de chaves pública/privada para acessar o cluster, é possível configurar o acesso ao contexto de computação do HDI Hadoop Spark de uma instância remota do Microsoft R Server ou do Microsoft R Client em execução em um desktop ou um laptop (veja Usando o Microsoft R Server como um cliente Hadoop na seção [Criando um contexto de computação para o Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) do [Guia online de introdução ao RevoScaleR no Hadoop Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)).  Para isso, você precisará especificar as seguintes opções ao definir o contexto de computação RxSpark em seu laptop: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript. Por exemplo:

```
    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )
```


## <a name="use-a-compute-context"></a>Use um contexto de computação

Um contexto de computação permite que você controle se o cálculo será executado localmente no nó de borda, ou se ele será distribuído entre os nós no cluster HDInsight.

1. No Servidor do RStudio ou no console R (em uma seção do SSH), use o seguinte para carregar dados de exemplo no armazenamento padrão do HDInsight.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Em seguida, criaremos algumas informações de dados e definiremos duas fontes de dados para que possamos trabalhar com os dados.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Vamos executar uma regressão logística com os dados usando o contexto de computação local.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Você deve ver a saída que termina com linhas semelhantes à seguinte.

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Em seguida, vamos executar a mesma regressão logística usando o contexto do Spark. O contexto do Spark distribuirá o processamento em todos os nós de trabalho no cluster HDInsight.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > Você também pode usar o MapReduce para distribuir a computação nos nós do cluster. Para saber mais sobre o contexto de computação, confira [Opções de contexto de computação para o Servidor R no HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribua o código R em vários nós

Com o Servidor R, você pode facilmente usar o código R existente e executá-lo em vários nós no cluster usando o `rxExec`. Isso é útil ao fazer uma limpeza de parâmetro ou simulações. Veja a seguir um exemplo de como usar o `rxExec`.

`rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )`

Se você ainda estiver usando o contexto do Spark ou do MapReduce, o valor do nome do nó será retornado para os nós de trabalho nos quais o código `(Sys.info()["nodename"])` é executado. Por exemplo, em um cluster de quatro nós, você pode receber a saída semelhante à seguinte.


    ```
    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"
    ```

## <a name="accessing-data-in-hive-and-parquet"></a>Acessando dados no Hive e no Parquet

Um novo recurso disponível no R Server 9.0 e versões superiores permite acesso direto a dados no Hive e no Parquet para serem usados pelas funções ScaleR no contexto de computação do Spark. Esses recursos estão disponíveis por meio de novas funções de fontes de dados ScalR chamadas RxHiveData e RxParquetData, que funcionam com o uso de Spark SQL para carregar dados diretamente em um DataFrame do Spark para serem analisados pelo ScaleR.  

Veja a seguir alguns exemplos de código na utilização das novas funções:



    ```
    #..create a Spark compute context

    myHadoopCluster <- rxSparkConnect(reset = TRUE)
    ```


    ```
    #..retrieve some sample data from Hive and run a model

    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)
    ```

    ```
    #..retrieve some sample data from Parquet and run a model

    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)
    ```


    ```
    #..check on Spark data objects, cleanup, and close the Spark session

    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)
    ```

Para saber mais sobre o uso dessas novas funções, confira a ajuda online no R Server usando os comandos ?RxHivedata e ?RxParquetData.  


## <a name="install-r-packages"></a>Instalar pacotes R

Se desejar instalar pacotes R adicionais no nó de borda, você poderá usar o `install.packages()` diretamente do console R quando conectado ao nó de borda por meio do SSH. No entanto, se você precisar instalar pacotes R em nós de trabalho do cluster, você deverá usar uma Ação de Script.

As Ações de Script são scripts de Bash que são usados para fazer alterações de configuração no cluster HDInsight ou para instalar um software adicional. Nesse caso, para instalar pacotes R adicionais. Para instalar pacotes adicionais usando uma Ação de Script, siga as etapas a seguir.

> [!IMPORTANT]
> As Ações de Script para instalar pacotes R adicionais só podem ser usadas depois que o cluster foi criado. Ele não deve ser usado durante a criação do cluster, uma vez que o script precisa que o Servidor R esteja completamente instalado e configurado.
>
>

1. No [Portal do Azure](https://portal.azure.com), escolha o Servidor R no cluster HDInsight.

2. Na folha **Configurações**, selecione **Ações de Script** e **Enviar Novo** para enviar uma nova Ação de Script.

   ![Imagem da folha de ações do script](./media/hdinsight-getting-started-with-r/scriptaction.png)

3. Na folha **Enviar ação de script** , forneça as informações a seguir.

   * **Nome**: um nome amigável para identificar esse script

   * **URI do script Bash**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Cabeçalho**: deve estar **desmarcado**

   * **Trabalho**: deve estar **marcado**

   * **Nós de borda**: deve estar **desmarcado**.

   * **Zookeeper**: deve estar **desmarcado**

   * **Parâmetros**: os pacotes R a serem instalados. Por exemplo, `bitops stringr arules`

   * **Persistir esse script...**: deve estar **marcado**  

   > [!NOTE]
   > 1. Por padrão, todos os pacotes R são instalados por meio de um instantâneo do repositório do Microsoft MRAN consistente com a versão do R Server que foi instalado.  Se você quiser instalar versões mais recentes dos pacotes, haverá um risco de incompatibilidade. Porém, isso é possível com a especificação de `useCRAN` como o primeiro elemento do pacote de lista, por exemplo, `useCRAN bitops, stringr, arules`.  
   > 2. Alguns pacotes R exigirão outras bibliotecas do sistema do Linux. Para sua conveniência, pré-instalamos as dependências necessárias aos 100 pacotes R mais populares. No entanto, se os pacotes R instalados exigirem bibliotecas além dessas, você deverá baixar o script base usado aqui e adicionar etapas para instalar as bibliotecas do sistema. Em seguida, você deverá carregar o script modificado em um contêiner de blob público no armazenamento do Azure e usar o script modificado para instalar os pacotes.
   >    Para saber mais sobre como desenvolver as Ações de Script, confira [Desenvolvimento de ação de script](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Adicionando uma ação do script](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Escolha **Criar** para executar o script. Quando o script for concluído, os pacotes R estarão disponíveis em todos os nós de trabalho.

## <a name="using-microsoft-r-server-operationalization"></a>Usando a operacionalização do Microsoft R Server

Quando sua modelagem de dados for concluída, você poderá operacionalizar o modelo para fazer previsões. Para configurar a operacionalização do Microsoft R Server, execute as etapas abaixo.

Primeiro, ssh no nó de borda. Por exemplo: ```ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net```.

Depois de usar o ssh, altere o diretório para o seguinte diretório e sudo dotnet dll, conforme mostrado abaixo.

```
   cd /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil
   sudo dotnet Microsoft.DeployR.Utils.AdminUtil.dll
```

Para configurar a operacionalização do Microsoft R Server com uma caixa configuração, faça o seguinte;

* Selecione "1. Configurar o R Server para operacionalização"
* Selecione "A. Uma caixa (web + nós de computação)"
* Insira uma senha para o usuário **admin**

![operações de uma caixa](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

Como uma etapa opcional, você pode executar verificações de diagnóstico com um teste de diagnóstico, conforme mostrado abaixo.

* Selecione "6. Executar testes de diagnóstico”
* Selecione "A. Configuração de teste”
* Insira o nome de usuário = "admin" e a senha da etapa de configuração anterior
* Confirme a integridade geral = aprovado
* Sair do utilitário de administração
* Sair do SSH

![Diagnóstico para operações](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)

Nesse estágio, a configuração de operacionalização foi concluída. Agora, você pode usar o pacote 'mrsdeploy' em seu RClient para conectar a operacionalização no nó de borda e começar a usar seus recursos, como a [execução remota](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) e os [serviços web](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). A depender se o cluster está configurado em uma rede virtual ou não, você pode precisar configurar o túnel de encaminhamento da porta por meio de logon de SSH, conforme explicado abaixo:

### <a name="rserver-cluster-on-virtual-network"></a>Cluster do RServer em Rede Virtual

Lembre-se de permitir o tráfego pela porta 12800 para o nó de borda. Dessa forma, você pode usar o nó de borda para se conectar ao recurso de operacionalização.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Se o remoteLogin() não puder se conectar ao nó de borda, mas for possível executar o SSH para o nó de borda, verifique se a regra que permite o tráfego na porta 12800 foi configurada corretamente. Caso o problema persista, você pode usar uma solução alternativa configurando o túnel de encaminhamento da porta por meio de SSH.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Cluster do RServer não configurado em rede virtual

Se o cluster não estiver configurado na rede virtual ou se você estiver tendo problemas com a conectividade por meio da rede virtual, use o túnel SSH de encaminhamento da porta da seguinte forma:

```
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Você também pode configurá-la em Putty.

![conexão SSH em putty](./media/hdinsight-hadoop-r-server-get-started/putty.png)

Depois que a sessão SSH estiver ativa, o tráfego da porta 12800 da máquina será encaminhado para a porta 12800 do nó de borda por meio de uma sessão SSH. Lembre-se de usar `127.0.0.1:12800` em seu método remoteLogin(). Isso fará logon na operacionalização do nó da borda por meio do encaminhamento de porta.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Como dimensionar nós de computação de operacionalização do Microsoft R Server em nós de trabalho do HDInsight


### <a name="decommission-the-worker-nodes"></a>Encerrar os nós de trabalho

Atualmente, o Microsoft R Server não é gerenciado por meio de Yarn. Se os nós de trabalho não forem encerrados, o gerenciador de recursos Yarn não funcionará como esperado porque não estará ciente dos recursos consumidos pelo servidor. Para evitar isso, é recomendável encerrar os nós de trabalho nos quais você deseja dimensionar os nós de computação.

Etapas para encerramento de nós de trabalho:

* Faça logon no console de Ambari do cluster do HDI e clique na guia "hosts"
* Selecione os nós de trabalho (a encerrar), clique em "Ações" > "Hosts selecionados" > "Hosts" > clique em "Ativar o modo de manutenção". Por exemplo, na imagem abaixo, selecionamos wn3 e wn4 para desativar.  

   ![encerrar nós de trabalho](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* Selecione "Ações" > "Hosts selecionados" > "DataNodes" > clique em "Encerrar"
* Selecione "Ações" > "Hosts selecionados" > "NodeManagers" > clique em "Encerrar"
* Selecione "Ações" > "Hosts selecionados" > "DataNodes" > clique em "Parar"
* Selecione "Ações" > "Hosts selecionados" > "NodeManagers” > clique em "Parar"
* Selecione "Ações" > "Hosts selecionados" > "Hosts" > clique em "Parar todos os componentes"
* Desmarque os nós de trabalho e selecione os nós de cabeça
* Selecione "Ações" > "Hosts selecionados" > "Hosts" > "Reiniciar todos os componentes"


###    <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Configurar nós de computação em cada nó de trabalho encerrado

* SSH em cada nó de trabalho encerrado
* Executar o utilitário Administrador usando `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`
* Insira "1" para selecionar a opção "1. Configurar o R Server para operacionalização"
* Insira "c" para selecionar a opção "C. Nó de computação”. Isso configurará o nó de computação no nó de trabalho.
* Sair do utilitário de administração

### <a name="add-compute-nodes-details-on-web-node"></a>Adicionar detalhes de nós de computação no nó da Web

Depois que todos os nós de trabalho encerrados forem configurados para executar o nó de computação, volte ao nó de borda e adicione os endereços IP dos nós de trabalho encerrados na configuração do nó de web do Microsoft R Server:

* SSH no nó de borda
* Execute o `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`
* Procure a seção "URIs" e adicione o IP do nó de trabalho e detalhes de porta.

![encerrar nós de trabalho cmdline](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Próximas etapas

Agora que você entende como criar um novo cluster HDInsight que inclui o Servidor R e as noções básicas sobre como usar o console R em uma sessão SSH, use o seguinte para descobrir outras maneiras de trabalhar com o Servidor R no HDInsight.

* [Adicionar Servidor do RStudio ao HDInsight (se não for instalado durante a criação do cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opções de contexto de computação para o Servidor R no HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opções de Armazenamento do Azure para o Servidor R no HDInsight](hdinsight-hadoop-r-server-storage.md)

