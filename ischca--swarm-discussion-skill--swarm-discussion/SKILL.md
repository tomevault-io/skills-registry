---
name: swarm-discussion
description: | Use when this capability is needed.
metadata:
  author: ischca
---

# swarm-discussion

**"Deep-dive into problems as if multiple experts were debating"**

## Staff+ Engineer Thinking Pattern

> "When there's no clear answer, expose blind spots by confronting diverse perspectives."

For unsolved problems or unprecedented challenges, multiple experts participate as a **team**,
engaging in "true discussions" through messaging where they challenge and supplement each other.

## Features

- **Team-based Architecture**: Compose teams using the Teammate API
- **Messaging-based Dialogue**: Experts communicate directly with each other
- **Structured Disagreement Protocol**: Prevent echo chambers through designed tension
- **Argument Graph**: Every claim cites or rebuts prior statements with explicit references
- **Adaptive Flow**: Discussion phases adjust based on convergence signals
- **Quality Gates**: Automatic checks prevent premature consensus
- **Dynamic Expert Generation**: Automatically define appropriate experts based on the topic
- **Cost-aware Modes**: Choose between deep and lightweight discussion modes
- **Live Progress Reporting**: Real-time updates shown to user as each discussion step completes
- **Complete Evidence Preservation**: Save all messages with citation chains
- **User Participation**: Use AskUserQuestion for direction confirmation

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                  Discussion Team                                 │
│                  team_name: discussion-{id}                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐   │
│  │ Expert 1   │ │ Expert 2   │ │ Contrarian │ │Cross-Domain│   │
│  │            │◄──────────────►│            │◄─────────────►│   │
│  │  inbox ◄───┼──── messages ──┼──► inbox   │  messages    │   │
│  └─────┬──────┘ └─────┬──────┘ └─────┬──────┘ └─────┬──────┘   │
│        │              │              │              │            │
│        └──────────────┴──────┬───────┴──────────────┘            │
│                              │                                   │
│                    ┌─────────▼─────────┐                         │
│                    │    Moderator      │                         │
│                    │  - Present topics │                         │
│                    │  - Quality gates  │                         │
│                    │  - Convergence    │                         │
│                    └─────────┬─────────┘                         │
│                              │                                   │
│                    ┌─────────▼─────────┐                         │
│                    │    Historian      │                         │
│                    │  - Argument graph │                         │
│                    │  - Synthesize     │                         │
│                    └───────────────────┘                         │
└─────────────────────────────────────────────────────────────────┘
```

## Discussion Modes

Choose based on problem complexity and budget:

| Mode | Experts | Rounds | Calls/Round | Use When |
|------|---------|--------|-------------|----------|
| **Deep** | 3-4 dynamic + 4 fixed | 3-5 | 8-12 | Unprecedented problems, high-stakes decisions |
| **Standard** | 2-3 dynamic + 4 fixed | 2-3 | 5-8 | Typical design decisions, tradeoff analysis |
| **Lightweight** | 2 dynamic + 2 fixed (Moderator, Contrarian) | 1-2 | 3-5 | Quick sanity checks, idea validation |

Default: **Standard**. Specify mode with `/swarm-discussion --mode deep "topic"`.

## Role Design

### Fixed Roles

| Role | Responsibility | Quality Function |
|------|----------------|------------------|
| **Moderator** | Facilitate discussion, enforce quality gates, determine convergence | Prevents premature consensus; ensures all perspectives are heard |
| **Historian** | Build argument graph, synthesize, generate summaries | Maintains traceability; prevents circular arguments |
| **Contrarian** | Question assumptions, present counterarguments | Prevents echo chambers; stress-tests ideas |
| **Cross-Domain** | Provide analogies from other fields | Prevents domain-locked thinking; seeds novel approaches |

### Dynamically Generated Roles (2-4 based on topic and mode)

Analyze the topic and define appropriate experts. Each expert has the following attributes:

```json
{
  "id": "database-expert",
  "name": "Database Expert",
  "expertise": ["RDB", "NoSQL", "Distributed DB"],
  "thinkingStyle": "pragmatic",
  "bias": "Prioritizes practicality and performance",
  "replyTendency": "Shows concrete implementation examples",
  "stakes": "Owns the data layer; bad decisions mean data loss or inconsistency",
  "blindSpots": ["Tends to underestimate application-level complexity"]
}
```

**New fields:**
- `stakes`: What this expert stands to lose if the wrong decision is made. Drives genuine engagement.
- `blindSpots`: Known limitations of this perspective. Enables other experts to target these.

### Tension Map (Critical for Discussion Quality)

When generating personas, the Moderator MUST design a **tension map** — pairs of experts with naturally opposing viewpoints:

```json
{
  "tensionMap": [
    {
      "between": ["database-expert", "api-designer"],
      "axis": "Data consistency vs. API flexibility",
      "description": "Database Expert wants strict schemas; API Designer wants loose coupling"
    },
    {
      "between": ["security-engineer", "ml-practitioner"],
      "axis": "Control vs. Autonomy",
      "description": "Security Engineer restricts; ML Practitioner needs freedom to experiment"
    }
  ]
}
```

**Why this matters**: Without designed tension, AI agents tend toward premature agreement. The tension map ensures that at least some expert pairs have structurally opposing incentives, producing genuine debate rather than a series of "I agree, and also..." statements.

## Structured Disagreement Protocol

### The Echo Chamber Problem

AI agents playing expert roles naturally tend to:
1. Agree with well-reasoned prior statements
2. Add supplementary points rather than challenge
3. Converge too quickly on the first plausible solution

### Prevention Mechanisms

**1. Mandatory Position Declaration**

Before the discussion begins, each expert MUST declare a preliminary position:

```json
{
  "from": "database-expert",
  "type": "position_declaration",
  "position": "Saga pattern with orchestration",
  "confidence": 0.7,
  "conditions": "Assuming < 10 services involved",
  "wouldChangeIf": "Someone shows orchestration overhead exceeds 30% of request latency"
}
```

The `wouldChangeIf` field is critical — it forces each expert to pre-commit to what evidence would change their mind, preventing unfalsifiable positions.

**2. Steel-Manning Requirement**

Before disagreeing, an expert MUST demonstrate understanding of the opposing view:

```
Before I present my counterargument, let me confirm I understand the position:
[Restate the argument in the strongest possible form]

