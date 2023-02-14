*LINKS*

EXAMPLE EXAM:
1. https://www.udemy.com/course/terraform-associate-practice-exam/
2. Dokumentacja https://developer.hashicorp.com/terraform/intro
3. Rejestr terraforma: https://registry.terraform.io/
4. https://learn.acloud.guru/course/hashicorp-certified-terraform-associate-1/overview
5. https://developer.hashicorp.com/terraform/tutorials/certification
6. https://www.datocms-assets.com/2885/1602500234-terraform-full-feature-pricing-tablev2-1.pdf


----

Terraform -A cloud Guru training:


===

    terraform init

- initalize working directory which contains my terraform code
- download plugins and modules
- setup backend for storring terrafrom state file


    terrafrom plan

- show what kind of changes will done;
- it's not deploy anything;


    terraform apply

- Deploy infrastructure and statements in code;


    terrafrom destroy

- This command destroy all resources which are tracked via tf code

Example TF code:

```terraform

provider "aws" {
  region = "us-west1"
}

```

GCP:

```terraform

provider "google"{
  credentials = file("credentials.json")
  project = "my-gcp-project"
  region = "us-west1"
}

```

Resource example:

```terraform

resource "aws_instance" "web" {
  ami = "ami-a1b2c3d4"
  instance_type = "t2.micro"
}

```

Resource address: `aws_instance.web`

Terraform Code Snippet (data source)


```terraform

data "aws_instance" "my-vm" {
  instance_id = "i-123456890abcdef0"
}

```

Resource address `data.aws_instance.my-vm`


---
Chapter 4
---

* Terraform look for providers in the terraform providers registry.
* Providers are "plugins" which provide you possibility to handle with
partiuclar resources.
* Providers are downloaded with `terraform init` command


Terraform state:

- A way for terraform to keep track on what has been deployed.
- Based on terraform state terraform decide that our resources shoudl been
modify, created or deleted.


Terraform variables

example of variable:

```terraform

variable "my-var" {
  description = "My test variable"
  type = string
  default = "Hello"
  sensitive = true // Showing or not output from executing code
  validation {
    condition = length(var.my-var) > 4
    error_message = "The string must be more than 4 characters"
  }
}

```

Base types of vars:

* String
* number
* bool

Complex types:

- list, set, map, object, tuple

Example of list:

```terraform

variable "avaliable_zone_names" {
  type = list(string)
  default = ["us-west-1a"]
}

```

Example list of ojbects:

```terraform

variable "docker_ports" {
  type = list(object({
    internal = number
    external = number
    protocol = string
  }))
  default = [
    {
      internal = 8300
      external = 8300
      protocl = "tcp"
    }
  ]
  
}
```

Output values:

```terraform

output "instance_ip" {
  description = "VMs private IP"
  value = aws_instance.my-vm.private_ip
}

```

Terraform provisioners:

- Terraform cannot track changes to provisioners as they can take any
independent action, hence they are not tracked via terraform state files.

```terraform

resource "null_resource" "dummy_resource" {
    provisioner "local-exec" { 
      command = "echo '0' > status.txt"
    }
}
```

Full VM setup:


```terraform

resource "aws_instance" "ec2-virutal-machine" {
  ami = ami-12345
  instance_type = t2.micro
  key_name = aws_key_pair.master-key.key_name
  associate_public_ip_address = true
  vpc_security_group_ids = [aws_security_group.jenkins-sg.id]
  subnet_id = aws_subnet.subnet.id
  provisioner "local-exec" {
    command = "aws ec2 wait instance-status-ok --region ue-east-1 --instance-ids ${self.id}"
  }
}

aws_instance.ec2-virtual-machine.id
```


GIT_HUB[*REPO*] with useful terraform code:
git clone https://github.com/linuxacademy/content-hashicorp-certified-terraform-associate-foundations.git


Question to verify! 

    Which are the uses of the Terraform output variables?



---
Chapter 5
---

Terraform state:

* By default, state is stored in file called terraform.tfstate
* Simply put, it maps real-world resources to Terraform configurations.
* Resources dependency metadata is also tracked via the state file.
* Helps boost deployment performance by caching resource attributes for subsequent use. 


Terraform state command: 

    | terraform state list | - List all resources tracked by the tf state file.

    | terraform state rm   | - Delete resource from the tf statet file.

    | terraform state show | - Show details of a resource tracked in the tf state file.


Example configure dokcer provider:

```terraform

# Configure docker provider
provider "docker" {}

# Image to be used by contianer
resource "docker_image" "terraform-centos" {
  name = "centos:7"
  keep_locally = "true"
}

# Create a container 
resource "docker_container" "centos" {
  image = docker_image.terraform-centos.latest
  name = "terraform-centos"
  start = true
  command = ["/bin/sleep", "500"]
}

```

Terraform state storage:

- Save state to remote data source. Optional.
Example of storage: AWS S3, Google Storage.
- 


