# Ресьорч: кастомізація списку ігор / фаворитів

> Дослідження патернів **кастомізації списків** (favorites / pinned / collections) у різних
> застосунках — гейм-лаунчери, ОС, браузери, продуктивність і медіа.
> Мета: знайти найкращі моделі взаємодії для **кастомізованого "Favorites bar"** у редизайні WGC.
> Дата: 2026-06-17.

---

## 0. Відправна точка — Battle.net (референс)

Те, з чого почали. У Battle.net є горизонтальний бар **FAVORITES** з іконками ігор:

- **Додати** — кнопка **`+`** у кінці бару → дропдаун `ADD A FAVORITE` зі списком усіх ігор + пункт `Manage All Favorites`.
- **Переставити / видалити** — **right-click по іконці** → контекстне меню: `Move Right`, `Move Left`, `Remove from Favorites`.
- **Підказка** — на hover спливає tooltip із повною назвою гри (іконки самі по собі неоднозначні).
- **Режим редагування** — клік на `+` перемикає бар у стан із `×` (вийти з режиму).

**Сильні сторони:** простий, явні дії в right-click, є tooltip для іконок.
**Слабкі сторони:** переставляння через `Move Left/Right` по одному кроку — повільно проти drag&drop; немає груп; іконки без підписів важко розрізняти (критично для 35+).

> ⚠️ 3 референс-скріншоти Battle.net надав замовник у чаті. Якщо потрібно зберегти їх у цю папку
> файлами — перекинь їх сюди (напр. `ref_battlenet_favorites-bar.png`, `ref_battlenet_context-menu.png`,
> `ref_battlenet_add-favorite.png`), і я допишу їх у галерею нижче.

---

## 1. Гейм-лаунчери

| Лаунчер | Додати | Переставити | Сховати/видалити | Групи | Де живе |
|---|---|---|---|---|---|
| **Steam** | right-click → Add to favorites / ⭐; collections | collections — drag; **ігри всередині — лише сорт, без drag** ❗ | hide → Hidden collection | ✅ manual **+ dynamic (smart rules)** | лівий рейл + home shelves |
| **GOG Galaxy 2.0** | tag "Favorites" + bookmark | **повний drag&drop** ✅ | hide (native 2.0.3) | ✅ tags + saved-view shelves (dynamic) | лівий рейл (saved views) |
| **EA app** | ❤️ heart icon | ❌ немає | ❌ тільки через тікет | ❌ | лівий рейл "My Collection" |
| **Ubisoft Connect** | ❌ немає favorites | ❌ | right-click → hide (внизу, погана видимість) | ❌ | таб "Games" |
| **Xbox PC app** | `+` → додати будь-який .exe | ❓ не підтверджено | hide по сторфронту | ✅ manual groups | бічна панель "My Library" |
| **Epic** | ⋯ → Add to Collection | ❌ немає | ❌ тільки глобальний toggle | manual only | таб Library |
| **Riot / Rockstar** | ❌ немає | ❌ | ❌ | ❌ | фіксований список |

### Ключові висновки по лаунчерах
1. **Переставляння (reorder) — спільне слабке місце всієї індустрії.** Ручний drag-to-arrange ігор майже ніде не працює добре. **Steam дозволяє drag для колекцій, але не для ігор всередині них.** GOG — єдиний з нормальним drag&drop ігор. → **Це найочевидніша точка диференціації для WGC.**
2. **Дві домінантні моделі "додати":** (а) right-click → "Add to…" (універсально, найкраща discoverability для 35+); (б) явний афорданс — ⭐/❤️/`+`.
3. **Smart / dynamic groups** є лише у Steam (rules: жанр, play state, installed…) і GOG (saved filter views). Решта — або manual, або нічого.
4. **Hide ≠ remove.** Найкраще зроблено у Steam (→ Hidden collection) і Ubisoft (хоч і захована внизу). Epic/EA не дають ховати окремі ігри взагалі.
5. **Конвенція розташування:** кастом-список майже завжди живе в **лівому вертикальному рейлі**, у центрі — сітка обкладинок. WGC-бар натомість **горизонтальний угорі** (як Battle.net) — це свідомо інша, "launcher-hub" конвенція.

