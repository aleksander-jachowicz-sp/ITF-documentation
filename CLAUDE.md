# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **documentation repository** for ITF (IdentityIQ Testing Framework) - a testing framework for SailPoint IdentityIQ. The project uses **MkDocs** with the ReadTheDocs theme to generate static documentation from Markdown files.

ITF is a Java-based testing framework that allows automated testing of IdentityIQ configurations using XML-based test cases. It supports:
- Loading test data (identities, roles, objects)
- Executing test actions via commands
- Validating results
- Simulating provisioning
- Testing workflows and approvals

## Build and Development Commands

### Build and serve documentation locally
```bash
mkdocs serve
```
This starts a local development server (typically on http://127.0.0.1:8000/)

### Build static documentation
```bash
mkdocs build
```
Generates static HTML in the `site/` directory

### Deploy to Read the Docs
The project is configured with `.readthedocs.yaml` for automatic deployment to Read the Docs platform. Build configuration:
- Python 3.11
- Ubuntu 22.04
- Outputs: HTML zip and PDF formats

## Documentation Structure

The documentation is organized into logical sections (see `mkdocs.yml`):

1. **Introduction** - Core testing concepts
2. **Installation** - Setup for ITF, plugins, IDE configuration
3. **Reference** - Command reference, connectivity, date handling
4. **IntelliJ Plugin** - IDE plugin capabilities
5. **Release Notes** - Version history

All content is in the `docs/` directory with Markdown files. Custom CSS is in `docs/css/itf_doc.css`.

## Key Documentation Files

- `docs/ITF Testing concepts.md` - Core framework concepts (3-part test structure: load data, execute actions, validate)
- `docs/ITF XML Reference.md` - Complete XML command reference (main technical reference, 1594 lines)
- `docs/ITF Installation and configuration.md` - Installation overview
- `docs/IIQ connectivity.md` - IdentityIQ connectivity configuration
- `docs/Pseudo date handling.md` - Dynamic date handling in test cases
- `docs/Learn by example.md` - Example test cases

## ITF Framework Architecture

### Test Case Structure
ITF test cases are XML files with three main phases:
1. **Load test data** - Import test identity and related objects via `<TestIdentity>` and `<ObjectsToLoad>`
2. **Execute commands** - Run test actions in `<TestCommands>` section
3. **Validate results** - Use validation commands like `ValidateResult` or `CheckAudit`

### Object Loading Order
1. Objects in `<ObjectsToLoad>` (in order)
2. Approvers from `CheckApprovals` commands
3. `<TestIdentity>`
4. Objects from `LoadObjects` commands

### Directory Structure
- `itf/lib/` - ITF binaries (AutomatedTester.x.y.jar)
- `itf/resources/` - Properties files and logging config
- `itf/schema/` - JSON schema for test cases
- `itf/testData/` - Default location for test data
- `itf/test/` - JUnit test files

## Common ITF Commands

### Data Loading
- `LoadObjects` - Load objects during test execution
- `mockedAggregate` - Aggregate from JSON/XML files
- `Aggregate` - Real aggregation from target systems

### Provisioning
- `Assign` - Assign roles, entitlements, links, workgroups
- `Provision` - Execute provisioning plan via Provisioner
- `SimulateProvisioning` - Simulate external provisioning

### Validation
- `ValidateResult` - Validate identity/link attributes
- `CheckAudit` - Validate audit events
- `CheckApprovals` - Check and execute workflow approvals
- `ValidateXpath` - Validate any object attribute via XPath

### Workflow
- `RunWfl` - Run workflows with parameters
- `WaitForWflFinish` - Wait for workflow completion
- `WaitForWflStep` - Wait for specific workflow step
- `Workflower` - Process backgrounded workflow cases
- `CheckApprovals` - Complex approval validation/execution

### Utilities
- `Clean` - Delete test data
- `TimeMachine` - Advance IdentityIQ time
- `ProcessEvents` - Trigger lifecycle events
- `CheckEmail` - Validate email notifications
- `RunTask` - Execute IdentityIQ tasks
- `RunRule` / `RunScript` - Execute BeanShell code

## Documentation Writing Guidelines

When modifying documentation:
- Use Markdown format
- Reference related pages using relative links
- Keep technical accuracy - this is reference documentation
- Include XML examples for commands with proper indentation
- Use code blocks with language hints (xml, java, bash)
- Document both attributes and nested elements for commands
- Explain object loading order and dependencies