---

# **📘 Class Notes – Jenkins Installation & Java Setup (Windows & macOS)**

---

# **1️⃣ What is Jenkins?**

* Jenkins is an **automation server**
* It helps in **Continuous Integration (CI)** and **Continuous Delivery/Deployment (CD)**
* It automatically runs:

  * Builds
  * Tests
  * Deployment
  * Scheduled jobs

DevOps + Automation testers use Jenkins to trigger tests automatically.

---

# **2️⃣ Java Requirement for Jenkins**

* Jenkins runs on Java.
* Supported Java versions: **JDK 17 or JDK 21**
* If you have **JDK 24**, Jenkins will NOT accept it.

---

# **3️⃣ Check If Java Is Installed**

### 🖥 **For Windows (CMD)**

Run:

```bash
java -version
```

If installed, you’ll see a version like:

```
openjdk version "21.0.1"
```

Check install path:

```bash
where java
```

### 🍏 **For macOS (Terminal)**

```bash
java -version
which java
```

---

# **4️⃣ If You Have Wrong Java Version (e.g., JDK 24)**

➡ Jenkins will show error:
**“Failed to find compatible Java version (17 or 21)”**

### ✔ Solution:

Install **JDK 21**, not 24.

JDK 21 download page (Oracle / Temurin / OpenJDK).

---

# **5️⃣ Install JDK 21 (Windows)**

1. Download JDK 21 (MSI installer preferred)
2. Double-click to install
3. Default installation path:

   ```
   C:\Program Files\Java\jdk-21
   ```
4. Add JAVA_HOME (optional but recommended)

   * Open *System Properties → Environment Variables*
   * Add:

     * **Variable name:** `JAVA_HOME`
     * **Variable value:** `C:\Program Files\Java\jdk-21`
   * Add to PATH:

     ```
     %JAVA_HOME%\bin
     ```

---

# **6️⃣ Install JDK 21 (macOS)**

1. Download `.pkg` file
2. Install normally
3. Verify:

   ```bash
   java -version
   /usr/libexec/java_home
   ```

---

# **7️⃣ Install Jenkins on Windows**

1. Download Jenkins `.msi` from jenkins.io
2. Double-click installer
3. When it asks for Java directory, select:

   ```
   C:\Program Files\Java\jdk-21
   ```

   ❌ DO NOT select `bin` folder
   ❌ DO NOT select JDK 24
4. Continue installation
5. Jenkins opens in browser at:

   ```
   http://localhost:8080
   ```

---

# **8️⃣ Why Jenkins Shows “Invalid Java Directory” Error**

Students commonly make these mistakes:

### 🚫 Mistake 1: Selecting JDK bin folder

```
C:\Program Files\Java\jdk-21\bin
```

→ ❌ Wrong

### 🚫 Mistake 2: Selecting Program Files (x86)

Older Java versions live there → ❌ Wrong

### 🚫 Mistake 3: Selecting JDK 24

Unsupported → ❌ Wrong

### 👍 Correct Path:

```
C:\Program Files\Java\jdk-21
```

---

# **9️⃣ Jenkins Installation on macOS**

1. Install Homebrew (optional)
2. Install Jenkins using:

```bash
brew install jenkins-lts
```

Start Jenkins:

```bash
brew services start jenkins-lts
```

Open Jenkins:

```
http://localhost:8080
```

---

# 🔟 Jenkins Setup After Installation

* Initial admin password from:

  ```
  C:\Windows\system32\config\systemprofile\AppData\Local\Jenkins\.jenkins\secrets\initialAdminPassword
  ```
* Install recommended plugins
* Create admin user
* Explore Jenkins dashboard

---

# **1️⃣1️⃣ CRON Expressions for Jenkins**

### Common Cron Symbols

| Expression | Meaning                  |
| ---------- | ------------------------ |
| *          | Every value              |
| ,          | Separate multiple values |
| -          | Range                    |
| /          | Step                     |

### Examples:

| Task                 | Cron              |
| -------------------- | ----------------- |
| Every 5 minutes      | `H/5 * * * *`     |
| Every 30 mins        | `H/30 * * * *`    |
| At 2:30 AM daily     | `30 2 * * *`      |
| Every Monday at 9 AM | `0 9 * * 1`       |
| Multiple times       | `0 9,12,18 * * *` |

