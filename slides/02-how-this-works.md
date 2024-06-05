#

![](./assets/assets/how-does-this-work.jpg)

##

![](./assets/assets/diagram.png)

## Analytics Service (Ruby)

back-end/app/services/analytics_service.rb

![](./assets/assets/analytics_service.png)

## Segment Service (Ruby)

back-end/app/services/segment_service.rb

![](./assets/assets/segment_service.png)

## How do we track?

![](./assets/assets/braze-track-example.png)

## Different between Braze Track and Track

You might notice in the snippet above that we have two events for firing events. braze_track and track

We can decide which destinations Segment passes through. Braze is not enabled by default for track

When calling braze_track all that is happening is that method is calling track but deep merging the data properties and setting braze to true so Segment will pass it over

## How do we identify?

![](./assets/assets/analytics_service--identify.png)

## US and Canada

We're operating in two countries with separate Segment instances, the Analytics Service handles which Segment client we use based on the country provided.

##

![](./assets/assets/analytics_service--track--filtered.png)

##

![](./assets/assets/analytics_service--country-client.png)

## What about the front-end?

## @pm/analytics

- There is trackEvent, trackAnalyticsEvent, trackPageView, and identify methods available
- This package provides Google Tag manager utilities
- This package assumes window.analytics is established from Segment front-end SDK!

##

![](./assets/assets/track-analytics-event.png)
![](./assets/assets/track-event-example-code.png)

## Why do we have track Event and track Analytics Event?

##

![](./assets/assets/track-event.png)

## Surprise! We're using track Analytics Event within track Event

## The difference here is track event is leveraging types

##

![](./assets/assets/types.png)

##

![](./assets/assets/track-event-type.png)
![](./assets/assets/track-event-type-example.png)

##

![](./assets/assets/track-analytics-identify.png)

## We're not using analytics Identify much across our apps

##

![](./assets/assets/window-analytics-identify.png)
