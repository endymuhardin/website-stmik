# UI Fixes Documentation

## Typography Styling for Lecturer Profiles (2025-11-19)

### Issue
Lecturer profile pages (e.g., `/lecturers/endy-muhardin/`) had broken typography styling. The markdown content displayed without proper formatting - no spacing between paragraphs, no heading styles, no link colors, etc.

### Root Cause
The lecturer profile template used Tailwind Typography plugin classes (`prose`, `prose-lg`) but the plugin was not installed or configured.

**File:** `frontend/src/pages/lecturers/[slug].astro:150`
```astro
<div class="prose prose-lg max-w-none">
  <Content />
</div>
```

### Solution

#### 1. Install Tailwind Typography Plugin
```bash
npm install @tailwindcss/typography --save-dev
```

#### 2. Configure Plugin for Tailwind CSS v4
In Tailwind CSS v4, plugins are loaded using the `@plugin` directive in CSS files, **not** in `tailwind.config.mjs`.

**File:** `frontend/src/styles/global.css`
```css
@import "tailwindcss";
@plugin "@tailwindcss/typography";

@theme {
  /* ... theme variables ... */
}
```

#### 3. Rebuild
```bash
npm run build
```

### Verification
After the fix:
- CSS file size increased from 24KB to 41KB
- 477 prose-related CSS rules added
- Markdown content now displays with proper typography (headings, paragraphs, lists, links, blockquotes)

### Important Notes for Tailwind CSS v4

**DO NOT** add plugins to `tailwind.config.mjs`:
```javascript
// ❌ Wrong (v3 syntax)
export default {
  plugins: [
    require('@tailwindcss/typography'),
  ],
};
```

**DO** add plugins using `@plugin` in CSS:
```css
/* ✅ Correct (v4 syntax) */
@import "tailwindcss";
@plugin "@tailwindcss/typography";
```

### Related Files
- Template: `frontend/src/pages/lecturers/[slug].astro`
- English Template: `frontend/src/pages/en/lecturers/[slug].astro`
- Global Styles: `frontend/src/styles/global.css`
- Config: `frontend/tailwind.config.mjs`

### References
- [Tailwind CSS v4 Plugin Configuration](https://github.com/tailwindlabs/tailwindcss/discussions/15904)
- [Tailwind Typography Plugin](https://github.com/tailwindlabs/tailwindcss-typography)
