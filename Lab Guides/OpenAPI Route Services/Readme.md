
# Apigee Service Broker and OpenAPI

## Head's Up!
In this section we'll be covering some beta capabilities of the Apigee Edge Service Broker. Specific details are likely to change!

## Overview

An API is an interface between the provider of some backend system(s) who wants to expose a set of services and the consumers of those services who want to do something with them. Adherence to a “contract” or a clear understanding of what the requests and responses should look like, makes life easier for all participants. In order to clearly communicate the terms of that contract, the industry has created a few formats for describing an API, the most popular of which is called [*OpenAPI Specification*](https://openapis.org/).

By designing your API in OpenAPI spec, you allow the API developer and the API consumer to both do their jobs and meet successfully in the middle without unpleasant surprises.

Apigee is a key contributing member of and has partnered with a number of other companies to help drive the OpenAPI spec and contribute open source software to the community.

This is a partial list of the tools that Apigee has created, open-sourced, or contributed to related to API-first design, OpenAPI spec, or API deployment:

-   [*http://specgen.apistudio.io/*](http://specgen.apistudio.io/) - OpenAPI Spec generator
  > Make API calls and automatically get an OpenAPI specification. The easiest way to write a spec for an existing API!

-   [*http://apistudio.io*](http://apistudio.io/) - In-browser OpenAPI
    > IDE including live documentation, code generation, mocking, and
    > cloud hosting.

-   [*https://github.com/apigee-127/a127-documentation/wiki*](https://github.com/apigee-127/a127-documentation/wiki) -
    > A toolkit for modeling and building rich, enterprise-class APIs in
    > Node.js on your laptop.

-   [*http://editor.swagger.io*](http://editor.swagger.io) -
    > This editor is the basis for the ones used in the above two projects, but it
    > also includes code generators for a number of other languages.


## Vendor Extensions
One cool thing about the OpenAPI Specification is support for vendor extensions.

[*https://github.com/OAI/OpenAPI-Specification/blob/master/guidelines/EXTENSIONS.md*](https://github.com/OAI/OpenAPI-Specification/blob/master/guidelines/EXTENSIONS.md)

These allows the addition of custom attributes to a specification. While they are often used to provide information to the end consumer of the API, they don't have to be restricted to that purpose. For example, a tool that automatically created proxies for an API running in PCF might want to leverage OpenAPI and vendor extensions to do some fun things... ;-)

## Updated Quotes API
Pull down the second release of the Quotes API, available here:

[*https://github.com/carloseberhardt/quote-service/archive/v1.1.0.tar.gz*](https://github.com/carloseberhardt/quote-service/archive/v1.1.0.tar.gz).

Extract it into a working directory, build it, test it locally, and push it to a new application in your space.
```Shell
➜  quote-service-1.0.0 cd ..
➜  working ls
quote-service-1.0.0  v1.0.0.tar.gz
➜  working curl -O -L https://github.com/carloseberhardt/quote-service/archive/v1.1.0.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   137    0   137    0     0   2575      0 --:--:-- --:--:-- --:--:--  2584
  0     0    0  7263    0     0  75304      0 --:--:-- --:--:-- --:--:-- 75304
➜  working tar xzf v1.1.0.tar.gz
➜  working cd quote-service-1.1.0
➜  quote-service-1.1.0 mvn clean install
[INFO] Scanning for projects...
[INFO]

<snip>
➜  quote-service-1.1.0 mvn spring-boot:run
[INFO] Scanning for projects...

<snip>

<validate it>

➜  quote-service-1.1.0 cf push carlos2quotes
Using manifest file /home/ubuntu/working/quote-service-1.1.0/manifest.yml
<snip>

```

This version of the API only has one new trick: it returns an OpenAPI spec when you hit the `/openApi.json` path:

```Shell
➜  ~ curl http://carlos2quotes.pcf.apigeese.net/openApi.json
{
  "basePath": "/quotes",
  "host": "carlos2quotes.pcf.apigeese.net",
  "info": {
    "contact": {
      "email": "carlos@apigee.com",
      "name": "Quotes API Team",
      "url": "apigee.com"
    },
<snip>
```

## Quotes API Spec
If you skim through that you'll notice a couple of interesting items. Vendor Extensions! (Search for "apigee")...

```Shell
"x-apigee-policies": {
  "JsonToXml": {
    "options": {
      "on": "response"
    },
    "type": "jsonToXml"
  },
  "MarketSummarySpikeArrest": {
    "options": {
      "allow": 5,
      "timeUnit": "minute"
    },
    "type": "spikeArrest"
  },
<snip>
```

These are vendor extensions that the Service Broker understands. The broker will automatically create a proxy with the specified policies if it finds an OpenAPI specification like the one here. So let's try it...

## Bind the New API to Edge
Bind the route for this new app as we did previously...
```Shell
➜  quote-service-1.1.0 cf brs pcf.apigeese.net carlos-edge --hostname carlos2quotes
Binding route carlos2quotes.pcf.apigeese.net to service instance carlos-edge in org springone / space carloseberhardt as springone...
OK
➜  quote-service-1.1.0
```

## Call the New API
When you call the API now, you'll see that it's already got some API Management capabilities built in.

```Shell
➜  quote-service-1.1.0 curl http://carlos2quotes.pcf.apigeese.net/quotes/marketSummary
<?xml version="1.0" encoding="UTF-8"?><Root><average>2170.06</average><open>2166.58</open><volume>5.6454394E8</volume><change>3.48</change><percentGain>+0.16%</percentGain></Root>%                      
➜  quote-service-1.1.0 curl http://carlos2quotes.pcf.apigeese.net/quotes/marketSummary
<?xml version="1.0" encoding="UTF-8"?><Root><average>2170.06</average><open>2166.58</open><volume>5.6454394E8</volume><change>3.48</change><percentGain>+0.16%</percentGain></Root>%                      
➜  quote-service-1.1.0 curl http://carlos2quotes.pcf.apigeese.net/quotes/marketSummary
{"fault":{"faultstring":"Spike arrest violation. Allowed rate : 5pm","detail":{"errorcode":"policies.ratelimit.SpikeArrestViolation"}}}%                                                                  ➜  quote-service-1.1.0 curl http://carlos2quotes.pcf.apigeese.net/quotes/APIC
{"fault":{"faultstring":"Failed to resolve API Key variable request.header.apikey","detail":{"errorcode":"steps.oauth.v2.FailedToResolveAPIKey"}}}%                                                       ➜  quote-service-1.1.0
```

Whoa! XML? And more... Instant API Management. You didn't even need to go into the Apigee Edge UI to set up the policies.


## Check Out the Proxy in Edge
Go back to your Edge org [*https://edge.apigee.com*](https://edge.apigee.com) and take a look at the new proxy. You'll see some pre-built policies that match up to the extensions we put into the OpenAPI spec, and they are attached where we specified. APIs... Is there anything they can't do? ;-)

## Summary

That completes this hands-on lesson. Remember, what we showed in this section should be considered "beta" and subject to change. We're rapidly working to get the functionality finalized. You can help! Tell us what you think is useful, what isn't, etc. We have a section on our community site devoted to our integration work: [*Apigee Community for Integrations*](https://community.apigee.com/spaces/134/index.html) Feel free to share thoughts there or reach out to Carlos<carlos@apigee.com> directly.

# THANK YOU!!
