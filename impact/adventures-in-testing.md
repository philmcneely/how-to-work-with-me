# Adventures in Testing: AI-Powered Testing Tools

**Consultant** — Jan 2025 to Oct 2025

[Back to Impact](../impact.md) | [Resume](../resume.md)

## The Work

Adventures in Testing was my consulting practice between FullStory and Western Union. I did client work, but the real value of this period was R&D — expanding beyond AI-assisted test creation into the full end-to-end pipeline: from story to test generation to execution. Not just "can AI write a test" but "can AI take a Jira ticket, understand the intent, generate the right tests, and run them."

## What I Built

**On-premises LLM infrastructure** — Architected and deployed local large language model infrastructure supporting 70B parameter models with 4-bit quantization. Operationalized Llama 3.1 8B and Qwen3 14B with a web interface for RAG workflows. No cloud dependency, no API costs, full control.

**AI-assisted Playwright test generation** — Proof of concept combining a local LLM with Playwright MCP to generate end-to-end tests from UI interactions. Streamlined test creation without shipping test logic through external APIs.

**AI-powered testing framework** — Python-based Playwright framework enhanced with LLM-driven diagnostics to analyze test failures and recommend code modifications. Available in [Python](https://github.com/philmcneely/playwright-ai-framework) and [TypeScript](https://github.com/philmcneely/playwright-ai-framework-ts).

**Test generation toolkit** — [test-forge](https://github.com/philmcneely/test-forge): generates tests from Jira tickets and Confluence pages with BDD pattern reuse via vector database.
