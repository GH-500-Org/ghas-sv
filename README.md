# ghas-demo-security

A hands-on sandbox for learning **GitHub Advanced Security (GHAS)**. This repo is
deliberately insecure — it has outdated dependencies, weak crypto, and no
automated security checks — so you can turn each GHAS feature on and watch it
find real problems.

> ⚠️ **Do not deploy this app anywhere.** It is intentionally vulnerable and is
> for classroom use only.

---

## What you will learn

By the end of these three demos, you will have used the three pillars of GHAS:

| # | Feature | What it finds |
|---|---|---|
| 1 | **Dependabot** | Vulnerable third-party libraries (SCA) |
| 2 | **Code Scanning with CodeQL** | Bugs & vulnerabilities in *your* code (SAST) |
| 3 | **Secret Scanning + Push Protection** | Credentials committed to git |

---

## Before you start

You will need:

1. A **GitHub account**.
2. A **fork** of this repository into your own account (click the **Fork**
   button at the top-right of the repo page).
3. GHAS features enabled on your fork. On a **public** fork these are free.
   Go to your fork → **Settings → Code security** and turn on:
   - Dependency graph
   - Dependabot alerts
   - Dependabot security updates
   - Secret scanning
   - Push protection for repository
   - Code scanning (we will configure this in Demo 2)

Optional — to run the app locally you need [Node.js](https://nodejs.org) 18+:

```bash
npm install
npm start
# open http://localhost:3000
```

You do **not** need to run the app to complete the demos.

---

## Demo 1 — Dependabot (finding vulnerable dependencies)

**Goal:** See GitHub identify known CVEs in the libraries this project uses,
then merge an automated fix.

### Steps

1. In your fork, open [`package.json`](./package.json). Notice the pinned
   versions of `express`, `lodash`, `axios`, and `minimist` — they are old on
   purpose.
2. Go to the **Security** tab → **Dependabot** → **Dependabot alerts**.
   - You should see one alert per vulnerable package. It may take a minute
     after enabling for alerts to appear — refresh the page.
   - Click into one alert (e.g. lodash) and read:
     - the CVE ID and severity,
     - the affected versions,
     - the patched version,
     - the vulnerability description.
3. Go to the **Pull requests** tab. Dependabot will have opened update PRs
   because of [`.github/dependabot.yml`](./.github/dependabot.yml).
   - Open one of the PRs.
   - Look at the **release notes** and **compatibility score** Dependabot
     includes in the PR body.
   - Merge the PR (green **Merge pull request** button).
4. Go back to **Security → Dependabot alerts**. The alert for the package you
   just updated should now be **Closed** as *Fixed*.
5. Open [`.github/dependabot.yml`](./.github/dependabot.yml) and see how the
   behaviour above is configured: schedule, grouping, ignore rules, PR limit,
   labels, and reviewers.

### Bonus: block a bad PR before it ever merges

This repo also ships a workflow at
[`.github/workflows/dependency-review.yml`](./.github/workflows/dependency-review.yml).
On every pull request it fails the check if the PR *introduces* a vulnerable
dependency.

Try it:

1. Create a new branch from the GitHub UI (e.g. `add-bad-dep`).
2. Edit `package.json` and add another old dependency, for example:
   ```json
   "moment": "2.19.3"
   ```
3. Commit and open a PR against `main`.
4. Scroll to the **Checks** section of the PR — the **Dependency review** check
   fails and lists the CVEs it blocked.

---

## Demo 2 — Code Scanning with CodeQL (finding vulnerabilities in your code)

**Goal:** Have GitHub analyze the JavaScript in this repo and flag insecure
patterns, then fix one.

### Steps

1. Open [`src/insecure.js`](./src/insecure.js). It hashes passwords with
   `md5` — a broken algorithm. This is what CodeQL will catch.
2. Go to **Security → Code scanning**.
3. Click **Set up → CodeQL → Default setup**.
   - Leave the languages as auto-detected (JavaScript).
   - Click **Enable CodeQL**.
4. Go to the **Actions** tab and watch the *CodeQL* workflow run
   (~1–3 minutes).
5. When it finishes, go back to **Security → Code scanning**. You should see an
   alert like **“Use of a broken or weak cryptographic algorithm”** pointing at
   `src/insecure.js`. Click it and read:
   - the highlighted line of code,
   - the description of the weakness,
   - the recommended fix.
6. Fix it:
   - Edit `src/insecure.js` on a new branch.
   - Replace `"md5"` with `"sha256"`.
   - Commit and open a PR.
   - On the PR, watch the **CodeQL** check re-run. The alert will be listed as
     *Fixed in this PR*.
   - Merge the PR. The alert closes automatically.

> Note: `sha256` is enough to make CodeQL happy for this demo, but in a real
> app you should hash passwords with a slow, salted algorithm such as
> `bcrypt`, `scrypt`, or `argon2`. Ask your instructor why.

---

## Demo 3 — Secret Scanning & Push Protection (blocking committed credentials)

**Goal:** See GitHub refuse to accept a push that contains a credential.

> 🚨 **Never use a real secret** for this exercise. The strings below are
> deliberately fake but *shaped* like real tokens so GitHub's detectors match
> them.

### Steps

1. Clone your fork locally and create a branch:
   ```bash
   git clone https://github.com/<your-username>/ghas-sv.git
   cd ghas-sv
   git checkout -b leak-a-secret
   ```
2. Create a file `demo-secret.txt` containing a **fake** AWS-style access key:
   ```
   AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
   AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   ```
3. Commit and push:
   ```bash
   git add demo-secret.txt
   git commit -m "demo: leak a fake secret"
   git push -u origin leak-a-secret
   ```
4. **The push is rejected.** Read the error message — GitHub's push protection
   tells you exactly which secret it detected and where.
5. In a real incident you would remove the secret, rotate the credential at the
   provider, and push again. For this demo you can instead click the link in
   the error message to **allow the secret** with reason *“Used in tests / this
   is a false positive”*, then push again.
6. Once the push succeeds, go to **Security → Secret scanning alerts**. Your
   fake secret appears with the file, line, and commit that introduced it.
7. Clean up: delete the branch and the alert (**Close as → Revoked**).

### Bonus: custom patterns

Go to **Settings → Code security → Secret scanning → Custom patterns** and add
a pattern for a made-up internal token, e.g. `ACME-[A-Z0-9]{20}`. Commit a
matching string on a branch and watch it get detected.

---

## Suggested lesson flow (~60–75 minutes)

| Time | Activity |
|---|---|
| 5 min  | Fork the repo, enable GHAS features under Settings. |
| 15 min | **Demo 1** — Dependabot alerts, update PR, dependency-review check. |
| 20 min | **Demo 2** — Enable CodeQL, review the `insecure.js` alert, fix via PR. |
| 15 min | **Demo 3** — Push a fake secret, get blocked, review the alert. |
| 10 min | Wrap-up — tour the **Security overview**, discuss `SECURITY.md`. |

---

## Reporting a vulnerability

If you find a real vulnerability in this repository (not one of the seeded
demo issues), please follow the process in [SECURITY.md](./SECURITY.md).

## License

See [LICENSE](./LICENSE).
