---
name: pr-process
description: Use before opening or updating any PR in a Bilby repo (pulse, ragstudio). The standard PR process and merge bar.
---
# Bilby PR process

## Before opening or updating a PR
1. **Clean.** Apply the `codebase-design` skill where it applies (deep modules, small interfaces, clean seams).
2. **Verify.** Actually test the change; be its QA, don't assume it works. Use whatever proves it:
   - agent-browser first: click through the flow like a user would;
   - the computer tool when the browser isn't enough (the OS, native apps, things outside the page);
   - anything else that helps: tests, hitting endpoints, querying the DB, reading logs.
3. **Audit tests.** If the PR adds or changes tests, run the `test-audit` skill and fix what it flags (duplicate or implementation-coupled tests, tests that don't exercise the path they claim, test-only production seams).
4. **Review.** Run the pr-review skill, yourself or through a reviewer agent; it posts the review as a PR comment. It gives an X/5 merge confidence, a pre-merge checklist and confidence bumps.
5. **Raise confidence.** Don't stop at the score. Make the changes and run the verifications it points to, then review again, until merge confidence is at least 4.0/5. If it can't reach 4.0, say why.
6. **Hand off.** The PR description says what was verified and how, and what couldn't be verified. Then hand it over for merge.

## Standing rules
- Push with your own Bilby GitHub account.
- One migration file per PR on unmerged branches.
- Frontend/UI changes: apply the `before-and-after` skill (before/after screenshots, desktop and mobile where relevant), then put them in the PR with `gh --attach` per the `pr-media` skill.
- Comment on the Linear ticket and move it to In Review.
