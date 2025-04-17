# GitHub Allowlist Action

This action sends a POST request to add a new entry in TON Society ID Github allowlist.

## Usage

For example, use this action when a PR is merged to reward an author:

```yaml
name: Add Github User to Allowlist on PR Merge

on:
  pull_request:
    types:
      - closed

jobs:
  allowlist_user:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
      - name: Use Allowlist GitHub Action
        uses: ton-society/github-allowlist-action@main # to always get the latest version. 
        with:
          activity_id: "12345" # replace with the actual activity ID
          github_user_id: "${{ github.event.pull_request.user.id }}"
          github_token: ${{ secrets.GITHUB_TOKEN }}
          x_api_key: ${{ secrets.X_API_KEY }}
          x_partner_id: ${{ secrets.X_PARTNER_ID }}
```
where `activity_id` is an id of the activity, for which user is rewarded. To create a new activity refer to TON Society's SBT platform [docs](https://github.com/ton-society/sbt-platform?tab=readme-ov-file#steps-for-direct-integration).