# Week 6 Check-In: Phase I Progress on a New Issue

**Student:** Sreytouch Lang (Jessica)  
**Week 6 Status:** Phase I of a new issue  
**Selected Issue:** [OpenHands/OpenHands#15064](https://github.com/OpenHands/OpenHands/issues/15064)  
**Issue Title:** `Architecture: extract settings access policy`

This README is my Week 6 progress journal. For this check-in, I am working on **Phase I of a new issue** rather than continuing the previous contribution. I selected a new OpenHands frontend refactor issue that is a strong match for my React and TypeScript experience, and I used this week to understand the issue, confirm it is claimable, and prepare a realistic implementation plan.

## Current Progress

I verified that issue `#15064` is a clean Phase I target:

- it is open
- it is unassigned
- it has no linked pull request
- there is no competing contributor claim comment

I also completed the main Phase I tasks:

- commented on the issue to introduce myself and express interest
- reviewed the relevant OpenHands frontend files
- confirmed the issue is a real refactor need, not a stale request
- verified my public fork exists under my GitHub account
- documented my Phase I plan

## What the Issue Is About

The problem is that the OpenHands settings route currently mixes route-loader wiring with access-policy and redirect decisions in one place. It handles settings access, feature-flag redirects, organization checks, ACP gating, and billing visibility logic inside procedural loader code, which makes the behavior harder to test and maintain.

The goal of this contribution is to extract those settings access decisions into a separate, pure policy module so the route loader gathers facts and the policy decides whether the page should render or redirect.

## Why I Chose It

I chose this issue because it fits my strongest skills:

- React
- TypeScript
- frontend architecture
- route and policy logic

It is also a good learning opportunity because it focuses on **refactoring framework-heavy code into a pure, unit-testable decision module** instead of only adding a new UI feature.

## Plan for the Next Phase

My next steps are:

1. confirm the proposed policy shape with maintainers because the issue is labeled `Needs Design`
2. trace the current `clientLoader` flow in `frontend/src/routes/settings.tsx`
3. identify the minimal fact object needed for access decisions
4. extract a pure `decideSettingsAccess(...)` function without changing user-visible behavior
5. add focused unit tests for the settings redirect matrix

## Links

- **Week 6 journal repo:** [Week6_Reflect_Phase1to4_SreytouchLang](https://github.com/jessicalang2595/Week6_Reflect_Phase1to4_SreytouchLang)
- **Direct README link for submission:** [README.md](https://github.com/jessicalang2595/Week6_Reflect_Phase1to4_SreytouchLang/blob/main/README.md)
- **Issue:** [OpenHands/OpenHands#15064](https://github.com/OpenHands/OpenHands/issues/15064)
- **My issue comment:** [comment on #15064](https://github.com/OpenHands/OpenHands/issues/15064#issuecomment-4942570892)
- **Detailed Phase I write-up:** [Week1_PhaseI_Issue_Selection_SreytouchLang](https://github.com/jessicalang2595/Week1_PhaseI_Issue_Selection_SreytouchLang)

## Summary

For Week 6, I am submitting progress on **Phase I of a new issue**. I selected a clean, claimable OpenHands frontend refactor task, confirmed why it matters, matched it to my skills, and prepared a clear plan for implementation in the next phase.
