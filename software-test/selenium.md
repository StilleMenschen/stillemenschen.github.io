# selenium

## 定位和等待

```python
import time

from selenium import webdriver
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support import expected_conditions
from selenium.webdriver.support.wait import WebDriverWait

options = webdriver.FirefoxOptions()
options.binary_location = r'C:\Program Files\Mozilla Firefox\firefox.exe'
driver = webdriver.Firefox(options=options)
driver.set_window_rect(x=64, y=128, width=1280, height=720)
driver.get('https://www.bing.com')

ele = driver.find_element(By.ID, 'sb_form_q')

WebDriverWait(driver, 30, 1).until(
    expected_conditions.visibility_of_element_located(
        (By.CLASS_NAME, 'cdxAcrylicContents')))

print('The Document Title is', driver.title)

ActionChains(driver, ele) \
    .send_keys('AI Chat') \
    .key_down(Keys.ENTER).perform()

time.sleep(2)

driver.quit()
```

Last Modified 2022-05-14
