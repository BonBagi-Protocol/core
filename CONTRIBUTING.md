# Contributing

1. Pick or open an issue. Backlog items use shared IDs `BB-xx` across BonBagi repositories.
2. Branch from `main`, keep pull requests small, and add a test for every behaviour change (`npm test`).
3. `npm run typecheck && npm test && npm run build` must pass; CI runs the same.
4. No network calls, no storage, no logging of addresses or payment requests: this package must stay pure.
5. Never commit keys, seeds or real payment requests (see SECURITY.md).
