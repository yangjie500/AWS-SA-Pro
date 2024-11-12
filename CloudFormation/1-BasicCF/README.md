## Intrinsic Functions
Use this in templates to assign values to properties that not available until runtime.
Few Eg.
### Ref
The following resource declaration for an Elastic IP address needs the instance ID of an EC2 instance and uses the Ref function to specify the instance ID of the MyEC2Instance resource
```yaml
MyEIP:
  Type: "AWS::EC2::EIP"
  Properties:
    InstanceId: !Ref MyEC2Instance
```

### Fn::Join
The !Join intrinsic function in CloudFormation concatenates a list of values into a single string
```yaml
!Join
  - '' # The delimiter (in this case, an empty string '' which means no separator).
  - - 'arn:' # A list of values to concatenate.
    - !Ref AWS::Partition
    - ':s3:::elasticbeanstalk-*-'
    - !Ref AWS::AccountId
```
will return arn:aws:s3:::elasticbeanstalk-*-[AccountId]

More Info
---
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html

## Mappings
The optional Mappings section matches a key to a corresponding set of named values. For example, if you want to set values based on a region, you can create a mapping that uses the region name as a key and contains the values you want to specify for each specific region. You use the Fn::FindInMap intrinsic function to retrieve values in a map.

```yaml
Mappings:
	RegionMap:
    us-east-1:
			HVM64: "ami-0ff"
			HVMG2: "ami-0a5"
		us-west-1:
			HVM64: "ami-0bd"
			HVMG2: "ami-066"
```
```yaml
!FindInMap ["RegionMap", !Ref 'AWS:Region', "HVM64"]
```

## Conditions
The optional Conditions section contains statements that define the circumstances under which entities are created or configured. You might use conditions when you want to reuse a template that can create resources in different contexts, such as a test environment versus a production environment. In your template, you can add an EnvironmentType input parameter, which accepts either prod or test as inputs. Conditions are evaluated based on predefined pseudo parameters or input parameter values that you specify when you create or update a stack. Within each condition, you can reference another condition, a parameter value, or a mapping. After you define all your conditions, you can associate them with resources and resource properties in the Resources and Outputs sections of a template

Uses the other intrinsic functions - AND, EQUALS, IF, NOT, OR

See Example basic.yml
```yaml
```

## DependsOn
With the DependsOn attribute you can specify that the creation of a specific resource follows another. When you add a DependsOn attribute to a resource, that resource is created only after the creation of the resource specified in theDependsOn attribute.
Although CloudFormation tries to determine a dependecy order For Eg. (VPC => SUBNET => EC2) through references

Below is an example which MyElasticIP might fail as it has to be created AFTER Internet Gateway Attachment resouces is completed and deleted BEFORE it is deleted.
Note that is does not have !Ref to reference any other resources as such require DependsOn
```yaml
Resources:
	MyElasticIP:
		Type: AWS::EC2::EIP
		DependsOn: MyIgwAttachment
		Properties:
			InstanceId: !Ref MyEC2Instance
```

## Wait Conditions & cfn-signal
CreationPolicy, WaitConditions and cfn-signal can all be used together to prevent the status if a resource from reaching create complete until AWS CloudFormation receives a specified number of success signals or the timeout period is exceeded.The cfn-signal helper script signals AWS CloudFormation to indicate whether Amazon EC2 instances have been successfully created or updated.

Docs 
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-signal.html
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-waitcondition.html

cfn-signal (utility running on the EC2 Instances)
- Configure CF to hold
- Wait for n number of success signals
- wait for Timeout H:M:S for those signals (12 hour max)
- If success signals received -> CREATE_COMPLETE by CF
- If failure signal received -> Creation fails
- Timeout is reached -> Creation fails
- Needs a logical resources called <b>CreationPolicy<b> or <b>WaitCondition<b>
- AWS recommends using CreationPolicy with EC2 Instances and WaitCondition for when configurations actions that are external to the stack creation

Below examples shows
CreationPolicy applies sgnal requirement(3) and timeout (15M)
ASG provisions 3 EC2 instances, each signalling once via cfn-signal
Only then Stack will show CREATE_COMPLETE or CREATE_FAILED
```yaml
AutoScalingGroup:
Type: AWS::AutoScaling::AutoScalingGroup
Properties:
	...(Stuff)
	DesiredCapacity: '3'
	MinSize: '1'
	MaxSize: '4'
CreationPolicy:
	ResourceSignal:
		Count: '3'
		Timeout: PT15M

LaunchConfig:
	Type: AWS::AutoScaling::LaunchConfiguration
	Properties:
		...(Stuff)
		UserData:
			"Fn::Base64":
				!Sub |
					#!/bin/bash -x
					yum update -y aws-cfn-bootstrap
					... (some bootstrapping command)
					/opt/aws/bin/cfn-signal -e $? --stack ${AWS::StackName} --resource AutoScalingGroup --region ${AWS::Region}
```

For example for WaitCondition
```yaml
WaitHandle:
	Type: AWS::CloudFormation::WaitConditionHandle
WaitCondition:
	Type: AWS::CloudFormation::WaitCondition
	DependsOn: "SomeResources"
	Properties:
		Handle: !Ref "WaitHandle"
		Timeout: "300"
		Count: '1'
```
WaitHandle will generate a presigned URL with below somewhere in the CF templatesF Eg. Userdata in EC2 Instances
```yaml
"Ref" : "myWaitHandle"
```
Use the presigned URL to signal success or failure by making a https call with specified payload shown below
Note that payload pass is accessible somewhere in the CloudFormation templates
```json
{
   "Status" : "SUCCESS",
   "Reason" : "Configuration Complete",
   "UniqueId" : "ID1234",
   "Data" : "Application has completed configuration."
}
```

READ MORE in WaitCondition Documentation





