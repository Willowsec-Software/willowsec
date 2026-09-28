# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

willowsec is a Java library of security building blocks, focused primarily on IAM (identity and
access management), with additional modules for secure API development and cryptographic
primitives.

## Commands

Build tool: Maven. Java version: 21.

```
mvn clean install                          # build all modules, run all tests
mvn test                                   # run all tests without installing
mvn -pl <module> test                      # run tests for one module only, e.g. mvn -pl iam test
mvn -pl <module> test -Dtest=ClassName                # run a single test class
mvn -pl <module> test -Dtest=ClassName#methodName     # run a single test method
mvn -pl <module> -am test                  # run one module's tests, building its dependencies first
```

`<module>` is one of: `common`, `crypto`, `iam`, `api-security`.

## Architecture

This is a multi-module Maven project (root `pom.xml` is `packaging: pom` and holds shared
`dependencyManagement`). Each module is a separate Maven artifact under group `com.willowsec`,
with Java packages rooted at `com.willowsec.<module>` (api-security's package is
`com.willowsec.apisecurity`).

Module dependency graph (a module may only depend on modules listed below it):

```
api-security  →  iam  →  crypto  →  common
```

- **common**: shared utilities and exception types with no dependency on any other willowsec
  module. Anything needed by two or more modules belongs here, not duplicated.
- **crypto**: cryptographic primitives (encryption, hashing, key management), built on
  BouncyCastle (`bcprov-jdk18on`). Other modules should call into `crypto` for any raw
  cryptographic operation rather than using `java.security`/JCA directly, so algorithm choices
  and provider setup stay centralized.
- **iam**: authentication, authorization, and token handling. Uses Nimbus JOSE+JWT
  (`com.nimbusds:nimbus-jose-jwt`) for JWT/JOSE support and depends on `crypto` for underlying
  key and signing operations rather than reimplementing them.
- **api-security**: secure API development helpers (request validation, security headers, rate
  limiting) that sit on top of `iam` for authentication/authorization concerns.

New third-party dependencies should be added to the root `pom.xml`'s
`<dependencyManagement>` block with a version property, then referenced without a version in the
module's own `pom.xml`, matching the existing modules.
