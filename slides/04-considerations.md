# Considerations you should make

## Event names

It's worth highlighting that updating event names can break reports and also cause issues in our lifecycle communications. In short, if you're updating an existing name it's likely you're impacting another department!

## BigQuery

Something worth highlighting is each event name we have creates a new table within BigQuery! This can make things complex for our data team. You should try to prefer generic names that can be differentiated with properties rather than unique names

## For example

Shopping Cart Button Clicked could actually be Button Clicked and the property can have a "type" identifier for ShoppingCart

## A Braze consideration on generic names

Generic names can be leveraged in Braze to trigger or track conversions. They however, cannot be used to segment users and populate custom data. There are options available such as webhooks to tweak the data to incorporate into Braze.

## For Braze, just ensure we're all on the same page and understand trade-offs for generic vs unique event names.

It's likely an event will need to drill further into just triggering and conversion tracking so be sure to work closely with lifecycle on new events and existing event updates!
