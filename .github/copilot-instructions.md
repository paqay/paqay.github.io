# Copilot Instructions for paqay.github.io

## Project Overview

This is the official portfolio website for **paqay**, an electronic music producer and rapper from Vienna. The site is hosted on GitHub Pages and features a glassmorphic UI with dynamic background videos and immersive visual effects.

## Architecture

### Single-File HTML Pages
Each page is self-contained with **embedded CSS and JavaScript** - no separate stylesheets or JavaScript files. This simplifies deployment on GitHub Pages and ensures all styles/logic travel together.

- **index.html** - Main landing page with video background switcher, social icons, and interactive elements
- **Drumkit.html** - Download page for the 1020_PACK drumkit with video background
- **TICKETS.html** - Event tickets sticker page
- **FLAKTAPE.html** - Multi-page project portfolio with releases
- **Releases/** - Individual HTML files for each music release
- **404.html** - Custom error page

### Video System
- **Dynamic loading**: Uses GitHub API to fetch .mp4 files from `media/videos/` directory
- **Fallback videos**: Hardcoded array for offline access if GitHub API fails
- **Caching**: Video source list cached in localStorage for 6 hours
- **Preloading**: Upcoming videos preloaded to minimize lag during playback

### Audio Architecture
- **Web Audio API**: Creates AudioContext with BiquadFilter for lo-fi lowpass filtering (cutoff: 800Hz, Q: 1)
- **Cross-fade**: Smooth transitions between videos with synchronized audio gain changes
- **Mute state**: Persisted per session, controlled by 'm' key or UI button

### Media Organization
```
media/
├── videos/           # MP4 footage (loaded dynamically)
├── icons/            # SVG and PNG social icons
├── paqay_tag inverted.svg
└── [audio, images, PDFs]
```

## Key Conventions

### CSS & Styling
- **Glass morphism**: Heavy use of `backdrop-filter: blur()` and `saturate()` with `-webkit-` prefixes for browser compatibility
- **CSS Custom Properties**: `--bg-blur` and `--bg-brightness` adjusted dynamically for performance profiles
- **Mix-blend-mode**: Frequent use of `mix-blend-mode: difference` for visual effects
- **Responsive breakpoints**: `768px` (tablet) and `480px` (mobile) media queries
- **Animations**: Keyframe animations use `ease-in-out` timing, often with staggered delays (1s, 1.2s, etc.)

### JavaScript Patterns
- **Google Analytics 4**: Installed on all pages with Measurement ID `G-E09V5RMZHJ`
  - GA4 script: `<script async src="https://www.googletagmanager.com/gtag/js?id=G-E09V5RMZHJ"></script>`
  - Located in `<head>` section of every HTML file for immediate tracking
  - Dashboard: https://analytics.google.com
- **Event tracking**: Three-fallback system
  ```javascript
  if (typeof window.gtag === 'function')  // Google Analytics
  if (typeof window.plausible === 'function')  // Plausible Analytics
  localStorage.setItem('paqay_metrics', ...)  // Fallback to localStorage
  ```
- **Safe DOM queries**: Always check element existence before manipulating
- **Error handling**: Try/catch blocks around localStorage, JSON.parse, and API calls
- **Performance optimization**: Lazy-load images with `loading="lazy" decoding="async"`
- **Keyboard shortcuts**:
  - `m` - toggle mute
  - `Space` - switch to next video
  - `ArrowRight` / `ArrowLeft` - navigate videos forward/backward
  - `Escape` - close menus

### Video Handling
- URLs for videos stored relative to site root: `media/videos/filename.mp4`
- Always wrap video switching in `isSwitchingVideo` flag to prevent overlapping requests
- Sync video playback time with session elapsed time for continuity: `elapsedTime % videoDuration`
- Handle "Autoplay blocked" errors gracefully with `.catch()`

### Accessibility
- Use semantic HTML where possible (`role="status"`, `aria-label`, `aria-expanded`)
- Hidden SEO text with `.visually-hidden` class (positioned off-screen but readable)
- Keyboard navigation for menus (Tab, Enter, Escape)

### URL Parameters
- Respond to `utm_source=ig` - hides Instagram icon when accessed from Instagram

## Common Tasks

### Adding a New Page
1. Create `.html` file in root directory
2. Use same SEO structure: meta tags, Open Graph, Twitter Card
3. Embed all CSS in `<style>` tag and JavaScript in `<script>` tag
4. Import media with relative paths: `media/filename`

### Modifying Video System
- Video fallback list: Search for `let videoSources = [` in index.html
- Cache expiry: `VIDEO_CACHE_MAX_AGE_MS = 6 * 60 * 60 * 1000` (6 hours)
- API endpoint: `https://api.github.com/repos/paqay/paqay.github.io/contents/media/videos`

### Styling Tips
- Use CSS variables for responsive adjustments (e.g., `--bg-blur`)
- Always test with `@media (max-width: 480px)` for mobile appearance
- Glass morphism pattern:
  ```css
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(15px) saturate(180%);
  -webkit-backdrop-filter: blur(15px) saturate(180%);
  border: 1px solid rgba(255, 255, 255, 0.2);
  ```

### Performance Profiles
The site detects low-power devices and adjusts blur/brightness:
- Data saver mode: `navigator.connection.saveData`
- Low memory: `navigator.deviceMemory <= 4`
- Small screens: `max-width: 768px`

When any condition is true, blur reduces to `8px` and brightness to `0.98`.

### Analytics & Tracking
- **GA4 Dashboard**: Access at https://analytics.google.com (Measurement ID: G-E09V5RMZHJ)
- **Local Analytics Dashboard**: `/analytics.html` - displays localStorage metrics and GA4 status
- **Adding GA4 to New Pages**: Include this in `<head>`:
  ```html
  <!-- Google Analytics 4 -->
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-E09V5RMZHJ"></script>
  <script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());
    gtag('config', 'G-E09V5RMZHJ');
  </script>
  ```
- **Custom Events**: Track user interactions using existing `trackEvent()` function in index.html

## No Build, Test, or Lint

This is a static site with **no build process**, test suite, or linter. Changes are deployed directly to GitHub Pages via git push.

## Comments Convention

Code comments are in **German** (native to the artist). Technical comments may be in English for clarity.
