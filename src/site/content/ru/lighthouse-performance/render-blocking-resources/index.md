---
layout: post-old
title: Устраните ресурсы, блокирующие рендеринг
description: Узнайте о проверке ресурсов, блокирующих рендеринг.
date: 2019-05-02
updated: 2020-08-11
web_lighthouse:
  - render-blocking-resources
---

В разделе Opportunities (Возможности) отчета Lighthouse перечислены все URL-адреса, блокирующие первую отрисовку страницы. Цель: уменьшить влияние URL-адресов, блокирующих рендеринг, путем встраивания критических ресурсов, отсрочки загрузки некритических ресурсов и удаления всего неиспользуемого.

<figure class="w-figure">{% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/8xAmOUQsM8sfEAgzLBis.png", alt="Скриншот проверки Lighthouse «Устраните блокирующие рендеринг ресурсы»", width="800", height="271", class="w-screenshot" %}</figure>

## Какие URL-адреса помечаются как ресурсы, блокирующие рендеринг?

[Lighthouse](https://developers.google.com/web/tools/lighthouse/) отмечает два типа URL-адресов, блокирующих рендеринг: скрипты и таблицы стилей.

Скрипт `<script>` помечается, если он:

- Находится в `<head>` документа.
- Не имеет атрибута `defer`.
- Не имеет атрибута `async`.

Таблица стилей `<link rel="stylesheet">` помечается, если она:

- Не имеет атрибута `disabled`. Если этот атрибут присутствует, браузер не загружает таблицу стилей.
- Не имеет атрибута `media`, который соответствует конкретному устройству пользователя. `media="all"` считается блокирующим рендеринг.

## Как определить критические ресурсы

Первый шаг к уменьшению воздействия ресурсов, блокирующих рендеринг, — это определить, что критично, а что нет. Используйте вкладку [Coverage](https://developer.chrome.com/docs/devtools/coverage/) в Chrome DevTools, чтобы определить некритический код CSS и JS. Когда вы загружаете или запускаете страницу, вкладка показывает, какое количество кода из всего загруженного реально использовалось.

<figure class="w-figure">{% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/Y2Yc8DMcBYMknz5y0Are.png", alt="Chrome DevTools: вкладка Coverage", width="800", height="407", class="w-screenshot w-screenshot--filled" %} <figcaption class="w-figcaption"> Chrome DevTools: вкладка Coverage. </figcaption></figure>

Вы можете уменьшить размер своих страниц, отправляя только тот код и стили, которые вам нужны. Щелкните URL-адрес, чтобы просмотреть этот файл на панели Sources (Источники). Стили в файлах CSS и код в файлах JavaScript отмечены двумя цветами:

- **зеленый (критический):** стили, необходимые для первой отрисовки; код, который важен для основных функций страницы;
- **красный (некритический):** стили, применяемые к контенту, который не виден сразу; код, неиспользуемый в основных функциях страницы.

## Как избавиться от скриптов, блокирующих рендеринг

Как только вы определили критический код, переместите этот код из URL-адреса, блокирующего рендеринг, во встроенный тег `script` на вашей HTML-странице. Теперь во время загрузки у страницы будет всё необходимое для обработки основных функций.

Если в URL-адресе, блокирующем рендеринг, есть код, который не является критическим, вы можете сохранить его в URL-адресе, а затем пометить URL-адрес атрибутами `async` или `defer` (см. также статью [«Добавление интерактивности с помощью JavaScript»](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/adding-interactivity-with-javascript)).

Код, который вообще не используется, следует удалить (см. статью [«Удалите неиспользуемый код»](/remove-unused-code)).

## Как избавиться от таблиц стилей, блокирующих рендеринг

Используйте прием, аналогичный переносу критического кода во встроенный тег `<script>`, встройте критические стили, необходимые для первой отрисовки, в блок `<style>` элемента `head` HTML-страницы. Затем асинхронно загрузите остальные стили, используя ссылку `preload` (см. статью [«Отложите загрузку неиспользуемого CSS»](/defer-non-critical-css)).

Подумайте об автоматизации процесса извлечения и встраивания CSS верхней половины полосы с помощью [инструмента Critical](https://github.com/addyosmani/critical/blob/master/README.md).

Другой подход к устранению стилей, блокирующих рендеринг, — это разделить эти стили на разные файлы, организованные по медиа-запросам. Затем добавьте атрибут media к каждой ссылке таблицы стилей. При загрузке страницы браузер блокирует только первую отрисовку, чтобы получить таблицы стилей, соответствующие устройству пользователя (см. статью [«Блокирующий рендеринг CSS»](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-blocking-css)).

Наконец, вы захотите выполнить минификацию CSS-кода, чтобы удалить лишние пробелы или символы (см. статью [«Выполните минификацию CSS-кода»](/minify-css)). Это гарантирует, что вы отправите пользователям минимально возможный бандл.

## Рекомендации по стекам

### AMP

Используйте такие инструменты, как [AMP-оптимизатор](https://github.com/ampproject/amp-toolbox/tree/master/packages/optimizer), для [рендеринга макетов AMP на стороне сервера](https://amp.dev/documentation/guides-and-tutorials/optimize-and-measure/server-side-rendering/).

### Drupal

Подумайте об использовании модуля для встраивания критического кода CSS и JavaScript или о потенциальной асинхронной загрузке ресурсов через JavaScript, например, с помощью модуля [Advanced CSS/JS Aggregation](https://www.drupal.org/project/advagg).

### Joomla

Существует ряд плагинов Joomla, которые могут помочь вам [встроить критические ресурсы](https://extensions.joomla.org/instant-search/?jed_live%5Bquery%5D=performance) или [отложить загрузку менее важных ресурсов](https://extensions.joomla.org/instant-search/?jed_live%5Bquery%5D=performance).

### WordPress

Существует ряд плагинов WordPress, которые могут помочь вам [встроить критические ресурсы](https://wordpress.org/plugins/search/critical+css/) или [отложить загрузку менее важных ресурсов](https://wordpress.org/plugins/search/defer+css+javascript/).

## Ресурсы

- [Исходный код проверки **Eliminate render-blocking resources** (Устраните ресурсы, блокирующие рендеринг)](https://github.com/GoogleChrome/lighthouse/blob/master/lighthouse-core/audits/byte-efficiency/render-blocking-resources.js).
- [Сократите полезную нагрузку JavaScript за счет разделения кода](/reduce-javascript-payloads-with-code-splitting).
- [Удалите неиспользуемый код (codelab)](/codelab-remove-unused-code).
- [Оптимизация загрузки JavaScript](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/javascript-startup-optimization/).
