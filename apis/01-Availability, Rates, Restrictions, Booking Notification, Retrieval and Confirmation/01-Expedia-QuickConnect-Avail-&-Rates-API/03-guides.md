# FAQ & Guides
<a name="/gettingconnected"></a>
## Getting Connected with Expedia for the First Time

Before activation on Expedia QuickConnect, the EQC partner has to go through several steps:

![ACTIVATION PROCESS ON EXPEDIA QUICKCONNECT](/images/ACTIVATION PROCESS ON EXPEDIA QUICKCONNECT.jpg)

### Development phase

All our API specifications are on this site.  You must abide by our [terms and conditions](/terms.html).
If you have any development related questions during this phase, please send email [eqcss@expedia.com](mailto:eqcss@expedia.com).

### Test phase

Expedia will send you access details for a test property in production once you send the following information to [eqcss@expedia.com](mailto:eqcss@expedia.com):

- Company name
- System Type (CRS, PMS, Channel Manager, etc)
- System name
- Address
- Phone number
- Contact information
- Website
- How many hotels utilize your system?
- System description
 
In order to assign a property that will suit your needs we need to know more about what you have coded for. Please return a completed [interface questionnaire](/files/EQC%20Interface%20Questionnaire.docx) to [eqcss@expedia.com](mailto:eqcss@expedia.com).
 
 
### Beta (Soft Launch) phase
If testing is successful, select one of your hotels to be Beta and return a completed [enrollment form](/files/EQC%20Enrollment%20Form.docx) to [eqcss@expedia.com](mailto:eqcss@expedia.com) so we can initiate the Soft Launch process (one hour phone call with testing plan, one week monitoring in production with a real hotel). There are also some [soft launch scenarios](/files/EQC%20Softlaunch%20Scenarios.docx) to view.
      
### Go Live phase
After soft launch is over (usually it takes one week), the connectivity will be officially up and running in production and other properties can be connected.

## Guidelines

### Basic Connectivity Requirements
In order for properties to use the Expedia QuickConnect solution, they must meet the following requirements:
- Have a reliable connection to the Internet
- Be able to initiate HTTPS connections to Expedia QuickConnect servers and provide authentication (username/password)
- Be able to connect using Expedia QuickConnect URL. Expedia does not support connection to the QuickConnect Service directly via IP Address, as this address is subject to change without notice. If the EQC partner generally prefers IP Addresses for communication performance reasons, it may consider implementing an address caching strategy to reduce DNS lookups for the URLs. Additionally, if partner whitelists outbound connections, it must do so using a URL pattern rather than an IP range, as Expedia cannot guarantee a specific IP range / subnet.
- Be able to generate XML documents conforming to Expedia QuickConnect schemas (XSD)
- Be able to send changes to rates and availability using XML messages
- Be able to retrieve bookings (reservations, modifications and cancelations) using XML messages
- Be able to provide confirmation numbers for retrieved bookings (reservations, modifications and cancelations) using XML messages
- Be able to handle errors and warning scenarios as per this specification’s recommendations

Optionally, EQC partners can also retrieve products, availability and rate data using XML messages.

### Mapping property room and rate plan codes to Expedia IDs
To use Expedia QuickConnect, the EQC partner must develop and maintain a mapping between its property’s room and rate codes and Expedia’s equivalent room type and rate plan IDs. For example, a rate code SUM08001 in the hotel system maps to a unique rate plan ID 1093294 in Expedia system. This mapping is crucial for sending updates, because the EQC partner must specify this Expedia ID in its XML messages instead of the property’s equivalent code. 

Any time a property changes its room or rate codes, or requests new products to be created in Expedia’s, the EQC partner must update the mapping of the property’s codes in its system with Expedia’s equivalent new or existing IDs to maintain successful communication of availability updates and booking notifications between the EQC partner and Expedia. 

Note: Expedia IDs are also used to identify the room type and rate plan of a booking in the fax notification sent to the property.
The Expedia product mapping can be obtained electronically via the Product API, or manually via ExpediaPartnerCentral.com. The Expedia room type and rate plan IDs are alpha-numeric, and the relationship between room type and rate plan is a one to many hirachy. 

#### View Expedia room type and rate plan IDs via Expedia Partner Central
To verify the room type and rate plan IDs currently defined for your property in Expedia’s system, use the Room and Rate Plan Summary page at ExpediaPartner Central.com. This page allows the EQC partner to look up a property’s room and rate plan names to determine Expedia’s equivalent IDs.

### Included Features
The following table is an overview of what is and is not included in the availability and rates API. 
Note: The features listed as “not supported” are ones that are managed through Expedia's tools. Some of these features can be managed by the EQC partner, while others are managed by the Expedia Market Manager.


Supported | Not Supported
--------- | -------------
Updates by Property for Room Types and/or Rate Plans using Expedia IDs as identifiers (mapping of Expedia IDs to hotel codes to be done by EQC partner) | Updates by Property for Room Types and Rate Plans using hotel codes as identifiers (only Expedia identifiers can be used)
Updates by date range and day(s) of week | Modification of base allocation count through EQC
Update of multiple room types and rate plans at the same time, for multiple different date ranges. | Closing a room with outstanding base allocation
Total room allocation for properties with or without base allocation contracts | Closing all rate plans for a room with outstanding base allocation
Additional allocation for properties with base allocation contracts | Single-person supplements in per-person pricing Extra person/child/infant fees for all pricing models
Opening and Closing – rate plans |
Opening and Closing – room types |
Rate per day for base occupancy (per day pricing model) |
Rate per day for base occupancy, per length of stay (per day length of stay pricing model) |
Rate per day by occupancy (occupancy based pricing model) **Recommended** |
Rates could be Net Rate, Sell Rate or Lowest Available Rate, based on product configuration in Expedia system. |
Day-of-arrival pricing with rate change indicator |
Minimum length of stay (based on arrival or stay through) |
Maximum length-of-stay (based on arrival or stay through) |
Update number of rooms available as far as two years in advance |
Closed-to-arrival restrictions |
Closed-to-departure restriction |

