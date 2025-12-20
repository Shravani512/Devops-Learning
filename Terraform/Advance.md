### Terrraform Advance

##### Topics:
1. Metadata (count, for_each, depends on)
2. Conditional Expressions
3. TF State management

##### Metadata
```
count        → number-based loop
for_each     → key-value loop
depends_on   → order control
```

1. count-
Used when you want to create multiple identical resources.
```
resource "aws_instance" "example" {
  count = 2
  instance_type = "t2.micro"

terraform internally thinks:
Create this resource 2 times → index 0 and index 1
}
```

2. for_each
Used when each resource needs a unique name or value.
```
for_each = {
  web = "t2.micro"
  app = "t3.micro"
}

terraform thinks:
Loop over each key-value pair and create one resource per key
Number of keys == number of instances will be created
```

3. depends on
Used when one resource must be created first.
```
depends_on = [aws_security_group.sg]

Terraform thinks as:
Wait for this resource → then create me
```
##### Conditional Expressions
It’s an if–else decision written in one line.

```
Syntax:
condition ? value_if_true : value_if_false

Example:
var.env == "prod" ? "t3.micro" : "t2.micro"

If env is prod → use t3.micro
Else → use t2.micro
```

##### TF state 
Terraform state is the source of truth that maps Terraform code to real cloud resources

Terraform state is a file that records what infrastructure Terraform has created in the real cloud. It maps resource definitions in Terraform code to actual resource IDs in providers like AWS. Because of this mapping, Terraform knows what already exists, what to change, and what to destroy.

Basic commands used-
1. terraform state list
2. terraform state show
3. terraform state rm id
4. terraform state mv
5. terraform state push
6. terraform state pull
7. terraform import
```
eg. wanted to import instance which in made on GUI but not on present on terraform

resourse aws_instance my_new_instance{
      ami="unknown"
      instance_state="unknown"
}

terraform import aws_instance.my_new_instance instance_id(from GUI)
terraform state list (list will now contain imported instance)
terraform show aws_instance.my_new_instance (instance name and details of imported instance with id will be shown)
```



