## API Gateway / Features
reportLocation(api_key, driver_id, longitude, latitude)
findDriver(api_key, user_id, cur_longtitude, cur_latitude, destination_longtitude, destination_latitude) => trip_info, driver_info, vehicle_info

## Service Design
locationSvc, driverSvc, tripSvc, VehicleSvc, matchSvc

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
- vehicle_info = vehicleSvc.getVehicleInfo(vehile_id)



## Storage



## Pseudo code
locationSvc.storeLocation(driver_id, longtitude, latitude)
-

