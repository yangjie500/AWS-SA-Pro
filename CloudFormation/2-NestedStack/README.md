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

## Cross Stack
Cross stack references allow one stack to reference another
Outputs in one stack reference logical resources or attributes in that stack
They can be exported, and then using the !ImportValue intrinsic function, referenced from another stack.

Why Cross-Stack might be needed?
- CFN Stack are isolated and self-contained
- So, Output are not visible from other stacks
- But Output can be exported, making them visible from other stacks
- Note that Export must have a unique name in the region
- Fn::ImportValue can be used instead of Ref
- Primarily used when the lifecycle of different stacks is different and start at different time
- For Eg. VPCCreationStack is to create VPC and should be long running
- EC2InstanceCreationStack is to create a short-running EC2 for some purposes which will happen at unknown timing
- It has to somehow references the VPC id in the region

VPC Stack
```yaml
Outputs:
    SHAREDVPCID:
        Description: Shared Services VPC
        Value: !Ref VPC
        Export: SharedVPCID  # Region + Account -> UniqueID
```

EC2 Stack
``yaml
!ImportValue SharedVPCID
```
