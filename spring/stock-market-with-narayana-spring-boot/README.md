Narayana Spring Boot stock market example
============================
Author: Gytis Trikleris
Level: Intermediate
Technologies: JTA, JMS, JPA, Spring Boot
Summary: simplified stock market application demonstrating Narayana in Spring Boot integrating with Artemis, Spring Data, and H2.


What is it?
-----------

This is a highly simplified stock market application demonstrating Narayana in Spring Boot. It exposes two REST endpoints for buying and selling shares as well as a bunch of endpoints generated by the Spring Data REST for users and shares management.
 
 Buying and selling is performed inside of a transaction and all actions inside of those methods are rolled back, if the following requirements are not met:
 
    1. User must exist.
    2. Share must exist.
    3. If user is buying shares, he has to have enough budget.
    4. If user is buying shares, there must be enough shares available in the market.
    5. If user is selling shares, he must have enough of them.
    
Also, during the buying and selling, multiple status messages are sent to the "updates" queue.

This application uses Narayana for transaction management, Spring Web for exposing REST endpoints, Spring Data for database access, Spring JMS for working with message queue, H2 as a database implementation, and Artemis message queue.


Requirements
------------

In order to start the application you needs Java 8.0 or later and Maven 3.2 or later.


Usage
-----

In a separate terminal window start the application:

    mvn clean spring-boot:run

Get users:

    curl -X GET "http://localhost:8080/users"

Create user:

    curl -X POST -H "Content-Type: application/json" -d '{"username": "petyr","budget": "1000"}' "http://localhost:8080/users"

Get shares:

    curl -X GET "http://localhost:8080/shares"

Create share:

    curl -X POST -H "Content-Type: application/json" -d '{"symbol": "rht","amount": 500,"price": 80}' "http://localhost:8080/shares"

Buy share:

    curl -X PUT "http://localhost:8080/portfolio/petyr/rht?amount=1"

If shares were bought successfully, you should see a similar outcome to the following in a log:

    ----> Updated 'petyr' budget to 920
    ----> Updated 'rht' amount to 499
    ----> 'petyr' just bought 1 shares of 'rht' for the amount of 80

Sell share:

    curl -X DELETE "http://localhost:8080/portfolio/petyr/rht?amount=1"

If shares were sold successfully, you should see a similar outcome to the following in a log:

    ----> Updated 'petyr' budget to 1000
    ----> Updated 'rht' amount to 500
    ----> 'petyr' just sold 1 shares of 'rht' for the amount of 80