**Анти-патерн Steam:** toggle `Group by Collection` ховає власну кастомізацію користувача й заплутує. → Не повторювати.

---

## 2. ОС та браузери

| Платформа | Додати | Переставити | Видалити | Групи / папки |
|---|---|---|---|---|
| **macOS Dock** | drag in / right-click → Keep in Dock | **drag (завжди live, без режиму)** | drag off до "Remove" (погано!) / right-click | тільки Stacks для файлів |
| **Windows 11 Taskbar** | right-click → Pin | drag | right-click → Unpin | ❌ плоский ряд |
| **Windows 11 Start** | right-click → Pin to Start | drag **+ "Move to top"** (меню!) | Unpin | ✅ **drag-onto-icon → папка + rename** |
| **Chrome bookmarks bar** | ⭐ icon / drag | drag | right-click → Delete | ✅ folders (right-click → Add folder) |
| **Edge favorites bar** | ⭐ icon | drag | right-click → Delete | ✅ folders; 3-стан видимості бару |
| **Pinned tabs** | right-click → Pin | drag | Unpin | implicit (зона pinned) |
| **Speed dial** (Opera/Vivaldi) | `+` tile | drag | hover → ×/3-dot | ✅ drag-onto (Opera) / tabbed groups (Vivaldi) |
| **iOS Home** | drag з App Library | **jiggle mode** + drag | `−` badge: Remove ≠ Delete | ✅ drag-onto → folder |
| **Android Home** | long-press → drag | long-press + drag | drag до labeled "Remove"/"Uninstall" | ✅ drag-onto → folder |

### Ключові висновки по ОС/браузерах
1. **Reorder = drag, всюди.** Але drag-афорданс часто *невидимий* (Win taskbar, bookmark bars). iOS/Android/Opera роблять його явним.
2. **Режим редагування vs без-режиму:** iOS використовує явний **jiggle/edit mode** → менше випадкових перестановок. macOS/Android — drag завжди live через long-press. → **Для 35+ десктоп-аудиторії явний, підписаний режим редагування безпечніший.**
3. **"Move to top" у Win11 Start** — меню-альтернатива довгому drag. **Важлива для доступності** (як `Move Left/Right` у Battle.net, але краще — одразу в початок).
4. **Дворівневе видалення — домінантний безпечний патерн:** iOS (Remove from Home ≠ Delete) і Android (Remove ≠ Uninstall) розділяють "прибрати з поверхні" і "видалити повністю". **Android з підписаними drop-таргетами — найзрозуміліший** destructive-афорданс. macOS "drag off until Remove" — **найгірший** (невидимий, випадковий).
5. **Папки створюють двома способами:** drag-onto-icon (iOS/Android/Win11/Opera) — інтуїтивно, але "виявляється" лише жестом; або явний "New folder" (bookmark bars) — discoverable. → Для зростаючого портфоліо WG **робити групування з видимим афордансом**, не лише жестом.
6. **Auto-overflow тримає поверхню чистою:** App Library / "Other bookmarks" / chevron `»`. Фаворити лишаються сфокусованими, а повне портфоліо — на крок глибше.
7. **Favicon-only занадто неоднозначний для 35+.** Pinned tabs / щільні бари втрачають підписи. → **Підписані плитки ігор краще за icon-only.**

---

## 3. Продуктивність / комунікація / медіа

