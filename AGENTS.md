# AGENTS.md

## Project Overview
This is a sophisticated, single-file Serverless Mermaid Chart Editor. It allows users to create, edit, manage, and persist Mermaid diagrams entirely in the browser.

**Core Philosophy:**
- **Zero Build Step:** The application must run directly from `index.html` without `npm install` or compilation.
- **Client-Side Only:** No backend server. All persistence is handled via browser APIs (IndexedDB) or direct S3 API calls from the client.
- **Dependency Minimalism:** Libraries are loaded via reliable CDNs (Tailwind, Mermaid, AWS SDK, Diff.js).

## Environment & Setup

### Running the Application
Since there is no build step, "deployment" is simply serving the file.

1.  **Direct Open:** Double-click `index.html` to open in Chrome/Edge/Firefox.
2.  **Local Server (Recommended):**
    ```bash
    # Python 3
    python3 -m http.server 8000
    # PHP
    php -S localhost:8000
    ```
    Access at `http://localhost:8000`.

### Dependencies (CDN)
- **Styling:** Tailwind CSS (Script tag, v3.x via CDN)
- **Core Logic:** Mermaid.js (v10.x)
- **Remote Storage:** AWS SDK for JavaScript (v2.1001.0)
- **Diffing:** jsdiff (v5.1.0)

## Testing Strategy

There is no automated test suite. Testing is manual and feature-based.

### Manual Test Plans (Run these for verification)

**1. Core Rendering Test**
- **Action:** Open app, clear editor, paste `graph TD; A-->B;`.
- **Expectation:** Preview pane updates immediately (debounced) showing two nodes connected by an arrow. No console errors.

**2. S3 Integration Test**
- **Action:** Open Settings (`#s3ConfigModal`), enter valid AWS credentials/Bucket, save.
- **Action:** Click "Save" in footer.
- **Expectation:** Notification "Saved successfully". S3 bucket should contain the `.md` file.

**3. Image Export Test**
- **Action:** Click "Export Image" -> Select "PNG".
- **Expectation:** Browser downloads a `.png` file.
- **Verify:** Open the PNG. It should match the SVG preview exactly, *including* styles/colors. If the image is blank or missing styles, the `renderToCanvas` pipeline is broken.

**4. Offline Persistence Test**
- **Action:** Create a tab, type some content. Refresh the page (F5).
- **Expectation:** The tab and content remain exactly as they were (restored from IndexedDB).

## Code Style Guidelines

### JavaScript (`<script>` block)
- **Standard:** Modern ES6+. Use `const` for immutables, `let` for mutables. **Never** use `var`.
- **Async/Await:** Prefer `async/await` over raw Promises for readability, especially in storage operations.
- **Semicolons:** Always use semicolons.
- **Naming Conventions:**
    - **Variables/Functions:** `camelCase` (e.g., `updateEditorUI`, `handleTabDrop`).
    - **Constants:** `UPPER_SNAKE_CASE` (e.g., `CONSTANTS.ZOOM_STEP`, `DB_NAME`).
    - **Classes:** `PascalCase` (e.g., `S3StorageProvider`).
    - **HTML IDs:** `kebab-case` (e.g., `mermaid-editor`, `zoom-in-btn`).
- **Formatting:**
    - Indentation: 4 spaces.
    - Strings: Single quotes `'` preferred for JS, backticks \`\`\` for templates.
    - Braces: K&R style (open brace on same line).

### Architecture & Patterns

#### 1. State Management
All application state lives in the global `state` object.
```javascript
const state = {
    tabs: [],           // Array of {id, name, code}
    activeTabId: null,
    uiConfig: {},       // Persisted user settings (formats, etc)
    s3Config: {},       // AWS Credentials (sensitive)
    currentFile: {      // Metadata about the open file
        type: 'local' | 's3',
        key: 'path/to/file.md'
    }
};
```
*Rule:* Updates to `state` usually require a UI refresh function (e.g., `renderTabs()`, `updateFooterUI()`) to be called immediately after.

#### 2. Storage Abstraction (`StorageProvider`)
Do not make AWS calls directly in UI functions. Use the `remoteStorage` wrapper.
- **Pattern:** `remoteStorage.provider` is an instance of `StorageProvider` (currently `S3StorageProvider`).
- **Extension:** To add WebDAV, extend `StorageProvider` and implement `list`, `get`, `put`, `delete`.

#### 3. Image Generation Pipeline
The export logic (`renderToCanvas`) is critical and complex due to browser security constraints on `foreignObject`.
- **Flow:** `SVG DOM` -> `Clone` -> `Style Injection (Computed Styles)` -> `XMLSerializer` -> `Base64` -> `Image` -> `Canvas`.
- **Constraint:** Do not simplify this back to `html2canvas` without thorough testing of Gantt/Sequence charts, as they often break without explicit style injection.

### HTML & CSS
- **Framework:** Tailwind CSS only. No custom CSS classes unless absolutely necessary for animation.
- **Layout:** Flexbox is the primary layout tool (`flex`, `flex-col`, `justify-between`).
- **Modals:** Use `fixed inset-0 z-50` overlays. Ensure they have a distinct `id` for targeting.

### Error Handling
- **Async Operations:** Always wrap `await` calls in `try/catch`.
- **User Feedback:**
    - **Critical:** `showNotification(msg, 'error')` (Red toast).
    - **Success:** `showNotification(msg, 'success')` (Green toast).
    - **Info:** `showNotification(msg, 'info')` (Blue toast).
    - **Console:** Use `console.error` for debugging details, but always notify the user if an action failed.

## Git Workflow
- **Branching:** Work on `main`. Feature branches are optional for small teams.
- **Commit Messages:**
    - `feat:` New features (e.g., `feat: Add JPEG export support`).
    - `fix:` Bug fixes (e.g., `fix: Resolve blank canvas on export`).
    - `refactor:` Code restructuring (e.g., `refactor: Extract S3 logic to class`).
    - `docs:` Documentation updates.
    - `style:` Formatting changes.

## Future Roadmap (Agents: Keep this in mind)
1.  **WebDAV Support:** Implement `WebDAVStorageProvider`.
2.  **File System Access API:** Allow saving directly to local disk (Chrome-only feature).
3.  **Module Splitting:** Eventually split `index.html` into `main.js`, `storage.js`, etc., if size exceeds 5000 lines.
