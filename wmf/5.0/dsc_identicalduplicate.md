---
ms.date: 2017-06-12
author: JKeithB
ms.topic: reference
keywords: "wmf,powershell,установка"
ms.openlocfilehash: d3a625d05eaf4e7448b4abf90499f6a94e2f7718
ms.sourcegitcommit: 75f70c7df01eea5e7a2c16f9a3ab1dd437a1f8fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2017
---
<a id="allowing-for-identical-duplicate-resources-in-a-configuration" class="xliff"></a>
# Поддержка идентичных повторяющихся ресурсов в конфигурации

DSC не допускает и не обрабатывает конфликтующие определения ресурсов в конфигурации. Вместо того чтобы попытаться разрешить конфликт, он просто завершается со сбоем. По мере того, как конфигурации все больше используются повторно с помощью составных ресурсов и т. п., конфликты будут возникать все чаще. Если конфликтующие определения ресурсов идентичны, DSC необходимо разрешить такую работу. В этом выпуске мы включаем поддержку нескольких экземпляров ресурсов с одинаковыми определениями:

```powershell
Configuration IIS_FrontEnd
{
    WindowsFeature FE_IIS         #Identical resource
    {
        Ensure = 'Present'
        Name = 'Web-Server'
    }

    WindowsFeature FTP
    {
        Ensure = 'Present'
        Name = 'Web-FTP-Server'
    }
}

Configuration IIS_Worker
{
    WindowsFeature Worker_IIS      #Identical resource
    {
        Ensure = 'Present'
        Name = 'Web-Server'
    }

    WindowsFeature ASP
    {
        Ensure = 'Present'
        Name = 'Web-ASP-Net45'
    }
}

Configuration WebApplication
{
    IIS_Frontend Web {}

    IIS_Worker ASP {}
}
```

Если бы в предыдущих выпусках имелся конфликт между экземплярами WindowsFeature FE_IIS и WindowsFeature Worker_IIS, пытающимися проверить установку роли "Веб-сервер", компиляция завершилась бы сбоем. Обратите внимание, что в этих двух конфигурациях *все* настраиваемые свойства идентичны. И именно благодаря идентичности *всех* свойств в этих двух ресурсах компиляция теперь выполняется успешно. 

Если же какие-либо свойства между двумя ресурсами не совпадают, они не считаются идентичными и компиляция завершается ошибкой:

```powershell
Configuration IIS_FrontEnd
{
    WindowsFeature FE_IIS
    {
        Ensure = 'Present'     # Ensure is Present here
        Name = 'Web-Server'
    }

    WindowsFeature FTP
    {
        Ensure = 'Present'
        Name = 'Web-FTP-Server'
    }
}

Configuration IIS_Worker
{
    WindowsFeature Worker_IIS
    {
        Ensure = 'Absent'      # Ensure property is Absent instead of Present
        Name = 'Web-Server'
    }

    WindowsFeature ASP
    {
        Ensure = 'Present'
        Name = 'Web-ASP-Net45'
    }
}

Configuration WebApplication
{
    IIS_Frontend Web {}

    IIS_Worker ASP {}
}
```

Эта конфигурация, которая очень похожа на предыдущую, завершается с ошибкой, поскольку ресурсы WindowsFeature FE_IIS и WindowsFeature Worker_IIS больше не совпадают, а значит конфликтуют.

