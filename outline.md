## Intro

- Ask questions whenever!

## How does this thing work?

- Highlight the general overview of how we connect our codebase to Segment and map it to Braze

- Code sample of AnalyticsService
- Code snippet of SegmentService
- US and Canada? Showcase the client method to grab the right SegmentService
- Highlight how track keep track of this

  - Mention identify is also doing a similar pattern for getting the proper client based on the country

- Showcase the @pm/analytics library

- Highlight difference between track and braze_track

## Segment setup

- Highlight how the connection to Segment works in our back-end codebase

- Highlight the AnalyticsService (differences between track and braze_track) in back-end and @pm/analytics package in front-end

# Example

## I want to send a email when a patient signs an agreement and see when they view an agreement

- Questions we should ask

  - Do we want all agreements being emailed to patients or just specific ones?
  - What properties do you need on that event, agreement name, ip address, the timestamp of the agreement to capture which version?

  - Would this be helpful as an event or an event attribute? I.e., should it be added to an existing event or created as a new event

- What to do if there's an existing event, adding a new property

## Let's dive in!

- Show the code changes on the back-end we need to make and front-end changes

## Sudden ask!

- I want to be able to see what agreements the patient has on their account!

## How might we do this?

## User Attributes (Segment Properties)

- Go over the custom attributes within the User class and what it means

- Show the change needed to support the sudden ask and off we go!

## All done!

## Wait a minute... Something is missing

## Tested

- Show untested meme

### Let's write a test!

- Show code examples on how to test analytic events in our codebase

## Considerations

- Updating event names can break reports and also mismatch on Braze for lifecycle. If you're updating an event, you're likely impacting something!

- Each event name is a database table! Keep this in mind when making events, prefer generic events that can be differentiated with properties
- Caveat! If it's a Braze specific event it might not be feasible to do a generic name as the filtering options in Braze is limited.
  - Generic names can be used to trigger or track conversions
  - Cannot be used to segment users
  - Cannot be used to populate custom data
  - This is pretty contextual to the goal of the event!
  - There is webhooks that can be used
  - Ensure we're all on the same page on the event!

## Recap

- Segment is our CDP that our events get emitted to and then funneled to various destinations all managed through the Dashboard

- Braze is our customer engagement tool that allows us to engage with a segment of patients when various events fire in our apps

## Q&A
