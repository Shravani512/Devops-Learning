### TerraForm

Terraform is a tool used to create, change, and manage infrastructure using code instead of doing it manually. This concept is called Infrastructure as Code (IaC).
Instead of Clicking in AWS console, Creating EC2, VPC, Load Balancer manually You write code, run a command, and Terraform does everything for you.

Why do we need Terraform?
Problems without Terraform
- Manual setup → time-consuming
- Human errors
- No version control
- Hard to recreate same infra in Dev / QA / Prod
- Difficult rollback

Terraform solves this

Infrastructure is repeatable
- Same code for Dev / QA / Prod
- Easy rollback
- Infra changes are tracked in Git
- Works with multiple cloud providers

Setup terraform on EC2 instance
- Create EC2 instance to install terraform on it
- install terraform from hasiCorp official link(https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
- Install hashicorp official key from same link (commands given)
- Verify if link is correct ( commands given)
- Add the official HashiCorp repository to your system.
- sudo apt update
- sudo apt-get install terraform
- Verify terraform --version

Setup terraform on Local
Chocolatey on windows


