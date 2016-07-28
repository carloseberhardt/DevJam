![](./media/image46.png)

*Publish APIs*

![](./media/image48.png)

## NOTICE for SpringOne DevJam --- this document has not been modified for the SpringOne session and it refers to proxies, etc that we don't have. Substitute your route services proxy as appropriate. Please don't hesitate to ask for clarification on anything!


**Objectives**

Publishing APIs can be broadly defined by the following tasks:

1.  Create the API products on Edge that bundle your APIs.
2.  Register app developers on Edge.
3.  Register apps on Edge.

**Prerequisites**

-   At a minimum, Lab 1 is completed

**Estimated Time: 30 mins**

> ![](./media/image03.png)

**Adding API Key Verification:**

A *developer* builds an *app* that makes requests to your *APIs* to
access your backend services. The following image shows the relationship
of a developer to an app and to an API:

![](./media/image47.png)

1)  **Adding an API Key Verification Policy**<br/>
&nbsp;&nbsp;a.  Go to the Apigee Edge Management UI browser tab<br/>
&nbsp;&nbsp;b.  Select your API proxy<br/>
&nbsp;&nbsp;c.  Click on the develop tab

> ![](./media/image50.png)

&nbsp;&nbsp;d.  Click on Proxy Endpoints -> PreFlow

> ![](./media/image49.png)

&nbsp;&nbsp;e.  Click on “+ Step” on the Request Flow

> ![](./media/image52.png)

&nbsp;&nbsp;f.  Select the ‘Verify API Key’ policy with the following properties:

&nbsp;&nbsp;&nbsp;&nbsp;i.  Policy Display Name: **Verify API Key**<br/>
&nbsp;&nbsp;&nbsp;&nbsp;ii. Policy Name: **Verify-API-Key**

> ![](./media/image51.png)

**NOTE:** If you have not completed Lab 2, please skip this step.

The ‘Verify API Key’ policy will get added after the ‘Response Cache’
policy. **Drag and move** the ‘Verify API Key’ policy to be before the
‘Response Cache’ policy

![](./media/image55.png)

**Note**: It depends on your use case, but typically API Key
verification should be one of the first policies in the flow. In this
scenario, we verify the API Key before the Response Cache policy to
ensure that an API Consumer whose API Key may have been revoked is not
able to get the data from the cache.

