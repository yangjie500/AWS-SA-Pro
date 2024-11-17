## Regional and Global AWS Architecture
Three main types of architecture
- Small Scale architectures which will only exist in one region on one country
- Systems which also exists in one region but DR requriment which it requires to fail over to a secondary region
- Systems that operate within multiple regions and need to operate through failure in one or more of those region

Global Level
- Global Service Localtion & Discovery
- Content Delivery (CDN) and optimisation
- Global health checks & Failover

Region Level
- Regional entry point
- Scaling & Resilience
- Application services and components

For Eg. Netflix Global
- Netflix use global DNS for its service discovery and regional based health checks and request routing
- Primary location will be US-East-1 but if fails then Autstralia will be used as a secondary
- Route53 can also Send customers to nearest location
- CDN are used to cache content globally - as close to end users as possible to improve performance

Netflix Regional
- Entry point from the customer via the web tier (Regional based services like Application Load Balancer, API Gateway)
- Compute tier is where all your backend resides behind the web tier (Ec2, lambda, container services)
- Storage tier is where all your data of your application resides (Elastic File Storage, Elastic Block Storage, S3)
- DB tier is where all your data will be stored as well (RDS, Aurora, DynamoDB, Redshift)
- Caching tier is where data is cached for optimaized read only access (Dynamo DAX, AWS elastic cache)
- App services tier provides specialized functions (Kinesis, Step Functions, SQS, SNS)