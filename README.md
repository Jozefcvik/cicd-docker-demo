# 🚀 CI/CD GitHub Docker Demo


A complete demonstration of Continuous Integration and Continuous Deployment using GitHub Actions and Docker.

---

Here’s a simple, practical CI/CD example for:
- Static website (HTML + CSS)
- Served by Docker + Nginx
- Lint check before build
- Manual approval before Docker push
- Auto push to Docker Hub after approval

We’ll use:
- GitHub repository
- GitHub Actions
- Docker Hub
- Environment protection for approval

---

### 🧱 Step 1 — Create Repository

1. Go to GitHub → New repository
2. Name it: static-nginx-site
3. Make it public (simpler for Docker Hub)
4. Click Create repository

Clone it locally:

```bash
git clone https://github.com/YOUR_USERNAME/static-nginx-site.git
cd static-nginx-site
```

---

### 📁 Step 2 — Project Structure

Create this structure:

```code
static-nginx-site/
│
├── index.html
├── style.css
├── Dockerfile
└── .github/
    └── workflows/
        └── ci-cd.yml
```

---

### 🌐 Step 3 — Add Static Website

**index.html**

```html
<!DOCTYPE html>
<html>
<head>
  <title>My Static Site</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Hello from Docker + GitHub Actions 🚀</h1>
</body>
</html>
```

**style.css**

```html
body {
  background: #f4f4f4;
  text-align: center;
  font-family: Arial, sans-serif;
}
```

---

### 🐳 Step 4 — Dockerfile (Nginx)

Create `Dockerfile`:

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html
COPY style.css /usr/share/nginx/html/style.css

EXPOSE 80
```

Test locally:

```bash
docker build -t static-site .
docker run -p 8080:80 static-site
```

Visit:

```code
http://localhost:8080
```

---

### 🧹 Step 5 — Add Linting

We’ll use:
- HTML lint via htmlhint
- CSS lint via stylelint

GitHub Actions will install them.

---

### 🔐 Step 6 — Add Docker Hub Secrets

Go to:

GitHub Repository **→ Settings → Secrets and variables → Actions**

Add:
| Name             | Value                                      |
|------------------|--------------------------------------------|
| DOCKER_USERNAME  | your Docker Hub username                   |
| DOCKER_PASSWORD  | your Docker Hub password or access token   |

---

### 🛑 Step 7 — Add Manual Approval (Environment Protection)

1. Go to **Settings → Environments**
2. Click **New environment**
3. Name it: `production`
4. Add **Required reviewers**
5. Add yourself
6. Save

This forces approval before Docker push.

---

### ⚙️ Step 8 — Create GitHub Action

Create:

```code
.github/workflows/ci-cd.yml
```
```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:

  lint:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install Node
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install linters
        run: |
          npm install -g htmlhint stylelint stylelint-config-standard

      - name: Lint HTML
        run: htmlhint index.html

      - name: Lint CSS
        run: stylelint "style.css" --config-basedir .

  build-and-push:
    needs: lint
    runs-on: ubuntu-latest
    environment: production

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Login to Docker Hub
        run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

      - name: Build Docker image
        run: docker build -t ${{ secrets.DOCKER_USERNAME }}/static-site:latest .

      - name: Push Docker image
        run: docker push ${{ secrets.DOCKER_USERNAME }}/static-site:latest
```

---

### 🚀 Step 9 — Push to GitHub

```bash
git add .
git commit -m "Initial static website with CI/CD"
git branch -M main
git push origin main
```

---

### 🔄 What Happens Now?

When you push:

1. ✅ Lint job runs first
2. ❌ If lint fails → pipeline stops
3. ✅ If lint passes → waits for manual approval
4. 🛑 You approve in GitHub Actions UI
5. 🐳 Docker image builds
6. 📦 Image pushes to Docker Hub

---

### 🖥 Optional — Run from Docker Hub Image

After push:

```bash
docker pull yourusername/static-site:latest
docker run -p 8080:80 yourusername/static-site
```

---

### 🎯 Summary of the Flow

```
Push to main
     ↓
Lint HTML/CSS
     ↓
Manual Approval
     ↓
Build Docker Image
     ↓
Push to Docker Hub
```
