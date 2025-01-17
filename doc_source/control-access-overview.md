# Overview of Managing Access to Your AWS KMS Resources<a name="control-access-overview"></a>

Every AWS resource belongs to an AWS account, and permissions to create or access the resources are defined in permissions policies in that account\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\), and some services \(including AWS KMS\) also support attaching permissions policies to other kinds of resources\.

**Note**  
An *account administrator* \(or administrator user\) is a user with administrator permissions\. For more information, see [Creating an Admin User and Group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide*\.

When managing permissions, you decide who gets the permissions, the resources they get permissions for, and the specific actions allowed\.

**Topics**
+ [AWS KMS Resources and Operations](#kms-resources-operations)
+ [Managing Access to AWS KMS CMKs](#managing-access)
+ [Specifying Permissions in a Policy](#overview-policy-elements)
+ [Specifying Conditions in a Policy](#overview-policy-conditions)

## AWS KMS Resources and Operations<a name="kms-resources-operations"></a>

To manage permissions, you should understand some basic information about resources and operations\. In AWS KMS, the primary resource type is a *customer master key \(CMK\)*\. AWS KMS also supports another resource type you can use with CMKs: an *alias*\. An alias is a friendly name that points to a CMK\. Some AWS KMS operations allow you to specify a CMK by its alias\.

These resource types have unique Amazon Resource Names \(ARNs\) associated with them, as shown in the following list\.
+ **Customer master key \(CMK\)**

  ARN format:

  `arn:aws:kms:AWS region:AWS account ID:key/CMK key ID`

  Example ARN:

  `arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab`

   
+ **Alias**

  ARN format:

  `arn:aws:kms:AWS region:AWS account ID:alias/alias name`

  Example ARN:

  `arn:aws:kms:us-west-2:111122223333:alias/example-alias`

AWS KMS provides a set of API operations to work with your AWS KMS resources\. For a list of available operations and the resources affected by each operation, see [AWS KMS API Permissions Reference](kms-api-permissions-reference.md)\.

## Managing Access to AWS KMS CMKs<a name="managing-access"></a>

The primary way to manage access to your AWS KMS CMKs is with *policies*\. Policies are documents that describe who has access to what\. Policies attached to an IAM identity are called *identity\-based policies* \(or *IAM policies*\), and policies attached to other kinds of resources are called *resource\-based policies*\. In AWS KMS, you must attach resource\-based policies to your customer master keys \(CMKs\)\. These are called *key policies*\. All KMS CMKs have a key policy\.

You can control access to your KMS CMKs in these ways:
+ **Use the key policy** – You must use the key policy to control access to a CMK\. You can use the key policy alone to control access, which means the full scope of access to the CMK is defined in a single document \(the key policy\)\.

   
+ **Use IAM policies in combination with the key policy** – You can use IAM policies in combination with the key policy to control access to a CMK\. Controlling access this way enables you to manage all of the permissions for your IAM identities in IAM\.

   
+ **Use grants in combination with the key policy** – You can use grants in combination with the key policy to allow access to a CMK\. Controlling access this way enables you to allow access to the CMK in the key policy, and to allow users to delegate their access to others\.

For most AWS services, IAM policies are the only way to control access to the service's resources\. Some services offer resource\-based policies or other access control mechanisms to complement IAM policies, but these are generally optional and you can control access to the resources in these services with only IAM policies\. This is not the case for AWS KMS, however\. To allow access to a KMS CMK, you must use the key policy, either alone or in combination with IAM policies or grants\. IAM policies by themselves are not sufficient to allow access to a CMK, though you can use them in combination with a CMK's key policy\.

For more information about using key policies, see [Using Key Policies](key-policies.md)\.

For more information about using IAM policies, see [Using IAM Policies](iam-policies.md)\.

For more information about using grants, see [Using Grants](grants.md)\.

## Specifying Permissions in a Policy<a name="overview-policy-elements"></a>

AWS KMS provides a set of API operations\. To control access to these API operations, AWS KMS provides a set of *actions* that you can specify in a policy\. For more information, see [AWS KMS API Permissions Reference](kms-api-permissions-reference.md)\.

A policy is a document that describes a set of permissions\. The following are the basic elements of a policy\.
+ **Resource** – In an IAM policy, you use an Amazon Resource Name \(ARN\) to specify the resource that the policy applies to\. For more information, see [AWS KMS Resources and Operations](#kms-resources-operations)\. In a key policy, you use `"*"` for the resource, which effectively means "this CMK\." A key policy applies only to the CMK it is attached to\.

   
+ **Action** – You use actions to specify the API operations you want to allow or deny\. For example, the `kms:Encrypt` action corresponds to the AWS KMS [Encrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html) API operation\.

   
+ **Effect** – You use the effect to specify whether to allow or deny the permissions\. If you don't explicitly allow access to a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user cannot access it, even when a different policy allows access\.

   
+ **Principal** – In an IAM policy, you don't specify a principal\. Instead, the identity \(the IAM user, group, or role\) that the policy is attached to is the implicit principal\. In a key policy, you must specify the principal \(the identity\) that the permissions apply to\. You can specify AWS accounts \(root\), IAM users, IAM roles, and some AWS services as principals in a key policy\. IAM groups are not valid principals in a key policy\.

For more information, see [Using Key Policies](key-policies.md) and [Using IAM Policies](iam-policies.md)\.

## Specifying Conditions in a Policy<a name="overview-policy-conditions"></a>

You can use another policy element called the *condition* to specify the circumstances in which a policy takes effect\. For example, you might want a policy statement to take effect only after a specific date\. Or, you might want a policy statement to control access based on whether a specific value exists in the API request\.

To specify conditions, you use predefined *condition keys*\. Some condition keys apply generally to AWS, and some are specific to AWS KMS\. For more information, see [Using Policy Conditions](policy-conditions.md)\.