<a name="/sendingupdatestoexpedia"></a>
### Sending updates to Expedia
In order to keep Expedia in synch with the hotel availabilty and rates, updates should be triggered by the hotel’s system as soon as rates, availability or restrictions change. 
Speaking from experience, Expedia expects to receive per day, on average, approximately 180 updates per product, per hotel. For a typical hotel connected through EQC, which has on average 10 different room type/rate plans combinations, this should translate into approximately 1,800 updates per hotel, every day.
Some specific conditions could cause hotels to generate significantly more updates per day. If you feel that your hotel will consistently generate more than 180 updates per product, please request help from Expedia through your Connectivity Account Manager.

#### Counting how many updates are included in a message
Expedia has specific rules and recommendations around message bundling, specifically related to how many updates are included in any one given message. Update count is defined as follows:
Expedia defines the Update Count of an AR message as the number of distinct data elements being changed by that message. Each individual rate, restriction or status change for one stay date is counted as one update.
For example, the following message excerpt contains 3 updates: number of rooms, a rate and a CTD restriction for one day.

```xml
<AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-01"/>
        <RoomType id="9989">
            <Inventory totalInventoryAvailable="50"/>
            <RatePlan id="556895">
                <Rate currency="EUR">
                    <PerDay rate="55.00"/>
                </Rate>
                <Restrictions closedToDeparture="true"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
```
The following excerpt contains 1098 updates: number of rooms, a rate and a CTD for 366 days.
```xml
<AvailRateUpdate>
        <DateRange from="2012-08-01" to="2013-07-31"/>
        <RoomType id="9989">
            <Inventory totalInventoryAvailable="50"/>
            <RatePlan id="556895">
                <Rate currency="EUR">
                    <PerDay rate="55.00"/>
                </Rate>
                <Restrictions closedToDeparture="true"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
```

#### Prevent Duplicate Updates
The property must ensure that it only sends Expedia QuickConnect true updates, not information that has already been transmitted to Expedia. For example, it is poor practice to send a property’s entire Expedia availabilty every day. Messages should only be sent to keep Expedia synchronized with changes on the hotel system. 
Avoiding redundant updates will also ensure that those necessary updates are processed faster, since each update is handled sequentially and:
- Expedia QuickConnect only allows one connection open per property at a time
- Processing one message can be a lengthy process, taking up to 5 seconds. 

#### Availabilty Changes related to Expedia bookings
Whenever Expedia books or cancels a room, the hotel system interface to Expedia QuickConnect should NOT update number of rooms available for sale on Expedia as this will be updated automatically by Expedia.
For example, if the property receives a booking for room type ID “111” for December 24th 2016 to December 26th 2016 from Expedia, the property should NOT send a decrement of 1 room for the room type “111” for December 24th and December 25th 2016. Expedia is already aware.

#### Combining multiple updates in one message to make messaging more efficient
To reduce the number of messages sent to Expedia QuickConnect, the EQC partner should make use of multiple AvailRateUpdate elements in one message, and leverage the possibility to specify multiple date ranges, room types and rate plans in the same message. 
Multiple updates can be bundled into one single AR message by making use of the new AR schema that allows for one or more AvailRateUpdate elements. The following example is an update request for 5 different products and for one day, with different values for each product:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- Sample AR request message: multiple AvailRateUpdate elements-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3546"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-01"/>
        <RoomType id="558875" closed="false">
            <Inventory totalInventoryAvailable="10"/>
            <RatePlan id="556895">
                <Rate currency="EUR">
                    <PerDay rate="100.00"/>
                </Rate>
                <Restrictions closedToArrival="false" closedToDeparture="false" minLOS="2" maxLOS="7"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-01"/>
        <RoomType id="558875" closed="false">
            <RatePlan id="665456">
                <Rate currency="EUR">
                    <PerDay rate="150.00"/>
                </Rate>
                <Restrictions closedToArrival="false" closedToDeparture="true" minLOS="1" maxLOS="14"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-01"/>
        <RoomType id="558875" closed="false">
            <RatePlan id="98789">
                <Rate currency="EUR">
                    <PerDay rate="180.00"/>
                </Rate>
                <Restrictions closedToArrival="false" closedToDeparture="false" minLOS="1" maxLOS="7"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-01"/>
        <RoomType id="99677" closed="false">
            <Inventory totalInventoryAvailable="10"/>
            <RatePlan id="35345">
                <Rate currency="EUR">
                    <PerDay rate="110.00"/>
                </Rate>
                <Restrictions closedToArrival="false" closedToDeparture="false" minLOS="1" maxLOS="7"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-01"/>
        <RoomType id="99677" closed="false">
            <RatePlan id="342223">
                <Rate currency="EUR">
                    <PerDay rate="145.00"/>
                </Rate>
                <Restrictions closedToArrival="false" closedToDeparture="false" minLOS="1" maxLOS="14"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
#### Restricting the maximum number of updates per message
Expedia will restrict the max number of updates allowed in one message. Any request containing more than 3000 updates will be rejected by Expedia and no update will be applied. The error message returned will be:
3107: Update exceeds allowable size - Maximum allowable size is 3000 updates.
If this happens, the EQC partner needs to make sure to revise its implementation of Expedia QuickConnect interface to prevent it from sending messages containing more than 3000 updates. Different strategies can be followed to break down messages into smaller sizes, such as breaking it down by date range or limiting the number of products included per message. 
Expedia recommends designing a system that properly balances between number of messages generated and number of updates included in one message. While the limit of updates per message is 3,000, Expedia doesn’t expect to see many messages coming close to this limit. 
If you are unsure on how to proceed, we recommend requesting help from Expedia through your Connectivity Account Manager.

### Date Ranges
Properties can manage their rates and availabilty for up to 2 years in the future. 
We recommend that EQC partners be careful when making use of long date ranges within one AR RQ. Long date ranges imply many updates per message, and Expedia limits one message to 3,000 updates at most.

### Minimum and Maximum Lengths of Stay
#### Arrival VS stay-through
Expedia supports two different methods to apply minimum and maximum length of stay restrictions.
Arrival-based restrictions: Minimum and maximum length of a hotel stay which the system calculates by reading the LOS configured for the requested arrival date.
Stay-through based restrictions: Minimum and maximum length of a hotel stay which the system calculates by reading all days of the requested stay and applying the most restrictive values from any of those days.
This is a hotel-level setting in Expedia system, and any Expedia market managers can modify this setting. If you are not sure which setting applies to your hotel, please contact:
- eqcss@expedia.com for new activations
- hothelp@expedia.com for existing connections

