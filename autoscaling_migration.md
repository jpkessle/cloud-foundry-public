---

copyright:
  years: 2019
lastupdated: "2019-04-18"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}

# Migration from legacy Auto-Scaling service
{: #autoscale_migration}

This new App Autoscaler offering is a replacement of the legacy [Auto-Scaling service](legacy-autoscaling-catalog). By migrating to the new autoscaler, you will have the following benefits:

* Simplified experience: No service creation and binding are needed to use the auto-scaling feature. You can directly configure the policy through the UI or CLI.

* No need to re-stage your application: You do not need to re-stage your CF applications to make the auto-scaling take effect.

* Runtime independent: The app-autoscaler works for all language runtimes with the same user experience and same set of supported metrics.

* No code injection: The new autoscaler does not require an autoscaler agent to be included in your code if you are using IBM runtimes and buildpacks. You simply set the policy and after a while,  the autoscaler will start collecting metrics and scale your app based on the metrics you defined in the policy.

* Matching pace with the CF community: Future features of the app-autoscaler open-source project, will be available without delay in IBM Cloud.

We provide similar user experience between the legacy offering and the new one, including the policy definition, metric statistics and scaling history retrieving. 

Running with both offering simultaneously may lead to unexpected scaling behavior, so we recommend you to migrate from the legacy Auto-Scaling service to the new App Autoscaler.  You can achieve this from the {{site.data.keyword.Bluemix}} user interface with below steps: 

## Migrate through web UI

1. (Optional) Convert the autoscaling policy

  The policy definition in new autoscaler is different than the one in legacy Auto-Scaling service. If you want to reuse the existing policy, you will need to convert it to the new format. A simple policy migration utility is also provided to help convert the policy.

  - From the [{{site.data.keyword.cloud_notm}} console ![External link icon](../icons/launch-glyph.svg "External link icon")](https://{DomainName}){: new_window}, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg), and select **Resource List**.
  - On the **Resource List** page, click **Cloud Foundry Apps**.
  - Click the application to view its **Overview** page.
  - Select **Connections** in the left navigation pane, and hover the legacy **Auto-Scaling** service instance entry
  - Click on **Auto-Scaling** service instance link to view its service dashboard,  then select **Policy** tab.
  - Click the button **Policy Migration**, copy the new `App Autoscaler` policy on the right side to your clipboard and save it as a local file. 
  
  **Note**: The new Autoscaler doesn't support **HeapUsage** metric particularly for Liberty and Node.js runtime. As a result, the _Heap Usage_ related rules will be discarded during the conversion.  You need to use other metrics, such as "cpu", "memoryused", "memoryutil", "response" or "throughput" in the new  `App Autocaler`.

  Refer to [legacy Auto-Scaling policy JSON specification][legacy_autoscaling_policy] and [App Autoscaler policy JSON specification][autoscaling_policy] to get the detailed policy definitions. 

2. Unbind legacy **Auto-scaling** service

   - Back to the **Connections** view, and hover on the legacy **Auto-Scaling** service instance entry again.
   - Click on the **menu** button in the right of the legacy **Auto-Scaling** service instance entry.
   - From the drop-down list, select **Unbind service** or **Delete service** if applicationable.

3. Create a scaling policy in the new App Autoscaler offering.

   - Select the **Autoscaling** menu from the left navigation pane to access the new App-Autoscaler dashboard.
   - If you want to use the exported `App-Autoscaler` policy that was converted from the legacy Auto-Scaling service in previous steps, select **Import From JSON** to create a autoscaling policy. Or use **Create Auto-Scaling policy** to create a new one from scratch. Refer to the [get started][autoscaler-get-started] guide for details.

## Migrate through command line interface

Alternatively, you can use command lines to migrate over.

1. Create a App-Autoscaler policy

   - If you already exported the converted new policy as described in above steps, just save it to a local file. 
   - If you would like to convert the policy by yourself, take the below steps:
      - Retrieve the existing policy using [Auto-scaling service CLI plugin][legacy-autoscaling-cli]: 
        ```
        ibmcloud as policy-show <APP_NAME> [--json]
        ```
        {:codeblock} 
        ```
      - Covert the policy manually to new format. 
    
    Refer to [legacy Auto-Scaling policy JSON specification][legacy_autoscaling_policy] and [App Autoscaler policy JSON specification][autoscaling_policy] for the detailed policy format.

2. Unbind the legacy service instance from your application with Cloud Foundry Command Line tool:

   ```
   cf unbind-service <YOUR_APP> <YOUR_SERVICE_INSTANCE>
   ```
   {:codeblock} 

3. Attach policy to enable new autoscaling experience.

   Now you can attach the new policy to your application through the new [AutoScaler CLI]:   

   ```
   cf aasp <YOUR_APP> <YOUR_POLICY_FILE>
   ```
   {:codeblock} 


[autoscaler_project]: https://github.com/cloudfoundry/app-autoscaler
[autoscaler_user_guide]: https://github.com/cloudfoundry/app-autoscaler/blob/master/docs/Readme.md
[autoscaling_policy]:https://github.com/cloudfoundry/app-autoscaler/blob/master/docs/policy.md
[autoscaler_cli]: https://github.com/cloudfoundry/app-autoscaler-cli-plugin#cloud-foundry-cli-autoscaler-plug-in-
[metric_type]:https://github.com/cloudfoundry/app-autoscaler/blob/master/docs/Readme.md#metric-types
[deploy_app]: https://{DomainName}/docs/cloud-foundry/deploy-apps.html#dep_apps
[legacy-autoscaling-catalog]: https://{DomainName}/catalog/services/auto-scaling
[legacy-autoscaling-cli]: https://{DomainName}/docs/cli?topic=auto-scaling-cli-autoscalingcli#bx_as_policy_show
[legacy_autoscaling_policy]: https://{DomainName}/docs/services/Auto-Scaling?topic=Auto-Scaling%20-policy_fields#policy-definition-through-rest-api-and-cli-
[autoscaler-get-started]: https://{DomainName}/docs/cloud-foundry-public?topic=cloud-foundry-public-autoscale_cloud_foundry_apps#autoscale_cloud_foundry_apps
