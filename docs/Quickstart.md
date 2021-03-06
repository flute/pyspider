Quickstart
==========

Installation
------------

* `pip install pyspider`
* run command `pyspider`, visit [http://localhost:5000/](http://localhost:5000/)

if you are using ubuntu, try:
```
apt-get install python python-dev python-distribute python-pip libcurl4-openssl-dev libxml2-dev libxslt1-dev python-lxml
```
to install binary packages first.

**Note:** `pyspider` command is running pyspider in `all` mode, which running components in threads or subprocesses. For production environment, please refer to [Deployment](Deployment).

Your First Script
-----------------

```python
from pyspider.libs.base_handler import *

class Handler(BaseHandler):
    crawl_config = {
    }

    @every(minutes=24 * 60)
    def on_start(self):
        self.crawl('http://scrapy.org/', callback=self.index_page)

    @config(age=10 * 24 * 60 * 60)
    def index_page(self, response):
        for each in response.doc('a[href^="http"]').items():
            self.crawl(each.attr.href, callback=self.detail_page)

    def detail_page(self, response):
        return {
            "url": response.url,
            "title": response.doc('title').text(),
        }
```

> * `def on_start(self)` is the entry point of the script. It will be called when you click the `run` button on dashboard.
> * [`@every(minutes=24*60, seconds=0)`*](/apis/@every/) is a helper to tell the scheduler that `on_start` method should be called every 24*60 minutes = 1 day
> * [`self.crawl(url, callback=self.index_page)`*](/apis/self.crawl) is the most important API here. It will add a new task to be crawled. `callback` is the method to parse the [`response`*](/apis/Response).
> * parameters of [`self.crawl`*](/apis/self.crawl) can also be set via `crawl_config` and `@config`.
> * `def index_page(self, response)` will get a [`Response`*](/apis/Response) object. [`response.doc`*](/apis/Response/#responsedoc) is a [pyquery](https://pythonhosted.org/pyquery/) object which has jquery-like API to select elements to be extracted.
> * `def detail_page(self, response)` return a `dict` object as result. The result will be captured into `resultdb` by default. You can override `on_result(self, result)` method to manage the result yourself.

You can test your script step by step by click the green `run` button. Switch to `follows` panel, click the play button to move on.

![run one step](imgs/run_one_step.png)

Start Running
-------------

1. Save your script.
2. Back to dashboard find your project.
3. Changing the `status` to `DEBUG` or `RUNNING`.
4. Click the `run` button.

![index demo](imgs/index_page.png)

Your script is running now!