#### Restricting updates beyond a certain Minimum length of stay value
Expedia will not accept updates past a certain minimum length of stay value. When a hotel exceeds the maximum value allowed per Expedia configuration, it will receive the following error:
3135: MinLOS value ([value specified in message]) exceeds Extranet auto-approval threshold ([configuration for this hotel in Expedia system]) for length of stay.
This is a setting that is configured on a per-hotel basis in Expedia system. If you feel your hotel is misconfigured, please reach out to your market manager.

### Sequencing/Ordering of Messages
Sequencing of messages (order in which messages are sent by the EQC partner and then processed by Expedia QuickConnect) is critical. EQC partners should ensure that messages for Expedia QuickConnect are sent in the right order so that Expedia QuickConnect is not updated with outdated information.
Since Expedia QuickConnect only accepts one connection at a time per property, and processes requests synchronously, an older message sent after a newer one would be processed in the order it is received, potentially overwriting more up to date information in Expedia QuickConnect. Therefore, it is important that EQC partners take extra care when designing their solution to make sure this cannot happen in their systems.

### Property Re/Synchronization Mechanism 
For many different reasons, it is possible for Expedia and its EQC partner to have their rates and/or availability fall out-of-synch. 
When a property is first activated on Expedia QuickConnect, the EQC partner’s system and Expedia QuickConnect should be synchronized. The EQC partner’s system therefore requires a function that can synchronize the property by triggering updates that send all the information about rates and availability for at least the next 365 days.
Also, an EQC partner could experience system problems and lose track of which updates were already sent to Expedia. It might then become necessary to perform resynch of availability and rates for specific products and dates.
Several parameters should be configurable before triggering synchronization:
- The interval of dates on which to perform the synchronization
- Which room type(s) (one or more, possibly a list)
- Which rate plan(s) (one or more, possibly a list)

he (re)synchronization data should only be sent once, without repeating the same information twice for any product included in the process.

<a name="/sendtherighttypeofratetoExpedia"></a>
### Send the right type of rate to Expedia: sell rate, net rate or LAR?
Expedia accepts receiving sell rate, net rate or LAR (lowest available rate) for rate update through EQC, and the type of rate to send to Expedia must be insync with the configuration in the Expedia system. 
Please verify which type of rate is being used to update Expedia products using the new Product, Avail, Rate and Restrictions API, or log on the Extranet and consult product information there.

Product Type | Distribution Type | Rate Acquisition Type 
------------ | ----------------- | ---------------------
Flex product, sell rate based | Hotel Collect and Expedia Collect | Sell rate
Flex product, net rate based | Hotel Collect and Expedia Collect | Net rate
Expedia Collect-only product | Expedia Collect | Net rate or LAR
Hotel Collect-only product | Hotel Collect | Sell rate

Please note that each flex product consists of a primary product and a derived product, where hotel should only send AR update for the primary product not for the derived product. The derived product will have the same availability as the primary product and the derived rate will be calculated by Expedia.

Product Type | Base Product – AR Update by Hotel | Derived Product – Calculated by Expedia
------------ | --------------------------------- | ---------------------------------------
Flex products, sell rate based | Hotel Collect rate plan, Sell Rate | Expedia Collect rate plan
Flex products,  net  rate based | Expedia Collect rate plan, Net Rate | Hotel Collect rate plan

It is critical for the EQC partner to define the right type of rate to upload because there will not be any validation done on the EQC AR interface to confirm the rate sent has the right type. Sending the wrong rate will either make Expedia rate much lower than hotel desired rate (when sending a net rate for a sell rate or LAR based product), or much higer (when sending a sell rate or LAR for a net rate based product). 

### Code to the Correct Pricing Model
When developing Expedia QuickConnect, make sure you develop the correct pricing models for your customers.
Currently Expedia offers three different pricing models. The table below provides a brief description for each of them.

Pricing Model | Description
------------- | -----------
Occupancy-based Pricing | Pricing model based on number of travelers staying in the room. It is recommended for the hotel to define a rate for each applicable occupancy, and if a rate is not assigned to a given occupancy, the following rules will apply: Any gap in defined occupancy rates will result in the rate for the next higher occupancy being charged (for an exception to this rule with regards to children, see below). So if rate is specified for occupancy of two but not for occupancy of one, the rate for double occupancy will be applied automatically when a guest wants to book a single. Any number of guests exceeding the highest specified occupancy rate (up to the maximum occupancy allowed in the room) will be charged extra person fees. By default, children are always charged an extra child fee and do not factor in to the occupancy being charged, with one exception:  the extra child fee is waived if there is a gap in the defined occupancy rates and it costs less to treat the child as an adult. For example, if there is one adult and one child booking a room on dates where there is only  a rate defined for a double occupancy and not for a single occupancy, rather than charge a double occupancy plus the extra child fee, the fee is waived for that first child.**Note that a property setting is available for those that wish to charge children as regular occupants until the number of allowed guests exceeds the highest defined occupancy. 
Per-day Pricing | Pricing model where a price is assigned to a room for each day for the base occupancy. With this pricing model, a room is assigned a rate each day for a base number of occupants. Though commonly configured for two occupants, the base number can be customized by rate plan. A single guest will always be quoted the rate for the base number of occupants, whereas guests above the base number are charged extra person fees if configured. For example, a single guest would be quoted a base double rate for each day at the property, while four guests would be quoted the base rate plus extra person fees for two of the occupants. Room rates are based on the number of adults in a booking, while children are charged an extra person fee. Children are only charged an adult guest rate if there are not enough adults to fill the base occupancy for a booking. For example, a booking for one adult and two children in a hotel with per-day pricing and a base occupancy of two will only charge an extra person fee for one child.
Day-of-Arrival Pricing | A pricing attribute that can be optionally enabled by Expedia (at the demand of the hotel) on a rate plan, where the guest pays the same rate on each day of stay. Rate changes can be requested on specific days using a rate change indicator flag. Day-of-Arrival pricing can be enabled for rate plans using anyone of the above pricing models.
Per Day Length-of-Stay Pricing | A pricing attribute that is currently only available in conjunction with Per-Day pricing. Allows hotels to define rates for length of stays 1 up to 28, per arrival date. For each date of a year, hotels can send up to 28 rates. More specifically: -	The rate to be sent for each length of stay is the rate for one night. If a rate of 100$ is sent for LOS=7, the base rate total for 7 nights will be 7x100$. -	The first time a hotel sends Expedia rates for a specific day, it is recommended to send rates for all supported lengths of stay per day. For example, for arrival on September 1st, Expedia should receive up to 28 different rates, for LOS 1 to 28, assuming the hotel supports 28. -	Subsquent updates should be made only to the length of stay rates changing: for example, if rates for LOS 7 and above are changing, the update message should only contain the rates for LOS 7 and above. If a rate is not defined for a length of stay, it will not be available for sale on Expedia points of sale. This is true for both gaps between 2 defined LOS, or any LOS after the max defined. For example, if, for arrival on Septembre 1st, rates for LOS 1, 3 and 5 are defined, it will not be possible to stay for 2,4 and 6 or more nights when arriving on that date. After a LOS rate is defined in Expedia system, it is not possible to remove it. Hotels should make use of restrictions to make previously loaded rates unavailable if needed: Min and Max LOS can be used to restrict specific length of stays, close at room and rate levels can also be used, etc. Restrictions are still in effect: for example, even if a rate for LOS=5 is defined, it might not be possible to book it if one of the subsequent days is closed for departure, or if it is completely closed at the room or rate level.

