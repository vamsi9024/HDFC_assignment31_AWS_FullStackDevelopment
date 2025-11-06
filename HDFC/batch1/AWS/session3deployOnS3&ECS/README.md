
# 🧩 AWS Full-Stack Deployment (ECR + ECS + S3)

## 🎯 Objective

Deploy a **Full-Stack Application** with:

* **Backend** on AWS ECS (Fargate) using an image from **ECR**
* **Frontend** on AWS S3 with static website hosting

---

## 🏗️ Architecture Overview

```
Frontend (React / HTML)
        |
        v
Amazon S3 (Static Hosting)
        |
        v
AWS ECS Service (Backend)
        |
        v
Amazon ECR (Docker Image Repository)
```

---

## ⚙️ Backend Deployment (ECS + ECR)

### 1️⃣ Create and Push Docker Image

```bash
# Build Docker image
docker build -t backend-app .

# Tag the image for ECR
docker tag backend-app:latest <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com/backend-app:latest

# Push to ECR
aws ecr get-login-password --region <REGION> | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com
docker push <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com/backend-app:latest
```

### 2️⃣ Create ECS Resources

* **ECS Cluster** → Fargate
* **Task Definition** → Use ECR image
* **Service** → Attach to Load Balancer (ALB)
* **Listener Port:** 80 (HTTP)
* **Target Group:** backend-tg

### ✅ Verify Backend

Visit:

```
http://<ALB-DNS-URL>/
```

Expected Output:

```
Welcome to AWS ECR-ECS Page
```

---

## 💻 Frontend Deployment (S3 Static Hosting)

### 1️⃣ Update Backend API URL

In your frontend code (e.g., `script.js` or React `.env`):

```js
const backendURL = "http://<ALB-DNS-URL>/";
```

### 2️⃣ Build and Upload

```bash
npm run build
```

* Go to **AWS S3 Console**
* Create a new **S3 Bucket** (e.g., `simplefrontendapp-vignesh`)
* Enable **Static Website Hosting**
* Upload contents of the `build/` folder

### 3️⃣ Make It Public

* Set **Public Access** → “Allow Public Reads”
* Add bucket policy for `GetObject` permission
* Copy the **Static Website Hosting URL**

---

## 🌍 URLs

| Type         | URL                                                                                                                                          |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **Backend**  | `http://ecs-backend-alb-xxxx.us-east-1.elb.amazonaws.com/`                                                                                   |
| **Frontend** | [http://simplefrontendapp-vignesh.s3-website-us-east-1.amazonaws.com/](http://simplefrontendapp-vignesh.s3-website-us-east-1.amazonaws.com/) |

---

## 🧠 Notes

* ECS uses **Fargate** → No server management.
* ALB ensures **load balancing and public access**.
* S3 static site fetches data directly from backend.
* Ensure **CORS is enabled** in the backend for frontend requests.

---

## 🧾 Deliverables

* ✅ Backend running on ECS via ECR
* ✅ Frontend hosted on S3
* ✅ Frontend successfully fetching backend response

---

~~~~
