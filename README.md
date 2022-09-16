# Amadeus Dotnet SDK

[![Nuget](https://img.shields.io/nuget/v/amadeus-dotnet)](https://www.nuget.org/packages/amadeus-dotnet)
[![build](https://github.com/amadeus4dev/amadeus-dotnet/actions/workflows/build.yml/badge.svg)](https://github.com/amadeus4dev/amadeus-dotnet/actions/workflows/build.yml)
[![Discord](https://img.shields.io/discord/696822960023011329?label=&logo=discord&logoColor=ffffff&color=7389D8&labelColor=6A7EC2)](https://discord.gg/cVrFBqx)


Amadeus provides a rich set of APIs for the travel industry. For more details, check out the [Amadeus for Developers portal](https://developers.amadeus.com).

## Installation

You can install the `amadeus-dotnet` via NuGet packages installer, by searching for it in the package explorer.

## Getting Started

To make your first API call you will need to [register for an Amadeus
Developer Account](https://developers.amadeus.com/create-account) and set up
your first application.

```C#
using System;

using amadeus;
using amadeus.resources;

namespace amadeusTest


{
    class Program
    {
        static void Main(string[] args)
        {
            GetCheckinLinks();
        }

        public static void GetCheckinLinks()
        {
            try
            {
                Amadeus amadeus = Amadeus
                    .builder("REPLACE_BY_YOUR_API_KEY", "REPLACE_BY_YOUR_API_SECRET")
                    .build();

                Console.WriteLine("Get Check-in links:");
                CheckinLink[] checkinLinks = amadeus.referenceData.urls.checkinLinks.get(Params
                        .with("airlineCode", "BA"));

                Console.WriteLine(checkinLinks[0].response.data);

            }
            catch (Exception e)
            {
                Console.WriteLine("ERROR: " + e.ToString());
            }
        }
    }
}
```

## Initialization

The client can be initialized directly.

```C#
//Initialize using parameters
Amadeus amadeus = Amadeus
        .builder("REPLACE_BY_YOUR_API_KEY", "REPLACE_BY_YOUR_API_SECRET")
        .build();
```

Alternatively it can be initialized without any parameters if the environment
variables `AMADEUS_CLIENT_ID` and `AMADEUS_CLIENT_SECRET` are present.

```C#
System.Environment.SetEnvironmentVariable("AMADEUS_CLIENT_ID", "REPLACE_BY_YOUR_API_KEY");
System.Environment.SetEnvironmentVariable("AMADEUS_CLIENT_ID", "REPLACE_BY_YOUR_API_SECRET");
Amadeus amadeus = Amadeus
        .builder()
        .build();
```

Your credentials can be found on the [Amadeus
dashboard](https://developers.amadeus.com/my-apps). [Sign
up](https://developers.amadeus.com/create-account) for an account today.

By default the environment for the SDK is the `test` environment. To switch to
a production (paid-for) environment please switch the hostname as follows:

```C#
Amadeus amadeus = Amadeus
        .builder()
        .setHostname("production")
        .build();
```

## Making API calls

This library conveniently maps every API path to a similar path.

For example, `GET /v2/reference-data/urls/checkin-links?airlineCode=BA` would be:

```C#
amadeus.referenceData.urls.checkinLinks.get(Params.with("airlineCode", "BA"));
```

Similarly, to select a resource by ID, you can pass in the ID to the **singular** path.

For example,  `GET /v2/shopping/hotel-offers/XXX` would be:

```C#
amadeus.hotelOffer("XXX").get(...);
```

You can make any arbitrary API call as well directly to call not supported yet APIs. 
Keep in mind, this returns a raw `Resource`. 

For the `.get` methods you can call the APIs such as 
```C#
amadeus.get("/v2/reference-data/urls/checkin-links",
  Params.with("airlineCode", "BA"))
```
and for the `.post` methods
```C#
amadeus.post("/v1/shopping/availability/flight-availabilities", body);
```

## Response

Every successful API call returns a `Resource` object. The underlying
`Resource` with the raw available.

```C#
Location[] locations = amadeus.referenceData.locations.get(Params
  .with("keyword", "LON")
  .and("subType", "CITY"));

Console.Write(locations[0].response.body); //the raw response, as a string
Console.Write(locations[0].response.result); //the body parsed as JSON, if the result was parsable
Console.Write(locations[0].response.data); //the list of locations, extracted from the JSON
```

## Pagination

If an API endpoint supports pagination, the other pages are available under the
`.next`, `.previous`, `.last` and `.first` methods.

```C#
Location[] locations = amadeus.referenceData.locations.get(Params
  .with("keyword", "LON")
  .and("subType", Locations.ANY));

// Fetches the next page
Location[] locations = (Location[]) amadeus.next(locations[0]);
```

If a page is not available, the method will return `null`.

## Logging & Debugging

The SDK makes it easy to add logger. To enable more verbose logging, you can set the appropriate level
on your own logger, though the easiest way would be to enable debugging via a
parameter on initialization, or using the `AMADEUS_LOG_LEVEL` environment
variable.

```C#
Amadeus amadeus = Amadeus
        .builder("REPLACE_BY_YOUR_API_KEY", "REPLACE_BY_YOUR_API_SECRET")
        .setLogLevel("debug") // or warn
        .build();
```

## List of supported endpoints
```C#
// Flight Inspiration Search
FlightDestination[] flightDestinations = amadeus.shopping.flightDestinations.get(Params
  .with("origin", "MAD"));

// Flight Cheapest Date Search
FlightDate[] flightDates = amadeus.shopping.flightDates.get(Params
  .with("origin", "MAD")
  .and("destination", "MUC"));

// Flight Check-in Links
CheckinLink[] checkinLinks = amadeus.referenceData.urls.checkinLinks.get(Params
  .with("airlineCode", "BA"));

// Airline Code LookUp
Airline[] airlines = amadeus.referenceData.airlines.get(Params
  .with("airlineCodes", "BA"));

// Airport & City Search (autocomplete)
// Find all the cities and airports starting by the keyword 'LON'
Location[] locations = amadeus.referenceData.locations.get(Params
  .with("keyword", "LON")
  .and("subType", Locations.ANY));
  
// Get a specific city or airport based on its id
Location location = amadeus.referenceData
  .location("ALHR").get();

// Airport Nearest Relevant (for London)
Location[] locations = amadeus.referenceData.locations.airports.get(Params
  .with("latitude", 0.1278)
  .and("longitude", 51.5074));

// Flight Most Searched Destinations
// Which were the most searched flight destinations from Madrid in August 2017?
SearchedDestination searchedDestination = amadeus.travel.analytics.airTraffic.searchedByDestination.get(Params
        .with("originCityCode", "MAD")
        .and("destinationCityCode", "NYC")
        .and("searchPeriod", "2017-08")
        .and("marketCountryCode", "ES"));
        
// How many people in Spain searched for a trip from Madrid to New-York in September 2017?
Search[] search = amadeus.travel.analytics.airTraffic.searched.get(Params
        .with("originCityCode", "MAD")
        .and("searchPeriod", "2017-08")
        .and("marketCountryCode", "ES"));

// Flight Most Booked Destinations
AirTraffic[] airTraffics = amadeus.travel.analytics.airTraffic.booked.get(Params
  .with("originCityCode", "MAD")
  .and("period", "2017-08"));

// Flight Most Traveled Destinations
AirTraffic[] airTraffics = amadeus.travel.analytics.airTraffic.traveled.get(Params
  .with("originCityCode", "MAD")
  .and("period", "2017-01"));

// Flight Busiest Traveling Period
Period[] busiestPeriods = amadeus.travel.analytics.airTraffic.busiestPeriod.get(Params
  .with("cityCode", "MAD")
  .and("period", "2017")
  .and("direction", BusiestPeriod.ARRIVING));
    
// Flight Offers Search
// GET endpoint
FlightOffer[] flights = amadeus.shopping.flightOffersSearch.getFlightOffers(Params.
    with("originLocationCode", "SYD")
    .and("destinationLocationCode", "BKK")
    .and("departureDate", System.DateTime.Now.AddMonths(2).ToString("2022-06-06"))
    .and("adults", "1"));
    
// POST ednpoint
FlightOffer[] flights = amadeus.shopping.flightOffersSearch.postFlightOffers(body);

// Flight Offers Price
FlightOfferPricingOutput pricing = amadeus.shopping.flightOffers.pricing.postFlightOffersPricing(body);

// Flight Create Orders
FlightOrderCreateQuery createOrder = amadeus.booking.flightOrder.postFlightOrderManagement(body);

// Flight Order Management
// GET endpoint
FlightOrderCreateQuery getOrder = amadeus.booking.flightOrder.getFlightOrderManagement(Params
    .with("flight-orderId", "eJzTd9cPsbR083cDAArgAkc%3D"));

// DELETE endpoint
FlightOrderCreateQuery[] deleteOrder = amadeus.booking.flightOrder.deleteFlightOrderManagement(Params
    .with("flight-orderId", "eJzTd9cPsbR083cDAArgAkc%3D"));
    
// SeatMap Display API 
// GET ednpoint
SeatMap[] seatsmap = amadeus.shopping.seatmaps.getSeatMap(Params
    .with("flightOrderId", "MlpZVkFMfFdBVFNPTnwyMDE1LTExLTAy"));

// POST endpoint
SeatMap[] response = amadeus.shopping.seatmaps.postSeatMap(body);

// Hotel Search
// Get list of hotels by city code
HotelOffer[] offers = amadeus.shopping.hotelOffers.get(Params
  .with("cityCode", "MAD"));
  
// Get list of offers for a specific hotel
HotelOffer hotelOffer = amadeus.shopping.hotelOffersByHotel.get(Params.with("hotelId", "BGLONBGB"));

// Confirm the availability of a specific offer
HotelOffer offer = amadeus.shopping.hotelOffer("NRPQNQBOJM").get();

// Points of Interest
// What are the popular places in Barcelona (based a geo location and a radius)
PointOfInterest[] pointsOfInterest = amadeus.referenceData.locations.pointsOfInterest.get(Params
   .with("latitude", "41.39715")
   .and("longitude", "2.160873"));

// What are the popular places in Barcelona? (based on a square)
PointOfInterest[] pointsOfInterest = amadeus.referenceData.locations.pointsOfInterest.bySquare.get(Params
    .with("north", "41.397158")
    .and("west", "2.160873")
    .and("south", "41.394582")
    .and("east", "2.177181"));
    
// Safe Place 
// Safe Place by coordinates 
SafetyRatedLocation[] safeLocation = amadeus.safety.safetyRatedLocations.getByGeoCode(Params
    .with("latitude", "41.397158")
    .and("longitude", "2.160873"));
    
// Safe Place by square 
SafetyRatedLocation[] safeLocation = amadeus.safety.safetyRatedLocations.getBySquare(Params
    .with("north", "41.397158")
    .and("west", "2.160873")
    .and("south", "41.394582")
    .and("east", "2.177181"));
    
// Safe Place by id 
SafetyRatedLocation safeLocation = amadeus.safety.safetyRatedLocations.getById(Params
    .with("safety-rated-locationId", "Q930402719"));

// Trip Purpose Prediction
Prediction prediction = amadeus.travel.predictions.tripPurpose.get(Params.with("originLocationCode", "NYC")
    .and("destinationLocationCode", "MAD")
    .and("departureDate", "2022-06-01")
    .and("returnDate", "2022-06-06"));
    
// Travel Restrictions 
DiseaseAreaReport restrictions = amadeus.dutyOfCare.diseases.covid19AreaReport.get(Params
    .with("countryCode", "US"));

```

## Development & Contributing

Want to contribute? Read our [Contributors Guide](.github/CONTRIBUTING.md) for
guidance on installing and running this code in a development environment.


## License

This library is released under the [MIT License](LICENSE).

## Help

You can find us on [StackOverflow](https://stackoverflow.com/questions/tagged/amadeus) or join our developer community on
[Discord](https://discord.gg/cVrFBqx).
