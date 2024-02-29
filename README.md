# qa-labels-for-prs

A GitHub action to automatically add labels to pull requests based on QA report comments. 
This action is designed mostly for use by the FlyByWire project, so it is more tailored to their needs, but it can be used by anyone. 

⚠️ As of 29/02/2024 FlyByWire is using their own action maintained by me, find it here: https://github.com/flybywiresim/actions-qa-labels

## Installation 

Add this to your workflow file (e.g. `.github/workflows/qa-labels-for-prs.yml`):

```yaml
on: issue_comment

jobs:
  pr_commented:
    name: "QA Labels for PRs"
    if: ${{ github.event.issue.pull_request }} # This job only runs for pull request comments 
    runs-on: "ubuntu-latest" 
    permissions: # This is required for the action to be able to add labels to the pull request
      pull-requests: write # For adding labels, otherwise set to 'read'
    steps:
      - name: "PR QA Comments Helper"
        uses: "auroraisluna/qa-labels-for-prs@v1" # Make sure to use the latest version available!
        with:
          repo-owner: "${{ github.event.repository.owner.login }}" # Do not change this - this will automatically be set to the owner of the repository
          repo-name: "${{ github.event.repository.name }}" # Do not change this - this will automatically be set to the name of the repository
          pr-number: "${{ github.event.issue.number }}" # Do not change this - this will automatically be set to the number of the pull request
          github-token: "${{ secrets.GITHUB_TOKEN }}" # Do not change this - this will automatically be set to the GITHUB_TOKEN secret so that the action can add labels to the pull request - if you want to use a personal access token instead, you can set it here
          label-pass: "QA Passed" # The label to add if the QA report comment contains the string "QA Passed"
          label-fail: "QA Failed" # The label to add if the QA report comment contains the string "QA Failed"
          label-rtt: "QA Ready to Test" # The label to add if the QA report comment contains the string "QA Ready to Test"
          # fail-action-if-no-qacomment: true # Uncomment this line if you want the action to fail if there is no QA report comment
          # fail-action-if-qa-failed: true # Uncomment this line if you want the action to fail if the QA report comment contains the string "QA Failed"
```

- `repo-owner` 
The owner of the repository. This will automatically be set to the owner of the repository, so you don't need to change this. 
Default: `${{ github.event.repository.owner.login }}`
Required: **Yes**
Accepts: `string`

- `repo-name`
The name of the repository. This will automatically be set to the name of the repository, so you don't need to change this.
Default: `${{ github.event.repository.name }}`
Required: **Yes**
Accepts: `string`

- `pr-number`
The number of the pull request. This will automatically be set to the number of the pull request, so you don't need to change this.
Default: `${{ github.event.issue.number }}`
Required: **Yes**
Accepts: `string`

- `github-token`
The GitHub token to use for adding labels to the pull request. This will automatically be set to the GITHUB_TOKEN secret, so you don't need to change this. 
If you want to use a personal access token instead for your own account or using a bot account, you can set it here.
Default: `${{ secrets.GITHUB_TOKEN }}`
Required: **Yes**
Accepts: `string`

- `label-pass`
The label to add if the QA report comment contains the string "QA Passed".
Recommended: `QA Passed`
Required: **No**
Accepts: `string`

- `label-fail`
The label to add if the QA report comment contains the string "QA Failed".
Recommended: `QA Failed`
Required: **No**
Accepts: `string`

- `label-rtt`
The label to add if the QA report comment contains the string "QA Ready to Test".
Recommended: `QA Ready to Test`
Required: **No**
Accepts: `string`

- `fail-action-if-no-qacomment`
Whether to fail the action if there is no QA report comment - useful if you want to make sure that the QA report comment is added for every pull request.
Default: `false`
Required: **No**
Accepts: `boolean`

- `fail-action-if-qa-failed`
Whether to fail the action if the QA report comment contains the string "QA Failed" - useful if you want to make sure that the QA report comment is added for every pull request.
Default: `false`
Required: **No**
Accepts: `boolean`

## Usage

This action will automatically add labels to pull requests based on QA report comments.

In order for this action to understand which type of QA report a comment is, the comment must contain the following: 
"QA Report", "Quality Assurance Tester Report", "Quality Assurance Trainee Report", "QA Tester Report", "QA Trainee Report", "Quality Assurance Report"

The comment must also contain: "Testing Results" followed by "Passed" or "Not Passed"

If those aren't present, depending on the configuration, the action will either fail or do nothing.

If the comment contains however "Ready to Test", the action will add the label specified in `label-rtt` to the pull request.

### Example comments

#### Example 1 - QA Passed
```markdown
Quality Assurance Tester Report

[...] Report [...]

Testing Results:
Passed
```

#### Example 2 - QA Failed
```markdown
Quality Assurance Tester Report

[...] Report [...]

Testing Results:
Not Passed
```

#### Example 3 - QA Ready to Test
```markdown
Ready to Test
```

## Suggestions & Contributing

If you want to contribute to this project, feel free to open a pull request or an issue.
Please note that this project was created to be used by the FlyByWire project, so it is more tailored to their needs, when requesting features, reporting bugs, or opening pull requests, please keep that in mind and: 
- Do not change how the action works by default, or the core functionality of the action (which is to add labels to pull requests based on QA report comments based on the format described above)
- Do not change the default values of the options, instead, add new options if you need to change the default values
- If you need to test your changes, create a fork and make sure to open PRs and comments on the PR to test both your new functionality and the default functionality of the action as described above

## Building

If you want to build this action yourself, you can do so by running `npm run build` in the root directory of this project.
This will create a `dist` folder with the built action.

Please note that the build script requires `ncc` to be installed globally, you can install it by running `npm install -g @vercel/ncc`.

