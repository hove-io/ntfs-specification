NTFS version 0.12
=================

# Introduction

NTFS is Kisio Digital's data exchange format : Navitia Transit Feed Specification. It aims to replace the CSV/Fusio format by addressing its weaknesses and managing all types of data in a single format (schedules, ODT, etc.).

This new data format is strongly based on the GTFS data format (https://developers.google.com/transit/gtfs/reference?hl=en-US), with some enhancements allowing a more exhaustive description of the data. As such, the data format is constantly evolving (see [changelog](./ntfs_changelog_fr.md)).

# Data format

Data must be compliant with the following rules :

* All files are compressed into one **ZIP** file
* File encoding : all files are **UTF-8** encoded
* Each file is a **CSV** file with a header : http://tools.ietf.org/html/rfc4180
* The field separator is the **","**
* The coordinate system is [**WGS84**](https://www.wikiwand.com/en/World_Geodetic_System)
* The decimal separator is the **"."**
* The date format is **YYYYMMDD**
* The time format is **HH:MM:SS**
* The datetime format is ISO-8601 in UTC ; it must include the time offset or the `Z`
* The **URL** format must be compliant with [**W3C** recommandations](http://www.w3.org/Addressing/URL/4_URI_Recommentations.html)
* TimeZone reference http://en.wikipedia.org/wiki/List_of_tz_zones
* Languages codes must follow the [**ISO639-2** standard](http://www.loc.gov/standards/iso639-2/php/code_list.php)
* Colors are encoded as Hexadecimal RGB characters (for example **00FFFF**)
* Geometries are described using the format [**WKT**](http://fr.wikipedia.org/wiki/Well-known_text)
* Objects' identifiers must not contain the object's type, as it will be added directly in Navitia's API.

# List of the format files
## Special files
These files are used to describe the origin of the data in the case of a merge of multiple sources of data. A _contributor_ represents a source of data (an operator may require the use of several contributors). A _dataset_ represents a set of data provided by a contributor.
The file [`trips.txt`](#tripstxt-required) refers to _dataset_id_ in order to link a trip to its source of data.

File | Constraint | Description
--- | --- | ---
[`contributors.txt`](#contributorstxt-required) | Required | This file contains the contributors.
[`datasets.txt`](#datasetstxt-required) | Required | This file contains the sets of data of a contributor.

## Core files
File | Constraint | Description
--- | --- | ---
[`feed_infos.txt`](#feed_infostxt-required) | Required | This file contains additional informations on the data such as its validity period, its provider, etc.
[`networks.txt`](#networkstxt-required) | Required | This file contains the description of the networks.
[`commercial_modes.txt`](#commercial_modestxt-required) | Required | This file contains the commercial modes (NAViTiA 1 Mode).
[`companies.txt`](#companiestxt-required) | Required | This file contains the companies.
[`lines.txt`](#linestxt-required) | Required | This file contains the lines.
[`physical_modes.txt`](#physical_modestxt-required) | Required | This file contains the physical modes (NAViTiA 1 ModeType).
[`routes.txt`](#routestxt-required) | Required | This file contains the routes.
[`stop_times.txt`](#stop_timestxt-required) | Required | This file contains the scheduled times.
[`stops.txt`](#stopstxt-required) | Required | This file contains the stops, entrances/exits and pathways nodes.
[`trips.txt`](#tripstxt-required) | Required | This file contains the trips.
[`calendar.txt`](#calendartxt-required) | Required | This file contains the days of operation.
[`calendar_dates.txt`](#calendar_datestxt-optional) | Optional | This file contains the exceptions on the operating days described in the file [`calendar.txt`](#calendartxt-required).
[`comments.txt`](#commentstxt-optional) | Optional | This file contains the comments.
[`comment_links.txt`](#comment_linkstxt-optional) | Optional | This file contains the links between each comment and the related objects.

## Additional files (excluding calendars per period)
File | Constraint | Description
--- | --- | ---
[`frequencies.txt`](#frequenciestxt-optional) | Optional | This file contains the frequencies properties.
[`equipments.txt`](#equipmentstxt-optional)  | Optional | This file contains the properties (as accessibility) of the stops and the connections.
[`transfers.txt`](#transferstxt-optional) | Optional | This file contains the connections.
[`trip_properties.txt`](#trip_propertiestxt-optional) | Optional | This file contains the accessibility of the trips.
[`geometries.txt`](#geometriestxt-optional) | Optional | This file contains the spatial representation of the geometries in the [Well Known Text (WKT)](https://www.wikiwand.com/en/Well-known_text_representation_of_geometry) format. Those geometries are referenced in the following files [`lines.txt`](#linestxt-required), [`routes.txt`](#routestxt-required), [`trips.txt`](#tripstxt-required).
[`object_properties.txt`](#object_propertiestxt-optional) | Optional | This file contains the list of additional properties on any object of the model.
[`object_codes.txt`](#object_codestxt-optional) | Optional | This file contains the list of additional identification codes for the objects of the model.
[`admin_stations.txt`](#admin_stationstxt-optional) | Optional | This file contains the list of administrative stops for journeys to or from a town.
[`line_groups.txt`](#line_groupstxt-optional) | Optional | This file contains the list of groups of lines.
[`line_group_links.txt`](#line_group_linkstxt-optional) | Optional | This file contains the links between a group of lines and the lines part of it.
[`pathways.txt`](#pathwaystxt-optional) | Optional | This file contains the list of pathways within a stop area. (à compléter)
[`levels.txt`](#levelstxt-optional) | Optional | This file contains the list of levels within a stop area.
[`addresses.txt`](#addressestxt-optional) | Optional | This file contains the list of stops' addresses.

## Calendars files per period
File | Constraint | Description
--- | --- | ---
[`grid_calendars.txt`](#grid_calendarstxt-optional) | Optional |  This file contains the operating days of the calendars.
[`grid_exception_dates.txt`](#grid_exception_datestxt-optional) | Optional | This file contains the exceptions on the operating days of the calendars.
[`grid_periods.txt`](#grid_periodstxt-optional) | Optional | This file contains the calendar periods.
[`grid_rel_calendar_line.txt`](#grid_rel_calendar_linetxt-optional) | Optional | This file contains the links between lines and calendars.

## Visualisation 
For a better understanding, you can find a diagram of the relationships between each file here : https://dbdiagram.io/embed/5e218b4a9e76504e0ef05fcd ([edit the diagram](https://dbdiagram.io/d/5e218b4a9e76504e0ef05fcd))

# Files description
### networks.txt (required)
This file contains the description of the networks.

Column | Type | Constraint | Note
--- | --- | --- | ---
network_id | String | Required | Unique and lasting identifier of the network.
network_name | String | Required | Name of the network.
network_url | String | Optional | Hyperlink to the institutional website.
network_timezone | String | Optional |
network_lang | String | Optional |
network_phone | String | Optional | Contact phone number.
network_address | String | Optional | Postal address of the network.
network_sort_order | Integer | Optional | Sort order of the networks. Those with smaller values are displayed first.

### calendar.txt (required)
This file described the dates when service is available for one or more routes. 

Column | Type | Constraint | Note
--- | --- | --- | ---
service_id | String | Required | Unique identifier of a set of dates.
monday | Integer | Required | (1)
tuesday | Integer | Required | (1)
wednesday | Integer | Required | (1)
thursday | Integer | Required | (1)
friday | Integer | Required | (1)
saturday | Integer | Required | (1)
sunday | Integer | Required | (1)
start_date | Date | Required |  Start date of the circulation (included).
end_date | Date | Required | End date of the circulation (included).

(1) Valid options are :

* 0 - Service is not available on this day
* 1 - Service is available on this day

### calendar_dates.txt (optional)
This file contains the exceptions on the operating days described in the file [`calendar.txt`](#calendartxt-required). For specific circulations, it is possible to set a calendar using the file [`calendar_dates.txt`](#calendar_datestxt-optional) only. As a result, the header `service_id` will not appear in the file [`calendar.txt`](#calendartxt-required).

Column | Type | Constraint | Note
--- | --- | --- | ---
service_id | String | Required | Unique identifier of a set of dates.
date | Date | Required | Exception date.
exception_type | Integer | Required | (1)

(1) Possible options are :

* 1 - Service is added on this date
* 2 - Service is deleted on this date

### comments.txt (optional)

Column | Type | Constraint | Note
--- | --- | --- | ---
comment_id | String | Required | Identifier of the comment.
comment_type | String | Optional | (1)
comment_label | String | Optional | Cross-reference character to the comment. If not specified, it will be generated automatically.
comment_name | String | Required | Text of the comment.
comment_url | String | Optional | URL giving more information about the note such as a link to the description page of the ODT service.

(1) Comment's categories differentiating them on display. Valid options are :

* information (or empty field) : indicates a general information note
* on_demand_transport : indicates an information note about an On Demand Transportation service. This note must specify the reservation's conditions and telephone number.

### comment_links.txt (optional)
This file links an object (line, stop, scheduled time, etc.) to a comment and associates multiple notes with an object and multiple objects with a note.

Column | Type | Constraint | Note
--- | --- | --- | ---
object_id | String | Required | Identifier of the object associated with the comment.
object_type | String | Required | Type of object associated with the comment. The possible options are : stop_area, stop_point, line, route, trip, stop_time or line_group.
comment_id | String | Required | Identifier of the comment (link to the file [`comments.txt`](#commentstxt-optional)).

### commercial_modes.txt (required)
This file describes the commercial modes, that is, a specific wording of a transport mode. For example, BusWay is a special name for BRT (Bus Rapid Transit) in Nantes.

Column | Type | Constraint | Note
--- | --- | --- | ---
commercial_mode_id | String | Required | Identifier of the commercial mode.
commercial_mode_name | String | Required | Name of the commercial mode. 

### companies.txt (required)
This file describes the transport operator running all or part of one of the networks contained in the data.

Column | Type | Constraint | Note
--- | --- | --- | ---
company_id | String | Required | Identifier of the company.
company_name | String | Required | Name of the company.
company_address | String | Optional | Full address of the company.
company_url | String | Optional | URL of the company's institutional website. Should not be confused with the link to the network's website.
company_mail | String | Optional | Contact email address of the company.
company_phone | String | Optional | Contact phone number.

### contributors.txt (required)
This file is used to identify the source(s) providing the data of the set of datas.

Column | Type | Constraint | Note
--- | --- | --- | ---
contributor_id | String | Required | Identifier of the contributor.
contributor_name | String | Required | Name of the contributor.
contributor_license | String | Optional | Data use license of the contributor.
contributor_website | String | Optional | URL of the data provider's website.

### datasets.txt (required)
This file lists the sets of data of a contributor.

Column | Type | Constraint | Note
--- | --- | --- | ---
dataset_id | String | Required | Identifier of the set of data.
contributor_id | String | Required | Identifier of the contributor (link to the file [`contributors.txt`](#contributorstxt-required)).
dataset_start_date | Date | Required | Start date of consideration of the set of data (may be different from the validity start date of the initial export).
dataset_end_date | Date | Required | End date of consideration of the set of data (may be different from the validity end date of the initial export).
dataset_type | Integer (1) | Optional | Type of data which represents the data's freshness.
dataset_extrapolation | Integer | Optional | Indicates whether the service's data has been extrapolated (field set to 1) or not (field set to 0).
dataset_desc | String | Optional | Note which indicates the contents of the set of data.
dataset_system | String | Optional | Name of the system that generated the data or name of the data format.

(1) Specifies the data type :

* 0 - These are theoretical data
* 1 - These are strike or revised data
* 2 - These are D-Day production data ?

### frequencies.txt (optional)
Column | Type | Constraint | Note
--- | --- | --- | ---
trip_id | String | Required | Identifier of the trip.
start_time | Time | Required | Frequency start time.
end_time | Time | Required | Frequency end time. Specify 26:00:00 for 2 a.m. of the day in question.
headway_secs | Integer | Required | Departure frequency in seconds.

### lines.txt (required)
Column | Type | Constraint | Note
--- | --- | --- | ---
line_id | String | Required | Identifier of the commercial line.
line_code | String | Optional | Code of the commercial line.
line_name | String | Required | Name of the commercial line.
forward_line_name | String | Optional | Name of the line in the outward journey.
backward_line_name | String | Optional | Name of the line in the return journey.
line_color | Color | Optional | Color of the line.
line_text_color | Color | Optional | Color of the line's code.
line_sort_order | Integer | Optional | Sort key for the line within the network. The smallest values are displayed first.
network_id | String | Required | Identifier of the line's network (link to the file [`networks.txt`](#networkstxt-required)).
commercial_mode_id | String | Required | Identifier of the commercial mode (link to the file [`commercial_modes.txt`](#commercial_modestxt-required)).
geometry_id | String | Optional | Identifier of a geospatial shape representing the line (link to the file [`geometries.txt`](#geometriestxt-optional)).
line_opening_time | Time | Optional | Start time of the line's service (regardless of the type of day or period). If this information is not provided, it will be recalculated.
line_closing_time | Time | Optional | End time of the line's service (regardless of the type of day or period). If this information is not provided, it will be recalculated. Specify an hour higher than 24 to indicate a time on the next day.

### routes.txt (required)
Column | Type | Constraint | Note
--- | --- | --- | ---
route_id | String | Required | Identifier of the route.
route_name | String | Required | Name of the route.
direction_type | String (1) | Optional | Description of the direction of the route. This is a free text field however, it is recommended to use an item in the list below.
line_id | String | Required | Identifier of the commercial line (link to the file [`lines.txt`](#linestxt-required)).
geometry_id | String | Optional | Identifier of a geospatial shape for the route (link to the file [`geometries.txt`](#geometriestxt-optional)).
destination_id | String | Optional | Identifier of the main destination (see stop area in the file [`stops.txt`](#stopstxt-required)).

(1) List of recommended values for the field _direction_type_ :

* For outward and return journey : _forward_ et _backward_
* For loop routes : _clockwise_ et _anticlockwise_
* For inbound and outbound routes : _inbound_ et _outbound_

### physical_modes.txt (required)
Column | Type | Constraint | Note
--- | --- | --- | ---
physical_mode_id | String | Required | Identifier of the physical mode. Choosing from the list below is mandatory.
physical_mode_name | String | Required | Name of the physical mode.
co2_emission | Decimal | Optional | CO2 emission rate of the physical mode per passenger per km.

**List of available physical modes :**

There is a hierarchy of modes to classify stop areas by their highest level mode (cf. norme ? [NeTEx (in french)](http://www.normes-donnees-tc.org/wp-content/uploads/2014/05/NF_Profil_NeTEx_pour_les_arrets-_F-_-_v2.pdf) , chapitre 6.2.3)

    1. Aérien
    2. Maritime/Fluvial
    3. Ferré
    4. Métro
    5. Tram
    6. Funiculaire/Câble
    7. Bus/Car/Trolley

The list below shows the NeTEx hierarchy of each mode (without the prefix of the object type returned by the API) :

Physical mode code | Physical mode name | NeTEx Hierarchy
--- | --- | ---
Air | Avion | 1
Boat | Navette maritime/fluviale | 2
Bus | Bus | 7
BusRapidTransit | Bus à haut niveau de service | 7
Coach | Autocar | 7
Ferry | Ferry | 2
Funicular | Funiculaire | 6
LocalTrain | Train régional / TER | 3
LongDistanceTrain | Train grande vitesse | 3
Metro | Métro | 4
RapidTransit | Train de banlieue / RER | 3
RailShuttle | Navette ferrée (VAL) | 3
Shuttle | Navette | 7
SuspendedCableCar | Téléphérique / télécabine | 6
Taxi | Taxi | 7
Train | Train | 3
Tramway | Tramway | 5

The list of physical modes below completes the previous one with the possible feeder modes in an itinerary in order to associate them with a CO2 rate in the export.

Physical mode code | Physical mode name
--- | ---
BikeSharingService | Vélo en libre service
Bike | Vélo
Car | Voiture

### equipments.txt (optional)
Column | Type | Constraint | Note
--- | --- | --- | ---
equipment_id | String | Required | Identifier of the equipment.
wheelchair_boarding | Integer (1) | Optional | Access for passengers in wheelchair.
sheltered | Integer (1) | Optional | Covered shelter.
elevator | Integer (1) | Optional | Elevator/Lift.
escalator | Integer (1) | Optional | Escalator.
bike_accepted | Integer (1) | Optional | Bicycle boarding.
bike_depot | Integer (1) | Optional | Bicycle parking.
visual_announcement | Integer (1) | Optional | Visual announcement.
audible_announcement | Integer (1) | Optional | Audible announcement.
appropriate_escort | Integer (1) | Optional | Escort at the stop.
appropriate_signage | Integer (1) | Optional | Appropriate signage at the stop.

    (1) Valid options are :
        0 (or empty) - No information available
        1 - The equipment is available
        2 - The equipment is not available

### stops.txt (required)
A line in this file represents a location or an area where a vehicle drops off or picks up passengers.

Column | Type | Constraint | Note
--- | --- | --- | ---
stop_id | String | Required | Identifier of the stop.
visible | Integer | Optional | Indicates whether the stop can be displayed in autocomplete (value 1) or ignored (value 0).
stop_name | String | Required | Name of the stop.
stop_code | String | Optional | Code of the stop known to the passenger or code/number of the entrance/exit for entrances/exits.
stop_lat | Decimal | Required (Special) | Latitude of the location. This field is mandatory except for generic nodes (`location_type = 4`) and boarding areas (`location_type = 5`).
stop_lon | Decimal | Required (Special) | Longitude of the location. This field is mandatory except for generic nodes (`location_type = 4`) and boarding areas (`location_type = 5`).
fare_zone_id | String | Optional | Fare zone of the stop. This field only applies to stop points (`location_type = 0`).
location_type | Integer (1) | Required | Type of the location.
geometry_id | Geometry | Optional | This field is linked to the file [`geometries.txt`](#geometriestxt-optional) that describes the geometry of a stop zone (`location_type = 2`) to let the engine define the addresses for an "address to address" zonal ODT. This field can also be used to specify a geometry for stop areas (`location_type = 1`) and municipalities (`location_type = 4`) to enrich the web service.
parent_station | String | Optional | Identifier of the stop area. Must not be filled in for stop areas (`location_type = 1`) and stop zones (`location_type = 2`).
stop_timezone | Timezone | Optional | Time zone of the location (refer to http://en.wikipedia.org/wiki/List_of_tz_zones). This field only concerns stop points (`location_type = 0`). The scheduled time of the stop uses the timezone of the trip's network, not the timezone of the stop even if they are different.
equipment_id | String | Optional | Identifier of the equipment.
level_id | String | Optional | Link to a level described in the file [`levels.txt`](#levelstxt-optional).
platform_code | String | Optional | Identifier of a stop's plateform (for example `G` or `3`). This field only concerns stop points (`location_type = 0`) or boarding areas (`location_type = 5`).
address_id | String | Optional | Identifier of the stop's address (link to the file [`addresses.txt`]). This field only applies to stop points (`location_type = 0`).

    (1) Type of the location :
        0 (or empty) - Stop point
        1 - Stop area
        2 - Stop zone (for the "address to address" zonal ODT)
        3 - Entrance/Exit
        4 - Pathways Interconnection Node
        5 - Boarding area (for example to indicate "middle of the platform")


### stop_times.txt (required)
Column | Type | Constraint | Note
--- | --- | --- | ---
stop_time_id | String | Optional | Unique identifier of the scheduled time in a dataset. This information is not sustainable and only links a scheduled time (file [`stop_times.txt`](#stop_timestxt-required)) to a comment (file [`comments.txt`](#commentstxt-optional)) using the file [`comment_links.txt`](#comment_linkstxt-optional). If this field is not provided, the scheduled time will not be linked to a comment.
trip_id | String | Required | Identifier of the trip.
arrival_time | Time | Required | Arrival time. If the arrival time is unknown, it must be estimated by the system providing the data and the field *stop_time_precision* must be specified at 1. If alighting is forbidden at this stop, the arrival time must be indicated and the field *drop_off_type* must be specified at 1.
departure_time | Time | Required | Departure time. If the departure time is unknown, it must be estimated by the system providing the data and the field *stop_time_precision* must be specified at 1. If boarding is forbidden at this stop, the departure time must be indicated and the field *pickup_type* must be specified at 1.
boarding_duration | Integer | Optional | Time required for boarding in seconds (train, plane, ferry, etc.). This value is necessarily positive or equal to zero.
alighting_duration | Integer | Optional | Time required for alighting in seconds (train, plane, ferry, etc.). This value is necessarily positive or equal to zero.
stop_id | String | Required | Identifier of the stop point (general case). This field can also reference a stop zone (stop type 2) or a municipality (stop type 4) in the case of zonal ODT.
stop_sequence | Integer | Required | Order of stops scheduled for a trip. This value must be necessarily positive or equal to zero and must increase along the trip.
stop_headsign | String | Optional | Wording to be displayed to the passenger instead of `trip_headsign` at that stop.
trip_short_name_at_stop | String | Optional | Name to be displayed to the passenger instead of `trip_short_name` at that stop.
pickup_type | Integer (1) | Optional | Indication on the scheduled time (from the gtfs file).
drop_off_type | Integer (1) | Optional | Indication on the scheduled time (from the gtfs file).
local_zone_id  | Integer | Optional | Identifier of the boarding restriction area of the scheduled time.
stop_time_precision | Integer (2) | Optional | Indicates whether the scheduled time is exact or approximative.

    (1)  Valid options are :
        0 (default entry) - Continuous pickup or drop off
        1 - Boarding or alighting forbidden i.e. no pickup or drop off available
        2 - Scheduled time on reservation for an ODT service. If a message is associated with an ODT, see its link with the file comment_links.txt
        3 - The vehicle does not stop (only passing through) ; in this case, both pickup_type and drop_off_type must be equal to 3

    (2) Reliability can take the following values :
        0 - The scheduled time is expected to be precise
        1 - The scheduled time is approximative; corresponding to the case timepoint = 0 in the GTFS, e.g. a schedule which is regular but not associated with ? un horaire régulier mais qui n'est pas associé à un arrêt de régulation.?
        ??? The time at a stop for a bus that is trying to regulate its delays
        2 - The scheduled time is not guaranteed; corresponding to approximated or interpolated times e.g. the estimated time of an ODT
        Not specified :
            if the location is a stop area (location_type = 2), the scheduled time is not guaranteed
            else, the scheduled time is considered exact

### transfers.txt (optional)
Column | Type | Constraint | Note
--- | --- | --- | ---
from_stop_id | String | Required | Identifier of the stop where the connection begins (link to the file [`stops.txt`](#stopstxt-required)).
to_stop_id | String | Required | Identifier of the stop where the connection ends (link to the file [`stops.txt`](#stopstxt-required)).
min_transfer_time | Integer | Optional | Minimum duration of the transfer in seconds. This value corresponds to the walking time that will be displayed to the passenger. If the value is not specified, the system calculates a minimum time based on the Manhattan distance between the two stops. The resulting time has a minimum value of 60 seconds. Note : The value provided could be lower than 60 (e.g. 0 in the case of an ensured connection).
real_min_transfer_time | Integer | Optional | Real duration of the transfer in seconds. This value corresponds to the walking time (min_transfer_time) to which a tolerance time (minimum time of transfer) is added. If the value is not specified, the system uses the min_transfer_time plus a default parameter equivalent to 120 seconds generally. The resulting time will therefore be greater than or equal to 120 seconds. The input value cannot be less than min_transfer_time but can be equal to it.
equipment_id | String | Optional | Identifier of the accessibility properties (link to the file [`equipments.txt`](#equipmentstxt-optional)).

### trip_properties.txt (optional)
Column | Type | Constraint | Note
--- | --- | --- | ---
trip_property_id | String | Required | Identifier of the property.
wheelchair_accessible | Integer (1) | Optional | The vehicule is accessible to passengers in wheelchair.
bike_accepted | Integer (1) | Optional | The vehicle allows the boarding of bicycle.
air_conditioned | Integer (1) | Optional | The vehicle has air conditioning.
visual_announcement | Integer (1) | Optional | The vehicle has visual announcements.
audible_announcement | Integer (1) | Optional | The vehicle has audible announcements.
appropriate_escort | Integer (1) | Optional | An on-board escort service is possible (on boarding and alighting).
appropriate_signage | Integer (1) | Optional | The display on board is clear and suitable for people with mental disabilities.
school_vehicle_type | Integer (2) | Optional | Type of school transport.

    (1) Valid options are :
        0 (or empty) - No information available
        1 - The equipment is available
        2 - The equipment is not available

    (2) Type of school transport :
        0 (or empty)  : regular transport (non-school one)
        1 - School transport exclusively
        2 - Mixed transport (school and regular one)

### trips.txt (required)
Column | Type | Constraint | Note
--- | --- | --- | ---
route_id | String | Required | Identifier of the route (link to the file [`routes.txt`](#routestxt-required)).
service_id | String | Required | Identifier of the operating days.
trip_id | String | Required | Identifier of the trip.
trip_headsign | String | Optional | Text displayed to the passenger on the vehicle (e.g. the destination of a bus or a rapid transit mission code).
trip_short_name | String | Optional | Name of the trip known by the passenger making it uniquely identifiable over the day (usually a train number).
block_id | String | Optional | Identifier of the service extension.
company_id | String | Required | Identifier of the company (link to the file [`companies.txt`](#companiestxt-required)).
physical_mode_id | String | Required | Identifier of the physical mode (link to the file [`physical_modes.txt`](#physical_modestxt-required)).
trip_property_id | String | Optional | Identifier of the accessibility property (link to the file [`trip_properties.txt`](#trip_propertiestxt-optional)).
dataset_id | String | Required | Identifier of the set of data that provided the trip (link to the file [`datasets.txt`](#datasetstxt-required)).
geometry_id | String | Optional | Identifier of a geospatial shape representing the trip (link to the file [`geometries.txt`](#geometriestxt-optional)).
journey_pattern_id | String | Optional | Identifier of the mission (i.e. an ordered sequence of stops with the same properties and sometimes known to the passenger).

    To specify whether part or the whole trip is on reservation, it is necessary to :
        Indicate at the scheduled time (file stop_times.txt) if the boarding and/or alighting is on reservation
        Specify an ODT comment (optional) via the comments.txt and comment_links.txt files

### geometries.txt (optional)
This file contains the spatial representation of a geometry (for lines, routes and/or trips). Each line in the file represents a complete geometry of the object.

Column | Type | Constraint | Note
--- | --- | --- | ---
geometry_id | String | Required | Identifier of the geometry.
geometry_wkt | Geometry | Required | Spatial representation of the geometry according to the [Well Known Text (WKT)](https://www.wikiwand.com/en/Well-known_text_representation_of_geometry) standard.

    Lines and routes can only be represented as LINESTRING or MULTILINESTRING.
    Trips can only be represented as LINESTRING. If a MULTILINESTRING is specified, only the first LINESTRING will be used.
    Stop points are represented as POINT.
    Stop areas can be POINT, POLYGON or MULTIPOLYGON.
    Stop zones are either POLYGON or MULTIPOLYGON.
    
    Only the specified types of geometry are considered, the other types are ignored.
    The format of the file is deliberately simple, an evolution can be considered if necessary.

### object_properties.txt (optional)
This file contains the list of additional properties on the different object of the model.
These properties are presented in the form of a list of keys/values that need to be standardized by process.
A key can only be used once (with a single value) for the same object.

Column | Type | Constraint | Note
--- | --- | --- | ---
object_type | String | Required | Type of the object the property is about (line, route, trip, stop_area, stop_point).
object_id | String | Required | Identifier of the object the property is about.
object_property_name | String | Required | Name of the additional property (free text field).
object_property_value | String | Required | Value of the additional property (free text field).

### object_codes.txt (optional)
This file contains the list of additional identification codes in the external systems of the different the objects in the model (?).
These properties are presented in the form of a list of keys/values that need to be standardized by process.
A key can be used multiple times (with different values) for the same object.

Column | Type | Constraint | Note
--- | --- | --- | ---
object_type | String | Required | Type of the object the property is about (company, network, line, route, trip, stop_area, stop_point).
object_id | String | Required | Identifier of the object the property is about.
object_system | String | Required | Name of the object identification system (free text field). For example : "Timeo" or "UIC" for stops, "Reflex" for lines.
object_code | String | Required | Identification code of the object in the system in question.

Hove provides in this file :
* the object identifiers in the old Navitia system for the objects "network", "line", "route", "trip", "stop_point" and "stop_area" with the object_system the string **"navitia1"**.
* the identifiers of the objects declared in the input file (NTFS or GTFS for example) with the object_system the string **"source"**.

### admin_stations.txt (optional)
Column | Type | Constraint | Note
--- | --- | --- | ---
admin_id | String | Required | Identifier of the municipality (or district) as returned by the Navitia API.
admin_name | String | Required | Name of the municipality (or district).
stop_id | String | Required | Identifier of the stop area used to represent the municipality (link to the file [`stops.txt`](#stopstxt-required)). Type 1 stop mandatory.
stop_name | String | Optional | Name of the stop area (to aid the readability of the file).

### pathways.txt (optional)
Note : This file describes a modeling of the station that is not necessarily geographical and can also be simplified. Modeling the pathways of a stop zone cannot be partial. Once a pathway is filled in for a stop zone, the entire station is considered filled in.
Attention, ce fichier décrit une modélisation de la station qui n'est pas nécessairement géographique, et peut également être simplifiée.

Column | Type | Constraint | Note
--- | --- | --- | ---
pathway_id | String | Required | Identifier of the pathway. 
from_stop_id | String | Required | Identifier of the start node of the pathway in the [`stops.txt`](#stopstxt-required) file. This start node can be a stop point, an entrance/exit, a generic node or a boarding area.
to_stop_id | String | Required | Identifier of the end node of the pathway (same constraints as `from_stop_id`).
pathway_mode | Integer (1) | Required | Type of pathway. See below for valid values.
is_bidirectional | Boolean | Required | Indicates whether the path can be used in both directions or only in the from->to direction.
length | Decimal | Optional | Distance in meters between the two endpoints of the path.
traversal_time | Integer | Optional | Average travel time in seconds.
stair_count | Integer | Optional | Number of stairs (estimated).
max_slope | Decimal | Optional | Maximum slope ratio on the pathway.
min_width | Decimal | Optional | Minimum width of the pathway.
signposted_as | String | Optional | String of text which informs the travelers about the pathway.
reversed_signposted_as | String | Optional | String of text which informs the travelers in the opposite direction (if the pathway is specified as bidirectional).

    (1) pathway_mode - Valid values are :
        1 - Walkway
        2 - Stair
        3 - Moving sidewalk / travelator
        4 - Escalator
        5 - Elevator
        6 - Fare gate
        7 - Exit gate


### levels.txt (optional)
Column | Type | Constraint | Note
--- | --- | --- | ---
level_id | String | Required | Identifier of the level.
level_index | Decimal | Required | Numeric index of the level. The ground floor has index 0 and levels below ground are indicated by negative indices.
level_name | String | Optional | Name of the level (for example, "Mezzanine").

### addresses.txt (optional)
Column | Type | Constraint | Note
--- | --- | --- | ---
address_id | String | Required | Identifier of the address.
street_name | String | Required | Name of the street.
house_number | String | Optional | Number of the building.

### line_groups.txt (optional)
Column | Type | Constraint | Note
--- | --- | --- | ---
line_group_id | String | Required | Identifier of the group of lines.
line_group_name | String | Required | Name of the group of lines.
main_line_id | String | Required | Identifier of the main line of the group of lines (link to the file [`lines.txt`](#linestxt-required))

A comment can be associated with a group of lines in the files [`comments.txt`](#commentstxt-optional) and [`comment_links.txt`](#comment_linkstxt-optional) .

### line_group_links.txt (optional)
Column | Type | Constraint | Note
--- | --- | --- | ---
line_group_id | String | Required | Identifier of the group of lines.
line_id | String | Required | Identifier of the line part of the group of lines (link to the file [`lines.txt`](#linestxt-required)). Note : A line can be part of several groups of lines.

### feed_infos.txt (required)
This file contains information about the dataset and the system that generated it. For ease of use, the structure of the file is generic and the list of information is listed below.

#### Description of the file format
Column | Type | Constraint | Note
--- | --- | --- | ---
feed_info_param | String | Required | Name of the parameter.
feed_info_value | String | Required | Value of the parameter.

#### Description of the contents of the file

This file contains 3 types of parameters :
* mandatory parameters
* recommended parameters (optional)
* free parameters (possible to add as many parameters as desired).

The table below lists the required and recommended parameters.

Column | Type | Constraint | Note
--- | --- | --- | ---
ntfs_version | String | Required | Version of the NTFS format used in the export file (for example, "0.3").
feed_start_date | Date | Optional | Validity start date of the dataset.
feed_end_date | Date | Optional | Validity end date of the dataset.
feed_creation_date |  Date |  Optional | Date (UTC) of creation of the dataset.
feed_creation_time | Time | Optional | Time (UTC) of creation of the dataset..
feed_creation_datetime | Datetime | Optional | Date and time (UTC) of creation of the dataset.

The table below shows the free parameters entered by Hove (depends on the tool that generates the data).

Column | Type | Constraint | Note
--- | --- | --- | ---
feed_publisher_name | String | Free | Name of the organization that provides the dataset.
feed_license | String | Free | Data license of the model.
feed_license_url | String | Free | URL associated with the data license.
fusio_url | String | Free | URL of the system that generated the dataset.
fusio_version | String | Free | Version of the system that generated the dataset.
tartare_platform | String | Free | Tag indicating the platform that generated the data.
tartare_coverage_id | String | Free | Identifier of the Tartare coverage that generated the dataset (1).
tartare_contributor_id | String | Free | Identifier of the Tartare contributor that generated the dataset (1).

    (1) Only one of the following fields 'tartare_coverage_id' and 'tartare_contributor_id' will be present. They are used to track the source of the data in Tartare in order to facilitate diagnosis.

### grid_calendars.txt (optional)
This file contains the calendars.

Column | Type | Constraint | Note
--- | --- | --- | ---
grid_calendar_id | String | Required | Identifier of the calendar.
name | String | Required | Name of the calendar.
monday | Integer | Required | 0 : Service is not available on this day. <br> 1 : Service is available on this day.
tuesday | Integer | Required | 0 : Service is not available on this day. <br> 1 : Service is available on this day.
wednesday | Integer | Required | 0 : Service is not available on this day. <br> 1 : Service is available on this day.
thursday | Integer | Required | 0 : Service is not available on this day. <br> 1 : Service is available on this day.
friday | Integer | Required | 0 : Service is not available on this day. <br> 1 : Service is available on this day.
saturday | Integer | Required | 0 : Service is not available on this day. <br> 1 : Service is available on this day.
sunday | Integer | Required | 0 : Service is not available on this day. <br> 1 : Service is available on this day.

### grid_exception_dates.txt (optional)
This file contains the exceptions on the calendars of the schedules.
Column | Type | Constraint | Note
--- | --- | --- | ---
grid_calendar_id | String | Required | Identifier of the calendar of the schedules.
date | Date | Required | Exception date.
type | Integer | Required | 0 : Service is not available on this day. <br> 1 : Service is available on this day.

### grid_periods.txt (optional)
This file contains the periods of the calendars of the schedules.

Column | Type | Constraint | Note
--- | --- | --- | ---
grid_calendar_id | String | Required | Identifier of the calendar of the schedules.
start_date | Date | Required | Start date.
end_date | Date | Required | End date.

### grid_rel_calendar_line.txt (optional)
This file contains all the links between the lines and the calendars of the schedules.

Column | Type | Constraint | Note
--- | --- | --- | ---
grid_calendar_id | String | Required | Identifier of the calendar of the schedules.
line_id | String | Required | Identifier of the line associated with this calendar (link to the file [`lines.txt`](#linestxt-required)). This field can be empty if the line_external_code field is filled.
line_external_code | String | Required | This column contains the external code NAViTiA 1 of the line (link to the file [`lines.txt`](#linestxt-required)). This field can be empty if the line_id field is filled.

# Évolutions possibles du format
Ce chapitre liste des évolutions du format qui peuvent être intéressantes si elles sont utiles concrètement.
## Modification du champ service_id et du nom du fichier [`calendar.txt`](#calendartxt-required)
L'identifiant d'un calendrier ne suit pas la même convention que les autres identifiants (même s'il est cohérent avec le GTFS). Une évolution possible serait de changer le libellé du champ pour indiquer "calendar_id".
De plus, le nom du fichier pourra être changé en `calendars.txt` pour améliorer la cohérence.

## Gestion des données perturbées / de grèves
Afin de limiter la complexité du format, la gestion des données de grève sera effectuée par plusieurs exports :

1. un export contenant toutes les données théoriques du référentiel. L'export NTFS est un export classique, et dont la clé "revised_networks" du fichier [`feed_infos.txt`](#feed_infostxt-required) est vide ou non renseignée.
2. un ou plusieurs exports NTFS de grèves, dont chaque export fournit toutes les données (impactées par la grève ou non) :
    * de un ou plusieurs réseaux spécifiés par la clé **revised_networks** du fichier [`feed_infos.txt`](#feed_infostxt-required)
    * pour des données valides entre les dates spécifiées par **feed_start_date** et **feed_end_date**

Un champ complémentaire et optionnel "base_trip_id" est à prévoir dans le fichier [`trips.txt`](#tripstxt-required) afin de permettre d'associer la circulation théorique et la circulation adaptée (en cas de données de grève par exemple).

## Gestion avancée des géométries (tracés des lignes, parcours et circulations)
Afin de ne pas complexifier inutilement le format NTFS et les outils qui vont le manipuler, le fichier [`geometries.txt`](#geometriestxt-optional) indique un tracé complet pour une géométrie, comme une ligne en fourche ou une ligne à tiroir.  Afin de pouvoir afficher le tracé réel des bus dans la feuille de route (ie. n'avoir que la portion utilisée de la ligne), un découpage de cette géométrie est réalisé dans Navitia de manière automatique.
Si le besoin d'affiner cette gestion est validé, une évolution du format du fichier [`geometries.txt`](#geometriestxt-optional) peut être envisagé de la manière suivante (à confirmer) :
* Une ligne représentera un segment de la ligne/parcours/circulation entre deux points d'arrêts consécutifs (et de manière orientée ?)
* La précision des points d'arrêts d'origine et de destination du segment est faite par l'ajout de deux colonnes optionnelles

## Gestion de l'émission de CO2 par ligne ou véhicule
L'émission de CO2 est gérée en France par mode de transport. Les valeurs sont spécifiées par l'ADEME. Le format NTFS permet d'échanger cette information au niveau du mode physique. En fonction des besoins rencontrés à l'avenir, un ajout de cette informaion au niveau de la ligne et/ou du véhicule est envisagée. Dans ce cas, un système de "surcharge" de l'inforamtion sera à mettre en place pour prendre en compte par priorité :

1. le taux au niveau du véhicule s'il est disponible
2. le taux au niveau de la ligne s'il est disponible
3. le taux au niveau du mode s'il est disponible
4. une indication sur le fait que la valeur est inconnue

## Gestion d'un service (ex : Acces+)
Ajouter la notion de "service" pour un accompagnement sur un réseau de transport, comme par exemple :
* Accès+ TER ou Accès+ TGV sur le réseau SNCF
* OptiBus de Keolis PMR Rhone d'accompagnement sur le réseau de TCL

Ce service permet d'ajouter de l'accessibilité (UFR, Cognitif ou autre) sur un ou plusieurs objets ou directement dans le calcul.

# Exemples de modélisation de TAD
Voici quelques exemples de modélisation de TAD dans les fichiers NTFS. Seuls les fichiers impactés sont représentés ([`stops.txt`](#stopstxt-required) et [`stop_times.txt`](#stop_timestxt-required)).
Il est à également à noter qu'il est possible :
* de faire du rabattement vers horaire en spécifiant bien un horaire à un point d'arrivé (et pas une zone) dans le ficheir stop_time,
* de faire de la fréquence sur du TAD zonal en utilisant le fichier [`frequencies.txt`](#frequenciestxt-optional)

**Attention, les coordonnées et les surfaces des zones ne sont pas cohérentes, c'est l'architecture des données dans les fichiers qui est importante ici.**

Pour résumé, il suffit de connaitre les points suivants:
* Les horaires sont portés par des « stop » (un stop est une ligne issue du fichier stop)
* Les stop peuvent être
    * Des stop_points (type=0)
    * Des stop_area (type=1)
    * Des area (type=2): représentant une surface géographique (commune, quartier, zone...)
* Et ensuite, on peut accrocher des horaires sur ces stops, quelque soient leur type
    * dans les faits, on accrochera des horaires sur les stop_points et les area, mais pas sur les stop_areas: les impacts sur les différentes utilisations (itinéraire, fiche horaire) seraient non maitrisés

Cette modélisation unique traite l'ensemble des possibilité:
* TAD zonal d'adresse à adresse
* TAD zonal d'arrêts à arrêts
* TAD de rabattement d'adresse à arrêts
* TAD de rabattement d'arrêts à arrêts
* TAD "multiple" Zones - Arrêts - Zones, Arrêts - Zones - Arrêts, etc...

### Modélisation NTFS

* Les lignes intégralement TAD, sans horaire, sont déclarée en "fréquence"
* Les stop_point du fichier [`stops.txt`](#stopstxt-required) de type "shape", ne devraient pas être en correspondance avec d'autres stop_point.
    * A l'intégration des données, les correspondances éventuellement déclarées seront ignorées
* Les correspondances entre 2 circulations ne sont autorisées QUE si un des 2 horaires est fixe
    * Les correspondances entre 2 horaires estimés sont interdites (stop.estimated vers stop.estimated)
* Des lignes peuvent traiter de zones d'adresse à adresse
    * Les itinéraires adresse à adresse au sein de ces zones ne seront pas proposés
* les lignes TAD ne sont plus typées
    * Ce sont les stop_times qui détermine le type de TAD
* Par exemple:
    * Gestion du TAD zonal adresse à adresse et arrêt à arrêt en horaire estimé
        * tous les horaires sont estimés
            * adresse à adresse=depuis un stop_point de type shape vers un stop_point de type shape
                * donc pas en correspondance parce que les hsape ne sotn pas en correspondance
            * arrêt à arrêt
                * correspondance proposée avec des lignes à horaires fixes
    * Gestion des correspondances entre TAD de rabattement, la même règle est appliquée
        * si l'horaire de rabattement est fixe
            * la correspondance sera proposée
        * si l'horaire de rabattement est estimé
            * la correspondance avec un horaire fixe (train) est possible
            * la correspondance avec un autre TAD à rabattement estimé ne sera pas possible


### Exemples
#### Ligne mixte
Le cas de la zone 2 ci-dessous est trivial: on définit cette zone à l'aide d'un shape qui englobe les adresses ayant accès au TAD. Il suffit ensuite d'associer un horaire à ce shape.

![Exemple de ligne mixte](NTFS_image1.png)

Les zones de dessertes ne remontent pas en autocompletion: elles permettent de déterminer l'offre uniquement.
On peut alimenter le fichier [`stop_times.txt`](#stop_timestxt-required) en mettant des horaires précis sur chacun des points d'arrêt pouvant appartenir à des zones d'arrêts différentes, avec des informations ITL manuelles:

**Fichier stop: déclare les "arrêts"**

stop_id | stop_name | stop_lat | stop_lon | location_type | geometry_id | parent_station
--- | --- | --- | --- | --- | --- | ---
stop_point_A | A | 47.01 | 1.01 | 0 |  | stop_area_A
stop_point_B | A | 47.01 | 1.01 | 0 |  | stop_area_B
stop_point_C | A | 47.01 | 1.01 | 0 |  | stop_area_C
stop_point_D | A | 47.01 | 1.01 | 0 |  | stop_area_D
stop_point_E | A | 47.01 | 1.01 | 0 |  | stop_area_E
stop_point_H | A | 47.01 | 1.01 | 0 |  | stop_area_H
stop_area_A | A | 47.01 | 1.01 | 1 |  |
stop_area_B | A | 47.01 | 1.01 | 1 |  |
stop_area_C | A | 47.01 | 1.01 | 1 |  |
stop_area_D | A | 47.01 | 1.01 | 1 |  |
stop_area_E | A | 47.01 | 1.01 | 1 |  |
stop_area_H | A | 47.01 | 1.01 | 1 |  |
zone_2 | Zone 1 | 47.01 | 1.01 | 2 | id_vers_POLYGON((1 1,5 1,5 5,1 5,1 1)) |

**Fichier [`stop_times.txt`](#stop_timestxt-required): déclare les "horaires", estimés ou non**

trip_id | stop_id | arrival_time | departure_time | stop_sequence | pickup_type | drop_off_type | stop_time_precision | zone_itl
--- | --- | --- | --- | --- | --- | --- | --- | ---
trip_1 | stop_point_A |  | 09:02:00 | 1 | 0 | 0 | 0 | 0
trip_1 | stop_point_B | 09:15:00 | 09:05:00 | 2 | 2 | 2 | 1 | 1
trip_1 | stop_point_C | 09:15:00 | 09:07:00 | 2 | 2 | 2 | 1 | 1
trip_1 | stop_point_D | 09:15:00 | 09:09:00 | 2 | 2 | 2 | 1 | 1
trip_1 | stop_point_E | 09:17:00 | 09:18:00 | 1 | 0 | 0 | 0 | 2
trip_1 | zone_2 | 09:25:00 | 09:20:00 | 2 | 2 | 2 | 1 | 3
trip_1 | stop_point_H | 09:27:00 |  | 1 | 0 | 0 | 0 | 4

#### Ligne arrêt à arrêt sans horaires
![Exemple de ligne arrêt à arrêt](NTFS_image2.png)

Les zones n'ont pas de valeur dans le NTFS: la prise des passagers se fait sur des stop_point. L'idée est de déclarer des "blocs d'horaires" sur chacun des stop_point de chaque zone :

* A>B>C
    * descente interdite
    * 08h00 partout
* A>B>C
    * montée interdite
    * 08h10 partout
* D>E>F
    * descente interdite
    * 09h00 partout
* D>E>F
    * montée interdite
    * 09h10 partout
* G>H>I
    * descente interdite
    * 10h00 partout
* G>H>I
    * montée interdite
    * 10h10 partout