My concern with this is: [counterargument]
```

**3. Disagreement Budget**

The Moderator tracks a "disagreement score" for each round:
- If score < 2 (too much agreement): Moderator explicitly asks "What could go wrong with this consensus?" and directs Contrarian to challenge the strongest agreement
- If score > 8 (too much disagreement): Moderator asks "Where is the common ground?" and focuses on finding shared premises

**4. Position Shift Tracking**

The Historian records when experts change their positions:

```json
{
  "type": "position_shift",
  "expert": "database-expert",
  "from": "Saga pattern with orchestration",
  "to": "Event sourcing with compensating actions",
  "trigger": "Message #msg-007 from operations-engineer",
  "reasoning": "Operational complexity of orchestrator convinced me"
}
```

## Discussion Flow

### Phase 1: Initialization

```javascript
// 1. Generate discussion ID
const discussionId = slugify(topic);  // e.g., "microservice-transaction"

// 2. Determine mode (deep / standard / lightweight)
const mode = parseMode(userInput);  // default: "standard"

// 3. Create team
Teammate({
  operation: "spawnTeam",
  team_name: `discussion-${discussionId}`
})

// 4. Create directory structure
Bash({
  command: `mkdir -p ~/.claude/discussions/${discussionId}/{personas,rounds,artifacts,context}`
})

// 5. Analyze topic and define experts with tension map
const topicAnalysis = analyzeTopicAndDefineExperts(topic, mode);
// → {
//      experts: [{ id, name, expertise, thinkingStyle, bias, replyTendency, stakes, blindSpots }],
//      tensionMap: [{ between, axis, description }],
//      problemDefinition: { statement, scope, successCriteria }
//    }

// 6. Add fixed roles (based on mode)
const fixedRoles = mode === "lightweight"
  ? [
      { id: "moderator", name: "Moderator", role: "Discussion facilitator" },
      { id: "contrarian", name: "Contrarian", role: "Devil's advocate" }
    ]
  : [
      { id: "moderator", name: "Moderator", role: "Discussion facilitator" },
      { id: "historian", name: "Historian", role: "Record keeper" },
      { id: "contrarian", name: "Contrarian", role: "Devil's advocate" },
      { id: "cross-domain", name: "Cross-Domain", role: "Alternative perspective" }
    ];

const allExperts = [...topicAnalysis.experts, ...fixedRoles];

// 7. Confirm expert composition with user (show tension map)
AskUserQuestion({
  questions: [{
    question: `Start discussion with the following experts?\n\n` +
      `**Dynamic Experts:**\n${topicAnalysis.experts.map(e =>
        `- ${e.name} (${e.thinkingStyle}) — Stakes: ${e.stakes}`
      ).join('\n')}\n\n` +
      `**Designed Tensions:**\n${topicAnalysis.tensionMap.map(t =>
        `- ${t.between[0]} vs ${t.between[1]}: ${t.axis}`
      ).join('\n')}\n\n` +
      `**Mode:** ${mode}`,
    header: "Confirm Experts & Tensions",
    options: [
      { label: "Start (Recommended)", description: "Begin with this composition" },
      { label: "Modify", description: "Add or change experts" },
      { label: "Change Mode", description: "Switch between deep/standard/lightweight" }
    ],
    multiSelect: false
  }]
})

// 8. Save manifest.json (includes tension map)
Write(`~/.claude/discussions/${discussionId}/manifest.json`, {
  id: discussionId,
  title: topic,
  created: new Date().toISOString(),
  status: "active",
  mode: mode,
  currentPhase: "initial",
  currentRound: 0,
  team_name: `discussion-${discussionId}`,
  personas: allExperts,
  tensionMap: topicAnalysis.tensionMap,
  problemDefinition: topicAnalysis.problemDefinition
})

// 9. Save each expert definition
for (const expert of allExperts) {
  Write(`~/.claude/discussions/${discussionId}/personas/${expert.id}.json`, expert)
}
```

### Phase 2: Round Execution (Position → Debate → Convergence Check)

#### Live Progress Reporting

During round execution, the orchestrator MUST report progress to the user after each
major step completes. This serves two purposes:

1. **User-facing output**: Print a concise markdown summary directly to the conversation
   so the user sees what's happening in real-time, without waiting for the full round.
2. **Progress file**: Append to `~/.claude/discussions/{id}/progress.md` so the user
   can check the file at any time (e.g., from another terminal).

**Progress format** (output directly to the user after each step):

```
### 📍 Round {N} — Step {step}: {step_name}

{1-3 sentence summary of what just happened}

