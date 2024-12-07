## S3 Object Storage Classes
### S3 Standard
- When object are stored in S3, Objects are replicated across at least 3 AZs
- 11 nines of durability, 99.999999999% durability
- Content-MD5 Checksums and cyclic redundancy checks are used to detect and fix any data corruption
- Objects are stored, a HTTP/1.1 200 OK response is returned
- Billed in the following way
    - GB/month fee for data stored
    - $ per GB charge for transfer OUT (IN is FREE)
    - price per 1000 requests
    - NO retrieval fee, NO minimum duration, NO minimum size (ONLY STANDARD)
- Designed for frequently accessed data
- Can be made publicly available, either bucket policy or static site hosting

### S3 Standard-IA
- Objects are replicated across at least 3 AZs
- Durability is the same as S3 Standard
- Same checks are done to ensure data corruption
- Billed in the following way
    - data stored are MUCH CHEAPER than S3 Standard
    - $ per GB charge for transfer OUT (IN is FREE)
    - $ per request charged
    - BUT
    - per GB data retrieval fee, overall cost increases with frequent data access
    - MINIMUM duration charge of 30 days - objects can be stored for less, but the minmum billing always applies
    - However small the objects you stored, you will still be billed a minimum capacity of 128 KB per object
- Designed for infrequent accessed of data or not storing data for short term or don't need to store lots of TINY object 
- in other words, don't use it for lots of small data, or temporary data or frequently access data

### S3 One Zone-IA
- Similar to S3 Standard-IA 
- Billed in the following way
    - data stored are MUCH MUCH MUCH CHEAPER than S3 Standard
    - $ per GB charge for transfer OUT (IN is FREE)
    - $ per request charged
    - BUT
    - per GB data retrieval fee, overall cost increases with frequent data access
    - MINIMUM duration charge of 30 days - objects can be stored for less, but the minmum billing always applies
    - However small the objects you stored, you will still be billed a minimum capacity of 128 KB per object
- BUT
- Data stored is only stored in ONE AZs
- One Zone-IA does not provide the multi-AZ resilience model of Standard or Standard-IA 
- STILL 11 nines of durability ASSUME IF that particular AZ do not fail (data is still replicated  within ONE AZs)
- Designed for long-lived data which is NON-CRITICAL & REPLACEBLE and where access is infrequent

### S3 Glacier - Instant Retrieval
- Similar to S3 Standard-IA but EVEN CHEAPER Storage BUT MORE EXPENSIVE Retrieval cost and longer minimum
- Standard-IA is for when you need data instantly but not that often (maybe once a month)
- Glacier Instant Retrieval extends this, where maybe you need data instantly but even not that often (maybe once every quarter)
- It has minimum duration charge of 90 days instead of 30 days(S3 Standard-IA)

### S3 Glacier - Flexible Retrieval (Previously known as S3 Glacier when S3 Glacier Instant Retrieval was not created)
- Same benefit as S3 Standard
- Storage cost is about 1/6 of the cost of S3 standard (cost effective)
- BUT
- Objects stored aren't immediately available
- Can't be made public (while you still can see these objects, they are now just a pointer to that object)
- Any access of data beyond object metadata requires a retrieval process (Which has to be paid to run this process)
- When object is retrieved, objects will be stored in S3 Standard-IA temporary
- This retrieval process has three types
    - Expedited (1-5 minutes)
    - Standard (3-5 hours)
    - Bulk (5-12 hours)
    - Faster = More Expensive
- First byte latency = minutes or hours as compared to the rest is miliseconds
- Other limits 
    - 40 Kb minimum billable size
    - 90 day minimum billable duration
    - Can't be made public
- Designed to store archival data where frequent or realtime access isn't needed (maybe yearly) and minutes-hours retrieval

### S3 Deep Glacier
- Much CHEAPER than S3 Glacier Flexible Retrieval BUT MORE RESTRICTIONS
- Objects stored aren't immediately available
- Can't be made public (while you still can see these objects, they are now just a pointer to that object)
- Any access of data beyond object metadata requires a retrieval process (Which has to be paid to run this process)
- When object is retrieved, objects will be stored in S3 Standard-IA temporary
- Even slower retrieval process
    - Standard (12 hours)
    - Bulk (up to 48 hours)