```terraform

provider "kubernetes" {
  config_path = "~/.kube/config"
}

resource "kubernetes_deployment" "tf-k8s-deployment" {
  metadata {
    name = "tf-k8s-deploy"
    labels = {
      name = "terraform-k8s-deployment"
    }
  }

  spec {
    replicas = 2

    selector {
      match_labels = {
        name = "terraform-k8s-deployment"
      }
    }

    template {
      metadata {
        labels = {
          name = "terraform-k8s-deployment"
        }
      }

      spec {
        container {
          image = "nginx"
          name  = "nginx"

        }
      }
    }
  }
}
```

Example questions:

    What does the terraform state mechanism do?


1. It allows for the syncing of configuration files across 2 Terraform projects.
2. It maps real-world resources to Terraform configuration/code.
3. It checks the status of your Terraform bug updates.
4. It enables Terraform to check the state of the services provided by popular cloud vendors.

Correct answer: `B`

    What benefits does storing Terraform state remotely offer?


1. It offers agility, memory optimization, and automated patching of your Terraform code.
2. It offers no particular advantage; it is the same as storing state locally.
3. It provides granular access, integrity, security, availability, and collaboration.
4. It allows for faster execution of Terraform deployments.

Correct answer: `C`

    Which command would you use to see all the resources that have been created and are being tracked by the Terraform state file?

1. terraform state rm <name-of-resource>
2. terraform state list
3. terraform state show all
4. terraform validate

Correct answer: `B`

    How does Terraform handle dependencies in your infrastructure when deploying or destroying resources?

1. It depends on Terraform Cloud features such as Sentinel for determining dependencies.
2. It does not handle any dependencies.
3. It handles them via the Terraform state file.
4. It requires the operator (user) to manually code in each and every dependency.

Correct answer: `C`

    In what file is your Terraform state stored locally?

1. terraform.tfvars
2. terraform.tfstate
3. terraform.tvstate
4. main.tf

Correct answer: `B`

    Where in Terraform code can you configure where the state file is stored?

1. In the terraform block, using the backend attribute.
2. Through the terraform_remote_state resource.
3. You cannot define or change where the state file is stored.
4. In the aws_instance resource.

Correct answer: `A`


---
Chapter 6
---

Terraform modules:

* A module is a container for multiple resources that are used together!
* Every terraform configuration has at least one module, called the root module,
which consist of code files in your main working directory.

  Modules can be downloaded or referenced from:
* Terraform public registry 
* A private registry 
* Your Local system 

Example reference using *module* block.

```terraform
module "my-vpc-module" {
    source = "./modules/vpc"
    version = "0.0.5"
    region = var.region
}

```

* Modules can optionally take input and provide outputs to plug back into your main code.

Accessing module outputs in your code:

```terraform

resource "aws_instance" "my-vpc-module" {
  ...# other arguments
  subnet_id = module.my-vpc-module.subnet_id
}
```

* Module inputs are arbitrary named parameters that you pass inside the module block.
* These inputs can be used as variable inside the module code.

```terraform

module "my-vpc-module" {
  source = "./modules/vpc"
  server-name = "us-east-1"  # -> input parameter(s) for module
}

```

* The outputs declared inside Terraform module code can be fed back into the root module
or your main code.

Output invocation convention in Terraform code:

``module.<name-of-module>.<name-of-output>``


Building and testing basic terraform module:




Make my module:

1. mkdir terraform_project
2. cd terraform_project
3. mkdir -p modules/vpc
4. cd /home/cloud_user/terraform_project/modules/vpc/
5. vim main.tf


```terraform
provider "aws" {
  region = var.region
}

resource "aws_vpc" "this" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "this" {
  vpc_id     = aws_vpc.this.id
  cidr_block = "10.0.1.0/24"
}

data "aws_ssm_parameter" "this" {
  name = "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
}
``` 
6. vim variables.tf

```terraform
variable "region" {
  type    = string
  default = "us-east-1"
}
```
7. vim outputs.tf

```terraform
output "subnet_id" {
  value = aws_subnet.this.id
}

output "ami_id" {
  value = data.aws_ssm_parameter.this.value
}

```

8. cd ~/terraform_project
9. vim main.tf
```terraform
variable "main_region" {
  type    = string
  default = "us-east-1"
}

provider "aws" {
  region = var.main_region
}

module "vpc" {
  source = "./modules/vpc"
  region = var.main_region
}

resource "aws_instance" "my-instance" {
  ami           = module.vpc.ami_id
  subnet_id     = module.vpc.subnet_id
  instance_type = "t2.micro"
}

```
10. vim outputs.tf

```terraform
output "PrivateIP" {
  description = "Private IP of EC2 instance"
  value       = aws_instance.my-instance.private_ip
}
```
11. terraform fmt -recursive
12. terraform init
13. terraform validate
14. terraform plan
15. terraform apply --auto-approve
16. terraform state list
17. terraform destroy

 
---
Chapter 7
---

Terraform built-in-functions.

* Terraform comes pre-packaged with functions to help you transform and combine values. 
* User-defined functions are not allowed --only built-in ones.
* General syntax: function_name(arg1, arg2,...) 
* Built-in functions are extremely useful in making Terraform code dynamic and flexible.