**Key takeaway**: {most important point from this step}
```

The orchestrator MUST NOT silently proceed through all steps. Each step's result
must be surfaced to the user immediately.

```javascript
async function executeRound(discussionId, roundNum, roundTopic) {
  const teamName = `discussion-${discussionId}`;
  const manifest = loadManifest(discussionId);
  const experts = loadPersonas(discussionId);
  const mode = manifest.mode;

  // Live progress file path
  const progressFile = `~/.claude/discussions/${discussionId}/progress.md`;

  // Helper: Report progress to user and append to progress file
  function reportProgress(step, stepName, summary, keyTakeaway) {
    const progressEntry = `### Round ${roundNum} — Step ${step}: ${stepName}\n\n` +
      `${summary}\n\n` +
      `**Key takeaway**: ${keyTakeaway}\n\n---\n\n`;

    // 1. Print directly to user (output as text in the conversation)
    output(progressEntry);

    // 2. Append to progress file for async checking
    Bash({ command: `echo '${escapeForShell(progressEntry)}' >> ${progressFile}` });
  }

  // Track argument graph for this round
  const argumentGraph = [];
  let messageCounter = 0;
  function nextMsgId() { return `r${roundNum}-msg-${String(++messageCounter).padStart(3, '0')}`; }

  // Array to store all messages
  const allMessages = [];

  // ========== Step 1: Position Declaration (NEW) ==========
  // Each dynamic expert declares their preliminary position BEFORE seeing others
  // This prevents anchoring bias from the first speaker

  const positionDeclarations = [];
  for (const expert of getDynamicExperts(experts)) {
    Task({
      team_name: teamName,
      name: expert.id,
      subagent_type: "general-purpose",
      prompt: buildPositionDeclarationPrompt(expert, roundTopic),
      run_in_background: true
    })
  }

  // Collect position declarations
  for (const expert of getDynamicExperts(experts)) {
    const declaration = await collectStatement(expert.id);
    const msgId = nextMsgId();
    positionDeclarations.push({
      id: msgId,
      from: expert.id,
      type: "position_declaration",
      content: declaration,  // { position, confidence, conditions, wouldChangeIf }
      timestamp: new Date().toISOString()
    });
    allMessages.push(positionDeclarations[positionDeclarations.length - 1]);
  }

  // >>> PROGRESS REPORT: Position Declarations <<<
  reportProgress(1, "Position Declarations",
    positionDeclarations.map(d =>
      `**${d.from}**: "${d.content.position}" (confidence: ${d.content.confidence})`
    ).join('\n'),
    `${positionDeclarations.length} experts declared positions. ` +
    `Confidence range: ${Math.min(...positionDeclarations.map(d => d.content.confidence))}–${Math.max(...positionDeclarations.map(d => d.content.confidence))}`
  );

  // ========== Step 2: Moderator Opening ==========
  // Moderator now has ALL positions and can frame the discussion
  // around actual disagreements rather than generic angles

  const moderatorOpening = await Task({
    team_name: teamName,
    name: "moderator",
    subagent_type: "general-purpose",
    prompt: `
You are the Moderator. Frame this round's discussion.

【Topic】
${roundTopic}

【Position Declarations】
${formatPositionDeclarations(positionDeclarations)}

【Tension Map】
${JSON.stringify(manifest.tensionMap, null, 2)}

【Previous Discussion】
${previousRoundsSummary || "(New discussion)"}

【Task】
1. Identify where positions actually diverge (not where they superficially differ)
2. Frame 2-3 specific questions that target the real disagreements
3. Direct questions to specific experts based on the tension map
4. Set the disagreement budget for this round (target: 3-6 on a 0-10 scale)

Do NOT present generic angles. Use the position declarations to find genuine fault lines.
    `
  });

  const moderatorMsgId = nextMsgId();
  allMessages.push({
    id: moderatorMsgId,
    from: "moderator",
    type: "opening",
    content: moderatorOpening,
    timestamp: new Date().toISOString()
  });

  // Broadcast to all
  broadcastMessage(teamName, "moderator", moderatorOpening, experts);

  // >>> PROGRESS REPORT: Moderator Opening <<<
  reportProgress(2, "Moderator Framing",
    `Moderator identified key fault lines and framed ${mode === "lightweight" ? "1-2" : "2-3"} targeted questions for the experts.`,
    moderatorOpening.substring(0, 200) + "..."
  );

  // ========== Step 3: Argumentation Phase ==========
  // Dynamic experts argue their positions with CITATIONS
  // Must reference prior messages by ID

  for (const expert of getDynamicExperts(experts)) {
    Task({
      team_name: teamName,
      name: expert.id,
      subagent_type: "general-purpose",
      prompt: buildExpertPrompt(expert, {
        phase: "argumentation",
        topic: roundTopic,
        positionDeclarations: positionDeclarations,
        moderatorOpening: moderatorOpening,
        tensionMap: manifest.tensionMap,
        instruction: `
Argue your position. You MUST:
1. Reference at least one other expert's position by message ID (e.g., "Regarding ${positionDeclarations[0]?.id}...")
2. Steel-man any position you disagree with before countering it
3. Identify what evidence would change your mind
4. Be specific — cite concrete scenarios, numbers, or code examples

Do NOT simply agree with others. If you agree, explain what ADDITIONAL risk or nuance they missed.
        `
      }),
      run_in_background: true
    })
  }

  // Collect arguments and build argument graph
  const arguments = [];
  for (const expert of getDynamicExperts(experts)) {
    const argument = await collectStatement(expert.id);
    const msgId = nextMsgId();
    const msg = {
      id: msgId,
      from: expert.id,
      type: "argument",
      content: argument,
      references: extractReferences(argument),  // Parse referenced message IDs
      timestamp: new Date().toISOString()
    };
    arguments.push(msg);
    allMessages.push(msg);

    // Build argument graph edge
    for (const ref of msg.references) {
      argumentGraph.push({
        from: msgId,
        to: ref.targetId,
        relation: ref.type  // "supports", "counters", "extends", "questions"
      });
    }

    broadcastMessage(teamName, expert.id, argument, experts);
  }

  // >>> PROGRESS REPORT: Argumentation <<<
  reportProgress(3, "Expert Arguments",
    arguments.map(a =>
      `**${a.from}**: "${a.content.position}" — references: ${a.references.map(r => r.targetId).join(', ') || 'none'}`
    ).join('\n'),
    `${arguments.length} arguments filed with ${argumentGraph.length} cross-references. ` +
    `${argumentGraph.filter(e => e.relation === 'counters').length} counterarguments, ` +
    `${argumentGraph.filter(e => e.relation === 'supports').length} supporting arguments.`
  );

  // ========== Step 4: Contrarian Stress Test ==========
  // Contrarian targets the STRONGEST consensus, not the weakest argument

  const contrarianResponse = await Task({
    team_name: teamName,
    name: "contrarian",
    subagent_type: "general-purpose",
    prompt: `
You are the Contrarian.

【Topic】
${roundTopic}

【All Arguments So Far】
${formatMessagesWithIds(allMessages)}

【Argument Graph】
${JSON.stringify(argumentGraph, null, 2)}

【Task】
1. Identify the point where most experts AGREE — this is your primary target
2. Challenge this consensus: What assumption does it rest on? What if that assumption is wrong?
3. Find the WEAKEST evidence cited in support of the consensus
4. Present a concrete scenario where the consensus leads to failure
5. If there's genuine disagreement already, amplify the minority position with new arguments

Do NOT be contrarian for its own sake. Target the most dangerous blind spot.

Reference prior messages by ID when challenging them.
    `
  });

  const contrarianMsgId = nextMsgId();
  allMessages.push({
    id: contrarianMsgId,
    from: "contrarian",
    type: "stress_test",
    content: contrarianResponse,
    references: extractReferences(contrarianResponse),
    timestamp: new Date().toISOString()
  });

  broadcastMessage(teamName, "contrarian", contrarianResponse, experts);

  // >>> PROGRESS REPORT: Contrarian Stress Test <<<
  reportProgress(4, "Contrarian Stress Test",
    `Contrarian targeted the emerging consensus and challenged ${extractReferences(contrarianResponse).length} prior arguments.`,
    typeof contrarianResponse === 'string'
      ? contrarianResponse.substring(0, 200) + "..."
      : JSON.stringify(contrarianResponse).substring(0, 200) + "..."
  );

  // ========== Step 5: Response Phase ==========
  // Experts respond to Contrarian — must explicitly state if position shifted

  for (const expert of getDynamicExperts(experts)) {
    Task({
      team_name: teamName,
      name: expert.id,
      subagent_type: "general-purpose",
      prompt: buildExpertPrompt(expert, {
        phase: "response",
        topic: roundTopic,
        allMessages: allMessages,
        contrarianResponse: contrarianResponse,
        contrarianMsgId: contrarianMsgId,
        instruction: `
Respond to Contrarian's stress test (${contrarianMsgId}).

You MUST include a position update:
{
  "positionShift": "none" | "minor" | "major",
  "currentPosition": "Your current stance",
  "previousPosition": "Your stance before this round (from position declaration)",
  "shiftReason": "What specifically changed your mind (if anything)"
}

If your position hasn't changed, explain specifically why the Contrarian's argument doesn't apply to your case.
        `
      }),
      run_in_background: true
    })
  }

  // Collect responses and track position shifts
  const responses = [];
  const positionShifts = [];
  for (const expert of getDynamicExperts(experts)) {
    const response = await collectStatement(expert.id);
    const msgId = nextMsgId();
    responses.push({
      id: msgId,
      from: expert.id,
      type: "response",
      content: response,
      references: extractReferences(response),
      timestamp: new Date().toISOString()
    });
    allMessages.push(responses[responses.length - 1]);

    // Track position shifts
    if (response.positionShift && response.positionShift !== "none") {
      positionShifts.push({
        type: "position_shift",
        expert: expert.id,
        from: response.previousPosition,
        to: response.currentPosition,
        trigger: contrarianMsgId,
        reasoning: response.shiftReason
      });
    }
  }

  // >>> PROGRESS REPORT: Response Phase <<<
  reportProgress(5, "Expert Responses & Position Shifts",
    responses.map(r =>
      `**${r.from}**: shift=${r.content.positionShift || 'none'}` +
      (r.content.positionShift !== 'none' ? ` → "${r.content.currentPosition}"` : '')
    ).join('\n'),
    positionShifts.length > 0
      ? `${positionShifts.length} position shift(s) detected! ${positionShifts.map(s => `${s.expert} shifted due to ${s.trigger}`).join('; ')}`
      : `No position shifts — experts held their ground against the Contrarian's challenge.`
  );

  // ========== Step 6: Cross-Domain Perspective (Standard/Deep only) ==========
  if (mode !== "lightweight") {
    const crossDomainResponse = await Task({
      team_name: teamName,
      name: "cross-domain",
      subagent_type: "general-purpose",
      prompt: `
You are the Cross-Domain Thinker.

【Topic】
${roundTopic}

【Discussion So Far (with message IDs)】
${formatMessagesWithIds(allMessages)}

【Position Shifts This Round】
${JSON.stringify(positionShifts, null, 2)}

【Task】
1. Identify the UNDERLYING pattern in this debate (not the surface-level topic)
2. Find an analogous problem in a completely different field where this pattern was resolved
3. Explain the analogy in detail: what maps to what, and what doesn't map
4. Propose a framework or principle from the analogous field
5. Be honest about where the analogy breaks down

Fields to consider: biology, physics, economics, law, military strategy, urban planning, game theory, ecology.

Reference specific messages by ID that your analogy addresses.
      `
    });

    const crossDomainMsgId = nextMsgId();
    allMessages.push({
      id: crossDomainMsgId,
      from: "cross-domain",
      type: "analogy",
      content: crossDomainResponse,
      references: extractReferences(crossDomainResponse),
      timestamp: new Date().toISOString()
    });

    // >>> PROGRESS REPORT: Cross-Domain <<<
    reportProgress(6, "Cross-Domain Perspective",
      `Cross-Domain Thinker provided analogies from other fields, referencing ${extractReferences(crossDomainResponse).length} prior messages.`,
      typeof crossDomainResponse === 'string'
        ? crossDomainResponse.substring(0, 200) + "..."
        : JSON.stringify(crossDomainResponse).substring(0, 200) + "..."
    );
  }

  // ========== Step 7: Quality Gate & Convergence Check ==========
  const qualityCheck = await Task({
    team_name: teamName,
    name: "moderator",
    subagent_type: "general-purpose",
    prompt: `
Evaluate this round's discussion quality and synthesize.

【All Messages】
${formatMessagesWithIds(allMessages)}

【Argument Graph】
${JSON.stringify(argumentGraph, null, 2)}

【Position Shifts】
${JSON.stringify(positionShifts, null, 2)}

【Quality Gate Checklist】
Rate each 1-5 and explain:
1. Genuine Disagreement: Did experts actually challenge each other, or just agree?
2. Evidence Quality: Were claims supported by specifics (scenarios, data, code)?
3. Steel-manning: Did experts accurately represent opposing views before countering?
4. Novel Insights: Did the discussion produce ideas not present in initial positions?
5. Position Evolution: Did any expert change their mind based on arguments?

【Synthesis Task】
{
  "qualityScore": {
    "genuineDisagreement": 1-5,
    "evidenceQuality": 1-5,
    "steelManning": 1-5,
    "novelInsights": 1-5,
    "positionEvolution": 1-5,
    "overall": 1-5
  },
  "summary": "Summary of this round (200-300 characters)",
  "agreements": [{ "point": "...", "supporters": ["expert-ids"], "strength": "strong|moderate|weak" }],
  "activeDisagreements": [{ "point": "...", "positions": [{"stance": "...", "advocates": ["expert-ids"]}] }],
  "insights": [{ "insight": "...", "novelty": "high|medium|low", "source": "message-id" }],
  "openQuestions": ["..."],
  "positionShifts": [...],
  "recommendation": "continue|deep-dive|different-angle|synthesize",
  "recommendationReason": "Why this next step"
}

If overall quality < 3, flag it and suggest what went wrong (e.g., "Too much agreement — re-run with sharper Contrarian framing").
    `
  });

  // ========== Step 8: Record ==========
  const roundRecord = {
    roundId: roundNum,
    topic: roundTopic,
    mode: mode,
    timestamp: new Date().toISOString(),
    messages: allMessages,
    argumentGraph: argumentGraph,
    positionShifts: positionShifts,
    synthesis: JSON.parse(qualityCheck),
    metadata: {
      messageCount: allMessages.length,
      participants: [...new Set(allMessages.map(m => m.from))],
      referenceCount: argumentGraph.length
    }
  };

  // Historian records (Standard/Deep only)
  if (mode !== "lightweight") {
    await Task({
      team_name: teamName,
      name: "historian",
      subagent_type: "general-purpose",
      prompt: `
Record this round's discussion for the argument graph.

【Round Record】
${JSON.stringify(roundRecord, null, 2)}

Save the following to the round file:
1. Complete message history with IDs and references
2. Argument graph edges (who challenged/supported whom)
3. Position shifts with triggers
4. Quality assessment

Also update a running "argument map" that shows the evolution of positions across rounds.
      `
    });
  }

  Write(`~/.claude/discussions/${discussionId}/rounds/${String(roundNum).padStart(3, '0')}.json`, roundRecord)

  // ========== Step 9: Confirm Next Action ==========
  const synthesis = roundRecord.synthesis;
  const recommendation = synthesis.recommendation || "continue";

  AskUserQuestion({
    questions: [{
      question: `Round ${roundNum} complete.\n\n` +
        `**Quality Score:** ${synthesis.qualityScore?.overall || "N/A"}/5\n` +
        `**Position Shifts:** ${positionShifts.length}\n` +
        `**Recommendation:** ${recommendation} — ${synthesis.recommendationReason || ""}\n\n` +
        `What would you like to do next?`,
      header: "Round Complete",
      options: [
        { label: "Follow recommendation", description: `${recommendation}` },
        { label: "Deep dive", description: synthesis.activeDisagreements?.[0]?.point || "Most contested topic" },
        { label: "Different angle", description: "Explore a different aspect" },
        { label: "Inject constraint", description: "Add a new requirement or assumption" },
        { label: "Synthesis phase", description: "Summarize the discussion" },
        { label: "Pause", description: "Resume later" }
      ],
      multiSelect: false
    }]
  })

  return roundRecord;
}

