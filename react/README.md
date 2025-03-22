# Dockerized Multi-Brand React App

## 🚀 Project Overview
This repository contains a Docker setup for a React application that supports **multiple brands or companies**.  
We achieve this by:
- Using **separate `.env.companyname` files** to configure builds for different companies.
- Running **custom build and start scripts** dynamically.
- Structuring our `docker-compose.yml` and `Dockerfile` efficiently.

---

## 📂 Project Structure
```
/deploy
 ├── docker-compose.yml
 ├── Dockerfile
/src
 ├── components
 ├── assets
 ├── App.js
 ├── index.js
package.json
.env.company1
.env.company2
```

---

## ⚙️ Why Use `dockerfile: deploy/Dockerfile`?
Since both `docker-compose.yml` and `Dockerfile` are inside the `/deploy/` folder, we specify:  
```yaml
build:
  context: ..
  dockerfile: deploy/Dockerfile
```
- **`context: ..`** → Ensures the whole project is available during the build.  
- **`dockerfile: deploy/Dockerfile`** → Explicitly tells Docker to use the correct file inside `/deploy/`.  
Without this, Docker might not find the `Dockerfile` when running `docker-compose`.

---

## 🏠 Dockerfile Explanation
Our `Dockerfile` builds and runs the project based on a selected brand:
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

RUN npm run build:companyname

EXPOSE 3000
CMD ["npm", "run", "start:companyname"]
```
- **Custom Build & Start Scripts:**  
  - `npm run build:companyname` → Builds for `companyname`.
  - `npm run start:companyname` → Starts the app using the `.env.companyname` file.
- This allows us to build for different companies without modifying the Dockerfile.

---

## 🌍 Handling Multiple Companies
Each company has a separate `.env.companyname` file.  
We use the [`env-cmd`](https://www.npmjs.com/package/env-cmd) package to load the correct `.env` file dynamically.

### **In `package.json`**
```json
"scripts": {
  "start:companyname": "env-cmd -f .env.companyname react-scripts start",
  "build:companyname": "env-cmd -f .env.companyname react-scripts build"
}
```
#### Example Usage:
```sh
npm run build:company1
npm run start:company2
```
This ensures each brand gets the correct configuration.

---

## 🚀 Running with Docker
### **Build & Run for a Specific Brand**
1️⃣ **Set up `.env.companyname`**  
Modify the `.env.company1` file:
```
REACT_APP_API_URL=https://api.company1.com
REACT_APP_THEME=dark
```
2️⃣ **Run the container:**
```sh
docker-compose -f deploy/docker-compose-stage.yml up -d
```
This builds and starts the project with the correct company environment.

---

## 🎯 Summary
- ✅ **Uses `dockerfile: deploy/Dockerfile`** because the `Dockerfile` is inside `deploy/`.
- ✅ **Supports multiple brands** by using different `.env.companyname` files.
- ✅ **Uses `build:companyname` & `start:companyname`** for easy switching.
- ✅ **Runs seamlessly in Docker with `docker-compose -f deploy/docker-compose-stage.yml up -d`**.

---

📌 **Now you can easily build & run your project for different brands!** 🚀
