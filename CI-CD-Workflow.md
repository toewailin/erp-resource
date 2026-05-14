React, Go Backend နဲ့ Traccar Server တွေကို VPS ပေါ်မှာ အလိုအလျောက် Deploy လုပ်ဖို့ဆိုရင် **Docker** နဲ့ **GitHub Actions** ကို တွဲသုံးတာက အလွယ်ကူဆုံးနဲ့ အတည်ငြိမ်ဆုံး နည်းလမ်းဖြစ်ပါတယ်။

ကုဒ်ရေးပြီး GitHub ပေါ်တင်လိုက်တာနဲ့ VPS ပေါ်မှာ အလိုအလျောက် Update ဖြစ်သွားမယ့် CI/CD Workflow တည်ဆောက်ပုံကို အဆင့် (၄) ဆင့်နဲ့ ရှင်းပြပေးပါမယ်။

### **အဆင့် (၁) - Project များကို Dockerize လုပ်ခြင်း (Dockerfile များ ဖန်တီးခြင်း)**

အရင်ဆုံး ကိုယ့်ရဲ့ Project တွေကို VPS ပေါ်မှာ Environment ပြဿနာမရှိဘဲ Run နိုင်ဖို့ Docker Image တွေ ပြောင်းရပါမယ်။

**၁။ Go Backend အတွက် `Dockerfile`:**
Root folder မှာ `Dockerfile` ဖန်တီးပြီး Multi-stage build သုံးပါမယ်။

```dockerfile
# Stage 1: Build
FROM golang:1.21-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN go build -o main .

# Stage 2: Run
FROM alpine:latest
WORKDIR /root/
COPY --from=builder /app/main .
EXPOSE 8080
CMD ["./main"]

```

**၂။ React Frontend အတွက် `Dockerfile`:**
React ကို Build ပြီး Nginx နဲ့ ပြန် Host မယ့် ပုံစံပါ။

```dockerfile
# Stage 1: Build
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

```

---

### **အဆင့် (၂) - Docker Compose ဖြင့် စနစ်တစ်ခုလုံးကို စုစည်းခြင်း**

Project ရဲ့ Root Directory (သို့မဟုတ်) VPS ပေါ်မှာ `docker-compose.yml` ဖန်တီးပြီး React, Go နဲ့ Traccar သုံးခုလုံးကို ချိတ်ဆက်ပါမယ်။

```yaml
version: '3.8'

services:
  frontend:
    build: ./frontend # React project path
    ports:
      - "3000:80"
    restart: always

  backend:
    build: ./backend # Go project path
    ports:
      - "8080:8080"
    environment:
      - DB_HOST=postgres
      - DB_USER=myuser
      - DB_PASS=mypassword
    depends_on:
      - postgres
    restart: always

  traccar:
    image: traccar/traccar:latest
    ports:
      - "8082:8082" # Web UI
      - "5000-5150:5000-5150" # GPS Device Ports
      - "5000-5150:5000-5150/udp"
    volumes:
      - ./traccar/logs:/opt/traccar/logs
      - ./traccar/traccar.xml:/opt/traccar/conf/traccar.xml
    restart: always

  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: vrms_db
    volumes:
      - pgdata:/var/lib/postgresql/data
    restart: always

volumes:
  pgdata:

```

---

### **အဆင့် (၃) - VPS ဘက်တွင် ကြိုတင်ပြင်ဆင်ခြင်း**

GitHub Actions ကနေ VPS ဆီကို လှမ်းချိတ်ဆက်နိုင်ဖို့ လုံခြုံရေးအရ SSH Key တည်ဆောက်ရပါမယ်။

1. VPS ထဲကို ဝင်ပြီး SSH Key အသစ်တစ်ခု ထုတ်ပါ-
```bash
ssh-keygen -t rsa -b 4096 -C "github-actions"

```


