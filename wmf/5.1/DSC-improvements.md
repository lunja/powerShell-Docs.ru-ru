---
title: "Усовершенствования DSC в WMF 5.1"
ms.date: 2016-07-13
keywords: PowerShell, DSC, WMF
description: 
ms.topic: article
author: keithb
manager: dongill
ms.prod: powershell
ms.technology: WMF
ms.openlocfilehash: 4c5dfaaf368097c18a2788a9df15632ce116dbbb
ms.sourcegitcommit: ee407927101c3b166cc200a39a6ea786a1c21f95
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2017
---
# <a name="improvements-in-desired-state-configuration-dsc-in-wmf-51"></a>Усовершенствования в настройке требуемого состояния (DSC) в WMF 5.1

## <a name="dsc-class-resource-improvements"></a>Усовершенствования в ресурсах классов DSC

В WMF 5.1 были устранены следующие известные проблемы.
* Командлет Get-DscConfiguration может возвращать пустые значения (NULL) или ошибки, если функция Get() ресурса DSC на основе классов возвращает сложный тип или хэш-таблицу.
* Командлет Get-DscConfiguration возвращает ошибку, если в конфигурации DSC используются учетные данные запуска от имени.
* Ресурсы на основе классов не могут использоваться в составной конфигурации.
* Командлет Start-DscConfiguration зависает, если ресурс на основе классов имеет свойство со своим собственным типом.
* Ресурсы на основе класса не могут использоваться в качестве монопольных ресурсов.


## <a name="dsc-resource-debugging-improvements"></a>Усовершенствования в отладке ресурсов DSC
В WMF 5.0 отладчик PowerShell не останавливался непосредственно в методах для работы с ресурсами, основанными на классах (Get, Set, Test).
В WMF 5.1 отладчик останавливается в методах для работы с ресурсами, основанными на классах, точно так же, как в методах для работы с ресурсами на основе MOF.

## <a name="dsc-pull-client-supports-tls-11-and-tls-12"></a>Опрашивающий клиент DSC поддерживает TLS 1.1 и TLS 1.2 
Ранее опрашивающий клиент DSC поддерживал только SSL3.0 и TLS1.0 через подключения по протоколу HTTPS. При принудительном использовании более безопасных протоколов опрашивающий клиент прекращал работу. В WMF 5.1 опрашивающий клиент DSC больше не поддерживает SSL 3.0, но поддерживает более безопасные протоколы TLS 1.1 и TLS 1.2.  

## <a name="improved-pull-server-registration"></a>Улучшенная регистрация опрашивающего сервера ##

В более ранних версиях WMF одновременные запросы регистрации или отчетов к опрашивающему серверу DSC при использовании базы данных ESENT привели бы к ошибке в LCM при регистрации или получении отчета. В журналах событий на опрашивающем сервере в таких случаях появляется ошибка "Имя экземпляра уже используется".
Эта ошибка вызывалась тем, что для доступа к базе данных ESENT в сценарии с несколькими потоками использовался неправильный шаблон. В WMF 5.1 эта проблема устранена. Одновременные запросы регистрации или отчетов (с использованием базы данных ESENT) выполняются корректно в WMF 5.1. Эта проблема относится только к базе данных ESENT и не касается базы данных OLEDB. 

## <a name="enable-circular-log-on-esent-database-instance"></a>Включить циклический журнал в экземпляре базы данных ESENT
В более ранней версии DSC-PullServer файлы журнала базы данных ESENT заполняли дисковое пространство опрашивающего сервера, так как экземпляр базы данных был создан без циклического ведения журнала. В этом выпуске можно управлять поведением циклического ведения журнала экземпляра с помощью web.config опрашивающего сервера. По умолчанию значение CircularLogging равно TRUE.
```
<appSettings>
    <add key="dbprovider" value="ESENT" />
    <add key="dbconnectionstr" value="C:\Program Files\WindowsPowerShell\DscService\Devices.edb" />
    <add key="CheckpointDepthMaxKB" value="512" />
    <add key="UseCircularESENTLogs" value="TRUE" />
  </appSettings>
```
## <a name="pull-partial-configuration-naming-convention"></a>Соглашение об именовании для частичной опрашивающей конфигурации
В предыдущем выпуске соглашение об именовании для частичной конфигурации было таким, что имя MOF-файла в опрашивающем сервере или службе должно было соответствовать имени частичной конфигурации, указанному в параметрах локального диспетчера конфигурации, которое, в свою очередь, должно соответствовать имени конфигурации, включенному в MOF-файл. 

