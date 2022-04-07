NTFS Timezones and Daylight saving
=================

## Handling time-zones

The `network_timezone` field in file networks.txt sets the time zone for all `stop_times` of `trips` of the `network`. 

If a feed covers multiple time zones, a specific time zone information can be specified on a `stop_point` outside the `network_timezone` in the `stop_timezone` field. Set this optional field in file `stops.txt` (for stops with location_type = 0). If not set, the timezone of the network is used. Ensure that trip travel times remain accurate and consistent by providing departure and arrival times based on the `network_timezone` for stops that are out of that timezone.

Navitia presents time for departure and arrival times in the local time zone of each stop. \
Warnings: 
- Time in `stop_times.txt` should always increase along a trip. 
- NTFS uses the local timezone at noon of the travel day of the trip. This is the implementation of the constraint specified on the (GTFS Time data format)[http://gtfs.org/schedule/reference/#field-types]:
> Time in the HH:MM:SS format (H:MM:SS is also accepted). The time is measured from "noon minus 12h" of the service day (effectively midnight except for days on which daylight savings time changes occur). For times occurring after midnight, enter the time as a value greater than 24:00:00 in HH:MM:SS local time for the day on which the trip schedule begins. \
Example: 14:30:00 for 2:30PM or 25:35:00 for 1:35AM on the next day.


## Daylight Saving Time
Some trips may operate during the switch from or to Daylight Saving time and then continue to operate as normal. The NTFS format uses the same way of dealing with daylight saving than [the GTFS does](https://support.google.com/transitpartners/answer/7074707).
It is strongly recommended to explicitly define trips operating across time changes (not using multiple day calendar).

### First modelization (recommanded)

Model the trip using the previous day as the point of reference in stop_times.txt. The departure stop_time may exceed 24h to indicate the time on the next day (for example 25:30 to indicate 01:30 before the changing time).
Make the departure times consistent with vehicle travel time. 

### Second modelization

Model the trip considering the time change is already effective at the first stop_time of the trip. This modelization is not recommended because a decrease of the stop_time (when clock is going backward) way result in a negative time value. As a negative time value is not allowed, using the First modelization in those cases is necessary. 

### Displayed times

When displaying a time in a journey or at a specific stop, Navitia gives the local time at the stop using its specified time-zone AND the Daylight Saving Time.

**Example with the clock switching forward**

A bus starts on October 2 at 1:50 AM the night the switch happens (for example, 2 AM changes to 3 AM). 

stop_sequence | Departure_times (1st model) | Departure_times (2nd model) | displayed departure time in Navitia
--- | --- | --- | ---
1 | 25:50:00 (October 1) | 02:50:00 (October 2) | 01:50 AM (October 2)
2 | 26:10:00 (October 1) | 03:10:00 (October 2) | 03:10 AM (October 2) *
3 | 26:50:00 (October 1) | 03:50:00 (October 2) | 03:50 AM (October 2)
4 | 27:30:00 (October 1) | 04:30:00 (October 2) | 04:30 AM (October 2)

_\* Time starting here use the new time display_

**Example with the clock switching backward**

A bus starts on October 2 at 1:50 AM the night the switch happens (for example, 3 AM changes to 2 AM). The trip is modeled as belonging to the previous day (October 1):

stop_sequence | Departure_times (1st model) | Departure_times (2nd model) | displayed departure time in Navitia
--- | --- | --- | ---
1 | 25:50:00 (October 1) | 00:50:00 (October 2) | 01:50 AM (October 2)
2 | 26:10:00 (October 1) | 01:10:00 (October 2) | 02:10 AM (October 2) 
3 | 26:50:00 (October 1) | 01:50:00 (October 2) | 01:50 AM (October 2) *
4 | 27:30:00 (October 1) | 02:30:00 (October 2) | 02:30 AM (October 2)

_\* Time starting here use the new time display_