2. ထွက်လာတဲ့ `id_rsa.pub` (Public Key) ကို VPS ရဲ့ `~/.ssh/authorized_keys` ထဲ ထည့်ပါ။
3. `id_rsa` (Private Key) ကို Copy ကူးထားပါ။
4. ကိုယ့်ရဲ့ GitHub Repository ရဲ့ **Settings > Secrets and variables > Actions** ကို သွားပြီး အောက်ပါ Secrets ၃ ခုကို ထည့်ပါ-
* `SERVER_HOST` (VPS ရဲ့ IP Address)
* `SERVER_USERNAME` (ဥပမာ - root သို့မဟုတ် ubuntu)
* `SSH_PRIVATE_KEY` (စောစောက Copy ကူးထားသော Private Key)



---

### **အဆင့် (၄) - GitHub Actions ဖြင့် CI/CD Pipeline ရေးဆွဲခြင်း**

Project ထဲမှာ `.github/workflows/deploy.yml` ဆိုတဲ့ ဖိုင်တစ်ခု ဖန်တီးပြီး အောက်ပါ ကုဒ်ကို ထည့်ပါ။

ဒီ Workflow ရဲ့ အလုပ်လုပ်ပုံက **Main branch ကို ကုဒ် Push လိုက်တာနဲ့ ချက်ချင်း VPS ထဲကို SSH နဲ့ လှမ်းဝင်မယ်၊ ကုဒ်အသစ်တွေကို `git pull` လုပ်မယ်၊ ပြီးရင် `docker-compose` နဲ့ Image အသစ်ပြန်ဆောက်ပြီး Run ပေးသွားမှာ ဖြစ်ပါတယ်။**

```yaml
name: Deploy to VPS

on:
  push:
    branches:
      - main # Main branch ကို push လုပ်တိုင်း အလုပ်လုပ်မည်

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Deploy via SSH
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USERNAME }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            # VPS ပေါ်က Project Folder ထဲဝင်မယ်
            cd /path/to/your/project 
            
            # ကုဒ်အသစ်တွေ ဆွဲယူမယ်
            git pull origin main
            
            # Docker Compose နဲ့ Image တွေ ပြန်ဆောက်ပြီး Background မှာ Run မယ်
            docker-compose up -d --build
            
            # မသုံးတော့တဲ့ Image အဟောင်းတွေကို ရှင်းလင်းမယ် (Space မပြည့်အောင်)
            docker image prune -f

```

### **အကျဉ်းချုပ် Workflow လည်ပတ်ပုံ-**

၁။ Local မှာ ကုဒ်ရေးပြီး `git push origin main` လုပ်လိုက်မယ်။
၂။ GitHub Actions က အလိုအလျောက် အလုပ်လုပ်ပြီး VPS ဆီကို SSH နဲ့ လှမ်းဝင်မယ်။
၃။ VPS က ကုဒ်အသစ်တွေကို ဆွဲယူ (Pull) ပြီး၊ ပြောင်းလဲသွားတဲ့ React နဲ့ Go ကို Docker Image အသစ် ပြန်ဆောက်မယ်။
၄။ `docker-compose up -d` ဖြစ်တဲ့အတွက် System ကြီးတစ်ခုလုံး Down မသွားဘဲ Container အဟောင်းနေရာမှာ အသစ်ကို အလိုအလျောက် အစားထိုးသွားပါလိမ့်မယ်။

ဒီ CI/CD ပုံစံက အသေးစားနဲ့ အလတ်စား လုပ်ငန်းတွေအတွက် အရမ်းကို အဆင်ပြေပြီး၊ ပိုကြီးလာတဲ့အခါမှ GitHub Container Registry (GHCR) လိုမျိုးကို သုံးပြီး Image တွေကို သီးသန့် သိမ်းတဲ့ပုံစံကို ထပ်ချဲ့လို့ ရပါတယ်။ ဘယ်အပိုင်းကို ပိုပြီး အသေးစိတ် သိချင်တာ ရှိသေးလဲဗျ?