- First byte latency = hours or days
- Other limits 
    - 40 Kb minimum billable size
    - 180 day minimum billable duration
- Designed for archival data where it is rarely if ever, needs to be accessed Eg. secondary backup data or Legal or Regulation data storage

### S3 Intelligent-Tiering
- Different from all S3 storage classes 
- 5 different tier of storage class
    - Frequent Access (S3 Standard)
    - Infrequent Access (S3 IA)
    - Archive Instant Access (Glacier Instant Retrieval)
    - Archive Access (Glacier Flexible Retrieval)
    - Deep Archive (Glacier Deep Archive)
- Monitors and automatically moves any objects not accessed for 30 days to a low cost infrequent access tier and eventually to archive instant access, etc...
- Intelligent-tiering archive configurations can be set on buckets (prefix & tags) - 90 days will be moved to Archive Instant Access
- To move objects to Archive Access, can be set when object isn't accessed (90 -> 270) (Optional) (Not immediately retrievable)
- To move objects to Deep Archive, can be set when object isn't accessed (180 -> 730) (Optional) (Not immediately retrievable)
- As objects are accessed, they are moved back to the frequent access tier. there are NO RETRIEVAL FEES for accessing objects (IF THEY STAYED UP TO ARCHIVE INSTANT ACCESS)
- Intelligent tiering has a monitoring and automation cost per 1000 objects.
- Designed for long-lived data with changing or unknown patterns of access

## S3 Lifecycle Configuration
- Set of rules which consist of actions that apply on a Bucket or a groups of objects defined by prefix or tags
- Two types of actions can be applied
    - Transition Action
        - Eg. Transition objects from S3 Standard to S3 IA after 30 days and then S3 Glacier Archive after 90 days
    - Expiration Action
        - Eg. Delete objects or objects version after a certain time period
- Able to work on object versionining 
- Designed to managed cost overtime
- These sets of rules aren't based on access. Cannot move object based on how frequently they are access
- Only can use this on object with well defined lifecycle (says, After 30 days, object will not be used frequently etc... )
- NOTE that transition only have happen in a downward fashion (Standard -> Standard IA -> S3 Intelligent-Tiering -> S3 One Zone-IA -> S3 Glacier Instant -> S3 Glacier Flexible -> S3 Glacier Deep Archive). 
- Transition may skip a few steps. Eg. Standard can skip Standard-IA and S3 Intelligent-Tiering straight to S3 One Zone-IA (ONE EXCEPTION S3 One Zone-IA UNABLE to transit down to S3 Glacier Instant Retrieval)
- NOTE: Smaller objects can cost more (minimum size) (Standard -> Standard-IA, S3 IT, One Zone-IA)
- NOTE: Minimum of 30 days before transition ONLY when using this Lifecycle Management (Standard -> Standard-IA, One Zone-IA)
- NOTE: A SINGLE rule cannot transition to Standard-IA or One Zone-IA and THEN to glacier classess .. within 30 days, In other words, objects must stay in IA,One-IA for 30 days before moving into glacier classes (BUT you can have two rules that does this movement without waiting)
    

## S3 Replication
S3 has two replication features which allow objects to be replicated between a SOURCE and DESTINATION buckets in the same or different AWS accounts
Cross-Region Replication (CRR) is the process used when Source and Destination are in different AWS regions
Same-Region Replication (SRR) is used when the buckets are in the same region.
- Replication Configuration applies on the source bucket, it configures S3 to replicate from source bucket to destination bucket
- NEED to configure IAM Role, S3 service has to assume it (Trust Policy), Permission policy needs to give permission to read objects on source bucket and replicate it to destination bucket

### Same AWS Account (Both Buckets in same Account)
- Both Buckets are owned by the same AWS Account
- Trust the AWS Account, Trust IAM as service
- Trust IAM Role
- Meaning IAM role automatically has access to the source and destination buckets as long as role's permission policy grant that access

### Different AWS Account 
- Destination bucket doesn't trust the source account or the role that replicate the bucket content
- Neccessary to add bucket policy (Resource Policy) on the destination bucket which allows the role in the source account(separate account) to replicaate objects into it

