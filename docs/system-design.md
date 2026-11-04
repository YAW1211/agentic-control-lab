# System Design

## Scope

The first prototype will connect one computer to one STM32 development board through USB serial. It will support temperature monitoring and LED or fan control. All behavior described here is planned.

## Responsibilities

| Part | Responsibility |
| --- | --- |
| Chat interface | Accept requests, display readings, and show results or errors |
| Agentic LLM | Choose allowed tools and use their results to answer the user |
| Python gateway | Validate commands, manage communication, and record logs |
| STM32 firmware | Read sensors, control outputs, enforce limits, and acknowledge commands |

## Proposed tools

| Tool | Input | Expected result |
| --- | --- | --- |
| `read_temperature` | None | Measured temperature, unit, and timestamp |
| `get_device_status` | None | Reported LED and fan states |
| `set_led` | Boolean state | Acknowledged state or error |
| `set_fan` | Boolean state | Acknowledged state or error |

These names describe the intended interface. They are not implemented APIs.

## Request handling

1. The user enters a request.
2. The agent selects an allowed tool.
3. The gateway validates the tool name and parameters.
4. The gateway sends a request with a unique identifier to the controller.
5. The controller checks the request, performs the action, and replies.
6. The gateway matches the reply to the request.
7. The interface displays the result and records the interaction.

For conditional requests, the agent first reads the sensor and then decides whether an output command is required. The number of tool calls per request will be limited.

## Errors and local control

Invalid or ambiguous requests will produce an error or a clarification request. Missing acknowledgements will produce a timeout rather than a success message. Stale or missing sensor readings will not be used to justify an output action.

The STM32 will enforce output limits independently of the LLM. A manual stop will override remote requests. The fan will default to OFF after reset or a control-link timeout. Exact timeout values and message formats will be defined during implementation.

## Data logging

Planned log fields include a request identifier, timestamp, requested tool, validated parameters, returned readings, controller status, response time, and error details. Logs will support evaluation and troubleshooting.
