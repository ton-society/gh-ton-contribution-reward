# GitHub TON Contribution Reward Action

This GitHub Action awards contributors with SBT (Soulbound Token) rewards upon successful PR merges. It listens for merged pull requests and issues rewards based on reviewer comments containing specific metadata.

---

## Features

- Triggered on commits pushed to `main` or `master` branches.
- Automatically identifies the merged PR associated with the commit.
- Reads reviewer approvals for reward data:  
  - **SCORE** — Numeric reward points  
  - **ACTIVITY_ID** — The reward activity identifier  
  - **WALLET** *(optional)* — TON wallet address to receive the reward  
- Posts reward claim link as a PR comment and adds a "Rewarded" label.

---

## How It Works

1. When a commit is pushed to your main branch, the action finds the associated merged PR.
2. It checks for PR reviews that are approved and parses the comment for `SCORE` and `ACTIVITY_ID`.
3. If provided, it extracts an optional `WALLET` address from the reviewer comment.
4. It sends a request to the TON ID reward platform API to issue the reward.
5. It posts a comment on the PR with a reward claim link.
6. It adds a label `Rewarded` to the PR to indicate the reward was granted.

---

## Reviewer Comment Format

Reviewers must include the following in their approval comment to trigger the reward:
```
SCORE = 10
ACTIVITY_ID = 1234
WALLET = EQC123...xyz # optional TON wallet address
```

- `SCORE` and `ACTIVITY_ID` are **required**.
- `WALLET` is optional; if not provided, the reward will be linked to the GitHub user ID.

---

## Setup & Usage

### 1. Add the Reward Action to Your Repository

Create a workflow file (e.g., `.github/workflows/reward.yml`) with the following content:

```yaml
name: Add Github User to reward allowlist on PR Merge

on:
  push:
    branches:
      - main
      - master

jobs:
  reward:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - name: GH action to reward a contribution
        uses: ton-society/gh-ton-contribution-reward@v1.1.0
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          x_api_key: ${{ secrets.X_API_KEY }}
          x_partner_id: ${{ secrets.X_PARTNER_ID }}
```

### 2. Set Your Secrets

- GITHUB_TOKEN: Default GitHub token (provided automatically in GitHub Actions).
- X_API_KEY: Your TON ID API key for authentication.
- X_PARTNER_ID: Your partner ID for the TON ID platform.

Add these secrets in your repository settings under Settings > Secrets and variables > Actions.

To obtain `X_API_KEY` and `X_PARTNER_ID` refer to [TON ID SBT Platform](https://github.com/ton-society/sbt-platform).

### 3. How Reviewers Should Approve PRs

When reviewing a PR, include a comment in the following format on the approval:

```
SCORE = 15
ACTIVITY_ID = 5678
WALLET = EQCABC123456789...   # optional
```

This metadata must be in the review body. The WALLET line is optional. If omitted, the reward links to the GitHub user ID.

### 4. What Happens on a Push to Main/Master

- The action finds the PR associated with the commit.
- If the PR is merged and has an approved review with the required metadata, it will issue the reward.
- The PR will get a comment with the reward claim link.
- The PR will be labeled with Rewarded.

---

## Example Output Comment on PR

> 🎉 @username, your reward for wallet address EQCABC123... is ready! Claim it here

or, if no wallet specified:

> 🎉 @username, your reward is ready! Claim it here

---

## Notes
- Ensure reviewers have write/admin/maintain permission, otherwise their SCORE/ACTIVITY_ID/WALLET info is ignored.
- The action will only reward the first valid approval with correct metadata.
- The API endpoint and parameters are configured internally; you just need to provide the required secrets.
