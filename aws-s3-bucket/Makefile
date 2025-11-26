# ============================
#  Terraform Makefile Wrapper
# ============================

.SILENT:

# Default tfvars file
TFVARS ?= terraform.tfvars
VERSION ?= 1.14.0
TF_DATA_DIR=/tmp/terraform-data-dir
TF_BIN_DIR=/tmp/terraform-bin
TERRAFORM=${TF_BIN_DIR}/terraform

# ---------- Install Terraform on Amazon Linux ----------
init:
	mkdir -p ${TF_BIN_DIR} && \
	cd ${TF_BIN_DIR} && \
	curl -fsSL -o terraform.zip "https://releases.hashicorp.com/terraform/${VERSION}/terraform_${VERSION}_linux_amd64.zip" && \
	unzip -o terraform.zip && \
	chmod +x terraform && \
	rm terraform.zip && \
	${TERRAFORM} -version && \
    mkdir -p ${TF_DATA_DIR} && \
    cd ~/snowflake-datacollector-terraform/aws-s3-bucket && \
	export TF_DATA_DIR=${TF_DATA_DIR} && ${TERRAFORM} init -input=false > /dev/null

# ---------- RUN 1: Snowflake only ----------
snowflake:
	export TF_DATA_DIR=${TF_DATA_DIR} && ${TERRAFORM} init -input=false > /dev/null && \
	$(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=false" && \
	cat ./snowflake_credentials.txt && \
	rm -f ./snowflake_credentials.txt

# ---------- RUN 2: Add CloudSibyl access ----------
cloudsibyl:
	export TF_DATA_DIR=${TF_DATA_DIR} && ${TERRAFORM} init -input=false > /dev/null && \
	$(TERRAFORM) apply -input=false -auto-approve -var-file="$(TFVARS)" -var="enable_cloudsibyl_access=true" && \
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