// Helper: Build position declaration prompt
function buildPositionDeclarationPrompt(expert, topic) {
  return `
You are "${expert.name}".

【Your Profile】
- Areas of expertise: ${expert.expertise.join(", ")}
- Thinking style: ${expert.thinkingStyle}
- Natural bias: ${expert.bias}
- What you stand to lose from a bad decision: ${expert.stakes}
- Your known blind spots: ${expert.blindSpots?.join(", ") || "None declared"}

【Topic】
${topic}

【Task】
Declare your preliminary position BEFORE hearing from others.

Output format:
{
  "position": "Your stance in 1-2 sentences",
  "confidence": 0.0-1.0,
  "conditions": "Under what assumptions this holds",
  "wouldChangeIf": "What evidence would make you reconsider",
  "keyRisk": "The biggest risk if your position is adopted"
}

Be honest about your confidence. Low confidence is fine — it signals where the group needs to dig deeper.
  `;
}

// Helper: Broadcast message
function broadcastMessage(teamName, fromId, message, experts) {
  for (const expert of experts) {
    if (expert.id !== fromId && expert.id !== "historian") {
      Teammate({
        operation: "write",
        target_agent_id: expert.id,
        value: JSON.stringify({
          type: "message",
          from: fromId,
          content: message
        })
      })
    }
  }
}

