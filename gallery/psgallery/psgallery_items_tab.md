---
ms.date: 2017-06-12
contributor: JKeithB
ms.topic: conceptual
keywords: "коллекции,powershell,командлет,psgallery"
title: "psgallery_вкладка_элементы"
ms.openlocfilehash: 8424c4729436a78fec3fdbb405591fcd3c6bc6a6
ms.sourcegitcommit: 75f70c7df01eea5e7a2c16f9a3ab1dd437a1f8fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2017
---
<a id="items-tab" class="xliff"></a>
Вкладка "Элементы"
==========

На вкладке "Элементы" отображаются все доступные элементы в коллекции PowerShell.

Чтобы просмотреть только модули в коллекции PowerShell, щелкните "Модули" в раскрывающемся списке на вкладке Items "Элементы".  Аналогично, чтобы просмотреть только скрипты в коллекции PowerShell, щелкните "Скрипты" в раскрывающемся списке на вкладке "Элементы".  

Чтобы узнать больше о каком-либо элементе, щелкните его.

Существует несколько способов сортировки элементов.

<a id="filter-by" class="xliff"></a>
##Раздел "Фильтровать по"
В разделе "Фильтровать по" можно отфильтровать результаты по следующим параметрам.
* Тип элемента
    * Модули
    * Сценарии
* Категория:
    * Командлет
    * Ресурс DSC
    * Функция
    * Рабочий процесс

Примечание. Фильтры находят все, в чем содержится их условие. И только это.  
Пример. Элемент, содержащий как командлеты, так и функции, будет отображаться как при выборе категорий "Командлет" или "Функция" по-отдельности, так и при выборе обеих категорий.  Если ни одна из этих категорий не выбрана, элемент не будет отображаться.  
Аналогично, если выбираются все категории, будут отображаться только элементы, содержащие хотя бы одну из этих категорий. **Элементы, которые не принадлежат ни к одной из этих категорий, отображаться не будут.**

<a id="sort-by" class="xliff"></a>
##Список "Сортировать по" 
Раскрывающийся список "Сортировать по" позволяет сортировать результаты по следующим параметрам.
* "Популярность" — определяется по числу скачиваний.
* A–Z — в алфавитном порядке по имени элемента.
* "Последние" — элементы располагаются в порядке их даты публикации.


<a id="search-box" class="xliff"></a>
##Поле поиска
Поле поиска позволяет искать элементы по ключевым словам.  
Подробности см. в статье [Синтаксис поиска](./psgallery_search_syntax.md).

