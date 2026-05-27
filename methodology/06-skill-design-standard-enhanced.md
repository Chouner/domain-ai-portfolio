# Skill Design Standard & Templates / Skill 设计标准与模板

## 1. Why Skill-based Design / 为什么采用 Skill 化设计

In domain-specific algorithm projects, the core risk is not code failure, but the **loss of physical constraints** during implementation — when a developer re-implements a formula without preserving its valid parameter ranges, physical assumptions, or domain boundary conditions.

Skill-based design aims to bind together:

Skill 化设计将以下要素绑定在一起：

- Domain formulas and rules / 领域公式和规则
- Input/output contracts / 输入输出契约
- Physical and algorithmic constraints / 物理和算法约束
- Upstream/downstream dependencies / 上下游依赖
- Validation thresholds and test cases / 验证阈值和测试用例
- Known limitations and failure modes / 已知限制和失效模式

---

## 2. Standard Skill Structure / 标准 Skill 目录结构

```
skill-name/
├── skill.md              # Core definition (purpose, logic, constraints)
├── references.md         # Papers, Echoview notes, code references
├── input-contract.md     # Required fields, types, units, valid ranges
├── output-contract.md    # Output fields, format, precision, units
├── pseudocode.md         # Algorithm pseudocode with edge case handling
├── test-cases.md         # Representative inputs with expected outputs
├── evaluation.md         # L1/L2/L3 expectations and acceptance thresholds
├── dependencies.md       # Upstream and downstream module dependencies
└── known-issues.md       # Failure modes, edge cases, when NOT to use
```

---

## 3. Skill Card Template / Skill 卡片模板

| Field / 字段 | Description / 描述 | Required / 必填 |
|---|---|---|
| **Skill ID** | Unique identifier (e.g., `sv-calculation-ek80`) | Yes |
| **Name / 名称** | Human-readable name (EN/CN) | Yes |
| **Version / 版本** | Semantic version (e.g., `v1.2.0`) | Yes |
| **Category / 类别** | `data_ingestion`, `signal_processing`, `acoustic_calculation`, `noise_removal`, `detection`, `aggregation`, `visualization`, `delivery` | Yes |
| **Purpose / 目标** | What problem this skill solves, in one paragraph | Yes |
| **Domain / 领域** | Underwater acoustics / fisheries / sonar imaging / positioning | Yes |
| **Input / 输入** | Required data format with field-level schema | Yes |
| **Output / 输出** | Expected result with field-level schema | Yes |
| **Domain Formula / 领域公式** | Mathematical formula(s) with variable definitions | If applicable |
| **Physical Constraints / 物理约束** | Energy, frequency, depth, speed, and other physical bounds | Yes |
| **Parameter Ranges / 参数范围** | Valid ranges for all tunable parameters | Yes |
| **Dependencies / 依赖** | Upstream modules (what must run first) and downstream modules (what consumes this output) | Yes |
| **L2 Validation Thresholds / L2 验证阈值** | Specific numeric thresholds for RMSE, MAE, IoU, etc. | Yes |
| **L3 Validation Criteria / L3 验证标准** | Physical plausibility and operational usability criteria | Yes |
| **Test Cases / 测试用例** | Reference to test-cases.md with ground truth | Yes |
| **Known Issues / 已知问题** | Documented failure modes and workarounds | Yes |
| **When NOT to Use / 何时不应使用** | Explicit exclusion conditions | Yes |
| **Reference Implementation / 参考实现** | Link to anonymized pseudocode | Yes |

---

## 4. Input Contract Template / 输入契约模板

```markdown
# Input Contract: [Skill Name]

## Required Fields / 必要字段

| Field | Type | Unit | Valid Range | Description |
|---|---|---|---|---|
| `timestamp` | float64 | seconds | > 0 | Unix or relative time |
| `frequency` | int32 | Hz | [1000, 1000000] | Operating frequency |
| `power` | float32 | W | [0.01, 10000] | Transmitted power |
| `sample_data` | float32[] | V | [-5.0, 5.0] | Raw voltage samples |
| `calibration_gain` | float64 | dB | [-100, 100] | System gain |

## Required Metadata / 必要元数据

| Field | Type | Description |
|---|---|---|
| `file_format` | str | EK80 / EK60 / RAW / CSV |
| `transducer_model` | str | ES38B / ES120-7C |
| `channel_count` | int | Number of channels |

## Validation Rules / 验证规则

- `timestamp` must be monotonically increasing
- `sample_data` length must match declared `sample_count`
- `frequency` must be within operating range of declared `transducer_model`
```

