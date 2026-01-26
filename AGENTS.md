# AGENTS.md

## Project Overview
This is a single-file Mermaid chart editor application written in HTML, CSS, and JavaScript. It's a client-side only application with no build system or dependencies.

## Build/Lint/Test Commands

### Running the Application
```bash
# Simply open the HTML file in a browser
open index.html

# Or serve with a local server (optional)
python3 -m http.server 8000
# Then visit http://localhost:8000
```

### No Build System
This project does not use a build system. All code is contained in `index.html`.

### No Testing Framework
No test framework is currently configured. When adding tests, consider:
- Using Jest or Vitest for unit tests
- Using Playwright or Cypress for E2E tests
- Testing DOM manipulation and event handlers

### No Linting/Formatting
No linting or formatting tools are configured. Consider adding:
- ESLint for JavaScript linting
- Prettier for code formatting
- HTMLHint for HTML validation

## Code Style Guidelines

### JavaScript Conventions
- Use `const` for variables that don't change, `let` for those that do
- Use camelCase for variable and function names
- Use kebab-case for HTML element IDs
- Use async/await for asynchronous operations
- Use template literals for string interpolation
- Use arrow functions for callbacks and short functions
- Use try-catch blocks for error handling

### Naming Conventions
- Variables: `camelCase` (e.g., `zoomLevel`, `renderCounter`)
- Functions: `camelCase` (e.g., `renderMermaid`, `zoomIn`)
- Constants: `UPPER_SNAKE_CASE` (e.g., `ZOOM_STEP`, `MIN_ZOOM`)
- HTML IDs: `kebab-case` (e.g., `mermaidEditor`, `zoomInBtn`)
- CSS Classes: `kebab-case` (e.g., `code-editor`, `line-numbers`)

### Event Handling
- Use `addEventListener` for event binding
- Use `e.preventDefault()` to prevent default behavior when needed
- Use `e.stopPropagation()` to stop event bubbling when needed
- Clean up event listeners when removing elements
- Use event delegation for dynamically added elements

### Error Handling
- Always wrap async operations in try-catch blocks
- Show user-friendly error messages via `showNotification()`
- Log errors to console for debugging
- Provide fallback UI when operations fail

### DOM Manipulation
- Cache DOM queries when possible (e.g., store element references)
- Use `querySelector` and `getElementById` for element selection
- Update UI state immediately after data changes
- Use `classList` methods for class manipulation
- Avoid inline styles when possible (use Tailwind classes instead)

### Styling Guidelines
- Use Tailwind CSS for styling (loaded via CDN)
- Use Font Awesome for icons (loaded via CDN)
- Custom CSS goes in the `<style>` tag
- Use semantic HTML elements
- Maintain responsive design principles
- Use consistent color scheme (primary: #6366f1, secondary: #10b981)

### Code Organization
- Keep related functions together
- Group constants at the top of the script
- Initialize components in `DOMContentLoaded` event
- Separate concerns: UI updates, business logic, event handlers
- Use descriptive function and variable names

### Comments and Documentation
- Use Chinese for comments and UI text (existing pattern)
- Comment complex logic and algorithms
- Document function parameters and return values
- Keep comments concise and relevant

### Performance Considerations
- Use `setTimeout` for debouncing user input (500ms delay)
- Minimize DOM reflows and repaints
- Use event delegation where appropriate
- Clean up unused resources (e.g., revokeObjectURL)
- Consider using `requestAnimationFrame` for animations

### Browser Compatibility
- Modern browser features are used (ES6+)
- Consider adding polyfills if supporting older browsers
- Test in Chrome, Firefox, Safari, and Edge
- Use feature detection when using newer APIs

### Security Best Practices
- Set `crossOrigin='anonymous'` for external images
- Use `securityLevel: 'loose'` for Mermaid (necessary for features)
- Validate user input before rendering
- Sanitize HTML when inserting user content
- Avoid using `innerHTML` with untrusted content

### Accessibility
- Use semantic HTML elements
- Provide text alternatives for icons
- Ensure keyboard navigation works
- Use ARIA labels where appropriate
- Maintain sufficient color contrast

### Testing Guidelines
When adding tests:
- Test core functions in isolation
- Test event handlers with simulated events
- Test error scenarios and edge cases
- Test responsive behavior
- Verify cross-browser compatibility

### Git Workflow
- Commit changes with clear, descriptive messages
- Use Chinese for commit messages (consistent with code)
- Break large changes into smaller, logical commits
- Review changes before committing
- Tag releases appropriately