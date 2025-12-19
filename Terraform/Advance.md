### Terrraform Advance

##### Topics:
1. Metadata (count, for_each, depends on)
2. Conditional Expressions

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
