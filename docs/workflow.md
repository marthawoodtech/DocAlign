# DocAlign Accept/Reject/Revise Workflow Guide

## Overview
DocAlign is a documentation intelligence tool designed to allow teams to discover any gaps, known as *drift*, between their code and associated documentation and to fix them, preventing confusion for internal stakeholders and external customers. DocAlign runs inside the pipeline, either as a GitHub Action or as a stage in a Jenkinsfile. A pull request (PR) triggers DocAlign, which analyzes documentation against code and posts the findings as a single consolidated comment on the pull request, each with a severity level and suggested fix.

Figure 1. DocAlign Accept/Reject/Revise Workflow
￼

### The PR Comment
The PR comment consolidates the drift findings DocAlign detected when it compared your documentation against your code. Each finding is a single instance of drift—a place where code and documentation differ. For each finding, DocAlign indicates the drift's severity level, its location, and a suggested fix. The severity level — Critical, Major, or Minor — signals how significant the drift is: Critical means the documentation directly contradicts the code; Major means the documentation is incomplete or missing for a change; Minor is informational.

The single consolidated comment DocAlign posts on the PR has the following structure:
-Header with run timestamp
-Summary line with severity levels of findings
-Findings grouped by severity level, each with
	-What changed in the code
	-What the documentation currently says
	-What the drift is
	-Severity level with brief explanation
	-Remediation suggestion, as applicable
	-For all findings: Accept, Reject, Revise buttons
-Footer with link to full audit log and DocAlign configuration


## Accept/Reject/Revise Decisions
After DocAlign posts a consolidated comment on the pull request, you review each finding and choose an action: **Accept** the finding, **Reject** it, or **Revise** the documentation yourself.

**Accept** <br>
If the finding is auto-remediable, when you accept the finding, DocAlign generates a preview diff and you confirm the diff. A separate documentation PR is opened. It follows your team’s normal review and merge process.
If the finding is not auto-remediable, when you accept the finding, it’s logged in the audit log and you make the change manually.

**Reject** <br>
When you reject the finding, you give a reason for the rejection and the finding is dismissed. No PR is opened.
The rejection reason codes are as follows:

**false_positive** — DocAlign is wrong; there's no actual drift here. The tool flagged a discrepancy that isn't one.
**code_bug** — the documentation is correct; the code is what's wrong.
**wont_fix** — the drift is real and correctly identified, but the team has decided not to address it (at least not now).
**disagree_severity** — the finding is valid, but you think it's rated at the wrong severity level.

**Revise**

When you revise the finding, you edit the documentation content. A separate documentation PR is opened.It follows your team’s normal review and merge process.

Note: Findings at or above the blocking severity level—Critical by default—prevent the pull request from merging until they are resolved. For information about how to change the threshold, see DocAlign Configuration Reference Guide.

## The Audit Log

Every workflow action you take—accept, reject, revise, confirm—is written to the append-only audit log with your identity and a timestamp, making it a permanent, trusted record of decisions you have made. Rejection reasons are recorded here, allowing you to review patterns across findings. You can view the audit log by clicking the link in the PR comment footer or by querying it through the DocAlign API. For more information, see *DocAlign API Reference Guide*.




## For More Information

For additional information, see the following DocAlign guides:

*Getting Started with DocAlign* <br>
*DocAlign Integration Guide for GitHub*  <br>
*DocAlign Integration Guide for Jenkins*  <br>
*DocAlign API Reference Guide*  <br>
*DocAlign Configuration Reference Guide*  <br>
*Release Notes for DocAlign 1.0*  <br>
