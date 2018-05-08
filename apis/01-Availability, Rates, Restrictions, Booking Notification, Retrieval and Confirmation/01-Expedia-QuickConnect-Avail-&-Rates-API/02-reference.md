# API Definition

## Overview

## Communication Protocol

The communication protocol between properties and Expedia QuickConnect consists of HTTPS (HTTP Secure) transactions with embedded XML documents. Note the following:

- Only HTTPS posts to Expedia’s secure server are supported. Using HTTP will not work (Expedia QuickConnect servers are not configured to accept posts over unsecured HTTP).
- Communication is synchronous -- on the same socket, Expedia QuickConnect reads the request and issues a positive or negative response, depending on whether Expedia QuickConnect is able to process the request or not.
- Content-Type of the HTTP Request Header should be: “text/xml”.
- Only connecting via our URLs is supported. Expedia does not support connection directly via IP Address, as this address is subject to change without notice. If the EQC partner generally prefers IP Addresses for communication performance reasons, it may consider implementing an address caching strategy to reduce DNS lookups for the URLs. Additionally, if partner whitelists outbound connections, it must do so using a URL pattern rather than an IP range, as Expedia cannot guarantee a specific IP range / subnet.

<a name="/schemaguidelines"></a>
## Schema Design Guidelines

The design of Expedia QuickConnect schemas is based on the following general guidelines:  

- Elements and attributes naming convention: Elements follow the upper camel case (UCC) convention while attributes follow the lower camel case (LCC) convention.  
- Information is found mostly in attributes, elements are only used to structure the information and make logical groupings.  
- If an element contains text, it is most likely because the text was entered manually.  
- Validation rules about data type, data format and data size/length are included in the schema and should be considered during development.  
- Expedia uses namespaces to version its schemas. Messages sent to Expedia QuickConnect should always contain the proper namespace.  
- Namespaces are also used for versioning: EQC partners should be careful when specifying a namespace in the messages they send to Expedia QuickConnect.  
- Boolean will be returned as “true” or “false”. 


## Availability and Rates Request (AR RQ)
### AR RQ Schema Complete Definition

_Legend: O = Optional_

Level | Element / @Attribute | Data Type | Number of occur. | Description | EQC validations
----- | -------------------- | --------- | ---------------- | ----------- | ---------------
0 | AvailRateUpdateRQ |  | 1 |  DC common message header structure
0 | @xmlns | URL | 1 | Namespace which belongs to this message. Also used to validate version of schema on which this message is based. Supported namespaces for AR messages are: http://www.expediaconnect.com/EQC/AR/2007/02 & http://www.expediaconnect.com/EQC/AR/2011/06 | Valid namespace, defined by at least one version of AR schema.
1 | Authentication | - | | Grouping of required information to grant access to Expedia QuickConnect interface.
1 | @username | String | | Username for Expedia QuickConnect login (case sensitive), provided by Expedia. | Minimum length: 4, Maximum length: 30, Username exists, User is allowed to access Expedia QuickConnect
1 | @password | String | | Password for Expedia QuickConnect login (case sensitive), provided by Expedia. | Minimum length: 6, Maximum length: 30, Password fits with the username
1 | Hotel | - | | Information about Hotel |
1 | @id | Integer | | Hotel ID defined by Expedia and uniquely identifying a property in Expedia system. | Positive integer of 14 digits or less, Hotel ID is valid, Hotel ID in Expedia system is assigned to the credentials provided in Authentication node.
1 | AvailRateUpdate | - | | Grouping of updates for one or more room type(s) and rate plan(s), for one or a list of date ranges. | This element can be omitted in the request and everything brought back one level down if EQC partner doesn’t see a need to send multiple updates within same message, but also to insure backward compatibility.
2 | DateRange | - | | Specify dates on which availability and rate information provided in this message applies. A 'from' and a 'to' date must be specified. They can be equal if the EQC partner wants to update only one date. This element can be repeated more than once if EQC partner wants to update non-consecutive dates or date ranges. | “from date” <= “to date”
2 | @from | Date | | Start date of the interval (format: yyyy-mm-dd) | “From date” >= today – 1
2 | @to | Date | | End date of the interval (format: yyyy-mm-dd).  | “To date” <= (today + 2yrs + 1 day)
**Note:** The following 7 attributes are used to indicate on which day of the week, from the date range specified in the “from”-“to” attributes, the updates should be applied. If none of the 7 attributes are specified, updates will apply to all days of the week. As soon as one attribute is specified, the updates will only apply to the days where the attribute value is set to true.
If EQC partners need to use this feature, Expedia recommends specifying all seven attributes, indicating on which day the updates should be applied (attribute value=true) and on which day the updates should not be applied (attribute value=false) 

