# Importing Key Material in AWS Key Management Service \(AWS KMS\)<a name="importing-keys"></a>

A customer master key \(CMK\) is a logical representation of a master key in AWS KMS\. In addition to the master key's identifiers and other metadata including its creation date, description, and [key state](key-state.md), a CMK contains the *key material* used to encrypt and decrypt data\. When you [create a CMK](create-keys.md), by default AWS KMS generates the key material for that CMK\. But you can create a CMK without key material and then import your own key material into that CMK\.

When you use imported key material, you remain responsible for the key material while allowing AWS KMS to use a copy of it\. You might choose to do this for one or more of the following reasons:
+ To prove that you generated the key material using a source of entropy that meets your requirements\.
+ To use key material from your own infrastructure with AWS services, and to use AWS KMS to manage the lifecycle of that key material within AWS\.
+ To set an expiration time for the key material in AWS and to [manually delete it](importing-keys-delete-key-material.md), but to also make it available again in the future\. In contrast, [scheduling key deletion](deleting-keys.md#deleting-keys-how-it-works) requires a waiting period of 7 to 30 days, after which you cannot recover the deleted CMK\.
+ To own the original copy of the key material, and to keep it outside of AWS for additional durability and disaster recovery during the complete lifecycle of the key material\.

For information about important differences between CMKs with imported key material and those with key material generated by AWS KMS, see [About Imported Key Material](#importing-keys-considerations)\.

The key material you import must be a 256\-bit symmetric encryption key\.

**Topics**
+ [About Imported Key Material](#importing-keys-considerations)
+ [How To Import Key Material](#importing-keys-overview)
+ [How to Reimport Key Material](#reimport-key-material)
+ [How to Identify CMKs with Imported Key Material](#identify-imported-keys)

## About Imported Key Material<a name="importing-keys-considerations"></a>

Before you decide to import key material into AWS KMS, you should understand the following characteristics of imported key material\.

**Secure key generation**  
You are responsible for generating the key material using a source of randomness that meets your security requirements\.

**One key per CMK**  
When you import key material into a CMK, the CMK is permanently associated with that key material\. You can [reimport the same key material](#reimport-key-material), but you cannot import different key material into that CMK\. Also, you cannot [enable automatic key rotation](rotate-keys.md) for a CMK with imported key material\. However, you can [manually rotate a CMK](rotate-keys.md#rotate-keys-manually) with imported key material\. 

**One CMK per ciphertext**  
When you encrypt data under a KMS CMK, the ciphertext cannot be decrypted with any other CMK\. This is true even when you import the same key material into a different CMK\.

**Availability and durability**  
You are responsible for the key material's overall availability and durability\. AWS KMS is designed to keep imported key material highly available\. But the service does not maintain the durability of imported key material at the same level as key material generated on your behalf\. This difference is meaningful in the following cases:
+ When you set an expiration time for your imported key material, AWS KMS deletes the key material after it expires\. AWS KMS does not delete the CMK or its metadata\. You cannot set an expiration time for key material generated by AWS KMS\.
+ When you [manually delete imported key material](importing-keys-delete-key-material.md), AWS KMS deletes the key material but does not delete the CMK or its metadata\. In contrast, [scheduling key deletion](deleting-keys.md#deleting-keys-how-it-works) requires a waiting period of 7 to 30 days, after which AWS KMS deletes the key material and all of the CMK's metadata\.
+ In the unlikely event of certain regionwide failures that affect the service \(such as a total loss of power\), AWS KMS cannot automatically restore your imported key material\. However, AWS KMS can restore the CMK and its metadata\.

To restore the key material after events like these, you must retain a copy of the key material in a system that you control\. Then, you can reimport it into the CMK\.

## How To Import Key Material<a name="importing-keys-overview"></a>

The following overview explains how to import your key material into AWS KMS\. For more details about each step in the process, see the corresponding topic\.

1. [Create a CMK with no key material](importing-keys-create-cmk.md) – To get started with importing key material, first create a CMK whose *origin* is `EXTERNAL`\. This indicates that the key material was generated outside of AWS KMS and prevents AWS KMS from generating key material for the CMK\. In a later step you will import your own key material into this CMK\.

1. [Download the public key and import token](importing-keys-get-public-key-and-token.md) – After completing step 1, download a public key and an import token\. These items protect the import of your key material to AWS KMS\.

1. [Encrypt the key material](importing-keys-encrypt-key-material.md) – Use the public key that you downloaded in step 2 to encrypt the key material that you created on your own system\.

1. [Import the key material](importing-keys-import-key-material.md) – Upload the encrypted key material that you created in step 3 and the import token that you downloaded in step 2\.

## How to Reimport Key Material<a name="reimport-key-material"></a>

If you manage a CMK with imported key material, you might need to reimport the key material, either because the key material expired, or because the key material was accidentally deleted or lost\. 

You must reimport the same key material that was originally imported into the CMK\. You cannot import different key material into a CMK\. Also, AWS KMS cannot create key material for a CMK that is created without key material\.

To reimport key material, use the same procedure that you used to [import the key material](#importing-keys-overview) the first time, with the following exceptions\.
+ Use an existing CMK, instead of creating a new CMK\. You can skip [Step 1](importing-keys-create-cmk.md) of the import procedure\.
+ If the CMK contains key material, you must [delete the existing key material](importing-keys-delete-key-material.md) before you reimport the key material\. 

Each time you import key material to a CMK, you need to [download and use a new wrapping key and import token](importing-keys-get-public-key-and-token.md) for the CMK\. The wrapping procedure does not affect the content of the key material, so you can use different wrapping keys \(and different import tokens\) to import the same key material\.

## How to Identify CMKs with Imported Key Material<a name="identify-imported-keys"></a>

When you create a CMK with no key material, the value of the `Origin` property of the CMK is `EXTERNAL`, and it cannot be changed\. You cannot convert a key that is designed to use imported key material to one that uses the key material that AWS KMS provides\.

You can identify CMKs that require imported key material in the AWS KMS console or by using the AWS KMS API\.

### To identify the value of the `Origin` property of CMKs \(Console\)<a name="identify-imported-keys-console"></a>

1. Open the AWS KMS console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. Use either of the following techniques to view the `Origin` property of your CMKs\.
   + To add an **Origin** column to your CMK table, in the upper right corner, choose the **Settings** icon\. Choose **Origin** and choose **Confirm**\. The **Origin** column makes it easy to identify CMKs with an `EXTERNAL` origin property value\.
   + To find the value of the `Origin` property of a particular CMK, choose the key ID or alias of the CMK\. The `Origin` property value appears in the **General configuration** section\.

### To identify the value of the `Origin` property of CMKs \(KMS API\)<a name="identify-imported-keys-api"></a>

Use the [DescribeKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html) operation\. The response includes the `Origin` property of the CMK, as shown in the following example\.

```
$ aws kms describe-key --key-id 1234abcd-12ab-34cd-56ef-1234567890ab
{
    "KeyId": "1234abcd-12ab-34cd-56ef-1234567890ab",
    "Origin": "EXTERNAL",
    "KeyManager": "CUSTOMER",
    "ValidTo": 1549224000.0,
    "Enabled": true,
    "AWSAccountId": "111122223333",
    "Arn": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab",
    "CreationDate": 1517867689.949,
    "KeyUsage": "ENCRYPT_DECRYPT",
    "Description": "example-key",
    "KeyState": "Enabled",
    "ExpirationModel": "KEY_MATERIAL_EXPIRES"
}
```