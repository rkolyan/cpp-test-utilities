# C++ Test Utilities interface

This repository contains a common interface for the test utilities used by the different Systelab projects.


## Entity comparators

Entity comparators allow adding an equality expectation for Google Test over two model entities. When the entities are different, the comparator will  provide a detailed message with the first difference found. So, they are very useful for troubleshooting failures on unit tests or integration tests.

For instance, given a `Patient` model entity:

```cpp
struct Patient
{
    int id;
    std::string name;
    int age;
    bool male;
    double height;
};
```

An entity comparator can be defined by implementing the `operator()`:

```cpp
namespace systelab { namespace test_utility {

    template <>
    testing::AssertionResult EntityComparator::operator() (const Patient& expected, const Patient& actual) const
    {
        if (expected.id != actual.id)
        {
            return AssertionFailure() << "Different value for id: expected=" << expected.id
                                      << ", actual=" << actual.id;
        }

        if (expected.name != actual.name)
        {
            return AssertionFailure() << "Different value for name: expected=" << expected.name
                                      << ", actual=" << actual.name;
        }

        if (expected.age != actual.age)
        {
            return AssertionFailure() << "Different value for age: expected=" << expected.age
                                      << ", actual=" << actual.age;
        }

        ...

        return AssertionSuccess();
    }
```

Then it can be used on a unit test as follows:

```cpp
TEST_F(PatientTest, testPatientsAreEqual)
{
    Patient expectedEntity = {1, "Nicolas Cage", 55, 1.81, true};
    Patient actualEntity = {1, "Nicolas Cage", 51, 1.81, true};
    ASSERT_TRUE(systelab::test_utility:EntityComparator()(expectedEntity, actualEntity));
}
```

The execution of this test should print something like:

```cpp
Different value for age: expected=55, actual=51
```

### Helper macros

Boilerplate code on entity comparators implementations can be reduced significantlly by using some helper macros. They allow performing simple comparisons within a single line of code:

```cpp
// Compares an expression result by asserting it to be equal
COMPARATOR_ASSERT_EQUAL(expected, actual, name);
COMPARATOR_ASSERT_EQUAL(expected, actual, age); 
COMPARATOR_ASSERT_EQUAL(expected, actual, male);

// Compares double/float expression result by asserting it to be near (using a given tolerance)
COMPARATOR_ASSERT_NEAR(expected, actual, height, 1e-4);                                                   
```

### isEqualTo() matcher

Entity comparators can also be used when checking arguments for Google Mock function call expectations. Using the `isEqualTo` matcher, wrong method invokations can be troubleshooted:

```cpp
Patient expectedEntity = {2, "Isabel Pantoja", 62, 1.75, false};
EXPECT_CALL(service, createPatient(isEqualTo(expectedPatient)));
```

### Comparison configuration

It is possible to configure an entity comparator to exclude the comparison of a certain members. In order to do so, a configuration object needs to be provided when invoking the `() operator`:

```cpp
systelab::test_utilitiy::EntityComparatorConfiguration configuration;
ASSERT_TRUE(systelab::test_utility:EntityComparator(configuration)(expectedEntity, actualEntity));
```

Aside from that, the implementation of the comparator needs to name the comparisons susceptible to be excluded. This can be achieved by directly accessing to the configuration object or by using the `COMPARATOR_ASSERT_EQUAL_WITH_EXCLUSION` macro:





