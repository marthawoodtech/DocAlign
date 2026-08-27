# Getting Started with DocAlign

## Overview
DocAlign is a documentation intelligence tool designed to allow teams to discover any gaps, known as *drift*, between their code and associated documentation and to fix them, preventing confusion for internal stakeholders and external customers. DocAlign runs inside the pipeline, either as a GitHub Action or as a stage in a Jenkinsfile. A pull request triggers DocAlign, which runs a sequence of four components: 

*Collector* pulls source artifacts from the repository.
*Analyzer* compares two sides: code and specs (Python and Java source, OpenAPI/Swagger files) against documentation (DITA XML and Markdown files).
*Reporter* posts the findings as a single consolidated comment on the pull request, each with a severity level and suggested fix.
*Remediator* can apply mechanical fixes once you approve them.

## Prerequisites
- You must have a GitHub or Jenkins CI/CD pipeline. DocAlign runs as either a GitHub Action or a Jenkins pipeline step via a provided plugin. 
- Source code must be in Python or Java.
- Documentation must be in DITA XML 1.3 or Markdown.

## Installation

### For GitHub

DocAlign runs as a step in your GitHub Actions workflow. To install DocAlign, follow these steps:

1. At .github/workflows/docalign.yml create a workflow file as shown in the example below:

```yaml
name: DocAlign

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  docalign:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Run DocAlign
        uses: covalent-tech/docalign-action@v1
        with:
          config: docalign.config.yaml
        env:
          DOCALIGN_TOKEN: ${{ secrets.DOCALIGN_TOKEN }}
``` 

2. To set up the Docalign API token, follow these steps:
3. Obtain a token with read access to your code and write access to pull requests and issues. For token setup details, see the *DocAlign API Reference Guide*.
4. Store it as a GitHub secret. In the repository go to Settings > Secrets and variables > Actions, and add a new repository secret named DOCALIGN_TOKEN with the token as its value.
5. Reference it in the workflow with ${{ secrets.DOCALIGN_TOKEN }}, as shown.

Caution: Never paste your token directly into the code. Instead, use a GitHub secret for DocAlign's authentication.

### For Jenkins

Note: DocAlign installation for Jenkins requires administrator access (steps 1 and 3).

DocAlign runs as a stage in your Jenkinsfile. To install DocAlign, follow these steps:
1. Install the DocAlign plugin (Manage Jenkins>Plugins).
2. Add a DocAlign stage to your Jenkinsfile as shown in the example below.


```yaml
pipeline {
    agent any

    stages {
        stage('DocAlign') {
            steps {
                docalign(config: 'docalign.config.yaml')
            }
        }
    }
}
```

3. Add the authentication token for DocAlign to the Jenkins credentials store with an ID as shown in the example below:


```yaml
pipeline {
    agent any

    environment {
        DOCALIGN_TOKEN = credentials('docalign-token')
    }

    stages {
        stage('DocAlign') {
            steps {
                docalign(config: 'docalign.config.yaml')
            }
        }
    }
}
```

Caution: Never paste your token directly into the code. Instead, reference it from the Jenkins credentials store.


## Configuration
DocAlign is configured with a YAML file named docalign.config.yaml that lives at the repository root. DocAlign reads it at the start of each analysis, so changes take effect on the next run. Here are the essentials for getting started with DocAlign. For the full set of configuration options, see *DocAlign Configuration Reference Guide*.


```yaml
integration: github  # use Jenkins if you run DocAlign on Jenkins

source_paths:
  - "src/**"

doc_paths:
  - "docs/**"
```

