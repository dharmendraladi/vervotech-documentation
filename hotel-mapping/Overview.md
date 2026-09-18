Dual mapping module is designed to enhance the accuracy and reliability of bookings by re-verifying mappings just before booking confirmations or verifying it post-booking.

### **What are the Problems?**
Financial Loss Due to wrongly mapped properties
Untimely Detection of Mapping/Booking Discrepancies
Operation Overhead to handle the bad bookings: Cancellation and re-booking with another property, time-sensitive actions, reconciliation overhead with suppliers.
Customer frustration since wrong property is getting booked
Brand degradation
### **What are the Solutions?**
Re-verify Mappings just before the actual booking happens: Detect discrepancies before checkout.
Re-verify Mappings immediately after booking: Detect discrepancies post-booking without blocking customers. Examples:
Allow refundable bookings with discrepancies to proceed, marking them for manual review.
Block non-refundable bookings with discrepancies and contact the customer.
For low-value bookings, proceed with a review flag; for high-value bookings, block and contact the customer.
### **What are the Benefits?**
Save financial loss from non-captured bad bookings.
Reduce Operational Overhead.
Gain extra time to manage bad mappings.
Avoid customer frustration and enhance brand image.
### **What are the Integration Points?**
Real-time Pre-Checkout Mapping Verification: Ensures accuracy before booking is finalized.
Real-time Post-Booking Mapping Verification: Verifies accuracy after booking confirmation.
### **What are the Variations in the Offerings?**
Retrieve Mappings Only: Retrieve provider property mappings based on the provider's hotel ID.
Retrieve Mappings with Curated Content: Provides mapping and curated content details.
### **What is the Onboarding Process?**
Signup with Vervotech Dual Mapping.
Vervotech's customer success team contacts you.
Account setup on Vervotech platform.
Providers grant access for mappings.
Begin receiving mappings in API response.
Integrate APIs and discrepancy detection on the customer platform.
### **Integration Example**

```
public async Task<List<string>> CheckMappingDiscrepanciesAsync(List<VervotechMapping> vervotechMappings,List<ExistingMapping> existingMappings)
    {
    List<string> discrepancies = new List<string>();
    foreach (var vervotechMapping in vervotechMappings)
    {
    bool existsInExisting = existingMappings.Any(existing =>
    existing.ProviderHotelId == vervotechMapping.ProviderHotelId &&
    existing.ProviderFamily == vervotechMapping.ProviderFamily);

    if (!existsInExisting)
          discrepancies.Add("Discrepancy found, take required action");
    }
    foreach (var existingMapping in existingMappings)
    {
      bool existsInVervotech = vervotechMappings.Any(vervotech =>
          vervotech.ProviderHotelId == existingMapping.ProviderHotelId &&
          vervotech.ProviderFamily == existingMapping.ProviderFamily);

      if (!existsInVervotech)
      {
          discrepancies.Add("Discrepancy found, take required action");
      }
     }
     return discrepancies;
     }
     // Example usage
      string providerHotelId = "12345";
      string providerFamily = "SampleProvider";

      //Fetch mapping using vervotech dual mapping
      List<VervotechMapping> vervotechMapping = FetchMappingByProviderHotelId(providerHotelId,providerFamily);

      string result = await CompareProviderMappingsAsync(vervotechMappings, existingMappings);
```