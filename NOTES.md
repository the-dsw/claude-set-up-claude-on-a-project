# Notes

## CLAUDE.md: what's in it and what's left out

It has a one-line description, the commands (`dev`, `test`, `lint`, plus how to run a single test file or a single test by name), conventions, and a short architecture section. The conventions are the rules Claude would otherwise get wrong by guessing: CommonJS instead of ES modules, `node:test` + `supertest` instead of Jest, data access only through `db/store.js`, and the JSON `{ error }` response shape. The architecture section covers things you only see after reading several files. `server.js` only calls `listen()` when run directly, which is why tests can import `app`. The store is shared module state, so tests in the same file affect each other.

I left out a file-by-file listing, the route list, and Express basics because Claude can read those straight from the code, and they would go stale. I also left out the course setup steps from the README (they're one-off, not something needed every session) and anything from `.env`. The file only says that real config lives there.

## Permission rules

- **Allow:** `npm test`, `npm run lint`, `node --test`. These are safe, read-only checks that I run constantly, so prompting for them every time would just slow things down. I didn't allow `npm run dev` because it starts a server that never exits.
- **Ask:** `git push`, because a push publishes work, and I want to confirm each one.
- **Deny:** reading `.env`, and `git push --force` / `-f`. I didn't block `.env.*` because that would also hide `.env.example`, which is safe and useful.

Without the deny rules, Claude could read `.env` while debugging config and copy a real secret into its output, a commit, or a log. It could also force-push to "fix" a rejected push and overwrite someone else's commits on the remote. Neither is easy to undo.

## Verification

- `/memory`: <!-- TODO: confirm CLAUDE.md shows as loaded -->
- `/permissions`: <!-- TODO: confirm the allow / ask / deny rules are listed -->
