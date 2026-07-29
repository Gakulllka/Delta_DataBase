# Расхождения в реализации Delta Design DNA

> Этот документ описывает все найденные расхождения между каноничным Design DNA и его реальными реализациями в Delta-BA и Delta-tasker. **Проекты не меняются** — документ служит для анализа и принятия решений.

---

## Сводная таблица

| Параметр | Delta Design DNA | Delta-BA | Delta-tasker | Статус |
|----------|-----------------|----------|--------------|--------|
| Цветовое пространство | hex (`#FAFAF8`) | hex (точное) | oklch (отклонения) | ⚠️ |
| Именование токенов | `--background`, `--card`... | `--color-bg`, `--color-surface`... | shadcn-совместимые | ⚠️ |
| Прогресс (синий) | `#3B82F6` | `#17181C` (монохром) | `#3B82F6` (в коде) | ❌ BA |
| Тёмная тема: фон | `#131418` | `#131418` (точно) | `oklch(0.145)` ≈ `#202020` | ❌ tasker |
| Тёмная тема: карточка | `#1A1B20` | `#1A1B20` (точно) | `oklch(0.205)` ≈ `#353535` | ❌ tasker |
| Destructive (свет) | `#C6453F` | `#C6453F` (точно) | `oklch(0.577)` ≈ `#D44040` | ❌ tasker |
| Focus ring | `2px solid` акцент | `2px solid` акцент | `2px solid` акцент 60% | ❌ tasker |
| Selection | 22% opacity | 20% | 24% | ⚠️ оба |
| Active feedback (кнопки) | 0.97 / 0.98 | 0.98 (все) | 0.97 / 0.98 | ❌ BA |
| Скроллбар ширина | 5–8px | 8px | 5px + 8px (конфликт) | ❌ tasker |
| Brand Splash | Описан в DNA | Отсутствует | Реализован | ❌ BA |
| Ink Rail | Описан в DNA | Отсутствует | Реализован | ❌ BA |
| Ink Pop / Ink Ctx | Описаны в DNA | Отсутствуют | Реализованы | ❌ BA |
| Ink Scope | Описан в DNA | Отсутствует | Реализован | ❌ BA |
| Названия анимаций | `fade-in`, `fade-in-up` | `delta-fade-in`, `delta-fade-in-up` | `fade-in`, `fade-in-up` | ❌ BA |
| Stagger (кол-во детей) | 7 (0.02→0.20s) | 7 (0.02→0.20s) | 8 (0.02→0.26s) | ⚠️ tasker |
| Компонентная библиотека | Не определена | Tailwind с нуля | shadcn/ui + Radix | ℹ️ разные |

**Легенда:** ✅ совпадает | ⚠️ незначительное отклонение | ❌ существенное отклонение | ℹ️ информационно

---

## Детальный разбор по категориям

### 1. Цвета и токены

#### 1.1. Цветовое пространство

**DNA:** все значения заданы в hex (`#FAFAF8`, `#17181C`, `#131418`...)

**Delta-BA:** использует hex напрямую через `@theme { --color-bg: #FAFAF8 }` — **точное совпадение с DNA**.

**Delta-tasker:** использует `oklch()` для CSS-переменных (шаблон shadcn/ui по умолчанию):
```css
/* Delta-tasker :root */
--background: oklch(0.985 0.008 270);  /* ≈ #FAFAF8 ✓ */
--foreground: oklch(0.145 0 0);         /* ≈ #202020 ✗ (DNA: #17181C) */
--card: oklch(0.995 0.004 270);         /* ≈ #FEFEFE ✓ */
--border: oklch(0.91 0.012 270);        /* ≈ #E2E1DC ✓ */

/* Delta-tasker .dark */
--background: oklch(0.145 0 0);         /* ≈ #202020 ✗ (DNA: #131418) */
--card: oklch(0.205 0 0);               /* ≈ #353535 ✗ (DNA: #1A1B20) */
--primary: oklch(0.922 0 0);            /* ≈ #EAEAEA ✗ (DNA: #F5F5F2) */
```

**Проблема:** oklch-значения не точно соответствует hex из DNA. Особенно критично в тёмной теме — фон и карточка значительно светлее эталона.

**Рекомендация:** либо зафиксировать hex в DNA как единственно верные, либо привести oklch-значения в tasker к точным.hex-эквивалентам.

#### 1.2. Именование токенов

**DNA:** `--background`, `--foreground`, `--card`, `--primary`, `--muted-foreground`, `--border`, `--destructive`

**Delta-tasker:** совпадает с DNA (шаблон shadcn/ui).

**Delta-BA:** использует уникальные имена:
```
--color-bg          вместо  --background
--color-surface     вместо  --card
--color-text        вместо  --foreground
--color-text-secondary  вместо  --muted-foreground
--color-primary     вместо  --primary
--color-border      вместо  --border
--color-success     (нет в DNA)
--color-warning     (нет в DNA)
--color-error       вместо  --destructive
```

