# Troubleshooting Steps: IAM Access Denied (S3)

## 1. Reproduce the Issue

Run:
aws s3 ls s3://my-demo-bucket

If the response is:
"An error occurred (AccessDenied) when calling the ListObjectsV2 operation"
the issue is confirmed.

---

## 2. Inspect the IAM Policy

Navigate to:
IAM → Users/Roles → Permissions → JSON

Look for:

- Missing s3:ListBucket
- Incorrect Resource ARN
- Missing wildcard for object access

Common broken example:
Resource: "arn:aws:s3:::my-demo-bucket"

---

## 3. Inspect the Bucket Policy

Navigate to:
S3 → Bucket → Permissions → Bucket Policy

Check for:

- Explicit denies
- Missing principals
- Conditions blocking access (e.g., aws:SecureTransport)

---

## 4. Check CloudTrail for Authorization Failures

Navigate to:
CloudTrail → Event History → Filter: Event Name = ListObjectsV2

Look for:

- errorCode: AccessDenied
- userIdentity (which role/user failed)
- resources[].ARN (the ARN AWS thinks you're accessing)

This is where incorrect ARNs become obvious.

---

## 5. Validate the ARN Format

Correct ARNs:

- Bucket: arn:aws:s3:::my-demo-bucket
- Objects: arn:aws:s3:::my-demo-bucket/*

Incorrect ARN examples:

- Missing wildcard
- Wrong bucket name
- Extra slash
- Using object ARN for ListBucket
- Using bucket ARN for GetObject

---

## 6. Apply the Fix

Use the corrected policy in policies/fixed-policy.json.

---

## 7. Validate the Fix

### Using AWS CLI

Run:
aws s3 ls s3://my-demo-bucket
aws s3 cp s3://my-demo-bucket/test.txt .

If both commands succeed, the IAM permissions are correctly applied.

### Using the AWS Console

You can also validate the fix through the AWS Console:

1. Go to S3 → your bucket.
2. Confirm you can view the list of objects.
3. Upload a small test file to verify write access (if applicable).
4. Download an object to confirm read access.

If both CLI and Console access work as expected, the issue is fully resolved.
