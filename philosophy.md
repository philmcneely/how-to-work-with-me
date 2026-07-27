# Testing Philosophy

**Phil McNeely**

[How to Work With Me](README.md) | [Career Story](career.md) | [How I Lead](leadership.md) | [How I Think About Technology](technical.md) | [Writing & Projects](writing.md)

## What I am

I'm a tester.

Not QA. Not quality assurance. A tester.

And not just at work. I test everything. I poke at the thing everyone assumes is fine and find the defect nobody wanted found — in a codebase, in a plan, in a "fact" someone stated a little too confidently. People spew "facts" blindly; I love asking *is that actually true?* and watching what happens. Same with assumptions — especially the load-bearing ones nobody thought to check. It drives people a little nuts. It isn't a job I clock into; it's how I'm wired.

Доверяй, но проверяй — trust, but verify. I live by it. I test *to see*, not to confirm: even when I'm certain I know the answer, I'd rather watch it happen than trust the theory. I knew two external GPUs on separate Thunderbolt buses couldn't be pooled into one big model; I wired them together and tried it anyway, just to watch exactly how it broke. Seeing beats assuming. Don't take the claim — check the thing. That's the whole job, dressed up in tooling.

The distinction matters. Michael Bolton wrote about it in 2010 and it's stuck with me ever since: testers don't assure quality. We can't. We don't control the schedule, the budget, the staffing, the scope, or the development model. The people who do control those things — management, executives — they're the ones who assure quality. Or don't.

Weinberg also gave us the definition that frames everything: "Quality is value to some person who matters." That strips away the rigid metrics — defect counts, passing test cases — and puts the focus where it belongs. Quality is personal. It can't be universally standardized because who the software is for determines what it should be. Testing should be geared toward identifying risks and failures that directly impact stakeholders, users, or the business. Testing gathers information about the product — it doesn't fix bugs or improve the software. It tells the product story so the people who matter can make informed business decisions.

We're instruments. We help the people building the product see things they can't see on their own — the edge cases, the assumptions that don't hold, the behavior nobody designed for. We're extra eyes, extra hands, extra paranoia. Weinberg also put it perfectly: "You're in quality assurance? Are you allowed to change the source code? No? Then how can you assure quality?"

None of this is original to me. It comes from the context-driven testing school — the people who argued and wrote and taught this way of thinking long before I picked it up, and who shaped how I approach the craft whether or not they'd ever know my name. I argued with Cem Kaner once — and lost, sorta. If you know Cem, you know that's a credential, not a complaint. Testing is a human activity. It requires skill, judgment, and critical thinking. It's not a phase in a process. It's not something you automate away. Automation is a tool that helps testers do their work. The thinking is the work.

## How my thinking evolved

For most of my career I'd have stopped right there: automation is a tool, the thinking is the work. I still believe the second half completely. The first half I've complicated.

When I started, testing was a craft you practiced and defended — a human activity in constant tension with everyone who wanted to schedule it, script it, or automate it out of existence. The context-driven school gave me the language to defend it, and I'm glad it did. Those truths still hold.

But the field moved, and it moved in a direction I came to agree with. Continuous integration turned "run the tests" from an event into a heartbeat. Shift-left stopped being a slogan and became, simply, the cheapest place to catch a defect. Automation grew up — from a tool a tester picks up into infrastructure a whole organization leans on. And Deming's oldest lesson — you cannot inspect quality into a product — turned out to be the point, not the objection: if you can't inspect it in, you build it in.

So I evolved. I stopped guarding the border between "testing" and "engineering" and started owning what's on both sides of it. I believe in automation now — not as a threat to testing but as the thing that lets testing scale past what one person can hold in their head. I believe in shift-left, in prevention over detection, in pipelines that fail fast and cheap. None of it is a betrayal of where I started. It's the same instinct — доверяй, но проверяй — just wired into the whole system instead of living only in my own hands.

## What Quality Engineering is now

My title says Quality Engineering, and I no longer think that just means "engineering better ways to test." It means owning the system that builds quality in across the entire lifecycle — from refinement and design, through automation and release, into production.

Quality isn't a phase you run before launch. It's a property the whole organization builds in, and the quality engineer owns the system that makes that possible. The aim is to prevent defects; where you can't prevent them, to find them fast and cheap — the earlier the better.

That's the real difference between QA and QE. QA was reactive: validate at the end, execute the tests, find the bugs after the work was already done. QE is proactive: build the architecture and the feedback loops that keep defects from being written in the first place, and catch the ones that slip the moment they land.

In practice it means owning quality strategy across the whole SDLC — shifting left into refinement and design, defining what "ready to release" actually signals, and leading the escape analysis when something reaches production anyway. It means building and curating the quality system as a living product my team ships: the persona and journey model, the end-to-end automation suite, the observability layer that makes flake, escape rate, time-to-feedback, and coverage visible, and the shared tooling developers actually rely on. It means making quality measurable and legible — the metrics that matter and the dashboards that surface them — because information nobody can see changes no decisions. And it means growing quality engineers into force multipliers for the engineers they partner with, because the discipline has to outlive any one person's hands on a keyboard.

None of that is me assuring quality — I still can't, and I've made my peace with it. I don't own the schedule or the scope. What I own is the system that lets the organization build quality in, and the information that makes the real state of it impossible to fake.

The newest part of the job is AI, and it's the same principle one more time. I champion agentic AI across the practice — AI-assisted test generation, agent-based testing — and I own the evaluation discipline and the human-in-the-loop governance that holds AI-assisted work to the same bar as anything written by hand. Trust the agent to do the work; verify it exactly as hard. An agent is a powerful new instrument. It is not an excuse to stop thinking.

## What still holds

Here's what the evolution didn't change: there's still room for testers, and there's still testing.

The tools got better and the remit got bigger, but the thinking is still the work. Automation runs the checks; it doesn't decide what's worth checking. A pipeline tells you a test failed; it doesn't tell you the feature was pointless to begin with. An agent can generate a thousand test cases; it can't tell you which one a real user would actually care about. That judgment — what matters, to whom, and why — is the part of testing that never automated away, and it's the part I was hired for whether the title said "tester" or "quality engineer."

Quality is still value to some person who matters. I still poke at the thing everyone assumes is fine, still ask *is that actually true?*, still trust nothing I haven't checked myself. The job is so much more than it was when I started — the automation, the pipelines, the dashboards, the agents. But underneath all of it, I'm still a tester at heart.

---

Have feedback? [Open an issue](https://github.com/philmcneely/how-to-work-with-me/issues/new/choose).
