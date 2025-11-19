# GitHub Pages Setup Guide

## Prerequisites

- Repository pushed to GitHub
- GitHub Actions enabled in repository settings

## Setup Steps

### 1. Enable GitHub Pages

1. Go to your repository on GitHub: `https://github.com/endymuhardin/website-stmik`
2. Click **Settings** tab
3. Click **Pages** in the left sidebar
4. Under "Build and deployment":
   - **Source**: Select "GitHub Actions"
   - Leave other settings as default
5. Click **Save**

### 2. Push Changes

Push your commits to trigger the workflow:

```bash
git push github main
```

### 3. Monitor Deployment

1. Go to **Actions** tab in your repository
2. Watch the "Deploy Frontend to GitHub Pages" workflow
3. Once complete (green checkmark), your site will be live

### 4. Access Your Site

Your site will be available at:
```
https://endymuhardin.github.io/website-stmik
```

**Note:** The first deployment may take 5-10 minutes. Subsequent deployments are faster.

## Workflow Details

### Trigger Conditions

The workflow runs when:
- Code is pushed to `main` branch
- Changes are made to `frontend/**` directory
- Changes are made to the workflow file itself
- Manually triggered via "Run workflow" button in Actions tab

### Build Process

1. **Checkout code** from repository
2. **Setup Node.js 20** with npm caching
3. **Install dependencies** (`npm ci` in frontend/)
4. **Build Astro site** (`npm run build`)
5. **Upload artifact** (dist/ directory)
6. **Deploy to GitHub Pages**

### Configuration Files

- **Workflow**: `.github/workflows/deploy-frontend.yml`
- **Astro Config**: `frontend/astro.config.mjs`
  - `site`: `https://endymuhardin.github.io`
  - `base`: `/website-stmik`
- **Jekyll Bypass**: `frontend/public/.nojekyll`

## Troubleshooting

### Build Fails

1. Check the Actions tab for error logs
2. Test build locally: `cd frontend && npm run build`
3. Ensure all dependencies are in `package.json`
4. Check Node.js version compatibility

### 404 Errors

1. Verify GitHub Pages is enabled with "GitHub Actions" source
2. Check that `base: '/website-stmik'` is set in `astro.config.mjs`
3. Wait a few minutes after deployment completes
4. Clear browser cache

### Assets Not Loading

1. Ensure all asset paths use Astro's built-in path helpers
2. Check that `.nojekyll` file exists in `frontend/public/`
3. Verify `base` setting in `astro.config.mjs` matches repo name

### Permissions Error

If you see "Error: The process '/usr/bin/git' failed with exit code 128":

1. Go to **Settings** → **Actions** → **General**
2. Under "Workflow permissions", select:
   - ✅ **Read and write permissions**
3. Click **Save**

## Manual Deployment

To manually trigger a deployment:

1. Go to **Actions** tab
2. Select "Deploy Frontend to GitHub Pages"
3. Click **Run workflow** dropdown
4. Select `main` branch
5. Click **Run workflow** button

## Custom Domain (Optional)

To use a custom domain:

1. Add a `CNAME` file to `frontend/public/`:
   ```
   youruni.edu
   ```
2. Configure DNS at your domain provider:
   - Add CNAME record pointing to `endymuhardin.github.io`
3. Update `site` in `astro.config.mjs`:
   ```js
   site: 'https://youruni.edu',
   base: '/', // Remove base path for custom domain
   ```
4. Enable custom domain in GitHub Pages settings

## CI/CD Best Practices

### Branch Protection

Recommended settings for `main` branch:
- Require pull request reviews
- Require status checks to pass (build workflow)
- Require branches to be up to date

### Caching

The workflow uses npm caching to speed up builds:
```yaml
cache: 'npm'
cache-dependency-path: 'frontend/package-lock.json'
```

### Concurrent Deployments

The workflow prevents concurrent deployments:
```yaml
concurrency:
  group: "pages"
  cancel-in-progress: false
```

## Monitoring

### View Deployment Status

- **Latest deployment**: Actions tab → Latest workflow run
- **Site status**: Settings → Pages (shows last deployment time)
- **Build logs**: Click on workflow run → "build" job

### Deployment History

All deployments are tracked in:
- **Actions tab**: Full history of workflow runs
- **Deployments**: Repository homepage → Environments → "github-pages"

## Next Steps

After successful deployment:

1. ✅ Verify all pages load correctly
2. ✅ Test bilingual navigation (id/en)
3. ✅ Check responsive design on mobile
4. ✅ Validate SEO meta tags
5. ✅ Test sitemap.xml accessibility
6. ✅ Monitor Lighthouse scores

## Resources

- [Astro GitHub Pages Guide](https://docs.astro.build/en/guides/deploy/github/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
