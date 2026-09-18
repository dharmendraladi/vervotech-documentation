## **Webhook Integration Guide**
Webhooks are HTTP callbacks triggered by specific events in our system. They provide real-time updates for mappings, enabling seamless communication and data sharing between systems.

#### **Key Features:**

- **Real-Time Notifications**: Immediate data transfer upon event occurrence.
- **Secure Delivery**: Includes authentication for added security.
- **Retry Mechanism**: Ensures reliable delivery with retries for failed attempts.

#### **Webhook Lifecycle:**

- **Event Trigger**: An action occurs in the system (e.g., hotel mapping creation, update, or deletion).
- **Payload Generation**: A JSON payload containing relevant details is created.
- **Delivery**: The payload is sent to the registered endpoint configured for webhook clients.
- **Acknowledgment**: The endpoint responds with a 200 OK status to confirm receipt.

#### **Configuration Steps:**

- **Step 1**: Create an API to receive updates triggered by mapping updates.
- **Step 2**: To enable a webhook in your account, contact support@vervotech.com and share your API details as mentioned in Step 3.
- **Step 3**: Configure a Webhook:
    - **Fields:**
            - **Target URL:** The URL to which payloads will be sent.
            - **Authentication Headers:** Required headers in key-value pair format.
#### **Payload Details**:

    - #### Example Payload:
        - Sample Request Headers: It could be any authentication headers.

            - **ApiKey**: "Your-ApiKey"
            - **AccountId**: "Your-AccountId"
   - Request:

        - **JSON Payload**
        

    ``` {
         "ProviderMapping": {
           "VervotechId": 12345,
           "ProviderHotelId": "PROV123",
           "ProviderName": "Provider A",
           "ChannelIds": ["CH1", "CH2"],
           "ModifiedOn": "2024-06-18T10:30:00Z",
           "ProviderLocationCode": "LOC123",
           "Type": "New"
          }
        }
        ```
- #### Retry Policy:

    - **Max Retries**: 3
    - **Condition**: Retry on failed API calls.

Sample API Code Snippet:

- **Method**: POST

- **URL**: /PushMapping

Code Snippet (C#):


```
        [HttpPost("PushMapping")]
        public async Task<IActionResult> PushMapping([FromBody] ProviderMapping pushMappingRequest)
        {
            string accountId = (RequestContext.OnGoing as UnicaApiCallContext).AccountId;
            try
            {
                var mapping = ParseProviderMapping(accountId, pushMappingRequest);
                bool status = await _hotelMappingProvider.SavePushMappingsAsync(accountId, mapping);

                if (status)
                {
                    _logger.Information($"[PushMapping] Mapping saved for {pushMappingRequest.VervotechId}-{pushMappingRequest.ProviderHotelId}{pushMappingRequest.ProviderName}");
                }
            }
            catch (System.Exception ex)
            {
                _logger.Fatal(ex, ex.Message + " - " + JsonConvert.SerializeObject(pushMappingRequest));
            }

            return Ok(new ApiResponse()
            {
                Status = true,
                Message = $"Mapping is saved successfully in {accountId}",
                StatusCode = 200
            });
        }
        ```
- **ClassDefinitions**:

    - **ProviderMapping**:

        - **VervotechId**: int
        - **ProviderHotelId**: string
        - **ProviderName**: string
        - **ChannelIds**: List
        - **ModifiedOn**: DateTime
        - **ProviderLocationCode**: string
        - **Type**: string
    
    - **Type**:
        - New
        - Update
        - Delete

    - **ApiResponse**:
        - Status: boolean
        - Message: string
        - StatusCode: int     