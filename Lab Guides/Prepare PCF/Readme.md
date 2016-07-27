## Welcome
Welcome to the Apigee & Pivotal Cloud Foundry hands-on Workshop!

We have a couple of prerequisites to get out of the way to ensure that you can complete the logs successfully.

1. Laptop.
2. Apigee trial or paid account.
3. Cloud Foundry CLI
4. Account on our Pivotal Cloud Foundry platform.


### laptop
You're welcome to follow along on-screen but it's more fun to mash keys on your own.

### Apigee Trial account
Open a browser (for best experience, a current release) and navigate to [https://edge.apigee.com]. If you cannot log in, create an account. You'll need access to your email in order to activate your account if you haven't completed this step prior to the session. Let us know and we can zip through this.

### CF CLI
You need the CF CLI installed on your dev environment. Get that here: [https://github.com/cloudfoundry/cli]

### Pivotal Account
We have a shared environment. We'll get you access somehow. This needs to be done in a hurry, or it's going to be a massive time sink.

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
