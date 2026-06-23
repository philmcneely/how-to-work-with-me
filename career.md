# Career Story

**Phil McNeely**

[How to Work With Me](README.md) | [How I Lead](leadership.md) | [How I Think About Technology](technical.md)

## The short version

Fast food to testing to engineering leadership. Self-taught, stubborn, practical. Every role followed the same pattern: find the broken thing, build something better, prove it with numbers, scale it.

## What I am

I'm a tester.

Not QA. Not quality assurance. A tester.

The distinction matters. Michael Bolton wrote about it in 2010 and it's stuck with me ever since: testers don't assure quality. We can't. We don't control the schedule, the budget, the staffing, the scope, or the development model. The people who do control those things — management, executives — they're the ones who assure quality. Or don't.

Weinberg also gave us the definition that frames everything: "Quality is value to some person who matters." That strips away the rigid metrics — defect counts, passing test cases — and puts the focus where it belongs. Quality is personal. It can't be universally standardized because who the software is for determines what it should be. Testing should be geared toward identifying risks and failures that directly impact stakeholders, users, or the business. Testing gathers information about the product — it doesn't fix bugs or improve the software. It tells the product story so the people who matter can make informed business decisions.

We're instruments. We help the people building the product see things they can't see on their own — the edge cases, the assumptions that don't hold, the behavior nobody designed for. We're extra eyes, extra hands, extra paranoia. Weinberg also put it perfectly: "You're in quality assurance? Are you allowed to change the source code? No? Then how can you assure quality?"

I've followed Michael Bolton's work for years. I once argued with Cem Kaner — and if you know Cem, you know that's a credential, not a complaint. I've been in the room with most of the luminaries in testing — James and Jon Bach, Jerry Weinberg, Ben Simo, Doug Hoffman, Paul Holland. None of them would pick me out of a crowd, but their work shaped how I think about testing. The context-driven school. Testing is a human activity. It requires skill, judgment, and critical thinking. It's not a phase in a process. It's not something you automate away. Automation is a tool that helps testers do their work. The thinking is the work.

## What is "Quality Engineering"

My title says Quality Engineering. Most of the industry uses that term now. So what is it?

It's not engineering quality. Developers do that. They write the code that either works or doesn't. You can't test quality into a product any more than you can inspect quality into one. That lesson is older than software — Deming was saying it about manufacturing before any of us were born.

Quality Engineering is engineering the best ways to test.

The craft is testing. The engineering is in how you do it — the frameworks, the infrastructure, the automation, the observability, the feedback loops. You build systems that make testing faster, more reliable, and more useful. You build pipelines that catch problems in minutes instead of hours. You build dashboards that show the team where the risk is before they ship.

The purpose never changes: provide information about the state of the product so the people with authority can make informed decisions about quality. The engineering is about making that information better — faster to produce, harder to ignore, impossible to fake.

## Where it started

I didn't plan any of this.

My first job was a paper route on Fort Wadsworth, Staten Island — riding my bike at 5 AM under the Verrazano-Narrows Bridge, 200 feet above my head, to pick up papers and deliver them. Every day, rain or shine. Before that it was babysitting my brothers and sisters. At 16, I worked at the California Meter Service repairing coin slides for a coin-operated laundry vendor.

Then Arby's. I started as crew and worked my way up to manager, running different stores over time. Won awards at both levels. The store I was at ranked #6 in the country and climbed to #5 while I was there. The culture was total ownership — if something needed doing, you did it. I painted signs, patched walls, fixed plumbing, handled everything short of electrical. You didn't wait for someone else. That mindset stuck. Running a lunch rush in Oceanside with half your crew not showing up teaches you things no management course ever will.

After that I managed a Subway, then ran graveyard shifts at Sullivan's AM/PM where I turned a dead shift into a 250% sales increase by actually giving a damn about the customer experience. All of those jobs taught me how to connect with people — customers became regulars, regulars became relationships, and some of those relationships are still in my life today.

What those years taught me about showing up, doing the work, and handling pressure has been more useful than any computer science class I've taken. Every leadership instinct I have traces back to those early jobs.

