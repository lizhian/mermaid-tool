# AGENTS.md

## Project Overview
This is a single-file Mermaid chart editor application written in HTML, CSS, and JavaScript. It functions as a client-side only application with no build system or external local dependencies (libraries are loaded via CDN).

## Architecture & State
- **Single File**: All logic, styling, and markup reside in `index.html`.
- **State Management**: A centralized `state` object manages application state:
  ```javascript
  const state = {
      tabs: [],           // Array of tab objects {id, name, code}
      activeTabId: null,  // Currently selected tab ID
      currentCode: '',    // Current editor content
      zoom: { level, translateX, translateY },
      pan: { isPanning, startX, startY },
      // ...
  };
  ```
- **Persistence**: IndexedDB (`MermaidEditorDB`) is used to persist tabs and state across reloads.
- **Rendering**: Mermaid.js renders the diagrams into an SVG within the preview area.

## Build/Lint/Test Commands
Since this is a no-build project, standard npm commands do not apply.

### Running the Application
- Open `index.html` directly in any modern web browser.
- Or serve locally: `python3 -m http.server 8000`

### Testing
- **Manual Testing**: Verify core features (Edit, Render, Zoom/Pan, Tabs, Export/Import) manually.
- **Console**: Check browser console for errors. Use `console.error` for catching and logging issues.

### Linting/Formatting
- **Style**: Mimic the existing code style.
- **Indentation**: 4 spaces for HTML structure, 4 spaces for JS logic (though mixing happens in HTML files, prefer consistency).
- **Quotes**: Single quotes for JS strings, double quotes for HTML attributes.

## Code Style Guidelines

### JavaScript Conventions
- **ES6+**: Use `const`/`let`, arrow functions, async/await, and template literals.
- **Naming**:
  - Variables/Functions: `camelCase` (e.g., `updateEditorUI`, `handleTabDrop`)
  - Constants: `UPPER_SNAKE_CASE` (e.g., `CONSTANTS.ZOOM_STEP`, `DB_NAME`)
  - HTML IDs: `kebab-case` (e.g., `mermaidEditor`, `zoomInBtn`)
- **Structure**:
  - Group related logic (e.g., `// --- State Management ---`, `// --- Initialization ---`).
  - Keep `init` functions at the top level of the script.
  - Event listeners are bound in `bindEvents()`.

### HTML/CSS
- **Tailwind CSS**: Use Tailwind utility classes for styling. Avoid inline `style="..."` unless for dynamic values (like drag resizing).
- **Layout**: Flexbox is heavily used for layout (`flex`, `flex-col`, `items-center`).
- **Icons**: FontAwesome 6 (CDN) for UI icons.
- **Layout Fixes**: Note `body { position: fixed; inset: 0; }` for preventing scroll bounce on mobile/Mac.

### Key Logic Patterns
- **Event Handling**:
  - Use `addEventListener` in `bindEvents`.
  - For complex interactions (Drag/Drop, Pan/Zoom), encapsulate logic in handler functions or modules.
  - **Pan/Zoom**: Unifies Mouse and Touch events. Zoom uses `transform: scale(...) translate(...)`.
- **Database**:
  - All DB operations return Promises.
  - Use `state` to hold data in memory, sync to DB on changes.

## Security & Best Practices
- **Input Handling**: Mermaid rendering handles some sanitization, but be cautious with user input.
- **CDN**: Ensure CDN links are reliable (cdn.tailwindcss.com, cdn.jsdelivr.net).
- **Error Handling**: Use `try-catch` blocks for async operations (rendering, DB, clipboard). Use `showNotification` or `showError` to inform the user.

## Git Workflow
- **Commits**: Use descriptive messages.
- **Branching**: `main` is the primary branch.