Level | Element / @Attribute | Data Type | Number of occur. | Description | EQC validations
----- | -------------------- | --------- | ---------------- | ----------- | ---------------
2 | @sun | Boolean | O | If set to true, apply update for each Sunday in the specified date interval. |
2 | @mon | Boolean | O | If set to true, apply update for each Monday in the specified date interval. |
2 | @tue | Boolean | O | If set to true, apply update for each Tuesday in the specified date interval. |
2 | @wed | Boolean | O | If set to true, apply update for each Wednesday in the specified date interval. |
2 | @thu | Boolean | O | If set to true, apply update for each Thursday in the specified date interval. |
2 | @fri | Boolean | O | If set to true, apply update for each Friday in the specified date interval. |
2 | @sat | Boolean | O | If set to true, apply update for each Saturday in the specified date interval. |
2 | RoomType | - | | Room type to be updated by this message. This element can be repeated more than once if hotel wants to update multiple room types for the same set of dates. |
2 | @id | String | | Room type ID (defined by Expedia). Note: mapping of Expedia IDs to hotel codes has to be done by EQC partner. | String of 50 characters or less, RoomType ID is valid only if the specified hotel has the corresponding room type defined for it in Expedia Partner Central, Room type should be active. If it isn’t, the update will still work but a warning will be returned to indicate that this room type is inactive. 
2 | @closed | Boolean | O | If true, the room type is no longer available on Expedia for dates specified in the request. If false, the room type is reopened if previously closed, or stays open if already open. | A close request will be refused if the room is enabled for base allocation and has base allocation left.
3 | Inventory | - | | Number of rooms being made available for sale on Expedia. **Note:** only applies to a single room type. | Either flexibleAllocation or totalInventoryAvailable, but not both, must be sent in any message for an availability update.
3 | @flexibleAllocation | Integer | O | In the case of a hotel using base allocation: number of additional rooms available for this room type, for each day specified by "DateRange" element. | Minimum value: 0, Maximum value: 4999
3 | @totalInventoryAvailable | Integer | O | Total number of rooms made available via Expedia for this room type, for each day specified by “DateRange” element, inclusive of base and flexible allocation. **Note:** In the case of a hotel using base allocation: if the value sent is lower than the current base allocation room count, it will be rejected with warning code 7013. | Minimum value: 0, Maximum value: 4999
3 | RatePlan | - | O | Information about a rate plan. This element can be repeated more than once if hotel wants to update multiple rate plans under this room type for the same set of dates. **Note:** Rate plans can only belong to a single room type and Rate plans should be active. If it isn’t, the update will still work but a warning will be returned to indicate that this rate plan is inactive. |
3 | @id | String | | Rate plan ID (assigned by Expedia). Unique throughout Expedia system, so the same rate plan ID cannot be associated to more than one room type. | String of 50 characters or less, RatePlan ID is valid only if the specified hotel has the corresponding rate plan defined for it in Expedia Partner Central. 
3 | @closed | Boolean | O | If true, the rate plan is no longer available on Expedia for dates specified under the room type. If false, the rate plan is reopened if previously closed, or stays open if already open. | For hotels with base allocation contracts, if this is the last rate plan opened,  and a close request is received, it will be rejected with warning code 7014.
4 | Rate | - | O | Room rates. The type of rate (sell, net, LAR) to include depends on the configuration for this hotel.  Up to 28 rate elements can be specified when updating per day and per length of stay rates. Else only one rate element should be defined. | Use only one pricing model, Pricing model in synch with configuration of the property on Expedia Partner Central or via the PARR interface
4 | @rateChangeIndicator | Boolean | O | To be used with rate plans enabled for day-of-arrival pricing only (message will be rejected if this attribute is used in conjunction with a rate plan that is not enabled for DOA pricing). Indicates that regardless of the applicable rate on the arrival date, the rate will change on this date. | Rate plan is day-of-arrival pricing enabled. It not, update is rejected with error code 3125.
4 | @currency | String | O | 3-letter currency code assigned to property on Expedia Partner Central. Based on ISO 4217 specification. Optional only when message is for rate change indicator updates, without any rate modification. | Minimum length  = 3, Must match the property currency defined on Expedia Partner Central, Must always be provided when update request includes rates.
4 | @lengthOfStay | Int | O | For properties managing per day and per length of stay rates. Rates from length of stay 1 up to 28 can be defined by arrival date. This attribute contains the length of stay for which this rate applies. | Minimum value=1, Maximum value=28
5 | PerDay | - | O | To be used when property is configured with Per Day pricing on Expedia Partner Central. This rate is for base occupancy of the room. | Property is configured with per day pricing on Expedia Partner Central.
5 | @rate | Decimal | | Rate for base occupancy, per day. When used in conjunction with length of stay pricing attribute, the rate defined is the rate per day. If the rate is for 100$ for LOS=7, then the total of this stay’s rate would be 7x100=700. | Rate has to be >= 0.000, Rate has to be <= 16 digits long
5 | @rate | Decimal | | Rate for one person, based on double occupancy. Single person supplement and additional person fees are configured when rate plan is created and cannot be modified in this interface. | Rate has to be >= 0.000, Rate has to be <= 16 digits long
4 | Restrictions | - | O | |
4 | @minLOS | Integer | O | Specifies minimum length of stay required to qualify for this rate. Guest has to stay at least this number of nights to benefit from rate.  If not specified, the minimum length of stay will be set to a default value configured in Expedia systems (1 is the default, but it can be changed). Refer to section 5.6.4 for more details on MinLOS and MaxLOS | Minimum value: 1, Maximum value: 28, Attempting to set MinLOS greater than Expedia’s configured value for the hotel will result in the update being refused with error code 3135.
4 | @maxLOS | Integer | O | Specifies maximum length of stay allowed for this product and day. If not specified, the maximum length of stay value will be set to a default value configured in Expedia systems (28 is the default, but it can be changed). Refer to section 5.6.4 for more details on MinLOS and MaxLOS. |
4 | @closedToArrival | Boolean | O | Designates the rate plan as unavailable for check-in by customers. A customer’s stay must start on an earlier or later date in order to access this rate plan. |
4 | @closedToDeparture | Boolean | O | Designates the rate plan as unavailable for check-out by customers. A customer’s stay must end on an earlier or later date in order to access this rate plan. |