Why not /15 or /45?
→ Jenkins uses **Hash (H)** for load balancing, so the correct form is:

```
H/15 * * * *
```

---

# **1️⃣2️⃣ Real-Time Example (Automation Testing with Pytest)**

### **Layman Example:**

1. Tester pushes new test scripts to GitHub
2. Jenkins automatically detects it
3. Jenkins installs Python dependencies
4. Runs pytest test cases
5. Generates pytest-html or allure reports
6. Shares report link to Slack / Email
7. QA team checks results

---

### **Technical Example:**

```
Git Push → Webhook → Jenkins Job → Pull Code → Create Virtual Env →
pip install -r requirements.txt → pytest --html=report.html →
Archive Report → Email Notification
```

---

# Jenkins + GitHub + Pipeline + Scheduling — Complete Session Notes

These improved notes are structured for teaching beginners clearly and professionally.

---

## ⭐ 1. Types of Jenkins Jobs

### **1. Freestyle Project**

A simple job where you manually configure:

* Git Repo
* Build Steps (Shell/BAT commands)
* Triggers (Scheduling)
* Post-build actions

Used for: **small scripts, Robot Framework, Python, batch files**.

### **2. Pipeline Project**

A job controlled by a **Jenkinsfile**, giving full automation and CI/CD capabilities.

Used for: **Professional projects, multistage builds, GitHub integration, scheduling**.

---

## ⭐ 2. Git & GitHub Essentials

### **Initialize Git in Your Project**

```bash
git init
git add .
git commit -m "Initial commit"
```

### **Add Remote Repository (GitHub)**

```bash
git remote add origin <repo-url>
```

### **Rename Branch to `main`**

```bash
git branch -M main
```

### **Push Code to GitHub**

```bash
git push -u origin main
```

### 🔍 If push fails → Check:

* `git status`
* `git log --oneline`
* `git remote -v`

---

## ⭐ 3. Jenkins + GitHub Integration

### 🔧 **Freestyle Job Setup**

1. Open Jenkins → New Item → Freestyle Project
2. Under **Source Code Management** → Select **Git**
3. Enter Repository URL
4. Branch to build: `main`
5. Add build steps:

   * Windows: **Execute Windows Batch Command**
   * Linux: **Execute Shell**

Example batch commands:

```bat
python -m pip install -r requirements.txt
python -m robot Tests
```

---

## ⭐ 4. Jenkins Pipeline (Declarative) Example

A clean, easy-to-understand Jenkinsfile:

```groovy
pipeline {
    agent any

    triggers {
        cron('H 10 * * *')   // Daily at 10 AM
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/Poornima91-pg/Rebot_Jenkin.git'
            }
        }

        stage('Install Requirements') {
            steps {
                bat 'python -m pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'python -m robot TestCases'
            }
        }
    }
}
```

### What this pipeline does:

1. Pulls code from GitHub
2. Installs dependencies
3. Runs Robot Framework tests
4. Supports scheduling via cron

---

## ⭐ 5. Scheduling Jenkins Jobs (CRON)

### **Freestyle Job Scheduling**

1. Configure job → **Build Triggers**
2. Enable **Build periodically**
3. Add a cron expression

### **Pipeline Job Scheduling**

Use inside Jenkinsfile:

```groovy
triggers {
    cron('H 10 * * *')
}
```

### ⏰ Common Cron Expressions

| Schedule                  | Cron           | Meaning                  |
| ------------------------- | -------------- | ------------------------ |
| Every 15 minutes          | `H/15 * * * *` | Jenkins distributes load |
| Daily at 10 AM            | `H 10 * * *`   | Auto-build everyday      |
| Every 2 minutes (testing) | `H/2 * * * *`  | Quick test runs          |
| Mon–Fri at 9 AM           | `H 9 * * 1-5`  | Workdays only            |
| Sunday midnight           | `H 0 * * 0`    | Weekly build             |

---

## ⭐ 6. Common Git Errors & Fixes

### ❌ **Error: failed to push some refs**

Cause: No commit / wrong branch / no upstream.
Fix:

```bash
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

### ❌ "remote origin already exists"

Fix:

```bash
git remote remove origin
git remote add origin <repo-url>
```

---

## ⭐ 7. Finding Python Path

Run in command prompt:

```bash
where python
```

or

```bash
where python.exe
```

Use this path in Jenkins for batch commands.

---
