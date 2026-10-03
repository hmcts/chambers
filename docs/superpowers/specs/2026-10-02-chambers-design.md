# Chambers — design

Status: draft for review. Written 2026-10-02.

## What this is

You brief one agent. It allocates the work to a set of capabilities, supervises
them, and brings you the decisions that are yours to make.

There is no application. Chambers is a cloned repository of instructions,
skills and helper scripts, and launching a coding agent inside it is what turns
that agent into a supervising one.

**Who it is for.** One person accountable for a change to a digital service who
does not have a full team to make it. What they are short of is disciplines
rather than hands: the change needs the journeys it touches identified, the
standards checked, the code written and the work reviewed, and there is one of
them. Chambers supplies the roles that are missing on one service — not more
parallel coders across many repositories.

A team with every discipline staffed is not the audience.

**It owns one thing: the layer between an instruction and supervised,
evidence-backed work on a matter.**

## Why not just use the harness directly

For one change you can hold in your head, you should. Opening a coding agent and
asking is faster than anything described here, and a tool that insists on itself
for small work gets abandoned for good reason.

Four things stop that working, and Chambers is for the point where two of them
are true at once:

1. **More than one thing at a time.** One agent doing four things in one
   checkout collides with itself: half-finished edits from one task are the
   starting state of the next, and the failure looks like a bad model rather
   than a bad setup. Separate worktrees are the fix, and managing them by hand
   is the tax this removes.
2. **The disciplines you would not think to ask for.** Asked for a code change
   you get a code change. Nobody asks which journeys it touches or whether it
   still meets the Service Standard, because at five o'clock you did not think
   of it. A capability that exists is asked every time; a discipline you have to
   remember is asked when you are fresh.
3. **Review that is not self-review.** Asking the agent that wrote the change to
   review it returns a review by its author. Separation has to be structural
   because no instruction survives being read by the thing it constrains.
4. **Not having to watch.** Direct interaction is a conversation: it stops when
   you stop reading. Supervision means work continues and you are interrupted
   when a decision is actually yours.

If fewer than two apply, use the harness directly. That is not a disclaimer, it
is the boundary of the problem this solves.

## The shape

Four parts, and the separation between them is the design.

| Part | What it is |
|---|---|
| **Chambers** | the capabilities that produce work |
| **The Bench** | reviews what was produced, from the output rather than the workings |
| **You** | the authority neither of the other two has |
| **Matters** | the services and repositories Chambers is instructed on, owned elsewhere |

A barristers' chambers is a set of independent specialists who share
infrastructure and a clerk that allocates work. Two things follow from
borrowing that structure, and both are load-bearing.

**The reviewer is not a member.** An agent that grades its own work grades
generously, and no instruction fixes that, because the instruction is read by
the same agent. The only arrangement under which reviewing means anything is
structural: the Bench runs with its own state and is given the work product
without the reasoning that produced it. If that separation is ever relaxed for
convenience, the component stops meaning anything and should be removed rather
than weakened.

**Matters are outside.** Chambers is read-only over a matter except through
narrow operations a human has approved. If services live inside the repository,
the repository couples to every service and grows until nobody can read it.

## What it will never be

- **A digital service, or part of one.** Nothing it produces runs in front of a
  citizen. It proposes changes that a service team reviews, tests and deploys
  through their own pipeline.
- **The reviewer of its own work.** See above; this is the one constraint that
  cannot be traded.
- **The authority.** Cost, disclosure, release and anything irreversible stay
  with a person. It escalates; it does not decide.
- **A replacement for a team.** It supplies capabilities. The accountable
  humans stay accountable, which in a government setting is what makes it
  adoptable at all.
- **A substitute for users, or for research with them.** It can read what people
  said and tie a change to the journeys it affects. It cannot generate findings,
  invent participants or produce personas, and output that resembles research is
  not research.
- **An owner of any matter's content.** A service's code, data and findings
  stay with that service. This repository is public, so no estate's repository
  names, field names, counts or live findings belong in it — illustrations use
  invented names.

## What a capability is

A capability is a role, not a person and not a model. It is four things:

- a **brief template** — what this capability is given and what it returns;
- a **check** — how its output is recognised as finished rather than abandoned;
- a **boundary** — the paths it may read and write;
- a **declared model and effort**, which the brief may override.

Model and effort are per capability because reviewing a change and writing one
are different kinds of work and do not want the same amount of thinking. It also
keeps the cost legible: the expensive roles are named rather than found on a
bill.

Capabilities are data. Adding one is adding a directory, not changing the
dispatcher.

**The scope is a service team, not an engineering team.** A change to a digital
service needs the journeys it touches identified and the standards it has to
meet checked, as much as it needs code written, and a tool that only writes code
hands the rest back to the person it was supposed to help.