This was the era of Windows 3.1, 3.11, Win 95, and NT4 — all happening at once, and DOS was still very much alive. While I was still at Sullivan's AM/PM, I was designing levels and testing a DOS game at QASoft — [Alien Cabal](https://www.mobygames.com/game/6893/alien-cabal/) — and I'm [in the credits](https://archive.org/details/ACabalFull). That was technically my first tech job.

When I started at Kelly/HP, we were still delivering Windows 3.1, then Win 95 — versions A, B, and C — then Win 98, testing across over 20 languages.

I wanted to get the MCSE+I on Windows NT4. Microsoft had a scholarship program, but I made too much money to qualify — even though I worked at a gas station. So I got mad and did the math: the classes cost $14K for seven courses, but the books were $50 each. $350 in books left a lot of wiggle room. I bought the books, got a couple of computers, and did everything in them — not just read, did. The registry, the network stack, the permissions model. My first server was a Pentium Pro 200 — I still miss that machine, those chips were awesome. That lab has never stopped growing. It's currently a fleet of Macs, a GPU server with six RTX 3090s, and more automation than most startups.

The MCSE opened doors everywhere. My first run at HP through Kelly Services I was still working on it, but the second run they wanted WHQL certification and NT4 knowledge made that happen. It helped at Raintree too — they hosted on NT. And it's what got me hired at Winternals, where I knew NT4 cold. In my sleep well. I worked alongside Mark Russinovich and Bryce Cogswell testing their Sysinternals and Winternals products, and supporting customers who had real problems. NASA Johnson Space Center, where an admin told me "I know a group of MCSEs that are going to be out of a job after this" when I solved his issue. Monday Night Football, where they got locked out of the machine that controlled the first down line graphics right before a game — the admin had been fired and locked the system on his way out, as was typical back then. I found a major bug that had already cost the company over $200K in refunds — turned out the install path changed the service load order — one way the software worked, the other it didn't. And the path that broke it happened to be the way the MCSE taught you to install NT. Once I found the root cause, they fixed it and recovered $90K+ in buybacks from customers who needed the product within two weeks of reaching out. That moment taught me the value of a tester who actually thinks.

## Before the big companies

While running Grid Technologies — my own IT services company for over 12 years — I took roles that built the foundation for everything that came after.

I got my first taste of automation at HP — Rational Visual Test 6.0. Record, playback, tweak. Primitive, but it clicked immediately. I'd been doing manual testing and the moment I saw what automation could do, I never looked back. Every job after that, I found a way to automate.

At Hire.com I started as a tester and was promoted to QE team lead. I ended up running a team of four permanent engineers plus contractors, offshore staff in Pune, and up to 12 interns, testing enterprise software across Java/Linux and .NET/Windows platforms. I managed priorities across five product lines and their maintenance releases. That was my first real exposure to distributed teams, offshore coordination, and juggling multiple simultaneous release cycles. We used RSW eTest and SilkTest for automation — and I started the RSW eTest user group in Austin and got the company to sponsor it. I wasn't just using the tools, I was building community around them.

At 360Commerce I led a team testing a J2EE-based point-of-sale system. At Pinion Software I managed testing across three product lines, stood up an offshore team, and built automation frameworks using AutoIt and Badboy. These were small companies with real problems and no budget for mistakes. You learn fast in that environment.

## The automation journey

At HomeAway, I started on VacationRentals.com — one of about 20 brands under the HomeAway umbrella at the time. As a Senior QA Engineer I immediately started building automation — Selenium, then frameworks that other teams could use. I also pioneered API testing at HomeAway, not just E2E UI work. When we delivered the Traveler Service Fee, the bulk of the testing was at the API level — around 500-600 tests run across multiple currencies, languages, and locales, producing something like 330K total test executions. Many of those I wrote myself. On top of that we ran a significant suite of E2E UI tests and manual testing. By that point I'd been automating for years across multiple tools and platforms. The brands eventually rolled together into what became Vrbo, and Expedia acquired the whole thing for $3.9 billion. The work evolved from "write test scripts" to "build testing infrastructure." The team grew. I went from individual contributor to managing 62 people across 5 teams.

The tools changed constantly — Selenium to WebDriverIO to Cypress to Playwright. Jenkins to CircleCI to GitHub Actions. Java to Python to TypeScript. But the pattern was always the same: inherit something manual or fragile, automate it, prove the value with numbers, then scale it so other teams benefit.

At FullStory, I took a team that had been cut from 11 to 4 through restructuring and made it more effective than the original. Release verification went from 2.5 hours of manual checking to a 15-minute automated suite. CI costs dropped 50% by moving runners to our own infrastructure. Build times dropped 32%. Smaller team, lower cost, faster shipping. That's the pattern.

## The AI chapter

In 2024, I completed a post-graduate program in AI/ML at UT Austin. Not because I wanted to build models — because I wanted to understand what AI could and couldn't do for testing.

The answer: a lot, but not what most people think. AI doesn't replace testers. It replaces the tedious parts of what testers do — generating test data, writing boilerplate assertions, triaging failures. The thinking, the judgment, the "what would a confused user do here" intuition — that's still human work. Maybe not forever. But for now.

I built AI-assisted testing tools during my consulting work at Adventures in Testing. Playwright frameworks with AI-powered defect diagnostics in [Python](https://github.com/philmcneely/playwright-ai-framework) and [TypeScript](https://github.com/philmcneely/playwright-ai-framework-ts). A [test generation toolkit](https://github.com/philmcneely/test-forge) that works from Jira tickets and Confluence pages. BDD test creation with vector database pattern reuse. The AI handles the mechanical work. The tester decides what to test and whether the results matter.

That's quality engineering. Engineering the best ways to test — and right now, AI is one of those ways.

## [Veterans4Quality](impact/veterans4quality.md)

From 2012 to 2023, I taught hands-on testing and automation skills to recently retired Veterans through the Veterans4Quality program. Eleven years. I restructured the curriculum, built a progression roadmap, and designed the capstone program. Topics ranged from testing fundamentals to cross-browser testing to API testing to automation basics.

Teaching testing to people coming from military careers — people who understand discipline, process, and attention to detail but have never written a line of code — forced me to get clear about what testing actually is. You can't hide behind jargon when your student was flying helicopters six months ago. You have to explain it in terms that make sense. That made me better at the craft, not just the teaching.

## The testing community

I've been active in the context-driven testing community for years. I attended CAST — the Conference of the Association for Software Testing — four times between 2011 and 2015, including co-presenting "Experiences from Asking Outsource Partners to Shift to Context Driven Testing" with Carl Shaulis. In 2014 I was invited to GTAC, Google's Test Automation Conference — an invite-only event that brought together testing practitioners from across the industry.

These weren't resume-padding conference attendance. CAST is where the people who shaped modern testing thinking — James Bach, Michael Bolton, Cem Kaner — present and debate. Being in those rooms, arguing about what testing actually is and how to do it better, is where a lot of my philosophy comes from.

## The thread through all of it

Every role, every company, every tool change — the constant is this: I look at how something is being done, I figure out a better way to do it, and I build the thing that makes the better way repeatable. Then I teach other people how to use it.

That's testing. That's engineering. That's what I do.