Expedia recommends EQC partners to use occupancy-based pricing as this pricing model offers the most flexible solution for pricing. A simple migration process can also convert a property currently on the per-day pricing model to occupancy-based pricing (the property can request this from their Market Manager). Note that migration from per-person to occupancy-based pricing involves a manual transfer of information which requires scheduling with the property’s Market Manager.
Please make sure the pricing model you specify when sending rate updates is the right one pre-configured for your property on Expedia Partner Central. You can obtain pricing model information through a PARR request for product details, including the “returnRatePlanAttributes” attribute. Alternatively, you can access Expedia Partner Central and confirm, from the Inventory Grid page, which pricing model is set for your property.  Should a property be using a pricing model you have not developed please contact:
- eqcss@expedia.com for new activations
- hothelp@expedia.com for existing connections

### Managing Rates for a Property Using Occupancy-based Pricing
If an EQC partner now wants to change the number of occupancy levels in the room, it has first to contact its Market Manager to change the configuration of the room type (thereby updating the property’s settings on Expedia Partner Central), and then it has to send Expedia the new rates for those occupancy levels. Occupancy-based pricing also requires the rate to be set to the total amount charged for that occupancy level.
For example, if a property already has occupancy level 4 newly configured for it on Expedia Partner Central and wants to set the rate for occupancy level 4 of a room type to $160.00, then it should include the following input in the AR RQ message:
```xml
<PerOccupancy rate="160.00" occupancy="4"/>
```
The AR API currently doesn’t support removal of occupancies. If occupancies need to be removed, please contact: 
- eqcss@expedia.com for new activations
- hothelp@expedia.com for existing connections

### AR Responses Containing more than 20 Warnings
When messages fail various Expedia validations, Expedia will return up to 20 warnings per type of problem. For example, if a supplier attempts to close remaining base allocation for 120 days, it will get the following response back:
```xml
<AvailRateUpdateRS xmlns="http://www.expediaconnect.com/EQC/AR/2007/02">
	<Success>
		<Warning code="7013">Warning 1 out of 120 for this cause. Inventory date 2011-08-30; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 2 out of 120 for this cause. Inventory date 2011-08-31; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 3 out of 120 for this cause. Inventory date 2011-09-01; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 4 out of 120 for this cause. Inventory date 2011-09-02; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 5 out of 120 for this cause. Inventory date 2011-09-03; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 6 out of 120 for this cause. Inventory date 2011-09-04; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 7 out of 120 for this cause. Inventory date 2011-09-05; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 8 out of 120 for this cause. Inventory date 2011-09-06; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 9 out of 120 for this cause. Inventory date 2011-09-07; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 10 out of 120 for this cause. Inventory date 2011-09-08; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 11 out of 120 for this cause. Inventory date 2011-09-09; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 12 out of 120 for this cause. Inventory date 2011-09-10; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 13 out of 120 for this cause. Inventory date 2011-09-11; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 14 out of 120 for this cause. Inventory date 2011-09-12; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 15 out of 120 for this cause. Inventory date 2011-09-13; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 16 out of 120 for this cause. Inventory date 2011-09-14; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 17 out of 120 for this cause. Inventory date 2011-09-15; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 18 out of 120 for this cause. Inventory date 2011-09-16; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 19 out of 120 for this cause. Inventory date 2011-09-17; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
		<Warning code="7013">Warning 20 out of 120 for this cause. Remaining 100 warnings will not be returned, we recommend that you address this problem and resubmit this request afterwards. Inventory date 2011-09-18; Room Type ID 352546; Total Inventory Available cannot be less than the current base allocation value (12). Ref=[683b1aa6-c1b5-11e0-8047-ef7899c6c51b]</Warning>
	</Success>
</AvailRateUpdateRS>
```
The EQC partner should solve the problems reported and attempt to resend the information to Expedia again. If needed, it is possible to obtain the full list of warnings that the request generated, if the request is issued within 7 days of the message creation date. For more details, please contact hothelp@expedia.com.

### Alarms and Monitoring
EQC partners should include monitors in their interface implementation that will allow partners to see the ratio of successful AR updates and to get detailed information on any errors or warnings. Alarms should also be created to notify concerned individuals (e.g. EQC partner tech support) when the rate of message errors or warnings returned by Expedia exceeds an acceptable threshold. It is recommended that an alarm be triggered when any type of message returns errors or warnings at a rate of 10% or more.
Partners should review errors and warnings frequently to ensure that bookings are received and confirmed, and that all updates are processed correctly. Failure to do so may result in Expedia booking rooms at the incorrect price or already sold out, or bookings to fall back to fax or email if not confirmed.