## Availability and Rate Response (AR RS)
The availability and rate response message is straightforward: it is returned synchronously to update the property’s system with the status of the AR request. The status can either be Success or Error. If successful, it can contain a warning.
### AR RS Schema Overview

![AR RS XML MESSAGE](/images/AR RS XML MESSAGE.jpg)

### AR RS Schema Complete Definition

_Legend: O = Optional_

Level | Element / @Attribute | Data Type | Number of occur. | Description
----- | -------------------- | --------- | ---------------- | -----------
0 | AvailRateUpdateRS | NA | | Root element
0 | @xmlns | URL | | Namespace which belongs to this message. Also used to validate version of schema on which this message is based. Namespace for AR RS messages http://www.expediaconnect.com/EQC/AR/2007/02
1 | Success | NA | | Element appears if the request to update availability and rates was successful. The node will be empty. If this element is present, Error node(s) does not appear. Not everything from the request was successfully processed if warnings are also returned.
2 | Warning | String | | Detailed description of a warning. When this element appears, anything that is not listed in the warning list is assumed to be successfully processed by Expedia and updated in Expedia system. Occurrence of this element means the update request was partially applied. For example, if one of the rates provided violates our rate verification threshold, that specific rate will not be applied, but the rest of the updates will. Maximum length: 1024 characters. Maximum number of occurrences per warning code: 20. All warning scenarios are described in section [AR Response Business Warnings](guides.html#/arresponsebusinesswarnings)
2 | @code | Int | | Code for the warning. Code will be between 7,000 and 8,000.
1 | Error | String | | Detailed description of an error message. When this element appears, nothing included in the AR request was processed. One or more of this element appears if the request failed. If this element is present, Success node does not appear. Maximum length: 1024 characters. All error scenarios are described in the [Troubleshooting](guides.html#/troubleshooting) section.
1 | @code | Int | | Code for this error, for example: authentication, xml structure, business validation. Code will be between 1000 and 7000.
