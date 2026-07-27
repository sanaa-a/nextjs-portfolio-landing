# Final Report

## Scenario
A client wanted a fast, modern landing page to showcase a grid of portfolio items. Tired of slow, bloated WordPress sites and asking for a modern build instead.

## My method of making the website
1. Started the project with create-next-app (Next.js 16, App Router, TypeScript, Tailwind v4) so data fetching, routing, and styling all came from one modern toolchain rather than HTML/CSS/JS.
2. Built a data layer first (`src/lib/projects.ts`) that fetches from a public API and shapes the response into a typed `Project[]`, with a local fallback so the site never breaks if the API is unreachable.
3. Assembled the page from small components (Nav, Hero, Marquee, ProjectGrid, ProjectCard, Stack/Footer) so each section could be coded independently for changes.

## What was built
A single-page developer portfolio:
1. Nav — hexagonal logo mark, section links with an animated underline, social media icons.
2. Hero — a centered split headline around an SVG centerpiece: an asymmetric gradient blob split by a circuit trace, with a slow-spinning dashed ring and pulsing dots.
3. Marquee — a looping ticker strip with a mountain peaks style texture.
4. Project grid — nine case-study cards pairing a real project name with a live, real-English summary sentence and real likes/views numbers fetched from DummyJSON.
5. Cards use an asymmetric cut-corner shape, a gradient accent bar, and lift with a color block shadow on hover.
6. Stack strip — a tilted row of skills.
7. Footer — contact details.

## Architecture and data flow
The fetch happens inside a Server Component, not in the browser. There's no client-side loading state, and no API call visible in devtools.

`next: { revalidate: 3600 }` means the data silently refreshes at most once an hour in the background (Incremental Static Regeneration), so the page is served as static HTML on every request but never goes stale for long without a manual rebuild.

`npm run build` confirms this: the `/` route is listed as `○ (Static)` in the build output.

All styling lives in one place — CSS custom properties and utility classes defined once in `globals.css`, so colors, fonts, and spacing stay consistent across every component instead of getting redefined file by file.

## Why Next.js over plain HTML/CSS/JS or a standard React app
Plain HTML/CSS/JS would mean manually wiring up the fetch call, writing loading/error states by hand, and rebuilding a lot of small tooling (bundling, dev server, image optimization) that Next.js already gives out of the box.

A standard React app (e.g. Vite + React) gets you components and state management, but every fetch still has to run in the browser via `useEffect` — meaning a visible loading flicker, a client-side network request anyone can see in devtools, and no automatic caching. Next.js's Server Components let the fetch run before the page ever reaches the browser, so the user just gets a finished page instantly, with the network request completely invisible to them.

## How this framework handles routing and data fetching differently
Next.js's App Router uses **file-based routing** which is a folder inside `app/` automatically becomes a URL route, with no separate router library or config file to maintain (unlike plain React, where you'd need to install and manually configure something like React Router). This project only needed one page, so routing stayed simple, but the same file-based structure is what would let this scale into a multi-page site (e.g. `/about`, `/projects/[slug]`) without adding new dependencies.

For data fetching, the biggest difference is that Server Components can `fetch()` data directly inside the component itself, on the server, before any HTML is sent to the browser. Plain React has no equivalent. Data fetching there always happens client-side after the page has already loaded, which is why traditional React apps often show a loading spinner first. Next.js skips that step entirely.

## Conclusion
The end result is a fast-loading, statically-served portfolio page that still shows live data.
