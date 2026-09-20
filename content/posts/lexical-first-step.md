---
title: "Основы редактора Lexical"
date: 2026-03-16T22:17:00+05:00
draft: false
type: "posts"
categories: ["technologies"]
images: ["posts/lexical-1.png"]
featured_image: "posts/lexical-1.png"
---
Lexical — это расширяемый JS-фреймворк для создания текстовых редакторов от Meta*. Ядро на Vanilla JS, работает с React, Vue, Angular или без них.

<!--more-->

## Упрощенная архитектура

1. **LexicalEditor:** главный инстанс, управляет логикой;
2. **EditorState:** снимок контента (дерево узлов);
3. **DOM Sync:** синхронизация состояния с содержимым `contenteditable`.

## Инициализация

### Установка
```bash
npm install lexical
```

### Создание экземпляра редактора
```javascript
import { createEditor } from 'lexical';
const editor = createEditor();
```

### Привязка к DOM
```javascript
const rootElement = document.querySelector('#editor');
editor.setRootElement(rootElement);
```

## Ключевые понятия

### Узлы

Контент — это дерево узлов. Основные расширяемые узлы:

#### ElementNode

Родительский узел для других узлов.

- **Уровень:** Блочный (`ParagraphNode`, `HeadingNode`) или строчный (`LinkNode`).
- **Поведение:** Настраивается через переопределение методов (`isInline`, `canBeEmpty`, `canInsertTextBefore`).

#### TextNode

Узел, содержащий текст.

- **format:** Стиль текста (bold, italic, underline, strikethrough, code, highlight, subscript, superscript).
- **mode:**
    - `token`: Неизменяемый узел, удаляется целиком.
    - `segmented`: Удаляется по сегментам (словам), редактируемый.
- **style:** Применение inline CSS стилей.

#### DecoratorNode

Узел-обертка для произвольного контента.

- **Назначение:** вставка компонентов (виджетов, медиа) внутри редактора.
- **Рендеринг:** не зависит от фреймворка (React, Vanilla JS, другие).

### Обновления

Изменение состояния происходит только внутри транзакции:
```javascript
editor.update(() => {
  // Создание и вставка узлов
});
```

### Выделение

Управление курсором и выделенным текстом через API редактора, а не напрямую через DOM.

Выделение — часть `EditorState`, всегда синхронизировано с деревом узлов.

#### RangeSelection

*   **Что:** Аналог нативного `Range` (курсор или выделенный текст).
*   **Использование:** Ввод текста, форматирование, копирование.
*   **Пример:** Выделение части слова или установка курсора между символами.

#### NodeSelection

*   **Что:** Выделение целых узлов (один или несколько).
*   **Использование:** Управление блоками: удаление, перемещение, копирование параграфов, изображений, видео.
*   **Пример:** Выделение абзаца целиком для удаления по клавише `Delete`.

#### TableSelection

*   **Что:** Специализированное выделение ячеек таблицы.
*   **Пакет:** Реализовано в `@lexical/table`.
*   **Использование:** Навигация и редактирование таблиц (выделение строк/столбцов).

####  null

*   **Что:** Отсутствие активного выделения.
*   **Сценарий:** Редактор не в фокусе или выделение сброшено программно.

### Команды

Внутренняя мощная система событий. Позволяет компонентам общаться без прямых связей (например, нажатие кнопки форматирует текст).

*   **Назначение:** Реакция на события (например, `KEY_ENTER_COMMAND`, `KEY_TAB_COMMAND`).
*   **Механика:** Регистрация слушателей с указанием приоритета.
*   **Обработка:** Возврат `true` помечает событие как «обработанное» и останавливает передачу другим слушателям.
*   **Применение:** Создание панелей инструментов, сложных плагинов (например, `TablePlugin`).

## Пример (Vanilla JS)

Базовая реализация без фреймворков.

```html
<div id="editor" contenteditable="true"></div>
<script type="module">
  import { createEditor, $createParagraphNode, $createTextNode, $getRoot } from 'lexical';

  const editor = createEditor();
  const rootElement = document.querySelector('#editor');
  editor.setRootElement(rootElement);

  // Программное добавление текста
  editor.update(() => {
    const root = $getRoot();
    const paragraph = $createParagraphNode();
    const text = $createTextNode('Привет, Lexical!');
    paragraph.append(text);
    root.append(paragraph);
  });

  // Чтение состояния
  editor.getEditorState().read(() => {
    const root = $getRoot();
    console.log(root.getTextContent());
  });
</script>
```

Lexical — это не просто «ещё один редактор», а конструктор, из которого можно собрать всё что угодно. Да, порог входа чуть выше, чем у готовых решений, но и контроль вы получаете полный.

В следующих статьях разберём практику:
* Как работать с API и состоянием редактора;
* Получение и управление выделением;
* Как написать свой узел для специфических задач.

## Ссылки

* [Официальная документация](https://lexical.dev/)
* [GitHub репозиторий](https://github.com/facebook/lexical)

*Компания Meta Platforms Inc. (владелец Facebook и Instagram) — организация признана экстремистской, ее деятельность запрещена на территории России*