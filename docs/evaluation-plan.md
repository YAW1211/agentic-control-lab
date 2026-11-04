# Evaluation Plan

The following tests are planned. No measured results are available yet.

## Test cases

| Test | Expected behavior | Evidence |
| --- | --- | --- |
| Read temperature | Return the latest valid reading with its unit | Sensor and interface logs |
| Switch LED on and off | Apply the requested state and acknowledge it | Physical observation and controller reply |
| Conditional fan control | Read temperature first; switch only when the condition is met | Tool sequence, readings, and output state |
| Temperature equals the threshold | Keep the fan unchanged for a strictly greater-than request | Recorded reading and command sequence |
| Unclear request | Ask for clarification without an output action | Chat and command logs |
| Unknown tool or invalid value | Reject before hardware execution | Validation and controller logs |
| USB connection loss | Report a connection error; apply the defined local timeout behavior | Error log and observed output state |
| Missing controller acknowledgement | Show a timeout without claiming success | Gateway log |
| Manual stop | Override remote output requests | Observed output and firmware status |

## Measurements

- **Command accuracy:** Correctly completed requests divided by all requests in the predefined test set, reported as a percentage. Report monitoring and control tasks separately.
- **Response time:** Time from request submission to the final user-visible result. Report the median and range across repeated trials.
- **Error handling:** Record whether each invalid request or failure produces the expected response without unintended output changes.

## Procedure

1. Fix the hardware, model, prompts, and configuration for each test run.
2. Define expected outcomes and test counts before testing.
3. Repeat each case and keep both successful and failed trials.
4. Compare displayed results with controller logs and observed outputs.
5. Document limitations, including model variability and communication delays.

Performance targets will be agreed with the supervisor before evaluation. Results will be added only after physical testing.
