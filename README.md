## API Gateway / Features
reportLocation(api_key, driver_id, longitude, latitude) => return trip
findDriver(api_key, user_id, cur_longtitude, cur_latitude, destination_longtitude, destination_latitude) => trip_info, driver_info, vehicle_info

### Optional features
- Driver can decline a trip
- Driver can mark him as not available
- Match a driver when he's finishing a trip
- Rider can ask where the driver is when matched a driver
- Show nearby cars to a rider when he's not requesting a trip
- rider cancel a trip
- Uber Eats
- Uber Pool
- filter by number of seats
- schedule a future pick up

## Service Design
locationSvc, driverSvc, tripSvc, vehicleSvc, matchSvc

reportLocation:
- locationSvc.storeLocation(driver_id, longtitude, latitude)
- driverSvc.updateStatus(longtitude, latitude)  # Can be used to get the accurate location of a driver. Given a driver, find the exact location

findDriver: (should probably be message queue, cause it may take a while)
### matchSvc.findDriver(user_id, longtitude, latitude) # So that changes to matchSvc won't be managed by API gateway
- driver_ids = locationSvc.findNearByDrivers(longitude, latitude)
- driver_infos = driverSvc.getDriverInfos(driver_ids)  # Find out whether they are in a trip, ratings etc.
pick_one_driver_by_ranking_and_filtering(driver_infos)
- driverSvc.updateDriverStatus(driver_id, "in a trip")   # Lock and update driver info
- trip_info = tripSvc.createTrip(driver_id, rider_id, cur_longtitude, cur_latitude, destination_longtitude, destination_latitude)
- vehicle_info = vehicleSvc.getVehicleInfo(vehicle_id)  # One driver can have multiple vehicles, can denormalize the model and car color in driver table for faster retrieval


## Storage
driverTable (SQL)
- vehicle_id: str
- longtitude: float
- latitude: float
- user_id: str
- last_updated: timestamp


vehicleTable (SQL):
color: str (fixed set)
year: str (validation)
model: str (fixed set)
maker: str (fixed set)
seats: int (4 or 6 or more?)

locationTable (Redis)
- driver_id: str
- geohash_str: str
- longtitude: str  # geohash is an approximation, exact location can be used for ranking
- latitude: str
- last_updated: time_stamp


## Helper service
matchSvc can be put behind a message queue because it takes time to match a driver


## Pseudo code
locationSvc.storeLocation(driver_id, longtitude, latitude)
- 5 letter hashTable