### Specifying number of rooms available inclusive of base allocation
For hotels that have a base allocation agreement with Expedia, the most straightforward method for the EQC partner to update number of rooms available is to specify the total rather than a flexible allocation. The totalInventoryAvailable attribute includes base allocation as well as any flexible allocation, so the EQC partner does not need to manage a separate count for base allocation and deduct that amount from the non-base (i.e. flexible) amount before providing updates.
It is important to note, however, that if there is unsold base allocation remaining for the room type specified in an update and the totalInventoryAvailable is set lower than that base amount, Expedia QuickConnect will reject the update and return warning code 7013.
Here are a few examples of how the totalInventoryAvailable amount will be divided into the base and flexible allocation for a property’s room count depending on whether there is base allocation contract:

![Base Allocation](/images/Base Allocation.jpg)

### Closing rate plans with remaining base allocation
Hotels typically use more than one rate plan to sell a room. One important reason for multiple rate plans is that those needed to sell rooms for standalone bookings (room-only) are different from those needed for package bookings (room + flight/car/train). As a result, hotels usually have both standalone and package versions of a rate plan configured, such as: Room Only (S), Room Only (P) and Room incl. Breakfast (S), Room incl. Breakfast (P).
Expedia allows a hotel to close out any and all of its rate plans, regardless of flexible allocation, as long as there is no base allocation remaining for the associated room type on affected days. If the base allocation is not entirely sold for a room type on a particular day, then one standalone rate plan is obliged to remain open in order for Expedia to be able to make bookings from that base allocation. As a result, if a hotel sends AR requests to close out all rate plans when there is a base allocation remaining, the request to close the last standalone rate plan will be rejected and a warning message (Warning 7014) is returned. 

### Closing rooms to avoid overbookings
In order to close a room that is still available on Expedia, always send a close message for the room type along with setting the number of available rooms @totalInventoryAvailable or @flexibleAllocation to zero. Sending zero (0) for flexible allocation or total will not completely close the room type and, in a case of cancellation, the room will become available again on Expedia. Refer also to Section 5.6.13 "Closing rate plans with remaining base allocation" above for additional recommendations for properties with base allocation contracts.

### Using day of week attributes with date ranges
Day of week attributes can be used when EQC partners want to perform updates based on the day of week. For example, EQC partners might want to update rates for Friday, Saturday and Sunday, for the month of August 2012.
To do so, it is not necessary to call out every single date needing to be updated. Instead, day of week attributes can be used.
As soon as day of week attributes are used, updates will only be applied to the attributes for which the value is set to true. Missing or omitted day of week attributes will see their value defaulted to false.
When using day of weeks along with date ranges, Expedia recommends always specifying all 7 attributes, with their desired value (true for days requiring an update, false for days that shouldn’t be updated). This is the safest way for EQC partners to insure Expedia will interpret their updates the desired way.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating rates for 1 room type and 1 rate plan every weekend of the month of August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3547"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-31" mon="false" tue="false" wed="false" thu="false" fri="true" sat="true" sun="true"/>
        <RoomType id="558025">
            <RatePlan id="556895" closed="false">
                <Rate currency="EUR">
                    <PerOccupancy rate="80.00" occupancy="1"/>
                    <PerOccupancy rate="120.00" occupancy="2"/>
                </Rate>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
### Rate Plan Linkage and AR
For products with rate plan linkage rules in place and effective on a set of stay dates, the partner is not allowed to update the child products’ rates for those dates. Restrictions are not expected to be managed either if they are linked.
Child rate plans with linkage rules cannot be managed over AR when a rule is effective for the stay dates being updated. Only the parent can be managed: Expedia will automatically derive rates and restriction updates to the child. If a partner attempts to manage rates and / or restrictions on a rate plan where a rate plan linkage rule exists, the EQC AR service will return a successful response to the partner but will ignore the updates received on the child.

### Retry
-    Retry strategy if EQC partner cannot establish communication: If EQC partner receives an error from their application, saying it cannot connect to Expedia QuickConnect (including a connection refused), the EQC partner should perform retries.
-    Retry strategy in case of specific errors returned in XML response: EQC partners should implement retry strategy to handle messages that fail because of communication/network errors  (error code equal to or greater than 4000):
-  Expedia QuickConnect is in maintenance mode
-  Expedia QuickConnect is experiencing temporary problems
-  A network error occurred
This retry strategy should be different than the retry where the communication cannot be established. 

### Concurrency
Expedia QuickConnect only allows one connection at a time per hotel. EQC partners cannot send concurrent requests to update the same hotel. Requests should be queued in the hotel system.

<a name="/troubleshooting"></a>

## Troubleshooting

### Detailed Error Handling and Retry Strategy Recommendation
Expedia recommends implementing a strong retry strategy to insure important messages are successfully delivered and processed by Expedia. Expedia QuickConnect defines several different categories of errors. When implementing connectivity, EQC partners should handle errors properly. Depending on the type of interface and type of error, different strategies should be used.

#### System errors retry recommendation, specific to interfaces updating Expedia systems (AR)

**Communication errors (cannot establish connection, connection timeout, no response):** If the EQC partner’s system receives a network or communication error from its application, saying that the connection to Expedia QuickConnect cannot be established, or that the connection times out, or even that there is no answer coming from Expedia on the connection, it should retry the message using the following strategy:

Occurrence | Time | Action 
---------- | ---- | ------
0 | T0*: new message | Try to send message to Expedia QuickConnect but failed to establish communication. Stop trying to send any other message (to avoid out of order messages) and enter retry mode for the current message.
1 | T0 + 1 minutes | Try to send message. If failed, keep retrying.
2 | T0 + 2 minutes | Try to send message. If failed, keep retrying.
3 | T0 + 4 minutes | Try to send message. If failed, keep retrying.
4 | T0 + 8 minutes | Try to send message. If failed, keep retrying.
5 | T0 + 15 minutes | Try to send message. If failed, put the message on hold, wait before sending any other message, and raise an alarm to someone on the support team.

**Network and communication errors in AR RS (error codes between 4000 and 4099):** if the EQC partner manages to connect to Expedia QuickConnect and receives an AR Response in XML with an error message code between 4000 and 4099, the EQC partner should adopt this retry strategy:

