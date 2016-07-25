---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: powershell,cmdlet,jea
ms.date: 2016-06-22
title: "повторное создание демонстрационной конечной точки"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: d20ea8418cb7389d756de94ea752cf604b8d07af
ms.openlocfilehash: acd2cfbd038250a26236c875d0e8b03a32cd84f9

---

# Повторное создание демонстрационной конечной точки
В этом разделе вы узнаете, как создать точную копию демонстрационной конечной точки, которая использовалась в предыдущем разделе.
Здесь представлены основные понятия, необходимые для понимания JEA, включая конфигурации сеансов и возможности ролей PowerShell.

## Конфигурации сеансов PowerShell
При работе JEA в предыдущем разделе вы начали со следующей команды:

```PowerShell
Enter-PSSession -ComputerName . -ConfigurationName JEA_Demo -Credential $NonAdminCred
```

Хотя большинство параметров говорят сами за себя, параметр *ConfigurationName* сначала может показаться противоречивым.
Этот параметр задается в конфигурации сеанса PowerShell, к которому вы подключаетесь.

*Конфигурация сеанса PowerShell* — еще один термин для обозначения конечной точки PowerShell.
Это то "место", где пользователи могут подключаться и получать доступ к функциональным возможностям PowerShell.
В зависимости от настройки конфигурация сеанса может предоставлять подключающимся пользователям различные функциональные возможности.
В JEA конфигурации сеансов используются для того, чтобы ограничить PowerShell определенным набором функциональных возможностей и вести работу от имени привилегированной виртуальной учетной записи.

На вашем компьютере уже есть несколько зарегистрированных конфигураций сеансов PowerShell, каждая из которых настроена несколько иначе, чем остальные.
Большинство из них входят в состав Windows, но конфигурация сеанса "JEA_Demo" была настроена в разделе [предварительных требований](prerequisites.md).
Чтобы увидеть все зарегистрированные конфигурации сеансов, выполните в командной строке администратора PowerShell следующую команду:

```PowerShell
Get-PSSessionConfiguration
```

## Файлы конфигурации сеансов PowerShell
Новые конфигурации сеансов PowerShell создаются путем регистрации новых *файлов конфигурации сеансов PowerShell*.
Файлы конфигурации сеансов имеют расширение PSSC.
Файл конфигурации сеанса можно создать с помощью командлета New-PSSessionConfigurationFile.

Теперь создадим и зарегистрируем новую конфигурацию сеанса для JEA.

## Создание и изменение конфигурации сеанса PowerShell
Для создания "каркасного" файла конфигурации сеанса PowerShell выполните следующую команду:

```PowerShell
New-PSSessionConfigurationFile -Path "$env:ProgramData\JEAConfiguration\JEADemo2.pssc"
```

> **Совет.** По умолчанию каркасный файл включает только основные настройки конфигурации.
> Чтобы включить в создаваемый PSSC-файл все применимые настройки, используйте параметр `-Full`.

Откройте файл в интегрированной среде сценариев PowerShell или в любом удобном текстовом редакторе.

```PowerShell
ise "$env:ProgramData\JEAConfiguration\JEADemo2.pssc"
```

Измените в нем следующие поля, указав приведенные ниже значения (не забудьте указать собственную группу безопасности без прав администратора).

```PowerShell
# OLD: SessionType = 'Default'
SessionType = 'RestrictedRemoteServer'

# OLD: TranscriptDirectory = 'C:\Transcripts\'
TranscriptDirectory = "C:\ProgramData\JEAConfiguration\Transcripts"

# OLD: # RunAsVirtualAccount = $true
RunAsVirtualAccount = $true

# OLD: RoleDefinitions = @{ 'CONTOSO\SqlAdmins' = @{ RoleCapabilities = 'SqlAdministration' }; 'CONTOSO\ServerMonitors' = @{ VisibleCmdlets = 'Get-Process' } }
RoleDefinitions = @{'CONTOSO\JEA_NonAdmin_Operator' = @{ RoleCapabilities =  'Maintenance' }}
```

Вот что означает каждая из этих записей:

1.  Поле *SessionType* определяет предустановленные параметры по умолчанию для конечной точки.
*RestrictedRemoteServer* определяет минимальный набор параметров, необходимых для удаленного управления.
По умолчанию конечная точка *RestrictedRemoteServer* имеет параметры Get-Command, Get-FormatData, Select-Object, Get-Help, Measure-Object, Exit-PSSession, Clear-Host и Out-Default.
Она устанавливает для параметра *ExecutionPolicy* значение *RemoteSigned*, а для параметра *LanguageMode* значение *NoLanguage*.
По итогам применения этих параметров создается минимальная безопасная основа для настройки конечной точки.

