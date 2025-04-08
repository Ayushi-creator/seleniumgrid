## 🧪 Running Playwright Tests on a Remote PC via Selenium Grid

This document outlines the step-by-step process to configure and execute Playwright tests on a remote machine using Selenium Grid.

---

### 🔹 Step 1: Setup Selenium Grid on the Remote PC

**1.1 Start the Selenium Hub**
```bash
java -jar selenium-server-4.29.0.jar hub
```

**1.2 Start the Selenium Node**
```bash
java -jar selenium-server-4.29.0.jar node --port 5556 --hub http://10.0.0.241:4444
```

✅ Make sure port **4444** is the default hub port and **5556** is the custom node port.

**1.3 Verify Hub & Node**
- Visit: [http://10.0.0.241:4444/ui](http://10.0.0.241:4444/ui)
- Or run:
```bash
curl http://10.0.0.241:4444/status | jq '.value.nodes'
```
- Output should list connected nodes.

---

### 🔹 Step 2: Get the Remote PC's IP Address

Run this command on the remote machine:
```bash
ip a
```
Look for the IP under the Ethernet interface (e.g., `enp1s0`). Example:
```
inet 10.0.0.241/24
```
✅ This is the IP to use in your Playwright test.

---

### 🔹 Step 3: Install Playwright on the Local PC

**3.1 Create Project Directory**
```bash
mkdir playwright-selenium-project
cd playwright-selenium-project
```

**3.2 Initialize Node.js Project**
```bash
npm init -y
```

**3.3 Install Dependencies**
```bash
npm install -D @playwright/test selenium-webdriver
```

---

### 🔹 Step 4: Configure Playwright for Selenium Grid

**4.1 Create `playwright.config.js`**
```javascript
const { defineConfig } = require('@playwright/test');

module.exports = defineConfig({
  use: {
    launchOptions: {
      executablePath: false // Playwright will use Selenium WebDriver
    }
  },
});
```

---

### 🔹 Step 5: Create Playwright Test File

**5.1 Create Folder and File**
```bash
mkdir tests
```
Create `tests/example.js`:

```javascript
const { test } = require('@playwright/test');
const { Builder } = require('selenium-webdriver');

test('run test on remote machine', async () => {
  const remoteGridUrl = 'http://10.0.0.241:4444/wd/hub';

  const driver = await new Builder()
    .usingServer(remoteGridUrl)
    .forBrowser('chrome')
    .build();

  try {
    await driver.get('https://example.com');
    console.log('Test running on remote machine');
  } finally {
    await driver.quit();
  }
});
```

---

### 🔹 Step 6: Run the Test

```bash
npx playwright test tests/example.js
```

✅ The test should open a browser on the **remote PC** and run successfully.

---

### 🔹 Step 7: Troubleshooting & Tips

- **Check if Selenium Grid is Up**:
  ```bash
  curl http://localhost:4444/status
  ```
  Expected Output:
  ```json
  {"value":{"ready":true}}
  ```

- **Verify Node Connection**:
  Visit: [http://localhost:4444/ui](http://localhost:4444/ui)

- **Firewall/Port Issues**:
  Ensure ports 4444 (hub) and 5556 (node) are open and accessible.

- **Same Network**:
  Ensure both machines are on the same LAN or connected via VPN.

---

✅ Now your Playwright tests can run on a remote machine via Selenium Grid!

