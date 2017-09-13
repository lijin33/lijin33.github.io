想自己封装一个long press之后move的关键字，然后把自己搞进了坑里
报错如下：
```Traceback (most recent call last):
  File "C:\Users\js\Desktop\test.py", line 30, in <module>
    action.long_press(AppNameElment).release().perform()
  File "C:\Python27\lib\site-packages\appium\webdriver\common\touch_action.py", line 94, in perform
    self._driver.execute(Command.TOUCH_ACTION, params)
  File "C:\Python27\lib\site-packages\selenium\webdriver\remote\webdriver.py", line 236, in execute
    response = self.command_executor.execute(driver_command, params)
  File "C:\Python27\lib\site-packages\selenium\webdriver\remote\remote_connection.py", line 410, in execute
    command_info = self._commands[command]
KeyError: 'touchAction'
[Finished in 32.8s with exit code 1]
```
原因咳咳：
```
from selenium import webdriver
from appium.webdriver.common.touch_action import TouchAction
```
把第一行改成：
```
from appium import webdriver
```
就好用了
import selenium的话，执行perform()时调用的是selenium.webdriver.remote中remote_connection中的execute，就找不到touchAction啦

这个坑坑了我好几天，改好之后心情愉悦
BTW简书的markdown功能真是简啊…[捂脸]
