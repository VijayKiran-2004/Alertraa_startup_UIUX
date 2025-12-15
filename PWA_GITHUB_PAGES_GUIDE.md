# PWA & GitHub Pages Setup Guide

This guide explains how to configure your Alertraa app as a Progressive Web Application (PWA) and deploy it to GitHub Pages.

## What's New

Your Alertraa Expo app has been configured as a PWA with the following additions:

### Files Added:
- `web/public/manifest.json` - PWA manifest with app metadata
- `web/public/service-worker.js` - Service worker for offline support and caching
- `web/public/index.html` - HTML entry point for web build

### Files Modified:
- `package.json` - Added PWA build scripts and gh-pages dependency
- `app.json` - Enhanced web configuration for PWA

## Prerequisites

1. **Node.js & npm** installed (v16 or higher)
2. **Git** installed
3. A **GitHub account** with a repository for this project
4. (Optional) **GitHub CLI** for easier authentication

## Setup Instructions

### 1. Install Dependencies

```bash
cd AlertraaExpo
npm install
```

This installs the required packages including `gh-pages` for deployment.

### 2. Update Homepage in package.json

Edit the `homepage` field in [package.json](./package.json):

```json
"homepage": "https://YOUR_GITHUB_USERNAME.github.io/alertraa-app"
```

Replace `YOUR_GITHUB_USERNAME` with your actual GitHub username.

### 3. Create/Configure GitHub Repository

If you don't have a repository yet:

```bash
git init
git add .
git commit -m "Initial commit: Add PWA configuration"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/alertraa-app.git
git push -u origin main
```

### 4. Build the Web App

```bash
npm run web:build
```

This creates a `dist` directory with the optimized web build.

### 5. Deploy to GitHub Pages

#### Option A: Using npm script (Recommended)

```bash
npm run deploy
```

This automatically builds and deploys to GitHub Pages.

#### Option B: Manual deployment

```bash
npm run web:build
gh-pages -d dist
```

### 6. Configure GitHub Pages in Repository Settings

1. Go to your repository on GitHub
2. Click **Settings** → **Pages**
3. Under "Build and deployment":
   - Select **Deploy from a branch** (or **GitHub Actions** if you want CI/CD)
   - Select the **gh-pages** branch
   - Select **/ (root)** as the source folder
   - Click **Save**

4. Wait a few moments for the deployment to complete
5. Your app should be live at: `https://YOUR_GITHUB_USERNAME.github.io/alertraa-app`

## GitHub Actions Deployment (Optional - Recommended for CI/CD)

Create a file `.github/workflows/deploy.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x]

    steps:
      - uses: actions/checkout@v3

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'

      - name: Install dependencies
        run: npm install

      - name: Build web app
        run: npm run web:build

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
          cname: alertraa-app  # Optional: if using custom domain
```

## PWA Features

Your app now supports:

### 1. **Offline Support**
   - Service worker caches app assets
   - Continues to work when offline
   - Re-syncs when connection returns

### 2. **Installation**
   - Users can install as a native-like app
   - Appears in app drawer/home screen
   - Standalone display mode (fullscreen)

### 3. **App Metadata**
   - Custom app name and icon
   - Theme colors
   - Orientation settings

### 4. **Web Manifest**
   - Standard PWA manifest configuration
   - Share target support
   - App shortcuts

## Testing Locally

### Test Web Build

```bash
npm run web:build
npx serve -s dist -l 3000
```

Then visit: `http://localhost:3000/alertraa-app`

### Test Service Worker

1. Open DevTools (F12)
2. Go to **Application** tab → **Service Workers**
3. Verify the service worker is registered and running

### Install as PWA

1. Click the install icon in the browser URL bar (or use menu)
2. Choose "Install" or "Add to Home Screen"
3. The app will appear as an installed application

## Troubleshooting

### Service Worker Not Registering

- Verify HTTPS is being used (required for service workers in production)
- Check browser console for errors
- Clear browser cache and site data

### Build Fails

```bash
# Clear cache and reinstall
rm -rf node_modules dist
npm install
npm run web:build
```

### Deployment Issues

```bash
# Verify gh-pages is installed
npm list gh-pages

# Check git remote
git remote -v

# Force push if needed
gh-pages -d dist -m "Deploy update"
```

### App Shows Blank Page

1. Check browser console for errors
2. Verify homepage URL matches your GitHub Pages settings
3. Check that the `dist` directory exists and has content

## Development Workflow

### For Development (Live Reload)

```bash
npm start
# Select 'w' to run in web mode
```

### For Production

```bash
npm run web:build
npm run deploy
```

## Performance Tips

1. **Lazy Load Routes** - Use React lazy loading for better performance
2. **Optimize Images** - Ensure images are optimized before deployment
3. **Minify Assets** - Expo automatically minifies for production builds
4. **Monitor Bundle Size** - Keep web bundle under 5MB for fast loads

## Advanced Configuration

### Custom Domain

1. Update `app.json` web scope to your domain
2. Add CNAME file in `web/public/`:
   ```
   your-domain.com
   ```
3. Configure DNS records with your domain provider
4. Update GitHub Pages settings to use custom domain

### Service Worker Updates

The service worker uses cache version `alertraa-v1`. To force updates:

1. Increment the version: Change `CACHE_NAME` to `alertraa-v2`
2. Rebuild and deploy
3. Users will automatically get the new version on next visit

## Resources

- [Expo Web Documentation](https://docs.expo.dev/build-reference/web/)
- [PWA Documentation](https://web.dev/progressive-web-apps/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)

## Support

If you encounter issues:

1. Check the [Expo documentation](https://docs.expo.dev/)
2. Review [GitHub Pages troubleshooting](https://docs.github.com/en/pages/getting-started-with-github-pages/troubleshooting-common-issues-with-github-pages)
3. Check browser console and network tabs for errors
4. Verify your repository settings match the guide above
