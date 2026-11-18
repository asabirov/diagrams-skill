# Diagram examples

Each picture is a render of its source in [checks/](../checks/), made with D2 0.9.0 and the render command in [SKILL.md](../SKILL.md). The examples are about a fictional online shop.

## Box diagram

What connects, and where are the boundaries? [Source](../checks/architecture.d2)

<img src="../assets/examples/architecture.png" alt="A box diagram of an online shop, with a Shop group and an External providers group." width="900">

## Flow

Which conditions lead to which outcomes? [Source](../checks/flow.d2)

<img src="../assets/examples/flow.png" alt="A refund flow with two yes or no questions and three outcomes." width="480">

## Customer journey

What does the customer do, including interruptions? [Source](../checks/journey.d2)

<img src="../assets/examples/journey.png" alt="A checkout journey that starts at a customer and has a declined card path." width="480">

## State diagram

Which states and transitions are possible? [Source](../checks/state.d2)

<img src="../assets/order-states.png" alt="An order goes from Placed to Paid, Shipped and Delivered, or ends as Cancelled or Refunded." width="900">

## Sequence diagram

Who sends what, in what order? [Source](../checks/sequence.d2)

<img src="../assets/examples/sequence.png" alt="A card payment between a customer, a shop and a payment provider, with approved and declined paths." width="620">

## Data tables

Which tables hold the data, and how do they join? [Source](../checks/tables.d2)

<img src="../assets/examples/tables.png" alt="Three joined tables: order items, orders and customers." width="760">

## Code structure

How is the code structured? [Source](../checks/classes.d2)

<img src="../assets/examples/classes.png" alt="Three classes: OrderService, Order and PaymentClient." width="480">

## Scenarios

How do variants of one system differ? [Source](../checks/scenarios.d2)

<img src="../assets/examples/scenarios.png" alt="The payment-down scenario of an online shop, with a retry queue." width="520">

## Steps

What happens at each stage, in order? [Source](../checks/steps.d2)

<img src="../assets/examples/steps.png" alt="The shipped step of an order, with a warehouse." width="480">

## Layers

What is inside one part of an overview? [Source](../checks/layers.d2)

<img src="../assets/examples/layers.png" alt="An overview board with a Shop box that links to a detail board." width="400">

## Tiles

How do similar items compare? [Source](../checks/grid.d2)

<img src="../assets/examples/grid.png" alt="Six order health tiles, four marked OK and two marked Watch." width="700">
