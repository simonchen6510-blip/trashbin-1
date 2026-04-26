# 听雨集

录下雨声，把声音特征转成水墨水彩风格的雨日记。

## Tech Stack

- Vite + React + TypeScript
- Express API server
- Gemini image generation through a server-side API
- IndexedDB local album storage

## Local Setup

1. Install dependencies:

   ```bash
   npm install
   ```

2. Create a local env file:

   ```bash
   cp .env.example .env.local
   ```

3. Put your Gemini key in `.env.local`:

   ```bash
   GEMINI_API_KEY="your_key_here"
   ```

   `.env.local` is ignored by git. Do not commit real API keys.

4. Run the app:

   ```bash
   npm run dev
   ```

   Frontend: `http://localhost:3000`

   API: `http://localhost:8787`

## Production

Build the frontend and server:

```bash
npm run build
```

Run the compiled server:

```bash
npm start
```

In production, set `GEMINI_API_KEY` as a server environment variable in your hosting platform. The frontend never reads the key directly.

## Security Notes

- Keep `GEMINI_API_KEY` only on the server.
- Do not commit `.env.local`, `dist`, `dist-server`, screenshots, or Playwright artifacts.
- If a key was ever pasted into chat, committed, or exposed in browser code, rotate it before publishing.
