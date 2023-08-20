# System Design In Practice

## Ticket Purchase

### Premisses

* Only credit card purchases
* Purchase needs to be confirmed instantly
* Must have consistency over high availability
* Concurrency - dont sell same ticket to more than one person
* Ticket code must be short

#### First Option
![system1-arch](system1-arch.png)

##### Tradeoffs

![tradeoffs](system1-tradeoffs.png)

#### Second Option
![system2-arch](system2-arch.png)

#### Third Option
![system3-arch](system3-arch.png)

#### Fourth Option
![system4-arch](system4-arch.png)

#### Data Modeling
![data-model](data-model.png)

![email-service-data-model](email-service-data-model.png)

#### API Design

##### Purchase
placeOrder(userId, eventId, spotId) (OrderId, TicketId)

##### View ticket

viewTicket(ticketId) (OrderId, UserId, UserName, EventDate, Spot, Total)

#### Metrics and monitoring
![metrics-and-monitoring](metrics-and-monitoring.png)