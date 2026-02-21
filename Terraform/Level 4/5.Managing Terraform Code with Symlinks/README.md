🧭 Part 1: Lab Step-by-Step Guidelines


/home/bob/terraform/
│
├── main.tf
├── variables.tf
├── terraform.tfvars
├── outputs.tf
│
└── modules/
    ├── sns/
    ├── ssm/
    └── stepfunctions/


=============================
ROOT FILES
=============================
📄 variables.tf (ROOT)

```
variable "KKE_SNS_TOPIC_NAME" {
  description = "Name of the SNS topic"
  type        = string
}

variable "KKE_SSM_PARAM_NAME" {
  description = "Name of the SSM parameter"
  type        = string
}

variable "KKE_STEP_FUNCTION_NAME" {
  description = "Name of the Step Function"
  type        = string
}
```

📄 terraform.tfvars

```
KKE_SNS_TOPIC_NAME     = "datacenter-sns-topic"
KKE_SSM_PARAM_NAME     = "datacenter-param"
KKE_STEP_FUNCTION_NAME = "datacenter-stepfunction"
```


📄 main.tf (ROOT)



```
module "sns" {
  source = "./modules/sns"

  KKE_SNS_TOPIC_NAME     = var.KKE_SNS_TOPIC_NAME
  KKE_SSM_PARAM_NAME     = var.KKE_SSM_PARAM_NAME
  KKE_STEP_FUNCTION_NAME = var.KKE_STEP_FUNCTION_NAME
}

module "ssm" {
  source = "./modules/ssm"

  KKE_SNS_TOPIC_NAME     = var.KKE_SNS_TOPIC_NAME
  KKE_SSM_PARAM_NAME     = var.KKE_SSM_PARAM_NAME
  KKE_STEP_FUNCTION_NAME = var.KKE_STEP_FUNCTION_NAME

  # 🔥 Direct dependency via ARN reference
  sns_arn = module.sns.kke_sns_topic_arn

  depends_on = [module.sns]
}

module "stepfunctions" {
  source = "./modules/stepfunctions"

  KKE_SNS_TOPIC_NAME     = var.KKE_SNS_TOPIC_NAME
  KKE_SSM_PARAM_NAME     = var.KKE_SSM_PARAM_NAME
  KKE_STEP_FUNCTION_NAME = var.KKE_STEP_FUNCTION_NAME

  ssm_parameter_name = module.ssm.kke_ssm_parameter_name

  depends_on = [module.ssm]
}
```

📄 outputs.tf (ROOT)

```
output "kke_sns_topic_name" {
  value = module.sns.kke_sns_topic_name
}

output "kke_ssm_parameter_name" {
  value = module.ssm.kke_ssm_parameter_name
}

output "kke_step_function_name" {
  value = module.stepfunctions.kke_step_function_name
}
```
=============================
MODULE 1: SNS
=============================
📄 modules/sns/main.tf

```
resource "aws_sns_topic" "this" {
  name = var.KKE_SNS_TOPIC_NAME
}
```


📄 modules/sns/outputs.tf

```
output "kke_sns_topic_name" {
  value = aws_sns_topic.this.name
}

output "kke_sns_topic_arn" {
  value = aws_sns_topic.this.arn
}
```



=============================
MODULE 2: SSM
=============================
📄 modules/ssm/main.tf

```
variable "sns_arn" {
  type = string
}

resource "aws_ssm_parameter" "this" {
  name  = var.KKE_SSM_PARAM_NAME
  type  = "String"

  # 🔥 THIS CREATES THE GRAPH DEPENDENCY
  value = var.sns_arn
}
```

📄 modules/ssm/outputs.tf

```
output "kke_ssm_parameter_name" {
  value = aws_ssm_parameter.this.name
}
```


=============================
MODULE 3: STEP FUNCTIONS
=============================

📄 modules/stepfunctions/main.tf

```
variable "ssm_parameter_name" {
  type = string
}

data "aws_ssm_parameter" "sns_param" {
  name = var.ssm_parameter_name
}

resource "aws_iam_role" "sfn_role" {
  name = "${var.KKE_STEP_FUNCTION_NAME}-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect    = "Allow"
      Principal = { Service = "states.amazonaws.com" }
      Action    = "sts:AssumeRole"
    }]
  })
}

resource "aws_iam_role_policy" "ssm_policy" {
  name = "${var.KKE_STEP_FUNCTION_NAME}-policy"
  role = aws_iam_role.sfn_role.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect   = "Allow"
      Action   = "ssm:GetParameter"
      Resource = "*"
    }]
  })
}

resource "aws_sfn_state_machine" "this" {
  name     = var.KKE_STEP_FUNCTION_NAME
  role_arn = aws_iam_role.sfn_role.arn

  definition = jsonencode({
    StartAt = "ReadSSM"
    States = {
      ReadSSM = {
        Type   = "Pass"
        Result = {
          SnsArn = data.aws_ssm_parameter.sns_param.value
        }
        End = true
      }
    }
  })
}
```