The first set is **investigate, research, review, implement**. That is a
sequencing decision rather than a ceiling: it is what one user needs on day one,
and the fifth waits for a second user who can say what it is for. A capability
nobody has asked for is a directory that rots.

**One boundary, and it is not negotiable: a capability analyses research, it
does not stand in for the people the research is about.** Reading what users
said, tying a change to the journeys it affects, and checking work against a
published standard are all analysis of material that exists. Generating
findings, inventing participants or producing personas is not research, however
much it resembles the output — and in a government setting, presenting it as
research would be the most damaging thing this tool could do.

## The command surface

**You type English.** "Look at the probate service and fix the flaky address
test" is the interface, and the measure of the design is how rarely you need
anything else. A command the operator has to learn is a failure of the brief.

Underneath, the supervising agent needs a small amount of machinery it can call
and whose output it can trust. The surface is deliberately short, and each entry
exists for a reason that survives being asked twice:

| Command | Why it cannot be prose |
|---|---|
| register and list **matters** | the set of repositories Chambers may touch is a security boundary, so it is a committed file a human edits, not something an agent infers |
| make and remove a **worktree** for a task | wrapping `git worktree` once puts the naming, the cleanup and the refusal to work outside a matter in one place |
| write and read a task's **status** | supervision reads fact off disk rather than a log of intent, so the format has exactly one writer |

That is the whole expected surface. There is no command to dispatch a worker,
because the harness does that; none to attach to a session, because there are no
panes; and none to install anything, because nothing is installed.

**The test for adding one:** a command exists because the agent cannot do the
thing reliably in prose, not because it is tidier as a script. A command surface
grows by one reasonable step at a time until nobody can hold it, and the only
defence is making each addition argue for itself.

**What a human may want beyond English**, and the only two worth anticipating: a
way to see what is in flight without asking, and a way to stop everything. Both
are read-only or destructive rather than productive, which is why they are
exceptions to the first paragraph rather than contradictions of it.

## Dispatch and isolation

Each task runs in its own `git worktree`, created from the matter's clone and
removed on completion. Parallel work on one repository cannot collide, and a
worker that goes wrong has damaged a disposable directory.

**A task has one of two shapes**, declared when it is raised. A *delivery* task
changes a matter and ends in something a human reviews. An *inquiry* task
changes nothing and ends in a report. Conflating them is how an investigation
quietly edits a repository.

**A delivery task carries its delivery contract**: how the change is meant to
land, resolved for that task when it is raised. It is never read from a standing
setting at the moment of dispatch — a default that decides how work lands is a
decision made by configuration rather than by a person. The brief records the
contract it was raised under, and a worker whose contract does not match the one
recorded is refused rather than run.

**Dispatch goes through the harness's own subagent mechanism.** There is no
terminal multiplexer, so there is no pane to watch a worker in or type into
mid-task.

That is a deliberate trade, not an omission. A session backend reintroduces a
dependency the install position exists to avoid, so reopening it needs a reason
rather than a preference.

## Supervision

A worker is finished, working, or stuck, and only the third needs a human.

Supervision reads the artefacts on disk rather than a dispatch log, because a
log records intent and the disk records fact.

Two properties of fan-out that are invisible from the code and expensive to
rediscover:

- **A worker writes each result immediately.** One that accumulates and writes
  at the end loses everything it produced when it dies.
- **"No output" has two causes** — a worker still working, and a worker that was
  never launched because a concurrency ceiling rejected it rather than queuing
  it. Only the second needs a human, and plan-ordered dispatch hides it.
- **A dead worker is not a stuck one.** Something stuck might still recover, so
  escalating it is useful. A worker that is gone never moves again: nothing
  changes, the idle timer never resets, and the escalation repeats without
  limit. Unbounded escalation on finished work is what trains a human to stop
  reading escalations, so a worker proven absent is reported once as gone rather
  than escalated as stale.

**A wake is not a person.** Supervision wakes the lead agent, and the lead agent
has to know that a wake is machinery rather than the human speaking. That is
carried by a structural marker on the input, not inferred from how the text
reads: prose is the one thing an agent will cheerfully misread, and a tool that
guesses will eventually treat a watcher's nudge as an instruction.

## State

All state is files under the Chambers clone, gitignored. No database, no daemon,
no background service that survives a reboot. A restart reconciles from disk.

Setting a task up is transactional. A failure part-way through removes the
worktree, the state it wrote and the registry entry it added, because a
half-created task is worse than none: it reads as work in flight and waits for a
worker that will never arrive.

If a durable background process is ever needed, it ships with an uninstall path
in the same change, and the uninstall is tested.

