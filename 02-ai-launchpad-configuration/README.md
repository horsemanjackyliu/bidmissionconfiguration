---
parser: v2
time: 20
author_name: Jacky Liu
---

# Configure SAP AI Launchpad: Connection, Resource Group, Model Deployment, and BTP Destination

<!-- description --> Connect SAP AI Launchpad to SAP AI Core using a service key, select a resource group, deploy GPT-4o and a text embedding model from the Model Library, and create a BTP Destination to enable application access to AI Core.

## You will learn

- How to add an SAP AI Core connection in SAP AI Launchpad using service key credentials
- How to select a resource group to scope your AI operations
- How to deploy GPT-4o and a text embedding model via the Generative AI Hub Model Library
- How to create a BTP Destination for SAP AI Core so applications can call the API

## Prerequisites

- SAP AI Launchpad subscribed and accessible (see [00-BTP-Initial-Setup](../00-BTP-Initial-Setup/README.md))
- SAP AI Core service instance created with the `extended` plan
- SAP AI Core service key available (JSON credentials from your Cloud Foundry service key binding)

---

## Part 1 — Add a Connection to SAP AI Core

The **Workspaces** app is the entry point for managing connections between SAP AI Launchpad and your SAP AI Core instances.

### Open the Workspaces app

1. Open your **SAP AI Launchpad** subscription URL from the BTP cockpit.

   ![SAP AI Launchpad home screen](img/image.png)

2. In the left navigation bar, click **Workspaces**.

3. Click **Add** to create a new connection to your SAP AI Core instance.

   ![Workspaces – Add button](img/image-1.png)

   ![Workspaces – Add connection](img/ail-add.png)

### Fill in the connection details from your service key

4. Enter `my-ai-core` as the **Connection Name**.

5. Open your AI Core service key JSON and map the fields as shown below:

   | AI Launchpad field     | Service key field             |
   | ---------------------- | ----------------------------- |
   | **AI API URL**         | `serviceurls.AI_API_URL`      |
   | **Authentication URL** | `url` (append `/oauth/token`) |
   | **Client ID**          | `clientid`                    |
   | **Client Secret**      | `clientsecret`                |

   ![Connection form filled from service key](img/ail-key.png)

   <!-- ![Service key – overview](img/image-2.png) -->

   ![Service key – credentials detail](img/image-3.png)

   ![Service key – full JSON](img/image-4.png)

6. Click **Create** to save the connection.

> **Tip:** If a connection already exists in your workspace, you can add another one under a different name. SAP AI Launchpad supports multiple concurrent connections to different AI Core instances.

---

## Part 2 — Select a Resource Group

Resource groups isolate AI assets (configurations, deployments, artifacts) within a single AI Core instance. Selecting one in the header scopes all subsequent operations to that group.

### Connect to your AI Core instance

7. In the **Workspaces** app, click on `my-ai-core`. The connection name appears in the top header and additional apps — including **Generative AI Hub** — become available in the left navigation.

   ![Connected to AI Core](img/ail-connection.png)

   <!-- ![AI Launchpad after connecting](img/image-5.png) -->

### Select the resource group

8. In the **Resource Groups** pane on the left, select `default`.

   The selected resource group is reflected in the header. All configurations, deployments, and model access are now scoped to this group.

   ![Resource group selected](img/ail-connection2.png)

> **Note:** The `default` resource group is created automatically during SAP AI Core provisioning. For a dedicated resource group for this procurement use case, create one via **SAP AI Core Administration** and select it here.

---

## Part 3 — Deploy GPT-5

SAP AI Core's **Generative AI Hub** provides a Model Library where you can deploy foundation models with a single click — no configuration file required.

### Deploy from the Model Library

9. In the left navigation, go to **Generative AI Hub** → **Model Library**.

   ![Model Library – search for gpt-4o](img/image-7.png)

10. Locate **GPT-4o** and click **Deploy**.

    ![GPT-4o – Deploy button](img/image-8.png)

### Verify the deployment is running

11. Go to **ML Operations** → **Deployments** to monitor the deployment status.

    Wait until the **Status** column shows **Running** before proceeding.

    ![Deployments list – gpt-4o running](img/image-9.png)

---

## Part 4 — Deploy Text Embedding Model

Repeat the same process to deploy a text embedding model, which is required for vector search and semantic retrieval in the procurement evaluation use case.

### Deploy from the Model Library

12. In the left navigation, go to **Generative AI Hub** → **Model Library**.

    ![Model Library – search for text embedding](img/image-10.png)

13. Locate the **text embedding** model and click **Deploy**.

    ![Text embedding model – Deploy button](img/image-11.png)

### Verify the deployment is running

14. Go to **ML Operations** → **Deployments** and confirm the text embedding deployment **Status** is **Running**.

    ![Deployments list – text embedding running](img/image-12.png)

> **Note:** Both the GPT-4o and text embedding deployments must have **Running** status before they can be consumed by applications.

---

## Part 5 — Create a BTP Destination for SAP AI Core

Applications running in your BTP subaccount access SAP AI Core through the **Destination** service. This step creates a destination named `bid-aicore` using the OAuth2ClientCredentials authentication method, so your app can obtain tokens and call the AI Core API automatically.

### Retrieve the AI Core service key

15. In the BTP cockpit, navigate to your subaccount → **Services** → **Instances and Subscriptions**.

16. Locate your SAP AI Core service instance, click the three dots, and choose **View Credentials** to open the service key.

    ![Service key – credentials view](img/image-3.png)

    ![Service key – full JSON for destination](img/image-4.png)

    Note the following values — you will need them in the next step:

    | Value to copy     | Service key field        |
    | ----------------- | ------------------------ |
    | AI Core API URL   | `serviceurls.AI_API_URL` |
    | Client ID         | `clientid`               |
    | Client Secret     | `clientsecret`           |
    | Token Service URL | `url` + `/oauth/token`   |

### Create the destination

17. In the left navigation of your BTP subaccount, go to **Connectivity** → **Destinations**.

18. Click **New Destination**.

    ![BTP Cockpit – New Destination](img/image-6.png)

19. Fill in the destination properties using the values from your service key:

    | Field                 | Value                                |
    | --------------------- | ------------------------------------ |
    | **Name**              | `bid-aicore`                         |
    | **Type**              | `HTTP`                               |
    | **Description**       | `bid-aicore`                         |
    | **URL**               | `<AI_API_URL from service key>`      |
    | **Proxy Type**        | `Internet`                           |
    | **Authentication**    | `OAuth2ClientCredentials`            |
    | **Client ID**         | `<clientid from service key>`        |
    | **Client Secret**     | `<clientsecret from service key>`    |
    | **Token Service URL** | `<url from service key>/oauth/token` |

    ![Destination configuration filled in](img/image-13.png)

20. Click **Save**, then click **Check Connection**. A `200 OK` response confirms that the destination is correctly configured and can reach the AI Core API.

---

## Summary

You have completed the full SAP AI Launchpad configuration for this use case:

| Step                      | What was configured                                                                       |
| ------------------------- | ----------------------------------------------------------------------------------------- |
| Connection                | SAP AI Launchpad connected to SAP AI Core via service key (`my-ai-core`)                  |
| Resource Group            | `default` resource group selected to scope all operations                                 |
| GPT-4o deployment         | Deployed and running in the Generative AI Hub — ready for chat and reasoning tasks        |
| Text embedding deployment | Deployed and running — ready for vector search and semantic retrieval                     |
| BTP Destination           | `bid-aicore` destination created — applications can now call the AI Core API using OAuth2 |
