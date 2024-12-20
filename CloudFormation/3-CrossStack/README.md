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
