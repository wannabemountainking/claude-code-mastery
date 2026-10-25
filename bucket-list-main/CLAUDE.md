# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

This is a static site with no package manager, build step, linter, or test suite. There is no `package.json`, so there are no build, lint, or test commands to run.

- Run: open `index.html` directly in a browser, or serve the folder with `python -m http.server 8000` and visit `http://localhost:8000`.
- Verify changes by reloading the page in the browser. Data persists in LocalStorage under the key `bucketList`, so clear it (DevTools → Application → Local Storage) to test the empty state.

## Architecture

The app is three layers loaded as plain `<script>` tags in `index.html` (`storage.js` before `app.js`), with no modules or bundler.

- **Data layer (`js/storage.js`)**: `BucketStorage` is a plain object that owns all persistence. Every mutation does `load()` → modify the whole array → `save()`. Nothing is cached in memory, so `app.js` always gets fresh data by calling storage again.
- **Controller/view layer (`js/app.js`)**: `BucketListApp` binds DOM events and renders. `render()` is the only way the UI updates: it refreshes the stats, fetches the filtered list from storage, and rebuilds `#bucketListContainer.innerHTML` from a template string. After any change, call `render()`. Do not patch individual DOM nodes.
- **Wiring**: The list buttons use inline `onclick="app.handleToggle(...)"` attributes that call the global `app` instance, which `app.js` creates on `DOMContentLoaded`. Any new per-item handler must be exposed the same way or it will not be reachable from the generated HTML.
- **Filters**: The filter state lives in `BucketListApp.currentFilter` (`'all' | 'active' | 'completed'`). `BucketStorage.getFilteredList()` applies it, and the stats always count the full list, not the filtered one.

### Data model

Each item in the LocalStorage array has this shape:

```
{ id: string (Date.now().toString()), title, completed, createdAt (ISO), completedAt (ISO | null) }
```

New items are inserted at the front (`unshift`), so the list is newest-first. `id` is used as the lookup key in every storage method.

### Gotchas

- Item HTML is built with template strings. Titles are escaped with `escapeHtml()`, but that helper does not escape `"` and the title is placed inside a double-quoted `onclick="..."` attribute. Titles containing `"` can break the attribute. Keep this in mind when changing the template. Using `data-*` attributes with event listeners avoids the problem.
- Dark mode is CSS-only (`prefers-color-scheme` in `css/styles.css`) and overrides Tailwind's `bg-white`, `text-gray-800`, and similar classes. New UI that uses other Tailwind color classes will not be darkened automatically.
- Tailwind is loaded from the CDN (`cdn.tailwindcss.com`) and the page needs network access to render correctly.
- The README mentions an `assets/` folder that does not exist. Do not assume it is present.

## Documentation

`README.md` is written in Korean and describes the feature list and the planned improvements (categories, notes, target dates, priorities, JSON import/export, dark mode toggle, drag-and-drop). Keep UI text and code comments in Korean to match the existing code.
