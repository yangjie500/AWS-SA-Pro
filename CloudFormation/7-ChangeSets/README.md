## Change Sets
When you need to update a stack, understanding how your changes will affect running resources before you implement them can help you update stacks with confidence. Change sets allow you to preview how proposed changes to a stack might impact your running resources, for example, whether your changes will delete or replace any critical resources, AWS CloudFormation makes the changes to your stack only when you decide to execute the change set, allowing you to decide whether to proceed with your proposed changes or explore other changes by creating another change set.

- When existsing stack is presence and user make a resrouce change
- One of three things might happen
- NO INTERRUPTION, SOME INTERRUPTION, REPLACEMENT
- NO INTERRUPTION -> Nothing will be changed in the existing resouces just and update will applied and that it
- SOME INTERRUPTION -> Non damaging event Eg. EC2 instance restarting which can impact service only
- REPLACEMENT -> New copy of the resource will be created replacing the old resource, DATA WILL BE LOSS

Change Sets let you preview changes and finally be applied by executing the change set