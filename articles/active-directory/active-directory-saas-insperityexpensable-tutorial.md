---
title: "Tutorial: integração do Azure Active Directory com o Insperity ExpensAble | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Insperity ExpensAble."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c579c453-580e-417d-8a5e-9b6b352795c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf5588885de9c280eb70712dbf800efe509ee912
ms.openlocfilehash: b009b203edadae92907aecd2a4eb626815492749


---
# <a name="tutorial-azure-active-directory-integration-with-insperity-expensable"></a>Tutorial: integração do Azure Active Directory com o Insperity ExpensAble
O objetivo desse tutorial é mostrar como integrar o Insperity ExpensAble ao Azure AD (Azure Active Directory).  
A integração do Insperity ExpensAble ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Insperity ExpensAble
* É possível permitir que seus usuários façam logon automaticamente no Insperity ExpensAble (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Insperity ExpensAble, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Insperity ExpensAble com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Insperity ExpensAble a partir da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-insperity-expensable-from-the-gallery"></a>Adição do Insperity ExpensAble a partir da galeria
Para configurar a integração do Insperity ExpensAble com o Azure AD, você precisa adicionar o Insperity ExpensAble a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Insperity ExpensAble a partir da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Insperity ExpensAble**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_01.png)
7. No painel de resultados, selecione **Insperity ExpensAble**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Insperity ExpensAble, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Insperity ExpensAble é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Insperity ExpensAble.  
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Username** no Insperity ExpensAble.

Para configurar e testar o logon único do Azure AD com o Insperity ExpensAble, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Insperity ExpensAble](#creating-a-insperityexpensable-test-user)** – para ter um equivalente de Brenda Fernandes no Insperity ExpensAble vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do Insperity ExpensAble.

**Para configurar o logon único do Azure AD com o Insperity ExpensAble, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos **Insperity ExpensAble**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você gostaria que os usuários fizessem logon no Insperity ExpensAble**, selecione **Logon Único do Azure AD**, em seguida, clique em **Próximo**.
   
    ![Configurar Logon Único](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_04.png) 

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo Insperity ExpensAble usando o seguinte padrão: `https://server.expensable.com/esapp/Authenticate?companyId=<company ID>`

    b. Clique em **Avançar**.

1. Na página **Configurar logon único no Insperity ExpensAble** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_05.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.

2. Para configurar o SSO em seu aplicativo, entre em contato com a equipe de suporte técnico do Insperity ExpensAble. Depois que o caso for atribuído, envie o arquivo de certificado baixado por email. Forneça também a URL do Emissor e a URL do Serviço de Logon para que elas possam ser configuradas para a integração com o SSO. 

3. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
4. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-insperityexpensable-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-insperity-expensable-test-user"></a>Criar um usuário de teste do Insperity ExpensAble
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Insperity ExpensAble. Trabalhe com a equipe de suporte do Insperity ExpensAble para adicionar os usuários na conta do Insperity ExpensAble. 

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Insperity ExpensAble.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao Insperity ExpensAble.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Insperity ExpensAble, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **Insperity ExpensAble**.
   
    ![Configurar Logon Único](./media/active-directory-saas-insperityexpensable-tutorial/tutorial_insperityexpensable_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Ao clicar no bloco Insperity ExpensAble no Painel de Acesso, você deve fazer logon automaticamente no aplicativo do Insperity ExpensAble.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-insperityexpensable-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO2-->


