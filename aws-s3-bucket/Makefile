# ============================
#  Terraform Makefile Wrapper
# ============================
.PHONY: init snowflake cloudsibyl plan destroy
.SILENT:

# Default tfvars file
TFVARS ?= terraform.tfvars
VERSION ?= 1.14.0
TF_DATA_DIR=/tmp/terraform-data-dir
TF_BIN_DIR=/tmp/terraform-bin
TERRAFORM=${TF_BIN_DIR}/terraform
TERRAFORM_INIT=export TF_DATA_DIR=${TF_DATA_DIR} && ${TERRAFORM} init -input=false > /dev/null
TOKEN_MSG=Make sure to copy your credentials now as you will not be able to see this again.

# ---------- Install Terraform on Amazon Linux ----------
init:
	echo "Installing Terraform v${VERSION}..." && \
	mkdir -p ${TF_BIN_DIR} && \
	cd ${TF_BIN_DIR} && \
	curl -fsSL -o terraform.zip "https://releases.hashicorp.com/terraform/${VERSION}/terraform_${VERSION}_linux_amd64.zip" && \
	unzip -o terraform.zip > /dev/null && \
	chmod +x terraform && \
	rm terraform.zip && \
	${TERRAFORM} -version > /dev/null && \
    mkdir -p ${TF_DATA_DIR} && \
    cd ~/snowflake-datacollector-terraform/aws-s3-bucket && \
	echo "Terraform Terraform on linux_amd64 installation completed." && \
	${TERRAFORM_INIT}

# ---------- RUN 1: Snowflake only ----------
snowflake:
	echo "Creating S3 bucket..." && \
	export TF_DATA_DIR=${TF_DATA_DIR} && ${TERRAFORM} init -input=false > /dev/null && \
	$(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=false" > /dev/null && \
	echo "S3 bucket created." && \
	echo "Generating Snowflake credentials..." && \
	echo "${TOKEN_MSG}" && \
	cat ./snowflake_credentials.txt && \
	rm -f ./snowflake_credentials.txt

# ---------- RUN 2: Add CloudSibyl access ----------
cloudsibyl:
	echo "Adding CloudSibyl access..." && \
	export TF_DATA_DIR=${TF_DATA_DIR} && ${TERRAFORM} init -input=false > /dev/null && \
	$(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=true" > /dev/null && \
	echo "CloudSibyl access added." && \
	echo "Generating CloudSibyl role information..." && \
	echo "${TOKEN_MSG}" && \
	cat ./cloudsibyl_role_info.txt && \
	rm -f ./cloudsibyl_role_info.txt

# ---------- Just show what would change ----------
plan:
	export TF_DATA_DIR=${TF_DATA_DIR} && ${TERRAFORM} init -input=false > /dev/null && \
	$(TERRAFORM) plan -input=false -var-file="$(TFVARS)"

# ---------- Destroy everything ----------
destroy:
	export TF_DATA_DIR=${TF_DATA_DIR} && ${TERRAFORM} init -input=false > /dev/null && \
	$(TERRAFORM) destroy -input=false -auto-approve -var-file="$(TFVARS)"