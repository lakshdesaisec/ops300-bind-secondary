# ops300-bind-secondary

Builds the Lab 1 bind-secondary image and pushes it to the private registry
(registry.assign.lndesai.300.ops:5000) through Jenkins.

## CVE scanning decision
The pipeline scans every image with Trivy before pushing. It fails the build
on HIGH or CRITICAL findings, so a vulnerable image never reaches the registry.
CVEs with no available fix (--ignore-unfixed) are skipped, since they cannot
be fixed by rebuilding.
