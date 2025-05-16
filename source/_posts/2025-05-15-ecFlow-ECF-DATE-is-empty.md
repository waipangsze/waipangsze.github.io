---
layout: post
title: ecFlow | ECF_DATE is empty?
categories: [ecFlow]
tags: [ecFlow, Spack, Micromamba, Conda, WRF, MPAS]
author: wpsze
date: 2025-05-15 13:58:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://ecflow.readthedocs.io/en/latest/_images/image1.png
banner_img: https://ecflow.readthedocs.io/en/latest/_images/image1.png
---

- [ecFlow -- Installation & Usage](https://waipangsze.github.io/2024/06/07/ecflow/)

---

# Key Points

- It seems likely that `ECF_DATE` is empty because the suite definition in `ecFlow` lacks a clock attribute, which is needed to generate the date.
- Research suggests that defining a `clock` (`real` or `hybrid`) in the suite is essential for `ECF_DATE` to be populated.
- The evidence leans toward ensuring the clock is correctly set in either text-based or Python-based suite definitions.

## Background on ecFlow and ECF_DATE

ecFlow, developed by the European Centre for Medium-Range Weather Forecasts (ECMWF), is a client/server workflow management system designed to handle complex workflows, especially in meteorological and environmental simulations. It enables the execution of numerous programs with dependencies on each other and on time, providing robustness against hardware and software failures with restart capabilities. A critical component of ecFlow is its ability to manage time-dependent tasks, which relies on generated variables such as ECF_DATE.
 
`ECF_DATE` is a generated variable that represents the current date of the suite's clock, formatted typically as YYYYMMDD. This variable is essential for scheduling and triggering tasks based on date dependencies. The documentation, such as the "Dates and Clocks" page ([Dates and Clocks](https://confluence.ecmwf.int/display/ECFLOW/Dates%2Band%2BClocks)), indicates that ECF_DATE is derived from the suite's clock, which can be either real (synchronized with the system clock) or hybrid (fixed date with system time).

Once a suite is complete, it is repeated automatically, with the next date. The value of the date is contained in the ecFlow variable ECF_DATE, and the value of the time is in ECF_TIME. ECF_CLOCK contains other information such as the day of week. A job should always use the ecFlow variables, and not directly access the system date.
If a hybrid clock is defined for a suite, any node held by a date dependency will be set to complete at the beginning of the suite, without the node ever being despatched. Otherwise the suite would never complete.

The documentation, such as the "Generated Variables" page ([Generated Variables](https://confluence.ecmwf.int/display/ECFLOW/Generated%2BVariables)), reinforces that variables like ECF_DATE are available at the suite level and may be overridden, but their generation depends on the clock. The "Variables and Substitution" page ([Variables and Substitution](https://confluence.ecmwf.int/display/ECFLOW/Variables%2Band%2BSubstitution)) further notes that undefined variables can cause tasks to abort, which aligns with the expectation that an empty ECF_DATE would disrupt date-dependent operations.

## Why ECF_DATE Might Be Empty

`ECF_DATE` is a generated variable in `ecFlow` that relies on the **suite's clock to provide the current date**. If this clock isn't defined, `ECF_DATE` will remain empty, affecting time-dependent tasks in your workflow.
 
## How to Fix It

To resolve this, add a clock to your suite definition:

- For text-based definitions, use `clock real` or `clock hybrid` at the suite level.
- For Python API, use `suite.add(Clock("real"))` or similar, depending on your setup.

| **Clock Type** | **Description**                          | **Impact on ECF_DATE**                     |
|----------------|------------------------------------------|--------------------------------------------|
| Real           | Synchronized with system clock (UTC)     | ECF_DATE reflects current system date      |
| Hybrid         | Fixed date, time follows system clock    | ECF_DATE is fixed for suite duration       |

- **Text-Based Suite Definition**:
  - Add the `clock` keyword to the suite definition. For example:
    ```
    suite my_workflow
    clock real
    # Other definitions...
    endsuite
    ```
  - The clock can be set to `real` for system synchronization or `hybrid` for a fixed date, as seen in examples from the "ecFlow@ECMWF" page.
  - After updating, reload the suite using `ecflow_client --load your_suite.def` to ensure the server recognizes the changes.
 
- **Python API**:
  - Use the `Clock` class from the ecFlow Python API. For instance:
    ```python
    from ecflow import Suite, Clock
 
    suite = Suite("my_workflow")
    suite.add(Clock("real"))
    # Other definitions...
    ```
  - The GitHub documentation for the Clock class ([Clock Class](https://github.com/ecmwf/ecflow/blob/develop/docs/python_api/Clock.rst)) indicates it specifies the clock type used by the suite, with options for gain to offset the date, which is useful for simulations running on historical data.

## Checking Your Setup

After updating, reload the suite into the ecFlow server and verify `ECF_DATE` using the `ecFlow UI` or **CLI command** `ecflow_client --get /your_suite | grep ECF_DATE`.

# My ECF_DATE issue

## ecf_server/checkpoint files

**Question:** `ECF_DATE` is empty (no value).

This issue typically indicates a problem with the **checkpoint file**, which contains the status information for the ecflow workflow. **When this file is corrupted or improperly configured**, it caseues the workflow from operating normally.

**Steps to Resolve:**

1. **Remove Checkpoint Files:**

   - Navigate to the `ecflow_server` directory. This is where the checkpoint files are stored.
   - Delete any existing checkpoint files. This action clears any corrupted or outdated status information that may be causing the issue.

2. **Restart the Server:**

   - Run the command `./start.sh` in your terminal. This script will restart the ecflow server and reinitialize the workflow environment.

3. **Restart the Workflow:**

   - Open the `ecflow_ui` (the Ecflow User Interface).
   - Select the **"Restart"** option from the menu, and then choose "**Begin**". This will initiate the workflow from a fresh state, allowing it to proceed without the previous checkpoint issues.

By following these steps, you should be able to resolve the empty `ECF_DATE` issue and restore normal functionality to the ecflow workflow. 

![](https://i.imgur.com/YLNenhb.png)
![](https://i.imgur.com/dZZ2gwP.png)

# Reference

1. [日期和时钟 | ecFlow教程](https://perillaroc.github.io/ecflow-tutorial-cn/chap04/data-and-clock/)
2. [Dates and Clocks | ecmwf](https://confluence.ecmwf.int/display/ECFLOW/Dates+and+Clocks)
3. [Dates and Clocks ecFlow ECMWF Confluence Wiki](https://confluence.ecmwf.int/display/ECFLOW/Dates%2Band%2BClocks)
4. [ecFlow@ECMWF ecFlow ECMWF Confluence Wiki](https://confluence.ecmwf.int/display/ECFLOW/ecFlow%40ECMWF)
5. [Generated Variables ecFlow ECMWF Confluence Wiki](https://confluence.ecmwf.int/display/ECFLOW/Generated%2BVariables)
6. [Variables and Substitution ecFlow ECMWF Confluence Wiki](https://confluence.ecmwf.int/display/ECFLOW/Variables%2Band%2BSubstitution)
7. [date ecFlow ECMWF Confluence Wiki](https://confluence.ecmwf.int/display/ECFLOW/date)
8. [Using ecflow_ui ecFlow ECMWF Confluence Wiki](https://confluence.ecmwf.int/display/ECFLOW/Using%2Becflow_ui)
9. [Simulation ecFlow ECMWF Confluence Wiki](https://confluence.ecmwf.int/display/ECFLOW/Simulation)
10. [Time Triggers ecFlow ECMWF Confluence Wiki](https://confluence.ecmwf.int/display/ECFLOW/Time%2BTriggers)
11. [Clock Class ecFlow GitHub Documentation](https://github.com/ecmwf/ecflow/blob/develop/docs/python_api/Clock.rst)