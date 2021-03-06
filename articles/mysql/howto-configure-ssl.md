---
title: "Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Instruções sobre como configurar apropriadamente o Banco de Dados do Azure para MySQL e aplicativos associados a fim de usar as conexões SSL corretamente"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 801806056b745be5663c0a10241795947d1dd036
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

---

# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL
O Banco de Dados do Azure para MySQL dá suporte à conexão de seu servidor de Banco de Dados do Azure para MySQL para aplicativos cliente usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Por padrão, o serviço de banco de dados deve ser configurado para exigir conexões SSL ao se conectar ao servidor de Banco de Dados do Azure para MySQL.  Recomendamos evitar desabilitar a opção SSL sempre que possível. 

## <a name="enforcing-ssl-connections"></a>Impor conexões SSL
Ao provisionar um novo servidor de Banco de Dados do Azure para MySQL por meio do Portal ou CLI do Azure, a imposição de conexões SSL está habilitada por padrão. 

Da mesma forma, cadeias de conexão previamente definidas nas configurações de "Cadeias de Conexão" em seu servidor no Portal do Azure incluem os parâmetros necessários para linguagens comuns a fim de se conectar ao seu servidor de banco de dados usando SSL. O parâmetro SSL varia de acordo com o conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="configure-enforcement-of-ssl"></a>Configurar a imposição de SSL
Você pode desabilitar ou habilitar a Imposição de SSL. O Microsoft Azure recomenda sempre habilitar a configuração Impor conexão SSL para melhorar a segurança.

### <a name="using-azure-portal"></a>Usando o Portal do Azure
Usando o Portal do Azure, visite seu servidor de Banco de Dados do Azure para MySQL e clique em **Segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **Impor conexão SSL**. Em seguida, clique em **Salvar**. A Microsoft recomenda sempre habilitar a configuração **Impor conexão SSL** para melhorar a segurança. 
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

Você pode confirmar a configuração exibindo a página **Visão geral** para ver o indicador **Status de imposição de SSL**.

### <a name="using-azure-cli"></a>Usando a CLI do Azure
Você pode habilitar ou desabilitar o parâmetro **ssl-enforcement** usando os valores Enabled ou Disabled respectivamente na CLI do Azure.
```azurecli
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```
## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificar se o seu aplicativo ou sua estrutura oferece suporte a conexões SSL
Muitos aplicativos comuns que usam MySQL para serviços de banco de dados, como Wordpress, Drupal e Magento, não habilitam o SSL por padrão durante a instalação.  A habilitação da conectividade SSL deve ser feita após a instalação ou por meio de comandos da CLI específicos ao aplicativo.  Se o seu servidor MySQL estiver impondo conexões SSL, e o aplicativo associado não estiver configurado corretamente, a conexão do aplicativo ao seu servidor de banco de dados poderá falhar.  Confira a documentação de seu aplicativo para saber como habilitar conexões SSL.

## <a name="applications-that-require-a-local-certificate-for-ssl-connectivity"></a>Aplicativos que exigem um certificado local para conectividade SSL
Em alguns casos, os aplicativos exigem um arquivo de certificado local (.pem) gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) para se conectar com segurança.  Veja as etapas a seguir para obter o arquivo .cer, gerar o arquivo .pem local e associá-lo ao seu aplicativo.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Baixar o arquivo de certificado da Autoridade de Certificação (CA) 
O certificado necessário para se comunicar por SSL com o servidor de Banco de Dados do Azure para MySQL está [aqui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).  Baixe o arquivo de certificado na unidade local (neste tutorial, usaremos **c:\ssl**).

### <a name="download-and-install-openssl-on-your-pc"></a>Baixar e instalar o OpenSSL em seu computador 
Para gerar o arquivo **.pem** local necessário para seu aplicativo se conectar com segurança ao servidor de banco de dados, instale o OpenSSL em seu computador local.  

As seções a seguir descrevem as abordagens possíveis, em um computador com Linux ou Windows, dependendo de sua preferência por sistema operacional, mas você só precisa seguir uma.

