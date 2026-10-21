---
description: 'Diagnose and fix GitHub Actions validation issues for any workshop step'
model: Claude Sonnet 4.6 (copilot)
---

# Workshop Step Troubleshooter

This prompt helps diagnose and fix issues when a workshop step fails GitHub Actions validation.

## Usage

When using this prompt, **you must specify the step number** you're having trouble with:

- `/fix-step 1` - Fix Step 1 (Architecture plan and test baseline)
- `/fix-step 2` - Fix Step 2 (MongoDB data tier)
- `/fix-step 3` - Fix Step 3 (Express logic tier)
- `/fix-step 4` - Fix Step 4 (React presentation tier)
- `/fix-step 5` - Fix Step 5 (Final verification after merging the pull request)

## Ground Rules

- Work on the `build-octofit-app` branch, never on `main`.
- Run every command from the repository root and use `--prefix` for npm commands.
- Never edit the grader or the protected contracts to make a check pass:
  - `.github/scripts/**` and `.github/workflows/**`
  - `octofit-tracker/backend/tests/data.contract.test.ts`
  - `octofit-tracker/backend/tests/api.contract.test.ts`
  - `octofit-tracker/frontend/tests/app.contract.test.tsx`
  - `octofit-tracker/backend/tsconfig.json`, `octofit-tracker/frontend/tsconfig.json`, and `octofit-tracker/frontend/vite.config.ts`
- If one of those files was modified, restore it with `git checkout origin/main -- <file>`.
- Do not add extra `vitest.config.*`, `vitest.workspace.*`, or `vite.config.*` files to `octofit-tracker/backend` or `octofit-tracker/frontend`.
- Keep MongoDB on port 27017, the Express API on port 8000, Vite on port 5173, and the tier boundaries described in `docs/architecture.md` and `AGENTS.md`.
- Fix the smallest thing that makes the first failing check pass, then rerun it.

## Your Task

Based on the step number provided by the user, perform the following.

Each step workflow in `.github/workflows/` runs `node .github/scripts/grade-step.mjs <check>`.
Run the same checks locally from the repository root:

| Step | Workflow | Runs when | Checks |
| --- | --- | --- | --- |
| 1 | `1-step.yml` | Push to `build-octofit-app` changing `docs/architecture.md` or a `package.json` | `step1-architecture` |
| 2 | `2-step.yml` | Push changing `octofit-tracker/backend/src/models/**`, `octofit-tracker/backend/src/scripts/**`, or the data contract test | `contract-data`, `step2-data` |
| 3 | `3-step.yml` | Push changing `octofit-tracker/backend/src/**` or the API contract test | `contract-api`, `step3-api` |
| 4 | `4-step.yml` | Push changing `octofit-tracker/frontend/src/**` or `octofit-tracker/frontend/tests/**` | `contract-frontend`, `build-frontend`, `step4-frontend` |
| 5 | `5-last-step.yml` | A merged pull request into `main` changing `octofit-tracker/**` or `docs/architecture.md` | `step5-final` |

---

### Step 1: Architecture plan and test baseline

**What GitHub Actions checks for (`step1-architecture`):**

- File: `docs/architecture.md` exists and still mentions `Presentation tier`, `Logic tier`, `Data tier`, the ports `8000`, `5173`, and `27017`, and the four capabilities (`Activity tracking`, `Teams`, `Leaderboard`, `Workout suggestions`).
- Each of these lines has a real answer (more than 8 characters) on the same line after the colon:
  - `- Presentation tier decision:`
  - `- Logic tier decision:`
  - `- Data tier decision:`
  - `- Activity tracking approach:`
  - `- Team management approach:`
  - `- Leaderboard approach:`
  - `- Workout suggestions approach:`
- The test plan still lists `npm run test:data --prefix octofit-tracker/backend`, `npm run test:api --prefix octofit-tracker/backend`, and `npm test --prefix octofit-tracker/frontend`.

**Common fixes:**

- Complete the empty decision lines in place and keep the `- Label: answer` format on one line.
- Restore any deleted section of `docs/architecture.md` from `origin/main`.
- Commit and push `docs/architecture.md` to `build-octofit-app`.

---

### Step 2: MongoDB data tier

**What GitHub Actions checks for:**

- `contract-data`: `octofit-tracker/backend/tests/data.contract.test.ts` passes.
- `step2-data`:
  - `mongoose` is a backend dependency and `octofit-tracker/backend/package.json` defines a `seed` script.
  - `octofit-tracker/backend/src/models/User.ts`, `Team.ts`, `Activity.ts`, and `Workout.ts` each declare a Mongoose `Schema(...)` and `model(...)` with `required: true` validation.
  - `octofit-tracker/backend/src/scripts/seed.ts` seeds users, teams, activities, and workouts with upserts (`upsert`, `findOneAndUpdate`, or `updateOne`), contains no `TODO` or `pending` placeholder text, and never clears collections with `deleteMany({})`.