См. моментальные снимки ниже.

• Параметры локальной конфигурации, определяющие частичную конфигурацию, которую разрешено получать узлу.

![Пример метаконфигурации](../images/MetaConfigPartialOne.png)

• Пример определения частичной конфигурации 

```PowerShell
Configuration PartialOne
{
    Node('localhost')
    {
        File test 
        {
            DestinationPath = "$env:TEMP\partialconfigexample.txt"
            Contents = 'Partial Config Example'
        }
    }
}
PartialOne
```

• Имя конфигурации (ConfigurationName), внедренное в созданный MOF-файл.

![Пример созданного MOF-файла](../images/PartialGeneratedMof.png)

• Имя файла в репозитории конфигураций 

![Имя файла в репозитории конфигураций](../images/PartialInConfigRepository.png)

Служба автоматизации Azure создавала MOF-файлы с именами `<ConfigurationName>.<NodeName>.mof`. Поэтому следующая конфигурация компилируется в файл PartialOne.Localhost.mof.

Это делало невозможным запрос к одной из частичных конфигураций из службы автоматизации Azure.

```PowerShell
Configuration PartialOne
{
    Node('localhost')
    {
        File test 
        {
            DestinationPath = "$env:TEMP\partialconfigexample.txt"
            Contents = 'Partial Config Example'
        }
    }
}
PartialOne
```

В WMF 5.1 частичная конфигурация на опрашивающем сервере или в службе может иметь имя `<ConfigurationName>.<NodeName>.mof`. Кроме того, если компьютер запрашивает одну конфигурацию с опрашивающего сервера или службы, то файл конфигурации в репозитории конфигураций опрашивающего сервера может иметь любое имя. Эта гибкость в именовании позволяет управлять узлами, которые находятся под частичным управлением службы автоматизации Azure. В этом случае часть конфигурации узла поступает из DSC службы автоматизации Azure, а другой частью конфигурации вы управляете локально.

В следующей метаконфигурации узел находится как под локальным управлением, так и под управлением службы автоматизации Azure.

```PowerShell
  [DscLocalConfigurationManager()]
   Configuration RegistrationMetaConfig
   {
        Settings
        {
            RefreshFrequencyMins = 30
            RefreshMode = "PULL"            
        }

        ConfigurationRepositoryWeb web
        {
            ServerURL =  $endPoint
            RegistrationKey = $registrationKey
            ConfigurationNames = $configurationName
        }

        # Partial configuration managed by Azure Automation service.
        PartialConfiguration PartialConfigurationManagedByAzureAutomation
        {
            ConfigurationSource = "[ConfigurationRepositoryWeb]Web"   
        }
    
        # This partial configuration is managed locally.
        PartialConfiguration OnPremisesConfig
        {
            RefreshMode = "PUSH"
            ExclusiveResources = @("Script")
        }

   }

   RegistrationMetaConfig
   Set-DscLocalConfigurationManager -Path .\RegistrationMetaConfig -Verbose
 ```

# <a name="using-psdscrunascredential-with-dsc-composite-resources"></a>Использование параметра PsDscRunAsCredential с составными ресурсами DSC   

