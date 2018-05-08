# Sample Messages
This section contains sample messages illustrating how to interact with the Avail and Rates API.

### Update a Room Type’s Flexible Allocation
Expedia stores number of rooms available by room type, so any update to flexible allocations must be attributed to a specific room type. The following is a sample AR request message to update flexible allocation for a single room type every day of the month of August 2012. It sets flexible allocation available for Expedia to sell at 10.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating flexible allocation for every day of the month of August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3546"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-31"/>
        <RoomType id="558945">
            <Inventory flexibleAllocation="10"/>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
Assuming the update for the flexible allocation wouldn’t be the same throughout the month, another way to send the update would be to bundle AvailRateUpdate elements into one request. The following is a sample AR request message to set flexible allocation made available for Expedia to sell at 10 for August 1st to August 15th, and 20 for August 16th to August 31st.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating inventory with 1 value for the first half of August 2012, and another value for the 2nd half-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3546"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-15"/>
        <RoomType id="558945">
            <Inventory flexibleAllocation="10"/>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-16" to="2012-08-31"/>
        <RoomType id="558945">
            <Inventory flexibleAllocation="20"/>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
**Note**: Flexible allocation represents total room allocation (for properties without a base allocation contract) OR additional room allocation (for properties with a base allocation contract) – refer to the Terminology section (2.3) for a complete definition of flexible allocation. To update total room allocation inclusive of base allocation, see example below.

### Update a Room Type’s Total Allocation
Room allocation updates may reflect the total number of rooms made available to Expedia for a given room type, inclusive of any contractual base allocation. The following is a sample AR request message to update the total allocation for a single room type. The “totalInventoryAvailable” attribute includes any remaining base allocation as well as additional, flexible allocation.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating total allocation of a room type-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username=" testuser" password=" testpass "/>
    <Hotel id="526101"/>
    <AvailRateUpdate>
        <DateRange from="2012-09-26" to="2012-09-26"/>
        <RoomType id="16818">
            <Inventory totalInventoryAvailable="3"/>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
If different values for totalInventoryAvailable for different dates need to be communicated at the same time, the following sample AR message can be used.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating total allocation of a room type with different values on different days-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username=" testuser" password=" testpass "/>
    <Hotel id="526101"/>
    <AvailRateUpdate>
        <DateRange from="2012-09-26" to="2012-09-26"/>
        <RoomType id="16818">
            <Inventory totalInventoryAvailable="3"/>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-09-27" to="2012-09-27"/>
        <RoomType id="16818">
            <Inventory totalInventoryAvailable="2"/>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-09-28" to="2012-09-28"/>
        <RoomType id="16818">
            <Inventory totalInventoryAvailable="1"/>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
If different room types need to be updated for the same date, the following sample AR message can be used.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating total alloctions for different room types-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username=" testuser " password=" testpass "/>
    <Hotel id="526101"/>
    <AvailRateUpdate>
        <DateRange from="2012-09-26" to="2012-09-26"/>
        <RoomType id="16818">
            <Inventory totalInventoryAvailable="3"/>
        </RoomType>
        <RoomType id="43223">
            <Inventory totalInventoryAvailable="4"/>
        </RoomType>
        <RoomType id="65223">
            <Inventory totalInventoryAvailable="2"/>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
**Notes**:
- If there is unsold base allocation remaining for the specified room type and totalInventoryAvailable is set lower than this current base amount, Expedia QuickConnect will reject the update and warning code 7013 will be returned.
- If a property does not have a base allocation contract with Expedia, there is no functional difference between specifying an update as “flexibleAllocation” or “totalInventoryAvailable”.

### Update Rates in a Rate Plan 
The following is a sample AR request message to update the daily rate for a rate plan for the month of August 2012 (for a property using the occupancy-based pricing model).
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating rates for 1 room type and 1 rate plan every day of the month of August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3547"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-31"/>
        <RoomType id="558025">
            <RatePlan id="556895" closed="false">
                <Rate currency="EUR">
                    <PerOccupancy rate="60.00" occupancy="1"/>
                    <PerOccupancy rate="100.00" occupancy="2"/>
                    <PerOccupancy rate="135.00" occupancy="3"/>
                    <PerOccupancy rate="160.00" occupancy="4"/>
                </Rate>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
