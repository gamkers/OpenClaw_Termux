
🚀 OpenClaw Setup on Termux (ARM) using proot Ubuntu

This guide helps you install and run OpenClaw inside Termux using a proot Ubuntu environment on ARM devices (Android).

It also fixes the common Node.js error:

uv_interface_addresses returned Unknown system error 13


---

📱 Requirements

Android device

Latest Termux (F-Droid recommended)

Internet connection



---

🛠 Step 1: Update Termux

pkg update && pkg upgrade -y
pkg install proot-distro -y


---

🐧 Step 2: Install Ubuntu (ARM)

proot-distro install ubuntu
proot-distro login ubuntu

You are now inside Ubuntu.


---

📦 Step 3: Update Ubuntu & Install Base Tools

apt update && apt upgrade -y
apt install curl git build-essential ca-certificates -y


---

🟢 Step 4: Install Node.js 22

curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
apt install -y nodejs

Verify installation:

node -v
npm -v


---

🧩 Step 5: Install OpenClaw

npm install -g openclaw@latest


---

🛡 Step 6: Fix uv_interface_addresses Error (Required in proot)

Create hijack file:

cat << 'eof' > /root/hijack.js
const os = require('os');
os.networkInterfaces = () => ({});
eof

Make it permanent:

echo 'export NODE_OPTIONS="--require /root/hijack.js"' >> ~/.bashrc
source ~/.bashrc

This prevents Node.js from trying to access restricted network interfaces inside proot.


---

🚀 Step 7: Run OpenClaw

openclaw onboard


---

📌 Full One-Shot Install Script

If you want everything in one go:

pkg update && pkg upgrade -y && pkg install proot-distro -y && \
proot-distro install ubuntu && \
proot-distro login ubuntu

Then inside Ubuntu:

apt update && apt upgrade -y && \
apt install curl git build-essential ca-certificates -y && \
curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
apt install -y nodejs && \
npm install -g openclaw@latest && \
echo "const os = require('os'); os.networkInterfaces = () => ({});" > /root/hijack.js && \
echo 'export NODE_OPTIONS="--require /root/hijack.js"' >> ~/.bashrc && \
source ~/.bashrc && \
openclaw onboard


---

⚠ Notes

Works on ARM64 Android devices.

Tested in proot Ubuntu environment.

Required because proot blocks certain network syscalls.

If error persists, restart Ubuntu session:


exit
proot-distro login ubuntu


---

🧠 Why This Fix Works

OpenClaw (Node.js) tries to read system network interfaces.
proot restricts this → causes system error 13.

We override:

os.networkInterfaces()

to return an empty object — preventing the crash.


---

👨‍💻 Author

Setup optimized for mobile ARM Linux environments.


---
