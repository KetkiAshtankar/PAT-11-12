
---

# 🧾 ROBOT FRAMEWORK 


---

## 🧱 1. Introduction to Robot Framework

**Robot Framework** is an **open-source, generic test automation framework** that supports:

* **Acceptance Testing**
* **Behavior-Driven Testing (BDD)**
* **Robotic Process Automation (RPA)**

It uses a **keyword-driven** approach — which means you don’t write code logic line by line but **use or create keywords** written in plain English.

---

### 🔹 Developed By:

Initially created at **Nokia Networks**, and now maintained by the **Robot Framework Foundation**.
It’s written in **Python** and easily extensible using **Python or Java libraries**.

---

## 💡 2. Why Robot Framework? (Benefits)

| Benefit              | Explanation                                                                         |
| -------------------- | ----------------------------------------------------------------------------------- |
| ✅ Easy to Learn      | Uses readable syntax and tabular format — even non-programmers can read/write tests |
| 🔄 Keyword-Driven    | Reuse keywords for multiple test cases, reducing duplication                        |
| ⚙️ Extensible        | Works with Selenium, REST APIs, Databases, Jenkins, and other tools                 |
| 📄 Auto Reports      | Automatically generates detailed HTML reports and logs                              |
| 🤝 Community Support | Active open-source community and rich plugin ecosystem                              |
| 🧠 Data-Driven       | Supports data-driven testing easily via variables and loops                         |
| 🔬 CI/CD Integration | Compatible with tools like Jenkins, GitHub Actions, etc.                            |
| 🌐 Cross Platform    | Runs on Windows, Linux, and macOS                                                   |

---

## ⚙️ 3. Robot Framework Architecture

```
┌────────────────────────────────────────────┐
│                 Test Data                  │
│   (Written in .robot files using keywords) │
└────────────────────────────────────────────┘
                     │
                     ▼
┌────────────────────────────────────────────┐
│            Robot Framework Core            │
│ - Reads test data                         │
│ - Parses keywords                         │
│ - Executes test logic                     │
│ - Generates Reports/Logs                  │
└────────────────────────────────────────────┘
                     │
                     ▼
┌────────────────────────────┐   ┌────────────────────────────┐
│  Standard Libraries         │   │   External Libraries       │
│ (BuiltIn, String, DateTime) │   │ (SeleniumLibrary, etc.)   │
└────────────────────────────┘   └────────────────────────────┘
                     │
                     ▼
         Python / Java Libraries (Custom)
```

**Flow:**

1. You write test cases using **keywords**.
2. Robot reads these keywords and uses its libraries to execute them.
3. It produces **log.html**, **report.html**, and **output.xml** automatically.

---

## 🧩 4. Installation of Robot Framework

### 🧱 Step 1: Prerequisites

Install **Python 3.8+** and **pip**.

Check:

```
python --version
pip --version
```

### 🧱 Step 2: Install Robot Framework

```
pip install robotframework
```

### 🧱 Step 3: Install Selenium Support

```
pip install robotframework-seleniumlibrary
```

### 🧱 Step 4: Verify Installation

```
robot --version
```

You should see something like:

```
Robot Framework 7.0 (Python 3.11)
```

---

## 🧰 5. IDE Options / Built-in Tools

| Tool                 | Description                                                | Recommendation           |
| -------------------- | ---------------------------------------------------------- | -----------------------  |
| **VS Code**          | Lightweight, modern editor with Robot Framework extensions | ✅ Recommended            |
| **PyCharm**          | Great for Python + Robot mixed projects                    | ✅ Good for professionals |

### 📦 VS Code Extensions:

* **Robot Framework Language Server**
* **Python**
* **Selenium**

---

## 📁 6. Test Case Structure in Robot Framework

Every `.robot` file is divided into **4 main sections**:

