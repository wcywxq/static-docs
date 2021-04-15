---
title: Attr 函数
date: 2020-05-17
categories: [Css]
---

> CSS 表达式 `attr()` 用来获取选择到的元素的某一 HTML 属性值，并用于其样式。它也可以用于伪元素，属性值采用伪元素所依附的元素。
> `attr()`  表达式可以用于任何 `css`  属性。

> `attr(attribute-name <type-or-unit>? [, <fallback> ]? )`

- **attribute-name**

\*\*
CSS 所引用的 HTML 属性名称。

- **<type-or-unit>**

\*\*
表示所引用的属性值的单位。如果该单位相对于所引用的属性值不合法，那么`attr()`表达式也不合法。若省略，则默认值为`string`。其合法值包括：

| 关键字                   | 类型                                                                      | 备注                                                                                                                                      | 默认值   |
| :----------------------- | :------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------- | :------- |
| `string`                 | [`<string>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/string)     | 属性值将被解析为 [`<string>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/string)                                                    | 空字符串 |
| `color`                  | [`<color>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/color_value) | 属性值将被解析为#xxx、#xxxxxx 或关键字的形式，且必须为合法 CSS [`<string>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/string) 值。 |
| 前缀与后缀空格将被截掉。 | 当前颜色                                                                  |
| `url`                    | [`<uri>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/uri)           | 属性值将被解析为可用于`url()`函数的字符串。                                                                                               |

相对 URL 是根据 HTML 文档的路径解析，而不是样式文件所在的路径。
前缀与后缀空格将被截掉。 | URL `about:invalid`，表示资源不存在。 |
| `integer` | [`<integer>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/integer) | 属性值将被解析为 CSS [`<integer>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/integer)。若不是合法值（不是整数或超出 CSS 属性规定的数字范围），则使用默认值。
前缀与后缀空格将被截掉。 | `0`，或该属性允许的最小值（如果 0 是不合法的值）。 |
| `number` | [`<number>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/number) | 属性值将被解析为 CSS [`<number>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/number)。 若不是合法值（不是数字或超出 CSS 属性规定的数字范围），则使用默认值。
前缀与后缀空格将被截掉。 | `0`，或该属性允许的最小值（如果 0 是不合法的值）。 |
| `length` | [`<length>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length) | 属性值将被解析为 CSS [`<length>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length)，带单位，比如 `12.5em`。 若不是合法值（不是长度值或超出 CSS 属性规定的范围），则使用默认值。
若属性值是一个相对长度， `attr()`会将其计算为绝对长度。
前缀与后缀空格将被截掉。 | `0`，或该属性允许的最小值（如果 0 是不合法的值）。 |
| `em`, `ex`, `px`, `rem`, `vw`, `vh`, `vmin`, `vmax`, `mm`, `cm`, `in`, `pt`, or `pc` | [`<length>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length) | 属性值将被解析为 CSS [`<number>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/number)，不带单位，如 `12.5`，并被解释为带有所规定单位的 [`<length>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/length) 值。若不是合法值（不是长度值或超出 CSS 属性规定的范围），则使用默认值。
若属性值是一个相对长度， `attr()`会将其计算为绝对长度。
前缀与后缀空格将被截掉。 | `0`，或该属性允许的最小值（如果 0 是不合法的值）。 |
| `angle` | [`<angle>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/angle) | 属性值将被解析为 CSS [`<angle>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/angle)，带单位，如`30.5deg`。若不是合法值（不是角度值或超出 CSS 属性规定的范围），则使用默认值。
前缀与后缀空格将被截掉。 | `0deg`，或该属性允许的最小值（如果 0deg 是不合法的值）。 |
| `deg`, `grad`, `rad` | [`<angle>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/angle) | 属性值将被解析为 CSS [`<number>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/number)，不带单位，如`12.5`)，并被解释为带有所规定单位的 [`<angle>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/angle) 值。若不是合法值（不是角度值或超出 CSS 属性规定的范围），则使用默认值。
前缀与后缀空格将被截掉。 | `0deg`，或该属性允许的最小值（如果 0deg 是不合法的值）。 |
| `time` | [`<time>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/time) | 属性值将被解析为 CSS [`<time>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/time)，带单位，如`30.5ms`。若不是合法值（不是时间值或超出 CSS 属性规定的范围），则使用默认值。
前缀与后缀空格将被截掉。 | `0s`，或该属性允许的最小值（如果 0s 是不合法的值）。 |
| `s`, `ms` | [`<time>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/time) | 属性值将被解析为 CSS [`<time>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/time)，不带单位，如`30.5`，并被解释为带有所规定单位的 [`<time>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/time) 值。。若不是合法值（不是时间值或超出 CSS 属性规定的范围），则使用默认值。
前缀与后缀空格将被截掉。 | `0s`，或该属性允许的最小值（如果 0s 是不合法的值）。 |
| `frequency` | [`<frequency>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/frequency) | 属性值将被解析为 CSS [`<frequency>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/frequency)，带单位，如`12.5kHz`)。若不是合法值（不是频率值或超出 CSS 属性规定的范围），则使用默认值。
前缀与后缀空格将被截掉。 | `0Hz`，或该属性允许的最小值（如果 0Hz 是不合法的值）。 |
| `Hz`, `kHz` | [`<frequency>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/frequency) | 属性值将被解析为 CSS [`<frequency>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/frequency)，不带单位，如`12.5`)，并被解释为带有所规定单位的[`<frequency>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/frequency)值。若不是合法值（不是频率值或超出 CSS 属性规定的范围），则使用默认值。
前缀与后缀空格将被截掉。 | `0Hz`，或该属性允许的最小值（如果 0Hz 是不合法的值）。 |
| `%` | [`<percentage>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/percentage) | 属性值将被解析为 CSS [`<number>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/number)，不带单位，如`12.5`)，并被解释为带有所规定单位的 [`<percentage>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/percentage)值。若不是合法值（不是数字或超出 CSS 属性规定的范围），则使用默认值。
若属性值用作长度，`attr()`将其计算为绝对长度。
前缀与后缀空格将被截掉。 | `0%`，或该属性允许的最小值（如果 0%是不合法的值）。 |

- **<fallback>**

\*\*
如果 `HTML`  元素缺少所规定的属性值或属性值不合法，则使用`fallback`值。该值必须合法，且不能包含另一个`attr()`表达式。如果`attr()`不是所在 `CSS`  属性值的唯一值，其`<fallback>`值必须为`<type-or-unit>`所指定的类型，否则 `CSS`  会使用相应`<type-or-unit>`定义的默认值（见上表）。

```css
ul li::before {
  position: absolute;
  color: #fff;
  left: calc(100% - 50px);
  font-size: 12px;
  content: attr(data-tip);
  line-height: 40px;
  transform: scale(0);
  transition: all 0.8s;
}
```

```html
<li data-tip="about">
  <a href="">关于我们</a>
</li>
<li data-tip="center">
  <a href="">项目中心</a>
</li>
<li data-tip="media">
  <a href="">媒体报道</a>
</li>
```