### <a name="linux-users---download-and-install-openssl-using-a-linux-pc"></a>Usuários do Linux - Baixar e instalar o OpenSSL usando um computador com Linux
As bibliotecas OpenSSL são fornecidas diretamente no código-fonte a partir da [OpenSSL Software Foundation](http://www.openssl.org).  As instruções a seguir orientarão você pelas etapas necessárias de instalação do OpenSSL no computador com Linux.  Neste guia, mostraremos comandos para Ubuntu 12.04 e superior.

Abra uma sessão de terminal e instale o OpenSSL
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
```  
Extraia os arquivos do pacote baixado
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Entre no diretório onde os arquivos foram extraídos.  Por padrão, deve ser da seguinte maneira.

```bash
cd openssl-1.1.0e
```
Configure o OpenSSL executando o seguinte comando: se você quiser os arquivos em uma pasta diferente de /usr/local/openssl, altere o seguinte conforme for apropriado.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Agora que o OpenSSL está configurado corretamente, você precisa compilá-lo para converter seu certificado.  Para compilar, execute o seguinte comando:

```bash
make
```
Após a conclusão da compilação, você estará pronto para instalar o OpenSSL como um executável, executando o seguinte comando:
```bash
make install
```
Para confirmar a instalação bem-sucedida do OpenSSL em seu sistema, execute o comando a seguir e verifique se você obtém o mesmo resultado.

```bash
/usr/local/openssl/bin/openssl version
```
Se for bem-sucedida, você verá a seguinte mensagem:
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

### <a name="windows-pc-users---download-and-install-openssl-using-a-windows-pc"></a>Usuários de computador com Linux - Baixar e instalar o OpenSSL usando um computador com Windows
Para gerar o arquivo **.pem** local necessário para seu aplicativo se conectar com segurança ao servidor de banco de dados, instale o OpenSSL em seu computador local.

As bibliotecas OpenSSL são fornecidas diretamente no código-fonte a partir da [OpenSSL Software Foundation](http://www.openssl.org). As instruções a seguir orientarão você pelas etapas necessárias de instalação do OpenSSL no computador com Linux.

A instalação do OpenSSL em um PC com Windows pode ser feita destas maneiras:

1. **(Recomendado)** Usando a funcionalidade interna Bash para Windows no Windows 10 e superior, o OpenSSL é instalado por padrão.  As instruções sobre como habilitar a funcionalidade Bash para Windows no Windows 10 podem ser encontradas [aqui](https://msdn.microsoft.com/commandline/wsl/install_guide).

2. Por meio do download de um aplicativo Win32/64 fornecido pela comunidade. Embora a OpenSSL Software Foundation não forneça, nem endosse, qualquer instalador específico do Windows, ela fornece uma lista dos instaladores disponíveis [aqui](https://wiki.openssl.org/index.php/Binaries)

### <a name="convert-your-cer-certificate-to-a-local-pem"></a>Converter seu certificado .cer para um .pem local
O arquivo de CA Raiz baixado estará no formato **.cer**. Você precisará usar o OpenSSL para converter o arquivo de certificação em um **.pem**.  Para fazer isso, execute a ferramenta de linha de comando openssl.exe e execute o seguinte comando: 

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.cer -out MyServerCACert.pem
```
Agora que você criou o arquivo de certificado (MyServerCACert.pem), pode conectar-se ao seu servidor de banco de dados com segurança por SSL. 

Os exemplos a seguir mostram como se conectar ao servidor MySQL pela interface de linha de comando do MySQL e por meio do MySQL Workbench, usando o arquivo **MyServerCACert.pem**.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Conectar-se ao servidor usando a CLI do MySQL sobre SSL
Usando a interface de linha de comando do MySQL, execute o seguinte comando:

```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -uUsername@mysqlserver4demo -pYourPassword --ssl-ca=c:\ssl\MyServerCACert.pem
```
Execute o comando **status** de mysql para verificar se você se conectou ao servidor MySQL usando o SSL:

```dos
mysql> status
--------------
mysql.exe  Ver 14.14 Distrib 5.7.18, for Win64 (x86_64)

Connection id:          65535
Current database:
Current user:           jason@66.235.55.141
SSL:                    Cipher in use is AES256-SHA
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             janderserver.mysql.sqltest-eg1.mscds.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    cp850
Conn.  characterset:    cp850
TCP port:               3306
Uptime:                 1 day 19 hours 9 min 43 sec

Threads: 4  Questions: 26082  Slow queries: 0  Opens: 112  Flush tables: 1  Open tables: 105  Queries per second avg: 0.167
--------------
```

> [!NOTE]
> Atualmente, há um problema conhecido em que, se você usar a opção "--ssl-mode=VERIFY_IDENTITY" em sua conexão mysql.exe com o serviço, a conexão falhará com o seguinte erro: _ERRO 2026 (HY000): erro de conexão SSL: Falha de validação do certificado SSL_ Faça um downgrade para "--ssl-mode=VERIFY_CA" ou [modos SSL](https://dev.mysql.com/doc/refman/5.7/en/secure-connection-options.html#option_general_ssl-mode) inferiores. Se precisar usar "--ssl-mode=VERIFY_IDENTITY", então você poderá realizar o ping do nome do seu servidor para resolver o nome do servidor regional, como westeurope1-a.control.database.windows.net, e usar esse nome do servidor regional na conexão até que esse problema seja resolvido. Planejamos remover esta limitação no futuro. 

### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Conectar-se ao servidor usando a o MySQL Workbench sobre SSL
A configuração do MySQL Workbench para se conectar com segurança via SSL exige que você navegue até a guia **SSL** na caixa de diálogo Configurar Nova Conexão do MySQL Workbench e insira o local do arquivo **MyServerCACert.pem** no campo **Arquivo de CA SSL:**.
![salvar bloco personalizado](./media/concepts-ssl-connection-security/mysql-workbench-ssl.png)

## <a name="next-steps"></a>Próximas etapas
- Analise as várias opções de conectividade do aplicativo em [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](concepts-connection-libraries.md)