**Проблема:** при переходе между проектами возникает путаница. Компоненты нельзя переиспользовать напрямую.

**Рекомендация:** DNA должна описать оба варианта именования как допустимые, или зафиксировать единый стандарт.

#### 1.3. Семантический прогресс

**DNA:** `--sem-progress: #3B82F6` (сдержанный синий) в обеих темах.

**Delta-BA:** `--sem-progress: #17181C` в светлой теме (монохром!) — **прямое отступление**. В тёмной: `--sem-progress: #F5F5F2`.

**Delta-tasker:** не объявляет `--sem-progress` в CSS, но синий `#3B82F6` используется в компонентах (статусы, прогресс-бары).

**Проблема:** Delta-BA делает прогресс монохромным, хотя DNA требует синий как единственное цветовое исключение.

**Рекомендация:** уточнить в DNA: монохромный прогресс допустим или синий обязателен?

#### 1.4. Destructive (опасность)

**DNA:** `#C6453F` (светлая), `#E0706A` (тёмная)

**Delta-BA:** `#C6453F` / `#E0706A` — **точное совпадение**.

**Delta-tasker:** `oklch(0.577 0.245 27.325)` ≈ `#D44040` в светлой теме — **отличается**.

---

### 2. Типографика

Расхождений в типографике практически нет. Оба проекта используют:
- **Geist** (основной шрифт)
- **Geist Mono** (моноширинный)
- `tabular-nums` для чисел
- `eyebrow`-паттерн (моно, 10px, caps, разрядка)

**Незначительные отличия:**
- Delta-BA: `letter-spacing: 0.14em` для eyebrow на бумаге
- Delta-tasker: `letter-spacing: 0.16em` для rail-eyebrow, `0.14em` для paper-eyebrow
- DNA: рекомендует `0.14em` на бумаге и `0.16em` на графите — **оба проекта следуют DNA**

---

### 3. Форма и глубина

#### 3.1. Радиусы

**DNA:** 8 / 10 / 12 / 14 / 16 / pill

**Delta-BA:** `--radius-card: 14px`, `--radius-dialog: 16px` — **совпадает**.

**Delta-tasker:** `--radius: 0.75rem` (12px), `--radius-card: 14px` — **совпадает**.

Расхождений нет.

#### 3.2. Тени

**DNA:** три уровня (card, raise, pop) с тёплым чёрным.

**Delta-BA:** точные значения из DNA — **совпадает**.

**Delta-tasker:** те же значения, но в `.dark` переопределены — **совпадает**.

Расхождений нет.

---

### 4. Движение

#### 4.1. Easing

**DNA:** `cubic-bezier(0.16, 1, 0.3, 1)`

**Delta-BA:** `--ease-delta: cubic-bezier(0.16, 1, 0.3, 1)` — **совпадает**.

**Delta-tasker:** `cubic-bezier(0.16, 1, 0.3, 1)` в CSS — **совпадает**.

Расхождений нет.

#### 4.2. Названия анимаций

**DNA:** `fade-in`, `fade-in-up`, `scale-in`, `view-enter`, `rail-enter`

**Delta-BA:** `delta-fade-in`, `delta-fade-in-up` — **префикс `delta-`**.

**Delta-tasker:** `fade-in`, `fade-in-up` — **без префикса, совпадает с DNA**.

**Проблема:** названия не совпадают, невозможно переиспользовать CSS между проектами.

#### 4.3. Active feedback (кнопки)

**DNA:** `scale(0.97)` для обычных кнопок, `scale(0.98)` для акцентных.

**Delta-tasker:** `scale(0.97)` глобально, `scale(0.98)` для `[data-slot="button"]` — **совпадает**.

**Delta-BA:** `scale(0.98)` для **всех** кнопок — **отступление**.

#### 4.4. Focus ring

**DNA:** `outline: 2px solid var(--primary)` + `outline-offset: 2px`

**Delta-BA:** `outline: 2px solid var(--color-primary)` — **совпадает**.

**Delta-tasker:** `outline: 2px solid color-mix(in srgb, var(--tracker-accent) 60%, transparent)` — **полупрозрачный, не DNA**.

#### 4.5. Selection highlight

**DNA:** `color-mix(in srgb, var(--primary) 22%, transparent)`

**Delta-BA:** `color-mix(in srgb, var(--color-primary) 20%, transparent)` — **−2%**.

**Delta-tasker:** `color-mix(in srgb, var(--tracker-accent, #9B72CF) 24%, transparent)` — **+2%, плюс используется fallback-цвет вместо токена**.

#### 4.6. Stagger timing

**DNA:** до 7-го ребёнка (0.02s → 0.20s, шаг 0.03s)

**Delta-BA:** до 7-го (0.02s → 0.20s) — **совпадает**.

