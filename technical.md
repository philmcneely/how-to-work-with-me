# How I Think About Technology

**Phil McNeely**

[How to Work With Me](README.md) | [How I Lead](leadership.md) | [Career Story](career.md)

This isn't a skills list — that's on the [resume](resume.md). This is how I make technical decisions, what I value in tools and systems, and where I'm honest about my gaps.

## How I evaluate tools

> Доверяй, но проверяй — *Trust, but verify* (Russian)

I don't trust marketing. I don't trust demos. I trust what happens when I put something in front of a real problem and see if it holds up.

When a new tool or framework shows up, my process is simple: try it, break it, decide. I'll spin it up, throw real scenarios at it, and find out where it falls apart. That tells me more in an afternoon than a month of vendor calls. If it survives that, I'll push it further — scale, edge cases, integration with what we already have. If it still holds, we talk about adoption.

I don't chase shiny. The tool that's been working for two years beats the tool that launched last week, unless the new one solves a problem the old one genuinely can't. Switching costs are real. Migration pain is real. "But it's newer" is not a reason.

## Build vs. buy

I lean build, but I'm not dogmatic about it. If a vendor tool does what we need, is priced reasonably, and doesn't create a dependency we can't walk away from, I'll use it. But if we can build it ourselves and own the result, I'd rather do that.

The reason isn't ego — it's control. When you own the tool, you understand it. When it breaks, you can fix it. When your needs change, you can adapt it. When the vendor doubles their price or gets acquired, you're not scrambling. I've been through enough vendor pivots and surprise pricing changes to value that independence.

The exception is commodity infrastructure. I'm not building my own database or my own cloud provider. But anything that sits close to our core workflow — testing frameworks, CI pipelines, observability — I want us to own.

## Automation

> 急がば回れ — *If in a hurry, take the roundabout path* (Japanese)

Automation is a tool, not a goal. "Automate everything" is a bumper sticker, not a strategy.

The right question is always: what's the value of automating this? If a manual process runs once a quarter and takes twenty minutes, automating it is a hobby project, not a priority. If it runs daily and takes two hours, that's a different conversation.

I automate for speed, consistency, and confidence. Automated tests don't get tired. They don't skip steps because it's Friday afternoon. They run the same way every time. That's the value — not replacing people, but freeing them to do the work that actually requires thinking.

The trap is automating too early. If you don't understand the problem well enough yet, you'll automate the wrong thing and spend more time maintaining the automation than you saved. Get it working manually first. Understand the failure modes. Then automate.

## AI and ML — what's real

I completed a post-graduate program in AI/ML at UT Austin. I run models on my own hardware. I'm not theorizing about AI — I'm using it every day.

What AI does well in testing: generating test data, writing boilerplate, triaging failures, identifying patterns in logs, summarizing results. The mechanical work. It's genuinely good at that and getting better.

What AI doesn't do well: judgment. Knowing what to test. Understanding why a user would do something unexpected. Deciding whether a behavior is a bug or a feature. That's still human work. The people selling "AI replaces your QA team" are either lying or don't understand what testers actually do.

I'm building with AI, not waiting for it. AI-assisted test generation, defect diagnostics, pattern reuse from vector databases. The AI handles the mechanical work. The tester decides what matters. That's the right division of labor — for now.

I'm also skeptical of hype cycles. Every few years the industry decides something will change everything — cloud, DevOps, microservices, AI. Each one changed some things. None of them changed everything. The pattern is always the same: overpromise, overcorrect, settle into the actual value. I try to skip the first two steps and go straight to the third.

## How I approach technical problems

