# rhel-ipu-example
Baseline Ansible Content for a RHEL In-Place Upgrade (RHEL IPU)

# Preparation
This content relies on the infra.leapp collection at version 1.5+ for RHEL 7->8

Ensure the latest version of the collection is available in your environment.

# Design Decisions

This codebase ships with the configuration-as-code elements to provision a RHEL In-Place Upgrade workflow directly into your Ansible Automation Platform 2.5 instance.  If you are using Ansible Automation Platform, these can be used to quickly deploy the assets to AAP for both demonstration and production usage.

# Usage with AAP

To deploy all the assets to Ansible Automation Platform, review the file aap-configration/vars/main.yml and aap-configuration/vars/vault.yml and configure to your environment.
If not every asset needs to be created, such as machine and source control credentials, or custom execution environments, remove the tasks from the playbook prior to running.

Rename the example inventory and complete with your controller and hub details.

To deploy the configuration, run the command from the base directory
` ansible-playbook -i aap_inventory aap-configuration/deploy.yml `


# Usage from the command line
Prior to running this workflow, an execution environment must be created with the appropriate ansible versions, since supported ansible versions change between RHEL 7, 8, 9, and beyond
Example EE files are provided in execution_environments for both community and supported EEs.

ansible-builder build -f execution_environments/rhel-7-supported.yml

NOTE: Running the RHEL supported will require an ansible.cfg file with the galaxy configuration to the Red Hat or your private automation hub collections.

Complete the vars files, including the target version in vars/all.yml
Satellite configuration should be included in vars/satellite.yml, if applicable

Start by taking a backup. Edit playbooks/01-backup.yml with whatever is needed for your hypervisor and environment.
Run playbooks/01-backup.yml to create the backup.


Edit playbooks/02-preparation.yml with any pre-upgrade configurations required, such as removing third-party tools or disabling conflicting services

Run playbooks/02-preparation.yml to prepare the system for upgrade
Run playbooks/03-analysis.yml to gather the preupgrade analysis report

Edit vars/remediation_list.yml with any findings in the preupgrade analysis report that need to be remediated
Run playbooks/04-remediate.yml to remediate the findings.

Re-run playbooks/03-analysis.yml to ensure all findings were remediated.

If all inhibitors are cleared, proceed to run the upgrade.

Run playbooks/05-upgrade.yml to upgrade the system to the version specified

Edit playbooks/06-postupgrade.yml with the inverse of the steps in 01-preparation.yml to return the tools and services to use on the upgraded system, as desired, as well as any changes that were made by the remediate tooling that need to be reverted, such as NFS mounts.

Run playbooks/06-postupgrade.yml to finish the upgrade with third-party tools and services

Edit playbooks/07-cleanup.yml to clean up any artifacts that need to be removed after successfully validating the install
Run playbooks/07-cleanup.yml to clean up the stray artifacts that were left over.