Occurrence | Time | Action 
---------- | ---- | ------
0 | T0*: new message | Try to send message to Expedia QuickConnect but received an AR Response with an error code between 4000 and 4099. Stop trying to send any other message (to avoid out of order messages) and enter retry mode for the current message.
1 | T0 + 1 minute | Try to send message. If failed, keep retrying.
2 | T0 + 2 minutes | Try to send message. If failed, keep retrying.
3 | T1 + 4 minutes | Try to send message. If failed, keep retrying.
4 | T2 + 6 minutes | Try to send message. If failed, keep retrying.
5 | T3 + 8 minutes | Try to send message. If failed, keep retrying.
6 | T4 + 16 minutes | Try to send message. If failed, keep retrying.
7 | T5 + 32 minutes | Try to send message. If failed, keep retrying.
8 | T6 + 1h04 minutes | Try to send message. If failed, keep retrying.
9 | T7 + 2h08 minutes | Try to send message. If failed, keep retrying.
10 | T8 + 4h16 minutes | Try to send message. If failed, keep retrying.
11 | T9 + 8h32 minutes | Try to send message. If failed, drop the message, raise an alarm to someone in the EQC partner’s team and try to send the next message in queue.

** TO: Time Zero

**Internal system errors with error codes greater or equal to 4100:** Those messages should not be retried as they are indicative of a non-temporary problem in Expedia systems. Our teams actively monitor those problems and do their best to fix them in a timely fashion. For more information about those problems, the EQC partners should contact Expedia.

#### Error handling recommendation for non-retriable errors

In many cases, messages shouldn’t be retried:
- Business errors (error codes 3xxx): A message failing because of a business error should be dropped right away to allow other messages to go through (no retries). An alarm should be raised in the PMS or CRS system, and/or a report should be run every day in the EQC partner’s system for information on the problems the interface encountered.
- Authentication errors (error codes 1xxx): if the EQC partner’s system receives an authentication error, the EQC partner should stop trying to send the message and an alarm should be raised to an administrator to verify the configuration of the EQC partner’s system and to contact Expedia.
- Parsing errors (error codes 2xxx): if the EQC partner’s system receives a wrong XML format error, the EQC partner should stop trying to send messages and should raise an alarm to an administrator to look at the problem. This error should not happen if EQC partners first try to parse the XML message they are trying to send to Expedia QuickConnect to make sure that it validates against Expedia QuickConnect schema.
- Warnings (codes between 7000 and 8000 for AR, 10,000+ for BC): warnings are problems with the request that were ignored in order to process the other valid updates in the request. They are equivalent to business errors, but do not make the whole request fail. EQC partners should take the exact same actions with warnings as they take with business errors. It is important to capture warnings and take corrective actions.

### Communication Issues for any of the EQC APIs
#### Connection Cannot Be Established
For many different reasons, attempting to connect to Expedia QuickConnect might not work. If the problem is:
- Connection timeout (before establishing connection)
- Cannot resolve host name
- Cannot establish connection
Before looking for assistance on the Expedia QuickConnect discussion forum, the EQC partner should:
- Verify the URL used to connect to Expedia QuickConnect and make sure the address starts with https://
- Verify the domain name, and make sure that the address you are using is the right one for the environment you are targeting (do not try to send QA information to production, or vice-versa)
- If the EQC partner’s system is behind a firewall, make sure that port 443 is opened for connection to Expedia’s production environment (contact Expedia if you don’t know what the URL to the production environment is)

It is also possible to fail to obtain a connection because Expedia QuickConnect servers cannot accept any more connections than the ones currently established to other EQC partners. In this case, the EQC partner will receive a communication error saying:

- Connection refused

When this happens, the EQC partner should simply enter in retry mode as previously described.

#### Connection Established, No Response
If the EQC partner’s system manages to establish a connection to Expedia QuickConnect servers, but is not getting a response, the EQC partner should:
- Make sure that the EQC partner’s system is not closing the connection too early. Because updates are done synchronously in Expedia’s backend, the processing can take several seconds. That is why Expedia QuickConnect cuts the connection only after 1 minute of inactivity. Therefore, the EQC partner should keep the connection open for at least 60 seconds.
- Make sure the content length specified in the HTTPS header corresponds to the actual length of the HTTPS request. If the length specified in the header is actually longer than the message itself, it results in Expedia QuickConnect waiting for bytes that never arrive, and eventually times out.

#### Connection Established, Error Returned (error code greater than or equal to 4000)

The EQC partner might encounter different types of errors while trying to connect to Expedia QuickConnect. These errors are monitored by the Expedia QuickConnect team, so there is no need to advise Expedia QuickConnect of a problem when this occurs. The following is a list of the most common errors, and what the EQC partner should do about them. Please note that every error returned by Expedia services contains a reference ID that can be used when communicating with Expedia to investigate a problem.

Error Code | Error Desc | Explanation and EQC partner Action 
---------- | ---------- | ----------------------------------
4000, 4004, 4007 | Internal system error, please try again in a few minutes. | Enter in incremental retry mode. 
4001 | Internal timeout error, please try again in a few minutes. | Enter in incremental retry mode. 
4100, 4101 | Internal System Error. Do not retry this request. Our support team was notified of the problem. | Do not retry this message.  Expedia has been notified of the issue and will work on finding a solution for it. It is recommended to perform the update manually on Expedia Partner Central.
4206 | Expedia QuickConnect interface is temporarily closed. Please try again shortly. | Enter in incremental retry mode. 
5000 | Internal database error, please try again in a few minutes. | Enter in incremental retry mode.

#### Authentication Issues (error code in the 1000s)
The following table lists a few possible errors related to communication issues, and what the EQC partner should do about them:

Error Code | Error Desc | Explanation and EQC partner Action 
---------- | ---------- | ----------------------------------
1000 | Access denied: you are not authorized to use Expedia QuickConnect. Please contact Expedia to gain access. | This message should not be retried. Contact rollout@expedia.com to gain access to EQC for new activations. Contact hothelp@expedia.com to gain access to EQC for hotels that have been EQC-enabled for a while.
1001 | Authentication error: invalid username or password. | This message should not be retried. Verify username and password configured in your EQC interface. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
1003 | The user account provided doesn’t have the right access level | This message should not be retried. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.

