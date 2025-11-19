# BASE_URL Path Concatenation Fix

## The Problem

**Symptom:** Logo images returned 404 errors with malformed URL:
```
https://endymuhardin.github.io/website-stmikimages/logo-stmik-tazkia.svg
                                    ^^^^^^^^^^^^^^^^
                                    Missing slash!
```

**Expected URL:**
```
https://endymuhardin.github.io/website-stmik/images/logo-stmik-tazkia.svg
                                            ^
                                            Slash needed here
```

## Root Cause

When using `import.meta.env.BASE_URL` in Astro with GitHub Pages subpath deployment:

```javascript
// astro.config.mjs
export default defineConfig({
  base: '/website-stmik',  // ← No trailing slash!
  // ...
});
```

The `BASE_URL` environment variable is set to `/website-stmik` **without a trailing slash**.

### Incorrect Code (BROKEN)

```astro
<img src={`${import.meta.env.BASE_URL}images/logo.svg`} />
```

**Result:**
```
/website-stmik + images/logo.svg = /website-stmikimages/logo.svg
                ❌ No separator!
```

### Correct Code (FIXED)

```astro
<img src={`${import.meta.env.BASE_URL}/images/logo.svg`} />
           Add explicit slash here ──────┘
```

**Result:**
```
/website-stmik + / + images/logo.svg = /website-stmik/images/logo.svg
                 ✅ Proper path!
```

## Files Fixed

All instances of `BASE_URL` concatenation now include explicit `/`:

1. **src/components/Header.astro**
   ```diff
   - src={`${import.meta.env.BASE_URL}images/logo-stmik-tazkia.svg`}
   + src={`${import.meta.env.BASE_URL}/images/logo-stmik-tazkia.svg`}
   ```

2. **src/components/Footer.astro**
   ```diff
   - src={`${import.meta.env.BASE_URL}images/logo-stmik-tazkia.svg`}
   + src={`${import.meta.env.BASE_URL}/images/logo-stmik-tazkia.svg`}
   ```

3. **src/layouts/BaseLayout.astro**
   ```diff
   - href={`${import.meta.env.BASE_URL}images/logo-stmik-tazkia.svg`}
   + href={`${import.meta.env.BASE_URL}/images/logo-stmik-tazkia.svg`}
   ```

4. **src/pages/index.astro**
   ```diff
   - src={`${import.meta.env.BASE_URL}images/logo-stmik-tazkia.svg`}
   + src={`${import.meta.env.BASE_URL}/images/logo-stmik-tazkia.svg`}
   ```

5. **src/pages/en/index.astro**
   ```diff
   - src={`${import.meta.env.BASE_URL}images/logo-stmik-tazkia.svg`}
   + src={`${import.meta.env.BASE_URL}/images/logo-stmik-tazkia.svg`}
   ```

## Verification

### Before Fix
```bash
$ cat dist/index.html | grep logo
src="/website-stmikimages/logo-stmik-tazkia.svg"
```

### After Fix
```bash
$ cat dist/index.html | grep logo
src="/website-stmik/images/logo-stmik-tazkia.svg"
```

## Best Practice Pattern

For Astro projects with `base` configuration, always use:

```astro
---
// For public assets in public/ directory
const assetPath = (path: string) => `${import.meta.env.BASE_URL}/${path}`;
---

<!-- In template -->
<img src={assetPath('images/logo.svg')} />
```

Or inline:
```astro
<img src={`${import.meta.env.BASE_URL}/images/logo.svg`} />
              Always include slash here ──┘
```

## Why This Happened

1. Initial implementation worked in **local development** because `base` is not set
2. Local dev server serves from root: `http://localhost:4321/`
3. Production serves from subpath: `https://endymuhardin.github.io/website-stmik/`
4. Without slash, string concatenation fails silently
5. Only caught when testing against deployed site

## Prevention

1. **Always test builds locally:**
   ```bash
   npm run build
   npm run preview
   # Visit http://localhost:4321/website-stmik/
   ```

2. **Use Playwright tests:**
   ```bash
   npx playwright test --grep "images"
   ```

3. **Create helper function:**
   ```typescript
   // src/utils/assets.ts
   export const asset = (path: string) => {
     const base = import.meta.env.BASE_URL;
     // Ensure exactly one slash between base and path
     return `${base}/${path.replace(/^\//, '')}`;
   };
   ```

## Related Issues

- ✅ Fixed: Image 404 errors
- ✅ Fixed: Console errors from failed resource loading
- ✅ Fixed: Favicon not displaying
- ✅ Fixed: Logo not showing in Header
- ✅ Fixed: Logo not showing in Footer

## Commit

**Hash:** `1ea15f2`
**Message:** Fix BASE_URL concatenation - add missing slash separator
**Date:** 2025-11-19