Добавлена поддержка использования параметра [*PsDscRunAsCredential*](https://msdn.microsoft.com/cs-cz/powershell/dsc/runasuser) с [составными](https://msdn.microsoft.com/en-us/powershell/dsc/authoringresourcecomposite) ресурсами DSC.    

Теперь можно указать значение параметра PsDscRunAsCredential при использовании составных ресурсов в конфигурации. Если значение задано, все ресурсы, включенные в составной ресурс, будут запущены от имени этого пользователя. Если составной ресурс вызывает другой составной ресурс, то все входящие в него ресурсы также будут запущены от имени этого пользователя. Учетные данные запуска от имени распространяются на любой уровень иерархии составных ресурсов. Если для одного из ресурсов, входящих в составной ресурс, указано собственное значение параметра PsDscRunAsCredential, при компиляции конфигурации появляется ошибка слияния.

В этом примере показано, как использовать этот параметр с составным ресурсом [WindowsFeatureSet](https://msdn.microsoft.com/en-us/powershell/wmf/dsc_newresources), включенным в модуль PSDesiredStateConfiguration. 



```powershell

Configuration InstallWindowsFeature     
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node $AllNodes.NodeName
    {
        WindowsFeatureSet features 
        {  
            Name = @("Telnet-Client","SNMP-Service")  
            Ensure = "Present"  
            IncludeAllSubFeature = $true  
            PsDscRunAsCredential = Get-Credential   
        }  
    }

}

$configData = @{
    AllNodes = @(
        @{
            NodeName             = 'localhost'
            PSDscAllowDomainUser = $true
            CertificateFile      = 'C:\publicKeys\targetNode.cer'
            Thumbprint           = '7ee7f09d-4be0-41aa-a47f-96b9e3bdec25'
        }
    )
}


InstallWindowsFeature -ConfigurationData $configData 

```

##<a name="dsc-module-and-configuration-signing-validations"></a>Модуль DSC и проверка подписи конфигурации
В DSC конфигурации и модули распространяются на управляемые компьютеры с опрашивающего сервера. При компрометации опрашивающего сервера злоумышленник может изменить конфигурации и модули на опрашивающем сервере и распространить их на все управляемые узлы, что также приведет к их компрометации. 

 В WMF 5.1 DSC поддерживает проверку цифровых подписей файлов каталогов и конфигурации (MOF-файлов). Эта функция предотвращает выполнение на узлах конфигураций или файлов модулей, которые не подписаны доверенным лицом или изменены после подписания доверенным лицом. 



###<a name="how-to-sign-configuration-and-module"></a>Подписывание конфигурации и модулей 
***
* Файлы конфигурации (MOF-файлы): поддержка подписывания MOF-файлов была добавлена к функциям существующего командлета PowerShell, [Set-AuthenticodeSignature](https://technet.microsoft.com/library/hh849819.aspx).  
* Модули: для подписывания модулей необходимо подписать каталог соответствующего модуля, выполнив следующие действия. 
    1. Создайте файл каталога: файл каталога содержит коллекцию криптографических хэшей или отпечатков. 
       Каждый отпечаток соответствует файлу, включенному в модуль. 
       Был добавлен новый командлет [New-FileCatalog](https://technet.microsoft.com/library/cc732148.aspx), благодаря которому пользователи могут создавать файлы каталогов для своих модулей.
    2. Подпишите файл каталога: подпишите файл каталога с помощью командлета [Set-AuthenticodeSignature](https://technet.microsoft.com/library/hh849819.aspx).
    3. Поместите файл каталога в папку модуля.
По соглашению файл каталога модуля должен находиться в папке модуля и имя файла каталога модуля должно совпадать с именем модуля.

###<a name="localconfigurationmanager-settings-to-enable-signing-validations"></a>Параметры локального диспетчера конфигураций для включения проверки подписи

####<a name="pull"></a>По запросу
Локальный диспетчер конфигураций узла выполняет проверку подписи модулей и конфигураций в соответствии со своими текущими параметрами. По умолчанию проверка подписи отключена. Проверку подписи можно включить, добавив блок "SignatureValidation" в определение метаконфигурации узла, как показано ниже.

```PowerShell
[DSCLocalConfigurationManager()]
Configuration EnableSignatureValidation
{
    Settings
    {
        RefreshMode = 'PULL'        
    } 
    
    ConfigurationRepositoryWeb pullserver{
      ConfigurationNames = 'sql'
      ServerURL = 'http://localhost:8080/PSDSCPullServer/PSDSCPullServer.svc'
      AllowUnsecureConnection = $true
      RegistrationKey = 'd6750ff1-d8dd-49f7-8caf-7471ea9793fc' # Replace this with correct registration key.
    }
    SignatureValidation validations{
        # By default, LCM uses the default Windows trusted publisher store to validate the certificate chain. If TrustedStorePath property is specified, LCM uses this custom store for retrieving the trusted publishers to validate the content.
        TrustedStorePath = 'Cert:\LocalMachine\DSCStore'            
        SignedItemType = 'Configuration','Module'         # This is a list of DSC artifacts, for which LCM need to verify their digital signature before executing them on the node.       
    }
 
}
EnableSignatureValidation
Set-DscLocalConfigurationManager -Path .\EnableSignatureValidation -Verbose 
 ```

Установка приведенной выше метаконфигурации для узла включает проверку подписи для загруженных конфигураций и модулей. Для проверки цифровых подписей локальный диспетчер конфигураций выполняет следующие действия.

1. Проверьте подпись в файле конфигурации (MOF-файл). 
   Для проверки подписи диспетчер использует командлет PowerShell [Get-AuthenticodeSignature](https://technet.microsoft.com/library/hh849805.aspx), в котором в версии 5.1 была добавлена поддержка проверки подписи MOF.
2. Убедитесь, что центр сертификации, который авторизовал подписывающую сторону, является доверенным.
3. Скачайте зависимости модуля или ресурса конфигурации во временный каталог.
4. Проверьте подпись каталога, включенного в модуль.
    * Найдите файл `<moduleName>.cat` и проверьте его подпись командлетом [Get-AuthenticodeSignature](https://technet.microsoft.com/library/hh849805.aspx).
    * Проверьте центр сертификации, который проверил подлинность доверенного лица.
    * Проверьте, чтобы содержимое модулей не было изменено, с помощью нового командлета, [Test-FileCatalog](https://technet.microsoft.com/library/cc732148.aspx).
5. Установите модуль (Install-Module) в папку "$env:ProgramFiles\WindowsPowerShell\Modules\".
6. Выполните конфигурацию.

> Примечание. Проверка подписи каталога модуля и конфигурации выполняется только при первом применении конфигурации к системе или при скачивании и установке модуля. При проверке на согласованность не проверяется подпись файла Current.mof и зависимости для его модулей.
Если проверка на любом этапе завершается неудачно, например если конфигурация, полученная с опрашивающего сервера, не подписана, обработка конфигурации завершается ошибкой, показанной ниже, и все временные файлы удаляются.

![Пример ошибки конфигурации](../images/PullUnsignedConfigFail.png)

Точно так же при попытке получить модуль, каталог для которого не подписан, появляется следующая ошибка:

![Пример ошибки модуля](../images/PullUnisgnedCatalog.png)

####<a name="push"></a>Принудительная отправка
Конфигурация, переданная на узел с помощью принудительной отправки, может быть изменена злоумышленником в месте ее отправки. Локальный диспетчер конфигураций выполняет похожие действия для проверки подписи принудительно отправленных и опубликованных конфигураций.
Ниже приведен полный пример проверки подписи для принудительной отправки.

* Включите проверку подписи на узле.

```PowerShell
[DSCLocalConfigurationManager()]
Configuration EnableSignatureValidation
{
    Settings
    {
        RefreshMode = 'PUSH'        
    } 
    SignatureValidation validations{
        TrustedStorePath = 'Cert:\LocalMachine\DSCStore'   
        SignedItemType =  'Configuration','Module'             
    }

}
EnableSignatureValidation
Set-DscLocalConfigurationManager -Path .\EnableSignatureValidation -Verbose
``` 
* Создайте пример файла конфигурации.

```PowerShell
# Sample configuration
Configuration Test
{

    File foo
    {
        DestinationPath =  "$env:TEMP\signingTest.txt"
        Contents = "ABC"
    }
}
Test
```

* Попробуйте принудительно отправить не подписанный файл конфигурации на узел. 

```PowerShell
Start-DscConfiguration -Path .\Test -Wait -Verbose -Force
``` 
![Ошибка "MOF-файл не подписан"](../images/PushUnsignedMof.png)

* Подпишите файл конфигурации с помощью сертификата подписи кода.

![Подписанный MOF-файл](../images/SignMofFile.png)

* Попробуйте отправить подписанный MOF-файл.

![Подписанный MOF-файл](../images/PushSignedMof.png)

