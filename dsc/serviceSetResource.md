---
ms.date: 2017-06-12
author: eslesar
ms.topic: conceptual
keywords: "dsc,powershell,конфигурация,установка"
title: "Ресурс DSC ServiceSet"
ms.openlocfilehash: 92fa4a442eb42e89195162b7831f1a96d40b84f5
ms.sourcegitcommit: 75f70c7df01eea5e7a2c16f9a3ab1dd437a1f8fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2017
---
<a id="dsc-serviceset-resource" class="xliff"></a>
# Ресурс DSC ServiceSet

> Область применения: Windows PowerShell 4.0, Windows PowerShell 5.0


Ресурс **ServiceSet** в DSC Windows PowerShell предоставляет механизм управления службами на целевом узле. Он является [составным ресурсом](authoringResourceComposite.md), который вызывает [ресурс Service](serviceResource.md) для каждой службы, указанной в свойстве `Name`.

Используйте этот ресурс, если нужно настроить одинаковое состояние для нескольких служб.

<a id="syntax" class="xliff"></a>
## Синтаксис

```
Service [string] #ResourceName
{
    Name = [string[]]
    [ StartupType = [string] { Automatic | Disabled | Manual }  ]
    [ BuiltInAccount = [string] { LocalService | LocalSystem | NetworkService }  ]
    [ State = [string] { Running | Stopped }  ]
    [ Ensure = [string] { Absent | Present }  ]
    [ Credential = [PSCredential] ]
    [ DependsOn = [string[]] ]
    
}
```

<a id="properties" class="xliff"></a>
## Свойства

|  Свойство  |  Описание   | 
|---|---| 
| Название| Указывает имена служб. Обратите внимание на то, что иногда они отличаются от отображаемых имен. Список служб и их текущее состояние можно получить с помощью командлета [Get-Service](https://technet.microsoft.com/en-us/library/hh849804.aspx).|
| StartupType| Указывает тип запуска службы. Допустимые значения этого свойства: **Automatic**, **Disabled** и **Manual**.|  
| BuiltInAccount| Указывает учетную запись, используемую службами для входа. Допустимые значения этого свойства: **LocalService**, **LocalSystem** и **NetworkService**.| 
| State| Указывает состояние, в котором должны находиться службы: **Stopped** или **Running**.| 
| Ensure| Указывает, имеются ли службы в системе. Если службы не должны существовать, присвойте этому свойству значение **Absent**. Если целевые службы должны существовать, укажите значение **Present** (используется по умолчанию).|
| Учетные данные| Указывает учетные данные для учетной записи, от имени которой будет запускаться ресурс службы. Это свойство нельзя использовать одновременно со свойством **BuiltinAccount**.| 
| DependsOn| Указывает, что перед настройкой этого ресурса необходимо запустить настройку другого ресурса. Например, если идентификатор первого запускаемого блока сценария для конфигурации ресурса — *ResourceName*, а его тип — *ResourceType*, то синтаксис использования этого свойства таков: `DependsOn = "[ResourceType]ResourceName"`.| 



<a id="example" class="xliff"></a>
## Пример

Приведенная ниже конфигурация запускает службы "Windows Audio" и "Службы удаленных рабочих столов".

```powershell
configuration ServiceSetTest
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    Node localhost
    {

        ServiceSet ServiceSetExample
        {
            Name        = @("TermService", "Audiosrv")
            StartupType = "Manual"
            State       = "Running"
        } 
    }
}
```