&nbsp;&nbsp;g.  Examine the XML configuration in the ‘Code’ panel (or properties using the ‘Property Inspector’ panel) associated with the ‘Verify API Key’ policy. The XML for the policy should look something like this:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <VerifyAPIKey async="false" continueOnError="false" enabled="true" name="Verify-API-Key">
  <DisplayName>Verify API Key</DisplayName>
  <FaultRules/>
  <Properties/>
  <APIKey ref="request.queryparam.apikey"/>
  </VerifyAPIKey>
  ```

*(You can find the policy xml*
[**here**](https://gist.github.com/prithpal/02ee175cc1e00a2de610)*.
Click the “Raw” button and copy/paste into your policy editor).*

Note the &lt;APIKey&gt; element, which identifies where the policy
should check for the API key. In this example, the policy looks for
the API key in a query parameter named 'apikey'. API keys can be
located in a query parameter, a form parameter, or an HTTP header.
Apigee Edge provides a message variable for each type of location. For
policy reference information, see [Verify API Key
policy](http://apigee.com/docs/api-services/reference/verify-api-key-policy)[.](http://apigee.com/docs/api-services/reference/verify-api-key-policy)[](http://apigee.com/docs/api-services/reference/verify-api-key-policy)

2)  **Removing the API Key from the query parameters**

&nbsp;&nbsp;a.  Click on “+ Step” on the Request Flow

> ![](./media/image53.png)

&nbsp;&nbsp;b.  Select ‘Assign Message’ policy with the following properties:

&nbsp;&nbsp;&nbsp;&nbsp;i.  Policy Display Name: **Remove APIKey QP**<br/>
&nbsp;&nbsp;&nbsp;&nbsp;ii. Policy Name: **Remove-APIKey-QP**

> ![](./media/image58.png)

&nbsp;&nbsp;c.  The ‘Remove APIKey QP’ policy will get added after the ‘Response Cache’ policy. **Drag and move** the ‘Remove APIKey QP’ policy to be before the ‘Response Cache’ policy

![](./media/image56.png)

&nbsp;&nbsp;d.  **Save** the configuration

&nbsp;&nbsp;e.  For the ‘Remove APIKey QP’ policy, change the XML configuration of the policy using the 'Code' panel as follows:

  ```
  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <AssignMessage async="false" continueOnError="false" enabled="true" name="Remove-APIKey-QP">
  <DisplayName>Remove APIKey QP</DisplayName>
  <Remove>
  <QueryParams>
  <QueryParam name="apikey"></QueryParam>
  </QueryParams>
  </Remove>
  <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
  <AssignTo createNew="false" transport="http" type="request"/>
  </AssignMessage>
  ```

(You can find the policy xml
[*here*](https://gist.github.com/prithpal/cbf66ee17b9afe75fdf2). Click
the “Raw” button and copy/paste into your policy editor).

As a security measure, the ‘Remove APIKey QP’ policy simply removes
the ‘apikey’ query parameter from the HTTP request message attached to
the flow so it is not sent to the backend service. In this scenario we
are removing the ‘apikey’ immediately after verify API Key policy, but
depending on your use case, removing the ‘apikey’ may need to be done
at a later stage in the flow.

3)  **Testing the API Key Verification Policy**

Until now anyone with the URL to the ‘{your\_initials}\_hotel’ API
Proxy has been able to make a request with appropriate parameters and
get a response back. Now that you have added the API Key Verification
policy, that will no longer be the case.

&nbsp;&nbsp;a.  Start the Trace session for the ‘**{your\_initials}**\_hotels’ proxy

&nbsp;&nbsp;b.  Now that the API Key Verification policy has been added to the proxy, try and send a test ‘/GET hotels’ request from Postman with the following query parameters: **zipcode=98101&radius=200**

**Note**: Replace the URL of hotels API with **{your\_initials}**\_hotels

&nbsp;&nbsp;c.  You will notice that the following fault is returned since an API Key has not been provided as a request query parameter:

  ```
  {
  fault: {
  faultstring: "Failed to resolve API Key variable request.queryparam.apikey",
  detail: {
  errorcode: "steps.oauth.v2.FailedToResolveAPIKey"
  }
  }
  }
  ```

The above response shows that the API Key Verification policy is being enforced as expected.

&nbsp;&nbsp;d.  Review the Trace for the proxy and the returned response to ensure that the flow is working as expected.

&nbsp;&nbsp;e.  Stop the Trace session for the proxy

**API Products**

API products enable you to bundle and distribute your APIs to multiple developer groups simultaneously, without having to modify code. An API product consists of a list of API resources (URIs) combined with a Service Plan (rate-limiting policy settings) plus any custom metadata required by the API provider. API products provide the basis for access control in Apigee, since they provide control over the set of API resources that apps are allowed to consume.

![](./media/image57.png)

As part of the app provisioning workflow, developer selects from a list of API products. This selection of an API product is usually made in the context of a developer portal. The developer app is provisioned with a key and secret (generated by and stored on Apigee Edge) that enable the app to access the URIs bundled in the selected API product. To access API resources bundled in an API product, the app must present the API key issued by Apigee Edge. Apigee Edge will resolve the key that is presented against an API product, and then check associated API resources and quota settings.

The API supports multiple API products per app key - your developers can consume multiple API products without requiring multiple keys. Also, a key can be 'promoted' from one API product to another. This enables you to promote developers from 'free' to 'premium' API products seamlessly and without user interruption.

The following table defines some of the terms used to register apps
and generate keys:

***API product***   A bundle of API proxies combined with a service plan that sets limits on access to those APIs. API products are the central mechanism that Apigee Edge uses for authorization and access control to your APIs. For more, see [API Products](http://apigee.com/docs/developer-services/content/what-api-product)[](http://apigee.com/docs/developer-services/content/what-api-product)

***Developer***     The API consumer. Developers write apps the make requests to your APIs.

***App***            A client-side app that a developer registers to access an API product. Registering the app with the API product generates the API key for accessing the APIs in that product.

***API key***       A string with authorization information that a client-side app uses to access the resources exposed by the API product. The API key is generated when a registered app is associated with an API product.

**Publishing an API Product**

1)  From the Apigee Edge Management UI, go to Publish → Products

2)  Click on ‘+ Product’ button to add a new product

3)  In the ‘Product Details’ section of the new product screen, enter or select the following values for the various fields:

&nbsp;&nbsp;a.  Display Name: **{Your_Initials}_Hospitality Basic Product**<br/>
&nbsp;&nbsp;b.  Description: **API Bundle for a basic Hospitality App.**<br/>
&nbsp;&nbsp;c.  Environment: **Test**<br/>
&nbsp;&nbsp;d.  Access: **Public**<br/>
&nbsp;&nbsp;e.  Key Approval Type: **Automatic**

![](./media/image59.png)

4)  In the ‘Resources’ section select the following values for the
    various fields:

&nbsp;&nbsp;a.  API Proxy: **{your\_initial}\_hotels **<br/>
&nbsp;&nbsp;b.  Revision: **1**<br/>
&nbsp;&nbsp;c.  Resource Path: **/**

> ![](./media/image60.png)

5)  Click on **‘Import Resources’** to add the **‘/’** resource of your proxy to the API product

6)  Repeat the above two steps for the **‘/\*\*’** resource

7)  Click **‘Save’** to save the API Product**.** The new product should now be listed on the ‘Products’ page.

**Developers**

Developers access your APIs through apps. When the developer registers an app, they receive a single API key that allows them to access all of the API products associated with the app. However, developers must be registered before they can register an app.

8)  **Registering a Developer**

Developers access your APIs through apps. When the developer registers
an app, they receive a single API key that allows them to access all
of the API products associated with the app. However, developers must
be registered before they can register an app.

Developers typically have several ways of registering:

-   If you have a paid Edge account, through a Developer
    Services portal. See [Add and manage user
    accounts](http://apigee.com/docs/developer-services/content/add-and-manage-user-accounts)
    for more.

-   By accessing a form that uses the Edge management API to register
    the developer. See [Using the Edge management API to Publish
    APIs](http://apigee.com/docs/developers-services/content/using-edge-management-api-publish-apis)
    for more.

-   By a back-end administrator using the Edge management UI.

The following steps describe the process of registering Developers and Developer Apps
using the Apigee Edge Management UI.

    a. From the Apigee Edge Management UI, go to Publish → Developers

    b. Click on ‘+ Developer’ button to add a new product

    c. Add a new developer with the following properties:
        -   First Name: Your First Name
        -   Last Name: Your Last Name
        -   Email: {your email_id}        
        -   Username: {firstname_lastname}

  > ![](./media/image22.png)

    d.  Click ‘Save’ to save the Developer. The new developer should now
    be listed on the ‘Developer’ page.

    NOTE : If you have already created a Developer App as part of the lab-3,
    you can skip this step.

9)  **Registering a Developer App**

Now that you have an API product and a developer, you can register a
Developer App with the API product. Registering the Developer App
generates the API key for the API products associated with the app.
You can then distribute the key to app developers so they can access
the features in the API products from the app.

The following steps describe the process of registering Developer Apps using the Apigee Edge Management UI.

    a. From the Apigee Edge Management UI, go to Publish → Developer Apps

    b. Click on ‘+ Developer App’ button to add a new product

    c. In the ‘Developer App Details’ section, enter or select
    the following values for the various fields:

        -   Display Name: {Your_Initials}_iExplore App
        -   Developer: {Your_name}
        -   Callback URL: Leave it blank

    d. In the ‘Products’ section, click on the ‘+ Product’ button

    e. From the ‘Product’ drop-down, select the product you created.

    f. Click the ‘check-mark’ button in the ‘Actions’ column to accept
    the changes

> ![](./media/image23.png)

    g. Click ‘Save’ to save the Developer App. The new app should now be
    listed on the ‘Developer Apps’ page

    h. From the ‘Developer Apps’ page, select your App.

    i. In the ‘Products’ section, next to the entry for
    ‘{Your_Initials}_Hospitality Basic Product,’ click ‘Show’ in
    the ‘Consumer Key’ and ‘Consumer Secret’ columns to display the generated keys

**Note:** Since you selected ‘Key Approval Type: Automatic’ when you
created the API product, the API key is automatically approved and you
can view it immediately

If you had selected ‘Approval Type: Manual,’ you would need to click
‘Approve’ in the ‘Actions’ column to approve the API key.

10)  Test the API

&nbsp;&nbsp;a.  Start the Trace session for the ‘**{your\_initials}\_hotels**’ proxy

&nbsp;&nbsp;b.  Now that the API Key Verification policy has been added to the proxy, try and send a test ‘/GET hotels’ request from Postman with the following query parameters: **zipcode=98101&radius=200&apikey={apikey from the application}**

> Note: Replace the URL of hotels API with **{your\_initials}**\_hotel


**Summary**

In this exercise, you learnt about how API keys can be used as an
application identifier, how APIs can be packaged in the form of
Products.
