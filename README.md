
# DocAlign

A documentation portfolio project by Martha Wood

DocAlign is a self-directed portfolio project exploring how documentation can be kept reliable, accurate, and maintainable through automated, AI-assisted analysis integrated into CI/CD pipelines, bringing a code-quality mindset to technical documentation.

DocAlign and its parent company, Covalent Technologies, are fictional. This project was created to demonstrate technical writing, documentation design, and docs-as-code practices. Any resemblance to real companies or products is coincidental.

Just as code-quality tools detect issues in source code—flagging problems, assigning severity levels, and guiding remediation before code ships—DocAlign is designed to detect documentation *drift*, the misalignment that occurs when documentation differs from the code, APIs, and specifications it describes. DocAlign's pipeline mirrors the analyze-and-remediate workflow used by modern development tools:

- Collector pulls source artifacts (code, specs, existing docs) when a pull request is opened.
- Analyzer compares documentation against current source to detect drift.
- Reporter posts findings as pull request comments with severity levels and recommended fixes.
- Remediator applies approved corrections.
  
This project demonstrates fluency with the concepts and workflows of the modern developer ecosystem: CI/CD pipelines, review based on pull requests, severity and remediation models, false-positive handling, and AI-assisted documentation.

## What's Here

This repository contains documentation deliverables for DocAlign, authored in a docs-as-code (Markdown) workflow:

- [Getting Started with DocAlign](docs/getting-started.md)—onboarding and first-use documentation
- [DocAlign Accept/Reject/Revise Workflow Guide](docs/workflow.md)—how DocAlign, running inside the pipeline, handles findings after writer review and action
  
Supporting design and planning artifacts including user personas, architecture blueprints, and a configuration reference were developed with AI to inform the documentation, reflecting an end-to-end approach from audience analysis through technical reference.

Note: This is an evolving portfolio project. Additional guides and reference materials are in development.

## Skills Demonstrated

- Technical writing and clear communication of complex technical concepts
- Docs-as-code practices (Git, Markdown, pull-request-based workflows)
- Audience analysis and persona-based documentation design
- Fluency with CI/CD pipelines, developer tooling, and AI-assisted documentation concepts

Author: Martha Wood, Senior Technical Writer
