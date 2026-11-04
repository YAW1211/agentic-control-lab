# Agentic Control Lab

### A natural-language interface for STM32 monitoring and control

I am exploring how users can monitor and control an STM32 system through plain-English requests. The proposed prototype combines temperature monitoring with LED and low-voltage fan control. My focus is on connecting a clear user interface to predictable hardware behavior.

**Status:** Proposal and system design. Firmware, application code, and hardware test results are planned and are not yet included.

**Author:** Ku Kwang Yaw  
**Academic project:** FYP-126  
**Formal title:** Development of an Agentic LLM Interface for Monitoring and Control of STM32-Based Cyber-Physical Systems

## Project work and design decisions

The current phase covers project scope, system architecture, component planning, and evaluation design. The project documents set out the following work:

- Define three objectives covering the interface, STM32 integration, and evaluation.
- Map the full path from a user request to a controller acknowledgement.
- Separate the computer's language-processing tasks from the STM32's hardware-control tasks.
- Plan command validation, timeout handling, and a manual stop.
- Define test cases for normal operation, unclear requests, and connection failures.

## Engineering challenges and design responses

These are problems considered during system design. The responses below are proposed solutions to be checked during implementation and hardware testing.

| Problem | Design response | What still needs to be checked |
| --- | --- | --- |
| A natural-language request can be unclear or contain an unsupported action. | Restrict the interface to predefined commands, validate parameters, and ask for clarification when needed. | Test unclear requests and confirm that no unintended action occurs. |
| Language-model response times can vary. | Run the LLM on the computer and keep timing-sensitive control in the STM32 firmware. | Measure response time and check local control behavior. |
| Sending a command does not prove that the hardware executed it. | Require an STM32 acknowledgement before reporting success. | Compare controller replies with observed LED and fan states. |
| USB communication may fail during a request. | Use bounded timeouts, report connection errors, and turn the fan off after the local control-link timeout. | Disconnect USB during tests and observe the output state. |
| Conditional control depends on a valid sensor reading. | Read temperature before deciding whether to switch the fan; reject missing or stale readings. | Test readings below, equal to, and above the threshold. |
| A fan needs a suitable electrical driver and power supply. | Include a protected driver circuit and a matched low-voltage supply. | Verify wiring, component ratings, and manual-stop behavior. |

## Objectives

1. **Develop a simple LLM chat interface.** Let users ask for readings and request device control in plain English.
2. **Connect the interface to an STM32 system.** Read temperature, control an LED or fan, and return device status through USB serial communication.
3. **Evaluate the system.** Measure command accuracy and response time, and check how it handles unclear requests, invalid commands, and connection failures.

## Example interaction

> Turn on the fan if the temperature is above 30 °C.

The planned agent first requests a temperature reading. If the reading is above the threshold, it requests fan activation through an allowed tool. The interface reports the result after receiving the controller's acknowledgement. A failed or missing acknowledgement is reported as an error.

This is an intended interaction, not a recorded demonstration.

## Proposed architecture

```mermaid
flowchart TD
    U[User request] --> UI[Chat interface]
    UI --> LLM[LLM on the computer]
    LLM --> G{Validate tool and parameters}
    G -->|Invalid or unclear| E[Reject request or ask for clarification]
    E --> UI
    G -->|Allowed| S[Python USB serial gateway]
    S --> MCU[STM32 controller]
    MCU --> HW[Temperature sensor and LED or fan]
    HW --> MCU
    MCU --> F[Readings and command acknowledgement]
    F --> S
    S --> LLM
    LLM --> R[User response and interaction log]
```

The computer hosts the LLM and application. The STM32 handles sensor access, device outputs, and local control limits. The LLM provides supervisory commands; timing-sensitive hardware behavior remains in the firmware.

## Planned hardware

| Component | Purpose |
| --- | --- |
| STM32 Nucleo board, such as NUCLEO-F446RE | Main controller |
| Temperature sensor compatible with 3.3 V logic | Temperature monitoring |
| LED and resistor | Simple control output |
| Low-voltage DC fan | Conditional-control demonstration |
| Suitable fan driver with protection | Fan switching |
| Regulated low-voltage supply | Fan and driver power |
| Breadboard, jumper wires, and USB cable | Prototyping and communication |
| Manual stop button | Local stop input |
| Laptop or PC | LLM, interface, gateway, and logs |

Final part numbers and wiring will be selected during hardware development.

## Planned software

| Software | Purpose |
| --- | --- |
| STM32CubeIDE and C/C++ | Firmware development and debugging |
| Python and pySerial | Command validation and serial communication |
| Streamlit | Chat interface and readings display |
| Ollama with a tool-capable LLM | Request interpretation and tool selection |
| CSV files | Test and interaction logs |
| Firebase, optional | Online storage of readings |
| Git | Version control |

The LLM will be selected according to the available computer resources and tool-calling performance. Firebase is optional and is not required for the initial USB prototype.

## Control safeguards

- Allow only predefined tools and valid parameter values.
- Keep output limits and the manual stop in the STM32 firmware.
- Turn the fan off after reset or a control-link timeout.
- Confirm execution using controller acknowledgements.
- Bound the number of agent steps per request.
- Use low-voltage DC demonstration loads.

## Development roadmap

- [x] Define project scope and objectives.
- [x] Prepare the proposal and architecture.
- [ ] Select components and a tool-capable model.
- [ ] Build and test sensor and output firmware.
- [ ] Implement the serial gateway and command validation.
- [ ] Build the chat interface and integrate LLM tools.
- [ ] Test monitoring, conditional control, and failure handling.
- [ ] Record results and prepare a demonstration.

## Project documents

- [Editable Word proposal](docs/Project_Proposal.docx)
- [System design](docs/system-design.md)
- [Evaluation plan](docs/evaluation-plan.md)

## Repository structure

```text
agentic-control-lab/
├── README.md
├── .gitignore
└── docs/
    ├── Project_Proposal.docx
    ├── system-design.md
    └── evaluation-plan.md
```

Application and firmware directories will be added when implementation begins. There are no installation or execution steps yet because this repository currently contains project documentation.

## Technical references

- [STMicroelectronics NUCLEO-F446RE](https://www.st.com/en/evaluation-tools/nucleo-f446re.html)
- [Ollama tool support](https://ollama.com/blog/tool-support)

## License

No open-source license has been selected. Contact the author before reusing or distributing project materials.
