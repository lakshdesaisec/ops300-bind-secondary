# ops300-bind-secondary

Builds the Lab 1 bind-secondary image and pushes it to the private registry
(registry.assign.lndesai.300.ops:5000) through Jenkins.

## CVE scanning decision
Every image is scanned with Trivy before it is pushed. The pipeline collects
the results as an artifact (trivy-report.txt, saved with each build) and
continues, instead of failing the build. The base image has HIGH/CRITICAL
findings outside this project's control, so failing would block every
build; the report keeps every finding visible for review.
