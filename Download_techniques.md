
````markdown
# Selenium + PyAutoGUI + File Download Automation (Pytest)

## 📌 Overview
This session covered:
- Using **PyAutoGUI** to control the keyboard and mouse for GUI automation.
- Combining **Selenium + PyAutoGUI** to interact with websites and desktop dialogs.
- Handling **file downloads in Chrome** with Selenium by configuring browser options.
- Wrapping download automation in **Pytest** with fixtures and assertions.

---

## 🔹 Part 1: PyAutoGUI Basics
```python
import pyautogui
import time

time.sleep(3) 
pyautogui.hotkey("win")
pyautogui.typewrite("Notepad", interval=0.2)
pyautogui.hotkey("enter")
time.sleep(2)
pyautogui.typewrite(" Hi from inside notepad ", interval=0.2)
time.sleep(3)
````

### ✅ What it does

1. Waits 3s, opens Start Menu (`win` key).
2. Types **Notepad** and opens it.
3. Types text inside Notepad.
4. Demonstrates basic **keyboard & mouse automation**.

---

## 🔹 Part 2: Selenium + PyAutoGUI (Save Image)

```python

from selenium import webdriver
from selenium.webdriver.common.by import By
import pyautogui
import time


options = webdriver.ChromeOptions()
options.add_argument("--incognito")
driver = webdriver.Chrome(options=options)

driver.maximize_window()
driver.get("https://www.saucedemo.com")
time.sleep(2)

driver.find_element(By.ID, "user-name").send_keys("standard_user")
driver.find_element(By.ID, "password").send_keys("secret_sauce")
driver.find_element(By.ID, "login-button").click()
time.sleep(2)

first_image = driver.find_element(By.CSS_SELECTOR,".inventory_item_img")
loc = first_image.location
size = first_image.size

x = loc['x'] + size['width'] / 2
y = loc['y'] + size['height'] / 2 + 150

pyautogui.moveTo(x, y, duration=1)
pyautogui.rightClick()
time.sleep(7)
pyautogui.press('down', presses=7, interval=0.2)
pyautogui.press('enter')
time.sleep(2)
pyautogui.typewrite("first_image", interval=0.2)
pyautogui.press('enter')
time.sleep(5)

driver.quit()

```

### ✅ What it does

* Uses Selenium to locate **first product image** on [SauceDemo](https://www.saucedemo.com/).
* Moves mouse to image, right-clicks, chooses **Save image as**.
* Saves file using PyAutoGUI keystrokes.

⚠️ Limitation → Depends on screen resolution and Chrome version.

---

## 🔹 Part 3: Selenium File Download with Chrome Options

```python
import os
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
import time
from selenium.webdriver.common.by import By

download_path = os.path.join(os.getcwd(), "chromedownloads")  # Set the download path to the current working directory
os.makedirs(download_path, exist_ok=True)  # Create the download directory if it doesn't exist

chrome_options = Options()
# chrome_options.add_argument("--headless")  
chrome_options.add_argument("--disable-notifications")  
prefs = {
    "download.default_directory": download_path,  # Set the default download directory
    "download.prompt_for_download": False,  # Disable the download prompt
    "download.directory_upgrade": True  # Upgrade the download directory
}

chrome_options.add_experimental_option("prefs", prefs)  # Set experimental options

driver = webdriver.Chrome(options=chrome_options)  # Create a new Chrome WebDriver instance with the specified options
driver.get("https://practice.expandtesting.com/download")  # Navigate to a website
time.sleep(3)  
driver.maximize_window()  # Maximize the browser window
time.sleep(2)  
link = driver.find_element(By.LINK_TEXT, "1758359701850_ss.png")  # Find the download link by its text
print("Found link:", link.text)  # Print the text of the found link

driver.execute_script("arguments[0].scrollIntoView(true);", link)  # Scroll to the download link
time.sleep(2) 

driver.execute_script("arguments[0].click();", link)  # Click the download link using JavaScript
time.sleep(5)

# Improved wait for download to complete
timeout = 60  # Increased timeout for download
for _ in range(timeout):
    files = os.listdir(download_path)  # List the files in the download directory
    if any(fname.endswith(".rtf") for fname in files):  # Check if any .rtf files are present
        print("Download complete!")
        break
    time.sleep(1)
else:
    print("Download did not complete in time.")

print("Downloaded Files in directory:", os.listdir(download_path))  # Display the list of downloaded files
time.sleep(5)  
driver.quit()

```

### ✅ Key Points

* **`download.default_directory`** → sets custom folder for downloads.
* **`download.prompt_for_download`** → skips Save As dialog.
* **`download.directory_upgrade`** → ensures folder upgrade.
* Useful for **CI/CD pipelines**.

---

## 🔹 Part 4: Wrapping in Pytest

### `conftest.py`

```python
import os
import pytest
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

@pytest.fixture(scope="class")
def setup():
    download_path = os.path.join(os.getcwd(), "chromedownloads")
    os.makedirs(download_path, exist_ok=True)

    chrome_options = Options()
    chrome_options.add_argument("--disable-notifications")
    prefs = {
        "download.default_directory": download_path,
        "download.prompt_for_download": False,
        "download.directory_upgrade": True
    }
    chrome_options.add_experimental_option("prefs", prefs)

    driver = webdriver.Chrome(options=chrome_options)
    driver.maximize_window()
    yield driver, download_path
    driver.quit()
```

### `test_download.py`

```python
import os
import time
import pytest
from selenium.webdriver.common.by import By

@pytest.mark.usefixtures("setup")
class TestFileDownload:

    def test_download_file(self, setup):
        driver, download_path = setup
        driver.get("https://practice.expandtesting.com/download")
        time.sleep(3)

        link = driver.find_element(By.PARTIAL_LINK_TEXT, ".png")
        filename = link.text.strip()
        driver.execute_script("arguments[0].click();", link)

        timeout = 60
        downloaded = False
        for _ in range(timeout):
            if any(fname.endswith(".png") for fname in os.listdir(download_path)):
                downloaded = True
                break
            time.sleep(1)

        assert downloaded, f"{filename} not downloaded in {timeout} sec"
        print("Downloaded files:", os.listdir(download_path))
```


## 📚 Key Takeaways

* **PyAutoGUI** → Best for desktop-level automation (keyboard, mouse).
* **Selenium** → Best for browser control.
* **Mix** → Can handle tricky situations like right-click save.
* **Chrome Options + Pytest** → Best practice for automated downloads in CI/CD.

```