#### Parsing and other protocol issues

Error Code | Error Desc | Explanation and EQC partner Action 
---------- | ---------- | ----------------------------------
2002 | Parsing error: parsing_error_description. | Correct XML format to comply with Expedia QuickConnect’s specification. Developers of the EQC partner system should be involved to find the problem.
2010 | The namespace specified is invalid. | Correct namespace and send a new message. Please note that namespaces are used to version Expedia service interfaces. Developers of the EQC partner system should be involved to find the problem.
3010 | Validation against schema failed because a value exceeds its defined length, the format is wrong, or because of another validation enforced by schema. | Correct the error in the system, and drop this message (no retry). Developers of the EQC partner system should be involved to find the problem.
3210 | Communication error: exceed max number of connections allowed (1). | EQC partner tried to open more than one simultaneous connection per hotel. For any given hotel, never attempt to send 2 concurrent messages. Always wait for a message to be responded by Expedia before sending any subsequent message

#### Response Business Errors

Once an AR RQ transaction has successfully been transmitted to Expedia QuickConnect, it can either receive a positive or negative acknowledgment. When errors are returned in AR Responses, none of the updates contained in the request were processed. In the case of availability and rates updates, several different types of errors can happen, and the negative acknowledgment of a transaction that failed can contain one or more of those error codes:

