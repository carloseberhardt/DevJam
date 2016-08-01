## Welcome
Welcome to the Apigee & Pivotal Cloud Foundry hands-on Workshop!

We have a couple of prerequisites to get out of the way to ensure that you can complete the labs successfully.

1. Laptop.
2. Apigee trial or paid account.
3. Cloud Foundry CLI
4. Account on our Pivotal Cloud Foundry platform.
5. An API to deploy


### Laptop
Of course you're welcome pair on these exercises but it might be more fun to mash keys on your own.

### Apigee Trial account
Open a browser (for best experience, a current release) and navigate to [https://edge.apigee.com(https://edge.apigee.com)]. If you cannot log in, create an account. You'll need access to your email in order to activate your account if you haven't completed this step prior to the session. Let us know and we can zip through this.

### CF CLI
You need the CF CLI installed on your dev environment. Get that here: [https://github.com/cloudfoundry/cli(https://github.com/cloudfoundry/cli)]

### Dev tools
We have a sample Spring Boot API project that we will deploy during the session. If you want to use this you'll need JDK, maven, etc. If you want to push your own API to CF that should be fine, too. Just make sure you've got all the tools you need.

### Pivotal Account
We have a shared PCF environment. We're not creating individual accounts out there for you, so no shenanigans, please. We'll use a shared login and org but please create your own spaces.

1. Target the API

  ```Shell
  ➜  ~
  ➜  ~ cf api https://api.pcf.apigeese.net --skip-ssl-validation
  Setting api endpoint to https://api.pcf.apigeese.net...
  OK


  API endpoint:   https://api.pcf.apigeese.net (API version: 2.54.0)
  Not logged in. Use 'cf login' to log in.
  ➜  ~
  ```

2. Log in with email springone and password ***REMOVED*** (all lower case).

  ```Shell
  ➜  ~ cf login
  API endpoint: https://api.pcf.apigeese.net

  Email> springone

  Password>
  Authenticating...
  OK

  Targeted org springone



  API endpoint:   https://api.pcf.apigeese.net (API version: 2.54.0)
  User:           springone
  Org:            springone
  Space:          No space targeted, use 'cf target -s SPACE'
  ➜  ~
  ```

3. Create a space for yourself. Since we're all sharing one account and org please give it a unique name. Use your name, your nick, your CB handle, etc. (Do not use your SSN or other sensitive data. C'mon people. And don't use "carloseberhardt" that one's already there.)

  ```Shell
  ➜  ~ cf create-space carloseberhardt
  Creating space carloseberhardt in org springone as springone...
  OK
  Assigning role RoleSpaceManager to user springone in org springone / space carloseberhardt as springone...
  OK
  Assigning role RoleSpaceDeveloper to user springone in org springone / space carloseberhardt as springone...
  OK

  TIP: Use 'cf target -o "springone" -s "carloseberhardt"' to target new space
  ➜  ~
  ```

4. Target the space. (Substitute your space name for 'carloseberhardt' in the command below, naturally.)

  ```Shell
  ➜  ~ cf target -s carloseberhardt

  API endpoint:   https://api.pcf.apigeese.net (API version: 2.54.0)
  User:           springone
  Org:            springone
  Space:          carloseberhardt
  ➜  ~
  ```

### An API to Deploy

1. Grab the first release of the example API, available here: [https://github.com/carloseberhardt/quote-service/releases/tag/v1.0.0](https://github.com/carloseberhardt/quote-service/releases/tag/v1.0.0)

  ```Shell
  ➜  ~ mkdir working
  ➜  ~ cd working
  ➜  working curl -O -L https://github.com/carloseberhardt/quote-service/archive/v1.0.0.tar.gz
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  100   137    0   137    0     0   3611      0 --:--:-- --:--:-- --:--:--  3702
    0     0    0  6219    0     0  78883      0 --:--:-- --:--:-- --:--:-- 78883
  ➜  working ls
  v1.0.0.tar.gz
  ➜  working tar xzf v1.0.0.tar.gz
  ➜  working l
  total 20K
  drwxrwxr-x  3 ubuntu ubuntu 4.0K Jul 27 20:55 .
  drwxr-xr-x 10 ubuntu ubuntu 4.0K Jul 27 20:55 ..
  drwxrwxr-x  4 ubuntu ubuntu 4.0K Dec 29  2015 quote-service-1.0.0
  -rw-rw-r--  1 ubuntu ubuntu 6.1K Jul 27 20:55 v1.0.0.tar.gz
  ➜  working
  ```

2. Build and verify the API locally. This will may take some time, depending upon how much of the internet you need to download.

  * Build it...

  ```Shell
  ➜  working
  ➜  working cd quote-service-1.0.0
  ➜  quote-service-1.0.0 mvn clean install
  [INFO] Scanning for projects...
  [INFO]
  [INFO] ------------------------------------------------------------------------
  [INFO] Building quote-service 1.0.0
  [INFO] ------------------------------------------------------------------------
  [INFO]
  [INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ quote-service ---
  [INFO]

  <doge>Such building. So maven. Wow.</doge>

  [INFO] ------------------------------------------------------------------------
  [INFO] BUILD SUCCESS
  [INFO] ------------------------------------------------------------------------
  [INFO] Total time: 22.803s
  [INFO] Finished at: Wed Jul 27 20:57:14 UTC 2016
  [INFO] Final Memory: 27M/65M
  [INFO] ------------------------------------------------------------------------
  ➜  quote-service-1.0.0
  ```

  * Run it...

  ```Shell
  ➜  quote-service-1.0.0 mvn spring-boot:run
  [INFO] Scanning for projects...
  [INFO]
  [INFO] ------------------------------------------------------------------------
  [INFO] Building quote-service 1.0.0
  [INFO] ------------------------------------------------------------------------
  [INFO]
  [INFO] >>> spring-boot-maven-plugin:1.2.3.RELEASE:run (default-cli) @ quote-service >>>
  [INFO]

  <snipped output>

  2016-07-27 21:14:26.140  INFO 16982 --- [lication.main()] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
  2016-07-27 21:14:26.147  INFO 16982 --- [lication.main()] o.s.nanotrader.quote.Application         : Started Application in 7.614 seconds (JVM running for 16.288)
  ```

  * And in another console, test it...

  ```Shell
  ➜  ~ curl http://localhost:8080/quotes/GOOG
  {"created":"2016-07-27T21:25:16Z","DaysLow":737.0,"Open":738.42,"PreviousClose":738.42,"Volume":799448,"Price":742.14,"DaysHigh":743.93,"Name":"Alphabet Inc.","Symbol":"GOOG","Change":3.72,"PercentageChange":"+0.50%","Ask":742.16,"Bid":741.85}%                                                                                                                                                                ➜  ~
  ```

3. Assuming everything is working as expected, we can now deploy this api to PCF. Since we're all playing in the same space, please give it a unique name and/or route when you push the app. In the next few minutes, we'll be binding the route for this API to the Apigee Edge route service. That means we'll want to keep our routes unique and avoid conflicts.

  ```Shell
  ➜  quote-service-1.0.0 cf target

  API endpoint:   https://api.pcf.apigeese.net (API version: 2.54.0)
  User:           springone
  Org:            springone
  Space:          carloseberhardt
  ➜  quote-service-1.0.0 cf push carlos1quotes
  Using manifest file /home/ubuntu/working/quote-service-1.0.0/manifest.yml

  Creating app carlos1quotes in org springone / space carloseberhardt as springone...
  OK

  Creating route carlos1quotes.pcf.apigeese.net...
  OK

  Binding carlos1quotes.pcf.apigeese.net to carlos1quotes...
  OK

  Uploading carlos1quotes...
  Uploading app files from: /tmp/unzipped-app223024643
  Uploading 965.3K, 110 files
  Done uploading

  <snipped output>

  Showing health and status for app carlos1quotes in org springone / space carloseberhardt as springone...
  OK

  requested state: started
  instances: 1/1
  usage: 512M x 1 instances
  urls: carlos1quotes.pcf.apigeese.net
  last uploaded: Wed Jul 27 21:31:45 UTC 2016
  stack: unknown
  buildpack: java-buildpack=v3.6-offline-https://github.com/cloudfoundry/java-buildpack.git#5194155 java-main open-jdk-like-jre=1.8.0_71 open-jdk-like-memory-calculator=2.0.1_RELEASE spring-auto-reconfiguration=1.10.0_RELEASE

       state     since                    cpu    memory           disk           details
  #0   running   2016-07-27 09:32:37 PM   0.0%   315.2M of 512M   145.6M of 1G
  ➜  quote-service-1.0.0
  ```

Tada! App is running... we can test via curl or browser...

```Shell
➜  quote-service-1.0.0 curl -i http://carlos1quotes.pcf.apigeese.net/quotes/APIC
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Date: Wed, 27 Jul 2016 21:34:24 GMT
Server: Apache-Coyote/1.1
X-Vcap-Request-Id: bbcfef4c-ff6c-46ca-4203-91569696c725
Content-Length: 240
Connection: keep-alive

{"created":"2016-07-27T21:34:24Z","DaysLow":12.33,"Open":12.45,"PreviousClose":12.45,"Volume":113843,"Price":12.38,"DaysHigh":12.6,"Name":"Apigee Corporation","Symbol":"APIC","Change":-0.07,"PercentageChange":"-0.56%","Ask":12.95,"Bid":0.0}%                                                                                                                                                                   ➜  quote-service-1.0.0
```

## Recap
So what we did in this lab was all familiar Pivotal Cloud Foundry good times. We got our accounts set up and pushed an API. Nothing out of the ordinary for your everyday Cloud Native native.

Next up, let's manage the API.
