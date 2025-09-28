---

# **Playwright with Python – Complete Notes**

---

## **1. What is Playwright?**

Playwright is a **modern open-source automation framework** for testing web applications across multiple browsers like **Chromium, Firefox, and WebKit**.

* Supports **cross-browser testing**
* Handles **modern web apps** with dynamic content
* Provides **auto-waiting** and **headless/headful execution**
* Supports **Python, JavaScript/TypeScript, Java, and C#**

---

## **2. Why Python?**

* Simple and beginner-friendly syntax
* Large community & rich libraries
* Easy integration with **pytest** for test management
* Easy to generate **Allure Reports** for test results

---

## **3. Difference between Playwright and Other Automation Tools**

| Feature              | Playwright                | Selenium        | Cypress             |
| -------------------- | ------------------------- | --------------- | ------------------- |
| Browser support      | Chromium, Firefox, WebKit | Many browsers   | Chromium-based only |
| Language support     | Python, JS, Java, C#      | Multiple        | JS/TS only          |
| Auto-wait            | Yes                       | No              | Yes                 |
| Headless mode        | Yes                       | Yes             | Yes                 |
| Handling dynamic UI  | Excellent                 | Moderate        | Good                |
| Network interception | Built-in                  | Plugin required | Limited             |

---

## **4. Playwright Architecture**

* **Playwright CLI** – Manages installation of browsers
* **Test Runner / Automation Library** – Python or JS scripts run against the browser
* **Browser Drivers** – Chromium, Firefox, WebKit drivers controlled via **Playwright API**
* **IDE / Test Editor** – PyCharm, VSCode for writing scripts
* **Reporting** – Integration with **Allure or pytest-html**

Diagram:

```
[Your Python Script] -> [Playwright API] -> [Browser Drivers] -> [Actual Browser]
```

---

## **5. Installing and Configuring Playwright**

### **Step 1: Install Python**

* Download from [https://www.python.org/downloads/](https://www.python.org/downloads/)
* Verify:

```bash
python --version
pip --version
```

### **Step 2: Install IDE**

* Recommended: **PyCharm** or **VSCode**

### **Step 3: Create a Python Project**

* Open IDE → New Project → Choose Interpreter (Python 3.10+)

### **Step 4: Create GitHub Repo**

```bash
git init
git remote add origin <repo_url>
git clone <repo_url>
```

### **Step 5: Install Playwright**

```bash
pip install playwright
```

### **Step 6: Install Browsers**

```bash
playwright install
playwright install --with-deps  # optional full installation
```

---

## **6. Record and Play First Test**

### **Step 1: Launch Codegen**

```bash
playwright codegen https://www.saucedemo.com
```

### **Step 2: Interact with Website**

* Click login, fill username & password
* Playwright generates Python code

### **Step 3: Save and Run Script**

```bash
python first_test.py
```

---

## **7. Test Structure**

### **AAA (Arrange-Act-Assert)**

```python
# Arrange
page.goto("https://www.saucedemo.com/")

# Act
page.fill("#user-name", "standard_user")
page.fill("#password", "secret_sauce")
page.click("#login-button")

# Assert
expect(page.locator(".title")).to_have_text("Products")
```

### **Given / When / Then**

```python
# Given
page.goto("https://www.saucedemo.com/")

# When
page.fill("#user-name", "standard_user")
page.fill("#password", "secret_sauce")
page.click("#login-button")

# Then
expect(page.locator(".title")).to_have_text("Products")
```

---

## **8. Practice Test Case – First Script**

```python
from playwright.sync_api import sync_playwright, expect

def test_login():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        page.goto("https://www.saucedemo.com/")
        page.fill("#user-name", "standard_user")
        page.fill("#password", "secret_sauce")
        page.click("#login-button")
        expect(page.locator(".title")).to_have_text("Products")
        browser.close()
```

---

## **9. Handling Waits in Playwright**

* **Auto-wait** for elements/actions
* **Explicit Waits**:

```python
page.wait_for_selector(".inventory_list")
page.wait_for_url("**/inventory.html")
page.wait_for_load_state("networkidle")
```

* **Custom Wait**:

```python
import time
def custom_wait(page, selector, timeout=5000):
    start = time.time()
    while True:
        if page.locator(selector).is_visible():
            return True
        if (time.time() - start) * 1000 > timeout:
            raise Exception(f"Timeout waiting for {selector}")
        time.sleep(0.5)
```

---

## **10. Assertions**

```python
# Visibility
expect(page.locator("#login-button")).to_be_visible()

# Text
expect(page.locator(".title")).to_have_text("Products")

# Count
expect(page.locator(".cart_item")).to_have_count(2)

# URL
expect(page).to_have_url("**/inventory.html")
```

---

## **11. Wait for Selector & Page Load**

```python
page.wait_for_selector(".inventory_item")
page.goto("https://www.saucedemo.com/", wait_until="domcontentloaded")
```

---

## **12. Mini-Project – Checkout Flow**

```python
from playwright.sync_api import sync_playwright, expect

def test_checkout():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        # Login
        page.goto("https://www.saucedemo.com/")
        page.fill("#user-name", "standard_user")
        page.fill("#password", "secret_sauce")
        page.click("#login-button")
        expect(page.locator(".title")).to_have_text("Products")

        # Add items
        page.click("#add-to-cart-sauce-labs-backpack")
        page.click("#add-to-cart-sauce-labs-bike-light")
        expect(page.locator(".shopping_cart_badge")).to_have_text("2")

        # Cart & Checkout
        page.click(".shopping_cart_link")
        page.wait_for_load_state("networkidle")
        expect(page.locator(".cart_item")).to_have_count(2)
        page.click("#checkout")
        page.fill("#first-name", "John")
        page.fill("#last-name", "Doe")
        page.fill("#postal-code", "12345")
        page.click("#continue")
        expect(page.locator(".title")).to_have_text("Checkout: Overview")
        page.click("#finish")
        expect(page.locator(".complete-header")).to_have_text("Thank you for your order!")
        browser.close()
```

---

## **13. Generating Allure Reports**

### **Step 1: Install Allure-pytest**

```bash
pip install allure-pytest
```

### **Step 2: Run tests with pytest and allure**

```bash
pytest test_checkout.py --alluredir=allure-results
```

### **Step 3: Install Allure Commandline**

* Mac: `brew install allure`
* Windows: Download binary from [https://docs.qameta.io/allure/#_get_started](https://docs.qameta.io/allure/#_get_started)

### **Step 4: Generate Report**

```bash
allure serve allure-results
```

* Opens HTML report in browser showing test steps, status, and screenshots.

---

## **14. Key Notes**

* Use **`expect()`** over `assert` for auto-waiting
* Avoid `time.sleep()` unless debugging
* Structure tests using **AAA** or **Given/When/Then**
* Mini-projects should validate **flow, cart, checkout**
* Allure reports provide **rich visualization** of results

---
