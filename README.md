# wafris-rb

## What's Wafris?
Wafris is an open-source Web Application Firewall (WAF) that runs within your existing web framework powered by Redis.

Need a better explanation? Read the overview at: [wafris.org](https://wafris.org)

## What's the Wafris Ruby client (this repository)

The Wafris Ruby client is gem that installs a Rack middleware into your Rails/Sinatra/Rack app that adds a Web Application Firewall (WAF).

The WAF features allow you to:

- Analyze the dark traffic hitting your site
- Determine what requests should be blocked
- Block malicious IP addresses (IPv6 and IPv4) from making requests
- Can also block on hosts, paths, user agents, parameters, and methods
- Create rate limit rules
- Block by CIDR ranges
- Allow list for IPs and CIDRs
- Detect malicious traffic patterns

## Installation and Configuration

### Requirements
- Redis 4.8+
- Rails 5+
- Ruby 2.5+

If you have a previous version of one of the requirements above please let us know and we'll test it out.

### 1. Add the gem

Update your Gemfile to include the Wafris gem and run `bundle install`

```
# Gemfile
gem 'wafris'
```

#### In case you have Rack::Attack

If you have Rack::Attack already installed, it can workk side by side with Wafris. You have two options:
1. Include the Rack::Attack middleware before Wafris.
2. Include the Rack::Attack middleware after Wafris.

We recommend including Wafris before Rack::Attack so that Wafris can capture the traffic data before it is blocked.
Then you can gradually migrate your rule set from Rack::Attack to Wafris and visualize the blocked traffic.

To ensure the order it's as simple as including the `wafris` gem before the `rack-attack` gem.

```
# Gemfile
gem 'wafris'
gem 'rack-attack'
```

to confirm that the order is correct you can run `rake middleware` and the output should look like:

### 2. Set your Redis Connection

By default Wafris will use the Redis instance defined in the environment variable `ENV['REDIS_URL']`

If you need to specify a different Redis location you can do so with an initalizer.

```ruby
# config/initalizers/wafris.rb

Wafris.configure do |c|
    c.redis = Redis.new(
      url: ENV['REDIS_URL']
    )
end
```

Note: depending upon your Redis provider the environment variable for your Redis connection string may be named something else.

### 3. Connect on Wafris Hub

Go to https://wafris.org/hub to login or create a new account.

- Add a new Firewall using the Redis URL you specified in step two.

Alternatively, you can add rules to your Wafris instance from the Wafris CLI - https://github.com/Wafris/wafris-cli 


### 4. Testing in Development (optional)

If you'd like to ensure that Waris is working properly you can launch it in development. Ensure that your Wafris configuration
or `REDIS_URL` is set, otherwise Wafris will use the default Redis connection: `localhost:6379/0`.

Set a block path using the following command where `<path>` is the bath you'd like to block:

```sh
redis-cli HSET rules-blocked-p <path> "This is a test rule"
```

Then visit this path in your browser: `http://localhost:3000/<path>` and you should see a page with
'blocked' and a 403 status code.

## Trusted Proxies

If you have Cloudflare, Expedited WAF, or another service in front of your application that modifies the `x-forwarded-for` HTTP Request header, please review how to configure [Trusted Proxy Ranges](docs/trusted-proxies.md)

## Help / Support

For any trouble configuring Wafris please email [support@wafris.org](mailto:support@wafris.org)

Or you can book at time at: https://app.harmonizely.com/expedited/wafris 

<img src='https://uptimer.expeditedsecurity.com/wafris-rb' width='0' height='0'>
