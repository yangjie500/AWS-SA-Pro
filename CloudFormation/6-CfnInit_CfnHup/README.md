## Cfn-init
Docs 
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-init.html
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html

CloudFormationInit and cfn-init are tools which allow a desired state configuration management system to be implemented within CloudFormation

Use the AWS::CloudFormation::Init type to include metadata on an Amazon EC2 instance for the cfn-init helper script. If your template calls the cfn-init script, the script looks for resource metadata rooted in the AWS::CloudFormation::Init metadata key. cfn-init supports all metadata types for Linux systems & It supports some metadata types for Windows

Procedural - HOW (User Data) vs Desired State - WHAT (cfn-init)

```yaml
Resources: 
  MyInstance: 
    Type: AWS::EC2::Instance
    Metadata: 
      AWS::CloudFormation::Init: 
        config: 
          packages: 
            :
          groups: 
            :
          users: 
            :
          sources: 
            :
          files: 
            :
          commands: 
            :
          services: 
            :
    Properties: 
      UserData: !Sub |
        #!/bin/bash -xe
        yum -y update
        /opt/aws/bin/cfn-init -v --stack ${AWS::StackId} --resource MyInstance --configsets xxx --region ${AWS::Region}
        /opt/aws/bin/cfn-signal -e $? --stack ${AWS::StackId} --resource MyInstance  --region ${AWS::Region}
```

## Cfn-hup
Docs
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-hup.html

The cfn-hup helper is a daemon that detects changes in resource metadata and runs user-specified actions when a change is detected. This allows you to make configuration updates on your running Amazon EC2 instances through the UpdateStack API action.

- cfn-init is run once as part of bootstrapping (user data)
- If CloudFormation::Init is updated, IT ISNT RERUN
- cfn-hup helper is a daemon which can be installed
- cfn-hup checks metadata periodically detects changes in resource metadata in cloudformation template
- running configurable actions when a change is detected (Calls cfn-init)
- ALL THIS upon UpdateStack
