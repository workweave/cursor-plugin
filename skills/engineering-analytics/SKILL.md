---
name: engineering-analytics
description: >-
  Analyze engineering team metrics using Weave. Use when asked about
  team productivity, code velocity, PR cycle time, review turnaround,
  AI code adoption, DORA metrics, or any engineering performance question.
---

# Engineering Analytics

Analyze engineering team performance using Weave's MCP tools. This skill handles questions about code output, PR cycle time, review quality, task delivery, AI code adoption, and more.

## When to use

- "How is my team performing?"
- "What's our PR cycle time?"
- "Who are the top contributors this quarter?"
- "How much AI-written code are we shipping?"
- "Show me review turnaround trends"
- "Compare team velocity month over month"
- Any question about engineering metrics, productivity, or code analytics

## Workflow

### Step 1: Understand the question

Identify what the user is asking about:
- **What metric?** Map to a specific `metric_type` (see list below)
- **What scope?** Org-wide, team, or individual
- **What time range?** Default to last 30 days if not specified

### Step 2: Resolve entities

If the question mentions specific teams or people:
1. Call `get_teams` to get team IDs and names
2. Call `get_accounts` to get member IDs and names
3. Use the returned IDs for filtering in subsequent calls

### Step 3: Fetch metrics

Choose the right tool based on the question:

- **Trends, aggregates, comparisons** → `get_metric_overview`
  - Use `group_by` for breakdowns (by team, person, repository)
  - Use `time_granularity` for time series (`day`, `week`, `month`)
  - Use `benchmark_group` for comparisons

- **Specific records, outliers, investigation** → `get_metric_drill_down`
  - Use `sort_by` and `sort_direction` to find extremes
  - Use `limit` to control result size

### Step 4: Present results

- Use tables for comparisons
- Round durations to human-readable values (e.g., "2.3 days" not "198432 seconds")
- Highlight trends (up/down from previous period)
- Include benchmark comparisons when available
- Call out notable outliers or patterns

## Metric reference

### Code velocity
| Metric type | What it measures |
|---|---|
| `code_output` | Weighted productivity score factoring in complexity |
| `code_output_per_engineer` | Per-person code output |
| `code_loc` | Raw lines of code changed |
| `prs` | Pull requests merged |
| `prs_per_engineer` | PRs per person |
| `pr_cycle_time` | Open to merge duration |
| `pr_merge_time` | Approval to merge duration |

### Code review
| Metric type | What it measures |
|---|---|
| `code_reviews` | Reviews performed |
| `code_review_turnaround` | Time to first review |
| `code_review_quality` | Review thoroughness score |
| `review_cycles` | Rounds of review before merge |
| `pr_review_rate` | Percentage of PRs that received review |
| `comment_resolution` | Comment resolution details |

### Task delivery
| Metric type | What it measures |
|---|---|
| `tasks` | Tasks completed |
| `points` | Story points delivered |
| `points_per_engineer` | Points per person |
| `task_lead_time` | Creation to completion duration |
| `task_delivery` | Delivery rate |
| `bug_tasks` | Bug tasks completed |

### AI code
| Metric type | What it measures |
|---|---|
| `ai_code_loc` | AI-assisted lines of code |
| `ai_code_percentage` | % of code written with AI tools |
| `ai_output_percentage` | AI contribution to output score |
| `ai_efficiency_index` | Composite score combining AI output volume, usage %, cost, and code turnover |
| `output_per_ai_dollar` | ROI metric: code output per dollar of AI tool spend |
| `tool_costs` | AI tool costs |

### Quality
| Metric type | What it measures |
|---|---|
| `bugs_introduced` | New bugs introduced |
| `bug_ratio` | Bugs as percentage of work |
| `revert_prs` | Reverted pull requests |
| `code_turnover` | Code churn rate |
| `innovation_ratio` | Feature vs maintenance work ratio |

## Example queries

**"How's the team doing this month?"**
→ Call `get_metric_overview` with `metric_type=code_output`, current month date range, `group_by=team`

**"Who shipped the most PRs last quarter?"**
→ Call `get_metric_drill_down` with `metric_type=prs`, Q date range, `sort_by=value`, `sort_direction=desc`, `limit=10`

**"What's our AI code adoption trend?"**
→ Call `get_metric_overview` with `metric_type=ai_code_percentage`, `time_granularity=week`

**"Compare review turnaround across teams"**
→ Call `get_teams` first, then `get_metric_overview` with `metric_type=code_review_turnaround`, `group_by=team`
