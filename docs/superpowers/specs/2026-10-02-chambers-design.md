# Chambers — design

Status: draft for review. Written 2026-10-02.

## What this is

You brief one agent. It allocates the work to a set of capabilities, supervises
them, and brings you the decisions that are yours to make.

It is an agent distro in the sense a comparable tool
([firstmate](https://github.com/kunchenguid/firstmate)) uses the word: a cloned
repository of instructions, skills and helper scripts that turns a general
coding agent into a supervising one. The concept is theirs. What is different
here is the posture, and the posture is the reason this exists separately
rather than as a fork.

**It owns one thing: the layer between an instruction and supervised,
evidence-backed work on a matter.**

## The shape

Four parts, and the separation between them is the design.

| Part | What it is |
|---|---|
| **Chambers** | the capabilities that produce work |
| **The Bench** | reviews what was produced, from the output rather than the workings |
| **You** | the authority neither of the other two has |
| **Matters** | the services and repositories Chambers is instructed on, owned elsewhere |

A barristers' chambers is a set of independent specialists who share
infrastructure and a clerk that allocates work. That is the structure being
borrowed, and it carries two things a ship's crew does not.

**The reviewer is not a member.** A judge is not in chambers, and the
independence is the point: the people who produced the work do not rule on it.
A comparable tool makes its supervisor the captain — top of the chain and
inside it — so every rule it has is a prompt asking the agent nicely. Here the
reviewing component runs with its own state and receives the work product
without the reasoning that produced it.

That constraint is not theoretical. The assessment skill in a sibling
repository has a verifier pass whose whole value is that it receives the
findings and not the reasoning behind them, and it exists because an agent
grading its own work grades generously. The same session that wrote it then
verified its own mutation mapping locally, declared it clean, and was
contradicted by CI twice.

**Matters are outside.** Chambers is read-only over a matter except through
narrow operations a human has approved. This is taken directly from the
comparable tool's first hard rule, and it is what keeps such a tool small: if
services live inside the repository, the repository couples to every service.

## What it will never be

- **A digital service, or part of one.** Nothing it produces runs in front of a
  citizen. It proposes changes that a service team reviews, tests and deploys
  through their own pipeline.
- **The reviewer of its own work.** The Bench has separate state. If that
  separation is ever relaxed for convenience, the component stops meaning
  anything and should be removed rather than weakened.
- **The authority.** Cost, disclosure, release and anything irreversible stay
  with a person. It escalates; it does not decide.
- **A replacement for a team.** It supplies capabilities. The accountable
  humans stay accountable, which in a government setting is what makes it
  adoptable at all.
- **An owner of any matter's content.** A service's code, data and findings
  stay with that service. This repository is public, so no estate's repository
  names, field names, counts or live findings belong in it — illustrations use
  invented names.

## What a capability is

A capability is a role, not a person and not a model. It is three files:

- a **brief template** — what this capability is given and what it returns;
- a **check** — how its output is recognised as finished rather than abandoned;
- a **boundary** — the paths it may read and write.

Capabilities are data. Adding one is adding a directory, not changing the
dispatcher. The first set is deliberately small: implement, review, investigate.
Anything else waits for a second user.

## Dispatch and isolation

Each task runs in its own `git worktree`, created from the matter's clone and
removed on completion. Parallel work on one repository cannot collide, and a
worker that goes wrong has damaged a disposable directory.

**Dispatch goes through the harness's own subagent mechanism**, not through a
terminal multiplexer. The comparable tool needs tmux, herdr, zellij, cmux or
orca because it spawns visible interactive panes a human can type into. That
affordance is real and this design gives it up.

**Assumption, stated because it was not confirmed:** losing the watchable pane
is acceptable. If it is not, a session-backend section is needed and the
dependency position below changes. This is the single assumption most likely to
be wrong, and it is cheap to correct now and expensive later.

## Supervision

A worker is finished, working, or stuck, and only the third needs a human.
Supervision reads the artefacts on disk rather than a dispatch log, because a
log records intent and the disk records fact.

Worth taking from the comparable tool, which learned them expensively: an agent
that batches its writes loses everything when it dies, so a worker writes each
result immediately; a concurrency ceiling that rejects rather than queues means
a task can be recorded as dispatched and never launched, so "no output" has two
causes and only one needs a human.

## State

All state is files under the Chambers clone, gitignored. No database, no daemon,
no background service that survives a reboot. A restart reconciles from disk.

If a durable background process is ever needed, it ships with an uninstall path
in the same change. A comparable tool installs two reboot-surviving launch
agents and nothing in its repository removes either; the tool it recommends for
contributions installs a third.

## Dependencies

**`git` and `gh`. Nothing else.**

This is the clearest departure. Worktrees come from `git worktree`, which is
built in. Dispatch comes from the harness. Neither needs a package.

Should an external tool become genuinely necessary, the rule is the one the
comparable tool already follows in its best code and bypasses in practice:
pinned version, SHA-256 verified before use, fails closed when no hashing tool
is present, installs to a caller-supplied directory, never `sudo`. Its
installer scripts do exactly this; the tools a real user installs go through an
unpinned `curl … | sh` instead.

**Never:** an unpinned `curl … | sh`, a global package install without a pinned
version, or a dependency whose source cannot be read.

## Permissions

Workers run in their harness's **prompting mode**. A harness with no such mode
is not supported rather than worked around.

The comparable tool defaults every worker to permissions disabled. One harness
reads a config file that can select a prompting mode; for the rest the bypass
flag is a string literal in the launch template with nothing that changes it.
Its own comments are straightforward about why: auto-approval is what an
unattended crewmate needs. Giving that up costs unattended throughput, and the
cost is the point.

A worker parked waiting for a decision is the system working, not a fault.
Supervision must report it as such.

## Attribution

Commits keep their AI co-author trailers. The comparable tool strips them by
default through a commit-msg hook, so agent-written code lands looking
human-authored. For an organisation that has to answer how a change was
produced, provenance is a requirement rather than a preference.

## No inbound instruction channel

No relay, no bridge from a public account, nothing that lets text from outside
the organisation reach an agent. A comparable tool offers an opt-in connector
that forwards public mentions to the local agent and posts its replies, where
enabling it is the standing authorisation for autonomous replies. That is a
prompt-injection surface no egress control addresses.

## Testing

The product is prose an agent executes and scripts that marshal it, so the
tests are of two kinds:

- **Prose that an agent executes has no gate unless one is written.** Every
  instruction that must reach a worker verbatim is pinned by a test, which is
  free to let the surrounding section be rewritten.
- **Every gate asserts its own sensitivity.** Break what it protects, confirm it
  notices, restore. A gate that cannot fail is decoration, and a check that read
  nothing reports that it read nothing rather than reporting no problems.

## Open questions

1. **The watchable pane.** Stated as an assumption above. Needs a decision
   before implementation.
2. **Which harnesses.** Supporting one well beats seven badly; the choice
   determines what prompting mode means in practice.
3. **How a matter is registered.** A committed list is the obvious shape, but
   whether it holds a clone URL or a path changes what Chambers can do without
   network access.
4. **Whether the Bench is a capability or a separate run.** Separate state is
   required; whether that means a distinct process or a distinct context is open.

## What this borrows, credited

The concept, the worktree-per-task isolation, event-driven supervision, the
stalled-worker escalation ladder, narrow directory grants, and the pinned and
checksum-verified installer discipline are all from
[firstmate](https://github.com/kunchenguid/firstmate) (MIT). This is not a fork.
That repository is large — count its `*.sh` files rather than trusting a figure
here, because it is pushed to most days and any number written down goes stale —
and much of its bulk supports a spread of agent harnesses and terminal backends
where this needs one of each. A patched fork would be a permanent rebase.
