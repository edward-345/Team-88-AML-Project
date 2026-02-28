# GitHub Pages Quick Start

## Quick Deployment Steps

1. **Enable GitHub Pages:**
   - Go to your repository → **Settings** → **Pages**
   - Under **Source**, select **GitHub Actions**
   - Save

2. **Push your code:**
   ```bash
   git add .
   git commit -m "Add GitHub Pages deployment"
   git push origin main
   ```

3. **Wait for deployment:**
   - Go to **Actions** tab
   - Watch the workflow complete
   - Your site will be at: `https://[username].github.io/[repo-name]/`

## That's it! 🎉

The workflow automatically:
- ✅ Detects your repository name
- ✅ Sets the correct base path
- ✅ Builds your app
- ✅ Deploys to GitHub Pages

## Manual Base Path (if needed)

If you need to override the automatic base path, edit `vite.config.js`:

```javascript
base: '/your-repo-name/',  // Replace with your repo name
```

Or set it to `/` for custom domains or root deployments.

## Troubleshooting

- **404 errors?** Check that the base path matches your repo name
- **Build fails?** Check the Actions tab for error logs
- **Assets not loading?** Verify the base path is correct

For more details, see [DEPLOYMENT.md](./DEPLOYMENT.md)
