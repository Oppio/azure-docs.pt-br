---
title: "Pré-requisitos do Catálogo de Dados do Azure | Microsoft Docs"
description: "Pré-requisitos do Catálogo de Dados do Azure: o que você precisa para começar a usar o Catálogo de Dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 412063692e87fe71890f62bcc18b99b317a80986
ms.contentlocale: pt-br
ms.lasthandoff: 11/17/2016


---
# <a name="azure-data-catalog-prerequisites"></a>Pré-requisitos de Catálogo de Dados do Azure
## <a name="what-do-i-need-to-get-started-with-azure-data-catalog"></a>O que é necessário para começar a usar o Catálogo de Dados do Azure?
Há algumas coisas que você precisará cuidar antes de instalar o **Catálogo de Dados do Azure**. Não se preocupe – isso não vai demorar!

## <a name="azure-subscription"></a>Assinatura do Azure
Para configurar o Catálogo de Dados do Azure, é necessário ser proprietário ou coproprietário de uma assinatura do Azure.

As assinaturas do Azure ajudam a organizar o acesso aos recursos de serviço de nuvem como o Catálogo de Dados do Azure. Eles também ajudam a controlar como o uso de recursos é reportado, cobrado e pago. Cada assinatura pode ter uma configuração diferente de cobrança e pagamento, assim você pode ter diferentes assinaturas e planos diferentes por departamento, projeto, escritório regional, etc. Cada serviço de nuvem pertence a uma assinatura, e você precisa ter uma assinatura antes de configurar o Catálogo de Dados do Azure. Para saber mais, consulte [Gerenciar Contas, Assinaturas e Funções Administrativas](../active-directory/active-directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Para configurar o Catálogo de Dados do Azure, você deve estar conectado usando uma conta de usuário do Azure Active Directory.

O Active Directory do Azure (AD do Azure) fornece uma maneira fácil para a sua empresa gerenciar identidades e acesso, tanto na nuvem quanto local. Os usuários podem usar uma única conta de trabalho ou da escola para logon único em qualquer nuvem e aplicativo Web local. O Catálogo de Dados do Azure usa o AD do Azure para autenticar o logon. Para saber mais, confira [O que é o Azure Active Directory](../active-directory/active-directory-whatis.md).

> [!NOTE]
> O [portal do Azure](http://portal.azure.com/) permite que os usuários entrem usando uma Conta pessoal da Microsoft ou uma conta corporativa ou de estudante do Azure Active Directory. Para configurar o Catálogo de Dados do Azure usando o portal do Azure ou usando o [portal do Catálogo de Dados](http://www.azuredatacatalog.com) , você deve estar conectado usando uma conta do Azure Active Directory, não uma conta pessoal.
>
>

## <a name="active-directory-policy-configuration"></a>Configuração de política do Active Directory
Em algumas situações, os usuários podem encontrar uma situação em que podem acessar o portal do Catálogo de Dados do Azure, mas quando tentam fazer logon na ferramenta de registro da fonte de dados encontram uma mensagem de erro que impede o logon. Esse comportamento de problema pode ocorrer apenas quando o usuário está na rede da empresa, ou quando está se conectando de fora da rede da empresa.

A ferramenta de registro de fonte de dados usa a Autenticação de Formulários para validar logons de usuário no Active Directory. Para um logon bem-sucedido, a autenticação de formulários deve ser habilitada na Política de Autenticação Global por um administrador do Active Directory.

A Política de Autenticação Global permite que os métodos de autenticação sejam habilitados separadamente para conexões intranet e extranet, conforme ilustrado abaixo. Erros de logon poderão ocorrer se a autenticação de formulários não estiver habilitada para a rede por meio da qual o usuário está se conectando.

 ![Política de Autenticação Global do Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obter mais informações, consulte [Configurando políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).