---

## 5. Output Contract Template / 输出契约模板

```markdown
# Output Contract: [Skill Name]

## Output Fields / 输出字段

| Field | Type | Unit | Precision | Description |
|---|---|---|---|---|
| `Sv` | float64[] | dB re 1 m⁻¹ | 4 decimal places | Volume backscattering strength |
| `range` | float64[] | m | 3 decimal places | Range from transducer |
| `ping_time` | float64 | s | 6 decimal places | Ping timestamp |

## Output Format / 输出格式

- Primary: NumPy array (n_pings × n_samples)
- Export: CSV with header row
- Metadata: JSON sidecar file

## Quality Flags / 质量标志

| Flag | Value | Meaning |
|---|---|---|
| `quality_flag` | 0 | Valid data |
| `quality_flag` | 1 | Below noise floor |
| `quality_flag` | 2 | Saturated signal |
| `quality_flag` | 3 | Interpolated / estimated |
```

---

## 6. Pseudocode Template / 伪代码模板

```markdown
# Pseudocode: [Skill Name]

## Algorithm Overview / 算法概述

[One-paragraph description of the algorithm in plain language]

## Algorithm Steps / 算法步骤

```
1. Validate input contract → raise ContractViolation if failed
2. Preprocess data (filter, normalize, align)
3. Apply domain formula
4. Apply physical constraints (clip out-of-range values)
5. Postprocess (smooth, interpolate)
6. Validate output contract → raise ContractViolation if failed
7. Return validated output
```

## Implementation Notes / 实现注意事项

- Edge case 1: [description and handling]
- Edge case 2: [description and handling]
- Performance note: [complexity, memory, optimization hint]

## Reference / 参考

- Source: [paper title, section, or Echoview feature]
```

---

## 7. Evaluation Template / 评估模板

```markdown
# Evaluation: [Skill Name]

## L1: Format Validation / 格式验证

| Check | Target | Status |
|---|---|---|
| Schema required fields present | 100% | ☐ |
| Type correctness | 0 errors | ☐ |
| Unit consistency | 0 mismatches | ☐ |
| Missing value ratio | < 1% | ☐ |

## L2: Numerical Validation / 数值验证

| Metric | Target | Actual | Pass |
|---|---|---|---|
| RMSE | < 0.5 dB | _____ | ☐ |
| MAE | < 0.1 dB | _____ | ☐ |
| p95 error | < 1.0 dB | _____ | ☐ |
| Pass rate (within tolerance) | > 95% | _____ | ☐ |

## L3: Domain Validation / 领域验证

| Criterion | Target | Status |
|---|---|---|
| Bottom line continuity | No jump > 5 m | ☐ |
| NASC spatial consistency | CV < 15% | ☐ |
| Processing time | < 30 s/file | ☐ |
| Expert acceptance | > 90% | ☐ |

## Test Configuration / 测试配置

- Reference tool: Echoview vX.Y
- Test dataset: [anonymized description]
- Number of test files: _____
- Total sample comparisons: _____
```

---

## 8. Skill Development Workflow / Skill 开发流程

```
┌─────────────────────────────────────────────────────────────────┐
│                    Skill Development Workflow                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │ 1.       │    │ 2.       │    │ 3.       │    │ 4.       │  │
│  │ Knowledge│ -> │ Contract │ -> │ Pseudocode│ -> │ Implement│  │
│  │ Extract  │    │ Design   │    │ Draft     │    │ Module   │  │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘  │
│       │                                               │         │
│       v                                               v         │
│  ┌──────────────────────────────────────────────────────┐      │
│  │                  5. Validation Loop                   │      │
│  │  ┌──────┐   ┌──────┐   ┌──────┐   ┌──────────────┐  │      │
│  │  │  L1  │ → │  L2  │ → │  L3  │ → │ Pass? → Register│      │
│  │  │Check │   │Check │   │Check │   │ Fail? → Fix    │  │      │
│  │  └──────┘   └──────┘   └──────┘   └──────────────┘  │      │
│  └──────────────────────────────────────────────────────┘      │
│       │                                                        │
│       v                                                        │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐                 │
│  │ 6.       │    │ 7.       │    │ 8.       │                 │
│  │ Register │ -> │ Document │ -> │ Deliver  │                 │
│  │ Manifest │    │ Issues   │    │ Interface│                 │
│  └──────────┘    └──────────┘    └──────────┘                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 9. Example: Sv Calculation Skill / 示例：Sv 计算 Skill

### skill.md

```markdown
# Sv Calculation (EK80 Narrowband) / Sv 计算（EK80 窄带）

