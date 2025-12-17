I'll walk you through testing your TrackerAPI endpoints with step-by-step Postman instructions, starting from scratch.

## Step 1: Download and Install Postman

1. If you don't have Postman yet, download it from [postman.com/downloads](https://www.postman.com/downloads/)
2. Install and launch the application

## Step 2: Create a New Collection

1. After opening Postman, locate the **Collections** tab on the left sidebar
2. Click the "+" icon next to "Collections" (or the "Create Collection" button)
3. A popup will appear - type "TrackerAPI" as the collection name
4. Click the **Create** button to save your new collection

## Step 3: Set Up an Environment (for storing your API key)

1. Click on the **Environments** tab in the left sidebar (looks like a gear icon)
2. Click the "+" icon or "Create Environment" button
3. Enter "TrackerAPI Production" as the environment name
4. In the variable table, add the following variables:
   - For the first row, under **Variable**, type `base_url`
   - In the **Initial Value** column, type `https://trackerapi.smartwire.com`
   - In the **Current Value** column, type the same value
5. Click the **Save** button in the top-right corner

## Step 4: Test Endpoint 1 (Without Authentication)

Now let's create your first request for the endpoint that doesn't require authentication:

1. Right-click on your "TrackerAPI" collection in the left sidebar
2. Select **Add request** from the dropdown menu
3. In the request name field that appears, type "Verify Flash Connection"
4. Click the **Save to TrackerAPI** button

Now, let's configure this request:

1. At the top of the request tab, make sure **GET** is selected in the dropdown (it's usually the default)
2. In the URL field, type `{{base_url}}/api/DeliveryTracker/verify-flash-connection`
3. Make sure the "TrackerAPI Production" environment is selected in the environment dropdown (top-right corner)
4. Click the blue **Send** button

You should receive a response in the lower panel with status 200 OK and JSON data showing the connection status.

## Step 5: Create an API Key for Authentication

Before testing the protected endpoints, you need an API key. If you don't already have one:

1. On your development machine or server, navigate to your project folder
2. Run the API Key Tool:
   ```powershell
   cd C:\path\to\TrackerAPI
   .\tools\ApiKeyTool.exe generate PostmanTesting Production
   ```
3. Copy the generated API key (starts with "sk_prod_")

## Step 6: Add the API Key to Your Environment

1. Go back to Postman
2. Click on the **Environments** tab in the left sidebar
3. Click on your "TrackerAPI Production" environment
4. Add a new variable row:
   - Under **Variable**, type `api_key`
   - In the **Initial Value** column, paste your API key
   - In the **Current Value** column, paste the same API key
5. Click the **Save** button in the top-right corner

## Step 7: Test Endpoint 2 (With Authentication)

Now let's create a request for an endpoint that requires authentication:

1. Right-click on your "TrackerAPI" collection in the left sidebar
2. Select **Add request**
3. Name it "Verify DeliveryTracker Connection"
4. Click the **Save to TrackerAPI** button

Configure this request:

1. Make sure **GET** is selected in the method dropdown
2. In the URL field, type `{{base_url}}/api/DeliveryTracker/verify-deliverytracker-connection`
3. Click on the **Headers** tab below the URL field
4. Add a header:
   - In the **Key** column, type `X-API-Key`
   - In the **Value** column, type `{{api_key}}`
   - Make sure the checkbox to the left is checked
5. Click the blue **Send** button

You should receive a response showing the connection status to the DeliveryTracker database.

## Step 8: Test Endpoint 3 (With Authentication)

Let's create one more request:

1. Right-click on your "TrackerAPI" collection again
2. Select **Add request**
3. Name it "Get All Drivers"
4. Click the **Save to TrackerAPI** button

Configure this request:

1. Make sure **GET** is selected
2. In the URL field, type `{{base_url}}/api/DeliveryTracker/drivers/get/all`
3. Click on the **Headers** tab
4. Add the same header as before:
   - Key: `X-API-Key`
   - Value: `{{api_key}}`
5. Click the blue **Send** button

You should see a list of drivers/trucks returned in the response.

## Step 9: Set Collection-Level Authentication (Optional)

To avoid adding the API key header to every request:

1. Click on the "TrackerAPI" collection in the left sidebar (not a specific request)
2. In the main panel, click on the **Authorization** tab
3. In the **Type** dropdown, select "API Key"
4. Configure these fields:
   - Key: `X-API-Key`
   - Value: `{{api_key}}`
   - Add to: "Header"
5. Click the **Save** button

Now any new requests you add to this collection will automatically use this API key.

## Step 10: Handle SSL Certificate Issues (If Needed)

If you see SSL/TLS errors:

1. Click on the gear icon (⚙️) in the top-right corner to open Settings
2. Find and turn OFF "SSL certificate verification"
3. Click "Save"

## Testing More Endpoints

To test additional endpoints:

1. Right-click on your collection → Add request
2. Name it appropriately
3. Set the correct HTTP method (GET, POST, etc.)
4. Enter the URL using the `{{base_url}}` variable
5. Add any required request body or parameters
6. Send the request

## Troubleshooting

- **401 Unauthorized**: Double-check your API key is correctly entered and not expired
- **Connection failure**: Make sure your server is reachable and running
- **Invalid URL**: Verify the endpoint path is exactly as defined in your API