# SMS Bikeshare Availability App

I developed an app that would allow me to check Capital Bikeshare availability over text-based SMS, with no imagery display or location services. I ping the app by texting it an address. The response informs me where the nearby bikeshare stations are, and how many bikes/docks are available.

## Motivation

In the spring of 2017, I was a flip phone user who had been loyal to a single phone for ten years. I had a Samsung Alias, a device with dual-axis flip, a full QWERTY keyboard, and a small volume (it measured just xxxxxxxx, fitting easily into my pocket). The downside: without a smartphone, I was at an information disadvantage in certain ways. One disadvantage was my inability to track bikeshare availability on the go; Spotcycle requires a smartphone. Since I had already built a webscraper to gather system-wide dock status data, I decided to extend it to provide myself a text-based Spotcycle substitute.

## Approach

Capital Bikeshare provides its dock info via XML at https://feeds.capitalbikeshare.com/stations/stations.xml . This includes real-time bike/dock availability as well as location description and lat/lon for each station. I avoid burdening the server by pinging it just once a minute.

Without location services, I need to communicate my location. The street networks and postal addresses in both Arlington and DC are very regular, so I find it extremely easy to "guess" an address sufficiently close to any location, regardless of where I happen to be. (This is not the case in the suburbs!). I send this address over text.

The app continuously monitors a Gmail inbox for emails from trusted senders that contain postal addresses. After verifying sender and message format, it constructs a response, consisting of the three nearest docking stations (by Euclidean distance), along with their location info and bike/dock availability. If either bikes or docks are scarce (0 or 1) at any of these three docks, the search is expanded until it finds three nearby stations that do have availability >= 2 bikes/docks, and a follow-on response is sent with that info.

## Comments

Several months after completing this app, my old Alias finally died. Alas, I ended up choosing the iPhone SE and joining the smartphone crowd. But although I have acquired many new capabilities with the iPhone, I have yet to download Spotcycle. :)

Also this has been a useful exercise as I learned some new python tricks (such as using the Gmail API to manage a mailbox).