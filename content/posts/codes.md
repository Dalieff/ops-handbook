---
title: "Formatting Cheatsheet"
date: 2026-02-26
draft: false
tags: ["poison", "hugo", "formatting"]
--------------------------------------

# Заголовок H1

## Заголовок H2

### Заголовок H3

Обычный текст.
**Жирный**
*Курсив*
~~Зачёркнутый~~

---

## Списки

* пункт
* пункт

  * вложенный
  * вложенный

1. первый
2. второй
3. третий

---

## Цитата

> Это цитата в теме Poison.

---

## Ссылки

[Ссылка на сайт](https://example.com)

---

## Код (inline)

Вот `inline code`.

---

## Блок кода

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Poison")
}
```

---

## Highlight shortcode

{{< highlight python >}}
print("Hello Poison")
{{< /highlight >}}

---

## Изображение Markdown

![Alt text](/images/example.png)

---

## Figure shortcode

{{< figure src="/images/example.png" title="Подпись изображения" >}}

---

## Tabs (вкладки)

{{< tabs tabTotal="2" >}}

{{% tab tabName="Tab One" %}}
Содержимое первой вкладки.
{{% /tab %}}

{{% tab tabName="Tab Two" %}}
Содержимое второй вкладки.
{{% /tab %}}

{{< /tabs >}}

---

## Details (раскрывающийся блок)

{{< details "Нажми чтобы раскрыть" >}}
Скрытый текст внутри блока details.
{{< /details >}}

---

## Notice / Callout

{{< notice info >}}
Информационный блок
{{< /notice >}}

{{< notice note >}}
Заметка
{{< /notice >}}

{{< notice warning >}}
Предупреждение
{{< /notice >}}

{{< notice error >}}
Ошибка
{{< /notice >}}

---

## Таблица

| Колонка A | Колонка B |
| --------- | --------- |
| 1         | 2         |
| 3         | 4         |

---

## Теги и серии (frontmatter пример)

```yaml
tags: ["linux", "devops"]
series: ["observability"]
```

---

## Список постов раздела

{{< list section="posts" limit="5" >}}

---

## Горизонтальная линия

---

Готово ✅
Эта страница показывает почти все форматирование, доступное в теме Poison.
