# CLAUDE.md — Plebitis Watch Project Rules

This file documents the working rules for this project. Follow these principles when implementing any feature.

1. **Front-end app backed by Supabase for authentication only (as of Phase 20, explicitly requested by the user).** Supabase (`@supabase/supabase-js`, client `src/lib/supabaseClient.ts`, schema `supabase/schema.sql`) provides real Auth. Clinical/operational data (patients, PIVCs, assessments, photos, reminders, notifications) remains local JSON + `localStorage` until migrated in a later phase — do not wire other hooks to Supabase without explicit instruction (see rule 14).
2. **React + TypeScript + Vite.** This is the fixed stack for the application.
3. **Use local JSON/mock data.** Data lives in `src/data/` as local modules/JSON files.
4. **Use `localStorage`** when persistence across sessions is needed, but only where necessary.
5. **Do not introduce additional backend infrastructure** (Node/Express, a custom REST API, Docker, a self-hosted auth server) unless explicitly requested by the user. Supabase (Auth today; a future data migration only on explicit instruction) is the one exception, already requested — see rule 1.
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
16. **Authentication is real, via Supabase Auth (Phase 20+).** `useAuth` wraps `supabase.auth` (`getSession`, `onAuthStateChange`, `signInWithPassword`, `signOut`), exposing an async `isLoading` state that `ProtectedRoute`/`GuestOnlyRoute` must check before redirecting (via `RouteLoadingScreen`) to avoid a flash-redirect while the session check is in flight. Login is by email, not username. User display name/role come from a `profiles` table (Postgres, RLS-protected), auto-populated from signup metadata by a `handle_new_user()` trigger — see `supabase/schema.sql`. There is no plaintext password list in the codebase; demo accounts must be created via the Supabase Dashboard.
17. **UX animations should be subtle, performance-friendly, and respect `prefers-reduced-motion`; do not use animation to replace functional feedback.** Shared patterns: `src/hooks/useMountTransition.ts` for animated dropdowns/dialogs/modals, and the base transition/keyframe rules in `src/index.css`.
18. **Deployment target is Vercel as a static frontend** (build: `npm run build`, output: `dist/`). No server/runtime code. `vercel.json` provides the SPA rewrite (`/(.*) → /index.html`) required so React Router's client-side routes don't 404 on direct load. `localStorage` remains the only persistence mechanism — do not introduce an API layer for deployment purposes.
19. **Recommendation matching has one source of truth**: `src/data/recommendations.json` (matched by exact `score`, `clinicalStatus: "prototype"`) + `getRecommendationForScore()` + `<RecommendationPanel>`, rendered on both the live assessment preview and the saved Result page. `/rekomendasi/:assessmentId` redirects to `/hasil-penilaian/:assessmentId` rather than re-implementing this — never add a second recommendation engine or duplicate its rendering.