**Delta-tasker:** до 8-го (0.02s → 0.26s, шаг 0.03s) — **расширен**.

---

### 5. Компоненты и архитектура

#### 5.1. Компонентные библиотеки

**DNA:** не определяет.

**Delta-BA:** все компоненты написаны с нуля на Tailwind CSS. Нет внешней UI-библиотеки.

**Delta-tasker:** shadcn/ui (New York стиль) + Radix UI. 20 готовых компонентов в `src/components/ui/`.

**Проблема:** разные подходы к вёрстке. Компоненты не переиспользуются между проектами.

**Рекомендация:** DNA должна описать shadcn/ui как допустимый вариант с указанием: токены DNA переопределяют shadcn-дефолты.

#### 5.2. Brand Splash

**DNA:** описывает анимированный Brand Splash (прорисовка △, «дыхание» стека дельт).

**Delta-tasker:** полностью реализован в `brand-splash.tsx` + CSS-анимации в `globals.css`.

**Delta-BA:** **отсутствует**. Нет ни компонента, ни CSS.

#### 5.3. Ink-система (рельса и меню)

**DNA:** описывает Ink Rail (графитовая рельса), Ink Pop (меню с рельсы), Ink Ctx (контекстное меню), Ink Scope (тёмные блоки).

**Delta-tasker:** полностью реализовано в `globals.css` (классы `.ink-rail`, `.ink-pop`, `.ink-ctx`, `.ink-scope`).

**Delta-BA:** **отсутствует**. Есть базовый rail (`--rail-bg: #17181C`), но нет Ink-классов.

#### 5.4. Скроллбар

**DNA:** 5–8px, thumb ~28% opacity, прозрачный трек.

**Delta-BA:** 8px, thumb 28% — **совпадает**.

**Delta-tasker:** **два конфликтующих определения**:
1. Строка 131–147: `width: 8px`, thumb из `--tracker-border`
2. Строка 493–506: `width: 5px`, thumb из `--tracker-accent` + `--tracker-border`

Второе определение перекрывает первое. Итоговая ширина — 5px.

---

### 6. Shadcn-специфичные токены

Delta-tasker (shadcn) объявляет токены, которых нет вDNA:

| Токен | Значение (tasker) | Есть в DNA? |
|-------|-------------------|-------------|
| `--chart-1..5` | oklch-цвета для графиков | Нет |
| `--sidebar` | `oklch(0.985 0 0)` | Нет |
| `--sidebar-foreground` | `oklch(0.145 0 0)` | Нет |
| `--sidebar-primary` | `oklch(0.205 0 0)` | Нет |
| `--sidebar-accent` | `oklch(0.97 0 0)` | Нет |
| `--sidebar-border` | `oklch(0.922 0 0)` | Нет |
| `--sidebar-ring` | `oklch(0.708 0 0)` | Нет |
| `--secondary` | `oklch(0.97 0 0)` | Нет |
| `--secondary-foreground` | `oklch(0.205 0 0)` | Нет |
| `--accent` | `oklch(0.97 0 0)` | Нет |
| `--accent-foreground` | `oklch(0.205 0 0)` | Нет |
| `--ring` | `oklch(0.708 0 0)` | Нет |
| `--input` | `oklch(0.91 0.012 270)` | Нет |

**Проблема:** DNA не описывает эти токены, хотя они необходимы для shadcn/ui.

---

## Сводка рекомендаций

| # | Что | Куда | Приоритет |
|---|-----|------|-----------|
| 1 | Добавить shadcn-токены в DNA | DNA §4.5 | Высокий |
| 2 | Уточнить hex vs oklch в DNA | DNA §4 | Высокий |
| 3 | Описать Ink-систему как обязательную | DNA (новый раздел) | Высокий |
| 4 | Добавить Brand Splash вDNA | DNA §3 | Средний |
| 5 | Зафиксировать именование токенов | DNA §4 | Средний |
| 6 | Уточнить прогресс: монохром или синий | DNA §4.4 | Средний |
| 7 | Исправить oklch вtasker на hex | tasker `:root` + `.dark` | Средний |
| 8 | Исправить destructive вtasker | tasker `:root` | Низкий |
| 9 | Унифицировать active feedback | BA `index.css` | Низкий |
| 10 | Унифицировать focus ring | tasker `globals.css` | Низкий |
| 11 | Унифицировать selection | оба проекта | Низкий |
| 12 | Унифицировать названия анимаций | BA `index.css` | Низкий |
| 13 | Исправить конфликт скроллбаров | tasker `globals.css` | Низкий |
| 14 | Добавить Brand Splash вBA | BA (новый компонент) | Низкий |
| 15 | Добавить Ink-классы вBA | BA `index.css` | Низкий |

---

*Документ создан для анализа. ПроектыDelta-BA иDelta-tasker не изменены.*
