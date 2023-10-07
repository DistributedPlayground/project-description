# Distributed Playground
The purpose of this project is to practice the development of distributed systems

- [Goal](#goal)
- [Architecture](#architecture)
- [Service Descriptions](#service-descriptions)
- [Running the Services](#running-the-services)

## Goal
For this project, I'll create an ecommerce platform. This platform will allow users to purchase goods that are maintained by us. This is *not* a two sided marketplace with our users being both companies and consumers -- at least for now. To simplify, we'll centrally define the products.

## Architecture

### Functional Requirements
- Users should be able to make purchases using card payments
- Users should be able to see available products, including their remaining stock
- Users should be able to sort and filter products

### Quality Attributes
- Should be designed to handle >10M users per day
- Should have 99.9% uptime
- Should allow for concurrent development from a large distributed team

### Design
- We will use Golang as the primary backend language.

- We will provide an internal CP db optimized for writes in postgres
    - This db will be exposed to our systems through a REST API
- Implementing a psudo CQRS (Command Query Responsibility Segregation)
    - "Psudo" here because we really just want to segregrate the most read heavy users (customers) to a separate db and api optimized for their needs
    - This customer read db will be updated through a service that reads from a Kafka queue

## Service Descriptions
- **API Gateway**: *Not Implemented*
- **[Products](https://github.com/DistributedPlayground/products)**: A REST API with a postgres db. It allows sellers to manage their products and product collections. This service publishes writes to a kafka queue.
- **[Product Search](https://github.com/DistributedPlayground/product-search)**: A GraphQL API with mongodb. It allows customers to query the current products and collections. It reads updates from kafka to update the mongodb database.
- **[Inventory](https://github.com/DistributedPlayground/inventory)**: A gRPC API with redis. It is intended to maintain the most up-to-date state of product inventory. It reads updates to product inventory made by sellers from kafka, and also writes updates to the product inventory made by customers through purchases.
- **Orders**: *Not Implemented* Orchestrator for *Payments*, *Fufillment*, and *Notifications*, state management with kafka
- **Order Recovery**: *Not Implemented*

## Running the Services
1. Ensure that you have cloned the following repositories into the same directory so that they are parallel in your local filesystem:
* [Products](https://github.com/DistributedPlayground/products)
```bash
git clone https://github.com/DistributedPlayground/products
```
* [Product-Migrations](https://github.com/DistributedPlayground/product-migrations)
```bash
git clone https://github.com/DistributedPlayground/product-migrations
```
* [Product-Search](https://github.com/DistributedPlayground/product-search)
```bash
git clone https://github.com/DistributedPlayground/product-search
```
* [Inventory](https://github.com/DistributedPlayground/inventory)
```bash
git clone https://github.com/DistributedPlayground/inventory
```
* [Infra](https://github.com/DistributedPlayground/infra)
```bash
git clone https://github.com/DistributedPlayground/infra
```

2. Follow the instructions detailed in the [infra](https://github.com/DistributedPlayground/infra) repository to set up and run the services.