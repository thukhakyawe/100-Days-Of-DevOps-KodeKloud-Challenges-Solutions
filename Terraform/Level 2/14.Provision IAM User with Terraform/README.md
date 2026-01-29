1️⃣ variables.tf


```
variable "KKE_USER_NAME" {
  type = string
}
```

2️⃣ terraform.tfvars


```
KKE_USER_NAME = "iamuser_siva"
```

3️⃣ main.tf


```
resource "aws_iam_user" "kke_user" {
  name = var.KKE_USER_NAME

  provisioner "local-exec" {
    command = "echo 'KKE ${var.KKE_USER_NAME} has been created successfully!' >> /home/bob/terraform/KKE_user_created.log"
  }
}
```

⚠️ Important details:

local-exec runs on the machine where Terraform is executed

>> appends the message (safe if file already exists)

Exact message text matters for grading

4️⃣ outputs.tf


```
output "kke_iam_user_name" {
  value = aws_iam_user.kke_user.name
}
```

5️⃣ Terraform Commands (Run in Order)
terraform init
terraform validate
terraform apply


Type:

yes

✅ Expected Results

```
bob@iac-server ~/terraform via 💠 default ➜  terraform apply 

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_user.kke_user will be created
  + resource "aws_iam_user" "kke_user" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "iamuser_siva"
      + path          = "/"
      + tags_all      = (known after apply)
      + unique_id     = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kke_iam_user_name = "iamuser_siva"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_iam_user.kke_user: Creating...
aws_iam_user.kke_user: Provisioning with 'local-exec'...
aws_iam_user.kke_user (local-exec): Executing: ["/bin/sh" "-c" "echo 'KKE iamuser_siva has been created successfully!' >> /home/bob/terraform/KKE_user_created.log"]
aws_iam_user.kke_user: Creation complete after 0s [id=iamuser_siva]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

kke_iam_user_name = "iamuser_siva"
```
✅ Verify

Log file content:
cat /home/bob/terraform/KKE_user_created.log


Output:

KKE iamuser_siva has been created successfully!

---

🧠 Step-by-Step Explanation (Simple & Clear)

Let’s break this down in plain English.

🔹 What is this lab teaching?

This lab demonstrates:

Terraform provisioners

Running local commands

Performing actions after resource creation

Provisioners are often used for:

Logging

Notifications

One-time setup tasks

🔹 What is local-exec?

local-exec means:

“Run this command on the Terraform machine, not on AWS.”

So:

The command runs on iac-server

NOT inside AWS

NOT inside IAM

🔹 Why attach provisioner to IAM user?

Provisioners run:

After resource creation (by default)

So Terraform flow is:
1️⃣ Create IAM user
2️⃣ Run the echo command
3️⃣ Write confirmation message to file

That guarantees:

“The message is logged only if the user was created.”

🔹 Why use variables?

Because:

Labs require it

Avoids hardcoding

Makes Terraform reusable

Grader checks variable usage

Flow:

terraform.tfvars → variables.tf → main.tf

🔹 What happens during terraform apply?

1️⃣ Terraform reads terraform.tfvars
2️⃣ IAM user iamuser_siva is created
3️⃣ local-exec runs on local machine
4️⃣ Message is appended to log file
5️⃣ Terraform outputs the user name

🧠 Easy Memory Trick

Resource created = ✅

Provisioner = 🔔 notification

local-exec = 🖥️ local command

Log file = 📝 proof

🚨 Common Mistakes 

❌ Writing to wrong path
❌ Using remote-exec instead of local-exec
❌ Wrong message text
❌ Forgetting variables
❌ Output name mismatch

---