Once your setup is complete, you can begin downloading hotel content and mappings from all your configured providers through Vervotech's unified web services. 

To manage this effectively, you’ll need to build a sync tool on your end. This tool should run on a regular schedule (e.g., daily or weekly) to fetch the latest mappings and content from Vervotech Mappings API. 

The sync process ensures that any updates — including new, updated and deleted mappings and content — are continuously and seamlessly reflected in your system. 

To support this, you should: 

- Create a content database within your infrastructure. 
- Use it to store the mapping and content data retrieved from Vervotech. 
- Keep it regularly updated by scheduling automated syncs. 

 

Additionally, you can enhance your content by using Metadata Content APIs, which allow you to fetch standardized information such as: 

- All Vervotech id’s 
- Hotel chains and Brands 
- Property types 
- Hotel amenities 
- All Countries list 

This provides a richer and more structured dataset for your travel platform. 

![UnicaContentSync_Premium_3_0.png](https://api.apidog.com/api/v1/projects/898598/resources/354281/image-preview)

As shown in the diagram above, there are mainly 6 mandatory APIs involved to get the updated mapping as well as hotel content.  

**Sync APIs – Initial and Incremental**

These APIs are required to download and update your hotel mappings and content: 

**Initial/First-Time Content Download**

1. Get All Vervotech IDs – Fetches all Vervotech hotel IDs. 
   Link: https://docs.vervotech.com/get-all-vervotech-ids-16383691e0

2. Get New Mappings - Fetches all Vervotech IDs include provider hotel IDs, and provider destination code, etc. 
3. Get Curated Content or Provider Content by Vervotech IDs – Use Vervotech hotel IDs to fetch full content. You can use any of these APIs and fetch the hotel content by using the Ids fetched from Get All Vervotech Ids API. 

**Incremental Sync**

1. Get Deleted Mappings – Fetch deleted mappings added since the last sync. 

2. Get New Mappings – Fetch mappings added since the last sync. 
3. Get Updated Mappings – Fetch modified mappings added since the last sync. 
4. Get Curated or Provider Content – Use the updated and new hotel IDs to fetch relevant content. 

**Testing and Validation**

Use Swagger or Postman to validate your API keys and environment variables. Ensure the variables (e.g., accountId, apiKey) are set correctly.  

**Customer Support**

For help and further guidance, access the Customer Success Portal for documentation, video tutorials, and direct support from the Vervotech team. 