---

React, Go Backend နဲ့ Traccar Server တွေကို VPS ပေါ်မှာ အလိုအလျောက် Deploy လုပ်ဖို့ဆိုရင် **Docker** နဲ့ **GitHub Actions** ကို တွဲသုံးတာက အလွယ်ကူဆုံးနဲ့ အတည်ငြိမ်ဆုံး နည်းလမ်းဖြစ်ပါတယ်။

ကုဒ်ရေးပြီး GitHub ပေါ်တင်လိုက်တာနဲ့ VPS ပေါ်မှာ အလိုအလျောက် Update ဖြစ်သွားမယ့် CI/CD Workflow တည်ဆောက်ပုံကို အဆင့် (၄) ဆင့်နဲ့ ရှင်းပြပေးပါမယ်။

### **အဆင့် (၁) - Project များကို Dockerize လုပ်ခြင်း (Dockerfile များ ဖန်တီးခြင်း)**

အရင်ဆုံး ကိုယ့်ရဲ့ Project တွေကို VPS ပေါ်မှာ Environment ပြဿနာမရှိဘဲ Run နိုင်ဖို့ Docker Image တွေ ပြောင်းရပါမယ်။

**၁။ Go Backend အတွက် `Dockerfile`:**
Root folder မှာ `Dockerfile` ဖန်တီးပြီး Multi-stage build သုံးပါမယ်။

```dockerfile
# Stage 1: Build
FROM golang:1.21-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN go build -o main .

# Stage 2: Run
FROM alpine:latest
WORKDIR /root/
COPY --from=builder /app/main .
EXPOSE 8080
CMD ["./main"]

```

**၂။ React Frontend အတွက် `Dockerfile`:**
React ကို Build ပြီး Nginx နဲ့ ပြန် Host မယ့် ပုံစံပါ။

```dockerfile
# Stage 1: Build
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

```

---

### **အဆင့် (၂) - Docker Compose ဖြင့် စနစ်တစ်ခုလုံးကို စုစည်းခြင်း**

Project ရဲ့ Root Directory (သို့မဟုတ်) VPS ပေါ်မှာ `docker-compose.yml` ဖန်တီးပြီး React, Go နဲ့ Traccar သုံးခုလုံးကို ချိတ်ဆက်ပါမယ်။

```yaml
version: '3.8'

services:
  frontend:
    build: ./frontend # React project path
    ports:
      - "3000:80"
    restart: always

  backend:
    build: ./backend # Go project path
    ports:
      - "8080:8080"
    environment:
      - DB_HOST=postgres
      - DB_USER=myuser
      - DB_PASS=mypassword
    depends_on:
      - postgres
    restart: always

  traccar:
    image: traccar/traccar:latest
    ports:
      - "8082:8082" # Web UI
      - "5000-5150:5000-5150" # GPS Device Ports
      - "5000-5150:5000-5150/udp"
    volumes:
      - ./traccar/logs:/opt/traccar/logs
      - ./traccar/traccar.xml:/opt/traccar/conf/traccar.xml
    restart: always

  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: vrms_db
    volumes:
      - pgdata:/var/lib/postgresql/data
    restart: always

volumes:
  pgdata:

```

---

### **အဆင့် (၃) - VPS ဘက်တွင် ကြိုတင်ပြင်ဆင်ခြင်း**

GitHub Actions ကနေ VPS ဆီကို လှမ်းချိတ်ဆက်နိုင်ဖို့ လုံခြုံရေးအရ SSH Key တည်ဆောက်ရပါမယ်။

1. VPS ထဲကို ဝင်ပြီး SSH Key အသစ်တစ်ခု ထုတ်ပါ-
```bash
ssh-keygen -t rsa -b 4096 -C "github-actions"

```


