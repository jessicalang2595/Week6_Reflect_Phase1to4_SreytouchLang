# Contribution 2: `Extract settings access policy (OpenHands frontend refactor)`

**Contribution Number:** 2  
**Student:** Sreytouch Lang (Jessica)  
**Issue:** [OpenHands/OpenHands#15064](https://github.com/OpenHands/OpenHands/issues/15064)  
**Status:** Phase I Complete

> **Claimability note (as of July 21, 2026):** Issue `#15064` is **open**, assigned to **`jessicalang2595`**, and I did not find a linked open pull request from another contributor in the public issue timeline when I reviewed it. The maintainer explicitly assigned the issue to me on **July 11, 2026**, so the claim is current and attributable.

**Phase I checklist completed:** posted a substantive introduction comment on the GitHub issue, verified that my public fork exists under my GitHub account, reviewed the relevant frontend files, confirmed the project is active with usable setup docs, and completed this Contribution README with a concrete Phase II plan.

---

## Submission Evidence

- **Selected issue:** [OpenHands/OpenHands#15064 — "Architecture: extract settings access policy"](https://github.com/OpenHands/OpenHands/issues/15064)
- **My issue comment:** [jessicalang2595 comment on #15064](https://github.com/OpenHands/OpenHands/issues/15064#issuecomment-4942570892)
- **Maintainer assignment comment:** [assignment confirmation on July 11, 2026](https://github.com/OpenHands/OpenHands/issues/15064#issuecomment-4948834729)
- **My fork:** [jessicalang2595/OpenHands](https://github.com/jessicalang2595/OpenHands)
- **Week 6 submission repo:** [Week6_Reflect_Phase1to4_SreytouchLang](https://github.com/jessicalang2595/Week6_Reflect_Phase1to4_SreytouchLang)
- **Project docs reviewed:** [OpenHands README](https://github.com/OpenHands/OpenHands/blob/main/README.md) and [OpenHands CONTRIBUTING.md](https://github.com/OpenHands/OpenHands/blob/main/CONTRIBUTING.md)

---

## Why I Chose This Issue

I chose this issue because it is a strong fit for my React and TypeScript experience and because it focuses on a kind of architecture work I want more practice doing: separating business-policy decisions from framework-heavy route code. The issue sits directly in frontend access logic, redirects, and testability, which matches the parts of larger UI codebases I enjoy reading and improving.

**Skill match:** this work touches React Router loaders, TypeScript control flow, config-driven redirects, and permission logic. Those are areas where I am comfortable tracing behavior, identifying edge cases, and restructuring logic without changing user-visible behavior.

**Learning goal:** I want to get better at extracting pure, unit-testable policy functions from route-level orchestration code. That is a useful engineering skill because it turns hard-to-test behavior into small decision surfaces that are easier to reason about and protect with targeted tests.

**Understanding:** I understand why this issue matters. The settings route currently gathers facts and makes policy decisions in the same procedural flow, which makes redirects and access rules harder to test in isolation and easier to break as new feature flags, organization rules, and billing behaviors are added.

---

## Understanding the Issue

### Problem Summary (2–4 sentences)

The OpenHands settings route currently mixes route-loader plumbing with access-policy and redirect decisions inside one procedural `clientLoader`. It decides who can access which settings page, when a user should be redirected, and how feature flags, organization state, ACP mode, and billing visibility affect navigation in the same code path. That matters because redirect logic is core product behavior, and when it stays coupled to query-cache reads and route details, it becomes harder to test and maintain. I chose this issue because it is a focused frontend refactor with clear user impact and strong testability value.

### Expected Behavior (after the refactor)

- The route should gather facts at the edge, then delegate access decisions to a pure function such as `decideSettingsAccess(facts)`.
- Redirect behavior should be expressed as data returned from the policy, not scattered inline through the loader.
- The redirect matrix should be unit-testable without needing React Router loaders, selected-organization store setup, or TanStack Query cache wiring.
- User-visible settings navigation behavior should stay the same after the refactor.

### Current Behavior

1. `frontend/src/routes/settings.tsx` owns settings access checks and redirect selection directly inside the `clientLoader`.
2. The loader mixes SaaS-only path checks, feature-flag hiding, ACP gating, selected-organization lookup, billing visibility, and organization permission logic in one procedural flow.
3. Some small helpers already exist, but the final orchestration and redirect selection still live in the loader.
4. The loader reads query-cache data and organization state directly, which makes the decision logic harder to unit-test in isolation.

### Affected Components (specific files/modules likely involved)

- `frontend/src/routes/settings.tsx`
- `frontend/src/utils/settings-utils.ts`
- `frontend/src/hooks/use-settings-nav-items.ts`
- `frontend/src/utils/org/permission-checks.ts`
- `frontend/src/utils/org/billing-visibility.ts`

### Why This Issue Is Appropriately Scoped

OpenHands is an active project with a public README, a public CONTRIBUTING guide, and recent repository activity within the last week as of **July 21, 2026**. This issue is not asking for a brand-new product feature; it is a bounded refactor of one settings-route decision surface with clearly named files and a concrete testability goal.

### Concrete Acceptance Criteria — what "fixed" looks like

1. A pure settings-access policy module exists and returns render-vs-redirect decisions.
2. `frontend/src/routes/settings.tsx` gathers facts but no longer owns the full redirect matrix inline.
3. Query-cache and store reads stay at the route edge rather than inside the policy itself.
4. Unit tests cover SaaS-only paths, hidden sections, ACP redirects, billing visibility, and admin-only settings behavior.
5. User-visible settings navigation behavior remains unchanged after the refactor.

---

## Reproduction Process

### Environment Setup

For Phase I, I focused on understanding the issue and mapping the relevant code rather than claiming implementation work before the design is clear. I reviewed the issue, the current OpenHands frontend route files on `main`, and the contributing/setup documentation.

OpenHands documents a setup path that uses:

- Docker
- Python 3.12
- Node.js 22+
- Poetry 1.8+
- the repository README and CONTRIBUTING workflow for local setup and testing

### Steps to Confirm the Refactor Target

1. Open `frontend/src/routes/settings.tsx` on current `main`.
2. Trace the `clientLoader` path and list which access and redirect decisions it owns.
3. Check whether those decisions already live in a pure policy function or still depend on route-loader wiring and query-cache reads.
4. Review the helper files already referenced by the route so I can separate existing utilities from the orchestration that still needs extraction.

### Findings

The issue is real and still worth doing. The code already has some helper extraction, but the main redirect orchestration still lives in the route loader and still depends on direct cache and organization-state reads. That makes this a good Phase I target because the refactor goal is concrete, testable, and limited to a known area of the frontend.

---

## Solution Approach

### Analysis

This is not mainly a UI bug. It is a design and testability problem caused by business-policy decisions living too close to framework-specific route-loader code.

### Proposed Solution

Extract the settings access and redirect rules into a pure policy module that accepts plain facts and returns a decision object. Keep framework-specific work like `queryClient.getQueryData(...)`, route params, and selected-organization store reads in a thin route-edge adapter.

### Implementation Plan (UMPIRE, adapted)

**Understand:** the route should gather facts; the policy should decide behavior.

**Match:** reuse the existing helper functions where they already express correct business rules, and refactor the orchestration layer rather than rewriting unrelated settings utilities.

**Plan:**
1. Confirm the desired policy shape with maintainers because the issue is labeled `Needs Design`.
2. Identify the minimum facts object required for access decisions.
3. Extract a pure `decideSettingsAccess(...)` function.
4. Update the route loader so it gathers facts and delegates to the policy.
5. Add focused unit tests for the redirect matrix.

**Implement:** Phase II will validate the fact-gathering seam and narrow the first extraction step.

**Review:** verify that redirect behavior is preserved and that the new policy surface is framework-free and testable.

**Evaluate:** run focused tests for the policy and confirm the route behavior remains unchanged from a user perspective.

---

## Testing Strategy

### Unit Tests

- [ ] `decideSettingsAccess(...)` returns render or redirect correctly for normal settings routes
- [ ] SaaS-only paths redirect correctly
- [ ] Hidden settings sections redirect correctly based on feature flags
- [ ] ACP gating behavior is preserved
- [ ] Billing visibility and admin-only page behavior are preserved

### Integration Tests

- [ ] The route loader still behaves correctly after delegating to the policy
- [ ] Existing settings navigation behavior remains unchanged from the user's perspective

### Manual Testing

Navigate through settings routes with different organization or permission states and confirm the app lands on the expected page after the refactor.

---

## Pull Request

**PR Link:** To be added in Phase IV.

**Planned PR summary:** extract settings access and redirect decisions into a pure policy module, keep fact gathering at the route edge, and add focused unit tests for the redirect matrix.

**Maintainer Feedback:** the issue already has a maintainer assignment response; any design clarification during implementation will be recorded here in later phases.

**Status:** Phase I complete, ready for Phase II investigation and design confirmation.

---

## Learnings & Reflections

### Technical Skills Gained

This issue already pushed me to think more carefully about the boundary between helper extraction and true policy extraction. I also got more practice reading route-loader code and identifying where framework concerns should stop and business rules should begin.

### Challenges Overcome

The biggest Phase I challenge was making sure I picked a truly claimable issue instead of relying on an issue title alone. I resolved that by checking the issue thread, my public comment, the maintainer assignment response, and the current project files before treating it as a valid contribution target.

### What I'd Do Differently Next Time

I would keep verifying issue state close to submission time because assignment and PR status can change quickly on active repositories. I would also capture the exact policy seam earlier in my notes so the Phase II plan starts from a more explicit facts-vs-decisions split.
