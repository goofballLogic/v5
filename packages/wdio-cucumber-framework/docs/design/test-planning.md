# Test planning

## Break down
Tests are broken down into Must, Should and Could categories. The first delivery will only be the _Must_ tagged tests as this will unblock users currently struggling with the v4 implementation.

# "Must" tests

The following are features of reporting which must be addressed for the reporting to be useable for the most basic use cases:

### Supporting the most basic gherkin features

#### Feature
- The title of the feature must be reported

#### Given, When, Then, And
- These steps must raise the following events:
    - test:start
    - test:pass / test:fail / test:pending
    - test:end
- 