The following is a sample AR request message to update the daily rate for a rate plan for 5 inconsecutive dates in August 2012 (for a property using the occupancy-based pricing model).
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating rates for 1 room type and 1 rate plan for 5 inconsecutive dates in August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3547"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-01"/>
        <DateRange from="2012-08-03" to="2012-08-03"/>
        <DateRange from="2012-08-12" to="2012-08-12"/>
        <DateRange from="2012-08-18" to="2012-08-18"/>
        <DateRange from="2012-08-22" to="2012-08-22"/>
        <RoomType id="558025">
            <RatePlan id="556895" closed="false">
                <Rate currency="EUR">
                    <PerOccupancy rate="60.00" occupancy="1"/>
                    <PerOccupancy rate="100.00" occupancy="2"/>
                    <PerOccupancy rate="135.00" occupancy="3"/>
                    <PerOccupancy rate="160.00" occupancy="4"/>
                </Rate>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
### Update a Rate Plan’s Restrictions
The following is a sample AR request message to update minimum and maximum length of stay restriction for a rate plan for the month of August 2012.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating minimum and maximum length of stay restriction for 1 Room Type and 1 Rate Plan for the month of August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3546"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-31"/>
        <RoomType id="558945">
            <RatePlan id="556887" closed="false">
                <Restrictions minLOS="2" maxLOS="14"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
In this sample, the AR request message sets 4 rate plans to be closed to departure on December 31st, 2012.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: set closed to departure restriction to 4 Rate Plans on December 31, 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="35499"/>
    <AvailRateUpdate>
        <DateRange from="2012-12-31" to="2012-12-31"/>
        <RoomType id="9989">
            <RatePlan id="556895">
                <Restrictions closedToDeparture="true"/>
            </RatePlan>
            <RatePlan id="434534">
                <Restrictions closedToDeparture="true"/>
            </RatePlan>
        </RoomType>
        <RoomType id="87655">
            <RatePlan id="543334">
                <Restrictions closedToDeparture="true"/>
            </RatePlan>
            <RatePlan id="224454">
                <Restrictions closedToDeparture="true"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
In this sample, the AR request message sets a rate plan to be closed to arrival on December 25th, 2012, and opened to arrival on December 26th.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: set closed and opened to arrival restriction to a Rate Plan-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3546"/>
    <AvailRateUpdate>
        <DateRange from="2012-12-25" to="2012-12-25"/>
        <RoomType id="558945">
            <RatePlan id="556895 ">
                <Restrictions closedToArrival="true"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-12-26" to="2012-12-26"/>
        <RoomType id="558945">
            <RatePlan id="556895 ">
                <Restrictions closedToArrival="false"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
### Update a Room Type’s Weekend Allocation and Per-Occupancy Rate Plan’s Restrictions and Rates 
The following is a sample AR request message to update a room type’s allocation and its rates and restrictions for a rate plan on a per-occupancy pricing model for the weekends of August 2012. Occupancy-based rates are always specified as a rate for the total occupancy specified. 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating allocation, rates and restriction for 1 room type and 1 rate plan on a per-occupancy pricing model for the weekends of August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="35499"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-31" sun="true" mon="false" tue="false" wed="false" thu="false" fri="true" sat="true"/>
        <RoomType id="9989" closed="false">
            <Inventory totalInventoryAvailable="10"/>
            <RatePlan id="556895" closed="false">
                <Rate currency="EUR">
                    <PerOccupancy rate="60.00" occupancy="1"/>
                    <PerOccupancy rate="100.00" occupancy="2"/>
                    <PerOccupancy rate="135.00" occupancy="3"/>
                    <PerOccupancy rate="160.00" occupancy="4"/>
                </Rate>
                <Restrictions minLOS="2" maxLOS="14" closedToArrival="false" closedToDeparture="true"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
