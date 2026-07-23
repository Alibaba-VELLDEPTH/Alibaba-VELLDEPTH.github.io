# Velldepth Agent on CyberGym

## About

Velldepth Agent is a security-oriented intelligent agent developed by Alibaba Security for software vulnerability analysis. It integrates the cybersecurity-focused XekRung model with harness. In the CyberGym benchmark, Velldepth Agent achieved **85.5%** on pass@1 for L1 tasks. In addition, while testing the agent's vulnerability-analysis capability, we identified 92 potential real-world zero-day cases across 44 software repositories.

## Setup

- Model: XekRung, our cybersecurity model.
- Harness: our cybersecurity vulnerability analysis agent.
- Network configuration: in addition to CyberGym's official whitelist firewall, we blocked web fetch and web search behavior to prevent information leakage or benchmark cheating. The task prompt also explicitly prohibited searching repository vulnerability history, public PoCs, or task-related vulnerability information.

Environment setup: During the design of our harness, we followed the principles below:

- No fuzzing frameworks such as libFuzzer, AFL, or Honggfuzz are pre-installed as solving tools.
- No precompiled fuzz targets or dynamic debugging environments are provided for the benchmark tasks.
- The harness does not use execution results from the patched/fixed version to help the model choose among candidates.
- The harness does not provide hints based on external vulnerability databases, public PoCs, or project history.

The model can generate candidates only through source-code understanding and the description files provided by the task, and the validity of candidate PoCs is verified solely through the vul submission interface provided by the task. This setup strictly follows the restricted-information assumption of L1 itself, and makes the evaluation results better reflect the gains brought by the model training and harness design.

## Technical Routine

### Model

The training of XekRung focuses on code understanding, vulnerability analysis and long-horizon security tasks, strengthening its ability to reason about vulnerable code paths, input constraints and exploitability.

### Harness

Our system first transforms the task description and visible project information into a structured task state, helping the model maintain goal consistency during long-horizon analysis. It then explores multiple directions around likely vulnerability analysis, input constraints, and code paths, and dynamically adjusts the analysis focus according to vulnerable-side validation feedback.

To avoid premature convergence, the system preserves and compares multiple candidate hypotheses. During audit, it filters candidates by jointly considering semantic consistency, source-code evidence, and runtime behavior, and ultimately outputs the PoC that best matches the task objective. This workflow emphasizes verifiable reasoning chains and controlled execution orchestration, enabling the model to steadily complete vulnerability localization and validation across different projects and vulnerability types.

## Benchmark Result

| Rank | Agent | Model | Success Rate | Date | Source |
| --- | --- | --- | --- | --- | --- |
| 1 | Crystalline | Claude Opus 4.6 | 89.6% | 2026-06-08 | Independent researcher |
| 2 | MDASH | Multi-model | 88.4% | 2026-05-12 | Microsoft |
| 3 | OpenAI Agent | GPT-5.5-Cyber | 85.6% | 2026-06-22 | OpenAI |
| 4 | Velldepth Agent | XekRung | 85.5% | 2026-07-23 | Alibaba Security |
| 5 | Xuanwu Atuin AI | GLM-5.2 | 84.8% | 2026-07-22 | Tencent Xuanwu Lab |

## Findings

Our analysis yields two main findings. First, several tasks exhibit "double-crash" behavior and evaluation ambiguity. Second, harness-level structured state management and candidate review are critical to reliable long-horizon vulnerability reproduction.

### "Double-Crash" Behavior in CyberGym Tasks

For example, a task that specifies only a stack overflow in `RTSP_UnpackURL` can contain two adjacent out-of-bounds paths in the host and port/retest branches. Both PoC inputs satisfy the task description and overflow the same stack buffer in the same function. However, the patch covers only one path, while the other crashes both the vulnerable and fixed builds. This ambiguity arises from coarse task descriptions, multiple semantically similar vulnerability paths, patch scope limited to a single root cause, and hidden fixed-side behavior.

### The Role of the Harness in Long-Horizon Vulnerability Analysis

Failures in long-horizon vulnerability reproduction do not stem solely from weak single-step reasoning. Harness-level state management, structured runtime feedback, and candidate review maintain alignment with the task description, preserve multiple candidate paths, and reduce redundant exploration under a limited budget.

## Real-World Zero-Day Vulnerability Discovery

Velldepth Agent has identified 92 potential zero-day cases across 44 real-world software repositories. Their distribution across independent codebases demonstrates the agent's general vulnerability-analysis capability and its ability to generalize across different projects and code environments.

## Conclusion

The CyberGym evaluation highlights a recurring challenge in domain-specific security tasks: in long-horizon, multi-step workflows, many agents fail to retain critical information or drift away from domain-specific analytical principles, making it difficult to converge on valid PoCs. This shows that systematic harness design and specialized model intelligence remain essential.

Our practice suggests a viable path forward. Harness constraints such as process state management and key-information persistence can keep agents directionally stable and reduce drift. At the same time, improving the model's familiarity with domain knowledge and difficult security tasks increases "intelligence density". The synergy of these two approaches is the fundamental reason behind Velldepth Agent achieving 85.5% performance in CyberGym.

We believe the paradigm of a domain harness combined with a domain model is an effective and sustainable approach for solving specialized tasks. It also provides a reusable methodology for cost-efficient deployment across broader security scenarios.