2. ထွက်လာတဲ့ `id_rsa.pub` (Public Key) ကို VPS ရဲ့ `~/.ssh/authorized_keys` ထဲ ထည့်ပါ။
3. `id_rsa` (Private Key) ကို Copy ကူးထားပါ။
4. ကိုယ့်ရဲ့ GitHub Repository ရဲ့ **Settings > Secrets and variables > Actions** ကို သွားပြီး အောက်ပါ Secrets ၃ ခုကို ထည့်ပါ-
* `SERVER_HOST` (VPS ရဲ့ IP Address)
* `SERVER_USERNAME` (ဥပမာ - root သို့မဟုတ် ubuntu)
* `SSH_PRIVATE_KEY` (စောစောက Copy ကူးထားသော Private Key)



---

### **အဆင့် (၄) - GitHub Actions ဖြင့် CI/CD Pipeline ရေးဆွဲခြင်း**

Project ထဲမှာ `.github/workflows/deploy.yml` ဆိုတဲ့ ဖိုင်တစ်ခု ဖန်တီးပြီး အောက်ပါ ကုဒ်ကို ထည့်ပါ။

ဒီ Workflow ရဲ့ အလုပ်လုပ်ပုံက **Main branch ကို ကုဒ် Push လိုက်တာနဲ့ ချက်ချင်း VPS ထဲကို SSH နဲ့ လှမ်းဝင်မယ်၊ ကုဒ်အသစ်တွေကို `git pull` လုပ်မယ်၊ ပြီးရင် `docker-compose` နဲ့ Image အသစ်ပြန်ဆောက်ပြီး Run ပေးသွားမှာ ဖြစ်ပါတယ်။**

```yaml
name: Deploy to VPS

on:
  push:
    branches:
      - main # Main branch ကို push လုပ်တိုင်း အလုပ်လုပ်မည်

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Deploy via SSH
        uses: appleboy/ssh-action@v1.0.0
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USERNAME }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            # VPS ပေါ်က Project Folder ထဲဝင်မယ်
            cd /path/to/your/project 
            
            # ကုဒ်အသစ်တွေ ဆွဲယူမယ်
            git pull origin main
            
            # Docker Compose နဲ့ Image တွေ ပြန်ဆောက်ပြီး Background မှာ Run မယ်
            docker-compose up -d --build
            
            # မသုံးတော့တဲ့ Image အဟောင်းတွေကို ရှင်းလင်းမယ် (Space မပြည့်အောင်)
            docker image prune -f

```

### **အကျဉ်းချုပ် Workflow လည်ပတ်ပုံ-**

၁။ Local မှာ ကုဒ်ရေးပြီး `git push origin main` လုပ်လိုက်မယ်။
၂။ GitHub Actions က အလိုအလျောက် အလုပ်လုပ်ပြီး VPS ဆီကို SSH နဲ့ လှမ်းဝင်မယ်။
၃။ VPS က ကုဒ်အသစ်တွေကို ဆွဲယူ (Pull) ပြီး၊ ပြောင်းလဲသွားတဲ့ React နဲ့ Go ကို Docker Image အသစ် ပြန်ဆောက်မယ်။
၄။ `docker-compose up -d` ဖြစ်တဲ့အတွက် System ကြီးတစ်ခုလုံး Down မသွားဘဲ Container အဟောင်းနေရာမှာ အသစ်ကို အလိုအလျောက် အစားထိုးသွားပါလိမ့်မယ်။

ဒီ CI/CD ပုံစံက အသေးစားနဲ့ အလတ်စား လုပ်ငန်းတွေအတွက် အရမ်းကို အဆင်ပြေပြီး၊ ပိုကြီးလာတဲ့အခါမှ GitHub Container Registry (GHCR) လိုမျိုးကို သုံးပြီး Image တွေကို သီးသန့် သိမ်းတဲ့ပုံစံကို ထပ်ချဲ့လို့ ရပါတယ်။ ဘယ်အပိုင်းကို ပိုပြီး အသေးစိတ် သိချင်တာ ရှိသေးလဲဗျ?
