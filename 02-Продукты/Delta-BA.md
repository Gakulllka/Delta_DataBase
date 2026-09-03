---
tags: [product, delta, react, vite, ai, business-analyst]
created: 2026-07-25
updated: 2026-08-23
status: production
repo: https://github.com/Gakulllka/Delta_BA
---

# Delta-BA — помощник аналитика

> SPA без бэкенда. Шесть ИИ-модулей превращают описание задачи в готовый артефакт: ТЗ, тест-чеклист, BPMN, протокол, расчётчик, HTML-прототип. Всё считается в браузере, данные в `localStorage`.

**Путь:** `C:\Delta\Delta-BA` · репозиторий `github.com/Gakulllka/Delta_BA`
**Сверено с кодом:** 2026-08-23 (после ревизии из 6 этапов).

---

## Ревизия 2026-08-23 — шесть этапов доработок

| Этап | Коммит | Главное |
|---|---|---|
| 1. Стабильность | `7966077` | фикс краша useSettings; подтверждение удалений; ошибки AI не в контент; `storage-safe` (квота); **полный бэкап JSON** (Настройки → Данные) |
| 2. UX-дыры | `633c5ce` | баннер «нет ключа» в 6 модулях; «Проверить соединение»; онбординг на главной; починен тест-режим (моки calculator/mockup/bpmn-draft); 404 |
| 3. Экспорт | `1021200` | Протокол DOCX/PDF; Чеклист XLSX; ТЗ PPTX (генератор был недостижим) |
| 4. Дизайн | `ac5b88e` | рамки 2px/3px (было 2.5px); контексты ТЗ монохром; блоки ТЗ ч/б + точка статуса; **Brand Splash**; scale(0.97), selection 22% |
| 5. Чистка | `4dfa643` | удалено ~2600 строк мёртвого кода (chat/*, Tool, History, 5 lib + 7 Python); **React.lazy: главный чанк 2.6МБ → 260КБ**; README/.env.example честные |
| 6. Продвинутое | `3ab8f87` | RelationsPanel → getRelations (кликабельно, полный граф); **UI версий + откат** в Documents; мобильная адаптация шапок/BlockEditView; даты по локале |

Все коммиты локальные (не запушены на момент сверки).

---

## Шесть модулей

| # | Модуль | Роут | Что делает | Экспорт |
|---|--------|------|-----------|---------|
| 1 | **Генератор ТЗ** | `/tz` | Контекст → шаблон → шапка → пошаговая генерация блоков. Ядро продукта | DOCX, PDF, PPTX |
| 2 | **Тест-чеклист** | `/test-checklist` | Интерактивный документ; проваленные пункты порождают доработочное ТЗ | MD (раундтрип), XLSX |
| 3 | **BPMN** | `/bpmn` | Черновик (Mermaid/PlantUML/текст) → BPMN 2.0 XML на канве `bpmn-js` | .bpmn, .mmd, .puml |
| 4 | **Протокол совещаний** | `/protocol` | Из заметок извлекает участников, вопросы, решения, ответственных, сроки | MD, DOCX, PDF |
| 5 | **Excel-расчётчик** | `/calculator` | Достаёт параметры и формулы, экспортирует XLSX с **живыми формулами** | XLSX |
| 6 | **HTML-прототип** | `/mockup` | Генерирует прототип, рендерит в `iframe sandbox="allow-scripts"` | .html |

Сервисные экраны: `/`, `/tz/template`, `/documents`, `/settings`, `*` (404).

**Контексты ТЗ:** 1С, BA ТЗ, ИИ ТЗ, Свободная тема — иконки монохромные (чернильные плашки).

**Связи артефактов.** Все живут в одном массиве `Artifact[]` (`lib/artifact-store.ts`), связываются полем `sourceTzId` + `links`/`provenance`. Цикл: Протокол → ТЗ → Тест-чеклист → Доработочное ТЗ. RelationsPanel (в ТЗ) и Documents показывают **полный граф** через `getRelations` — кликабельно. Версии: `pushVersion` при каждом сохранении, просмотр и **откат** — в Documents (разворачиваемая панель).

---

## Стек

| Слой | Технология | Версия |
|------|------------|--------|
| UI | React + TypeScript | `^19.2.7` / `~6.0.2` |
| Сборка | Vite | `^8.1.1` |
| Стили | Tailwind CSS | `^4.3.2` |
| Роутинг | React Router | `^7.18.1` |
| Диаграммы | `bpmn-js` `^18.19`, `mermaid` `^11.16` | |
| Экспорт | `docx`, `pdf-lib`, `xlsx`, `pptxgenjs` | |
| Импорт Word | `mammoth` `^1.12` | |
| Облако (опц.) | `@supabase/supabase-js` `^2.110` | |
| Линтер | oxlint `^1.71` | `.oxlintrc.json` |

> [!warning] Чего в проекте нет
> Не добавляй без явного запроса: Next.js, Server Components, shadcn/ui, Recharts, Redux/Zustand, тесты (скрипта `test` нет).

---

## ИИ-провайдеры

Единая точка входа — `lib/ai-providers.ts`. **Шесть провайдеров:**

| Провайдер | Модели |
|-----------|--------|
| OpenAI | `gpt-4o`, `gpt-4o-mini`, `gpt-4-turbo` |
| Anthropic (Claude) | `claude-sonnet-4-20250514`, `claude-3-5-haiku-20241022` |
| Google Gemini | `gemini-3.1-pro`, `gemini-3.5-flash`, `gemini-3.1-flash-lite`, `gemini-3-flash` |
| Grok (xAI) | `grok-3`, `grok-3-mini`, `grok-2` |
| Xiaomi MiMo | `mimo-v2.5`, `mimo-v2.5-pro` |
| Z.AI (GLM) | `glm-4.7`, `glm-4.7-flash`, `glm-4.7-flashx` |

**Ключи вводятся на экране «Настройки»** и хранятся в `localStorage`. Переменные `VITE_*_API_KEY` из `.env.example` кодом **не читаются** — устаревший артефакт.

Retry при 429 — до 2 попыток с экспоненциальной задержкой. Есть тестовый режим на моках, работает без ключа.

---

## Хранилище

`localStorage` через save-функции сторов + `lib/storage-safe.ts` (safeSetItem: QuotaExceededError → сообщение RU/EN). **Полный бэкап/восстановление** — Настройки → «Данные» (`lib/backup.ts`, все `ba-*` ключи одним JSON). Слой адаптеров `lib/storage/` (local/supabase) сохранён как задел, к UI не подключён.

| Ключ | Содержимое |
|------|-----------|
| `ba-artifacts` | Единое хранилище артефактов (с историей версий) |
| `ba-tz-documents` | Документы ТЗ с блоками и версиями |
| `ba-tz-templates` | Шаблоны ТЗ |
| `ba-tz-presets-version` | Версия пресетов, для миграции |
| `ba-file-storage` | Загруженные файлы |
| `ba-assistant-settings` | Настройки (провайдер, ключ, модель, тест-режим) |
| `ba-assistant-lang` | Язык интерфейса |
| `ba-theme` | Тема |

**Реально читаемые переменные окружения:** `VITE_STORAGE_BACKEND`, `VITE_SUPABASE_URL`, `VITE_SUPABASE_ANON_KEY`.

---

## Запуск

```bash
npm run dev      # vite, порт по умолчанию :5173
npm run lint     # oxlint
npm run build    # tsc -b && vite build
```

---

## Деплой

| Платформа | URL | Конфиг |
|-----------|-----|--------|
| Vercel | `https://delta-ba.vercel.app` | `vercel.json`, SPA-rewrites |
| Netlify | `https://delta-ba.netlify.app` | `netlify.toml` |

---

## Дизайн

Токены плоским hex в `@theme` и `.dark` (`src/index.css`), значения совпадают с [[Delta_Design_DNA|ДНК]] точно. Именование собственное: `--color-bg`, `--color-surface`, `--color-text` — ДНК §13.3 допускает оба варианта.

**Рельса есть, но горизонтальная** — `components/layout/Header.tsx` на токенах `--rail-*`. Сайдбара нет, поэтому Ink-классы не реализованы. **Brand Splash есть** (с 2026-08-23): `components/BrandSplash.tsx`, стек трёх «дельт», 1.2 c при старте.

**Канон 2026-08-23:** рамки — панели 2px / карточки главной 3px (чёрные `border-text`); блоки ТЗ — белая карточка + цветная точка статуса; контексты ТЗ — чернильные плашки (были emerald/violet/amber); lift-класс `.lift`; scale(0.97); selection 22%; keyframes без `delta-`-префикса. Из прежних расхождений ДНК остаются: ~400 Tailwind-палитр vs токены, серые полутоны в TSX (125 `gray-*` + hex), зоопарк скруглений.

---

## Структура

```
src/
├── pages/         11 экранов-роутов (все кроме Home — React.lazy)
├── components/
│   ├── tz/        редактор ТЗ (7)
│   ├── ai/        BpmnViewer, MermaidViewer, PlantUmlViewer
│   ├── input/     InputSourceSelector
│   ├── layout/    Header
│   ├── ApiKeyNotice, BrandSplash, ErrorBoundary
├── hooks/         useAI, useSettings
└── lib/           ai-providers, prompts/, tz-*, artifact-store,
                   doc-generator (ленивый чанк), backup, storage-safe,
                   storage/, i18n
```

**Code splitting:** главный чанк 260 КБ (было 2.6 МБ); doc-generator 1.7 МБ — отдельный чанк. **Крупнейшие файлы:** `lib/prompts/tz-catalog.ts` (1724), `pages/TzEditor.tsx`, `lib/tz-template.ts`.

---

## Известные расхождения

1. **Тестов нет** (скрипта `test` нет).
2. **Серые полутона и Tailwind-палитра** в TSX (~125 `gray-*`, ~79 hex) — остаток до полной миграции на токены.
3. **Supabase-слой не подключён к UI** — поля URL/Key в настройках не читаются; адаптер ждёт `VITE_*` из env.
4. **i18n:** ~213 инлайн-тернарников `locale === 'ru' ?` против 39 `t()`; ~65 строк только RU (TzEditor/TemplateEditor/ImportModal/BlockEditView).
5. **Стриминга AI нет** — все провайдеры ждут целиком; usage/токены не показываются.
6. **`xlsx@0.18.5`** с известными CVE (npm-версия SheetJS не обновляется).
7. **Баг `duplicateTemplate`** — копия теряет контекст (падает в «1С»); «Сохранить» на пресете не блокируется.

---

## Связано

- [[Delta_Design_DNA]] · [[Delta_Расхождения_проектов]] · [[Промпты-агентов]] · [[Delta-tasker]]
