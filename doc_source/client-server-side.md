# Client\-Side or Server\-Side Encryption?<a name="client-server-side"></a>

The DynamoDB Encryption Client supports *client\-side encryption*, where you encrypt your table data before you send it to DynamoDB\. However, DynamoDB supports an encryption at rest feature that transparently encrypts your table when it is persisted to disk and decrypts it when you access the table\. 

The tools that you choose depend on the sensitivity of your data and the security requirements of your application\. 

You can also use the DynamoDB Encryption Client and encryption at rest together for special cases\. When you send encrypted and signed items to DynamoDB, DynamoDB does not recognize the items as being protected\. It just detects typical table items with binary attribute values\. Those table items can be part of an encrypted or unencrypted table\.

**Encryption at Rest**

DynamoDB offers [encryption at rest](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/EncryptionAtRest.html), a *server\-side encryption* option in which DynamoDB transparently encrypts your tables for you when the table is persisted to disk, and decrypts them when you access the table data\. 
+ It's easy to use\. Just select the encryption option when you create a table\. \(You cannot change the option on an existing table\.\) DynamoDB transparently encrypts and decrypts the table for you\.

   
+ DynamoDB creates and manages the cryptographic keys\. The unique key for each table is protected by an [AWS Key Management Service](http://docs.aws.amazon.com/kms/latest/developerguide/) \(AWS KMS\) customer master key that never leaves AWS KMS unencrypted\.

   
+ All table data is encrypted on disk\. When an encrypted table is saved to disk, DynamoDB encrypts all table data, including the primary key and local and global secondary indexes\. BUGBUG:\(In rare instances, attributes in metadata are hashed, but not encrypted\.\)

   
+ The entire table is decrypted when you access it\. When you access the table\. DynamoDB decrypts the entire table and keeps the plaintext data in memory\. 

**DynamoDB Encryption Client**

Client\-side encryption is designed to protect your data at its source\. Your plaintext data is never exposed to any third party, including AWS\. However, you need to add the encryption features to your DynamoDB applications\. 
+ Your data is protected in transit and at rest\. It is never exposed to any third party, including AWS\.

   
+ You choose how your cryptographic keys are generated and protected\. You can create and manage them yourself, or use a cryptographic service, such as AWS Key Management Service or [AWS CloudHSM](http://docs.aws.amazon.com/cloudhsm/latest/userguide/), to generate and protect your keys\.

   
+ You determine how your data is protected by [selecting a cryptographic materials provider](crypto-materials-providers.md) \(CMP\), or writing one of your own\. The CMP determines the encryption strategy used, including when unique keys are generated, and the encryption and signing algorithms that are used\.
+ The DynamoDB Encryption Client does not encrypt the entire table\. It encrypts the values of [specified attributes](concepts.md#attribute-actions) in a table item, but not the attribute names\. By default, it does not encrypt the names or values of the primary key \(partition key and sort key\)\. It also signs the item so you can detect unauthorized changes to the item as whole\.

   

**AWS Encryption SDK**

You might also consider using the [AWS Encryption SDK](http://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/) to implement client\-side encryption and decryption of your data\. The AWS Encryption SDK can protect any type of data\. However, it is not designed to work with database records\. It has no special logic to recognize attributes or prevent encryption of primary keys, and it has no features to make signing the item easy to implement\. We recommend the DynamoDB Encryption Client for data that you store in DynamoDB\.

If you use the AWS Encryption SDK to encrypt any element of your table, remember that it is not compatible with the DynamoDB Encryption Client\. You cannot encrypt with one library and decrypt with the other\.