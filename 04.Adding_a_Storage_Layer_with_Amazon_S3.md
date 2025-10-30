# **Chapter 4: Adding a Storage Layer with Amazon S3**

---

**Module 4 Objectives & Overview**
- Define Amazon S3 and how it works
- Recognize problems that Amazon S3 can solve
- Describe how to move data to and from Amazon S3
- Manage content storage efficiently using Amazon S3
- Recommend appropriate use of Amazon S3 based on requirements
- Configure static websites on Amazon S3
- Apply Well-Architected Framework principles to S3 storage

---

**1. Defining Amazon S3**

**Types of Storage**

**Block Storage**
- Data stored on a device in fixed-sized blocks
- Typical use: Operating systems, databases
- AWS example: Amazon EBS

**File Storage**
- Data stored in the hierarchical structure
- Typical use: File shares, user directories
- AWS example: Amazon EFS

**Object Storage**
- Data stored as objects based on attributes and metadata
- Typical use: Unstructured data, web content
- AWS example: Amazon S3

**Amazon S3 Fundamentals**

**Key Concepts**
- Stores massive (unlimited) amounts of unstructured data
- Data files stored as objects in buckets
- Maximum single object size: 5 TB
- Objects have globally unique URLs (universal namespace)

**S3 Components**
- **Bucket**: Container for objects with a unique name
- **Object**: File and its metadata
- **Key**: Unique identifier for object within bucket
- **Version ID**: Unique identifier for object versions
- **Metadata**: Name-value pairs describing the object

**Bucket Naming and Structure**
- Format: `https://s3-<aws-region>.amazonaws.com/<bucket-name>/<object-key>`
- Bucket names must be globally unique across all AWS accounts
- Region-specific endpoints

**Folder Structure Using Prefixes**
```
Objects in graphics-bucket:
photos/2022/catpiano.jpg
photos/2022/catonphone.jpg
photos/2022/ninepuppies.png
photos/2021/lakefront.png
```

**Query with prefix `photos/2022` returns:**
- graphics-bucket/photos/2022/catpiano.jpg
- graphics-bucket/photos/2022/catonphone.jpg
- graphics-bucket/photos/2022/ninepuppies.png

