![](./media/image18.png)

*Appendix 3 - Monitoring your APIs’ Health*

![](./media/image20.png)

**Overview**

As a digital company, you understand that APIs are critical for your
business. You want your APIs to function as expected and you want to be
notified of any anomalies the moment they happen so that you can
expedite the resolution.

Apigee's **API Health** service is designed to help organizations detect
and prevent the potential consequences of bad API performance such as
poor application experience, user frustration, and lost revenue. API
Health helps you answer two very important questions by collecting a
standard set of vitals: Are my APIs alive? How are they behaving? With a
network of probes deployed across globally distributed data centers in
the cloud, API Health continually measures the success rate and
performance of APIs, and alerts DevOps teams to any failures or
slowdowns of their APIs.

**Objectives**

The objective of this lesson is to get you familiar with Apigee’s API
Health service.

**Prerequisites**
-   Lab 1 is completed

**Estimated Time: 30 mins**

In this lab we will monitor your hotels API proxy and generate alerts
whenever there is a latency anomaly or failures using assertions like
status code. Let’s get started -

1.  Login to API Health -
    [*https://health.apigee.com/*](https://health.apigee.com/) with
    your Apigee Edge credentials.

2.  Select the Organization.

3.  Click ’Create new probe’ to add a new monitoring probe to your API. (If you have not created any probes previously click on 'Create your first probe')

  > ![](./media/create_new_probe.png)

4. Hit the "add new" link to create a new test case, and configure it with a single step
    -   Test case Name : {your_intials} Test case
    -   Step Name: Get Hotel
    -   URI : http://{org}-{env}.apigee.net/v1/{your_initials}_hotels

    > Note : Replace org, environment & API proxy values before you Save.

    -   Http Method : Get
    -   Response &gt; Assert &gt; HTTP Code - 2XX

  > ![](./media/create_test_case.png)

5.  Click on “Save”.

6.  Give your probe a name

    -   Probe Name : {your\_initials}\_probe

7.  Define Alerts by configuring the following values :

  -   Run Every : 30 seconds
  -   Raise Alert : enable “If Failure exceeds” and set the value as 5
    consecutive times.
  -   Notification Destination : Choose Email and provide **your email
    address**.

  > ![](./media/configure_new_probe.png)

7.  Click on “Next”.

8.  Select Run locations : Check all the five locations.

9.  Click on “Save”.

10.  This will create a probe in API Health. You can view all your Probes
    from the API Health dashboard.

  >![](./media/probe_dashboard.png)

11.  As the Hotels API proxy is protected, all calls will fail with 4xx
    errors and you will receive alerts (like the one below) on the
    configured email address.

  > ![](./media/image26.png)

12.  You can deactivate the probe, from API Health dashboard.

  > ![](./media/image25.jpg)

13.  Viewing graphical reports on API Health

    -   By clicking a step name on the API Health dashboard, you're
        shown graphs of average response times and success rates for
        that step. At the top of the Reports window, you can also
        select other steps in the probe to view composite graphs of
        all steps.

  > ![](./media/image29.png)

  > ![](./media/image27.png)

  -   By clicking a probe name on the API Health dashboard, you're shown
    composite graphs of average response times and success rates for
    all steps in the probe. You can also deselect steps to compare
    only the ones you want, and you can filter the view by time period
    and location the calls were made from.

  > ![](./media/image28.png)

  > ![](./media/image30.png)

**Summary**

That completes this hands-on lesson. In this lesson you learned about
the API monitoring service provided by Apigee. As an assignment
configure multiple probes and monitor your APIs.

**Bonus Exercise**

Use the UI to alter the configuration of your test case to supply the necessary headers so that the probe calls are successful.
