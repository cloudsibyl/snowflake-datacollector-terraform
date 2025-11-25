# ============================
#  Terraform Makefile Wrapper
# ============================

# Default tfvars file (can be overridden: make snowflake TFVARS=custom.tfvars)
TFVARS ?= terraform.tfvars
VERSION ?= 1.14.0
TERRAFORM ?= /tmp/terraform-bin/terraform
TF_DATA_DIR=/tmp/terraform-install

# ---------- Install Terraform on Amazon Linux ----------
init:
	mkdir -p /tmp/terraform-bin && \
	cd /tmp/terraform-bin && \
	curl -fsSL -o terraform.zip "https://releases.hashicorp.com/terraform/${VERSION}/terraform_${VERSION}_linux_amd64.zip" && \
	unzip -o terraform.zip && \
	chmod +x terraform && \
	rm terraform.zip && \
	/tmp/terraform-bin/terraform -version && \
    mkdir -p /tmp/terraform-install && \
    cd ~/snowflake-datacollector-terraform/aws-s3-bucket

dryrun:
	TF_DATA_DIR=/tmp/terraform-install && $(TERRAFORM) init -input=false
	TF_DATA_DIR=/tmp/terraform-install && $(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=true"

# ---------- RUN 1: Snowflake only ----------
snowflake:
	TF_DATA_DIR=/tmp/terraform-install && $(TERRAFORM) init -input=false
	TF_DATA_DIR=/tmp/terraform-install && $(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=false"

# ---------- RUN 2: Add CloudSibyl access ----------
cloudsibyl:
	TF_DATA_DIR=/tmp/terraform-install && $(TERRAFORM) init -input=false
	TF_DATA_DIR=/tmp/terraform-install && $(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=true"

# ---------- Just show what would change ----------
plan:
	TF_DATA_DIR=/tmp/terraform-install && $(TERRAFORM) init -input=false
	TF_DATA_DIR=/tmp/terraform-install && $(TERRAFORM) plan -input=false -var-file="$(TFVARS)"

# ---------- Destroy everything ----------
destroy:
	TF_DATA_DIR=/tmp/terraform-install && $(TERRAFORM) destroy -input=false -auto-approve -var-file="$(TFVARS)"
