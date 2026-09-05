# CLAUDE.md — Plebitis Watch Project Rules

This file documents the working rules for this project. Follow these principles when implementing any feature.

1. **Front-end only.** No backend service is part of this project.
2. **React + TypeScript + Vite.** This is the fixed stack for the application.
3. **Use local JSON/mock data.** Data lives in `src/data/` as local modules/JSON files.
4. **Use `localStorage`** when persistence across sessions is needed, but only where necessary.
5. **Do not introduce a backend** (Node/Express, REST API, database, Docker, auth server) unless explicitly requested by the user.
6. **Do not invent clinical rules.** Any clinical logic (VIP Score criteria, thresholds, recommendations) must come from the user or supplied reference material, not be fabricated.
7. **VIP Score logic must be configuration-driven** when implemented — scoring rules/thresholds should live in data/config, not be hardcoded across components.
8. **Do not use photos as automatic diagnosis.** Photo documentation is for human review only, not automated image-based diagnosis.
9. **Recommendations are clinical decision support only** — never presented as a definitive diagnosis or replacement for clinical judgment.
10. **Monitoring intervals must be configurable**, not hardcoded constants.
11. **Reuse components** instead of duplicating UI across pages.
12. **Follow the supplied UI screenshots closely** when they are provided for a feature.
13. **Build the application incrementally by phase.**
14. **Do not start the next phase without explicit instruction** from the user.
15. **VIP Score is configuration-driven and hierarchical.** Score is calculated from the combination of observation signs (`src/data/vipRules.json` + `src/utils/vipScore.ts`), not by summing arbitrary per-component points. Source: Visual Infusion Phlebitis Scale (Jackson).
16. **Authentication in this prototype is simulated client-side and is not production security.** Login checks demo credentials in `src/data/users.json` and gates routes via `ProtectedRoute`/`GuestOnlyRoute` (`useAuth` + `localStorage`). No backend, hashing, or real session/token is involved.
17. **UX animations should be subtle, performance-friendly, and respect `prefers-reduced-motion`; do not use animation to replace functional feedback.** Shared patterns: `src/hooks/useMountTransition.ts` for animated dropdowns/dialogs/modals, and the base transition/keyframe rules in `src/index.css`.
18. **Deployment target is Vercel as a static frontend** (build: `npm run build`, output: `dist/`). No server/runtime code. `vercel.json` provides the SPA rewrite (`/(.*) → /index.html`) required so React Router's client-side routes don't 404 on direct load. `localStorage` remains the only persistence mechanism — do not introduce an API layer for deployment purposes.