So, based on the rates in the example above, a room booked on Sunday for 4 people represents a total rate of $160. 
### Update a Room Type’s Weekend Allocation and Per-Day Rate Plan’s Rates and Restrictions 
The following is a sample AR request message to update room allocation and per-day rate plan rates and restrictions for 2 room types for the weekends of August 2012.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message: updating allocation, rates and restriction for 2 room types and 2 per-day rate plans for the weekends of August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="35499"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-31" sun="true" mon="false" tue="false" wed="false" thu="false" fri="true" sat="true"/>
        <RoomType id="9989" closed="false">
            <Inventory totalInventoryAvailable="50"/>
            <RatePlan id="556895" closed="false">
                <Rate currency="EUR">
                    <PerDay rate="55.00"/>
                </Rate>
                <Restrictions minLOS="2" maxLOS="14" closedToArrival="false" closedToDeparture="false"/>
            </RatePlan>
        </RoomType>
        <RoomType id="87655" closed="false">
            <Inventory totalInventoryAvailable="40"/>
            <RatePlan id="543334" closed="false">
                <Rate currency="EUR">
                    <PerDay rate="59.00"/>
                </Rate>
                <Restrictions minLOS="2" maxLOS="7" closedToArrival="true" closedToDeparture="false"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```

### Update Rates and Rate Change Indicator for Day of Arrival pricing-enabled rate plans
The following is a sample AR request message to request a rate change on specific dates, along with a rate update, for 2 different rate plans under one room type.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message:updating rate change indicator and rate values for 2 rate plans, in August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="4223"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-02"/>
        <RoomType id="32241">
            <RatePlan id="23123">
                <Rate currency="EUR" rateChangeIndicator="true">
                    <PerDay rate="75.00"/>
                </Rate>
            </RatePlan>
            <RatePlan id="52333">
                <Rate currency="EUR" rateChangeIndicator="true">
                    <PerDay rate="65.00"/>
                </Rate>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-07" to="2012-08-09"/>
        <RoomType id="32241">
            <RatePlan id="23123">
                <Rate currency="EUR" rateChangeIndicator="true">
                    <PerDay rate="71.00"/>
                </Rate>
            </RatePlan>
            <RatePlan id="52333">
                <Rate currency="EUR" rateChangeIndicator="true">
                    <PerDay rate="61.00"/>
                </Rate>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
### Closing a rate plan
The following is a sample AR message to close out a rate plan for the month of August:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- Sample AR request message: closing rate plan for the month of August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3546"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-31"/>
        <RoomType id="558875">
            <RatePlan id="556895" closed="true"/>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
### Closing a room type
The following is a sample AR message to close out a room type for the month of August:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- Sample AR request message: closing room type for the month of August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3546"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-31"/>
        <RoomType id="558875" closed="true"/>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
### Applying different types of updates to different date ranges and products
The following is a sample AR message demonstrating how many different updates can now be bundled into one single AR request. The following updates are made with this request: modification of room allocation for a few days, modification of rates for another set of dates, modifications of restrictions for different inconsecutive dates, modification of room type status for another set of days (overlapping with dates already specified for the modification of rates).
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- Sample AR request message: contains many different updates-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="3546"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-04"/>
        <RoomType id="558875">
            <Inventory totalInventoryAvailable="10"/>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-05" to="2012-08-09"/>
        <RoomType id="558875">
            <RatePlan id="556895">
                <Rate currency="EUR">
                    <PerOccupancy occupancy="1" rate="80.50"/>
                    <PerOccupancy occupancy="2" rate="101.50"/>
                    <PerOccupancy occupancy="3" rate="140"/>
                    <PerOccupancy occupancy="4" rate="160"/>
                </Rate>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-10" to="2012-08-11"/>
        <DateRange from="2012-08-15" to="2012-08-16"/>
        <DateRange from="2012-08-20" to="2012-08-20"/>
        <RoomType id="558875">
            <RatePlan id="556895">
                <Restrictions closedToArrival="false" closedToDeparture="false" minLOS="2" maxLOS="7"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-20"/>
        <RoomType id="558875" closed="false">
            <RatePlan id="556895" closed="false"/>
            <RatePlan id="644891" closed="false"/>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
### Send rate updates for per day length of stay pricing
The following is a sample request meant to update per-day rates per length of stay. 28 rates (maximum allowed by Expedia interface) will be defined for LOS 1 to 28, for each arrival date contained in the date range.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message:updating rates and restrictions, triggering warnings in the response, for August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="4223"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-05"/>
        <RoomType id="32241">
            <RatePlan id="23123">
                <Rate lengthOfStay="1" currency="EUR">
                    <PerDay rate="100.00"/>
                </Rate>
                <Rate lengthOfStay="2" currency="EUR">
                    <PerDay rate="100.00"/>
                </Rate>
                <Rate lengthOfStay="3" currency="EUR">
                    <PerDay rate="100.00"/>
                </Rate>
                <Rate lengthOfStay="4" currency="EUR">
                    <PerDay rate="100.00"/>
                </Rate>
                <Rate lengthOfStay="5" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="6" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="7" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="8" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="9" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="10" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="11" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="12" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="13" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="14" currency="EUR">
                    <PerDay rate="95.00"/>
                </Rate>
                <Rate lengthOfStay="15" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="16" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="17" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="18" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="19" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="20" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="21" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="22" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="23" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="24" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="25" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="26" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="27" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Rate lengthOfStay="28" currency="EUR">
                    <PerDay rate="90.00"/>
                </Rate>
                <Restrictions closedToArrival="false" closedToDeparture="true"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
A subsequent update for this product and dates could only be meant to update LOS 1 to 4 for example:
```xml
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="4223"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-05"/>
        <RoomType id="32241">
            <RatePlan id="23123">
                <Rate lengthOfStay="1" currency="EUR">
                    <PerDay rate="111.00"/>
                </Rate>
                <Rate lengthOfStay="2" currency="EUR">
                    <PerDay rate="111.00"/>
                </Rate>
                <Rate lengthOfStay="3" currency="EUR">
                    <PerDay rate="111.00"/>
                </Rate>
                <Rate lengthOfStay="4" currency="EUR">
                    <PerDay rate="120.00"/>
                </Rate>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>   
