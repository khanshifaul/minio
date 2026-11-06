# 🚀 MinIO Docker Setup : Guide

This guide makes your local MinIO setup super smooth, with all the essentials + pro tips for dev workflows.

---

## ✅ What You’re Setting Up

A production‑grade object storage service (S3 compatible) running fully local using Docker Compose.

MinIO gives you:

* **Object storage** (images, videos, files, backups, etc.)
* **S3 APIs** (works with AWS SDKs)
* **Admin console** (web UI)
* **Buckets, policies, access keys**, all locally

---

## 🛠️ docker-compose.yml (Drop‑in Working Config)

```yaml
services:
  minio:
    image: quay.io/minio/minio
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin
    volumes:
      - ./minio_data:/data
    ports:
      - "9000:9000"
      - "9001:9001"
```

---

## ▶️ Start / Stop / Restart

Start:

```
docker compose up -d
```

Stop:

```
docker compose down
```

Restart (fresh):

```
docker compose down && docker compose up -d
```

Check running containers:

```
docker ps
```

---

## 🌐 Access URLs

* **MinIO API (S3)** → [http://localhost:9000](http://localhost:9000)
* **MinIO Console (UI)** → [http://localhost:9001](http://localhost:9001)

Default login:

```
Username: minioadmin
Password: minioadmin
```

---

## 📁 Folder Structure

```
project/
 ├─ docker-compose.yml
 └─ minio_data/   # your buckets & files stored here
```

You can delete `minio_data/` anytime for a full reset.

---

## 🪣 Create a Bucket (via UI)

1. Go to **[http://localhost:9001](http://localhost:9001)**
2. Login → `minioadmin / minioadmin`
3. Click **Buckets**
4. **Create bucket** → add name → save

---

## 🧪 Upload a File (UI)

1. Select your bucket
2. Press **Upload**
3. Drag & drop any file

---

## ⚙️ Generate Access Keys (Programmatic Access)

1. Go to **Identity → Users**
2. Create a new user
3. Save the **Access Key** + **Secret Key**
4. Assign a policy (example: `readwrite`)

Use these keys in your backend or SDK (Node, Python, etc.).

---

## 🧰 S3-Compatible Client Examples

### Node.js (AWS SDK v3)

```js
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";

const s3 = new S3Client({
  endpoint: "http://localhost:9000",
  region: "us-east-1",
  credentials: {
    accessKeyId: "minioadmin",
    secretAccessKey: "minioadmin",
  },
  forcePathStyle: true,
});

await s3.send(new PutObjectCommand({
  Bucket: "my-bucket",
  Key: "test.txt",
  Body: "Hello MinIO!",
}));
```

---

## 🔐 Useful Policies

Built‑in policies:

* `readwrite`
* `readonly`
* `writeonly`
* `consoleAdmin`

Custom example (public read):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": ["s3:GetObject"],
      "Resource": ["arn:aws:s3:::my-bucket/*"]
    }
  ]
}
```

---

## 🧹 Reset Everything

Delete containers + data:

```
docker compose down
rm -rf minio_data
```

Start again:

```
docker compose up -d
```

---

## 🦾 Power Tips (Cheat Sheet)

### Change Root Credentials

```yaml
MINIO_ROOT_USER: rootUserHere
MINIO_ROOT_PASSWORD: ultraSecretPasswordHere
```

### Create multiple disks

```yaml
command: server /data1 /data2 --console-address ":9001"
```

### Health Check

```
curl http://localhost:9000/minio/health/live
```

### List buckets via CLI

```
mc alias set local http://localhost:9000 minioadmin minioadmin
mc ls local
```

---
