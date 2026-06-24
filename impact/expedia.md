# Expedia Group: Quality Engineering for Lodging

**Senior Engineering Manager, Operational Excellence** — Jul 2020 to Sep 2022

[Back to Impact](../impact.md) | [Resume](../resume.md)

## The Role

Expedia acquired HomeAway in 2015, but for years Vrbo still operated semi-independently. During that transition — while I was still technically under the HomeAway/Vrbo banner — I reinvented my team from quality engineering into CI pipeline operations. We migrated from the legacy build system to Jenkins and delivered a modular pipeline as a product. By working with the platform teams, we built it so any team could spin up a new product — complete with a functioning hello world for each of our main platforms and a full CI pipeline — ready to push to production from minute one. Everything included, ready to go. We offered customizable pipeline steps and consulted with teams on new needs. We also controlled the SOX gate to keep deployments compliant.

We owned that Jenkins infrastructure 24/7 and ran it well — a typical 120-day snapshot showed 99.5% build stability across 218K+ builds, 424 production deploys per week, and 36-minute median deployment lead time. We built the dashboards ourselves — Software Delivery and Pipeline Infrastructure — with drill-down trending, all tied to DORA 2018 Report benchmarks. We ran a tiered SLA system with PagerDuty for P1 incidents and 4-hour response times. When something downstream broke and developers couldn't deploy, it was our pipeline that took the call.

When Vrbo fully integrated into the Expedia platform, that CI work got absorbed into Expedia Group's processes and tooling. I reinvented the role again — this time as "Lodging Opex Automation and Quality Advocacy," focused on improving quality practices across the Lodging organization as Vrbo incorporated into the larger Expedia platform.

## Quality Advocacy Across Lodging

This was hands-on quality consulting across Expedia Group's Lodging domain teams. The work included:

- **Test stratification coaching** — working with Property Location, Property Images, and other domain teams to define which tests belong at which layer (unit, integration, system, E2E) and which tooling fits each
- **API architecture review from a quality standpoint** — reviewing Property Location API architecture and brainstorming quality approaches with the engineering teams
- **Test analysis and feedback** — analyzing existing and newly created tests, providing coaching on heuristics of test creation
- **Tooling guidance** — assisting teams in implementing testing tooling appropriate to their layer and context

## Release Resiliency

I drove the "Release Resiliency" initiative across Expedia Group with three goals:

1. Create a testing community that drives best practices across EG, resulting in a reduction of change-related failures
2. Provide consistent tools to unlock proper testing of software releases — low-touch effort for shift-left testing
3. Provide a progressive deployment capability that allows for automated deployment and rollback

## Customer Obsessed Quality Strategy

I developed a structured quality strategy framework that moved from strategy through implementation:

- **Who** — define the customers in scope when defining quality
- **What** — translate customer quality expectations into measurable outcomes
- **Where** — identify quality drivers and levers across the technology ecosystem
- **What/How** — define the end state vision for each driver
- **How** — gap analysis, remediation plan, and program alignment
- **When** — roadmap alignment

This is the framework I used to get the CTO's ear on testing strategy and get principal engineers and senior director/VP-level architects to accept an overall testing strategy and shared understanding. That was the foundation for moving toward re-implementation across the org.

Ultimately, the CTO hired a VP of Quality who took things in a different direction. After 15 years in the HomeAway/Expedia ecosystem, the culture I'd built my career in was gone. FullStory offered a Director role with the scope and promotion I was looking for, so I moved on and let the new VP do her thing. We still talk.

## What I Learned

This role taught me more about navigating large-org politics than any other. It also taught me what it feels like to have real impact constrained by structure — and when it's time to move on.

## Budget Ownership

Owned the CloudBees/Jenkins enterprise contract and AWS spend for our services and infrastructure. Everyone estimated expenses monthly and got graded on accuracy — I hit an A almost every month.

## Tools and Infrastructure

JIRA, Confluence, IntelliJ, Groovy, Jenkins, AWS, Artifactory, Docker, Node.js, GitHub Actions, WebDriverIO, NightwatchJS.
