The **Code Quality** stage in a CI/CD pipeline analyzes the source code **without running the application** (static code analysis). Its goal is to identify bugs, security vulnerabilities, coding standard violations, duplicated code, and maintainability issues before deployment.

The most common tools are:

* **SonarQube** (most widely used)
* Checkstyle (Java coding standards)
* PMD
* SpotBugs (FindBugs)
* ESLint (JavaScript)
* Pylint (Python)

---

# CI/CD Flow

```text
Developer
     │
Git Push
     │
Jenkins/GitHub Actions
     │
mvn clean package
     │
mvn test
     │
SonarQube Scan
     │
Quality Gate
     │
Pass → Docker Build
Fail → Stop Pipeline
```

---

# How is the report generated?

For a Java Maven project, the pipeline typically runs:

```bash
mvn clean verify sonar:sonar \
-Dsonar.host.url=http://sonarqube.company.com \
-Dsonar.login=<token>
```

Or in GitHub Actions/Jenkins:

```bash
mvn sonar:sonar
```

The Sonar scanner:

1. Reads the source code.
2. Parses the Java classes.
3. Applies hundreds of predefined rules.
4. Collects unit test and coverage reports (if available).
5. Uploads the analysis to the SonarQube server.
6. SonarQube generates a dashboard and evaluates the Quality Gate.

---

# What does it check?

## 1. Bugs

Looks for code that may behave incorrectly.

Example:

```java
String s = null;
System.out.println(s.length());
```

Issue:

> Possible NullPointerException.

---

## 2. Vulnerabilities (Security)

Detects insecure coding practices.

Example:

```java
Statement stmt = connection.createStatement();
stmt.execute("SELECT * FROM users WHERE id=" + userInput);
```

Issue:

> SQL Injection vulnerability.

---

## 3. Code Smells

These aren't bugs but indicate poor design or maintainability.

Example:

```java
if(a){
}
else{
}
```

Empty blocks, long methods, deeply nested `if` statements, duplicate logic, and poor naming are common code smells.

---

## 4. Duplicated Code

Example:

```java
calculateTax();

calculateTax();
```

If the same logic appears in multiple places, SonarQube reports duplicate code and suggests refactoring.

---

## 5. Code Coverage

Usually measured using **JaCoCo**.

Example:

```java
public int add(int a,int b){
    return a+b;
}
```

If there are no unit tests for this method, coverage will be low.

Typical Quality Gate:

```text
Coverage >= 80%
```

---

## 6. Complexity

Measures how difficult code is to understand and test.

Example:

```java
if()
{
   if()
   {
      if()
      {
         while()
         {
              switch()
```

High cyclomatic complexity makes maintenance harder.

---

## 7. Coding Standards

Checks naming conventions and formatting.

Example:

Bad:

```java
int A=10;
```

Better:

```java
int employeeCount = 10;
```

---

## 8. Unused Code

Example:

```java
private int salary;
```

If `salary` is never used, it will be reported as dead code.

---

## 9. Resource Leaks

Example:

```java
FileInputStream fis = new FileInputStream("abc.txt");
```

If `fis.close()` is never called, the analyzer reports a resource leak.

---

## 10. Exception Handling

Example:

```java
catch(Exception e)
{
}
```

Swallowing exceptions with an empty `catch` block is flagged.

---

## 11. Hardcoded Secrets

Example:

```java
String password="admin123";
```

This is reported as a security issue because secrets should come from a secure store, not source code.

---

## 12. Commented-Out Code

Example:

```java
// int salary=10000;
```

Large blocks of commented code are usually reported as code smells.

---

# Typical SonarQube Dashboard

A report usually includes:

```text
Project Name

Reliability : A

Security : A

Maintainability : B

Coverage : 85%

Duplications : 2%

Bugs : 3

Vulnerabilities : 0

Code Smells : 15

Security Hotspots : 5
```

---

# What is a Quality Gate?

A Quality Gate is a set of pass/fail conditions.

Example:

```text
Coverage > 80%

Bugs = 0

Vulnerabilities = 0

Code Smells < 20

Duplicated Code < 3%
```

If any condition fails:

```text
Quality Gate FAILED
```

The CI/CD pipeline stops before building or deploying.

---

# Difference between Checkstyle and SonarQube

| Checkstyle                         | SonarQube                                |
| ---------------------------------- | ---------------------------------------- |
| Checks coding style and formatting | Comprehensive static analysis platform   |
| Naming conventions                 | Bugs                                     |
| Indentation                        | Vulnerabilities                          |
| Braces and whitespace              | Code smells                              |
| Imports                            | Code duplication                         |
| Simple rule checks                 | Test coverage, complexity, quality gates |

---

## Interview Answer (2–3 minutes)

> "In our CI/CD pipeline, after the build and unit test stages, we run static code analysis using SonarQube. The Sonar scanner analyzes the source code and applies predefined rules to detect bugs, security vulnerabilities, code smells, duplicate code, high complexity, and coding standard violations. It also imports unit test coverage from tools like JaCoCo. Based on predefined Quality Gate conditions—for example, no critical vulnerabilities, no blocker bugs, and minimum 80% coverage—the pipeline either proceeds to the Docker build and deployment stages or stops if the quality criteria are not met."
