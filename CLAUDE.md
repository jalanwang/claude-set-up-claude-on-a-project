# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express JSON API (users + health endpoints) that serves as the practice codebase for the Claude Code course.

## Commands

```bash
npm install
npm run dev                      # start on http://localhost:3000 with --watch reload
npm start                        # start without watch
npm test                         # node:test runner, discovers tests/
npm test -- tests/users.test.js  # run a single test file
npm run lint                     # eslint .
```

CI (`.github/workflows/ci.yml`) runs `npm install`, `npm run lint`, `npm test` on Node 22 for every push and PR — keep both green.

## Architecture

- `server.js` builds the app, mounts `express.json()` and one router per resource, and only calls `app.listen()` when `require.main === module`. That guard is what lets `tests/users.test.js` do `require("../server")` and drive the app through supertest without binding a port — don't move the listen call out of it.
- `routes/<resource>.js` exports an `express.Router()` mounted at `/<resource>` in `server.js`. Paths inside a router are relative to that mount point (`router.get("/:id")` serves `/users/:id`). A new resource means a new file plus one `app.use` line.
- `db/store.js` is an in-memory array behind named functions. It is the only module that touches user data; routes call `store.*` and never reach into the array. Data resets on restart, and tests depend on the two seeded users existing.

## Conventions

- CommonJS only (`require` / `module.exports`), not ESM `import`. There is no `"type": "module"` and no build step or TypeScript.
- Double-quoted strings, semicolons, 2-space indent — match the existing files; `npm run lint` is the arbiter.
- Route handlers validate first and return early: `return res.status(4xx).json({ error: "..." })`, then the success `res.json(...)`. Errors are always a JSON object with an `error` string, never a bare string or HTML.
- Coerce `req.params` with `Number(...)` before comparing to store ids — ids are numbers, params are strings.
- Tests use `node:test` + `node:assert` + supertest. No Jest, Mocha, or Chai.
- Keep dependencies as they are; don't add packages to solve something the standard library or Express already covers.

## Course project constraints

This repo is a course exercise about configuring Claude Code, not about the API. Unless asked explicitly, do not change the app code under `server.js`, `routes/`, `db/`, or `tests/` — the deliverables are `CLAUDE.md`, `.claude/settings.json`, and `NOTES.md`. Later course levels build on this same repo.

Real config lives in a git-ignored `.env`; `.env.example` is the committed template. Read the example, not `.env`.
