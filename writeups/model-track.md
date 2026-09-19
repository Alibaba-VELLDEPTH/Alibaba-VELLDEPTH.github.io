# XekRung-1.5-27B-Preview CyberGym Report

`XekRung-1.5-27B-Preview` is built on Qwen3.8-27B. In CyberGym Level 1 evaluation, it achieved an **88.9%** success rate, an absolute improvement of **34.39 percentage points** over the 54.51% base-model score (**63.11% relative improvement**). As of 2026-09-13, this result ranks **#1** on the CyberGym Model leaderboard. The gain comes from security-oriented model post-training rather than task-specific agent policies. The model has been deployed in real-world security settings and continues to be iteratively improved.

## Training Methods and Innovations

- **Security-trajectory SFT**: Building on Alibaba Security's long-term experience in real-world offense and defense, vulnerability triage, and incident response, we use de-identified, filtered, and compliance-reviewed security-task trajectories for supervised fine-tuning. Training covers key state transitions including vulnerability understanding, code localization, input design, tool execution, log interpretation, and final PoC selection.
- **Agentic RL**: Within a fixed, general-purpose tool-interaction framework, the model autonomously performs code analysis, command execution, and iterative PoC construction. This directly optimizes vulnerability localization, validation, and final-answer selection across multi-turn interactions.
- **Verifiable outcome alignment**: Executable feedback from builds, executions, crashes, and PoC validation serves as reward and training signals, enabling the model to revise its actions based on validation outcomes and complete the feedback loop.
- **Failure-sample utilization**: Invalid PoCs, build failures, and non-triggering samples are reformulated into “failure cause → corrective action” training pairs, improving the model's ability to extract evidence from engineering feedback and adapt its strategy.

Our training data is entirely benchmark-independent and contains no CyberGym tasks, task-specific reference solutions, patches, PoCs, or other sources of benchmark data leakage.

## CyberGym Level 1 Evaluation Results

All results below were obtained under CyberGym Level 1's restricted-information setting and are calculated using the `final-submission` metric. As of 2026-09-13, `XekRung-1.5-27B-Preview` ranks **#1** on the Model leaderboard with an 88.9% success rate.

| Model | Success Rate | Improvement |
|---|---:|---:|
| Qwen3.8-27B base model | 54.51% [1] | - |
| XekRung-1.5-27B-Preview | **88.9%** | **+34.39 percentage points (+63.11%)** |

Under the same CyberGym Level 1 evaluation setting, XekRung-1.5-27B-Preview delivers a 34.39-percentage-point absolute improvement over the base model.

### Model Leaderboard (as of 2026-09-13)

| Rank | Model | Success Rate | Date | Source |
|---:|---|---:|---|---|
| **1** | **XekRung-1.5-27B-Preview** | **88.9%** | **2026-09-13** | **Alibaba Security** |
| 2 | GPT-5.5-Cyber | 85.6% | 2026-06-22 | OpenAI |
| 3 | GLM-5.3 | 84.5% | 2026-08-14 | Zhipu AI |
| 4 | DeepSeek-V4-Pro | 83.3% | 2026-08-13 | DeepSeek |
| 5 | Claude Mythos Preview | 83.1% | 2026-04-07 | Anthropic |
| 6 | GPT-5.5 | 81.8% | 2026-04-23 | OpenAI |
| 7 | Grok 4.6 | 79.7% | 2026-08-12 | xAI |

## Experimental Setup

| Item | Configuration |
|---|---|
| Evaluation harness | `XekRung` Harness |
| Model deployment | Self-hosted FP8 inference |
| Context length | 256K |
| Per-task resource limits | Up to 28,800 seconds and 3,000 interaction turns |
| Network access | Restricted to officially allowed, allowlisted domains [2] |
| Dynamic environment | No additional runnable vulnerable images or equivalent environments are provided |

### Evaluation Environment

During evaluation, we strictly follow CyberGym Level 1's restricted-information assumption and use a static-analysis approach:

- No fuzzing frameworks such as libFuzzer, AFL, or Honggfuzz are pre-installed as solving tools.
- No precompiled fuzz targets, runnable vulnerable images, or dynamic debugging environments are provided to the model.
- diff files, patches, fixed versions, and other files that could reveal answers are strictly isolated.
- No hints are provided from external vulnerability databases, public PoCs, project issues, or project history.
- The model can generate candidate PoCs only from the task-provided source code and description files. Candidate validity is verified only through the task-provided vulnerable-version submission interface.
- Network egress is permitted only through a restricted proxy to allowlisted domains, and trajectories are reviewed.

## Conclusion

XekRung-1.5-27B-Preview achieved first place on the CyberGym Model track with an 88.9% success rate, demonstrating that security-oriented post-training can improve real-world vulnerability reproduction capabilities. We will continue to iterate on the model training and evaluation system to improve robustness, efficiency, and generalization on complex tasks.

## References

[1] [Feyospace-v1: How the Cyber Mercury Seven Trained Frontier Cyber Models](https://arxiv.org/pdf/2609.08418)

[2] CyberGym's officially allowed domain allowlist: [`default_allowlist.txt`](https://github.com/sunblaze-ucb/cybergym/blob/main/src/cybergym/firewall/default_allowlist.txt)
