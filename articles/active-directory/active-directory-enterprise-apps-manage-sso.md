---
title: "Gerenciamento de logon único para aplicativos empresariais no Azure Active Directory | Microsoft Docs"
description: "Saiba como gerenciar o logon único para aplicativos empresariais usando o Azure Active Directory"
services: active-directory
documentationcenter: 
author: asmalser
manager: femila
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: asmalser
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 118b77064a96585b0d5b951ca56313776021624e
ms.contentlocale: pt-br
ms.lasthandoff: 05/08/2017


---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Gerenciar o logon único para aplicativos empresariais
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-enterprise-apps-manage-sso.md)
> * [Portal clássico do Azure](active-directory-sso-integrate-saas-apps.md)
> 

Este artigo descreve como usar o [Portal do Azure](https://portal.azure.com) para gerenciar configurações de logon único para aplicativos, especialmente os que foram adicionados da [galeria de aplicativos do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps"></a>Localizando seus aplicativos
## <a name="finding-your-apps-in-the-portal"></a>Localizar seus aplicativos no portal
Todos os aplicativos configurados para logon único em um diretório por um administrador de diretório usando a [galeria de aplicativos do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) podem ser exibidos e gerenciados no [Portal do Azure](https://portal.azure.com). Os aplicativos podem ser encontrados na seção **Mais Serviços** &gt; **Aplicativos Empresariais** do Portal. Aplicativos empresariais são aplicativos que são implantados e usados dentro da sua organização.

![Folha de Aplicativos Empresariais][1]

Selecione **Todos os aplicativos** para exibir uma lista de todos os aplicativos que foram configurados, incluindo aplicativos que foram adicionados da galeria. A seleção de um aplicativo carrega a folha de recursos do aplicativo, na qual os relatórios podem ser exibidos para que o aplicativo e várias configurações possam ser gerenciados.

Para gerenciar as configurações de logon único, selecione **Logon único**.

![Folha de recursos do aplicativo][2]

## <a name="single-sign-on-modes"></a>Modos de logon único
A folha **Logon único** começa com um menu **Modo**, que permite que o modo de logon único seja configurado. As opções disponíveis incluem:

* **Logon baseado em SAML** - essa opção estará disponível se o aplicativo der suporte ao logon único federado completo com o Azure Active Directory usando o protocolo SAML 2.0.
* **Logon baseado em senha em** -essa opção está disponível se o Azure AD dá suporte ao preenchimento de formulário de senha para este aplicativo.
* **Logon vinculado** - anteriormente conhecida como "Logon único existente", essa opção permite que os administradores coloquem um link para esse aplicativo no painel de acesso do Azure AD ou no iniciador de aplicativo do Office 365 do usuário.

Para obter mais informações sobre esses modos, confira [Como o logon único funciona com o Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Logon baseado em SAML
A opção **logon baseado em SAML** exibe uma folha que é dividida em quatro seções:

### <a name="domains-and-urls"></a>Domínios e URLs
É onde todos os detalhes sobre o domínio do aplicativo e as URLs são adicionados ao seu diretório do Azure AD. Todas as entradas necessárias para criar o aplicativo de logon único são exibidas diretamente na tela, enquanto todas as entradas opcionais podem ser exibidas marcando a caixa de seleção **Mostrar configurações de URL avançadas** . A lista completa de entradas com suporte inclui:

* **URL de logon** – onde o usuário vai para entrar nesse aplicativo. Se o aplicativo estiver configurado para executar logon único iniciado pelo provedor de serviços, quando um usuário navegar para essa URL, o provedor de serviços fará o redirecionamento necessário para o Azure AD a fim de autenticar e conectar o usuário. Se esse campo estiver preenchido, o Azure AD usará essa URL para iniciar o aplicativo do Office 365 e o painel de acesso do Azure AD. Se esse campo for omitido, o Azure AD executará, em vez disso, o logon iniciado pelo provedor de identidade quando o aplicativo for iniciado do Office 365, do painel de acesso do Azure AD ou da URL de logon único do Azure AD.
* **Identificador** - esse URI deve identificar exclusivamente o aplicativo para o qual o SSO está sendo configurado. Esse é o valor que o Azure AD envia para o aplicativo como o parâmetro Audiência do token SAML e o aplicativo deve validá-lo. Esse valor também aparece como a ID da entidade em todos os metadados SAML fornecidos pelo aplicativo.
* **URL de resposta** – a URL de resposta é onde o aplicativo espera receber o token SAML. Ela também é chamada de URL de ACS (Serviço do Consumidor de Declaração). Depois que eles tiverem sido inseridos, clique em Avançar para prosseguir para a próxima tela. Esta tela fornece informações sobre o que precisa ser configurado no lado do aplicativo para habilitá-lo a aceitar um token SAML do AD do Azure.
* **Estado de Retransmissão** - o estado de retransmissão é um parâmetro opcional que pode ajudar a informar o aplicativo para onde redirecionar o usuário após a autenticação. Normalmente, o valor é uma URL válida no aplicativo. No entanto, alguns aplicativos usam esse campo de forma diferente (confira a documentação do logon único do aplicativo para obter detalhes). A capacidade de definir o estado de retransmissão é um novo recurso que é exclusivo do novo portal do Azure.

### <a name="user-attributes"></a>Atributos de usuário
É onde administradores podem exibir e editar os atributos que são enviados no token SAML que o Azure AD emite para o aplicativo sempre que os usuários entram.

O único atributo editável com suporte é o atributo **Identificador de Usuário**. O valor desse atributo é o campo no Azure AD que identifica exclusivamente cada usuário no aplicativo. Por exemplo, se o aplicativo foi implantado usando "Endereço de Email" como o nome de usuário e o identificador exclusivo, o valor deve ser definido para o campo "user.mail" no Azure AD.

### <a name="saml-signing-certificate"></a>Certificado de assinatura de SAML
Esta seção mostra os detalhes do certificado que o Azure AD usa para assinar tokens SAML emitidos para o aplicativo sempre que o usuário é autenticado. Ele permite que as propriedades do certificado atual sejam inspecionadas, incluindo a data de validade.

### <a name="application-configuration"></a>Configuração de aplicativo
A seção final fornece a documentação e/ou os controles necessários para configurar o aplicativo para usar o Azure Active Directory como um provedor de identidade.

O menu suspenso **Configurar Aplicativo** fornece novas instruções concisas internas para configurar o aplicativo. Esse é outro novo recurso exclusivo para o novo portal do Azure.

> [!NOTE]
> Para obter um exemplo completo de documentação interna, confira o aplicativo do Salesforce.com. A documentação de aplicativos adicionais está sendo adicionada continuamente.
> 
> 

![Documentos internos][3]

## <a name="password-based-sign-on"></a>Logon baseado em senha em
Se houver suporte para o aplicativo, selecionar o modo SSO baseado em senha e selecionar **Salvar** configura-o instantaneamente para fazer o SSO baseado em senha. Para obter mais informações sobre a implantação de SSO baseada em senha, confira [Como o logon único funciona com o Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Logon baseado em senha em][4]

## <a name="linked-sign-on"></a>Logon vinculado
Se houver suporte para o aplicativo, selecionar o modo de SSO vinculado permite que você insira a URL para a qual deseja que o painel de acesso do Azure AD ou do Office 365 redirecione quando os usuários clicam nesse aplicativo. Para obter mais informações sobre o SSO vinculado (anteriormente conhecido como "SSO existente"), confira [Como o logon único funciona com o Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Logon vinculado][5]

##<a name="feedback"></a>Comentários

Esperamos que você goste de usar a experiência aprimorada do Azure AD. Continue a fazer seus comentários! Poste seus comentários e suas ideias para aprimoramento na seção **Portal de Administração** do nosso [fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Nós estamos empolgados para criar algo novo e interessante diariamente e usar suas diretrizes para formar e definir o que devemos criar a seguir.

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG

