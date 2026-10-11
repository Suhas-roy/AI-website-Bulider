# Sitecraft — implementation and design

## Product direction

Build an AI website studio where a person describes the site they want in natural language. The studio will turn that brief into a page hierarchy, tailored copy, visual system and section layouts; show the result in an interactive preview; and accept conversational requests for targeted revisions. The original brief, current website and revisions stay together in a locally saved project workspace.

## Implementation approach

- **Frontend:** dependency-free HTML, CSS and browser JavaScript. A split studio workspace holds the prompt and conversation on the left and a website canvas on the right. Preview controls switch between desktop, tablet and mobile widths, select generated pages, and toggle a hierarchy/structure view.
- **Backend:** Node.js built-in HTTP server on the configured port (3000 by default). `POST /api/generate` accepts JSON generation or refinement requests, rejects cross-site browser calls, bounds input, and limits AI use to 8 requests per client per 15 minutes and 60 requests per server per hour. It calls the project's built-in Manus chat-completions endpoint with server-only credentials, parses the JSON result, and normalizes it before returning it. The browser never receives a platform API key.
- The limiter is process-local and is intended as preview abuse protection. A future multi-instance public release should add authenticated user identity and a durable shared quota store rather than relying on per-process counters.
- **Generation contract:** request a complete site object containing its name, audience/category, brand palette and up to four pages. Each page carries its purpose, hero content and reusable typed sections; the system prompt requires finished, brief-specific copy rather than filler. Refinements receive the current site and the new instruction and return a complete updated object.
- **Preview:** render normalized page data into an escaped `srcdoc` document in a sandboxed iframe. The page picker chooses among generated routes; the structure tab exposes page purpose and section hierarchy. Site text is escaped and CSS colors are validated before rendering.
- **Workspace persistence:** save the original brief, latest brief, active generated object, selected preview state and bounded revision history in browser `localStorage`. This keeps a user's work together without requiring accounts or a shared database.
- **Serving:** serve `public/` and the `/api` endpoints from the same origin. Include `/manus-routes.json`, a health endpoint, a pinned Node/npm runtime, and a small container contract for the server-backed app.

## Project structure

- `server/index.js` — HTTP server, static assets, health route, AI request, output validation and normalization.
- `public/index.html` — accessible studio shell and controls.
- `public/styles.css` — responsive application styling and preview-device layout.
- `public/app.js` — workspace state, prompt/refinement interactions, revision persistence, structure view and safe site rendering.
- `public/manus-routes.json` — declared page route manifest for the studio.
- `package.json`, `package-lock.json`, `Dockerfile` — runtime and reproducible server deployment metadata.
- `app.config.ts` — durable project logo metadata.
- `TODO.md` — outcome criteria carried from the request and saved product scope.

## Design direction

- **Design movement:** a restrained Swiss editorial workspace crossed with a contemporary creative-coding instrument panel: precise alignment, clear hierarchy and one confident expressive signal.
- **Core principles:** make the brief the starting point; keep the generated site visually primary; make hierarchy and iteration legible; distinguish agent activity from user-authored project content.
- **Color philosophy:** a deep ink chassis communicates focus, while warm near-white canvas surfaces make websites feel tangible. Acid-lime is the ownable action signal; lavender is reserved for the AI's generative state rather than used as decoration.
- **Layout paradigm:** an asymmetric, three-part studio—compact tool rail, a bounded brief/conversation column, and a broad canvas. Preview mode gives the output room; structure mode turns the canvas into an editorial page map.
- **Signature elements:** a lime orbital mark; a thin, ruled canvas frame with a subtle drafting-grid background; and small “live / revision” status labels that make change visible without noise.
- **Interaction philosophy:** one primary action follows the work's state (generate first, refine after). Example briefs are one-click starting points. Every AI action is acknowledged, can fail without erasing the brief, and becomes a selectable revision.
- **Animation:** short 140–200 ms opacity/transform transitions for controls and panel changes; a restrained pulse only while generation is running; no layout-jumping motion; honor `prefers-reduced-motion`.
- **Typography system:** Inter/system sans for interface labels and controls; a restrained Georgia-style editorial serif for generated-site hero headlines. Use compact uppercase labels for metadata and generous line height for generated copy.
- **Brand essence:** “A clear brief in, a buildable direction out” — for founders, marketers and independent creators who need a considered website concept without a blank-canvas stall. Personality: **clear, inventive, composed**.
- **Brand voice:** confident and practical, with specific verbs and no inflated promises. Example lines: “Give the idea a shape.” “A first draft you can actually react to.”
- **Wordmark & logo:** a custom open orbital ring with a short lime spark cutting through its upper-right edge, paired with a quiet uppercase SITECRAFT wordmark.
- **Signature brand color:** acid-lime `#D7F36A`.
