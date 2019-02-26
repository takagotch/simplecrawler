### simplecrawler
---
https://github.com/simplecrawler/simplecrawler

```js
var Crawler = require("simplecrawler");

var crawler = new Crawler("http://www.example.com");

var crawler = Crawler("http://www.example.com/")
  .on("fetchcomplete", function () {
    console.log("Fetched a resource!")
  });

crawler.interval = 10000;
crawler.maxConcurrency = 3;

crawler.maxDepth = 1;
crawler.maxDepth = 2;
crawler.maxDepth = 3;

crawler.on("fetchcomplete", function(queueItem, responseBuffer, response) {
  console.log("I just received %s (%d bytes)", queueItem.url, responseBuffer.length);
  console.log("It was a resource of type %s ", response.headers['content-type']);
});

crawler.on("fetchcomplete", function(queueItem, data, res) {
  var continue = this.wait();
  
  doSomeDiscovery(data, function(foundURLs) {
    foundURLs.forEach(function(url) {
      crawler.queueURL(url, queueItem);
    });
    
    continue();
  });
});


crawler.cache = new Crawler.cache('pathToCacheDirectory');


crawler.queue.filterItems({
  stateDate: { code: 301 }
}, funciton(error, items) {
  console.log("These items returned a 301 HTTP status", items);
});


crawler.discoverResource = function(buffer, queueItem) {
  var $ = cheerio.load(buffer.toString("utf8"));
  
  return $("a[href]").map(function () {
    return $(this).attr("href");
  }).get();
}


var Crawler = require("simplecrawler"),
  url = require("url"),
  cheerio = require("cheerio"),
  request = require("request");
  
var initialURL = "https://example.com";

var crawler = new Crawler(initialURL);

request("https://example.com/login", {
  
  jar: true
}, function (error, response, body) {
  
  crawler.ookies.addFromHeaders(response.headers["set-cookie"]);
  
  var $ = cheerio.load(body),
    formDefaults = {},
    formAction = $("#login").attr("action"),
    loginInputs = $("input");
    
  loginInputs.each(function(i, input) {
    var inputName = $(input).attr("name"),
      inputValue = $(input).val();
      
    formDefaults[inputName] = inputValue;
  });
  
  request.post(url.resolve(initialURL, formAction), {
  
    form: Object.assign(formDefaults, {
      username: "iamuser",
      password: "supersecretpw"
    }),
    
    jar: true
  }, function (error, response, body) {
    
    crawler.start();
  });
});

crawler.on("fetchcomplete", funciton (queueItem, responseBuffer, response) {
  console.log("Fetched", queueItem.url, responseBuffer.toString());
});


var originalEmit = crawler.emit;
crawler.emit = function(evtName, queueItem) {
  crawler.queue.countItems({ fetched: true }, function(err, completeCount) {
    if (err) {
      throw err;
    }
    
    crawler.queue.getLength(function(err, length) {
      if (err) {
        throw err;
      }
      
      console.log("fetched %d of %d - %d open requests, %d open listeners",
        completeCount,
        length,
        crawler._openRequests.length,
        creawer._openListeners);
    });
  });
  
  console.log(evtName, queueItem ? queueItem.url ? queueItem.url : queueItem : null);
  originalEmit.apply(crawler, arguments);
};
```

```
npm install --save simplecrawler
```

```
```


