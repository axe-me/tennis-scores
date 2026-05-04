# 🎾 Tennis Scoreboard

A professional tennis match umpire scoreboard — built as an installable PWA with Vue 3 and Vite.

## Features

### Scoring Engine
- **Standard tennis scoring** — 0, 15, 30, 40, Deuce, Advantage
- **Tiebreak scoring** — numeric points (first to 7, win by 2) with automatic server rotation
- **Set management** — tracks completed and in-progress sets with visual indicators

### Game Formats
| Format | Description |
|---|---|
| **Regular (6)** | Standard sets — first to 6 games, tiebreak at 6-6 |
| **Fast4 (4)** | Short sets — first to 4 games, tiebreak at 3-3 |
| **Pro Set (8)** | Single set — first to 8 games, tiebreak at 8-8 |

### Match Configuration
- **Match length** — Best of 3 or Best of 5 (hidden for Pro Set)
- **Deuce rule** — Advantage (standard) or No-Ad (sudden death at deuce)
- **Final set rule** — 7-point Tiebreak, 10-point Super Tiebreak, or Advantage (no tiebreak, win by 2 games)

### Umpire Controls
- **Serve indicator** — click to toggle server; only the serving player shows the 🎾 icon
- **Point buttons** — dedicated buttons per player
- **Undo** — full point-by-point history with state restoration
- **Reset** — clear the match and start fresh
- **Screenshot** — capture the scoreboard as a PNG with transparent background (save-as dialog on supported browsers)

### UI / UX
- Dark green tennis-court palette
- Scoreboard auto-sizes to content (no wasted space)
- Set columns appear only as needed
- Monospace scores for alignment
- Match status badge with live state indicator
- Winner banner with trophy on match completion

### PWA
- Installable on desktop and mobile (Add to Home Screen)
- Offline support via Workbox service worker
- Custom tennis ball app icon

## Tech Stack

- **[Vue 3](https://vuejs.org/)** — reactive UI
- **[Vite](https://vitejs.dev/)** — dev server and build tool
- **[vite-plugin-pwa](https://vite-pwa-org.netlify.app/)** — service worker and manifest generation
- **[html2canvas](https://html2canvas.hertzen.com/)** — scoreboard screenshot capture
- **Vanilla CSS** — custom design system with CSS variables

## Getting Started

```bash
# Install dependencies
npm install

# Start dev server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## Project Structure

```
tennis-scoreboard/
├── public/
│   ├── pwa-192x192.png       # PWA icon (192×192)
│   ├── pwa-512x512.png       # PWA icon (512×512)
│   └── apple-touch-icon.png  # iOS home screen icon
├── src/
│   ├── App.vue               # Main app — scoring logic + UI
│   ├── main.js               # Vue app entry point
│   └── style.css             # Full design system
├── index.html                # Shell HTML with meta tags
└── vite.config.js            # Vite + PWA plugin config
```

## License

MIT
