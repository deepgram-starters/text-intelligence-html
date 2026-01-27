# Text Intelligence HTML Frontend

Pure HTML/CSS/JavaScript frontend for [Deepgram's Text Intelligence API](https://developers.deepgram.com/docs/text-intelligence).

## Features

- 📄 Analyze text with AI-powered intelligence
- 🔍 Sentiment analysis, topic detection, and entity recognition
- 📊 Summarization and intent classification
- 🎛️ Configurable intelligence features
- 📈 Visual display of analysis results
- 🎨 Built with [Deepgram Design System](https://github.com/deepgram/design-system)
- 🚀 No framework dependencies - pure vanilla JavaScript

## Prerequisites

- Node.js 14.0.0+
- pnpm 10.0.0+
- A backend server that implements the Text Intelligence API endpoint

## Backend Requirements

This frontend requires a backend server that provides:

1. **HTTP POST endpoint** at `/analyze` that:
   - Accepts JSON with text to analyze
   - Sends request to Deepgram's Text Intelligence API
   - Returns analysis results

2. **HTTP endpoint** at `/metadata` (optional) that returns:
   ```json
   {
     "title": "Your App Title",
     "description": "Your app description",
     "repository": "https://github.com/your-org/your-repo"
   }
   ```

See the [Node.js Text Intelligence starter](https://github.com/deepgram-starters/node-text-intelligence) for a complete backend implementation example.

## Quickstart

### Install Dependencies

```bash
pnpm install
```

### Development Mode

**Option 1: With Backend (Recommended)**

When using with a backend like [node-text-intelligence](https://github.com/deepgram-starters/node-text-intelligence):

- **Access the app at:** `http://localhost:8080` (backend port)
- The backend proxies to Vite on port 5173 for HMR
- Users should NEVER access `http://localhost:5173` directly

The frontend's `vite.config.js` has `strictPort: true`, so Vite will fail if port 5173 is in use rather than switching to an alternative port (which would break the backend proxy).

**Option 2: Standalone (Development Only)**

To run the frontend standalone for UI development:

```bash
pnpm dev
```

This runs on `http://localhost:5173` and proxies `/analyze` and `/metadata` requests to `http://localhost:8080`.

To change the backend URL, edit `vite.config.js`:

```javascript
proxy: {
  '/analyze': {
    target: 'http://localhost:YOUR_BACKEND_PORT',
  },
}
```

### Build for Production

```bash
pnpm build
```

Outputs to `dist/` directory. Serve these static files from your backend.

### Preview Production Build

```bash
pnpm preview
```

## Integration Patterns

### Pattern 1: Backend Proxies to Frontend (Development)

**Best for:** Active development with HMR (used by node-text-intelligence)

- Backend: `http://localhost:8080` ← **Users access this URL only**
- Frontend (Vite): `http://localhost:5173` (internal, proxied by backend)
- Backend proxies all requests to Vite for HMR
- Vite proxies API routes (`/analyze`, `/metadata`) back to backend

### Pattern 2: Backend Serves Frontend (Production)

**Best for:** Production deployment

1. Build frontend: `pnpm build`
2. Copy `dist/*` to backend's static files directory
3. Backend serves both static files and API endpoints

Example with Express:

```javascript
import express from 'express';
import path from 'path';

const app = express();

// Serve static files
app.use(express.static(path.join(__dirname, 'dist')));

// API routes
app.get('/metadata', (req, res) => { /* ... */ });
app.post('/analyze', (req, res) => { /* ... */ });
```

### Pattern 3: CDN + Backend API (Advanced)

**Best for:** Global scale, edge deployment

1. Build frontend: `pnpm build`
2. Deploy `dist/*` to CDN (Cloudflare, Vercel, etc.)
3. Update API endpoints in `main.js` to point to backend
4. Configure CORS on backend

## Configuration

### Intelligence Features

Located in the left sidebar:

- **Sentiment**: Analyze emotional tone
- **Topics**: Detect key topics and themes
- **Intents**: Classify user intent
- **Entities**: Extract named entities (people, places, organizations)
- **Summarization**: Generate text summaries
- **Language**: Detect language

Features can be selected before analysis.

### Environment Variables

Set `VITE_PORT` to change dev server port:

```bash
VITE_PORT=3000 pnpm dev
```

## Architecture

```
text-intelligence-html/
├── index.html          # Main HTML structure
├── main.js            # All JavaScript logic
├── vite.config.js     # Vite configuration
└── package.json       # Dependencies

main.js modules:
├── State Management   # API, config state
├── DOM Initialization # Element references, event listeners
├── Metadata Loading   # Fetch /metadata endpoint
├── API Layer          # HTTP requests to backend
├── Results Display    # Render analysis results
└── UI Updates         # Status indicators, loading states
```

## API Reference

### HTTP Request (Client → Server)

**POST /analyze** - Analyze text
```javascript
{
  text: "Your text to analyze...",
  features: {
    sentiment: true,
    topics: true,
    intents: true,
    entities: true,
    summarize: true,
    detect_language: true
  }
}
```

### HTTP Response (Server → Client)

**Analysis Results**
```javascript
{
  results: {
    sentiment: {
      polarity: "positive",
      score: 0.85
    },
    topics: ["technology", "innovation"],
    intents: ["information_request"],
    entities: [
      { type: "PERSON", text: "John Doe" },
      { type: "ORGANIZATION", text: "Deepgram" }
    ],
    summary: "Brief summary of the text...",
    language: {
      detected: "en",
      confidence: 0.99
    }
  }
}
```

**Error Response**
```javascript
{
  error: "Error message",
  code: "ERROR_CODE"
}
```

## Customization

### Styling

Uses Deepgram Design System CSS custom properties:

```css
/* Override in index.html <style> */
:root {
  --dg-primary: #13ef95;
  --dg-background: #0b0b0c;
  --dg-charcoal: #1a1a1f;
}
```

### Adding Features

The code is organized into clear sections in `main.js`:

1. **State Management** - Add new state properties
2. **DOM Elements** - Add new element references
3. **Event Listeners** - Add new interactions
4. **API Handlers** - Add new request/response handling

## Troubleshooting

### API request fails

- Ensure backend is running on correct port
- Check `vite.config.js` proxy configuration
- Verify backend implements `/analyze` endpoint

### Results not displaying

- Check browser console for JavaScript errors
- Verify API response format matches expected structure
- Ensure results container elements exist in DOM

### Features not working

- Verify API key is valid and has Text Intelligence enabled
- Check that feature flags are correctly sent to backend
- Ensure backend properly forwards features to Deepgram API

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines.

## Code of Conduct

This project follows the [Deepgram Code of Conduct](./CODE_OF_CONDUCT.md).

## Security

For security policy and procedures, see [SECURITY.md](./SECURITY.md).

## License

MIT - See [LICENSE](./LICENSE)

## Related Projects

- [Node Text Intelligence Starter](https://github.com/deepgram-starters/node-text-intelligence) - Complete Node.js backend + this frontend
- [Deepgram Text Intelligence API Docs](https://developers.deepgram.com/docs/text-intelligence)
- [Deepgram Design System](https://github.com/deepgram/design-system)

## Getting Help

- [Open an issue](https://github.com/deepgram-starters/text-intelligence-html/issues/new)
- [Deepgram Discord Community](https://discord.gg/xWRaCDBtW4)
- [Deepgram GitHub Discussions](https://github.com/orgs/deepgram/discussions)
