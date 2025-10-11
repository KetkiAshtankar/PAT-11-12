---

# **Python BDD with Behave (SauceDemo.com)**

---

## **1. What is Behaviour Driven Testing (BDD)?**

**BDD** is a software development approach that allows teams to:

* Write tests in **plain English** (or natural language).
* Connect **requirements with test cases**.
* Improve communication between **developers, testers, and business stakeholders**.

**Key idea:** Focus on **behavior of the system**, not just code.

**Example:**

> "As a user, I want to log in to SauceDemo, so that I can access my inventory."

---

## **2. Difference Between BDD & TDD**

| Feature       | TDD (Test Driven Development) | BDD (Behaviour Driven Development) |
| ------------- | ----------------------------- | ---------------------------------- |
| Focus         | Code correctness              | Behavior of application            |
| Test Language | Programming code              | Natural language (Gherkin)         |
| Collaboration | Developers only               | Developers + Testers + Business    |
| Tool Example  | Pytest, JUnit                 | Behave, Cucumber                   |

---

## **3. Introduction to Python Behave Framework**

**Behave** is a Python BDD framework that uses:

* **Gherkin syntax** (`Feature`, `Scenario`, `Given`, `When`, `Then`)
* **Step Definitions** in Python to automate tests

**Why Behave?**

* Easy to write **plain English test scenarios**
* Supports **parameterization, scenario outlines, data-driven testing**
* Can integrate with **Allure for reports**

---

## **4. Installation of Python Behave & Allure Framework**

```bash
# Create virtual environment
python -m venv .venv
source .venv/Scripts/activate   # Windows
# OR
source .venv/bin/activate       # Linux/Mac

# Install packages
pip install behave selenium openpyxl json5 allure-behave
```

**Optional:** Install ChromeDriver for Selenium automation.

---

## **5. Project Structure for Python Behave**

```
BDD_Saucedemo/
│
├── features/
│   ├── login.feature              # Feature file
│   ├── login_excel.feature        # Excel-driven scenario
│   ├── login_json.feature         # JSON-driven scenario
│   └── steps/
│       ├── login_steps.py         # Step definitions
│       ├── login_excel_steps.py
│       └── login_json_steps.py
│
├── testdata/
│   ├── login_data.xlsx
│   └── login_data.json
│
└── README.md
```

---

## **6. Introduction to Gherkin Language**

**Keywords:**

* `Feature` → What feature is being tested
* `Scenario` → Single test case
* `Given` → Precondition / setup
* `When` → Action performed
* `Then` → Expected outcome
* `And/But` → Additional steps
* `Scenario Outline` → Template for multiple examples

---

## **7. Writing Simple Test-Cases with Python Behave**

### **7.1 Normal Scenario – Login**

**Feature File:** `login.feature`

```gherkin
Feature: Login to SauceDemo

  Scenario: Successful login with standard user
    Given I open the SauceDemo login page
    When I enter username "standard_user" and password "secret_sauce"
    Then I should see the inventory page
```

**Step Definitions:** `login_steps.py`

```python
from behave import given, when, then
from selenium import webdriver
from selenium.webdriver.common.by import By

@given("I open the SauceDemo login page")
def step_open_page(context):
    context.driver = webdriver.Chrome()
    context.driver.get("https://www.saucedemo.com/")

@when('I enter username "{username}" and password "{password}"')
def step_enter_credentials(context, username, password):
    driver = context.driver
    driver.find_element(By.ID, "user-name").send_keys(username)
    driver.find_element(By.ID, "password").send_keys(password)
    driver.find_element(By.ID, "login-button").click()

@then("I should see the inventory page")
def step_verify_inventory(context):
    assert "inventory" in context.driver.current_url
    context.driver.quit()
```

---

### **7.2 Scenario with Parameters**

You can pass multiple usernames and passwords directly using placeholders:

```gherkin
Scenario: Login with multiple credentials
  Given I open the SauceDemo login page
  When I login with username "<username>" and password "<password>"
  Then I should verify login outcome
```

* Step definitions use the `{param}` syntax.
* Supports multiple users via `Examples` or external data.

---

### **7.3 Scenario Outline with Examples**

**Feature File:**

