## Excel Import/Export (Angular Frontend)

This repository contains the Angular frontend for a full‑stack Excel Import/Export application built with NestJS (backend) and Angular (frontend).

The app lets users upload Excel files, preview and validate data, submit it to the server, and download data as Excel.

### Tech Stack
- **Frontend**: Angular 20, TypeScript, Tailwind CSS
- **Backend**: NestJS (separate service; see API section)
- **Build/Tooling**: Angular CLI, Jasmine/Karma

---

## Features
- **Upload Excel**: Select or drag-and-drop `.xlsx` files, send to backend for parsing/validation
- **Preview Data**: Tabular preview before submit (with validation messages)
- **Submit Data**: Post validated data to backend
- **Export to Excel**: Request and download server-generated `.xlsx`
- **(Optional) Client parsing/export**: Use `xlsx` and `file-saver` on the client if desired

---

## Prerequisites
- Node.js 18+ (20+ recommended)
- npm 9+
- Angular CLI 20+ (`npm i -g @angular/cli`)
- Running backend service (default: `http://localhost:3000`)

---

## Getting Started

### 1) Install dependencies
```bash
npm install
```

### 2) Start the frontend
```bash
npm start
# or
ng serve
```
App runs at `http://localhost:4200/` by default.

### 3) Backend URL
Set the backend API base URL in your frontend service/config (e.g., `API_BASE_URL = http://localhost:3000`). If you use a dedicated config token or environment file, point it to your NestJS instance.

---

## API Contract (NestJS Backend)
The frontend expects these endpoints (paths are examples; adjust if your backend differs):

- **POST** `/api/excel/upload`
  - Content-Type: `multipart/form-data`
  - Field: `file` (Excel `.xlsx`)
  - Response (example):
    ```json
    {
      "success": true,
      "data": {
        "headers": ["Name", "Email", "Age"],
        "rows": [["Alice", "a@example.com", 30], ["Bob", "b@example.com", 24]]
      },
      "errors": [
        { "row": 3, "column": "Email", "message": "Invalid email", "severity": "error" }
      ]
    }
    ```

- **POST** `/api/excel/process`
  - Content-Type: `application/json`
  - Body (example):
    ```json
    {
      "headers": ["Name", "Email", "Age"],
      "rows": [["Alice", "a@example.com", 30]]
    }
    ```
  - Response: `{ "success": true }` (shape can include more details)

- **POST** `/api/excel/export`
  - Content-Type: `application/json`
  - Body: optional filter/payload
  - Response: binary `.xlsx` stream
  - Headers: `Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`

### Example cURL
```bash
# Upload
curl -X POST http://localhost:3000/api/excel/upload \
  -F "file=@./sample.xlsx"

# Process
curl -X POST http://localhost:3000/api/excel/process \
  -H "Content-Type: application/json" \
  -d '{"headers":["Name","Email"],"rows":[["Alice","a@example.com"]]}'

# Export (downloads export.xlsx)
curl -X POST http://localhost:3000/api/excel/export \
  -H "Content-Type: application/json" \
  --output export.xlsx \
  -d '{}'
```

---

## Optional: Client-side Excel parsing/export
By default, parsing/export can be fully handled by the backend. If you want client-side parsing (preview without server) or client-side export:

```bash
npm install xlsx file-saver
npm install -D @types/file-saver
```
- Parse on client with `xlsx` for quick preview.
- Export on client by converting JSON → worksheet → workbook → Blob and download with `file-saver`.

> Note: Keep large-file parsing on the server for performance and memory safety.

---

## Scripts
- `npm start` – run dev server at `http://localhost:4200/`
- `npm run build` – production build to `dist/`
- `npm test` – unit tests (Karma/Jasmine)

---

## Project Structure (frontend)
```
src/
  app/
    app.ts            # Root component (standalone)
    app.html          # Root template
    app.config.ts     # Application providers (router, http, etc.)
    app.routes.ts     # Routes (add lazy-loaded features here)
  styles.scss         # Global styles (Tailwind CSS)
  main.ts             # Bootstrap
```

---

## Guidelines (coding conventions)
This project follows modern Angular 20 patterns:
- Standalone components, signals for state, native control flow (`@if`, `@for`)
- `ChangeDetectionStrategy.OnPush` for components
- Prefer `input()` / `output()` helpers over decorators
- Lazy load feature routes
- Tailwind CSS for styling

See `.cursorrules` in the repo root for assistant rules.

---

## CI/CD & Docker (overview)
- CI: Use GitHub Actions to lint, test, and build.
- Docker: Multi-stage build for production (frontend served by Nginx or static host).
- Environments: staging and production with separate configs.

> Implementation specifics may live in separate backend/frontend repos. Ensure the frontend `API_BASE_URL` matches deployed backend.

---

## Troubleshooting
- CORS errors: enable CORS on NestJS (`@nestjs/platform-express` with proper CORS config).
- File too large: raise upload limits (NestJS Multer and reverse proxy limits).
- MIME types: ensure correct `Content-Type` headers for Excel downloads.

---

## License
Proprietary or your chosen license. Replace this section as needed.
