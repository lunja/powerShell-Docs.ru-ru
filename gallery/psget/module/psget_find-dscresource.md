---
ms.date: 2017-06-12
contributor: manikb
ms.topic: reference
keywords: "коллекция,powershell,командлет,psget"
title: Find-DscResource
ms.openlocfilehash: 37ba7925d6f73c453126f25e0818b3f8839d3b3b
ms.sourcegitcommit: 75f70c7df01eea5e7a2c16f9a3ab1dd437a1f8fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2017
---
<a id="find-dscresource" class="xliff"></a>
# Find-DscResource

Находит ресурсы DSC в модулях.

<a id="description" class="xliff"></a>
## Описание

Командлет Find-DscResource ищет ресурсы [настройки требуемого состояния (DSC)](https://msdn.microsoft.com/en-us/PowerShell/dsc/overview), имеющиеся в модулях, соответствующих заданному условию, из зарегистрированных репозиториев.
Для каждого модуля, найденного командлетом Find-DscResource, он возвращает объект PSGetDscResourceInfo, который можно передать в командлет Install-Module, чтобы установить возвращенные этим командлетом модули, содержащие ресурсы.

DSC — это новая платформа управления в Windows PowerShell, позволяющая развертывать данные конфигураций для служб программного обеспечения, а также управлять этими данными и средой, в которой выполняются такие службы.

Ресурсы службы настройки требуемого состояния (DSC) предоставляют шаблоны для настройки DSC. В ресурсе представлены свойства, которые можно настроить (схема), и функции сценариев PowerShell, которые вызывает локальный диспетчер конфигураций (LCM).

Ресурс может моделировать что-либо универсальное, например файл, или конкретное, например настройку сервера IIS. Группы похожих ресурсов объединяются в DSC-модуль, в котором все необходимые файлы упорядочиваются в переносимую структуру и включаются метаданные для идентификации целевого предназначения ресурсов.

- Командлет Find-DscResource позволяет выполнять фильтрацию с помощью параметров версии: MinimumVersion, RequiredVersion, AllVersions.
  - Эти параметры являются взаимоисключающими.
  - Эти параметры версии допускаются только с единственным именем модуля без каких-либо подстановочных знаков.
  - Если параметр RequiredVersion не указан, командлет Find-DscResource возвращает последнюю версию модуля не ниже указанной минимальной версии или последнюю версию модуля, если минимальная версия не указана.
  - Если параметр RequiredVersion указан, Find-DscResource возвращает только версию модуля, которая точно совпадает с указанной версией.
- Find-DscResource позволяет фильтровать метаданные модуля с помощью параметра -Tag
- Find-DscResource позволяет фильтровать язык поиска для определенного репозитория с помощью параметра -Filter.
- Find-DscResource может фильтровать модули из всех или некоторых из зарегистрированных репозиториев.

<a id="cmdlet-syntax" class="xliff"></a>
## Синтаксис командлета
```powershell
Get-Command -Name Find-DscResource -Module PowerShellGet -Syntax
```

<a id="cmdlet-online-help-reference" class="xliff"></a>
## Ссылка на раздел справки по командлету в Интернете

[Find-DscResource](http://go.microsoft.com/fwlink/?LinkId=517196)

<a id="example-commands" class="xliff"></a>
## Примеры команд
```powershell

# Find a specific DSC Resource
Find-DscResource -Name xIisFeatureDelegation

Name                                Version    ModuleName                          Repository
----                                -------    ----------                          ----------
xIisFeatureDelegation               1.10.0.0   xWebAdministration                  PSGallery

# Find all available DSC Resources from all registered repositories
Find-DscResource

# Find a DSC resource by name
Find-DscResource -Name xWebsite

# Find multiple DSC Resources
Find-DscResource -Name xIisHandler, xFirewall

# Find all DSC resources contained within a specific module
Find-DscResource -ModuleName xNetworking
Find-DscResource -ModuleName xWebAdministration

# Find all DSC resources in modules with DSCResourceKit or DesiredStateConfiguration
Find-DscResource -Tag DesiredStateConfiguration, DSCResourceKit

# Find available DSC Resources from few registered repositories
Find-DscResource -Repository PSGallery,PrivatePSGallery

# Find all DSC Resources in a specified repository
Find-DscResource -Repository PSGallery

# Find DSC Resources from all versions of a module
Find-DscResource -ModuleName xNetworking -AllVersions

# Find DSC Resources with module name and MinimumVersion.
Find-DscResource -ModuleName xNetworking -MinimumVersion 1.1

# Find DSC Resources with module name and exact version
Find-DscResource -ModuleName xNetworking -RequiredVersion 2.1.1

# Find DSC Resources defined modules with -Filter based search. -Filter searches in description and module names
Find-DscResource -Filter Domain

# Find all DSC Resources with tags Azure or DSC in module metadata
Find-DscResource -Tag Azure, DSC

```

