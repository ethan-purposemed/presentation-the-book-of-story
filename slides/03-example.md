# Time for a scenario

## Rob is wanting to know when a patient signs an agreement

We think about this request

##

Rob adds some more context that for every 100 agreements signed the developers will get a free pizza party.

## We are now committed to the work

![](./assets/assets/lets-do-this.gif)

## We don't need an event for this

You're right but for this presentation let's pretend we do need this as an event for sending a patient an email through Braze for every agreement being signed

## We need to ask some questions

1. Do we want all agreements being emailed to patients or just specific ones?
2. What properties do you need on that event?
3. Is there any other events you foresee us needing to add?

## Answers are in

1. We need all the agreements being emailed
2. We need agreements name, the ip address, and the created at timestamp agreement to capture the version
3. Nothing at the moment

## Okay, let's get started!

## Let's make an event

We need to figure out where to fire this event.

##

![](./assets/assets/thinking.gif)

## We need to fire this event in the create agreement mutation!

##

![](./assets/assets/agreements_code.png)

## All done!

![](./assets/assets/jobs-finished.gif)
![](./assets/assets/pizza-party.gif)

## Wait a minute... Something is missing

##

![](./assets/assets/aint-much.jpg)

## Let's write a test for this work

But we don't want to actually be calling Segment when firing events. Let's talk about mocks

## RSpec Mocking

When writing tests in our code we can leverage mocks to isolate our tests to focus on the actual code being tested and not the state of external dependencies.

## Time to test

Let's first allow the test to receive the braze track method which will enable mocking on that method!

##

![](./assets/assets/agreements-spec-example-01.png)

## Now let's run our mutation

##

![](./assets/assets/agreements-spec-example-02.png)

## Next, we need to do our assertions

##

![](./assets/assets/analytics-track-assertion.png)

##

We expected the AnalyticsService to be called twice without actually calling out to Segment at all!

## Let's verify this is working!

## Admin App ✅

![](./assets/assets/agreement-event-firing.png)

## Segment

Now that we verified it is firing within our app and being stored in the events table, let's check if it's being sent to Segment!

##

![](./assets/assets/segment_step_1.png)

##

![](./assets/assets/segment_step_2.png)

##

![](./assets/assets/segment_step_3.png)

##

![](./assets/assets/segment_step_4.png)

## Braze

Now that Segment is verified, let's go check in on Braze and see if we're able to see the event under the "Custom Events" box in the profile

##

![](./assets/assets/braze_step_1.png)

##

![](./assets/assets/braze_step_2.png)

##

![](./assets/assets/braze_step_3.png)

## Sudden request!

The event has been in production now for some time and a request has come up to fire an event whenever the agreement has been viewed on the front-end.

## Let's do this!

## First, let's make a typed event in track-event.ts

##

![](./assets/assets/agreement-viewed-example.png)

##

![](./assets/assets/agreements-example-2.png)

## Now, let's use this event in our app!

![](./assets/assets/pending-agreement-gate-code.png)

## Now, let's write a test for this!

![](./assets/assets/tracks-test.png)

## Since we don't store these events in our Heroku database, we need to verify in Segment

##

The steps for verifying Segment are the same as the back-end example so I'll spare the repetition!

## Another request!

A new request has come in, we want to add a custom attribute in Braze for the number of agreements signed by the patient.

## We can do this with segment traits on the user!

##

![](./assets/assets/segment_traits.png)

## How does this get to Braze?

We are able to call identify again and pass in the new segment properties which then should map over to Braze. Whenever the patient updates their profile this should happen and it also occurs at sign up.

## Alright, we're all good now!

##

![](./assets/assets/pizza_time.gif)

<style>
#section-16 p > img {
  width: 650px;
}
</style>
