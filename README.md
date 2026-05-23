# API-Testing-Collection

REST Countries API — Full Automation Suite

A fully layered REST API test automation suite built with **REST Assured**,
**TestNG**, and **ExtentReports**, validating the
[REST Countries v3.1 API](https://restcountries.com/) across 44 tests
covering schema validation, data integrity, region filtering, language
lookup, currency lookup, and negative error handling.

---

## 📋 Project Overview

This project demonstrates professional-grade API test automation without
a browser. Every endpoint exposed by the REST Countries API is exercised
across six test classes, each with a distinct responsibility. Responses
are deserialised into typed Java model objects using Jackson, replacing
raw `Map<String, Object>` chains with clean field access. A rich
self-contained HTML report is generated after every run showing pass/fail
status, request URLs, status codes, and truncated response bodies for
every test.

The suite is built in layers — base configuration, HTTP client, test data,
typed models, listeners, utilities, and test classes are all fully
separated. A change to the API base URL is a one-line fix in `BaseTest`.
A change to a country's capital is a one-line fix in `TestData`. No test
class contains a hardcoded string or a raw HTTP call.

---

## 🏗️ Project Structure
RestCountriesAPI/
│
├── pom.xml                              # Dependencies and Surefire config
├── testng.xml                           # Suite file — all 6 test classes
├── README.md
│
└── src/test/
├── java/
│   ├── base/
│   │   └── BaseTest.java            # RequestSpec + ResponseSpec setup
│   ├── clients/
│   │   └── CountryClient.java       # All HTTP calls centralised here
│   ├── data/
│   │   ├── Endpoints.java           # All API paths and schema paths
│   │   └── TestData.java            # Country names, codes, expected values
│   ├── listeners/
│   │   └── ExtentReportListener.java # TestNG → Extent HTML report
│   ├── models/
│   │   └── Country.java             # Typed Jackson response model
│   ├── tests/
│   │   ├── SchemaValidationTest.java  # 3 tests
│   │   ├── CountryDataTest.java       # 11 tests
│   │   ├── RegionTest.java            # 8 tests
│   │   ├── LanguageTest.java          # 8 tests
│   │   ├── CurrencyTest.java          # 7 tests
│   │   └── NegativeTest.java          # 7 tests
│   └── utils/
│       ├── ExtentReportManager.java   # Singleton Extent instance
│       └── ResponseLogger.java        # Logs request/response to report
└── resources/
└── schemas/
├── all-names-schema.json      # Schema for /all?fields=name
└── single-country-schema.json # Schema for single country object

---

## ✅ Test Scenarios — 44 Tests Total

### `SchemaValidationTest` — JSON Schema Compliance (3 tests)

Validates that response bodies conform to predefined JSON Schema
definitions stored in `src/test/resources/schemas/`. Schema tests run
first — if a field is removed or renamed in the API, these fail
immediately before any data assertions run.

| # | Test | What it validates |
|---|---|---|
| 1 | `testAllNamesSchemaValidation` | Every country has `name.common` and `name.official` string fields |
| 2 | `testSingleCountrySchemaValidation` | Single country response has all required top-level fields |
| 3 | `testRegionResponseSchemaValidation` | Region array response matches the names schema |

---

### `CountryDataTest` — Field Value Assertions (11 tests)

Validates specific field values returned by the API — names, capitals,
regions, populations, currencies, and culturally specific data.
Responses are deserialised into `Country[]` using Jackson for clean
typed field access.

| # | Test | What it validates |
|---|---|---|
| 1 | `testTotalCountryCountIs195` | `/all` returns exactly 195 countries |
| 2 | `testSouthAfricaBasicData` | Common name, region, and subregion are correct |
| 3 | `testSouthAfricaCapital` | Capital list includes Pretoria |
| 4 | `testSouthAfricaPopulation` | Population is greater than 50 million |
| 5 | `testSouthAfricaCurrency` | Currency map contains ZAR |
| 6 | `testSouthAfricanSignLanguageInNativeName` | `sasl` key present in `nativeName` map |
| 7 | `testGermanyData` | Capital is Berlin and region is Europe |
| 8 | `testGermanyCurrency` | Currency map contains EUR |
| 9 | `testJapanData` | Capital is Tokyo and region is Asia |
| 10 | `testCountryLookupByCodeZA` | ISO code ZA returns South Africa |
| 11 | `testCountryLookupByCodeDE` | ISO code DE returns Germany |

---

### `RegionTest` — Region & Subregion Filtering (8 tests)

Validates the `/region` and `/subregion` endpoints — not just that they
return data, but that every country in the response actually belongs to
the requested region.

| # | Test | What it validates |
|---|---|---|
| 1 | `testAfricaRegionReturnsCountries` | Africa returns a non-empty list |
| 2 | `testAllAfricaCountriesHaveCorrectRegion` | Every result has `region = Africa` |
| 3 | `testSouthAfricaInAfricaRegion` | South Africa is present |
| 4 | `testNigeriaInAfricaRegion` | Nigeria is present |
| 5 | `testEuropeRegionReturnsCountries` | Europe returns a non-empty list |
| 6 | `testGermanyInEuropeRegion` | Germany is present |
| 7 | `testSouthernAfricaSubregion` | Every result has `subregion = Southern Africa` |
| 8 | `testJapanInAsiaRegion` | Japan is present in Asia |

---

### `LanguageTest` — Language Endpoint (8 tests)

Validates the `/lang/{code}` endpoint using ISO 639-3 language codes.
Includes a culturally specific test asserting South Africa's 11
constitutionally recognised official languages are all present.

| # | Test | What it validates |
|---|---|---|
| 1 | `testZuluLanguageReturnsCountries` | Zulu (zul) returns a non-empty list |
| 2 | `testSouthAfricaInZuluLanguage` | South Africa present in Zulu results |
| 3 | `testSouthAfricaInAfrikaansLanguage` | South Africa present in Afrikaans results |
| 4 | `testAllGermanCountriesSpeakGerman` | Every German (deu) result lists `deu` in languages |
| 5 | `testGermanyInGermanLanguage` | Germany present in German results |
| 6 | `testJapanInJapaneseLanguage` | Japan present in Japanese (jpn) results |
| 7 | `testBrazilInPortugueseLanguage` | Brazil present in Portuguese (por) results |
| 8 | `testSouthAfricaHas11OfficialLanguages` | South Africa language map has at least 11 entries |

---

### `CurrencyTest` — Currency Endpoint (7 tests)

Validates the `/currency/{code}` endpoint using ISO 4217 currency codes.
Tests verify both that the correct countries appear and that the currency
data object contains the expected `name` and `symbol` fields.

| # | Test | What it validates |
|---|---|---|
| 1 | `testZARCurrencyReturnsCountries` | ZAR returns a non-empty list |
| 2 | `testSouthAfricaInZARCurrency` | South Africa present in ZAR results |
| 3 | `testAllZARCountriesHaveZARCurrency` | Every ZAR result actually lists ZAR |
| 4 | `testGermanyInEURCurrency` | Germany present in EUR results |
| 5 | `testEURUsedByMoreThan10Countries` | EUR is used by more than 10 countries |
| 6 | `testJapanInJPYCurrency` | Japan present in JPY results |
| 7 | `testZARCurrencyHasNameAndSymbol` | ZAR object has `name` and `symbol` fields |

---

### `NegativeTest` — Error Handling (7 tests)

Validates that every endpoint that accepts user input returns the correct
404 response and error body structure for invalid input. Negative tests
confirm the API fails gracefully — no 500 errors, no unexpected data.

| # | Test | What it validates |
|---|---|---|
| 1 | `testInvalidCountryNameReturns404` | Invalid name → 404 |
| 2 | `testInvalidCountryCodeReturns404` | Invalid code → 404 |
| 3 | `testInvalidRegionReturns404` | Invalid region → 404 |
| 4 | `testInvalidLanguageCodeReturns404` | Invalid language code → 404 |
| 5 | `testInvalidCurrencyCodeReturns404` | Invalid currency code → 404 |
| 6 | `testEmptyNameSearchDoesNotReturn200` | Empty search string → not 200 |
| 7 | `test404ResponseBodyContainsStatusField` | Error body contains `status: 404` |

---

## 🛠️ Tools & Technologies

| Tool | Version | Purpose |
|---|---|---|
| **Java** | 21 | Core programming language |
| **REST Assured** | 5.4.0 | HTTP client and fluent assertion library |
| **REST Assured JSON Schema Validator** | 5.4.0 | JSON Schema contract validation |
| **TestNG** | 7.11.0 | Test framework, assertions, listeners |
| **ExtentReports** | 5.1.2 | Self-contained HTML test report |
| **Jackson Databind** | 2.17.1 | JSON → typed Java model deserialisation |
| **Apache Commons IO** | 2.15.1 | Report directory handling |
| **Maven** | — | Dependency management and build |

---

## 💡 Concepts Demonstrated

### Architecture & Design

**Layered architecture** — the project is split into six distinct layers:
base configuration, HTTP client, test data, typed models, event listeners,
utilities, and test classes. Each layer has one responsibility and no
layer reaches into another's concern.

**Single Responsibility Principle** — `CountryClient` handles HTTP calls,
`TestData` holds expected values, `Endpoints` holds URL paths, `Country`
models the response, `ResponseLogger` handles report output. No class does
more than one job.

**Constants-driven testing** — `TestData.java` and `Endpoints.java` hold
every string, number, and path used across the suite. Test classes contain
zero hardcoded values. A country's capital or an endpoint path changes in
one file and every test that references it is updated automatically.

---

### REST Assured

**RequestSpecification / ResponseSpecification** — the base URL, content
type, and logging are configured once in `BaseTest` and assigned to
`RestAssured.requestSpecification` and `RestAssured.responseSpecification`.
Every test class inherits them without any explicit setup.

**Fluent chain** — every request follows the
`.given()` → `.when()` → `.then()` pattern, making each test readable as
a plain English sentence: given these headers, when I call this endpoint,
then I expect this response.

**`.extract().as(Country[].class)`** — REST Assured deserialises the JSON
response body directly into a typed Java array using Jackson. No raw
`Map<String, Object>` casts appear anywhere in the test classes.

**`.jsonPath().getInt()`** — used in negative tests to read specific
fields from error response bodies without full deserialisation.

**`matchesJsonSchemaInClasspath()`** — schema files stored in
`src/test/resources/schemas/` are validated against the full response
body in one assertion, covering the entire structure at once.

---

### JSON Schema Validation

Schema files define the expected structure of each response — field names,
types, and which fields are required. Schema tests run first in the suite.
If a field is removed or its type changes in the API, schema tests fail
immediately and clearly before any data assertion tests run, giving a
fast and specific signal about what broke.

---

### Typed Models with Jackson

`Country.java` is a Jackson-annotated POJO with `@JsonIgnoreProperties(ignoreUnknown = true)`
so fields present in the API but not needed by the tests are silently
ignored. The nested `Name` inner class models the `name` object including
the deeply nested `nativeName` map. Tests access data as
`country.getName().getCommon()` rather than
`((Map) ((Map) response.get(0)).get("name")).get("common")`.

---

### Filtering Accuracy Testing

Most API test suites assert "did the endpoint return something." This
suite goes further — for every filter endpoint (region, language,
currency) it asserts that every country in the response actually matches
the filter. If `/region/Africa` returns a country with `region = Europe`,
the test fails and names the offending country in the assertion message.

---

### Negative Testing

Seven dedicated negative tests cover all endpoints that accept user input.
Each one asserts the correct 404 status code and the correct error body
structure. This confirms the API handles invalid input gracefully and
does not return unexpected data, redirect silently, or throw a 500.

---

### Reporting

**Singleton ExtentReports** — `ExtentReportManager` creates the shared
instance once and all test classes write into the same report file.

**ITestListener** — `ExtentReportListener` hooks into TestNG's
`onTestStart`, `onTestSuccess`, `onTestFailure`, `onTestSkipped`, and
`onFinish` events without touching any test class. Tests are unaware of
the reporting layer.

**`ResponseLogger`** — every test logs its request URL, status code, and
a truncated response body directly into its report entry. The HTML report
is self-contained — a reviewer can see exactly what was called and what
came back without re-running anything.

**ThreadLocal<ExtentTest>** — parallel-safe report entries using one
`ExtentTest` reference per thread.

---

## 📊 HTML Report

After every run a self-contained HTML report is generated at:
reports/ExtentReport.html

Open it in any browser — no server required. Each entry shows:

- ✅ Pass / ❌ Fail / ⏭️ Skip status
- Request URL called
- HTTP status code returned
- Truncated response body
- Full exception and stack trace on failure
- Environment panel: API version, base URL, tester

---

## 🚀 How to Run

### Prerequisites
- Java 21+
- Maven

```bash
# Run all 44 tests
mvn test

# Run a single test class
mvn test -Dtest=NegativeTest
mvn test -Dtest=CountryDataTest
mvn test -Dtest=RegionTest
mvn test -Dtest=LanguageTest
mvn test -Dtest=CurrencyTest
mvn test -Dtest=SchemaValidationTest
```

Open the report after the run:
reports/ExtentReport.html

> **Note:** Run via `mvn test` or point IntelliJ at `testng.xml` via
> **Run → Edit Configurations → Test kind: Suite**. Running individual
> classes directly from the IntelliJ play button bypasses `testng.xml`
> and the listener, so no report will be generated.

---

## 🌍 API Reference

All tests run against the public [REST Countries v3.1 API](https://restcountries.com/).
No authentication is required. No data is written — all requests are
read-only GET calls.

| Endpoint | Description |
|---|---|
| `/all` | All countries — all fields |
| `/all?fields=name` | All countries — name only |
| `/name/{name}` | Search by country name |
| `/alpha/{code}` | Search by ISO country code |
| `/region/{region}` | Filter by continental region |
| `/subregion/{subregion}` | Filter by subregion |
| `/lang/{code}` | Filter by ISO 639-3 language code |
| `/currency/{code}` | Filter by ISO 4217 currency code |
