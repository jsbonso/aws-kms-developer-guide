# Using a Custom Key Store<a name="custom-key-store-overview"></a>

AWS KMS supports [custom key stores](key-store-concepts.md#concept-custom-key-store) backed by [AWS CloudHSM clusters](https://docs.aws.amazon.com/cloudhsm/latest/userguide/)\. When you create an AWS KMS [customer master key](concepts.md#master_keys) \(CMK\) in a custom key store, AWS KMS generates and stores non\-extractable key material for the CMK in an AWS CloudHSM cluster that you own and manage\. When you use a CMK in a custom key store, the cryptographic operations are performed in the HSMs in the cluster\. This feature combines the convenience and widespread integration of AWS KMS with the added control of an AWS CloudHSM cluster in your AWS account\. 

AWS KMS provides full console and API support for creating, using, and managing your custom key stores\. When you create CMKs in a custom key store, you can use them just as you would any CMK\. For example, you can use the CMKs to generate data keys and encrypt data\. You can also use the CMKs in your custom key store with AWS services that support customer managed CMKs\. 

**Do I need a custom key store?**

For most users, the default AWS KMS key store, which is protected by [FIPS 140\-2 validated cryptographic modules](https://csrc.nist.gov/projects/cryptographic-module-validation-program/Certificate/3139), fulfills their security requirements\. There is no need to add an extra layer of maintenance responsibility or a dependency on an additional service\. 

However, you might consider creating a custom key store if your organization has any of the following requirements:
+ Key material cannot be stored in a shared environment\.
+ Key material must be backed up in multiple AWS Regions\.
+ Key material must be subject to a secondary, independent audit path\.
+ The HSMs that generate and store key material must be certified at [FIPS 140\-2 Level 3](https://docs.aws.amazon.com/cloudhsm/latest/userguide/compliance.html)\.

**How do custom key stores work?**

Each custom key store is associated with an AWS CloudHSM cluster in your AWS account\. When you connect the custom key store to its cluster, AWS KMS creates the network infrastructure to support the connection\. Then it logs into the key AWS CloudHSM client in the cluster using the credentials of a [dedicated crypto user](key-store-concepts.md#concept-kmsuser) in the cluster\.

You create and manage your custom key stores in AWS KMS and create and manage your HSM clusters in AWS CloudHSM\. When you create customer master keys \(CMKs\) in an AWS KMS custom key store, you view and manage the CMKs in AWS KMS\. But you can also view and manage their key material in AWS CloudHSM, just as you would do for other keys in the cluster\.

![\[Managing CMKs in a custom key store\]](http://docs.aws.amazon.com/kms/latest/developerguide/images/kms-hsm-view.png)

You can use the same techniques to view and manage the CMKs in your custom key store that you use for CMKs in the AWS KMS key store\. You can control access with IAM and key policies, create tags and aliases, enable and disable the CMKs, and schedule key deletion\. You can use the CMKs for cryptographic operations and use them with AWS services that integrate with AWS KMS\. However, you cannot enable automatic key rotation and you cannot import key material into a CMK in a custom key store\.

In addition, you have full control over the AWS CloudHSM cluster, including creating and deleting HSMs and managing backups\. You can use the AWS CloudHSM client and supported software libraries to view, audit, and manage the key material for your CMKs\. While the custom key store is disconnected, AWS KMS cannot access it, and users cannot use the CMKs in the custom key store for cryptographic operations\. This added layer of control makes custom key stores a powerful solution for organizations that require it\.

**Where Do I Start?**

To create and manage a custom key store, you use features of AWS KMS and AWS CloudHSM\.

1. Start in AWS CloudHSM\. [Create an active AWS CloudHSM cluster](https://docs.aws.amazon.com/cloudhsm/latest/userguide/getting-started.html) or select an existing cluster\. The cluster must have at least two active HSMs in different Availability Zones\. Then create a [dedicated crypto user \(CU\) account](key-store-concepts.md#concept-kmsuser) in that cluster for AWS KMS\. 

1. In AWS KMS, [create a custom key store](create-keystore.md) that is associated with your selected AWS CloudHSM cluster\. AWS KMS provides [a complete management interface](manage-keystore.md) that lets you create, view, edit, and delete your custom key stores\.

1. When you're ready to use your custom key store, [connect it to its associated AWS CloudHSM cluster](disconnect-keystore.md)\. AWS KMS creates the network infrastructure that it needs to support the connection\. It then logs in to the cluster using the dedicated crypto user account credentials so it can generate and manage key material in the cluster\.

1. Now, you can [create customer master keys \(CMKs\) in your custom key store](create-cmk-keystore.md)\. Just specify the custom key store when you create the CMK\.

If you get stuck at any point, you can find help in the [Troubleshooting a Custom Key Store](fix-keystore.md) topic\. If your question is not answered, use the feedback link at the bottom of each page of this guide or post a question on the [AWS Key Management Service Discussion Forum](https://forums.aws.amazon.com/forum.jspa?forumID=182)\.

**Limits**

There are no limits on the number of custom key stores in an AWS account or region\. However, there are [limits on the number of AWS CloudHSM clusters in each AWS region](https://docs.aws.amazon.com/cloudhsm/latest/userguide/limits.html), and [throttle limits on the rate of cryptographic operations](limits.md#requests-per-second) using the CMKs in each custom key store\.

**Regions**

AWS KMS supports custom key stores in all AWS Regions where both AWS KMS and AWS CloudHSM are available, except for Asia Pacific \(Hong Kong\), EU \(Stockholm\), Middle East \(Bahrain\), AWS GovCloud \(US\-East\), and AWS GovCloud \(US\-West\)\. For a list of AWS Regions that each service supports, see [AWS Key Management Service](https://docs.aws.amazon.com/general/latest/gr/rande.html#kms_region) and [AWS CloudHSM](https://docs.aws.amazon.com/general/latest/gr/rande.html#cloudhsm_region)\.

**Topics**
+ [What is a Custom Key Store?](key-store-concepts.md)
+ [Controlling Access to Your Custom Key Store](authorize-key-store.md)
+ [Creating a Custom Key Store](create-keystore.md)
+ [Managing a Custom Key Store](manage-keystore.md)
+ [Managing CMKs in a Custom Key Store](manage-cmk-keystore.md)
+ [Troubleshooting a Custom Key Store](fix-keystore.md)