| Застосунок | Додати/pin | Переставити | Видалити | Групи | Афорданс |
|---|---|---|---|---|---|
| **Slack** | ⭐ star → авто-секція; custom sections | **drag + явний Sort** (A-Z/recency) | unstar | ✅ **named + emoji секції**, collapsible | star, 3-dot, drag, chevron |
| **Discord** | drag server-onto-server | drag (+ keyboard a11y drag) | drag out (folder авто-зникає) | ✅ folders, **колір + назва** | drag (схований!), right-click |
| **Notion** | ⭐ star → Favorites | drag (live) | star off / ⋯ | секції + nesting | star, drag, hover ⋯ |
| **Spotify** | swipe→📌 / right-click; **макс 4 pin** | pins угорі; Custom order (desktop) | повторити pin | filter chips + folders | pin badge, chips, sort |
| **Netflix** | `+` → ✓ toggle | ❌ **алгоритм, не юзер** ❗ | ✓ off | ❌ плоский | +/✓ |
| **YouTube** | Subscribe | ❌ **алгоритм** ❗ | Unsubscribe | ❌ | toggle |
| **Figma** | ⭐ (hover) / right-click | drag | un-star | Teams→Projects (fixed) | star (тільки hover ❗) |
| **MS Office QAT** | right-click → Add to QAT | **Move Up/Down кнопки** ✅ | Remove | ✅ manual 3-рівні | модальний 2-pane редактор |
| **Photoshop toolbar** | Edit Toolbar dialog, drag | drag | drag out (non-destructive) | drag-onto → nest | модальний dialog |
| **iOS Control Center** | `+` → Gallery | drag | `−` badge | pages | **WYSIWYG + resize плитки** |
| **Chrome extensions** | 🧩 → pushpin (blue/gray) | drag | unpin → overflow | ❌ (pinned vs overflow) | pushpin toggle |

### Ключові висновки по застосунках
1. **Алгоритмічний порядок (Netflix, YouTube) — застереження для 35+.** Список, що сам тихо перевпорядковується, **руйнує просторову пам'ять** і прибирає контроль. → WGC-бар має бути **строго ручним**.
2. **Найкращий афорданс "додати": один видимий toggle.** Chrome pushpin (blue/gray) і Netflix `+/✓` — найзрозуміліші. Універсальний ⭐ (Slack/Notion/Figma) — теж сильний. **Уникати hover-only / тільки-context-menu** (Figma, Apple Music) — погана discoverability.
3. **Найкращий reorder: drag + явний fallback.** Drag — очікування 8/11 застосунків, але важкий для слабкої моторики. Slack Sort, Office Up/Down, Discord keyboard-drag — accessible-альтернативи. → **Давати обидва.**
4. **Найкраще групування: іменовані, підписані emoji, collapsible секції Slack** — золотий стандарт читабельного manual-групування.
5. **Discord: агрегований badge непрочитаного на згорнутій папці** — найкращий at-a-glance status. → Ідеально для **сповіщень про новини/івенти на згорнутій групі ігор** у WGC.
6. **Spotify "макс 4 pin"** — розумне обмеження: priority-зона коротка й однозначна.
7. **iOS Control Center — найкраще WYSIWYG-редагування** (in-place + resize = більші таргети для старшого зору). Але **обов'язково з видимою кнопкою "Customize"**, не лише long-press.
8. **Reversibility safety net:** Chrome overflow і Photoshop "Extra Tools" гарантують, що нічого не губиться — unpin ховає, а не видаляє. → Критично для впевненості 35+.

---

## 4. Зведені висновки → рекомендації для WGC Favorites bar

Синтез усіх трьох доменів. Рекомендований "blend" для бару фаворитів WGC:

1. **Видима іконка pin/⭐ на кожній плитці гри** (Chrome/Slack), ніколи не hover-only — і **ніколи стан лише кольором** (WCAG AA вимагає не покладатися на колір).
2. **Обмежена, завжди-вгорі зона "Favorites"** (дисципліна Spotify "4 pin") — для передбачуваності та чистоти.
3. **Drag-to-reorder + явний fallback** ("Move to start" як у Win11, або Up/Down як в Office) — для моторної доступності 35+. Battle.net `Move Left/Right` працює, але по одному кроку — повільно; додати "в початок/кінець".
4. **Явний, підписаний режим редагування ("Customize")** — уникати схованих жестів Discord/Spotify/Control Center як єдиного шляху. iOS jiggle-mode як орієнтир, але з видимою кнопкою.
5. **Дворівневе видалення:** "прибрати з бару" ≠ "сховати гру". Підписані дії (як Android Remove/Uninstall), reversible (overflow "All games").
6. **Іменовані, collapsible групи** (Slack) для тих, хто хоче структуру — **опційно, не примусово**. Масштабованість для всього портфоліо WG.
7. **Агрегований badge новин/івентів на згорнутій групі** (Discord) — закриває engagement-мету WGC без розгортання.
8. **Підписані плитки, не icon-only** — Battle.net показує назву лише на hover; для 35+ краще постійний підпис або хоча б більший контраст/таргет.
9. **Overflow "All games" / App-Library модель** — бар лишається сфокусованим на фаворитах, повне портфоліо на крок глибше.
10. **Smart shelves з адекватними дефолтами** (сила GOG/Steam dynamic), але **без примусу tag/bookmark mental model**, що робить GOG складним для не-power-users.

