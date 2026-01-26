# AGENTS.md

## Project Overview
This is a sophisticated, single-file Serverless Mermaid Chart Editor. It allows users to create, edit, manage, and persist Mermaid diagrams entirely in the browser.

**Core Philosophy:**
- **Zero Build Step:** The application must run directly from `index.html` without `npm install` or compilation.
- **Client-Side Only:** No backend server. All persistence is handled via browser APIs (IndexedDB, File System Access) or direct API calls (S3, WebDAV).
- **Dependency Minimalism:** Libraries are loaded via reliable CDNs (Tailwind, Mermaid, AWS SDK, Diff.js).

## Environment & Setup

### Running the Application
Since there is no build step, "deployment" is simply serving the file.

1.  **Direct Open:** Double-click `index.html` to open in Chrome/Edge/Firefox.
2.  **Local Server (Recommended):**
    ```bash
    # Python 3
    python3 -m http.server 8000
    ```
    Access at `http://localhost:8000`.

### Dependencies (CDN)
- **Styling:** Tailwind CSS (v3.x)
- **Core Logic:** Mermaid.js (v10.x)
- **Remote Storage:** AWS SDK (v2.x)
- **Diffing:** jsdiff (v5.x)
- **Icons:** FontAwesome (v6.x)

## Testing Strategy

There is no automated test suite. Testing is manual and feature-based.

### Manual Test Plans (Run these for verification)

**1. Core Rendering**
- **Action:** Paste `graph TD; A-->B;`.
- **Expectation:** Preview pane updates immediately. No console errors.

**2. Storage: S3 & WebDAV**
- **Action:** Configure Settings (`#s3ConfigModal`). Select "S3" or "WebDAV".
- **Action:** Click "Save" in footer.
- **Expectation:** Notification "Saved successfully". File appears in remote bucket/folder.
- **Verify:** Use the integrated "Cloud File Browser" (`#s3ListModal`) to see the file.

**3. Storage: Local Native (FS Access)**
- **Action:** Click "Open" (folder icon) in footer. Select a local `.md` file.
- **Expectation:** File content loads. Footer shows "Native File: filename.md".
- **Action:** Click "Save".
- **Expectation:** Changes write directly to disk without a "Save As" prompt (after initial permission).

**4. Image Export**
- **Action:** Click "Export Image" -> "PNG".
- **Expectation:** Downloaded PNG matches preview exactly (styles included).

## Code Style Guidelines

### JavaScript (`<script>` block)
- **Standard:** Modern ES6+. Use `const` over `let`. **Never** use `var`.
- **Async/Await:** Prefer `async/await` for all storage/IO operations.
- **Naming:** `camelCase` for JS, `UPPER_SNAKE_CASE` for Constants, `kebab-case` for HTML IDs.
- **Formatting:** 4 spaces indentation. Single quotes for JS strings.

### Architecture & Patterns

#### 1. State Management
All application state lives in the global `state` object.
```javascript
const state = {
    tabs: [],
    uiConfig: {},       // User prefs (formats, themes)
    s3Config: {},       // Storage credentials (S3/WebDAV)
    currentFile: {      // Active file metadata
        type: 'local' | 's3' | 'webdav' | 'native',
        key: 'path/to/file.md', // or filename for native
        handle: FileSystemFileHandle // Only for 'native' type
    }
};
```

#### 2. Storage Abstraction (`StorageProvider`)
Remote operations use the `StorageProvider` interface.
- **Classes:** `S3StorageProvider`, `WebDAVStorageProvider`.
- **Methods:** `list(path)`, `get(key)`, `put(key, body)`, `delete(key)`.
- **WebDAV Note:** Uses a custom XML parser to handle namespaced responses (`d:response`) and `AbortController` for timeouts.

#### 3. Native File System
- **API:** Uses `window.showOpenFilePicker` and `FileSystemFileHandle`.
- **Logic:** Handled separately in `handleOpenLocal` and `handleSave` (bypasses `StorageProvider`).

#### 4. Image Pipeline
- **Flow:** `SVG` -> `Clone` -> `Style Injection (Computed)` -> `XMLSerializer` -> `Image` -> `Canvas`.
- **Critical:** Do not remove the style injection logic; it fixes "blank chart" issues in exports.

### HTML & CSS
- **Framework:** Tailwind CSS only. No custom CSS classes unless necessary for animation.
- **Modals:** Use `fixed inset-0 z-50` overlays with distinct IDs.

### Error Handling
- **Async:** Always wrap `await` in `try/catch`.
- **User Feedback:** Use `showNotification(msg, type)` ('error'|'success'|'info').

## Git Workflow
- **Branching:** Work on `main`.
- **Commits:** Conventional Commits (`feat:`, `fix:`, `refactor:`, `docs:`).

## Future Roadmap
1.  **Refactoring:** Split `index.html` into ES Modules (`src/main.js`, `src/storage/*.js`) to improve maintainability.
2.  **Plugin System:** Allow custom Mermaid directives or rendering logic.