Note: integration is the only parameter required. It tells DocAlign which CI/CD system you use. All the others can be left to their defaults as needed.
source_paths and doc_paths point DocAlign at the directories where your code and documentation live. They accept glob patterns (src/** matches everything under src). If you omit them, DocAlign analyzes the entire repository.


## First Run
To begin using DocAlign, initiate a pull request. That pull request triggers the pipeline and DocAlign runs its sequence. When the analysis is finished, you will see a single comment on the pull request with DocAlign’s findings, the associated severity levels, and suggested remediation. For more information about working with DocAlign’s findings, see *DocAlign Accept/Reject/Revise Workflow Guide*. 

Note: If a comment from DocAlign does not appear on the pull request, check that the pipeline ran and your token is configured. For more information, see Troubleshooting.

### The PR Comment
The PR comment consolidates the drift findings DocAlign detected when it compared your documentation against your code. Each finding is a single instance of drift—a place where code and documentation differ. For each finding, DocAlign indicates the drift's severity level, its location, and a suggested fix. The severity level — Critical, Major, or Minor — signals how significant the drift is: Critical means the documentation directly contradicts the code; Major means the documentation is incomplete or missing for a change; Minor is informational.

The single consolidated comment DocAlign posts on the PR has the following structure:
- Header with run timestamp
- Summary line with severity levels of findings
- Findings grouped by severity level, each with
	- What changed in the code
	- What the documentation currently says
	- What the drift is
	- Severity level with brief explanation
	- Remediation suggestion, as applicable
	- For all findings: Accept, Reject, Revise buttons
- Footer with link to full audit log and DocAlign configuration

### Accept/reject/revise workflow
After DocAlign posts findings on the pull request, you review each one and choose an action: **Accept** the finding, **Reject** it, or **Revise** the documentation yourself. Accepting an auto-remediable finding lets DocAlign apply the fix after you confirm it; other findings you handle manually. Every action is recorded in the audit log. For the full workflow—including how to choose among the three actions—see *DocAlign Accept/Reject/Revise Workflow Guide*.


## Troubleshooting
The following table contains common issues and their fixes. For more information, see *DocAlign Configuration Reference Guide*.

| Issue | Cause(s) | Fix |
| : ——| ———— | ——|
| No comment appears on the pull request. | The pipeline didn’t run (workflow file misplaced or misnamed, or the PR didn’t trigger it); the pipeline ran but the DocAlign step failed (check the step’s logs); or authentication failed (token missing/invalid so DocAlign couldn’t post). | 1. Confirm that the pipeline ran. 2. Check DocAlign’s step’s logs. 3. Verify the token.|
| : ——| ———— | ——|
| Authentication or permission errors (often a 401 or 403 error). | The token may be missing, expired, or lacking required permissions (read code, write PRs/issues), or the secret/credential name not matching what the pipeline references.| Verify the secret/credential exists, the name matches and the token has the right scopes. |
| : ——| ———— | —— |
| DocAlign runs but reports no findings (which were expected). | source_paths/doc_paths may be misconfigured so DocAlign is looking in the wrong directories. | Check that the path patterns match your actual repo structure. |
| : ——| ———— | —— |
| The pipeline fails or the DocAlign step errors out. | The config file may be missing or may have malformed YAML. The DocAlign plugin may not be installed (for Jenkins). Or there may be a version mismatch. | Confirm docalign.config.yaml exists at the repo root and is valid YAML. Confirm the plugin is installed (for Jenkins). |
| : ——| ———— | —— |
| Too many findings/noise. | source_paths/doc_paths is too broad so DocAlign analyzes files you don’t need checked. | See *DocAlign Configuration Reference Guide*. |
| : ——| ———— | —— |
| Merge blocked unexpectedly. | A Critical finding blocking merge per the severity default. | Address the Critical drift DocAlign flagged. For more information about acting on findings, see *DocAlign Accept/Reject/Revise Workflow Guide*.
If you don’t want Critical findings to block merges, adjust severity_thresholds. For more information, see *DocAlign Configuration Reference Guide*. |

## For More Information

For additional information, see the following DocAlign guides:

*DocAlign Integration Guide for GitHub*
*DocAlign Integration Guide for Jenkins*
*DocAlign API Reference Guide*
*DocAlign Configuration Reference Guide*
*DocAlign Accept/Reject/Revise Workflow Guide*
*Release Notes for DocAlign 1.0*