2.  Поле *RoleDefinitions* назначает возможности ролей для отдельных групп.
Оно определяет, какие действия разрешает выполнять привилегированная учетная запись.
В этом поле можно указать функциональные возможности, которые будут предоставлены подключившемуся пользователю в зависимости от членства в группах.
Это основные функциональные возможности элемента RBAC в JEA.
В этом примере членам группы "Contoso\JEA_NonAdmin_Operator" предоставляются возможности роли "Maintenance".

3.  Поле *RunAsVirtualAccount* означает, что PowerShell следует запустить от имени виртуальной учетной записи в этой конечной точке.
По умолчанию виртуальная учетная запись входит во встроенную группу администраторов.
На контроллере домена она также по умолчанию входит в группу администраторов домена.
Далее в этом руководстве вы узнаете, как настроить права доступа для виртуальной учетной записи.

4.  Поле *TranscriptDirectory* определяет, где будут сохраняться созданные детализированные записи PowerShell с "подсматриванием" после каждого удаленного сеанса.
Эти записи позволяют отслеживать, какие действия были выполнены в каждом сеансе.
Дополнительные сведения о записях PowerShell см. в этой [записи в блоге](http://blogs.msdn.com/b/powershell/archive/2015/06/09/powershell-the-blue-team.aspx).
Примечание. В обычные журналы событий Windows также записываются данные о том, какие команды каждый пользователь выполнил в PowerShell.
При этом записи, создаваемые здесь, более удобны для чтения.

Наконец, сохраните изменения в *JEADemo2.pssc*.

## Применение конфигурации сеанса PowerShell

Для создания конечной точки из файла конфигурации сеанса необходимо зарегистрировать файл.
При этом требуется несколько фрагментов данных:

1. Путь к файлу конфигурации сеанса.
2. Имя зарегистрированной конфигурации сеанса. Это то же имя, которое пользователи указывают как значение параметра "ConfigurationName" при подключении конечной точки к `Enter-PSSession` или `New-PSSession`.

Чтобы зарегистрировать конфигурацию сеанса на локальном компьютере, выполните следующую команду:

```PowerShell
Register-PSSessionConfiguration -Name 'JEADemo2' -Path "$env:ProgramData\JEAConfiguration\JEADemo2.pssc"
```

Все готово. Вы настроили конечную точку JEA.

## Тестирование конечной точки
Повторите шаги, указанные в разделе [Использование JEA](using-jea.md), с новой конечной точкой, чтобы убедиться в том, что она работает, как предполагалось.
Задавая имя конфигурации в команде `Enter-PSSession`, используйте имя новой конечной точки (JEADemo2).

```PowerShell
Enter-PSSession -ComputerName . -ConfigurationName JEADemo2 -Credential $NonAdminCred
```

## Основные понятия
**Конфигурация сеанса PowerShell**: иногда называется *конечной точкой PowerShell*; это то "место", где пользователи могут подключаться к PowerShell и получать доступ к его функциональным возможностям.
Чтобы получить список конфигураций сеансов, зарегистрированных в системе, выполните команду `Get-PSSessionConfiguration`.
При определенной настройке конфигурацию сеанса PowerShell можно назвать *конечной точкой JEA*.

**Файл конфигурации сеанса PowerShell (PSSC)**: файл, который, будучи зарегистрированным, определяет параметры конфигурации сеанса PowerShell.
Он содержит спецификации для ролей пользователей, которые могут подключаться к конечной точке, виртуальную запись, с использованием которой он запускается, и многое другое.     

**Определения ролей**: поле в файле конфигурации сеанса, задающее возможности ролей, предоставляемые подключающимся пользователям.
Оно определяет, *кому* и *какие* действия разрешает выполнять привилегированная учетная запись.
Это основные функциональные возможности RBAC в JEA.

**SessionType**: поле в файле конфигурации сеанса, представляющее параметры по умолчанию для конфигурации сеанса.
У конечных точек JEA оно должно иметь значение RestrictedRemoteServer.

**PowerShell Transcript**: файл, содержащий представление сеанса PowerShell с "подсматриванием".
В PowerShell можно настроить создание записей сеансов JEA с помощью поля TranscriptDirectory.
Дополнительные сведения о записях см. в этой [записи в блоге](https://technet.microsoft.com/en-us/magazine/ff687007.aspx).




<!--HONumber=Jul16_HO1-->