**Amazon S3 Benefits**
- Durability: 99.999999999% (11 9's)
- Availability: 99.99%
- High performance
- Scalability (unlimited storage)
- Security and compliance

---

**2. Using Amazon S3**

**Common Use Cases**

**Handling Demand Spikes**
- Host web content needing bandwidth for extreme demand spikes
- Global distribution via CloudFront

**Static Website Hosting**
- Host static sites with HTML files, images, and videos
- Direct URL access: `https://<bucket-name>.s3.amazonaws.com/video.mp4`

**Data Store for Analytics**
- Store data for computation and large-scale analytics
- Integration with AWS analytics services (Athena, Redshift, EMR)

**Backup and Archive**
- Disaster recovery and data backup solutions
- Long-term archival with Glacier storage classes

**Specific Implementation Patterns**

**Media Hosting**
- Direct access to media files via URLs
- Integration with CDN for global distribution

**Static Website Pattern**
- S3 bucket configured for website hosting
- Custom domain configuration
- Integration with Route 53

**Data Integration and Preparation**
- Central data lake storage
- ETL processes feeding from S3
- Analytics tools reading from S3

---

**3. Moving Data to and from Amazon S3**

**Uploading Objects**
- No limit to a number of objects in the bucket
- Write permission required for uploads
- Objects encrypted by default (server-side encryption)

**Upload Methods**
- AWS Management Console
- AWS CLI
- AWS SDKs
- Amazon S3 REST API

**Performance Optimization Features**

**Multipart Upload**
- Upload a single object as a set of parts
- Recommended for objects 100 MB or larger
- **Advantages**:
  - Improved throughput
  - Quick recovery from network issues
  - Pause and resume capability
  - Upload before knowing the final object size

**S3 Transfer Acceleration**
- Fast, secure transfers over long distances
- Uses globally distributed CloudFront edge locations
- Speed improvement: 50-500% for cross-country transfers
- Requires bucket configuration

**AWS Transfer Family**
- Fully managed service for file transfers
- **Supported Protocols**:
  - SFTP (SSH File Transfer Protocol)
  - FTPS (FTP Secure)
  - FTP (File Transfer Protocol)
  - AS2 (Applicability Statement 2)

**Transfer Family Benefits**
- Managed service scaling in real time
- No application modifications required
- No file transfer protocol infrastructure to manage
- Pay-per-use pricing, no upfront costs

**Transfer Family Use Cases**
- **Amazon S3**: Data lakes, third-party uploads, data distribution
- **Amazon EFS**: Data distribution, supply chain, content management

---

**4. Storing Content with Amazon S3**

**Storage Classes**

**General Purpose**
- **S3 Standard**: Frequently accessed data
- **S3 Intelligent-Tiering**: Automatic cost optimization

**Infrequent Access**
- **S3 Standard-IA**: Infrequently accessed, rapid access needed
- **S3 One Zone-IA**: Infrequently accessed, single AZ storage

**Archive**
- **S3 Glacier Instant Retrieval**: Archive, milliseconds retrieval
- **S3 Glacier Flexible Retrieval**: Archive, minutes to hours retrieval
- **S3 Glacier Deep Archive**: Lowest cost, 12+ hours retrieval

**Specialized**
- **S3 on Outposts**: On-premises object storage

**Amazon S3 Lifecycle Configuration**
- Set of rules defining actions applied to object groups
- **Transition Actions**: Move to different storage classes
- **Expiration Actions**: Define when objects expire

**Lifecycle Examples**
- Move from Standard to Standard-IA after 30 days
- Move to Glacier after 90 days
- Permanently delete after 365 days

**Amazon S3 Versioning**

**Versioning Benefits**
- Protection from accidental overwrites and deletes
- All versions preserved with unique version IDs

**Versioning Operations**
- **PUT**: New version ID generated, original version preserved
- **DELETE**: Delete marker added, all versions preserved
- **GET**: Returns most recent version (excluding delete markers)
- **Specific Version GET**: Request with version ID returns specific version
- **Permanent Delete**: DELETE with version ID (no recovery)

**Cross-Origin Resource Sharing (CORS)**
- Enables web applications in one domain to interact with resources in different domains
- Required for web applications accessing S3 from different domains

**Data Consistency Model**
- Consistent for all new and existing objects in all Regions
- Read-after-write consistency for GET, LIST, PUT, DELETE operations
- Advantage for big data workloads
- Simplifies migration of on-premises analytics workloads

---

**5. Designing with Amazon S3**

**Security Configurations**

**Default Security**
- Buckets and objects are private by default
- Default encryption: SSE-S3 (Server-Side Encryption with S3 Managed Keys)

**Data Sharing Principles**
- Manage and control data access explicitly
- Follow a principle of least privilege

**Encryption Options**

**Server-Side Encryption**
- S3 encrypts objects before saving to disk
- Enable via a default encryption option on the bucket
- **Options**: SSE-S3, SSE-KMS, SSE-C

**Client-Side Encryption**
- Data an encrypted on a client before upload
- Client manages encryption keys and a process

**Access Configuration Approaches**
1. **Default**: Private, explicit access policies required
2. **Access Policy Applied**: IAM policies, bucket policies
3. **Public Access**: Carefully controlled public access

**Region Selection Considerations**
- Data privacy laws and regulatory compliance
- Proximity of users to data
- Service and feature availability
- Cost-effectiveness

**Amazon S3 Inventory**
- Audit and report on replication and encryption status
- Business, compliance, and regulatory needs
- Scheduled alternative to synchronous List API operations
- Speed up business workflows and big data jobs

**Cost Structure**
- **Charged For**:
  - GB-month storage (varies by Region and storage class)
  - PUT, COPY, POST, LIST requests
  - Lifecycle transitions
- **No Charge For**:
  - First 100 GB/month out to the  internet
  - Data transferred in from the internet
  - Data transferred between S3 buckets in the same Region
  - Data transferred to services in the same Region
  - Data transferred out to CloudFront

---

**6. Practical Implementation Examples**

**Cafe Scenario: Employee Document Access**
**Requirements**: Cooking instructions, vendor information, payroll, and compliance training accessible only to employees

**Solution**:
- Create in S3 bucket with default private access
- Implement IAM policies for employee access control
- Use groups for different access levels (HR, kitchen staff, management)
- Enable versioning for document protection
- Implement lifecycle policies for cost optimization

**Static Website Configuration**
1. Create in S3 bucket with public read access
2. Configure bucket for static website hosting
3. Upload HTML, CSS, JavaScript files
4. Set bucket policy for public read access
5. Configure custom domain (optional)

**Data Protection Strategies**
- Enable versioning to prevent accidental deletion
- Implement lifecycle policies for cost management
- Use Cross-Region Replication for disaster recovery
- Configure default encryption
- Implement access logging

---

**7. Applying Well-Architected Framework to Storage**

**Security Pillar: Data Protection**
- **Best Practices**:
  - Enforce encryption at rest
  - Enforce access control
  - Use versioning for accidental deletion protection

**Performance Efficiency: Architecture Selection**
- **Best Practices**:
  - Understand available cloud services and features
  - Factor cost into architectural decisions
  - Use Transfer Acceleration and multipart upload for performance

**Cost Optimization: Resource Selection**
- **Best Practice**: Perform cost analysis for different usage over time
- **S3 Features**: Lifecycle policies, intelligent tiering, S3 Inventory

**Reliability: Failure Management**
- **Best Practice**: Use fault isolation in multi-location deployments
- **S3 Features**: High durability, cross-region replication, versioning

**Operational Excellence: Monitoring and Management**
- **Tools**: S3 Inventory, access logs, CloudWatch metrics
- **Practices**: Regular audits, lifecycle management, access reviews

---

**8. Certification Exam Strategy**

**Common Question Patterns**

**Scenario: Data Protection**
**Key Indicators**:
- "Accidentally delete or overwrite"
- "Protect against unintended user actions"
- "Salespeople uploading daily reports"

**Correct Approach**: Enable S3 Versioning

**Scenario: Cost Optimization**
**Key Indicators**:
- "Infrequently accessed data"
- "Reduce storage costs"
- "Long-term retention"

**Correct Approach**: Implement Lifecycle Policies to transition to appropriate storage classes

**Scenario: Performance**
**Key Indicators**:
- "Large file uploads"
- "Cross-country transfers"
- "Slow upload speeds"

**Correct Approach**: Use Multipart Upload and/or Transfer Acceleration

**Critical Exam Concepts**

**Storage Class Selection**
- **Frequently accessed**: S3 Standard
- **Infrequent access, rapid retrieval**: S3 Standard-IA
- **Archive, immediate retrieval**: Glacier Instant Retrieval
- **Archive, flexible retrieval**: Glacier Flexible Retrieval
- **Lowest cost archive**: Glacier Deep Archive

**Security Defaults**
- Buckets are private by default
- SSE-S3 encryption enabled by default
- Explicit access policies on required for any access

**Data Transfer Costs**
- No charge for data transfer into S3
- No charge for data transfer between S3 and other services in the same Region
- Charges apply for data transfer out to the internet (after 100 GB/month)

---

**9. Hands-On Lab Focus**

### **Cafe Website Lab Tasks**
1. Create S3 bucket for static website hosting
2. Upload website content to S3 bucket
3. Create bucket policy for public read access
4. Enable versioning on S3 bucket
5. Set lifecycle policies
6. Enable a cross-Region replication

**Key Learning Points**
- **Versioning Protection**: Prevents accidental overwrite and deletion
- **Cost Strategy**: Lifecycle policies to manage costs as the website grows
- **Disaster Recovery**: Cross-region replication for business continuity

---

**Key Success Factors**

**Certification Focus**
- Understand S3 storage classes and their appropriate use cases
- Know security defaults and how to configure access
- Recognize cost optimization strategies (lifecycle policies, storage classes)
- Understand data protection mechanisms (versioning, replication)

**Practical Implementation**
- Implement least privilege access controls
- Use versioning for critical data protection
- Configure lifecycle policies for cost optimization
- Enable appropriate encryption based on sensitivity

**Architecture Considerations**
- Choose appropriate storage classes based on access patterns
- Implement cross-region replication for critical data
- Use Transfer Acceleration for large file transfers over long distances
- Monitor costs and adjust lifecycle policies as needed

---