## Install

Two separate questions, and they have different answers.

### How Chambers arrives

It is cloned. There is no package, no binary and no install step, so nothing is
fetched and nothing needs verifying at install time.

What that leaves is the real exposure, and it is not the clone. **Launching an
agent inside the directory executes whatever hooks the repository registers,
before the first prompt.** That is a disclosure problem rather than an install
one, and it has a design consequence rather than a mitigation:

**Chambers ships few enough hooks that reading them before the first launch is
realistic.** A reader who cannot audit the startup path in one sitting will not
audit it at all, and a tool that cannot be audited cannot be adopted here. Any
change that adds a hook argues for it against that budget.

### What it needs to work

**`git` and `gh`. Nothing else.**

Two substitutions get it there. Worktrees come from `git worktree`, which is
built in. Dispatch comes from the harness's own subagent mechanism. Neither
needs a package.

Should an external tool become genuinely necessary: pinned version, SHA-256
verified before use, fails closed when no hashing tool is present, installs to a
caller-supplied directory, never `sudo`.

**Never:** an unpinned `curl … | sh`, a global package install without a pinned
version, or a dependency whose source cannot be read.

## Permissions

Workers run in their harness's **prompting mode**. A harness with no such mode
is not supported rather than worked around.

This costs unattended throughput, and the cost is the point. **A worker parked
waiting for a decision is the system working, not a fault**, and supervision
reports it as such rather than treating it as a stall to clear.

## Attribution

Commits keep their AI co-author trailers. For an organisation that has to answer
how a change was produced, provenance is a requirement rather than a preference,
and code that lands looking human-authored cannot answer it.

## No inbound instruction channel

Nothing lets text from outside the organisation reach an agent. No relay, no
bridge from a public account, no mention-handling.

The reason is that such a channel is a prompt-injection surface no egress
control addresses: whoever can post at the account can put text in front of an
agent that acts on it.

## Testing

The product is prose an agent executes and scripts that marshal it, so the tests
are of two kinds:

- **Prose that an agent executes has no gate unless one is written.** Every
  instruction that must reach a worker verbatim is pinned by a test, which is
  free to let the surrounding section be rewritten.
- **Every gate asserts its own sensitivity.** Break what it protects, confirm it
  notices, restore. A gate that cannot fail is decoration, and a check that read
  nothing reports that it read nothing rather than reporting no problems.

## Open questions

1. ~~**Which harness.**~~ Settled: Claude Code. It is the harness with a
   configurable permission mode, which is what makes the Permissions section
   buildable rather than aspirational. A second harness waits for a user who
   needs one.
2. ~~**How a matter is registered.**~~ Settled: a committed file naming a path
   to a clone that already exists on the machine, with the origin URL recorded
   beside it as context only.

   A path rather than a URL, because Chambers never clones. Cloning needs
   credentials, and a tool that holds credentials to reach a repository is a
   larger thing to trust than one that reads a directory a human already chose
   to have. A clone that exists is also a consent signal: somebody decided this
   machine should hold that code. Registration then needs no network, and the
   read-only boundary is enforceable by containment against a resolved path
   rather than by parsing a URL.

   The origin URL is recorded because pull requests and links need it, and is
   never the thing anything clones from.
3. **Whether the Bench is a capability or a separate run.** Separate state is
   required; whether that means a distinct process or a distinct context is open.

## Provenance

This is [firstmate](https://github.com/kunchenguid/firstmate)'s design (MIT),
re-implemented smaller and with a different posture. The concept is theirs and
so is most of the hard-won mechanism: one agent you brief, a crew working in
isolation, supervised, with the machinery kept out of the way.

Taken from it directly: worktree-per-task isolation, event-driven supervision
rather than polling, the stalled-worker escalation ladder and the distinction
between a stuck worker and an absent one, structurally typed operational input
so a machine-generated wake is not mistaken for a person, the split between
tasks that deliver and tasks that report, a delivery contract resolved per task
rather than inherited from a standing setting, transactional setup, narrowly
scoped directory grants, and the discipline of pinned, checksum-verified, fail-
closed installers.

This is not a fork. That repository is large — count its `*.sh` files rather
than trusting a figure here, because it is pushed to most days and any number
written down goes stale — and much of its bulk supports a spread of agent
harnesses and terminal backends where this needs one of each. A patched fork
would be a permanent rebase.

The departures above are posture rather than disagreement about the concept,
and they come from auditing that repository for use in this estate: permissions,
dependency surface, commit attribution, reboot persistence, and inbound
channels. Each is argued in its own section on its own terms, so this design can
be read, and disagreed with, by someone who has never seen that project.
