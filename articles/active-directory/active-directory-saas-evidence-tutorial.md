---
title: "Tutorial: integração do Azure Active Directory ao Evidence.com | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Evidence.com."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f9a7cb7c-ff67-40dc-872c-1fa35f9dd03b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d5183b136a0ceca939f754f67130d1179f6f4f7


---
# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Tutorial: Integração do Active Directory do Azure ao Evidence.com
O objetivo deste tutorial é mostrar como configurar o logon único entre o AAD (Active Directory do Azure) e o Evidence.com. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Microsoft Azure
* Uma assinatura do Evidence.com com logon único habilitado (envie um email para earlyaccess@evidence.com caso o logon único baseado em SAML não esteja habilitado)

Depois de concluir este tutorial, os usuários do AAD aos quais você atribuiu acesso ao Evidence.com poderão fazer logon único no aplicativo usando o Painel de Acesso do AAD.

## <a name="add-evidencecom-to-your-directory"></a>Adicionar o Evidence.com ao seu diretório
Esta seção descreve como adicionar Evidence.com como um aplicativo integrado ao Active Directory do Azure.

**Para habilitar a integração de aplicativos no Evidence.com:**

1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
4. Para abrir a Galeria de Aplicativos, clique em **Adicionar** e em **Adicionar um aplicativo da galeria**.
5. Na caixa de pesquisa, digite **Evidence.com**.
6. No painel de resultados, selecione **Evidence.com** e clique em **Concluir** para adicionar o aplicativo.

## <a name="configuring-single-sign-on"></a>Configurando o logon único
Esta seção descreve como permitir que os usuários autentiquem no Evidence.com usando suas contas do Active Directory do Azure e a federação baseada no protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. Depois de adicionar o Evidence.com no Portal Clássico do Azure, clique em **Configurar Logon Único**. 
2. Na tela seguinte, escolha **Logon Único do Azure AD** e clique em **Avançar**.
3. Na tela Configurar URL do Aplicativo, insira a URL na qual os usuários entrarão em sua URL de locatário do Evidence.com (exemplo: https://yourtenant.evidence.com) e clique em **Avançar**. 
4. Clique no link **Baixar Certificado** e salve-o em sua unidade local. Esse certificado e as URLs de metadados (ID da Entidade, URL de Entrada e URL de saída de SSO) serão usados para configurar o SSO no site Evidence.com. 
5. Em uma janela separada do navegador da Web, faça logon como administrador em seu locatário do Evidence.com e navegue até a guia **Administrador**
6. Clique em **Logon Único de Agência**
7. Escolha **Logon Único Baseado em SAML**
8. Copie os valores de **URL do Emissor**, **Logon Único** e **Saída Única** mostrados no Portal clássico do Azure e para os campos correspondentes em Evidence.com.
9. Abra o certificado baixado na etapa 4 usando um editor de texto, como Notepad.exe, e copie e cole o conteúdo na caixa **Certificado de Segurança** . 
10. Salve a configuração no Evidence.com.
11. No portal clássico do Azure, marque a caixa de seleção **Confirmar que você configurou o logon único conforme descrito acima**. A marcação dessa opção permitirá que o certificado atual comece a trabalhar para esse aplicativo.
12. Na página de confirmação de logon único, clique em **Concluir**.  

## <a name="creating-an-evidencecom-test-user"></a>Criação de um usuário de teste de Evidence.com
Para que os usuários do AD do Azure possam fazer logon, eles deverão receber a permissão de acesso dentro do aplicativo Evidence.com. Esta seção descreve como criar contas de usuário do AD do Azure no Evidence.com.

**Para provisionar uma conta de usuário no Evidence.com:**

1. Em uma janela do navegador da Web, faça logon no site de sua empresa do Evidence.com como administrador.
2. Navegue até a guia **Administrador** .
3. Clique em **Adicionar Usuário**.
4. Clique no botão **Adicionar** .
5. O **Endereço de Email** do usuário adicionado deverá coincidir com o nome de usuário dos usuários no AD do Azure aos quais você deseja conceder acesso. Se o nome de usuário e o endereço de email não tiverem o mesmo valor em sua organização, use a seção **Evidence.com > Atributos > Logon Único** do Portal clássico do Azure para alterar o identificador de nome enviado ao Evidence.com para ser o endereço de email.

## <a name="assigning-users-to-evidencecom"></a>Atribuição de usuários ao Evidence.com
Para que os usuários provisionados do AAD possam ver o Evidence.com em seus Painéis de Acesso, deverão obter o acesso no portal clássico do Azure.

**Para atribuir usuários ao Evidence.com:**

1. Na página de início rápido do Evidence.com no portal clássico do Azure, clique em **Atribuir Usuários ao Evidence.com**.
2. No menu **Mostrar** , selecione se deseja atribuir um usuário ou um grupo ao Evidence.com e clique no botão Marca de Seleção.
3. Na lista **Usuários** , escolha o usuário ou o grupo ao qual você deseja atribuir ao Evidence.com.
4. No rodapé da página, clique no botão **Atribuir** .




<!--HONumber=Nov16_HO3-->