📄 modules/stepfunctions/outputs.tf

```
output "kke_step_function_name" {
  value = aws_sfn_state_machine.this.name
}
```




🚀 Deploy

🔗 Absolute Symlinks (IMPORTANT)

Run exactly:

```
cd /home/bob/terraform/modules/sns
ln -sf /home/bob/terraform/variables.tf variables.tf

cd ../ssm
ln -sf /home/bob/terraform/variables.tf variables.tf

cd ../stepfunctions
ln -sf /home/bob/terraform/variables.tf variables.tf

cd ../..
terraform init
terraform validate
terraform apply -auto-approve
```

Output

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply --auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create
 <= read (data resources)

Terraform will perform the following actions:

  # module.sns.aws_sns_topic.this will be created
  + resource "aws_sns_topic" "this" {
      + arn                         = (known after apply)
      + beginning_archive_time      = (known after apply)
      + content_based_deduplication = false
      + fifo_topic                  = false
      + id                          = (known after apply)
      + name                        = "datacenter-sns-topic"
      + name_prefix                 = (known after apply)
      + owner                       = (known after apply)
      + policy                      = (known after apply)
      + signature_version           = (known after apply)
      + tags_all                    = (known after apply)
      + tracing_config              = (known after apply)
    }

  # module.ssm.aws_ssm_parameter.this will be created
  + resource "aws_ssm_parameter" "this" {
      + arn            = (known after apply)
      + data_type      = (known after apply)
      + has_value_wo   = (known after apply)
      + id             = (known after apply)
      + insecure_value = (known after apply)
      + key_id         = (known after apply)
      + name           = "datacenter-param"
      + tags_all       = (known after apply)
      + tier           = (known after apply)
      + type           = "String"
      + value          = (sensitive value)
      + value_wo       = (write-only attribute)
      + version        = (known after apply)
    }

  # module.stepfunctions.data.aws_ssm_parameter.sns_param will be read during apply
  # (depends on a resource or a module with changes pending)
 <= data "aws_ssm_parameter" "sns_param" {
      + arn            = (known after apply)
      + id             = (known after apply)
      + insecure_value = (known after apply)
      + name           = "datacenter-param"
      + type           = (known after apply)
      + value          = (sensitive value)
      + version        = (known after apply)
    }

  # module.stepfunctions.aws_iam_role.sfn_role will be created
  + resource "aws_iam_role" "sfn_role" {
      + arn                   = (known after apply)
      + assume_role_policy    = jsonencode(
            {
              + Statement = [
                  + {
                      + Action    = "sts:AssumeRole"
                      + Effect    = "Allow"
                      + Principal = {
                          + Service = "states.amazonaws.com"
                        }
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + create_date           = (known after apply)
      + force_detach_policies = false
      + id                    = (known after apply)
      + managed_policy_arns   = (known after apply)
      + max_session_duration  = 3600
      + name                  = "datacenter-stepfunction-role"
      + name_prefix           = (known after apply)
      + path                  = "/"
      + tags_all              = (known after apply)
      + unique_id             = (known after apply)

      + inline_policy (known after apply)
    }

  # module.stepfunctions.aws_iam_role_policy.ssm_policy will be created
  + resource "aws_iam_role_policy" "ssm_policy" {
      + id          = (known after apply)
      + name        = "datacenter-stepfunction-policy"
      + name_prefix = (known after apply)
      + policy      = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = "ssm:GetParameter"
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + role        = (known after apply)
    }

  # module.stepfunctions.aws_sfn_state_machine.this will be created
  + resource "aws_sfn_state_machine" "this" {
      + arn                       = (known after apply)
      + creation_date             = (known after apply)
      + definition                = (sensitive value)
      + description               = (known after apply)
      + id                        = (known after apply)
      + name                      = "datacenter-stepfunction"
      + name_prefix               = (known after apply)
      + publish                   = false
      + revision_id               = (known after apply)
      + role_arn                  = (known after apply)
      + state_machine_version_arn = (known after apply)
      + status                    = (known after apply)
      + tags_all                  = (known after apply)
      + type                      = "STANDARD"
      + version_description       = (known after apply)

      + encryption_configuration (known after apply)

      + logging_configuration (known after apply)

      + tracing_configuration (known after apply)
    }

Plan: 5 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_sns_topic_name     = "datacenter-sns-topic"
  + kke_ssm_parameter_name = "datacenter-param"
  + kke_step_function_name = "datacenter-stepfunction"
module.sns.aws_sns_topic.this: Creating...
module.sns.aws_sns_topic.this: Creation complete after 1s [id=arn:aws:sns:us-east-1:000000000000:datacenter-sns-topic]
module.ssm.aws_ssm_parameter.this: Creating...
module.ssm.aws_ssm_parameter.this: Creation complete after 1s [id=datacenter-param]
module.stepfunctions.data.aws_ssm_parameter.sns_param: Reading...
module.stepfunctions.aws_iam_role.sfn_role: Creating...
module.stepfunctions.data.aws_ssm_parameter.sns_param: Read complete after 0s [id=datacenter-param]
module.stepfunctions.aws_iam_role.sfn_role: Creation complete after 0s [id=datacenter-stepfunction-role]
module.stepfunctions.aws_iam_role_policy.ssm_policy: Creating...
module.stepfunctions.aws_iam_role_policy.ssm_policy: Creation complete after 0s [id=datacenter-stepfunction-role:datacenter-stepfunction-policy]
module.stepfunctions.aws_sfn_state_machine.this: Creating...
module.stepfunctions.aws_sfn_state_machine.this: Creation complete after 0s [id=arn:aws:states:us-east-1:000000000000:stateMachine:datacenter-stepfunction]

Apply complete! Resources: 5 added, 0 changed, 0 destroyed.

Outputs:

kke_sns_topic_name = "datacenter-sns-topic"
kke_ssm_parameter_name = "datacenter-param"
kke_step_function_name = "datacenter-stepfunction"
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)


Let’s explain what we built in a very simple way.

This lab creates a small AWS pipeline using:

SNS → sends messages

SSM Parameter Store → stores the SNS ARN

Step Functions → reads the stored value

Everything is built using Terraform modules.

🌊 What Are We Building?

Think of it like this:

1️⃣ Create an SNS topic
2️⃣ Save its ARN inside SSM
3️⃣ Create a Step Function that reads that ARN

Flow:

SNS Topic
    ↓
SSM Parameter (stores ARN)
    ↓
Step Function (reads ARN)
🟢 Step 1 — Create the SNS Topic

Inside the SNS module, we create:

resource "aws_sns_topic" "this" {
  name = var.KKE_SNS_TOPIC_NAME
}

This creates:

datacenter-sns-topic

We also output:

output "kke_sns_topic_arn" {
  value = aws_sns_topic.this.arn
}

Why?

Because the next module needs this ARN.

🟢 Step 2 — Store the SNS ARN in SSM

Inside the SSM module, we receive the ARN from SNS:

sns_arn = module.sns.kke_sns_topic_arn

Then we store it:

resource "aws_ssm_parameter" "this" {
  name  = var.KKE_SSM_PARAM_NAME
  type  = "String"
  value = var.sns_arn
}

Important:

We do NOT manually build the ARN string.

We directly use:

module.sns.kke_sns_topic_arn

This creates a real dependency:

SNS → SSM

Terraform now understands that SSM depends on SNS.

🟢 Step 3 — Create Step Function

In the Step Functions module, we:

Read the parameter:

data "aws_ssm_parameter" "sns_param" {
  name = var.ssm_parameter_name
}

Create IAM role for Step Functions

Create the state machine:

resource "aws_sfn_state_machine" "this" {
  name     = var.KKE_STEP_FUNCTION_NAME
  role_arn = aws_iam_role.sfn_role.arn
}

The Step Function now reads the SNS ARN from SSM.

Dependency chain:

SNS → SSM → Step Function
🟢 Step 4 — Why We Used Modules

Each module does one job:

Module	Responsibility
sns	Creates SNS topic
ssm	Stores ARN
stepfunctions	Reads ARN

This keeps code clean and reusable.

🟢 Step 5 — Why We Used depends_on

In root main.tf:

depends_on = [module.sns]

and

depends_on = [module.ssm]

This ensures:

SNS is created first

Then SSM

Then Step Functions

Even though Terraform is smart, labs often require explicit dependency.

🟢 Step 6 — Why the Dependency Must Be Visible

This line is very important:

value = var.sns_arn

Because we passed:

sns_arn = module.sns.kke_sns_topic_arn

Terraform clearly sees:

aws_sns_topic.this → aws_ssm_parameter.this

If we built the ARN manually like this:

value = "arn:aws:sns:us-east-1:000000000000:topic-name"

Then Terraform would NOT see the dependency.

And the lab would fail.

🟢 Step 7 — Why We Used Symbolic Links

We reused:

/home/bob/terraform/variables.tf

Inside every module using:

ln -s /home/bob/terraform/variables.tf variables.tf

This avoids duplicating variable definitions.

Important:

The symlink must use an absolute path.

🟢 Final Result

After running:

terraform apply

We get:

SNS Topic created

SSM parameter storing SNS ARN

Step Function reading parameter

Outputs printed

🎯 What I Learned

I learned how to:

✔ Use Terraform modules
✔ Pass outputs between modules
✔ Create real dependency chains
✔ Use SSM to store values
✔ Create IAM roles for Step Functions
✔ Make Terraform graph dependencies visible

---