# 🌩️ Project: IAM Access Denied Case Study (S3)

A user cannot list or read objects in an S3 bucket despite having an IAM policy that “should” allow it.
This case study documents the investigation, root causes, and resolution.

---

## 🎯 Root Causes

- Bucket policy missing s3:ListBucket

- Incorrect S3 resource ARN (wrong bucket name, missing wildcard, wrong path)

---

## 🧰 Tools and Technologies Used

- AWS IAM – reviewing user/role policies

- Amazon S3 – bucket permissions and access control

- AWS CloudTrail – identifying authorization failures

- AWS CLI – reproducing and validating the issue

---

## 📂 File Structure

iam-s3-access-issue/
├── README.md                # This documentation
├── architecture-diagram.txt
├── policies/
│   ├── broken-policy.json   # Incorrect IAM policy
│   └── fixed-policy.json    # Corrected IAM policy
├── troubleshooting-steps.md # Step-by-step investigation
└── lessons-learned.md       # Key takeaways

---

## 🏗️ Architecture Diagram

   [User]
      |
      v
 [IAM Policy] ----> allows s3:GetObject
      |
      v
 [Bucket Policy] ----X----> denies ListBucket or mismatched ARN
      |
      v
 [S3 Bucket]

---

### 🧪 Try It Yourself

1. Create an S3 bucket:
Name it something simple like iam-access-demo-bucket.

2. Attach the broken IAM policy to a test IAM user or role:
Use the JSON in policies/broken-policy.json.

3. Attempt to list objects:

     aws s3 ls s3://iam-access-demo-bucket

4. Observe the AccessDenied error
This reproduces the scenario.

### 🧹 How to Destroy Everything

- Delete the IAM user/role used for testing

- Remove the inline policy

- Delete the S3 bucket

🧹 This ensures no leftover resources or permissions remain.

---

## 🛡️ Prevention Checklist

- Confirm the bucket ARN is correct: arn:aws:s3:::bucket-name

- Confirm the object ARN includes the wildcard: arn:aws:s3:::bucket-name/*

- Include s3:ListBucket when listing objects is required

- Separate bucket‑level and object‑level permissions into distinct statements

- Check the bucket policy for:

- explicit denies

- restrictive conditions (e.g., aws:SecureTransport)

- missing principals

- Validate access using both the AWS CLI and the AWS Console

- Use CloudTrail to confirm which ARN AWS is evaluating during the failed request

- Double‑check bucket names for typos, case sensitivity, and environment suffixes

- Ensure the correct IAM principal (user or role) is the one making the request

- Re‑test after applying changes to confirm the fix is complete

---

## 📖 Lessons learned

- S3 requires two different ARNs: one for the bucket, one for objects.

- ListBucket is required even if you only want to read objects.

- IAM policies can appear correct while the bucket policy silently denies access.

- CloudTrail reveals which ARN AWS is evaluating — essential for debugging.

- Typos in bucket names are extremely common and easy to miss.

- S3 ARNs are global and do not include regions.

- Always validate with the AWS CLI after applying a fix.

---