// Helper: Extract references from message content
function extractReferences(content) {
  // Parse "r1-msg-001" style references from the text
  const refs = [];
  const pattern = /r\d+-msg-\d{3}/g;
  let match;
  while ((match = pattern.exec(JSON.stringify(content))) !== null) {
    refs.push({ targetId: match[0], type: "references" });
  }
  return refs;
}
```

### Phase 3: Synthesis

```javascript
async function synthesizeDiscussion(discussionId) {
  const teamName = `discussion-${discussionId}`;
  const allRounds = loadAllRounds(discussionId);
  const manifest = loadManifest(discussionId);

  // Build cumulative argument graph
  const fullArgumentGraph = allRounds.flatMap(r => r.argumentGraph || []);
  const allPositionShifts = allRounds.flatMap(r => r.positionShifts || []);

  // Historian synthesizes everything
  const finalSynthesis = await Task({
    team_name: teamName,
    name: "historian",
    subagent_type: "general-purpose",
    prompt: `
You are the Historian. Synthesize the entire discussion.

【All Round Records】
${JSON.stringify(allRounds, null, 2)}

【Full Argument Graph】
${JSON.stringify(fullArgumentGraph, null, 2)}

【All Position Shifts】
${JSON.stringify(allPositionShifts, null, 2)}

【Original Problem Definition】
${JSON.stringify(manifest.problemDefinition, null, 2)}

【Synthesis Rules】
1. Every insight MUST be traceable to specific message IDs
2. Include a "minority report" for dissenting views that were outnumbered but not refuted
3. Distinguish between "resolved by argument" and "resolved by majority" — the latter is weaker
4. Rate confidence based on evidence quality, not on how many experts agreed
5. Open questions should include WHY they remain open (lack of evidence? fundamental uncertainty?)

【Output Format】
{
  "executiveSummary": "Summary of the entire discussion (500-800 characters)",
  "insights": [
    {
      "title": "Insight title",
      "description": "Detailed description",
      "confidence": "high/medium/low",
      "confidenceReason": "Why this confidence level (evidence-based, not vote-based)",
      "supportingEvidence": [{ "messageId": "r1-msg-003", "summary": "..." }],
      "dissentingViews": [{ "messageId": "r2-msg-007", "summary": "...", "refuted": true/false }]
    }
  ],
  "agreements": [
    { "point": "Agreement point", "supporters": ["..."], "strength": "strong|moderate|weak" }
  ],
  "minorityReport": [
    {
      "position": "Dissenting view",
      "advocate": "expert-id",
      "reason": "Why this was not adopted by majority",
      "stillValid": true/false,
      "note": "Why this deserves attention despite being minority"
    }
  ],
  "unresolvedDebates": [
    {
      "topic": "Point of contention",
      "positions": [
        { "stance": "Position A", "advocates": ["..."], "arguments": ["..."] },
        { "stance": "Position B", "advocates": ["..."], "arguments": ["..."] }
      ],
      "whyUnresolved": "Reason this couldn't be settled"
    }
  ],
  "positionEvolution": [
    {
      "expert": "expert-id",
      "journey": ["Initial position", "After round 1", "Final position"],
      "keyTurningPoints": ["message-ids that caused shifts"]
    }
  ],
  "openQuestions": [
    { "question": "...", "whyOpen": "...", "suggestedApproach": "..." }
  ],
  "recommendations": [
    { "action": "...", "confidence": "high/medium/low", "risk": "...", "prerequisite": "..." }
  ],
  "metaObservations": "Observations about the discussion process itself (quality, blind spots, what worked)"
}
    `
  });

  // Save artifacts
  const synthesis = JSON.parse(finalSynthesis);

  Write(`~/.claude/discussions/${discussionId}/artifacts/synthesis.json`, synthesis)
  Write(`~/.claude/discussions/${discussionId}/artifacts/synthesis.md`, formatSynthesisAsMarkdown(synthesis))
  Write(`~/.claude/discussions/${discussionId}/artifacts/open-questions.md`, formatOpenQuestions(synthesis))
  Write(`~/.claude/discussions/${discussionId}/artifacts/argument-graph.json`, fullArgumentGraph)
  Write(`~/.claude/discussions/${discussionId}/artifacts/position-evolution.md`, formatPositionEvolution(synthesis))

  return synthesis;
}
```

### Phase 4: Checkpoint / Termination

```javascript
async function checkpointDiscussion(discussionId) {
  const teamName = `discussion-${discussionId}`;

  // 1. Generate resume context (richer than before)
  const resumeContext = await Task({
    team_name: teamName,
    name: "historian",
    subagent_type: "general-purpose",
    prompt: `
Generate the context needed to resume this discussion later.

Items to include:
1. Discussion theme and purpose (1 paragraph)
2. Key progress so far (bullet points)
3. Current topics and state
4. Summary of each participant's CURRENT position (after shifts)
5. Active disagreements and their state
6. Issues to address next time
7. Quality trajectory (improving or declining over rounds?)

Length: 1500-3000 characters
Format: Markdown
    `
  });

  Write(`~/.claude/discussions/${discussionId}/context/summary.md`, resumeContext)

  // 2. Update manifest
  updateManifest(discussionId, {
    lastActive: new Date().toISOString(),
    status: "paused",
    currentPhase: "checkpoint"
  })

  // 3. Shutdown all workers
  const experts = loadPersonas(discussionId);
  for (const expert of experts) {
    Teammate({
      operation: "requestShutdown",
      target_agent_id: expert.id
    })
  }

  // 4. Cleanup
  Teammate({ operation: "cleanup" })
}

