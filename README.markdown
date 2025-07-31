# AWS VPC with Network Firewall

This Terraform configuration provisions a secure AWS VPC with public and private subnets across three availability zones, integrated with AWS Network Firewall for traffic inspection.

## Prerequisites

- Terraform >= 1.0.0
- AWS CLI configured with appropriate credentials
- AWS account with permissions to create VPC, subnets, NAT Gateway, and Network Firewall resources

## Deployment Instructions

1. **Clone the repository** or copy the Terraform files to your local environment.
2. **Initialize Terraform**:
   ```bash
   terraform init   # Initialize the working directory and Install required plugins  & create .terraform folder
   terraform fmt   # It fix the indentation, spacing
   terraform validate  # It will check the configuration sysntax
   ``` 
3. **Review variables** in `variables.tf`. Modify defaults (e.g., region, CIDR blocks) if needed by creating a `terraform.tfvars` file:
   ```hcl
   region = "us-west-2"
   vpc_cidr = "10.1.0.0/16"
   environment = "dev"
   ```
4. **Plan the deployment**:
   ```bash
   terraform plan   # Shows the changes will happen before applying 
   ```
5. **Apply the configuration**:
   ```bash
   terraform apply  # Applies the actual changes to your infrastructure
   ```
6. **Destroy resources** when no longer needed:
   ```bash
   terraform destroy  #Destroys all resources
   ```

## Key Design Decisions

- **VPC Structure**: The VPC spans three availability zones for high availability. Public subnets host NAT Gateways, private subnets host workloads, and dedicated firewall subnets host Network Firewall endpoints.
- **Routing**:
  - Public subnets route directly to the Internet Gateway.
  - Private subnets route outbound traffic through NAT Gateways for internet access.
  - Firewall subnets route traffic to Network Firewall endpoints for inspection.
- **Network Firewall**:
  - Deployed in dedicated subnets per AZ to inspect traffic from private subnets.
  - Stateless rules allow HTTP/HTTPS outbound traffic for efficiency.
  - Stateful rule denies traffic to a specific IP (198.51.100.1) for security.
- **Tagging**: All resources are tagged with `Name` and `Environment` for clarity and management.

## Assumptions and Limitations

- **Region and AZs**: Defaults to `us-west-2` with three AZs. Ensure your chosen region supports Network Firewall.
- **CIDR Blocks**: Predefined CIDR ranges are used. Ensure they don't overlap with existing VPCs.
- **Firewall Rules**: Basic rules are implemented. Expand rules as needed for your use case.
- **Cost**: NAT Gateways and Network Firewall incur costs. Monitor usage to avoid unexpected charges.
- **No End-to-End Testing**: This setup assumes resources are deployed correctly but does not include sample workloads for testing firewall rules.


## Output
![alt text](image.png)
![alt text](image-1.png)
![alt text](image-2.png)
![alt text](image-3.png)
![alt text](image-4.png)
![alt text](image-5.png)
![alt text](image-6.png)


## Another way to create the files like
   * Define the files individually
     * main.tf
     * var.tf
     * out.tf
     * terraform.tfvars


## References

- [AWS Network Firewall Deployment Models](https://aws.amazon.com/blogs/networking-and-content-delivery/deployment-models-for-awsnetwork-firewall/)
- [AWS Network Firewall Architecture](https://docs.aws.amazon.com/network-firewall/latest/developerguide/arch-igw-ngw.html)
