---
id: propose-new-feature
title: Предложение новой функции
description: "Шаблон PR и инструкции по предложению новой функции."
keywords:
  - docs
  - polygon
  - edge
  - feature
  - PR
  - template
  - fix
---

## Обзор {#overview}

Если вы хотите включить исправление или просто внести добавление в код, мы настоятельно вам рекомендуем сначала связаться с командой. <br/>Polygon Edge использует относительно простой шаблон для предложения функций, лаконичный и понятный.

## Шаблон PR {#pr-template}

### Описание {#description}

Просим вас представить подробное описание того, что было сделано в этом PR

### Изменения включают {#changes-include}

- [ ]Bugfix (изменение, которое не ломает структуру, но при этом решает проблему)
- [ ]Hotfix (изменение, которое решает срочную проблему и требует немедленного внимания)
- [ ]Новая функция (изменение, которое не ломает структуру, но при этом добавляет функциональность)
- [ ]Серьезное изменение (изменение, которое не является обратно-совместимым и/или изменяет текущую функциональность)

### Серьезные изменения {#breaking-changes}

Заполните этот раздел, если были сделаны серьезные изменения, в противном случае удалите его

### Контрольный список {#checklist}

- [ ]Я поручил этот PR себе
- [ ]Я добавил не менее 1 рецензента
- [ ]Я добавил соответствующие ярлыки
- [ ]Я обновил официальную документацию
- [ ]Я добавил достаточную документацию в код

### Тестирование {#testing}

- [ ]Я протестировал этот код с помощью официального пакета тестов
- [ ]Я проверил этот код вручную

## Тесты вручную {#manual-tests}

Заполните этот раздел, если вы выполнили тесты для этой функциональности вручную, в противном случае удалите его

### Обновление документации {#documentation-update}

Просим вас разместить ссылку на PR обновления документации в этом разделе, если такая ссылка существует, в противном случае удалите этот раздел.

### Дополнительные комментарии {#additional-comments}

Просим вас оставить дополнительные комментарии в этом разделе, если они у вас есть, в противном случае удалите этот раздел