| Section        | Header               | Description                                   |
| -------------- | -------------------- | --------------------------------------------- |
| **Settings**   | `*** Settings ***`   | Import libraries, resource files, or metadata |
| **Variables**  | `*** Variables ***`  | Store data for reuse                          |
| **Keywords**   | `*** Keywords ***`   | Create reusable actions                       |
| **Test Cases** | `*** Test Cases ***` | Write actual test logic                       |

---

### Example:

```robot
*** Settings ***
Library    SeleniumLibrary
Resource   ../resources/variables.robot

*** Variables ***
${URL}    https://www.saucedemo.com/
${BROWSER}    chrome

*** Keywords ***
Open Browser To Application
    Open Browser    ${URL}    ${BROWSER}
    Maximize Browser Window

*** Test Cases ***
Verify Login Page Loads
    Open Browser To Application
    Page Should Contain Element    id:user-name
    Close Browser
```

---

## 🧠 7. Built-in Keywords (Quick Reference)

| Keyword           | Function               |
| ----------------- | ---------------------- |
| `Log`             | Write a log message    |
| `Sleep`           | Wait for given time    |
| `Should Be Equal` | Check equality         |
| `Run Keyword If`  | Conditional execution  |
| `Evaluate`        | Run Python expression  |
| `Set Variable`    | Assign new value       |
| `Create List`     | Create a list variable |
| `Get Length`      | Returns length of list |
| `Fail`            | Force test to fail     |

### Example:

```robot
*** Test Cases ***
Math Check
    ${result}=    Evaluate    5 * 2
    Should Be Equal As Integers    ${result}    10
```

---

## 💻 8. Demo: Writing and Executing a Basic Test Case

Let’s automate a **simple login** scenario for [SauceDemo](https://www.saucedemo.com/).

```robot
*** Settings ***
Library    SeleniumLibrary

*** Variables ***
${URL}          https://www.saucedemo.com/
${USERNAME}     standard_user
${PASSWORD}     secret_sauce
${BROWSER}      chrome

*** Test Cases ***
Verify Successful Login
    Open Browser    ${URL}    ${BROWSER}
    Input Text      id:user-name    ${USERNAME}
    Input Text      id:password     ${PASSWORD}
    Click Button    id:login-button
    Wait Until Page Contains Element    xpath://div[@class='inventory_list']
    Log To Console   Login Successful!
    Close Browser
```

Run it with:

```
robot login_test.robot
```

Output generated:

* `report.html` → Summary of tests
* `log.html` → Detailed steps and screenshots (if enabled)

---

## 🧩 9. Creating a Final Framework Using Robot

Organize your project properly to make it **scalable and professional**.

```
RobotFramework-SauceDemo/
│
├── tests/
│   └── login_tests.robot
│
├── resources/
│   ├── keywords.robot
│   ├── variables.robot
│   └── locators.robot
│
├── results/
│   ├── log.html
│   ├── report.html
│
└── requirements.txt
```

### Example: keywords.robot

```robot
*** Keywords ***
Login To Application
    Input Text    id:user-name    ${USERNAME}
    Input Text    id:password     ${PASSWORD}
    Click Button    id:login-button
```

### Example: test file

```robot
*** Settings ***
Library    SeleniumLibrary
Resource   ../resources/variables.robot
Resource   ../resources/keywords.robot

*** Test Cases ***
Test Valid Login
    Open Browser    ${URL}    ${BROWSER}
    Login To Application
    Page Should Contain Element    xpath://div[@class='inventory_list']
    Close Browser
```

---

## 🚀 10. Executing and Tagging

### Add Tags:

```robot
*** Test Cases ***
Verify Successful Login
    [Tags]    smoke
    Login To Application
```

Run only smoke tests:

```
robot -i smoke tests/
```

---

---

✅ **To check the framework in repository follow following link**

```
https://github.com/KetkiAshtankar/RobotFrameWork.git
```
✅ **To check list of all builtin keywords follow following link**

```
https://robotframework.org/robotframework/2.6.1/libraries/BuiltIn.html
```


---
