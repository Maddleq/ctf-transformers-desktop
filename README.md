# CTF Transformers — Desktop (Tauri)

Десктоп-обёртка для приложения «Общая информация по игре». Фронтенд —
самодостаточный файл `dist/index.html` (всё встроено, работает офлайн). Tauri
упаковывает его в нативное Windows-приложение с установщиком (`.exe` / `.msi`).

```
desktop/
├─ dist/index.html              ← само приложение (офлайн-сборка)
├─ package.json                 ← @tauri-apps/cli
├─ .github/workflows/release.yml← CI: собирает установщик по тегу
└─ src-tauri/
   ├─ tauri.conf.json           ← окно, иконки, цели сборки (nsis + msi)
   ├─ Cargo.toml · build.rs · src/ · capabilities/
   └─ icons/app-icon.png        ← исходник иконки (полный набор генерит CI)
```

---

## Вариант A (рекомендую): сборка `.exe` в облаке через GitHub Actions

Локально ничего, кроме `git`, ставить не нужно — Rust/Node/Tauri отработают в CI.

1. Скачай папку `desktop/` из проекта (меню экспорта).
2. Создай новый репозиторий на GitHub (например `ctf-transformers-desktop`).
3. В папке `desktop/` выполни:
   ```bash
   git init
   git add .
   git commit -m "CTF Transformers desktop"
   git branch -M main
   git remote add origin https://github.com/<ТВОЙ_ЛОГИН>/ctf-transformers-desktop.git
   git push -u origin main
   ```
4. Поставь тег версии — это запускает сборку:
   ```bash
   git tag v0.1.0
   git push origin v0.1.0
   ```
5. Вкладка **Actions** покажет сборку (~5–10 мин). По завершении установщик
   (`.exe` и `.msi`) появится во вкладке **Releases** — скачивай и устанавливай.

Для новой версии — поменяй `version` в `package.json` и `src-tauri/tauri.conf.json`,
затем новый тег (`git tag v0.2.0 && git push origin v0.2.0`).

---

## Вариант B: локальная сборка

Нужны [Node 18+](https://nodejs.org) и [Rust](https://rustup.rs) (+ на Windows —
WebView2, обычно уже стоит).

```bash
cd desktop
npm install
npm run icon      # генерирует набор иконок из src-tauri/icons/app-icon.png
npm run build     # установщик в src-tauri/target/release/bundle/
npm run dev       # запуск без сборки установщика (для проверки)
```

---

## Обновление приложения

`dist/index.html` — это экспорт из дизайн-проекта. После изменений в дизайне
пересоберите офлайн-файл и замените `dist/index.html`, затем новый тег.

## Заметки
- Цели сборки — `nsis` (`.exe`-инсталлятор) и `msi`. Лишнее можно убрать в
  `tauri.conf.json → bundle.targets`.
- Иконка приложения — `src-tauri/icons/app-icon.png` (1024×1024). Замени файл,
  чтобы поменять иконку; полный набор пересоберётся в CI.
- «Папка логов» (выбор каталога для логов) в десктоп-сборке работает надёжно —
  WebView2 поддерживает File System Access API.
