# Recap

![](./assets/assets/brooklyn-99.gif)

## Segment

Segment is our CDP that our events get emitted to and then funneled to various destinations all managed through the Dashboard

## Braze

Braze is our customer engagement tool that allows us to engage with a segment of patients when various events fire in our apps

## Analytics Service, Segment Service, and @pm/analytics

We have AnalyticsService in our toolkit for firing events to Segment from our back-end which uses SegmentService

We have @pm/analytics on the front-end to fire front-end events that go directly to Segment and then mapped to various destinations

## Track vs Braze Track

In AnalyticsService we have track and braze_track, braze_track is just calling into track but it's passing braze as true in the properties to ensure this event goes to Braze!

Track will not send to Braze by default!

## Impacts

When making updates to existing events or adding new events it's important to inform other departments such as marketing, ops, and data.

If you're updating an existing event you're likely going to cause an issue downstream so be sure to coordinate!

When adding new events, coordinate with other teams to ensure the event makes sense for their workflows and be on the same page!
