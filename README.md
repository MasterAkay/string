# caas

Cortex as a Service

## Project Structure

```
.
├── app/
│   ├── core/           # Core application configuration
│   ├── helpers/        # Helper functions and utilities
│   ├── models/         # Database models
│   ├── routers/        # API route handlers
│   ├── schemas/        # Pydantic schemas
│   ├── services/       # Business logic services
│   └── main.py         # Application entry point
├── terraform/
│   ├── environments/   # Environment-specific configurations
│   ├── modules/        # Reusable Terraform modules
│   ├── main.tf         # Main Terraform configuration
│   └── variables.tf    # Terraform variables
├── .gitlab-ci.yml      # GitLab CI/CD configuration
├── Dockerfile          # Docker configuration
├── requirements.txt    # Python dependencies
└── README.md
```

## Prerequisites

- AWS CLI configured with appropriate credentials
- Docker and Docker Compose installed
- Terraform installed
- Python 3.11 or later
- GitLab account (for CI/CD)

## Local Development

1. Create a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Run the application locally:
```bash
uvicorn app.main:app --reload
```

## Docker Development

You can use Docker Compose for local development:
```bash
docker-compose up --build
```

## Docker Build

Build the Docker image:
```bash
docker build -t caas .
```

## AWS Deployment

1. Initialize Terraform:
```bash
cd terraform
terraform init
```

2. Create a `terraform.tfvars` file in the appropriate environment directory (e.g., `terraform/environments/dev/terraform.tfvars`):
```hcl
aws_region = "us-east-1"
domain_name = "cortex.kaatu.ai"
```

3. Deploy the infrastructure:
```bash
cd terraform/environments/dev  # or your target environment
terraform plan
terraform apply
```

4. After the infrastructure is created, build and push the Docker image:
```bash
# Get the ECR repository URL
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $(aws ecr describe-repositories --repository-names caas-repo --query 'repositories[0].repositoryUri' --output text)

# Build and tag the image
docker build -t caas .
docker tag caas:latest $(aws ecr describe-repositories --repository-names caas-repo --query 'repositories[0].repositoryUri' --output text):latest

# Push the image
docker push $(aws ecr describe-repositories --repository-names caas-repo --query 'repositories[0].repositoryUri' --output text):latest
```

## CI/CD

This project uses GitLab CI/CD for automated testing and deployment. The pipeline configuration is defined in `.gitlab-ci.yml`. The pipeline includes:
- Building and testing the application
- Building and pushing Docker images
- Deploying to AWS using Terraform

## Accessing the Application

Once deployed, the application will be available at:
- HTTP: http://cortex.kaatu.ai
- Health Check: http://cortex.kaatu.ai/health

## Cleanup

To destroy the infrastructure:
```bash
cd terraform/environments/dev  # or your target environment
terraform destroy



check for this!!! 1.0
```

 