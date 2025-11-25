# ============================
#  Terraform Makefile Wrapper
# ============================

.SILENT:

# Default tfvars file
TFVARS ?= terraform.tfvars
VERSION ?= 1.14.0
TERRAFORM ?= /tmp/terraform-bin/terraform
TF_DATA_DIR=/tmp/terraform-data-dir

# ---------- Install Terraform on Amazon Linux ----------
init:
	mkdir -p /tmp/terraform-bin && \
	cd /tmp/terraform-bin && \
	curl -fsSL -o terraform.zip "https://releases.hashicorp.com/terraform/${VERSION}/terraform_${VERSION}_linux_amd64.zip" && \
	unzip -o terraform.zip && \
	chmod +x terraform && \
	rm terraform.zip && \
	/tmp/terraform-bin/terraform -version && \
    mkdir -p /tmp/terraform-data-dir && \
    cd ~/snowflake-datacollector-terraform/aws-s3-bucket

dryrun:
	TF_DATA_DIR=/tmp/terraform-data-dir && $(TERRAFORM) init -input=false
	TF_DATA_DIR=/tmp/terraform-data-dir && $(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=true"

# ---------- RUN 1: Snowflake only ----------
snowflake:
	TF_DATA_DIR=/tmp/terraform-data-dir && $(TERRAFORM) init -input=false
	TF_DATA_DIR=/tmp/terraform-data-dir && $(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=false"
	cat ./snowflake_credentials.txt
	rm ./snowflake_credentials.txt

# ---------- RUN 2: Add CloudSibyl access ----------
cloudsibyl:
	TF_DATA_DIR=/tmp/terraform-data-dir && $(TERRAFORM) init -input=false
	TF_DATA_DIR=/tmp/terraform-data-dir && $(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=true"
	cat ./cloudsibyl_role_info.txt
	rm ./cloudsibyl_role_info.txt

# ---------- Just show what would change ----------
plan:
	TF_DATA_DIR=/tmp/terraform-data-dir && $(TERRAFORM) init -input=false
	TF_DATA_DIR=/tmp/terraform-data-dir && $(TERRAFORM) plan -input=false -var-file="$(TFVARS)"

# ---------- Destroy everything ----------
destroy:
	TF_DATA_DIR=/tmp/terraform-data-dir && $(TERRAFORM) destroy -input=false -auto-approve -var-file="$(TFVARS)"