async function resumeDiscussion(discussionId) {
  // 1. Load manifest
  const manifest = Read(`~/.claude/discussions/${discussionId}/manifest.json`)

  // 2. Recreate team
  Teammate({
    operation: "spawnTeam",
    team_name: manifest.team_name
  })

  // 3. Load resume context
  const context = Read(`~/.claude/discussions/${discussionId}/context/summary.md`)

  // 4. Load last round for position continuity
  const lastRound = loadLatestRound(discussionId);

  // 5. Update manifest
  updateManifest(discussionId, {
    lastActive: new Date().toISOString(),
    status: "active"
  })

  // 6. Notify Moderator of resumption (with richer context)
  const reopening = await Task({
    team_name: manifest.team_name,
    name: "moderator",
    subagent_type: "general-purpose",
    prompt: `
Resuming the discussion.

【Previous Context】
${context}

【Last Round Synthesis】
${JSON.stringify(lastRound?.synthesis, null, 2)}

【Participants and Their Last Known Positions】
${manifest.personas.map(p => `- ${p.name}`).join('\n')}

【Tension Map】
${JSON.stringify(manifest.tensionMap, null, 2)}

Review the previous state. Specifically:
1. What was the most productive tension in the last session?
2. What open question is most important to address now?
3. Has anything in the tension map been resolved?

Propose the next round's topic and framing.
    `
  })

  return reopening;
}
```

## Data Structure

```
~/.claude/discussions/{discussion-id}/
├── manifest.json              # Metadata, personas, tension map, mode
├── progress.md                # Live progress log (appended after each step)
├── personas/                  # Expert definitions (with stakes and blind spots)
│   ├── expert-1.json
│   ├── contrarian.json
│   └── ...
├── rounds/                    # Complete record of each round
│   ├── 001.json               # Messages with IDs, argument graph, position shifts
│   ├── 002.json
│   └── ...
├── artifacts/                 # Outputs
│   ├── synthesis.json         # Structured synthesis with minority report
│   ├── synthesis.md           # Markdown format
│   ├── open-questions.md      # Unresolved questions with reasons
│   ├── argument-graph.json    # Full argument graph across all rounds
│   └── position-evolution.md  # How each expert's position changed
└── context/
    └── summary.md             # Resume context
