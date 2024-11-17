## Custom Resources
Docs
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html

Custom resources enable you to write custom provisioning logic in templates that AWS CloudFormation runs anytime you create, update (if you changed the custom resource), or delete stacks

- Note that CloudFormation doesn't support everything natively
- Custom Resrouces let CFN integrate with anything it doesn't yet, or doesn't natively support
- Example would be populating S3 bucket when you create the bucket or delete all objects in a bucket when you want to delete S3 bucket
- Can even integrate with external systems
- It works by passing data to an endpoint and gets data back from endpoint

### Demo
Demo will not works as intended just to visualize what is happening