</AvailRateUpdateRQ>
```
After this message is processed, LOS 1 to 4 would have new rate values, and previously defined LOS 5 to 28 would remain with the values originally defined.
### AR Response: Success
The following is a sample success response message.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Success message for AR update request-->
<AvailRateUpdateRS xmlns="http://www.expediaconnect.com/EQC/AR/2007/02">
	<Success/>
</AvailRateUpdateRS>
```
### AR Response: Success with warning
The following is a sample success request/response pair with a list of warnings returned in the response due to the AR RQ containing rates that violate our Rate Verification logic.
Note that in this example, the restriction and rate change indicator updates would be successfully applied, and the rate updates where rates equal 71 and 61 EUR respectively would also be successful. Only the rates equal to 0.00 EUR are ignored/refused/not processed.

**Request**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample AR request message:updating rates and restrictions, triggering warnings in the response, for August 2012-->
<AvailRateUpdateRQ xmlns="http://www.expediaconnect.com/EQC/AR/2011/06">
    <Authentication username="testuser" password="testpass"/>
    <Hotel id="4223"/>
    <AvailRateUpdate>
        <DateRange from="2012-08-01" to="2012-08-05"/>
        <RoomType id="32241">
            <RatePlan id="23123">
                <Rate currency="EUR">
                    <PerDay rate="0.00"/>
                </Rate>
                <Restrictions closedToArrival="false" closedToDeparture="true"/>
            </RatePlan>
            <RatePlan id="52333">
                <Rate currency="EUR">
                    <PerDay rate="0.00"/>
                </Rate>
                <Restrictions closedToArrival="false" closedToDeparture="true"/>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
    <AvailRateUpdate>
        <DateRange from="2012-08-06" to="2012-08-15"/>
        <RoomType id="32241">
            <RatePlan id="23123">
                <Rate currency="EUR" rateChangeIndicator="true">
                    <PerDay rate="71.00"/>
                </Rate>
            </RatePlan>
            <RatePlan id="52333">
                <Rate currency="EUR" rateChangeIndicator="true">
                    <PerDay rate="61.00"/>
                </Rate>
            </RatePlan>
        </RoomType>
    </AvailRateUpdate>
</AvailRateUpdateRQ>
```
**Response**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--AR Success message with warning due to rates failing our Rate Verification logic-->
<AvailRateUpdateRS xmlns="http://www.expediaconnect.com/EQC/AR/2007/02">
    <Success>
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 23123; Stay Date 2012-08-01] One or more costs provided are out of threshold for an identified room type.</Warning>
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 23123; Stay Date 2012-08-02] One or more costs provided are out of threshold for an identified room type.</Warning>
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 23123; Stay Date 2012-08-03] One or more costs provided are out of threshold for an identified room type.</Warning>
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 23123; Stay Date 2012-08-04] One or more costs provided are out of threshold for an identified room type.</Warning>
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 23123; Stay Date 2012-08-05] One or more costs provided are out of threshold for an identified room type.</Warning>
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 52333; Stay Date 2012-08-01] One or more costs provided are out of threshold for an identified room type.</Warning>                                                       
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 52333; Stay Date 2012-08-02] One or more costs provided are out of threshold for an identified room type.</Warning>                                                       
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 52333; Stay Date 2012-08-03] One or more costs provided are out of threshold for an identified room type.</Warning>                                                       
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 52333; Stay Date 2012-08-04] One or more costs provided are out of threshold for an identified room type.</Warning>
           <Warning code="7013">[Room Type ID 32241; Rate Plan ID 52333; Stay Date 2012-08-05] One or more costs provided are out of threshold for an identified room type.</Warning>
    </Success>
</AvailRateUpdateRS>
```
Please note that when AR requests generate more than 20 different warnings per warning code, EQC AR RS will only contain the first 20.
### AR Response: Errors
The following is a sample error response message: in this case the property made mistakes in its room type ID and its rate plan ID in the AR request message.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Sample error message, in case the property either made a mistake while mapping -->
<AvailRateUpdateRS xmlns="http://www.expediaconnect.com/EQC/AR/2007/02" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<Error code="3203">The following RoomTypeIDs do not belong to the given hotel: 432432.</Error>
	<Error code="3204">The following RatePlanIDs do not belong to the given hotel: 234543.</Error>
</AvailRateUpdateRS>
```
