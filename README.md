# rhel-ipu-example
Baseline Ansible Content for a RHEL In-Place Upgrade (RHEL IPU)

# Preparation
Coming soon: Ansible Automation Platform Configuration-as-code initial deployment instructions

# Usage
Complete the vars files, including the target version in vars/all.yml
Satellite configuration should be included in vars/satellite.yml, if applicable

Edit playbooks/01-preparation.yml with any pre-upgrade configurations required, such as removing third-party tools or disabling conflicting services

Run playbooks/01-preparation.yml to prepare the system for upgrade
Run playbooks/02-analysis.yml to gather the preupgrade analysis report

Edit vars/remediation_list.yml with any findings in the preupgrade analysis report that need to be remediated

Run playbooks/03-remediate.yml to remediate the findings.  Optionally, re-run playbooks/02-analysis.yml to ensure all findings were remediated.

Run playbooks/04-upgrade.yml to upgrade the system to the version specified

Edit playbooks/05-postupgrade.yml with the inverse of the steps in 01-preparation.yml to return the tools and services to use on the upgraded system, as desired

Run playbooks/05-postupgrade.yml to finish the upgrade with third-party tools and services