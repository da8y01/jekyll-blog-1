---
layout: post
title:  "A simple ruby twitter client"
date:   2014-11-02 18:14:44
tags: simple ruby twitter client dev development programming code tw
---



## Install the gem
Proceed to install the required *gem* with `gem install twitter` as indicated at [https://github.com/sferik/twitter][1]{: target="_blank"}.


## Sample code
{% highlight ruby linenos %}
require "twitter" # include lib/gem

# set the configuration object with the application and access tokens provided by registering an app on twitter site
config = {
  consumer_key: 'YOUR_consumerkey', 
  consumer_secret: 'YOUR_consumersecret', 
  access_token: 'YOUR_accesstoken', 
  access_token_secret: 'YOUR_accesstokensecret'
}
client = Twitter::REST::Client.new(config) # instantiate the working object

client.update('Tweet from simple ruby client.') # execute a status update (a tweet)
# perform as many other operations you want with the client object, read the gem page (see "references") ...
{% endhighlight %}


## References
* [https://github.com/sferik/twitter][1]{: target="_blank"}



[1]: https://github.com/sferik/twitter