> 千里之行，始于足下 — *A journey of a thousand miles begins with a single step* (Chinese — [Lao Tzu](https://en.wikipedia.org/wiki/Tao_Te_Ching))

Start simple. Verify assumptions before building on them. The number of times I've seen teams build elaborate solutions to problems they didn't actually have is embarrassing.

When something's broken, I resist the urge to [jump to conclusions](https://www.youtube.com/watch?v=sDEL4Ty950Q). First symptom is rarely the root cause. I dig until I understand why it's happening, not just what's happening. Then I fix the actual problem, not the symptom.

I think in systems, not components. A fix that solves the problem in one place but creates a problem somewhere else isn't a fix. Understanding how things connect is more valuable than understanding any single piece in isolation.

## What I know

I'm not a computer scientist and I'm not a full-stack developer. I'm a practitioner. Everything I know, I learned by building something with it — and there are gaps. I know that.

Where I'm strong: infrastructure, automation, CI/CD pipelines, testing frameworks, and building the tooling that connects them. I've been through every generation of browser automation — Selenium to Playwright — and every CI platform along the way. I've managed systems from the metal up since NT4. I can read code in most languages, write production tooling in Python and JavaScript, and I understand what's happening under the hood well enough to troubleshoot things most people hand off. I build my own tools: AI-powered test frameworks in [Python](https://github.com/philmcneely/playwright-ai-framework) and [TypeScript](https://github.com/philmcneely/playwright-ai-framework-ts), a [test generation toolkit](https://github.com/philmcneely/test-forge) that works from Jira tickets and Confluence pages, and a [TDD coaching skill](https://github.com/philmcneely/tdd-coach) for AI-assisted development, and a [Telegram bot](https://github.com/philmcneely/claude-telegram-bot) that lets me text Claude Code from my phone in ~100 lines of Python.

Where I'm still growing: I'm deeper in AI/ML than most people in my role — I run models on my own hardware and built [ML pipelines, neural networks, NLP, and computer vision projects](https://github.com/philmcneely/AI-ML-Class) through a post-graduate program at UT Austin — but I'm not a machine learning engineer. I know enough to build with it, evaluate it honestly, and not get sold snake oil. That gap is closing fast because I'm in it every day.

I also know where my instincts work against me. I'm a builder — when something is broken, my first impulse is to jump in and fix it myself. At the IC level that was a superpower. At the leadership level, it means I sometimes do work I should be developing someone else to do. I'm aware of it, and I actively work against it, but the pull is real.

Organizational politics challenge me. I'd rather fix the problem than navigate the politics around it. At this level, navigating the politics often is fixing the problem — getting the right people aligned, building the coalition, framing the ask. I've gotten better at it, but it doesn't come naturally and it costs me more energy than the technical work does.

I'm an introvert leading in an extrovert's role. Stakeholder management, presentations, hallway influence — I can do all of it, and I do it well. But it challenges me in a way it doesn't drain extroverted leaders. I manage that energy deliberately, but it's a real constraint.

I also build trust slowly. Most people see a version of me before they see the whole picture. That's not distrust — it's just how I'm wired. In a new role, it means relationship-building with peers and stakeholders takes me longer than it might for someone more immediately open. The relationships I do build tend to be durable, but the ramp-up is real.

Testing is where I go deepest. For eleven years I [taught testing to Veterans](https://github.com/philmcneely/Class12CapstoneExample) transitioning into tech — people with no prior software experience — and took them from fundamentals through test planning, test case development, and automation. You don't teach something for that long without knowing it cold.

I'm not the person who's going to write your microservice from scratch. I'm the person who's going to make sure it works, ship it faster, and build the systems that keep it working.

## Technical debt

> Petit à petit, l'oiseau fait son nid — *Little by little, the bird builds its nest* (French)

All codebases have it. The question isn't whether you have technical debt — it's whether you're managing it or ignoring it.

I treat technical debt like financial debt. Some of it is strategic — you took a shortcut to hit a deadline and you know you'll pay it back. That's fine as long as you actually pay it back. Some of it is accidental — nobody realized the shortcut was a shortcut until it started causing problems. That happens. The dangerous kind is the debt nobody's tracking. It compounds silently until something breaks in production and suddenly you're spending a week on something that should have been a one-hour fix six months ago.

I build paying down technical debt into the regular roadmap. Not as a separate initiative — as part of the work. Every sprint should leave the codebase a little better than it found it. If you only address tech debt when things are on fire, you'll always be on fire.
