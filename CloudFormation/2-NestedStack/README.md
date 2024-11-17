## Nested Stack
docs
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-nested-stacks.html

Nested stacks allow for a hierarchy of related templates to be combined to form a single product
A root stack can contain and create nested stacks .. each of which can be passed parameters and provide back outputs.
Nested stacks should be used when the resources being provisioned share a lifecycle and are related.

Why Nested Stack might be needed?
- Stack Resource limit (500)
- Multiple Stack does allow one resource in one stack to reference another resource in another stack (Stack is isolated)
- It promotes modularaity of code to solve a single solution
- Make Installation easier as root stack only needs to be applied for all nested stack to be applied as well

It is a logical resources by itself that references a stack elsewhere using the URL
```yaml
VPCSTACK:
    Type: AWS::CloudFormation::Stack
    Properties:
        TemplateURL: https:////someurl.com/template.yaml
        Parameters:
            Param1: !Ref SomeParam1
```