```gherkin
Scenario Outline: Login with multiple users
  Given I open the SauceDemo login page
  When I enter username "<username>" and password "<password>"
  Then I should see "<outcome>"

  Examples:
    | username       | password      | outcome          |
    | standard_user  | secret_sauce  | inventory        |
    | locked_out_user| secret_sauce  | login error      |
```

* Step definition remains same; `<username>` and `<password>` are replaced for each row.
* Avoids duplicating multiple scenarios manually.

---

### **7.4 Working with Excel**

**Feature File:** `login_excel.feature`

```gherkin
Feature: Login using Excel data

  Scenario: Verify login for multiple Excel users
    Given I open the SauceDemo login page
    When I login with credentials from Excel file
    Then I should verify successful login
```

**Step Definitions:** `login_excel_steps.py`

```python
import openpyxl, os
from behave import given, when, then
from selenium import webdriver
from selenium.webdriver.common.by import By

def read_excel(file_path):
    wb = openpyxl.load_workbook(file_path)
    sheet = wb.active
    return [(row[0].value, row[1].value) for row in sheet.iter_rows(min_row=2)]

@given("I open the SauceDemo login page")
def step_open_page(context):
    context.driver = webdriver.Chrome()
    context.driver.get("https://www.saucedemo.com/")

@when("I login with credentials from Excel file")
def step_login_excel(context):
    creds = read_excel(os.path.join("testdata", "login_data.xlsx"))
    driver = context.driver
    for username, password in creds:
        driver.find_element(By.ID, "user-name").clear()
        driver.find_element(By.ID, "password").clear()
        driver.find_element(By.ID, "user-name").send_keys(username)
        driver.find_element(By.ID, "password").send_keys(password)
        driver.find_element(By.ID, "login-button").click()
        # handle logout if successful, otherwise refresh for next row

@then("I should verify successful login")
def step_verify_excel(context):
    print("Excel-based login verification done")
    context.driver.quit()
```

---

### **7.5 Working with JSON**

**Feature File:** `login_json.feature`

```gherkin
Feature: Login using JSON data

  Scenario: Verify login for multiple JSON users
    Given I open the SauceDemo login page
    When I login with credentials from JSON file
    Then I should verify successful login
```

**Step Definitions:** `login_json_steps.py`

```python
import json, os
from behave import given, when, then
from selenium import webdriver
from selenium.webdriver.common.by import By

@given("I open the SauceDemo login page")
def step_open_page(context):
    context.driver = webdriver.Chrome()
    context.driver.get("https://www.saucedemo.com/")

@when("I login with credentials from JSON file")
def step_login_json(context):
    with open(os.path.join("testdata", "login_data.json")) as f:
        users = json.load(f)["users"]
    driver = context.driver
    for user in users:
        driver.find_element(By.ID, "user-name").clear()
        driver.find_element(By.ID, "password").clear()
        driver.find_element(By.ID, "user-name").send_keys(user["username"])
        driver.find_element(By.ID, "password").send_keys(user["password"])
        driver.find_element(By.ID, "login-button").click()
        # handle logout or refresh for next user

@then("I should verify successful login")
def step_verify_json(context):
    print("JSON-based login verification done")
    context.driver.quit()
```

---

### **8. Step Parameters & Markers**

* Parameters allow **dynamic values** in steps using `{}` or `<>`.
* **Markers / tags** allow grouping tests:

```gherkin
@smoke
Scenario: Smoke test for standard user login
  Given I open the login page
  When I login with username "standard_user" and password "secret_sauce"
  Then I should see inventory
```

Run with tag:

```bash
behave -t @smoke
```

---

### **9. Allure Report Generation**

1. Install: `pip install allure-behave`
2. Run tests with Allure formatter:

```bash
behave -f allure_behave.formatter:AllureFormatter -o allure-results
```

3. Generate HTML report:

```bash
allure serve allure-results
```

This opens a browser with a **professional test report**.

---

### **10. Recap / Summary**

* BDD focuses on **behavior**; TDD on **code correctness**.
* Behave + Gherkin allows writing tests **in plain English**.
* We can implement:

  * **Normal scenario**
  * **Scenario with parameters**
  * **Scenario outline**
  * **Data-driven tests** (Excel & JSON)
* Allure reports provide **visual test reports**.
* Project structure and step definitions make automation **reusable and maintainable**.

---

