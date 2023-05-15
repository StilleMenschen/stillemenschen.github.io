# selenium

## 定位等待和键鼠

```python
import time
from urllib.parse import unquote

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.wait import WebDriverWait


class TestCase:
    options = webdriver.FirefoxOptions()
    options.binary_location = r'C:\Program Files\Mozilla Firefox\firefox.exe'

    def __init__(self):
        self.driver = webdriver.Firefox(options=self.options)
        self.driver.set_window_rect(x=128, y=64, width=1280, height=720)

    def wait_element(self, by: By, val):
        WebDriverWait(self.driver, 30, 1).until(
            lambda driver: driver.find_element(by, val))

    def match_bing(self):
        self.driver.get('https://www.bing.com')
        self.wait_element(By.CLASS_NAME, 'cdxAcrylicContents')

        print('The Title is', self.driver.title)

        self.driver.find_element(By.ID, 'sb_form_q').send_keys('Python' + Keys.RETURN)

        self.wait_element(By.PARTIAL_LINK_TEXT, 'Welcome to Python')
        self.driver.find_element(By.PARTIAL_LINK_TEXT, 'Welcome to Python').click()

        print(self.driver.window_handles)

        self.driver.switch_to.window(self.driver.window_handles[-1])

        self.wait_element(By.CSS_SELECTOR, 'a[title="Service Under Maintenance"]')

        print(self.driver.current_url)
        print(self.driver.current_window_handle)

        a = self.driver.find_element(By.CSS_SELECTOR, '.main-content .row .download-widget p a')
        print(a.get_dom_attribute('href'))
        print(a.text)

        # from selenium.webdriver.common.action_chains import ActionChains
        # ActionChains(driver, ele).send_keys('AI Chat').key_down(Keys.ENTER).perform()

        self.driver.close()

        self.driver.switch_to.window(self.driver.window_handles[0])

    def match_baidu(self):
        self.driver.get('https://www.baidu.com')
        self.wait_element(By.ID, 'kw')

        element = self.driver.find_element(By.ID, 'kw')
        element.send_keys('Python 协程 原理')
        element.send_keys(Keys.CONTROL, 'a')
        element.send_keys(Keys.CONTROL, 'c')

        self.wait_element(By.ID, 'su')

        self.driver.find_element(By.ID, 'su').click()

        self.wait_element(By.PARTIAL_LINK_TEXT, '协程原理')
        self.driver.find_element(By.PARTIAL_LINK_TEXT, '协程原理').click()

        print(self.driver.window_handles)

        self.driver.switch_to.window(self.driver.window_handles[-1])

        self.wait_element(By.CSS_SELECTOR, '.profile-box .profile-img')

        name = self.driver.find_element(By.CSS_SELECTOR, '.profile-box .profile-name')
        print(name.text)
        print(name.rect)

        self.wait_element(By.ID, 'toolbar-search-input')

        element = self.driver.find_element(By.ID, 'toolbar-search-input')
        element.send_keys(Keys.CONTROL, 'v')

        user_agent = self.driver.execute_script('return navigator.userAgent')
        print(user_agent)

        self.driver.close()

        self.driver.switch_to.window(self.driver.window_handles[0])

        self.wait_element(By.CSS_SELECTOR, '.cr-content.new-pmd')

        link_list = self.driver.find_elements(By.CSS_SELECTOR, '.cr-content.new-pmd a')

        for link in link_list:
            href = link.get_attribute('href')
            if not href.startswith('http'):
                continue
            print(unquote(href)[:200])

    def __del__(self):
        self.driver.quit()


if __name__ == '__main__':
    t0 = time.monotonic()
    case = TestCase()
    # case.match_bing()
    case.match_baidu()
    print('time elapsed:', time.monotonic() - t0, 's')
```

Last Modified 2022-05-15
