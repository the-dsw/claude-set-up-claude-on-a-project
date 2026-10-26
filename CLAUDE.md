# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express REST API (users + health check) backed by an in-memory store, used as the starter project for a Claude Code course.

## Commands

- `npm run dev` — start the API with `node --watch` on `http://localhost:3000` (override with `PORT`)
- `npm test` — run all tests with Node's built-in runner (`node --test`)
- `node --test tests/users.test.js` — run one test file
- `node --test --test-name-pattern="404"` — run tests whose name matches a pattern
- `npm run lint` — ESLint over the whole repo

CI (`.github/workflows/ci.yml`, Node 22) runs `npm run lint` then `npm test`; both must pass.

## Conventions

- Use CommonJS (`require` / `module.exports`), not ES modules — ESLint is configured with `sourceType: "script"`.
- Write tests with `node:test` + `node:assert` and `supertest`, not Jest or Mocha. Import `app` from `server.js` and call `request(app)`; never start a real server in tests.
- Route handlers access data only through functions exported from `db/store.js`; never keep data arrays inside route files.
- Errors are returned as JSON `{ error: "<message>" }` with the matching status code (400 for validation, 404 for not found); successful creates return 201.
- Route params arrive as strings — convert IDs with `Number(...)` before passing them to the store.

## Architecture

- `server.js` builds the Express app, mounts each router under its resource path (`/users`, `/health`), and exports `app`. It only calls `listen()` when run directly (`require.main === module`), which is what lets tests import it.
- `routes/` holds one `express.Router()` file per resource. A new resource means a new file here plus an `app.use(...)` line in `server.js`.
- `db/store.js` is an in-memory stand-in for a database: module-level state that resets on restart. Because it's shared module state, tests within the same file see each other's writes — don't rely on exact counts or IDs after a `POST`.

## Project scope

This repo is a course exercise: the deliverables are `CLAUDE.md`, `.claude/settings.json`, and `NOTES.md`. Don't change the app code unless explicitly asked. Real config lives in `.env` (git-ignored); `.env.example` shows the shape.