## Purpose / 目标
Convert raw EK80 power/angle samples to calibrated Sv (volume backscattering strength) values.

## Domain Formula / 领域公式
Sv = 10 * log10(P_er) + 20 * log10(r) + 2 * alpha * r - 10 * log10(P_t * lambda^2 * c * tau * psi / (32 * pi^2)) + G

## Parameters / 参数
| Param | Symbol | Typical Range | Description |
|---|---|---|---|
| Pulse duration | tau | [64, 1024] μs | Transmit pulse length |
| Sound speed | c | [1450, 1550] m/s | In-situ sound speed |
| Absorption coefficient | alpha | [1, 100] dB/km | Frequency-dependent |
| Equivalent beam angle | psi | [-30, -10] dB | Transducer-specific |
| Gain | G | calibratable | System calibration gain |

## Physical Constraints / 物理约束
- Sv range: [-200, 0] dB (reject values outside for water column)
- Range r must be > transducer near-field distance
- Valid only for single-frequency narrowband data

## When NOT to Use / 何时不应使用
- Broadband (frequency-modulated) data → use broadband Sv skill instead
- Multi-frequency composite → use frequency-response skill
- Calibration unknown → flag as uncalibrated
```

### pseudocode.md

```python
def calculate_sv(power, range_m, params, calibration):
    """
    Calculate Sv from raw power values.

    Args:
        power: Received power [W], shape (n_pings, n_samples)
        range_m: Range from transducer [m], shape (n_samples,)
        params: dict with keys: tau, c, alpha, psi, lambda, pt
        calibration: dict with keys: gain, sa_correction

    Returns:
        sv: Volume backscattering strength [dB re 1 m⁻¹], shape (n_pings, n_samples)
        quality_flag: 0=valid, 1=below_noise, 2=saturated, 3=estimated
    """
    n_pings, n_samples = power.shape

    # 1. Range correction term
    range_term = 20 * np.log10(range_m)  # [dB]

    # 2. Absorption correction term
    alpha_linear = params['alpha'] / (10 * np.log10(np.e))  # dB/km → Np/m
    absorption_term = 2 * alpha_linear * range_m * 1000  # → dB

    # 3. System constant
    wavelength = params['c'] / params['frequency']
    pulse_volume_term = (params['c'] * params['tau'] * 1e-6) / 2
    psi_linear = 10 ** (params['psi'] / 10)  # dB → linear
    constant = 10 * np.log10(params['pt'] * wavelength**2 *
                             pulse_volume_term * psi_linear /
                             (32 * np.pi**2))

    # 4. Apply calibration gain
    gain = calibration['gain'] + calibration.get('sa_correction', 0)

    # 5. Compute Sv
    sv = 10 * np.log10(power) + range_term + absorption_term - constant + gain

    # 6. Physical constraint: clip to valid range
    quality_flag = np.zeros_like(sv, dtype=np.int8)
    quality_flag[sv < -200] = 1  # Below noise floor
    quality_flag[sv > 0] = 2     # Physically impossible

    sv = np.clip(sv, -200, 0)

    return sv, quality_flag
```

---

## 10. Governance Skills / 治理型 Skill

Beyond algorithmic Skills, the system also defines **governance Skills** for process control:

除了算法 Skill，系统还定义了**治理型 Skill** 用于流程控制：

| Skill | Purpose / 目标 | Input / 输入 | Output / 输出 |
|---|---|---|---|
| `require-skill` | Define work item boundaries | Task description | Bounded artifact |
| `retrieve-context-skill` | Fetch relevant documentation | Domain + task keywords | Structured context |
| `contract-design-skill` | Create module contract | Domain formula + constraints | Input/output contract |
| `plan-work-skill` | Generate work plan | Contract + constraints | Task breakdown |
| `test-data-skill` | Prepare validation data | Module type + test scope | Test dataset |
| `coding-skill` | Execute implementation | Contract + pseudocode + test data | Implementation |
| `validate-skill` | Run L1/L2/L3 checks | Implementation + test data | Validation report |
| `remediate-skill` | Handle validation failure | Failure signature | Correction plan |
| `register-skill` | Register validated module | Implementation + validation report | Registered manifest |
| `orchestrate-skill` | Compose modules into workflow | Pipeline requirements | Orchestrated workflow |
