# Testing Considerations

- Flag tightly coupled code that is difficult to unit test (e.g., `new`-ing up dependencies directly instead of injecting them).
- Ensure public APIs have clear contracts that are testable.
- Prefer **parameterized tests** (a single test method with input parameters and expected output) over duplicating test methods for each case. This reduces boilerplate, improves maintainability, and makes it easier to add new test cases.

### Parameterized Test Examples

**xUnit — `[Theory]` with `[InlineData]`:**
```csharp
public class CalculatorTests
{
    [Theory]
    [InlineData(2, 3, 5)]
    [InlineData(0, 0, 0)]
    [InlineData(-1, 1, 0)]
    public void Add_ReturnsExpectedSum(int a, int b, int expected)
    {
        var result = Calculator.Add(a, b);
        Assert.Equal(expected, result);
    }
}
```

**NUnit — `[TestCase]`:**
```csharp
public class CalculatorTests
{
    [TestCase(2, 3, ExpectedResult = 5)]
    [TestCase(0, 0, ExpectedResult = 0)]
    [TestCase(-1, 1, ExpectedResult = 0)]
    public int Add_ReturnsExpectedSum(int a, int b)
    {
        return Calculator.Add(a, b);
    }
}
```

**MSTest — `[DataTestMethod]` with `[DataRow]`:**
```csharp
[TestClass]
public class CalculatorTests
{
    [DataTestMethod]
    [DataRow(2, 3, 5)]
    [DataRow(0, 0, 0)]
    [DataRow(-1, 1, 0)]
    public void Add_ReturnsExpectedSum(int a, int b, int expected)
    {
        var result = Calculator.Add(a, b);
        Assert.AreEqual(expected, result);
    }
}
```

For complex input data that cannot be expressed as inline constants, use `[MemberData]` (xUnit), `[TestCaseSource]` (NUnit), or `[DynamicData]` (MSTest) to supply test cases from a method or property.
