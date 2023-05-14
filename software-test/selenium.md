# selenium

## 定位和等待

```python
import time

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support import expected_conditions
from selenium.webdriver.support.wait import WebDriverWait

options = webdriver.FirefoxOptions()
options.binary_location = r'C:\Program Files\Mozilla Firefox\firefox.exe'
driver = webdriver.Firefox(options=options)
driver.set_window_rect(x=128, y=64, width=1280, height=720)
driver.get('https://www.bing.com')


WebDriverWait(driver, 30, 1).until(
    expected_conditions.visibility_of_element_located(
        (By.CLASS_NAME, 'cdxAcrylicContents')))

print('The Title is', driver.title)

driver.find_element(By.ID, 'sb_form_q').send_keys('Python' + Keys.RETURN)

time.sleep(1)

driver.find_element(By.PARTIAL_LINK_TEXT, 'Welcome to Python').click()

print(driver.window_handles)

driver.switch_to.window(driver.window_handles[-1])

WebDriverWait(driver, 30, 1).until(
    expected_conditions.visibility_of_element_located(
        (By.CSS_SELECTOR, 'a[title="Service Under Maintenance"]')))

print(driver.current_url)
print(driver.current_window_handle)

a = driver.find_element(By.CSS_SELECTOR, '.main-content  .row .download-widget p a')
print(a.get_dom_attribute('href'))
print(a.text)

# from selenium.webdriver.common.action_chains import ActionChains
# ActionChains(driver, ele).send_keys('AI Chat').key_down(Keys.ENTER).perform()

time.sleep(1)

driver.quit()
```

Last Modified 2022-05-14
