
# 🚀 Deploy ASP.NET Core on Ubuntu with Nginx

This guide walks you through deploying an ASP.NET Core app on a Linux server using Nginx as a reverse proxy.

---


## 1. 🔐 SSH into the Server

```bash
ssh username@your-server-ip
```

---

## 2. 📦 Install Required Packages

```bash
sudo apt update
sudo apt install -y wget
```

---

## 3. ⚙️ Install ASP.NET Core Runtime

```bash
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt update
sudo apt install -y aspnetcore-runtime-8.0  //based on your runtime version
```

> Replace `aspnetcore-runtime-8.0` with the correct runtime version if needed.

---

## 4. 📤 Publish Your App (on Local Machine)

```bash
dotnet publish -c Release -o "folder-path"
```

> Example: `dotnet publish -c Release -o bin/Release/net8.0/publish`

---

## 5. 📁 Create Deployment Directory on Server

```bash
sudo mkdir -p /var/www/myapp
sudo chown username /var/www/myapp
```

---

## 6. 📡 Upload Published Files (from Local Machine)

```bash
scp -r "folder-path" username@your-server-ip:/var/www/myapp
```

> Replace `folder-path` with your local publish directory.

---

## 7. 🚀 Run the App to Test

```bash
cd /var/www/myapp/publish
dotnet Neemah.dll --urls "http://0.0.0.0:5000"
```

> Make sure to replace `Neemah.dll` with your actual DLL name.

---

## 8. 🌐 Install and Start Nginx

```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

---

## 9. ⚙️ Configure Nginx Site

Create a new file:

```bash
sudo nano /etc/nginx/sites-available/neemah
```

Paste the following configuration:

```nginx
server {
    listen 80;
    server_name your-server-ip;

    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

> Replace `your-server-ip` with your actual server IP address.

---

## 10. 🔗 Enable Nginx Site and Reload

```bash
sudo ln -s /etc/nginx/sites-available/neemah /etc/nginx/sites-enabled/
sudo systemctl reload nginx
```

---

## 11. 🛠️ Run the App in Background

```bash
cd /var/www/myapp/publish
nohup dotnet Neemah.dll --urls "http://0.0.0.0:5000" &
```

> `nohup` ensures the process continues running after logout.

---

## ✅ Done!

Your ASP.NET Core app is now running behind Nginx and accessible at:

```text
http://your-server-ip/
```
