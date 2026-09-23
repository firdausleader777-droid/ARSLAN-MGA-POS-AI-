# ARSLAN POS Agent Guide

## Project Shape

- This is a static, single-file browser application. The UI, CSS, and JavaScript all live in [index.html](index.html).
- It is an Indonesian-language POS application covering login and roles, cashier transactions, products, customers, sales staff, suppliers, reports, printer settings, and optional AI insights.
- There is no package manifest, bundler, backend, or automated test suite. External libraries are loaded from CDNs in [index.html](index.html).

## Running And Validation

- Serve the workspace from a local HTTP server when testing browser behavior:
  `python3 -m http.server 8000`
- Open `http://localhost:8000` in a browser. A local server is preferred over `file://` because Web Serial and other browser capabilities may require localhost or HTTPS.
- After a change, manually exercise the affected flow and check the browser console for errors. For data changes, verify both the visible UI and the relevant `localStorage` entry.
- There are no repository build, lint, or unit-test commands unless one is added later.

## Architecture And State

- Application state is global and imperative. Keep changes local to the owning function and preserve the existing global API and inline event handlers.
- Business data is initialized near the state declarations in [index.html](index.html), persisted under `arslan_*` `localStorage` keys, and optionally synchronized to Supabase.
- Login is client-side role gating only: the admin PIN is stored locally and defaults to `1234`; it is not server authentication.
- Cashier behavior is centered on `renderProduk`, `tambahKeKeranjang`, `ubahQty`, `hitungHargaItem`, `renderKeranjang`, and `selesaikanTransaksi`.
- Master-data CRUD follows the `bukaModal*`, `simpan*`, and `hapus*` naming pattern. Reports and charts are rendered by `renderLaporan`, `renderTabelYoY`, and `renderGrafikLaporan`.
- Printer and cloud setup are browser-dependent. Treat Supabase and printer connectivity as optional and keep local-only behavior working when either is unavailable.

## Editing Conventions

- Use Indonesian domain names and existing naming patterns for new functions, IDs, and labels unless an external API requires another name.
- Prefer the existing DOM and CSS custom-property patterns. Avoid introducing a framework, module system, or new dependency for a focused change.
- Make small, targeted edits because the file is large and tightly coupled. Preserve unrelated user changes and avoid broad formatting rewrites.
- Keep browser event behavior compatible with existing inline `onclick`, `oninput`, and `onsubmit` attributes.
- When rendering user or persisted values into HTML, prefer text APIs or escape values first. Do not introduce new unsafe `innerHTML` interpolation.
- Guard changes that read `localStorage` or optional DOM elements when the affected path can encounter missing or malformed data.

## Important Checks

- Test both admin and cashier roles when changing access or navigation.
- For transaction changes, verify stock limits, retail/wholesale/auto pricing, discount and payment calculations, receipt output, and persisted transaction history.
- For CRUD changes, verify create, edit, delete, refresh, and persistence behavior.
- Reset or isolate browser `localStorage` when testing initialization or default-data behavior so old workspace data does not hide regressions.
- Do not expose Supabase keys or treat the local admin PIN as secure authentication.