

## 原因：

- 有些想要爬取的网页的数据是需要去点击才能看得到的，这时就要我们用selenium模拟浏览器操作去点击之后，再继续去爬取

## 位置：

- 在下载器中间件中去使用集成selenium

## 步骤：

1. 重写下载器中间件的`__init__`方法，来开启一个驱动

   ```python
   class JianshuDownloaderMiddleware:
       def __init__(self):
           # 初始化一个driver
           self.driver = webdriver.Chrome(executable_path='D:\chromedriver\chromedriver.exe')
   ```

2. 在`process_request`方法中拦截请求，改换成selenium去请求，并通过显式等待来等待相关事件发生

   ```python
   self.driver.get(request.url)
           next_btn_xpath = "//div[@role='main']/div/section[last()]/div/div"
           WebDriverWait(self.driver, 5).until(
               EC.element_to_be_clickable((By.XPATH, next_btn_xpath))
           )
           while True:
               try:
                   next_btn = self.driver.find_element_by_xpath(next_btn_xpath)
                   self.driver.execute_script("arguments[0].click();", next_btn)
               except:
                   break
   ```

3. 把selenium获取到的数据创建成一个Response对象返回个spider，而spider中的 response.text 就是body携带的网页源码，可以对其进行解析
   
   ```python
   response = HtmlResponse(request.url, body=self.driver.page_source, request=request, encoding='utf-8')
   
   return response
   ```
   
   
   