Error Code | Error Desc | Explanation and EQC partner Action 
---------- | ---------- | ----------------------------------
3015 | Business validation error, such as but not limited to: No updates provided in ICPRUpdateMessage, MaxLOS value (X) smaller than the MinLOS value(Y), The NumberOfGuests attribute may not be 0, etc. | This error can happen for various reasons: The AR RQ sent contained no updates - only room or rate IDs and dates, Contradictory Min and Max LOS were included in the message (Min > Max), For an OBP rate update, occupancy=0 was specified, which is not allowed/impossible, etc. EQC partner needs to capture the description returned along with this code and should advise affected hotel or property of the error to verify if there is a problem with its system or the implementation of Expedia QuickConnect. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3020 | Validation error: start date must not be before yesterday. | Make sure the system cannot send a date in the past, and drop this message (no retry).
3021 | Validation error: end date must not be before start date. | Make sure the system cannot send an end date smaller than a start date, and drop this message (no retry).
3022 | Validation error: end date must be within 2 years range. | Make sure that the end date of a date range cannot be greater than today + 2 years, and drop this message (no retry).
3026 | Flexible and total allocation cannot be sent together for roomType. | Make sure that the system cannot generate an AR request which specifies both flexible allocation and total allocation available for a room type.
3090 | Pricing models mismatch: you tried to update a price for <name of pricing model>, but your property is configured to use <name of other pricing model>. | Review your implementation of Expedia QuickConnect and make sure the pricing model you use is in line with what is configured on Expedia Partner Central for your property. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3103 | Currency code mismatch. | Make sure the property is sending the same currency as the one configured on Expedia Partner Central for this property. The property should send the same currency as the one configured on Expedia Partner Central for this property.
3107 | Update exceeds allowable size  - Maximum allowable size is 3000 updates | The message contains too many different updates. Expedia limits the number of distinct updates in one message to 3000. Please refer to [Sending Updates To Expedia](guides.html#/sendingupdatestoexpedia) and its sub sections for detailed information about Expedia recommendation for maximum number of updates per messages.
3108 | Duplicate Updates in the same message – [exact nature of conflicting updates received in request] | The message sent contains the same product and dates twice or more, and the updates requested conflicts with one another. Please make sure to only send us rates, restrictions or availability updates for a product and stay date combination once in a message.
3123 | Length of stay has to be provided for the Rate Plans with LOS enabled | The message didn’t include the Rate/@lengthOfStay attribute for a rate plan that is length of stay pricing enabled. Review your implementation of Expedia QuickConnect and make sure the pricing model you use is in line with what is configured on the HotelExtranet for your property. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3124 | Length of stay attribute can only be set on Rate Plans with LOS enabled | The message included the Rate/@lengthOfStay attribute for a rate plan that is not length of stay pricing enabled. Review your implementation of Expedia QuickConnect and make sure the pricing model you use is in line with what is configured on the HotelExtranet for your property. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3125 | Field not accessible for edit for this hotel and source  - [Exact field not accessible] | The message sent contains updates that cannot be accepted for the hotel specified. The sources may vary: Rate change edit not allowed: the rate change indicator is used in request but product isn’t enabled for day of arrival pricing. Please correct the problem in the message. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3128 | Invalid data in request | For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3129 | Invalid Date | Verify the dates you provided in the AR RQ and then resubmit your message.
3135 | MinLOS value ([value provided in RQ]) exceeds Extranet auto-approval threshold ([Expedia configuration]) for length of stay | You submitted a MinLOS restriction value greater than the acceptable threshold defined for your hotel in Expedia system. Please resubmit your update with a smaller value, or contact your market manager.
3142 | Rate plan does not match the property acquisition type  - Rate plan XXX does not match acquisition type | You provided an update for a derived/non-manageable rate, which is not allowed.  For more information about flex rate plans and derived/non-manageable rates, please refer to the [Send the right type of rate to Expedia](guides.html#/sendtherighttypeofratetoExpedia) section
3145 | Not available for updates. Hotel undergoing business model conversion. | Verify if hotel has completed business model conversion for ETP and resubmit updates. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3202 | Hotel ID not found. You either specified an invalid hotel ID or your account is not linked to this hotel. | Verify if there is a mapping issue in EQC partner’s system. If the mapping is correct, please verify that the user configured for Expedia QuickConnect has access to update this property (i.e. the user is able to update through Expedia Partner Central). For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3203 | The following RoomTypeIDs do not belong to the given hotel : room_type_ID | Fix mapping in EQC partner’s system. For EQC partners building new products, Expedia Market manager must be notified ahead of time to create the new product in Expedia systems. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3204 | The following RatePlanIDs do not belong to the given hotel : rate_plan_ID | Fix mapping in EQC partner’s system. For EQC partners building new products, Expedia Market manager must be notified ahead of time to create the new product in Expedia systems. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
3405 | Message size limit exceeded. | The overall size of the AR incoming request exceeds our limit of 100 kb. EQC partner needs to break down AR RQ into smaller requests.

<a name="/arresponsebusinesswarnings"></a>
#### AR Response Business Warnings

Once an AR RQ transaction has been submitted to Expedia QuickConnect, it can either receive a successful or negative acknowledgment. A successful acknowledgment can optionally contain warnings, which are meant to indicate that part of the incoming request was not successfully processed, with details on what failed. When warnings are returned, anything that is not explicitely listed as a warning has been successfully processed.
A request can potentially contain dozens of warnings. If a request generated too many warnings, not all of them will be reported back to the EQC partner in the response. When this happens the warning messages will clearly indicate that the EQC partner should fix the problems with the request first before resubmitting it to Expedia later.

Warning Code | Error Desc | Explanation and EQC partner Action 
------------ | ---------- | ----------------------------------
7000 | Default warning | Covers use cases that are not defined below. Refer warning description for a specific warning details.
7009 | Occupancy exceeds maximum allowed value | Applicable to occupancy-based pricing hotels only. The EQC partner requested to update an occupancy level that exceeds the room’s maximum occupancy defined in Expedia system. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
7010 | Room type cannot be closed because there is base allocation remaining on the room. | The room type will remain available on Expedia until all base allocation is sold. For assistance, please contact your market manager, who will manage the process internally or correct the issue.
7013 | Total Inventory Available cannot be less than the current base allocation value (XX). | In the case of a hotel using base allocation, Total Inventory Avaliable cannot be set to a value less than the current base allocation room count. 
7014 | At least one standalone rate plan should be kept open until all base allocation has been sold. | The last available rate plan of the standalone rate category has to remain open until all base allocation has been sold. For assistance, please contact your market manager, who will manage the process internally or correct the issue.
7016 | MaxLOS value (value provided) smaller than the MinLOS value (value provided). | This problem will occur if a MinLOS restriction received in incoming AR RQ is greater than existing MaxLOS restriction already stored in Expedia system for this hotel. The EQC partner must make sure to provide a MinLOS value smaller than MaxLOS. Note that if an EQC partner includes within the same AR RQ a MinLOS > MaxLOS, an error will be returned.
7018 | Base Availability request adjusted to prevent total availability from exceeding the system maximum | Only applicable to hotels on base allocation and for updates made using “flexibleAllocation”. If the total of base and flexible allocation exceeds Expedia’s hard limit of 4999, this error will be returned. In this specific case, the update was processed and the total allocation is adjusted to 4999 automatically. Note that it is not possible to provide flexible or total allocation greater than 4999 in AR RQs, as the schema doesn’t allow it. This warning is returned solely when there is base allocation left in Expedia systems, and the addition of base plus the value provided in AR RQ exceeds 4999.
7019 | Net rate must be within Rate Verification threshold. | As per Expedia rate validation & verification rules (based off recent bookings), the rate you provided is either too high or too low. Please correct the rate value. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
7021 | Sell/LAR rate must be within Rate Verification threshold. | As per Expedia rate validation & verification rules (based off recent bookings), the rate you provided is either too high or too low. Please correct the rate value. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
7022 | Inactive Rate Plan Updated. | The update was processed; however this product will never be sold until an Expedia Market Manager activates this product. For assistance, please contact eqcss@expedia.com for new activations and/or hothelp@expedia.com for existing connections.
7023 | Restrictions were not updated due to a rate plan linkage rule. Only Main rate plans, not Derived rate plans, can receive restrictions updates. | An active rate plan linkage rule exists, that links the restrictions between the rate plan provided in the request and a parent/main rate plan. It is not possible to update a child/derived rate plan.
7024 | Rates were not updated due to a rate plan linkage rule. Only Main rate plans, not Derived rate plans, can receive rates updates. | An active rate plan linkage rule exists, that links the rates between the rate plan provided in the request and a parent/main rate plan. It is not possible to update the child/derived rate plan.
7025 | Net rate must be within Rate Verification threshold. The rate plans (listed) with the violation and their Main/Derived rate plans (not listed) are not updated. | An active rate plan linkage rule exists between the rate plan provided in the request and a parent/main rate plan. The net rate update we received was outside of the rate verification threshold defined for either the parent/main rate plan, or the child/derived rate plan. The net rates of both the parent/main and child/derived rate plans were not updated. This warning is equivalent to 7019 (used for unlinked rate plans). 
7026 | At least one rate plan should be kept open until all base allocation has been sold. The rate plans (listed) with the violation and their Main/Derived rate plans (not listed) are not updated. | An active rate plan linkage rule exists between the rate plan provided in the request and a parent/main rate plan. The AR request attempted to close either the last open parent/main rate plan, or the child/derived rate plan, but there was base allocation left. The availability statuses of both the parent/main and child/ derived rate plans were not updated. This warning is equivalent to 7013 (used for unlinked rate plans). 
7027 | Sell rate must be within Rate Verification threshold. The rate plans (listed) with the violation and their Main/Derived rate plans (not listed) are not updated. | An active rate plan linkage rule exists between the rate plan provided in the request and a parent/main rate plan. The sell rate update we received was outside of the rate verification threshold defined for either the parent/main rate plan, or the child/ derived rate plan. The sell rates of both the parent/main and child/ derived rate plans were not updated. This warning is equivalent to 7021 (used for unlinked rate plans). 

### SSL Certificate Validation Problems

EQC partners developing in Java should use version 1.4.2_13 or more recent to avoid issues with certificate signing authorities. If EQC partners prefer to keep using an older version of Java or in-house SSL libraries, they need to import the Entrust CA certificate.
- Entrust CA common name CN = Entrust.net Certification Authority (2048).
- To manually import the Entrust.net Certification Authority (2048) Certificate, download the CA certificate at https://www.entrust.net/downloads/root_request.cfm.

### Miscellaneous

Like any technology, it is possible in some very rare situations that Expedia QuickConnect will fail to process a message and generate a comprehensive AR RS. In such a situation, where the data returned by Expedia QuickConnect is not proper XML, the EQC partner should retry the message. Even though this situation should never happen, the EQC partner should be ready to handle it.


