# CSS3 Flexbox and Grid Layout Guide

## Introduction

CSS provides two powerful layout systems:

- **Flexbox** → One-dimensional layout (row OR column)
- **Grid** → Two-dimensional layout (rows AND columns)

---

# Flexbox

## What is Flexbox?

Flexbox (Flexible Box Layout) is designed to arrange items in a single direction:

- Horizontal (row)
- Vertical (column)

Perfect for:
- Navigation bars
- Menus
- Cards
- Centering content
- Toolbars

---

## Flexbox Terminology

```text
Flex Container
│
├── Flex Item 1
├── Flex Item 2
├── Flex Item 3
└── Flex Item 4
```

---

## Creating a Flex Container

```css
.container {
    display: flex;
}
```

```html
<div class="container">
    <div>1</div>
    <div>2</div>
    <div>3</div>
</div>
```

---

## Flex Direction

### Row (Default)

```css
.container {
    display: flex;
    flex-direction: row;
}
```

```text
1  2  3
```

### Column

```css
.container {
    display: flex;
    flex-direction: column;
}
```

```text
1
2
3
```

---

## Justify Content

Controls alignment along the main axis.

```css
justify-content: center;
justify-content: space-between;
justify-content: space-around;
justify-content: space-evenly;
justify-content: flex-start;
justify-content: flex-end;
```

---

## Align Items

Controls alignment along the cross axis.

```css
align-items: center;
align-items: flex-start;
align-items: flex-end;
align-items: stretch;
```

---

## Centering a Div

```css
.container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}
```

---

## Flex Grow

```css
.item {
    flex-grow: 1;
}
```

Available space is distributed equally.

---

## Flex Example

```html
<div class="container">
    <div class="box">A</div>
    <div class="box">B</div>
    <div class="box">C</div>
</div>
```

```css
.container {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.box {
    width: 100px;
    height: 100px;
}
```

---

# CSS Grid

## What is CSS Grid?

Grid is a two-dimensional layout system.

Unlike Flexbox, Grid can control:

- Rows
- Columns

at the same time.

Perfect for:
- Dashboards
- Web page layouts
- Galleries
- Admin panels

---

## Grid Terminology

```text
+-----+-----+-----+
|  1  |  2  |  3  |
+-----+-----+-----+
|  4  |  5  |  6  |
+-----+-----+-----+
```

---

## Creating a Grid Container

```css
.container {
    display: grid;
}
```

---

## Define Columns

```css
.container {
    display: grid;
    grid-template-columns: 200px 200px 200px;
}
```

---

## Equal Columns

```css
.container {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
}
```

---

## Repeat Function

```css
.container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
}
```

---

## Define Rows

```css
.container {
    display: grid;
    grid-template-rows: 100px 100px;
}
```

---

## Gap Between Items

```css
.container {
    gap: 20px;
}
```

---

## Grid Example

```html
<div class="grid">
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <div>5</div>
    <div>6</div>
</div>
```

```css
.grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
}
```

---

# Flexbox vs Grid

| Feature | Flexbox | Grid |
|----------|----------|----------|
| Layout Type | One-Dimensional | Two-Dimensional |
| Direction | Row or Column | Rows and Columns |
| Best For | Components | Full Page Layouts |
| Alignment | Excellent | Excellent |
| Complexity | Easier | More Powerful |

---

# When to Use Flexbox

Use Flexbox when:

- Creating navigation bars
- Aligning buttons
- Centering content
- Card layouts
- Toolbars

---

# When to Use Grid

Use Grid when:

- Building dashboards
- Website layouts
- Admin panels
- Photo galleries
- Complex responsive pages

---

# Common Interview Question

Can Flexbox and Grid be used together?

**Yes.**

A common approach:

- Use Grid for overall page structure.
- Use Flexbox inside individual sections.

Example:

```text
Grid Layout
│
├── Header (Flexbox)
├── Sidebar
├── Main Content (Flexbox)
└── Footer
```

---

# Summary

Flexbox:
- One-dimensional
- Best for components
- Easier to learn

Grid:
- Two-dimensional
- Best for page layouts
- More powerful

Modern websites typically use both Flexbox and Grid together.
