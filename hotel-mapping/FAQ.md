**Q. Can I make parallel calls for Vervotech Mappings APIs ?**
It is strictly not recommended to make parallel calls for Vervotech Mappings APIs.

**Q. How do I get the accountId and apikey for making API request ?**
Please connect with our support team at https://support.vervotech.com/support/tickets, they will guide you regarding getting the accountId and apikey.

**Q. What should be the frequency of the sync ?**
We highly recommend performing a daily synchronization to ensure your data remains up-to-date. However, at a minimum, you should synchronize at least twice a week to maintain a reasonable level of freshness.

**Q. What should be the frequency of downloading the provider content ?**
We recommend downloading provider content once a month. This frequency strikes a balance between keeping your content current and minimizing resource usage.

**Q. In what order should the mapping API calls be made ?**
The API calls should be made in the following order:

- **GetDeletedMappings**: Call this API first to fetch all the deleted mappings. Ensure that you call the API repeatedly until all deleted mappings are retrieved. Once obtained, update your database accordingly.

- **GetUpdatedMappings**: After handling deletions, proceed to call this API to get the updated mappings. Like before, keep calling it until all updates are fetched. Update your database with the new mapping information.

- **GetNewMappings**: Finally, call this API to get new mappings added since the last sync. Ensure that the API is called iteratively until all new mappings are acquired. Update your database with these new entries.