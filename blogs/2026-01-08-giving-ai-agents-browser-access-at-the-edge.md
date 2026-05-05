---
title: "Giving AI Agents Browser Access at the Edge"
url: "https://blog.salad.com/ai-agents-browsers-at-the-edge/"
date: "Thu, 08 Jan 2026 20:02:43 +0000"
author: "Maksim Gorkii"
feed_url: "https://blog.salad.com/feed/"
---
<p>AI agents like <a href="https://claude.com/product/claude-code">Claude Code</a> and <a href="https://openai.com/codex/">Codex</a> are becoming capable of increasingly sophisticated tasks—making and executing plans over hundreds or thousands of turns. Like us, they often need access to information from websites to do their work. Unlike us, they&#8217;re frequently blocked.</p>



<p>Bot prevention takes many forms, but one of the most common is simply blocking IP address ranges associated with datacenters and filtering out browser user agents tied to known crawlers. It&#8217;s effective against naive scraping, but it also blocks legitimate agent workflows.</p>



<h2 class="wp-block-heading">MCProxy: Browsers Where They&#8217;re Least Expected</h2>



<p><a href="https://github.com/SaladTechnologies/mcproxy">MCProxy</a> lets your agents use full web browsers running on remote residential PCs across <a href="https://salad.com/salad-container-engine">Salad’s globally distributed cloud</a>, sidestepping the most common bot prevention mechanisms. Because it runs on Salad Container Engine, billing is friendlier than standard residential proxy services—starting at less than $0.01/hr per geolocation, with unmetered bandwidth and requests.</p>



<p>The system has two components:</p>



<ol class="wp-block-list">
<li><strong>The MCP server</strong> runs locally on your machine and connects to your agent</li>



<li><strong>The Browser server</strong> deploys on SaladCloud and runs the actual browsers</li>
</ol>



<p>If you&#8217;re new to MCP (Model Context Protocol), check out <a href="https://www.notion.so/The-Market-What-is-the-TAM-for-Salad-Golem-a6101c69ad424f34843e66ccb5f53d1b?pvs=21">this primer</a>, but the core idea is straightforward: MCP provides a uniform standard for exposing tools and resources to AI agents, expanding their capabilities without additional training.</p>



<p>The browser server uses <a href="https://playwright.dev/">Playwright</a> to run headless browsers on SaladCloud nodes, exposing interaction capabilities to your agent through its MCP connection. Your agent can take screenshots, click or drag on specific locations, type text, and execute JavaScript. A single agent can even maintain multiple browser contexts across different geolocations—say, a Firefox session in the US and a Chromium instance in Germany—or emulate mobile devices.</p>



<h2 class="wp-block-heading">Deploying on SaladCloud</h2>



<p>Getting started is straightforward. You&#8217;ll need a <a href="https://portal.salad.com">SaladCloud account</a> with <a href="https://docs.salad.com/container-engine/explanation/billing-pricing/billing">credits added</a> to your organization. From there, deploy a container group using the <a href="https://docs.salad.com/container-engine/reference/recipes/mcproxy">MCProxy Recipe</a>.</p>



<p>The recipe configuration page lets you:</p>



<ul class="wp-block-list">
<li>Set an auth token (you&#8217;ll use this to configure your local MCP server)</li>



<li>Choose a number of replicas—each one runs on a PC in a random part of Salad&#8217;s network, spread across 150+ countries</li>
</ul>



<p>As configured, replicas cost $0.008/hr each. They get 1 vCPU and 4GB of RAM, which handles most use cases comfortably. You can adjust resource allocation as needed—and here&#8217;s an interesting quirk: Salad doesn&#8217;t charge extra for vCPU and RAM when you add a GPU. Since GPUs start at $0.015/hr, there are configurations where adding a GPU is actually cheaper than scaling up CPU and memory alone.</p>



<p>Want to restrict browsers to specific countries? You can update the container group <a href="https://docs.salad.com/reference/saladcloud-api/container-groups/update-container-group#body-country-codes-one-of-0">via the API</a> after deployment.</p>



<h2 class="wp-block-heading">Connecting Your Agent</h2>



<p>Once your container group is running, you&#8217;ll see an Access Domain Name in the dashboard. Grab this value and replace <code>https</code> with <code>wss</code>. Combined with the auth token you set during configuration, you&#8217;ll have everything needed to configure your local MCP server.</p>



<p>If you deployed via the recipe, the full MCP configuration appears in the readme for your container group—just copy and paste.</p>



<h2 class="wp-block-heading">What This Unlocks</h2>



<p>As AI agents take on more complex, multi-step tasks, their ability to interact with the web becomes increasingly important. Research, verification, form submission, data gathering—these are fundamental capabilities that most agents need but struggle to access reliably.</p>



<p>MCProxy removes one of the more frustrating barriers. By giving agents access to real browsers running on residential hardware across a global network, you&#8217;re not just avoiding bot detection—you&#8217;re giving your agents the same view of the web that actual users see. That means accurate pricing data, region-specific content, and access to services that would otherwise be invisible behind IP-based restrictions.</p>



<p>We&#8217;re still in the early days of figuring out what agents can do when given the right tools. Browser access at the edge is one piece of that puzzle—and at less than a penny per hour per location, it&#8217;s now cheap enough to experiment freely.</p>



<p><strong>Ready to get started?</strong> Check out the <a href="https://github.com/SaladTechnologies/mcproxy">MCProxy repo</a> or deploy directly from the <a href="https://docs.salad.com/container-engine/reference/recipes/mcproxy">recipe</a>.</p>



<p></p>
<p>The post <a href="https://blog.salad.com/ai-agents-browsers-at-the-edge/">Giving AI Agents Browser Access at the Edge</a> appeared first on <a href="https://blog.salad.com">SaladCloud Blog</a>.</p>