```

### Round JSON Structure (Message-based with References)

```json
{
  "roundId": 2,
  "topic": "Microservice Transaction Management",
  "mode": "standard",
  "timestamp": "2026-01-29T10:30:00Z",
  "messages": [
    {
      "id": "r2-msg-001",
      "from": "database-expert",
      "type": "position_declaration",
      "content": {
        "position": "I recommend the Saga pattern with orchestration",
        "confidence": 0.7,
        "conditions": "Assuming < 10 services",
        "wouldChangeIf": "Orchestration overhead exceeds 30% of request latency",
        "keyRisk": "Compensating transaction complexity grows quadratically"
      },
      "timestamp": "2026-01-29T10:30:00Z"
    },
    {
      "id": "r2-msg-005",
      "from": "contrarian",
      "type": "stress_test",
      "content": "Both experts agree on Saga, but neither addresses the case where...",
      "references": [
        { "targetId": "r2-msg-001", "type": "counters" },
        { "targetId": "r2-msg-002", "type": "counters" }
      ],
      "timestamp": "2026-01-29T10:33:00Z"
    },
    {
      "id": "r2-msg-007",
      "from": "database-expert",
      "type": "response",
      "content": {
        "response": "The Contrarian raises a valid point about...",
        "positionShift": "minor",
        "currentPosition": "Saga with orchestration, but with circuit breakers for > 5 services",
        "previousPosition": "Saga with orchestration",
        "shiftReason": "Hadn't considered cascade failure in the compensation chain"
      },
      "references": [
        { "targetId": "r2-msg-005", "type": "responds_to" }
      ],
      "timestamp": "2026-01-29T10:35:00Z"
    }
  ],
  "argumentGraph": [
    { "from": "r2-msg-005", "to": "r2-msg-001", "relation": "counters" },
    { "from": "r2-msg-005", "to": "r2-msg-002", "relation": "counters" },
    { "from": "r2-msg-007", "to": "r2-msg-005", "relation": "responds_to" }
  ],
  "positionShifts": [
    {
      "expert": "database-expert",
      "from": "Saga with orchestration",
      "to": "Saga with orchestration + circuit breakers for > 5 services",
      "trigger": "r2-msg-005",
      "reasoning": "Cascade failure risk in compensation chain"
    }
  ],
  "synthesis": {
    "qualityScore": {
      "genuineDisagreement": 4,
      "evidenceQuality": 3,
      "steelManning": 4,
      "novelInsights": 3,
      "positionEvolution": 4,
      "overall": 4
    },
    "summary": "Saga pattern consensus emerged but was stress-tested; circuit breaker addition shows genuine position evolution",
    "agreements": [
      { "point": "Distributed 2PC should be avoided", "supporters": ["database-expert", "api-designer"], "strength": "strong" }
    ],
    "activeDisagreements": [
      { "point": "Choreography vs Orchestration", "positions": [
        { "stance": "Orchestration", "advocates": ["database-expert"] },
        { "stance": "Choreography", "advocates": ["api-designer"] }
      ]}
    ],
    "insights": [
      { "insight": "Compensation chain failure is a real risk at scale", "novelty": "high", "source": "r2-msg-005" }
    ],
    "openQuestions": ["What is the maximum practical service count for orchestration-based Saga?"],
    "recommendation": "deep-dive",
    "recommendationReason": "Choreography vs Orchestration is unresolved and has significant implementation implications"
  }
}
```

## Prompt Templates

### Dynamic Expert Prompt

```javascript
function buildExpertPrompt(expert, options) {
  return `
You are participating in the discussion as "${expert.name}".

【Your Profile】
- Areas of expertise: ${expert.expertise.join(", ")}
- Thinking style: ${expert.thinkingStyle}
- Natural bias: ${expert.bias}
- Response tendency: ${expert.replyTendency}
- What you stand to lose from a bad decision: ${expert.stakes}
- Known blind spots: ${expert.blindSpots?.join(", ") || "None declared"}

【Current Phase】
${options.phase}

【Topic】
${options.topic}

【Position Declarations】
${options.positionDeclarations ? formatPositionDeclarations(options.positionDeclarations) : "(No declarations yet)"}

【Previous Statements (with message IDs)】
${options.allMessages ? formatMessagesWithIds(options.allMessages) : options.previousStatements ? formatStatements(options.previousStatements) : "(First statement)"}

${options.contrarianResponse ? `
【Contrarian's Stress Test (${options.contrarianMsgId})】
${options.contrarianResponse}
` : ""}

【Relevant Tensions】
${options.tensionMap ? options.tensionMap
  .filter(t => t.between.includes(expert.id))
  .map(t => `You and ${t.between.find(e => e !== expert.id)} disagree on: ${t.axis}`)
  .join('\n') : "(None)"}

【Task】
${options.instruction}

【Referencing Rules】
- When responding to someone's point, reference their message ID: "Regarding r1-msg-003..."
- When supporting a claim, cite the specific message
- When disagreeing, first steel-man the position you're countering

【Output Format】
{
  "position": "Your stance on this topic (1-2 sentences)",
  "reasoning": "Detailed analysis with specific evidence",
  "proposals": ["Concrete proposals"],
  "references": [
    { "targetId": "message-id", "relation": "supports|counters|extends|questions", "comment": "Why" }
  ],
  "counterpoints": ["Rebuttals or supplements to others (with message IDs)"],
  "questions": ["Additional questions to consider"],
  "codeOrDiagrams": "Code examples or diagrams (optional)"
}
  `;
}
```

## Important Notes

### Cost Awareness
- Team-based approach has **higher overhead** than direct launch
- Use **lightweight mode** for quick checks; reserve **deep mode** for high-stakes decisions
- Approximate costs per mode:
  - Lightweight: 3-5 Task calls/round
  - Standard: 5-8 Task calls/round
  - Deep: 8-12 Task calls/round

### Live Progress Reporting
- After each step in a round, the orchestrator MUST output a brief summary to the user
- This includes: who said what (1-2 sentences), position shifts detected, key takeaways
- The same information is appended to `progress.md` in the discussion directory
- Users can also check `~/.claude/discussions/{id}/progress.md` from another terminal
- Progress output should be **concise** (3-5 lines per step) — not a full dump of the expert's output
- The goal is to let the user follow along and decide whether to intervene early

### Quality Over Quantity
- A 2-round discussion with genuine disagreement beats a 5-round discussion with polite agreement
- Monitor the quality score — if it drops below 3, the discussion structure needs adjustment
- Position shifts are the strongest signal of a productive discussion

### Leveraging Messaging
- Use `Teammate({ operation: "write" })` to share statements
- Experts can respond based on received messages
- Enables **true dialogue**

### Importance of Cleanup
- Always `requestShutdown` → `cleanup` when discussion ends
- Leaving teams running wastes resources

### Evidence & Traceability
- Save **all statements** with unique message IDs
- Every claim should reference the message it responds to
- The argument graph enables post-hoc analysis of how conclusions were reached
- Position evolution tracking shows which arguments were actually persuasive

### Anti-Patterns to Avoid
- **"I agree, and also..."** — If all experts agree, the Contrarian isn't doing their job
- **Generic analogies** — Cross-Domain should cite specific cases, not vague similarities
- **Unfalsifiable positions** — Every position must have a `wouldChangeIf` condition
- **Authority-based arguments** — "Best practice says..." is not evidence. Show WHY it's best practice
- **Premature synthesis** — Don't synthesize before real disagreements have been explored

## Usage Example

```
User: /swarm-discussion "Microservice Transaction Management"

System:
1. Analyze topic → define experts with tension map
2. Show experts:
   - Distributed Systems Designer (stakes: system reliability)
     vs. API Designer (stakes: developer experience)  ← designed tension
   - Database Expert (stakes: data consistency)
     vs. Operations Engineer (stakes: operational simplicity)  ← designed tension
   - Contrarian, Cross-Domain (fixed)
3. User confirms → "Start" (Standard mode)
4. Round 1:
   - All experts: Position declarations (parallel, BEFORE seeing others)
   - Moderator: Frame based on actual disagreements
   - Experts: Argue with citations (parallel)
   - Contrarian: Target the strongest consensus
   - Experts: Respond with position shift tracking
   - Cross-Domain: Analogies addressing specific messages
   - Moderator: Quality gate + convergence check
5. Quality score: 4/5, recommendation: "deep-dive on choreography vs orchestration"
6. User: "Follow recommendation"
7. Round 2, 3, ...
8. Synthesis phase → artifacts/ (includes minority report, argument graph, position evolution)
9. Checkpoint → cleanup

User: /swarm-discussion --mode lightweight "Should we use GraphQL or REST for this API?"
→ Quick 1-2 round discussion with 2 experts + Moderator + Contrarian
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ischca) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
