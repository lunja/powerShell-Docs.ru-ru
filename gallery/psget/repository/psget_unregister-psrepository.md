---
ms.date: 2017-06-12
contributor: manikb
ms.topic: reference
keywords: "коллекция,powershell,командлет,psget"
title: Unregister-PSRepository
ms.openlocfilehash: 91380210f262208fce39d596bd6c2ad05a819fbf
ms.sourcegitcommit: 75f70c7df01eea5e7a2c16f9a3ab1dd437a1f8fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2017
---
<a id="unregister-psrepository" class="xliff"></a>
# Unregister-PSRepository

Отмена регистрации репозитория.

<a id="description" class="xliff"></a>
## Описание

Командлет Unregister-PSRepository отменяет регистрацию репозитория для текущего пользователя.
- Отмена регистрации и повторная регистрация репозитория PSGallery допустима для корпоративного и автономного сценариев.
- Пользователи могут повторно зарегистрировать PSGallery, просто выполнив команду `Register-PSRepository -Default`.
- Так как PSGallery — это репозиторий для публикации по умолчанию в командлетах Publish-Module и Publish-Script, возникнет ошибка, если коллекция PSGallery недоступна в списке зарегистрированных репозиториев.

<a id="cmdlet-syntax" class="xliff"></a>
## Синтаксис командлета

```powershell
Get-Command -Name Unregister-PSRepository -Module PowerShellGet -Syntax
```
<a id="cmdlet-online-help-reference" class="xliff"></a>
## Ссылка на раздел справки по командлету в Интернете

[Unregister-PSRepository](http://go.microsoft.com/fwlink/?LinkID=517130)

<a id="example-commands" class="xliff"></a>
## Примеры команд

```powershell
Unregister-PSRepository -Name "MyPrivateGallery"

Get-PSRepository exp | Unregister-PSRepository
```

<a id="unregistration-and-re-registration-of-the-psgallery-repository-is-allowed-for-an-enterprise-and-disconnected-scenarios" class="xliff"></a>
### Отмена регистрации и повторная регистрация репозитория PSGallery допустима для корпоративного и автономного сценариев.
```powershell

# Unregister PSGallery repository
Unregister-PSRepository PSGallery

# Publish-Module throws an error when PSGallery is not a registered repository
Publish-Module -Name MyModule
publish-module : Unable to find repository 'PSGallery'. Use Get-PSRepository to see all available repositories. Try again after specifying a valid repository name. You can use 'Register-PSRepository -Default' to register the PSGallery repository.
At line:1 char:1
+ publish-module -name mymodule
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (PSGallery:String) [Publish-Module], ArgumentException
    + FullyQualifiedErrorId : PSGalleryNotFound,Publish-Module

# Re-register PSGallery repository
Register-PSRepository -Default
```