```terraform

variable "project-name" {
  type = string
  default = "prod"
}

resource "aws_vpc" "my-vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = join("-",["terrafrom"], var.project-name)
  }
}

```

Terraform type constrains:

* type constrains control the type of variable values.
* Primitive:
  * number
  * string
  * bool
* Complex types:
  * list
  * tuple
  * map
  * object
* Collection types allow multiple values of one primitive type to be grouped together
* Constructors for these Collections include:
* list (type)
* map (type)
* set (type)


```terraform
variable "training" {
  type = string
  default = ["ACG", "LA"] # List of string
}

```

* Structural types allow multiple values of different primitive types to be grouped together:
* Constructors for these Collections include:
  * object (type)
  * tuple (type)
  * set (type)


```terraform
variable "instructor" {
  type = object({
    name = string
    age = number
  })
}
```
* any is a placeholder for a primitive type yet to be decided
* actual type will be determined at runtime

```terraform

variable "data" {
  type = list(any)
  default = [1, 42, 7]
}
```

Terraform dynamic blocks.

* Dynamically constructs repeatable nested configuration blocks inside Terraform resources
* Supported within the follow block types: 
    - resource
    - data
    - provider
    - provisioner
* Used to make your code look cleaner.

Example of using dynamic blocks

```terraform

resource "aws_security_group" "my-sg" {
  name   = "my-aws_security_group"
  vpc_id = aws_vpc.my-vpc.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["1.2.3.4/32"]
  }

  ingress {
    #... more ingress rule
  }
}
```
Now using dynamic block:

```terraform

resource "aws_security_group" "my-sg" {
  name   = "my-aws_security_group"
  vpc_id = aws_vpc.my-vpc.id

  dynamic "ingress" {
    for_each = var.rules
    content {
        from_port   = ingress.value["port"]
        to_port     = ingress.value["port"]
        protocol    = ingress.value["proto"]
        cidr_blocks = ingress.value["cidrs"]
    }
  }
}
```

and here are variables:

```terraform

variable "rules" {
  default = [
    {
      port = 80
      proto = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    },
    {
      port = 22
      proto = "tcp"
      cidr_blocks = ["1.2.3.4/32"]
    }   
  ]
}
```

```
https://github.com/linuxacademy/content-hashicorp-certified-terraform-associate-foundations.git
```

* Dynamic blocks expect a complex variable type to iterate over.
* It acts like a for loop and outputs a nested block for each element in your variable.
* Be careful not to overuse dynamic blocks in your main code, as they can be hard to read and maintain.
* Only use dynamic blocks when you need to hide detail in order to build a cleaner user interface when writing reusable modules.

Example questions:

True or False? In Terraform, you can create your own user-defined functions.

1. False 
2. True

Correct answer: `False`


---
Chapter 8
---

    terraform fmr

* This command formats code for readability
* Help in keeping code consistent 
* Safe to run at any time


    terraform taint <resource_addres>

* Taint a resource, forcing it to be destroyed and recreated
* Modifies the state file, which causes the recreation workflow
* Tainting a resource may cause other resources to be modified.


    terraform import <resource_address> <ID>

* Maps existing resources to terraform using an "ID"
* "ID" is dependent on the underlying vendor, for example to import
an AWS EC2 instance you will need to provide its instance ID.
* Importing the same resource to multiple Terraform resource can cause
unknown behavior nad is not recommended.

Example terraform configuration block:

```terraform

terraform {
    required_version = ">=0.13.0"
    required_providers {
      aws = ">=3.0.0."
    }
}

```

Terraform workspaces: 

* The Terraform Workspaces are alternate state files within the same working directory. 
* Terraform starts with a single workspace that is always called default. It cannot be deleted.

    terraform workspace new <WORKSPACE-NAME>  --> Create workspace
    terraform workspace select <WORKSPACE-NAME>  --> Select workspace

*Scenario:*
- Test changes using a parallel, distinct copy of infrastructure.
- It can be modeled against branches in version control such as Git. 
- Access to workspace name is provided through the "${terraform.workspace}" variable


Example of using terraform workspace:

```terraform

resource "aws_instance" "example" { 
  count = terraform.workspace == "default" ? 5 : 1
  #... other arguments
}

```

Another with creating bucket:

```terraform

resource "aws_s3_bucket" "bucket" {
  bucket = "myxyzbucket-${terraform.workspace}"
  acl = "private"
  
}

```

Terraform debugging:

TF_LOG --> is an environment variable for enabling verbose logging in tf.
By default it will send logs into stderr (standard error output)
Can be set to the following levels:

- TRACE
- DEBUG
- INFO
- WARN
- ERROR

- Trace is most verbose level of logging ant the most reliable one.
- To persist output use the TF_LOG_PATH 
- Setting logging env var for tf in Linux:

       export TF_LOG=TRACE
       export TF_LOG_PATH=./terraform.log 



---
Chapter 9
---

Benefits of sentinel 































































































































































