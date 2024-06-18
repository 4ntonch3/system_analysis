# README

## Glossary

`Order` - entity related to task created by *Client* and to be processed by *Worker*.

`Order Processing` - process of solving `Order` by *Worker*. Starts, when *Worker* comes to solve `Order`, ends, when *Worker* finishes solving `Order`. Its start and end doesn't mean start and end of `Order`

`Application` - entity related to a form signed by *Cat* to become a *Worker*.

## Event Storming. Contexts

**Authentication** - special context to manage access to System.

All actions related to hiring and managing new *Workers* is moved to **Hiring of Workers** context.

The flow of orders located in **Orders** context.

**Order Mapping** is moved to separate context, because it's claimed as competitive advantage in requirements.

**Order Q&A** & **Order Gambling** moved to separate context due to:

1. Having specific actors compared to **Orders**
2. Its functionality doesn't have impact on order flow process

**Accounting** consists of money-related things (calculating costs for orders & manage client's and worker's bills).

**Equipment for Order** is a separate context due to operating in specific area with specific actors.

## System Structure

Main points:

- Low TTM
- Low load (10 orders/day, 100 clients)
- Many changes in hiring action

|     *Aspect*      |                              *Monolith*                               |                            *MicroServices*                            |
| :---------------: | :-------------------------------------------------------------------: | :-------------------------------------------------------------------: |
|        TTM        | Initial version will be created faster.\nChanges will be added slower | Initial version will be created slower.\nChanges will be added faster |
|       Load        |                         Handles required load                         |                         Handles required load                         |
| Agility in Hiring |    Changing Hiring component causes whole System to be redeployed     |               Changing Hiring component is independent                |

Solution will be done primary in *microservice* manner. Anyhow, some components can be united into 1 monolith service, which contains several modules (1 module for 1 context).

### Order Service

Its suggested to place all services, which are related to orders into one service - **Order Service**, which contain all modules related to orders:

- *Order Mapping*
- *Orders*
- *Order Quality Assurance*
- *Order Gambling*
- *Equipment for Order*

The main idea - decrease initial TTM by using the same DB. Streaming communications inside *Order Service* are replaced by using the same DB (common data).

All communications inside can be done in sync manner due to low load on service and no requirements for latency.

### Communications

All communication between services can be asynchronous. The main point - availability of the core of the system (*Order Service*) if other service having troubles.

Nevertheless, communication between *Order Service* & *Accounting Service* is asynchronous to ensure that no data related to payments will be lost.