**Common fixes:**

- Read the first failing assertion and add the missing schema field, validation rule, or relationship.
- Use stable fixture keys and upserts so running the seed twice is idempotent and preserves unrelated records.
- Confirm with `npm run seed --prefix octofit-tracker/backend` (twice) while MongoDB is running.

---

### Step 3: Express logic tier

**What GitHub Actions checks for:**

- `contract-api`: `octofit-tracker/backend/tests/api.contract.test.ts` passes.
- `step3-api`:
  - The backend exposes `/api/activities`, `/api/teams`, `/api/leaderboard`, and `/api/workouts/suggestions/:userEmail`.
  - `getLeaderboard` and `suggestWorkouts` are implemented using activity `points` and `minutes`.
  - No service still throws the starter "not implemented yet" placeholder.

**Common fixes:**

- Implement one failing contract at a time in `octofit-tracker/backend/src/services/octofitService.ts`.
- Return deliberate 4xx responses (never a 500) for malformed or unknown `userId` and `teamId`, teams without a name, and unknown user emails.
- Order the leaderboard by total points, highest first, with a 1-based `rank`.

---

### Step 4: React presentation tier

**What GitHub Actions checks for:**

- `contract-frontend`: `octofit-tracker/frontend/tests/app.contract.test.tsx` passes.
- `build-frontend`: `tsc --noEmit` and `vite build` succeed in `octofit-tracker/frontend`.
- `step4-frontend`:
  - `react` is version 19, and `bootstrap` and `react-router-dom` are dependencies.
  - The frontend source uses `import.meta.env`, calls `/api/activities`, `/api/teams`, `/api/leaderboard`, and `/api/workouts/suggestions/`, renders `role="status"` and `role="alert"` states, and shows an empty state such as "No activities".
  - `Activities.tsx`, `Teams.tsx`, `Leaderboard.tsx`, and `Workouts.tsx` in `src/components/` import `getJson` from `src/api/client.ts`, call it, and never call `fetch` directly.

**Common fixes:**

- Route every request through `getJson` and keep relative `/api` paths; never hard-code a Codespaces URL.
- Add the loading, empty, success, and error states each route needs.
- Workout suggestions must call `/api/workouts/suggestions/:userEmail` for a seeded user such as `mona@example.com`.

---

### Step 5: Final verification

**What GitHub Actions checks for (`step5-final`):**

- Every Step 1 to Step 4 check above, with unchanged protected contracts.
- The backend `package.json` defines `test:data`, `test:api`, and `build`; the frontend `package.json` defines `test` and `build`.
- All three contract test suites and both builds pass.

**Common fixes:**

- Final grading only runs when a pull request from `build-octofit-app` into `main` is merged.
- If it fails after merging, fix the issue on `build-octofit-app`, rerun every check locally, and merge a new pull request into `main`.

---

## Execution Process

1. **Identify the step** from the user's input (must be 1, 2, 3, 4, or 5)
2. **Read the relevant workflow file** from `.github/workflows/` and the matching checks in `.github/scripts/grade-step.mjs` to understand the exact validation criteria
3. **Install dependencies** for the tiers that step checks: `npm ci --prefix octofit-tracker/backend` and/or `npm ci --prefix octofit-tracker/frontend`
4. **Run each check locally** with `node .github/scripts/grade-step.mjs <check>` and read the first `FAIL:` message
5. **Diagnose the issue** by comparing what exists vs. what's expected, using `docs/architecture.md` and `.github/instructions/` for the intended behavior
6. **Apply fixes** to learner-owned files only, following the ground rules above
7. **Verify the fix** by rerunning every check for that step until each one prints `PASS:`
8. **Commit and push** changes to the `build-octofit-app` branch if fixes were applied
9. **Report back** with:
   - What was wrong
   - What was fixed
   - Current validation status
   - Next steps for the user

## Important Notes

- Make sure MongoDB is running before seeding or starting the API: `ps aux | grep mongod`
- The contract tests start their own in-memory MongoDB, so they do not need the Codespace database
- Don't start the backend or frontend dev servers automatically - let the user run them in dedicated terminals or with the VS Code launch configurations
- If a step depends on previous steps, verify those checks pass first

## Error Messages to Watch For

- `Protected contract ... was modified` - A contract test or config file was edited; restore it from `origin/main`
- `Remove extra test configuration` - Delete the extra `vitest` or `vite` config file
- `Missing required file` - A file is not at the expected path
- `... must mention ...` or `... is missing ...` - A required route, state, or behavior is missing
- `MongoServerSelectionError` or `ECONNREFUSED 127.0.0.1:27017` - MongoDB is not running
- `EADDRINUSE` - Another process is already using port 8000 or 5173

## Success Criteria

After running this prompt, every check for the specified step should print `PASS:` locally and the GitHub Actions workflow for that step should pass after you push. If it doesn't, iterate and fix remaining issues until all checks pass.
