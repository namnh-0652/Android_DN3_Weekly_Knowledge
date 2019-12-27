# Tech Link
`Given-When-Then` template:
- https://martinfowler.com/bliki/GivenWhenThen.html?source=post_page-----affdd2273310----------------------

`Four Phase Test (Setup-Exercise-Verify-Teardown)`:
- http://xunitpatterns.com/Four%20Phase%20Test.html 

`3A – Arrange, Act, Assert`
- https://xp123.com/articles/3a-arrange-act-assert/
# Description

Learning a little bit about 3 templates of `UnitTest`:
- `Given-When-Then`
- `3A – Arrange, Act, Assert`
- `Four Phase Test (Setup-Exercise-Verify-Teardown)`

## Given-When-Then template

- The **given** part describes the state of the world before you begin the behavior you're specifying in this scenario. You can think of it as the pre-conditions to the test.A testing framework, however, interprets the givens as a set of commands to bring the system-under-test into the correct state before executing the **when** command.
- The **when** section is that behavior that you're specifying. Testing frameworks provide various query methods for the then commands - these should be free of side-effects
- Finally the **then** section describes the changes you expect due to the specified behavior.

Ex:
```ruby
Feature: User trades stocks
  Scenario: User requests a sell before close of trading
    Given I have 100 shares of MSFT stock
       And I have 150 shares of APPL stock
       And the time is before close of trading

    When I ask to sell 20 shares of MSFT stock
     
     Then I should have 80 shares of MSFT stock
      And I should have 150 shares of APPL stock
      And a sell order for 20 shares of MSFT stock should have been executed
```
## 3A – Arrange, Act, Assert

- **Arrange**: Set up the object to be tested. We may need to surround the object with collaborators. For testing purposes, those collaborators might be test objects (mocks, fakes, etc.) or the real thing.
d
- **Act**: Act on the object (through some mutator). You may need to give it parameters (again, possibly test objects).

- **Assert**: Make claims about the object, its collaborators, its parameters, and possibly (rarely!!) global state. 

## Four Phase Test (Setup-Exercise-Verify-Teardown)
The four parts are `fixture setup`, `exercise SUT`, `result verification` and `fixture teardown`.
- In the first phase, we set up the `test fixture` (the "before" picture) that is required for the SUT(system under test - whatever thing we are testing) to exhibit the expected behavior as well as anything you need to put in place to be able to observe the actual outcome (such as using a Test Double (page X).)
- In the second phase, we interact with the SUT.When we are writing unit tests the system under test (`SUT`) is whatever class (a.k.a. `CUT`), object (a.k.a. `OUT`) or method(s) (a.k.a. `MUT`) we are testing; when we are writing customer tests, the `SUT` is probably the entire application (a.k.a. `AUT`) or at least a major subsystem of it. The parts of the application that we are not verifying in this particular test may still be involved as a depended-on component (`DOC`) 
- In the third phase, we do whatever is necessary to determine whether the expected outcome has been obtained.
- In the fourth phase, we tear down the `test fixture` to put the world back into the state in which you found it.

Ex:

1. `Four phase test (inline)`
```java
public void testGetFlightsByOriginAirport_NoFlights_inline() throws Exception {
   // Fixture setup
   NonTxFlightMngtFacade facade =new NonTxFlightMngtFacade();
   BigDecimal airportId = facade.createTestAirport("1OF");
   try {
      // Exercise System
      List flightsAtDestination1 = facade.getFlightsByOriginAirport(airportId);
      // Verify Outcome
      assertEquals( 0, flightsAtDestination1.size() );
   } finally {
      // Fixture teardown
      facade.removeAirport( airportId );
   }
}
```
> All four phases of the Four-Phase Test are included inline. Because the calls to Assertion Methods raise exceptions, we need to surround the `fixture teardown` part of the `Test Method` with a `try/finally` construct to ensure that is is run in all cases.

2. `Example: Four Phase Test (Implicit SetUp/TearDown)`
```Java
NonTxFlightMngtFacade facade = new NonTxFlightMngtFacade();
private BigDecimal airportId;

protected void setUp() throws Exception {
   // Fixture setup
   super.setUp();
   airportId = facade.createTestAirport("1OF");
}

public void testGetFlightsByOriginAirport_NoFlights_implicit() throws Exception {
   // Exercise SUT
   List flightsAtDestination1 = facade.getFlightsByOriginAirport(airportId);
   // Verify Outcome
   assertEquals( 0, flightsAtDestination1.size() );
}   

protected void tearDown() throws Exception {
   // Fixture teardown
   facade.removeAirport(airportId);
   super.tearDown();
}
```
> Because the `tearDown` method is called automatically even after test failures, we don't need the `try/finally` construct inside the `Test Method`.

**Implementation Notes:**

Free-Standing 

- `Inline setup` => setup pre-condition inside `test method`
- `Implicit setup` => setup in `@Before` annotation method
- `Garbage-Collected Teardown` => good for using this for the in-memory objects during fixture setup (in most cases, we do nothing because local objects in test method will available to be collected by GC when method is completed)
```java
public void testCancel_proposed_GCT() {
   // fixture setup:
   Flight mutableFlight = createAnonymousProposedFlight();
   // exercise SUT
   mutableFlight.cancel();
   // verify outcome\
   assertEquals( FlightState.CANCELLED, mutableFlight.getStatus());
   // tearDown:
   //  Garbage collected when mutableFlight goes out of scope
}
```
- `Inline Teardown` => good for using this for the persitent object (which may stored in database)
```java
public void testGetFlightsByOriginAirport_NoFlights() throws Exception {
   // Fixture setup
   BigDecimal outboundAirport = createTestAirport("1OF");
   // Exercise System
   try {
      // Exercise System
      List flightsAtDestination1 = facade.getFlightsByOriginAirport(outboundAirport);
      // Verify Outcome
      assertEquals(0, flightsAtDestination1.size());
   } finally {
      // Inline Teardown
      if (outboundAirport != null) {
         facade.removeAirport(outboundAirport);
      }
   }
}
```
- `Implicit Teardown` => good to go side by side with `Implicit setup`, then leaves 2 parts `exericise SUT` & `result verification` in `Test method`.
- `Testcase Class per Class` => 1 test class for each class, good when we don't have very many `test methods`.
- `Testcase Class per Feature` => good when we have significant number of test methods and we want to specification of each feature of SUT more obvious. Note that if we have a large number of features on a class, that indicating class is "smell" (class should be "single of responsibility").
- `Testcase Class per Fixture` => good to find which cases we missed

# Tech hashtags
**#UnitTest**, **#Given-Then-When**, **#Testing-Template**, **#3A–Arrange-Act-Assert**, **#Four-Phase-Test**

> Kent Beck: "Let the code tell you what to do!"
