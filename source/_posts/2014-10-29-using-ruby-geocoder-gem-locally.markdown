---
layout: post
title: "Using Ruby Geocoder gem locally"
date: 2014-10-29 23:06
comments: false
categories: ruby gems rails development geocoder
---
These days I have been needing test a small feature (for a personal project) related with Geolocalization. The first and most obvious
option was used [Geocoder](http://www.rubygeocoder.com) which has a number features enough for what I'm needing.

Alright I already have settled the gem on my Gemfile, but *How I can test this gem on my local environment?* due to in the [docs](https://github.com/alexreisner/geocoder#request-geocoding-by-ip-address)
it's clear when you use this Gem with a IP `0.0.0.0` the [Geocoder::Result](http://www.rubydoc.info/github/alexreisner/geocoder/master/Geocoder/Result)
object is `nil`.

Fortunately there is a option at least I can figure out this one in order to sort out the previous issue and do test on local.

We're going to use Rack middleware in order to mock the IP with a custom one.

Let's create a file which we called it `fake_ip.rb` with following content.

{% codeblock lang:ruby %}
class FakeIp
  def initialize(app, ip)
    @app = app
    @ip = ip
  end

  def call(env)
    env['HTTP_X_FORWARDED_FOR'] = nil
    env['REMOTE_ADDR'] = env['action_dispatch.remote_ip'] = @ip
    @status, @headers, @response = @app.call(env)
    [@status, @headers, @response]
  end
end
{% endcodeblock %}

The previous code is pretty simple, the interesting part is on `call` method which change the values for some of
elements `env`.

In terms very simple The `env` variable is a hash. That hash contains a lot of useful information including
request headers and body, as well as run-time environment data that may have been added by upstream middleware.

Now, edit `config/application.rb` and put:


{% codeblock lang:ruby %}
config.middleware.use('FakeIp', '187.16.144.141')
{% endcodeblock %}

The previous code allow us to mock the IP with someone custom. Now if you visit a controller a inspect the content

{% codeblock lang:ruby %}
puts request.location.country_code
# should see the country code for IP 187.16.144.141 `BR`
{% endcodeblock %}

I wrote a small [rack-app](https://github.com/deivinsontejeda/rack-app) in order to show you the whole workflow.

Do you know a better way to do this? Share it!