### Replication Options
- What to replicate - Default All objects, All Prefix, All tag are replicated, BUT subset can be replicated via creation of rule (Filter rule)
- Which Storage Classes the destination bucket will use - Default is to maintain the same storage class (Useful to replicate to secondary data storage for HA but using cheaper storage class)
- Ownership of objects in the destination bucket - Default is the source account (Could be a problem when the destination bucket is in a different AWS Account, because by default the objects is owned by source AWS Account)
- Replication Time Control (RTC) which adds a guaranteed 15 minute replication SLA onto this process (Without this, it is a best effort process). You can even see which objects are queued for replication. Only usually used when there is a strict set of requirements from your business to make sure that the destination bucket and source bucket are in sync as much as possible.

### Exam Pointers
- By Default, Not retroactive & Versioning needs to be ON on both buckets , Meaning if you enable replication on a bucket which already has objects, those objects will not be replicated
- Batch replication can be used to replicate existing objects (needs to be specifically selected)
- it is strictly one-way replication from source to destiantion (Recently AWS introduced bi-directional replication but must be specifically configured)
- What can be replicated - Unencrypted objects, SSE-S3 encrypted, SSE-KMS (need extra config), SSE-C (NEW)
- Source bucket owner needs permissions on the objects to be replicated
- No system events (Any changes made on the object by lifecycle mangement, they will not be replicated over to destination bucket) 
- Cannot replicate to glacier or glacier deep archive
- DELETE are not replicated but in actual fact objects can add the delete marker (DELTE in Bucket Versioning) ONLY IF the option (DeleteMarkerReplication) is enabled 

### Why Replicate?
- SRR - Log Aggregation
- SRR - PROD and TEST environment Sync of data
- SRR - Resilience when there is strict sovereignty (data cannot be another region)
- CRR - Global Resilience Improvements
- CRR - Latency Reduction


## S3 Encryption
Bucket aren't encrypted but objects are - Each object might be using encryption setting
S3 Encryption is mandated by AWS
Two main types of Encryption
- Server Side Encryption
- Client Side Encryption
### Client Side Encryption
- All the key management and encryption process is done by client and ciphertext objects is then send to S3
- AWS will never see the plaintext data at all time
### S3 Server-Side Encryption (SSE)
- Server-Side Encryption with Customer-Provided Keys (SSE-C)
    - Customer managed the key
    - AWS handle the cryptographic operations
    - Essentially offloading the CPU requirements of cryptographic operations of HUGE data to AWS, while customer manages the key
    - Customer provides plaintext data and key
    - When it arrives at S3 Endpoint, Object is encrypted and a hash of the key is tagged to the object, then the Key is destroyed
    - Hash of the key is used during decryption, where the hash can identify if the specific key was used or not
    - S3 Storage only has ciphertext object and key hash
    - Need to TRUST S3 will discard the key (There is audit)
- Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3) (Default)
    - AWS handle the cryptographicc operations and the key generation/management
    - Customer provides only Plaintext to S3 Endpoint
    - AWS then create a key just for the object and encrypt into ciphertext
    - There is another key, AWS S3 key (not the object key), is FULLY managed by AWS and will be used to encrypt the object key 
    - Object key will then be discarded
    - Ciphertext and encrypted object key will be stored in S3 storage
    - NOT Suitable for when you need a tight controlled environement where you need to control rotation of keys
    - NOT Suitable for when you need role separation (S3 Administrator will be able to view ALL S3 Objects) which is not ideal in Medical industry
- Server-Side Encryption with KMS Keys Stored in AWS Key Management Service (SSE-KMS)
    - Involves Key Management System (KMS) 
    - Ideally use Customed Managed Key in KMS, which means key created by user in KMS
    - When S3 needs to encrypt an object, it will have to liaise with KMS and request a new data encryption key to be generated using chosen KMS key
    - KMS will then deliver two versions of the same DEK, a plaintext version and a ciphertext version
    - S3 will then use the plaintext version of key and plaintext version of object and encrypts, immediately discarding the plaintext key leaving only ciphertext object and ciphertext key which will be then stored it in S3.
    - KMS only can encrypt objects up to 4KB size, so it is used to generate DEK (< 4KB size), KMS doesn't store DEK, it generates and gives it to S3
    - Gives fine grain controlled over the KMS key being used as well as its rotation, logging and audit logs of the KMS key will be available in cloudtrail
    - In order to encrypt or decrypt objects, a person needs to have permission to use the KMS key
    - By using multiple KMS key to encrypt objects, Role Separation can then be achieved

