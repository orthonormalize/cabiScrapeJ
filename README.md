# SMS Bikeshare Dock Spotter

I developed this app so that I could view nearby Capital Bikeshare availability data over text-based SMS. The app requires no imagery display or location services. I ping the app by texting it a street address. The app response informs me where the nearest bikeshare stations are, along with real-time bike/dock availability.

## Motivation

I started this project in the spring of 2017. At the time I was a flip phone user who had not replaced my phone for ten years. I had a Samsung Alias SCH-U740, a device with dual-axis flip and a full QWERTY keyboard. Its best feature was a tiny form factor. At just 98 x 52 x 16 mm, this phone could fit in my pocket without any noticeable bulk; thus it was far more convenient to carry than modern large-screen devices.

The downside: without a smartphone, I was at an information disadvantage. One such disadvantage: I couldn't track bikeshare availability on the go. While many public transit systems continue to provide status info over SMS, bikeshare as urban transit is a phenomenon younger than the smartphone (Capital Bikeshare launched in Sep 2010). When ridership grew, and developers such as Spotcycle began creating apps to assist bikeshare users with their travels, they leveraged smartphone features like location services and map visualizations to build their products. Without a smartphone I could not use these services. To my knowledge, no one had productized a version for SMS that could be used on older phones. So I decided to write one myself. Since I had already built a webscraper to gather system-wide dock status data for another project, I decided to extend it so that I could access the data I needed anywhere on the streets using my flip phone.

## Design Criteria

All communications between the app and the mobile device are plaintext SMS. No other data services are available at the mobile device.


## Approach

Capital Bikeshare provides its dock info via XML at https://feeds.capitalbikeshare.com/stations/stations.xml . (2020 update: the legacy data feed has been disabled. CaBi now provides this info over GBFS). This includes real-time bike/dock availability as well as location description and lat/lon for each station.

Without location services, a problem is presented. I can't sift through the status of all 600 stations, so I need to communicate my location somehow. The easiest way to communicate a location over text is to enter a street address. Fortunately, the street networks and postal addresses in both Arlington and in Washington DC are very regular and predictable. If one can identify one's alphabetic and numeric "grid coordinates" within either jurisdiction, it's extremely easy to construct a valid street address that is sufficiently close to any location therein. (That's not the case in the outlying suburbs!). All I need to do is mentally calculate such an address, type it in to my SMS app, and send the message.

The app continuously monitors a Gmail inbox for these SMS pings, by filtering for emails from trusted senders that contain a single postal address in the body. After verifying sender and message format, it queries Google's Geocoding API, decodes the street address, and returns the corresponding lat/lon. Two features of Google's API simplify this process further. First, the address itself need not physically exist; as long as such an address COULD exist on the correct block, Google will find it. Also the Geocoding API autocompletes addresses, so I never need to enter city/state/zip. 

Once the app has retrieved the lat/lon of the street address, it constructs a response, consisting of the three nearest Capital Bikeshare docking stations (by Euclidean distance), including their bike/dock availability. If either bikes or docks have limited availability (0 or 1) at any of these three nearest stations, the search continues until it finds the three nearest stations that do have sufficient availability. A follow-on response is sent with that additional info.

For brevity, the output format is:
rank: (num_available_bikes, num_available_docks) heading_angle distance, location_name

## Example

![screenshot](screenshot_SMS_Bikeshare_App.png?raw=true)

I was recently at 9th and N NW and wanted to find a bike to ride home. Since 9th St is the 900 block of N St, I made up an address in the 900 block of N St NW and submitted it. From the response, we can see that the closest CaBi station to the input address is at 8th and O Streets, 610 feet to the northeast. This station has nineteen bikes but zero empty docks and is thus completely full. The next closest station, 820 feet to the northwest at 11th and O, is well balanced with six bikes and twelve empty docks. The third-nearest station is Convention Center, which has just one bike. Because those three closest stations include one with a shortage of bikes and another with a shortage of docks, a second text is sent showing one more station with bikes and one more station with docks. In the present example, those turn out to be the same station: the fourth-nearest station, at 11th and M Streets, has seven bikes and twelve empty docks.



## Comments

Several months after completing this app, my old Alias finally died. Alas, I ended up choosing the iPhone SE and joining the smartphone crowd after all.

But this was a fun exercise and I learned some new skills and tools. :)