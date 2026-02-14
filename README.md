
# 🚀 OpenClaw Setup on Termux (ARM) using proot Ubuntu

Run **OpenClaw** inside Termux on Android (ARM64) using a `proot` Ubuntu environment.

---

## 📱 Requirements

* Android (ARM64)
* Latest **Termux** (F-Droid recommended)
* Stable Internet connection
* At least 2GB free storage

---

## 🛠 Step 1: Prepare Termux

Update packages and install `proot-distro`:

```bash
pkg update -y && pkg upgrade -y && pkg install proot-distro -y
```

---

## 🐧 Step 2: Install & Login to Ubuntu (ARM)

```bash
proot-distro install ubuntu && proot-distro login ubuntu
```

You are now inside Ubuntu.

---

## 📦 Step 3: Install Base Dependencies

```bash
apt update -y && apt upgrade -y && \
apt install -y curl git build-essential ca-certificates
```

---

## 🟢 Step 4: Install Node.js 22

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
apt install -y nodejs
```

Verify:

```bash
node -v
npm -v
```

---

## 🧩 Step 5: Install OpenClaw

```bash
npm install -g openclaw@latest
```

---

## 🛡 Step 6: Fix `uv_interface_addresses` Error (IMPORTANT)

`proot` blocks certain network syscalls used by Node.js.

We override `os.networkInterfaces()` to prevent the crash.

### Create hijack file:

```bash
echo "const os = require('os'); os.networkInterfaces = () => ({});" > /root/hijack.js
```

### Make it permanent:

```bash
echo 'export NODE_OPTIONS="--require /root/hijack.js"' >> ~/.bashrc && source ~/.bashrc
```

---

## 🚀 Step 7: Run OpenClaw

```bash
openclaw onboard
```

---

# ⚡ One-Shot Installation (Fully Combined)

### Run this in Termux:

```bash
pkg update -y && pkg upgrade -y && pkg install proot-distro -y && \
proot-distro install ubuntu
```

### Then login:

```bash
proot-distro login ubuntu
```

### Inside Ubuntu run:

```bash
apt update -y && apt upgrade -y && \
apt install -y curl git build-essential ca-certificates && \
curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
apt install -y nodejs && \
npm install -g openclaw@latest && \
echo "const os = require('os'); os.networkInterfaces = () => ({});" > /root/hijack.js && \
echo 'export NODE_OPTIONS="--require /root/hijack.js"' >> ~/.bashrc && \
source ~/.bashrc
```

Now start:

```bash
openclaw onboard
```

---

# 🔄 If Error Still Persists

Restart Ubuntu session:

```bash
exit
proot-distro login ubuntu
```

---

# 🧠 Why This Fix Works

OpenClaw (Node.js) tries to access system network interfaces.

Inside `proot`, restricted syscalls cause:

```
uv_interface_addresses returned Unknown system error 13
```

We override:

```js
os.networkInterfaces()
```

to return an empty object — preventing the crash.

---

# 📌 Tested Environment

* Android ARM64
* Termux (F-Droid)
* proot Ubuntu
* Node.js 22

---

# 👨‍💻 Author

Optimized for mobile ARM Linux environments running inside Termux.

---