### Топ-3 точки диференціації для WGC
- **Нормальний drag&drop reorder ігор** — те, що провалює майже вся індустрія (навіть Steam).
- **Контент-badge (новини/івенти) прямо в барі фаворитів** — поєднує time-to-play з engagement-метою брифу.
- **Доступність 35+ за замовчуванням:** підписи, великі таргети, явний режим редагування, меню-fallback до drag.

---

## 5. Галерея скріншотів (у цій папці)

### Гейм-лаунчери
- `launcher_steam_dynamic-collection.jpg` — Steam, редактор dynamic collection (rules)
- `launcher_steam_collections.jpg` — Steam, "Your Collections"
- `launcher_steam_create-collection.jpg` — Steam, створення колекції
- `launcher_epic_library-filter.jpg` — Epic, фільтри бібліотеки
- `launcher_gog_main.png` — GOG Galaxy 2.0, головна (saved-view shelves у рейлі)
- `launcher_uplay_gamelist.png` — Uplay/Ubisoft, список ігор (legacy)
- `launcher_xbox_aggregated-library.jpg` — Xbox PC app, агрегована бібліотека

### ОС / браузери
- `os_win11_start-drag-folder.jpg` — Win11 Start, drag-onto → папка
- `os_win11_start-folder-rename.jpg` — Win11 Start, перейменування папки
- `os_win11_start-folders.jpg` — Win11 Start, кілька іменованих папок

### Застосунки
- `app_slack_sidebar-sections.jpg` — Slack, кастом-секції сайдбару
- `app_slack_drag-channel.png` — Slack, drag каналу в секцію
- `app_discord_drag-server-folder.jpg` — Discord, drag server-onto-server → папка
- `app_discord_folder-colored.jpg` — Discord, папка з кольором/назвою
- `app_notion_favorites.jpg` — Notion, додавання у Favorites (⭐)
- `app_spotify_pinned-playlists.jpg` — Spotify, pinned playlists
- `app_netflix_mylist.jpg` — Netflix, "My List" (+/✓)
- `app_office_move-up-down.png` — MS Office QAT, кнопки Move Up/Down
- `app_office_qat-dialog.png` — MS Office, діалог Quick Access Toolbar
- `app_ios_control-center-resize.png` — iOS Control Center, resize плитки в edit-mode
- `app_chrome_pin-extensions.png` — Chrome, pin/unpin розширень (pushpin)

> _Photoshop toolbar-edit скріншот не завантажився (сайт блокує ботів) — за потреби додам вручну._

---

## 6. Джерела (вибірково)

**Лаунчери:** Steam Library Update (store.steampowered.com/libraryupdate), TheGamer (dynamic collections), Epic Help (collections), PCWorld (GOG Galaxy 2.0), Ubisoft Help 000061584 (hide games), Windows Central / Xbox Wire (Xbox aggregated library).
**ОС/браузери:** Apple Support (Dock, iOS 108307), Microsoft Support (Taskbar, Start, pin apps), XDA (Win11 Start folders), Google Chrome Help (bookmarks), Mozilla (pinned tabs), Vivaldi/Opera Help (speed dial).
**Застосунки:** Slack Help (sections, star), Discord Support (Server Folders 101), Notion Help (sidebar), Spotify Support (Your Library, pins), Netflix Help (My List), Figma Help (star files), Microsoft Office Support (QAT, ribbon), Adobe Helpx (toolbar), Apple Support (Control Center), Google Chrome Help (extensions).

> Повні URL збережені в історії ресьорчу; за потреби винесу в окремий `sources.md`.
