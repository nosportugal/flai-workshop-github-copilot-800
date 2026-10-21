# Workshop Troubleshooting Guide

## Quick Fix for GitHub Actions Failures

If your GitHub Actions validation is failing for any workshop step, use the troubleshooting prompt to automatically diagnose and fix the issue.

### How to Use

1. **Open GitHub Copilot Chat** in VS Code
2. **Select "Agent" mode** from the dropdown (not "Ask" or "Plan")
3. **Run the fix command** with your step number:

```prompt
/fix-step 2
```

Replace `2` with the step number you're stuck on (1, 2, 3, 4, or 5).

### Available Commands

- `/fix-step 1` - Fix Step 1: Architecture plan and test baseline (`docs/architecture.md`)
- `/fix-step 2` - Fix Step 2: MongoDB data tier (Mongoose models, seed script)
- `/fix-step 3` - Fix Step 3: Express logic tier (API routes and services)
- `/fix-step 4` - Fix Step 4: React presentation tier (components and shared API client)
- `/fix-step 5` - Fix Step 5: Final verification after merging the pull request

### What the Prompt Does

The troubleshooting prompt will automatically:

1. **Diagnose** - Run the same grading checks as GitHub Actions locally
2. **Compare** - Verify file contents against the step's validation criteria
3. **Fix** - Update learner-owned files to meet the validation criteria, without editing the contract tests or the grader
4. **Verify** - Rerun the checks until they pass
5. **Report** - Tell you what was wrong and what was fixed

### Common Issues by Step

#### Step 1 Issues

- ❌ One of the `Implementation decisions` lines in `docs/architecture.md` is still empty
- ❌ A required section, port, or test command was removed from `docs/architecture.md`
- ✅ **Fix**: Completes each decision line and restores the required sections

#### Step 2 Issues

- ❌ A Mongoose model is missing `required: true` validation
- ❌ `seed.ts` still has placeholder text, clears collections, or is not idempotent
- ❌ `data.contract.test.ts` fails
- ✅ **Fix**: Completes the schemas and makes the seed script upsert stable fixtures

#### Step 3 Issues

- ❌ A service still throws the starter "not implemented yet" error
- ❌ Invalid input returns a 500 or a success-shaped response instead of a deliberate 4xx
- ❌ `api.contract.test.ts` fails
- ✅ **Fix**: Implements activities, teams, leaderboard, and workout suggestions one contract at a time

#### Step 4 Issues

- ❌ A component calls `fetch` directly instead of `getJson` from `src/api/client.ts`
- ❌ Loading (`role="status"`), error (`role="alert"`), or empty states are missing
- ❌ `app.contract.test.tsx` fails or `vite build` breaks
- ✅ **Fix**: Routes every request through the shared API client and adds the missing UI states

#### Step 5 Issues

- ❌ The pull request was closed without merging, so final grading never ran
- ❌ One of the earlier checks regressed before the merge
- ✅ **Fix**: Fixes the regression on `build-octofit-app` so you can merge a new pull request into `main`

### Example Usage

```text
User: /fix-step 3

Copilot Agent Mode:
- Checking Step 3 validation requirements in .github/workflows/3-step.yml...
- Running: node .github/scripts/grade-step.mjs contract-api
- Found issue: POST /api/activities returns 501 (not implemented yet)
- Found issue: GET /api/leaderboard returns an empty list
- Implementing createActivity validation and getLeaderboard in octofitService.ts...
- Rerunning contract-api and step3-api...
- ✅ All Step 3 checks now pass!
- Committing changes to build-octofit-app branch...

Ready to push! Your changes should pass GitHub Actions validation.
```

### Tips

- **Wait for completion** - Agent mode may take a minute to analyze and fix issues
- **Press Continue** - Allow agent mode to execute commands when prompted
- **Keep changes** - Don't cancel or undo files created by the agent
- **Push after fixing** - Remember to push your changes after the fix completes
- **Re-run if needed** - You can run the fix command multiple times

### When to Use This

Use the fix-step prompt when:

- ⚠️ GitHub Actions workflow fails validation
- ⚠️ Mona reports validation errors in the issue comments
- ⚠️ You're not sure what's missing or incorrect
- ⚠️ You want to quickly verify your work meets requirements

### Manual Verification

You can run the same checks as GitHub Actions from the repository root.
Install the dependencies first:

```bash
npm ci --prefix octofit-tracker/backend
npm ci --prefix octofit-tracker/frontend
```

**Step 1:**

```bash
node .github/scripts/grade-step.mjs step1-architecture
```

**Step 2:**

```bash
node .github/scripts/grade-step.mjs contract-data
node .github/scripts/grade-step.mjs step2-data
```

**Step 3:**

```bash
node .github/scripts/grade-step.mjs contract-api
node .github/scripts/grade-step.mjs step3-api
```

**Step 4:**

```bash
node .github/scripts/grade-step.mjs contract-frontend
node .github/scripts/grade-step.mjs build-frontend
node .github/scripts/grade-step.mjs step4-frontend
```

**Step 5:**

```bash
node .github/scripts/grade-step.mjs step5-final
```

### Still Having Issues?

If the automatic fix doesn't resolve your issue:

1. **Check the branch** - Ensure you're on `build-octofit-app` branch
2. **Verify file paths** - Files must be in exact locations specified
3. **Restore protected files** - If a contract test, `tsconfig.json`, or `vite.config.ts` was changed, restore it with `git checkout origin/main -- <file>`
4. **Review the step instructions** - Re-read the step requirements in `.github/steps/`
5. **Check GitHub Actions logs** - Review the actual error messages in the Actions tab

### Need More Help?

- Review step instructions: `.github/steps/[1-5]-step.md`
- Review the architecture and API contract: `docs/architecture.md`
- Check instruction files: `.github/instructions/*.md`
- Review other prompts: `.github/prompts/*.prompt.md`
- Ask Copilot: "What did I miss in Step X?"

---

**Remember**: This troubleshooting prompt is designed to save you time by automatically fixing common validation issues. Let agent mode do the heavy lifting!
