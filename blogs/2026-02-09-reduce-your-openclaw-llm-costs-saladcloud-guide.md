---
title: "Reduce Your OpenClaw LLM Costs: SaladCloud Guide"
url: "https://blog.salad.com/reduce-your-openclaw-llm-costs-saladcloud-guide/"
date: "Mon, 09 Feb 2026 20:28:15 +0000"
author: "Maksim Gorkii"
feed_url: "https://blog.salad.com/feed/"
---
<p>OpenClaw (previously known as Moltbot and Clawdbot) is one of the hottest open-source projects of early 2026. It&#8217;s a personal AI agent that lives on your computer and can actually do things for you such as browse the web, manage files, send messages, check your calendar, and more.</p>



<p>Soon after the initial setup, a practical question arises for most of the users: what does it actually cost to run, and how isolated is my data? The software itself is free, but that&#8217;s not the whole story. You still have to account for the API fees that can accumulate pretty quickly.</p>



<p>This guide breaks down OpenClaw&#8217;s real-world LLM costs and shows you how to reduce them using SaladCloud. We&#8217;ll cover why API costs get out of control, the economics of different deployment models, and practical strategies to cut your bills. With self-hosting your models, you also gain stronger data privacy, if you grant OpenClaw elevated permissions, it may pull sensitive information from third-party services or your local machine that you wouldn&#8217;t want sent to an external API provider.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">What Is OpenClaw?</h2>



<p>At its core, OpenClaw is a self-hosted, open-source personal AI agent. You run it on your own machine: Mac, Windows (with WSL2), or Linux. You can also run it inside a Docker container, which adds an important security boundary by isolating execution, reducing exposure of local credentials, and creating a controlled environment where experiments don&#8217;t affect your main system.</p>



<p>What makes OpenClaw different from a regular chatbot is that it can act, not just chat. It&#8217;s built to be an agent with access to your system. According to the official documentation, its main capabilities include:</p>



<p><strong>Full system access.</strong>&nbsp;It can read and write files on your computer and run terminal commands.</p>



<p><strong>Browser control.</strong>&nbsp;It can go online, browse websites, and fill out forms autonomously.</p>



<p><strong>Chat integration.</strong>&nbsp;It connects directly to WhatsApp, Telegram, Slack, Discord, Signal, iMessage, and more, letting you interact with it from the platforms you already use.</p>



<p><strong>Persistent memory.</strong>&nbsp;It learns from your conversations, building a personalized understanding of your needs and preferences over time.</p>



<p><strong>Proactive heartbeat.</strong>&nbsp;You can configure it to perform tasks on a schedule such as sending morning briefings, checking your calendar, monitoring your inbox, or surfacing anything that needs your attention.</p>



<p>The key concept to understand is that OpenClaw isn&#8217;t the &#8220;brain.&#8221; It&#8217;s the framework that connects to a powerful AI model of your choice. You can plug it into models like Anthropic&#8217;s Claude 4.5, OpenAI&#8217;s GPT-5, or run self-hosted open-source models on local or remote machines. This decision shapes the agent&#8217;s intelligence and, more importantly, its operational cost.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">The Cost Problem: OpenClaw Is Free, But LLMs Are Not</h2>



<p>Here&#8217;s the catch that surprises most new OpenClaw users: the software is completely free and open-source, but the AI models it relies on are not. When you use OpenClaw with cloud API providers like Anthropic or OpenAI, you&#8217;re paying per token &#8211; every input you send and every response you get back.</p>



<h3 class="wp-block-heading">What Is a Token?</h3>



<p>Tokens are the basic units that language models process. Think of them as the chunks the AI uses to understand and generate text. They&#8217;re not exactly words &#8211; a token might be a complete word like &#8220;hello,&#8221; a word fragment like &#8220;un&#8221;, a punctuation mark, a space, or a special character. For English, a rough rule of thumb is that one token equals about four characters, or roughly three-quarters of a word. This varies across languages and content types.</p>



<h3 class="wp-block-heading">The Main Reasons OpenClaw Burns Through Tokens</h3>



<p>The model you will see in most of the OpenClaw tutorials is Claude Opus 4.5 which represents the premium tier at $5 per million input tokens and $25 per million output tokens. But raw pricing is only part of the story. OpenClaw&#8217;s architecture amplifies token usage in several specific ways:</p>



<p><strong>1. Context accumulation.</strong> Every time you chat with OpenClaw, the entire conversation history is saved in JSON files within <code>.~/.openclaw/agents/&lt;agentId>/sessions/sessions.json</code>. With every new request, OpenClaw sends the full session transcript to the model. In that case tens of thousands of tokens can be consumed with every request sent. The same accumulation happens during multi-round reasoning for complex tasks, where each round carries an increasingly large context.</p>



<p><strong>2. Tool output storage.</strong>&nbsp;When OpenClaw runs commands or reads files, the entire output gets stored in the session transcript. A single directory listing or config schema dump can inject tens of thousands of tokens into your context that get resent with every subsequent message.</p>



<p><strong>3. System prompt overhead.</strong> OpenClaw assembles a complex system prompt on every API call that includes workspace files, tool definitions, and skill metadata. </p>



<p><strong>4. Heartbeat overhead.</strong>&nbsp;OpenClaw&#8217;s Heartbeat feature wakes the agent every 30 minutes by default. Each heartbeat is a full API call carrying the complete session context. At 48 heartbeats per day with a moderate context window, that&#8217;s millions of tokens daily for checking whether anything needs attention.</p>



<p><strong>5. Sub-agent spawning.</strong>&nbsp;When your main agent delegates work to sub-agents for parallel processing, each sub-agent also consumes tokens using your primary model unless you configure it otherwise.</p>



<p><strong>6. Poor model selection for simple tasks.</strong>&nbsp;Using Opus for a heartbeat check is, as one guide put it, &#8220;like hiring a lawyer to check your mailbox.&#8221;</p>



<p>In practice, many users report their first month&#8217;s bill exceeding <strong>$200-500</strong> with Claude Opus 4.5, with heavy users hitting <strong>$1,000</strong> or more as their workflows mature. Misconfigured heartbeats alone can burn through $50 in a single day. One user reported 5.7 million tokens consumed overnight &#8211; most of it from heartbeats and scheduled tasks they&#8217;d forgotten were running. These aren&#8217;t edge cases, they&#8217;re the natural result of an always-on agent architecture where every interaction, every background check, and every sub-agent task multiplies your per-token spend. So how to can you avoid this? </p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Strategy 1: Run OpenClaw&#8217;s Brain on SaladCloud</h2>



<p>The most impactful cost reduction comes from switching from pay-per-token API calls to a flat hourly rate for GPU compute.</p>



<h3 class="wp-block-heading">Why SaladCloud?</h3>



<p>SaladCloud is the world&#8217;s largest distributed GPU cloud, powered by thousands of individual nodes across 190+ countries. SaladClouds model allows to offer pricing that dramatically undercuts traditional cloud providers:</p>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>GPU</th><th>SaladCloud (Batch)</th><th>Closest Competitor</th></tr></thead><tbody><tr><td>RTX 4090 (24GB VRAM)</td><td>$0.16/hr</td><td>over $0.20</td></tr><tr><td>RTX 3090 (24GB VRAM)</td><td>$0.09/hr</td><td>over $0.20</td></tr><tr><td>RTX 5090 (32GB VRAM)</td><td>$0.25/hr</td><td>over $0.69</td></tr></tbody></table></figure>



<p>Additional cost advantages include no data ingress or egress fees, no cold start billing (you only pay once your container reaches a running state), pay-as-you-go with no prepaid contracts, and the ability to start and stop deployments on demand or on schedule.</p>



<h3 class="wp-block-heading">The Key Difference: Hourly Compute vs. Per-Token Billing</h3>



<p>When you deploy an LLM on SaladCloud, you pay for compute time not for tokens. That means you can generate as many tokens as you want and only pay for how long your machine is running. Every cost amplifier described above &#8211; context growth, heartbeats, sub-agents, system prompts stop affecting your bill. Your cost is fixed and predictable. When you’re not using OpenClaw, simply stop the deployment or let <a href="https://docs.salad.com/container-engine/how-to-guides/autoscaling/time-of-day-scaling">scheduled autoscaling</a> scale it down to zero.</p>



<h3 class="wp-block-heading">How to Deploy an LLM on SaladCloud</h3>



<p>SaladCloud provides ready-to-deploy recipes for the three most popular LLM inference servers: <strong>Ollama, vLLM, and TGI </strong>(Text Generation Inference). Each serves models via an OpenAI-compatible <code>/v1/chat/completions</code> endpoint, so they all work with OpenClaw&#8217;s <code>openai-completions</code> API mode. The choice between them depends on your model and performance needs.</p>



<p>To deploy any recipe, go to the&nbsp;<a href="https://portal.salad.com/">SaladCloud Portal</a>, click&nbsp;<strong>Deploy a Container Group</strong>, and choose from the available recipes. Every recipe asks you to configure similar core parameters:</p>



<ul class="wp-block-list">
<li><strong>Model name:</strong> Which model to serve (e.g., <code>llama3.1</code>, <code>qwen3:14b</code>, <code>meta-llama/Llama-3.1-8B-Instruct</code>, <code>Qwen/Qwen2.5-7B-Instruct</code>).</li>



<li><strong>Hugging Face Token:</strong> Optional but required for gated or private models on Hugging Face (like Llama).</li>



<li><strong>Replicas:</strong> How many instances to run. Recipes default to 3 replicas. For single-user OpenClaw setups with fallbacks configured, one replica can be enough. Larger replica counts are recommended for better reliablility, shared and production workloads.</li>



<li><strong>Authentication:</strong> For easier OpenClaw configuration disable authentication on the container gateway.</li>
</ul>



<p>When you deploy, SaladCloud will find qualified nodes and begin downloading the container image and model weights. Once nodes show a green checkmark in the &#8220;Ready&#8221; column, your endpoint is live. Your API endpoint URL will appear on the deployment page, looking something like&nbsp;<code>https://vegetable-words-3e487ysdyhfkvjah.salad.cloud</code>.</p>



<h3 class="wp-block-heading">Choosing Between Ollama, vLLM, and TGI</h3>



<p><strong>Ollama</strong> is the easiest starting point for OpenClaw. You pick any model from the Ollama library by name, and the recipe handles the rest. The trade-off is that Ollama is designed for simplicity over maximum throughput. Ollama is not optimized for high-concurrency per server, but it&#8217;s still great for agent use.</p>



<p><strong>vLLM</strong>&nbsp;provides higher throughput through continuous batching, PagedAttention for optimized memory management, and quantization support (FP8, AWQ, GPTQ).&nbsp;<strong>TGI</strong>&nbsp;(Text Generation Inference by Hugging Face) provides optimized inference for text generation models with features like continuous batching, tensor parallelism, and efficient memory management. Both of those servers might be faster than Ollama, but also need larger GPU’s to handle bigger models.</p>



<p>For more details, see the recipe guides:</p>



<ul class="wp-block-list">
<li><a href="https://docs.salad.com/container-engine/reference/recipes/ollama">Ollama Recipes</a></li>



<li><a href="https://docs.salad.com/container-engine/reference/recipes/vllm">vLLM Recipe</a></li>



<li><a href="https://docs.salad.com/container-engine/reference/recipes/tgi">TGI API Recipes</a></li>



<li><a href="https://docs.salad.com/guides/llm/llm-general">LLM Inference General Guide</a></li>
</ul>



<h3 class="wp-block-heading">Connecting OpenClaw to Your SaladCloud Endpoint</h3>



<p>Once your LLM server is running on SaladCloud, you need to tell OpenClaw to use it. Open your config file at&nbsp;<code>~/.openclaw/openclaw.json</code>&nbsp;and update the&nbsp;<code>models</code>&nbsp;configuration.</p>



<p>Here&#8217;s a complete example using Ollama on SaladCloud with GLM-4.7-Flash:</p>



<div class="wp-block-kevinbatdorf-code-block-pro"><span><svg height="14" viewBox="0 0 54 14" width="54" xmlns="http://www.w3.org/2000/svg"><g fill="none" fill-rule="evenodd" transform="translate(1 1)"><circle cx="6" cy="6" fill="#FF5F56" r="6" stroke="#E0443E" stroke-width=".5"></circle><circle cx="26" cy="6" fill="#FFBD2E" r="6" stroke="#DEA123" stroke-width=".5"></circle><circle cx="46" cy="6" fill="#27C93F" r="6" stroke="#1AAB29" stroke-width=".5"></circle></g></svg></span><span class="code-block-pro-copy-button" style="color: #d8dee9ff; display: none;" tabindex="0"><pre class="code-block-pro-copy-button-pre"><textarea class="code-block-pro-copy-button-textarea" readonly="readonly" tabindex="-1">{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "&lt;https://your-salad-deployment-url.salad.cloud/v1>",
        "apiKey": "ollama-local",
        "api": "openai-completions",
        "models": [
          {
            "id": "glm-4.7-flash",
            "name": "GLM 4.7 Flash",
            "reasoning": false,
            "input": &#91;"text"&#93;,
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 128000,
            "maxTokens": 8192
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/glm-4.7-flash"
      }
    }
  }
}
</textarea></pre><svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path class="with-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"></path><path class="without-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round" stroke-linejoin="round"></path></svg></span><pre class="shiki nord" style="background-color: #2e3440ff;" tabindex="0"><code><span class="line"><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">models</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;">: </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">providers</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">baseUrl</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">&lt;https://your-salad-deployment-url.salad.cloud/v1&gt;</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">apiKey</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama-local</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">api</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">openai-completions</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">models</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> [</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">id</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">glm-4.7-flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">name</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">GLM 4.7 Flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">reasoning</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #81A1C1;">false</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">input</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> &#91;</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">text</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;">&#93;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cost</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">input</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">output</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cacheRead</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cacheWrite</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">contextWindow</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">128000</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">maxTokens</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">8192</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        ]</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">agents</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;">: </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">defaults</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">model</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">primary</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama/glm-4.7-flash</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #ECEFF4;">}</span></span>
<span class="line"></span></code></pre></div>



<p>A few notes on this configuration:</p>



<ul class="wp-block-list">
<li><strong><code>baseUrl</code></strong> should be the domain name from your SaladCloud deployment page with <code>/v1</code> appended (for the OpenAI-compatible API).</li>



<li><strong><code>apiKey</code></strong> can be any placeholder value since Ollama doesn&#8217;t require a real key.</li>



<li><strong><code>cost</code></strong> is set to zero across the board because you&#8217;re paying for compute time, not tokens. This also means OpenClaw&#8217;s built-in cost tracking won&#8217;t inflate your reported spend.</li>



<li><strong><code>reasoning</code></strong> should be set to <code>false</code> for most models unless they specifically support reasoning/thinking output (like DeepSeek R1). Setting it to <code>true</code> incorrectly can cause tool-call errors.</li>



<li><strong><code>contextWindow</code></strong> should match the actual context length of your model. OpenClaw uses this to determine when to trigger compaction.</li>
</ul>



<p>After saving the config, restart the OpenClaw gateway for changes to take effect:</p>



<div class="wp-block-kevinbatdorf-code-block-pro"><span><svg height="14" viewBox="0 0 54 14" width="54" xmlns="http://www.w3.org/2000/svg"><g fill="none" fill-rule="evenodd" transform="translate(1 1)"><circle cx="6" cy="6" fill="#FF5F56" r="6" stroke="#E0443E" stroke-width=".5"></circle><circle cx="26" cy="6" fill="#FFBD2E" r="6" stroke="#DEA123" stroke-width=".5"></circle><circle cx="46" cy="6" fill="#27C93F" r="6" stroke="#1AAB29" stroke-width=".5"></circle></g></svg></span><span class="code-block-pro-copy-button" style="color: #d8dee9ff; display: none;" tabindex="0"><pre class="code-block-pro-copy-button-pre"><textarea class="code-block-pro-copy-button-textarea" readonly="readonly" tabindex="-1">openclaw gateway restart
</textarea></pre><svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path class="with-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"></path><path class="without-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round" stroke-linejoin="round"></path></svg></span><pre class="shiki nord" style="background-color: #2e3440ff;" tabindex="0"><code><span class="line"><span style="color: #D8DEE9;">openclaw</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">gateway</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">restart</span></span>
<span class="line"></span></code></pre></div>



<p>Ollama supports running multiple models on a single server. This is useful if you want different models for specific tasks. Simply list additional models in the <code>models</code> array and reference them in your agent configuration. For example, you could run <code>glm-4.7-flash</code> for general conversation and <code>qwen2.5-coder</code> for coding tasks on the same deployment if hardware permits. Keep in mind that running multiple large models concurrently requires enough VRAM to hold them all. For better experience it is recommended to use a separate deployment for each model.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Strategy 2: Smart Multi-Model Routing</h2>



<p>Most OpenClaw tutorials only show how to set up the agent with one model. That&#8217;s the easy path, but it&#8217;s the most expensive one. The fundamental idea is simple: not every task deserves your most expensive model.</p>



<h3 class="wp-block-heading">The Problem with the Default Configuration</h3>



<p>Most configs look like this:</p>



<div class="wp-block-kevinbatdorf-code-block-pro"><span><svg height="14" viewBox="0 0 54 14" width="54" xmlns="http://www.w3.org/2000/svg"><g fill="none" fill-rule="evenodd" transform="translate(1 1)"><circle cx="6" cy="6" fill="#FF5F56" r="6" stroke="#E0443E" stroke-width=".5"></circle><circle cx="26" cy="6" fill="#FFBD2E" r="6" stroke="#DEA123" stroke-width=".5"></circle><circle cx="46" cy="6" fill="#27C93F" r="6" stroke="#1AAB29" stroke-width=".5"></circle></g></svg></span><span class="code-block-pro-copy-button" style="color: #d8dee9ff; display: none;" tabindex="0"><pre class="code-block-pro-copy-button-pre"><textarea class="code-block-pro-copy-button-textarea" readonly="readonly" tabindex="-1">{
  "agents": {
    "defaults": {
      "model": "anthropic/claude-opus-4-5"
    }
  }
}
</textarea></pre><svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path class="with-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"></path><path class="without-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round" stroke-linejoin="round"></path></svg></span><pre class="shiki nord" style="background-color: #2e3440ff;" tabindex="0"><code><span class="line"><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">agents</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;">: </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">defaults</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">model</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">anthropic/claude-opus-4-5</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #ECEFF4;">}</span></span>
<span class="line"></span></code></pre></div>



<p>There is only one model and everything is sent to it. Heartbeats, sub-agents, simple lookups all of it uses the most expensive model. For a heartbeat that just checks whether your inbox has anything urgent, there&#8217;s no need to use the biggest model.</p>



<p>The key is understanding and making the correct model decision for your particular use case. For example, you can use your self-hosted SaladCloud model as the default, and only route to expensive cloud APIs when you actually need them. You can also do it in an opposite way &#8211; pass the main flow through a big model and let the smaller self-hosted models do the smaller tasks. Here&#8217;s a deployment-ready configuration that shows how to define multiple custom providers each pointing to a different SaladCloud deployment with API fallbacks:</p>



<div class="wp-block-kevinbatdorf-code-block-pro"><span><svg height="14" viewBox="0 0 54 14" width="54" xmlns="http://www.w3.org/2000/svg"><g fill="none" fill-rule="evenodd" transform="translate(1 1)"><circle cx="6" cy="6" fill="#FF5F56" r="6" stroke="#E0443E" stroke-width=".5"></circle><circle cx="26" cy="6" fill="#FFBD2E" r="6" stroke="#DEA123" stroke-width=".5"></circle><circle cx="46" cy="6" fill="#27C93F" r="6" stroke="#1AAB29" stroke-width=".5"></circle></g></svg></span><span class="code-block-pro-copy-button" style="color: #d8dee9ff; display: none;" tabindex="0"><pre class="code-block-pro-copy-button-pre"><textarea class="code-block-pro-copy-button-textarea" readonly="readonly" tabindex="-1">{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "&lt;https://prune-egg-stp8tels3izgcb61.salad.cloud/v1>",
        "apiKey": "ollama-local",
        "api": "openai-completions",
        "models": [
          {
            "id": "glm-4.7-flash",
            "name": "glm-4.7-flash",
            "reasoning": false,
            "input": &#91;"text"&#93;,
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 128000,
            "maxTokens": 8192
          }
        ]
      },
      "ollama-2": {
        "baseUrl": "&lt;https://guava-thyme-3rjbiukd7h54hj0z.salad.cloud/v1>",
        "apiKey": "ollama-local",
        "api": "openai-completions",
        "models": [
          {
            "id": "gpt-oss:20b",
            "name": "gpt-oss:20b",
            "reasoning": false,
            "input": &#91;"text"&#93;,
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 128000,
            "maxTokens": 8192
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/glm-4.7-flash",
        "fallbacks": &#91;
          "ollama-2/gpt-oss:20b"
        &#93;
      },
      "models": {
        "ollama/glm-4.7-flash": { "alias": "ollama-glm" },
        "ollama-2/gpt-oss:20b": { "alias": "ollama-gpt-oss" },
        "google/gemini-3-flash": { "alias": "gemini-flash" },
        "deepseek/deepseek-chat": { "alias": "deepseek" }
      },
      "heartbeat": {
        "every": "30m",
        "model": "ollama/glm-4.7-flash",
        "target": "last",
        "activeHours": { "start": "08:00", "end": "23:00" }
      },
      "subagents": {
        "model": "ollama/glm-4.7-flash",
        "maxConcurrent": 4,
        "archiveAfterMinutes": 60
      },
      "contextTokens": 100000,
      "contextPruning": {
        "mode": "cache-ttl",
        "ttl": "5m"
      },
      "compaction": {
        "mode": "safeguard",
        "memoryFlush": {
          "enabled": true,
          "softThresholdTokens": 40000
        }
      }
    }
  }
}
</textarea></pre><svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path class="with-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"></path><path class="without-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round" stroke-linejoin="round"></path></svg></span><pre class="shiki nord" style="background-color: #2e3440ff;" tabindex="0"><code><span class="line"><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">models</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;">: </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">providers</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">baseUrl</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">&lt;https://prune-egg-stp8tels3izgcb61.salad.cloud/v1&gt;</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">apiKey</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama-local</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">api</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">openai-completions</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">models</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> [</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">id</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">glm-4.7-flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">name</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">glm-4.7-flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">reasoning</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #81A1C1;">false</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">input</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> &#91;</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">text</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;">&#93;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cost</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">input</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">output</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cacheRead</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cacheWrite</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">contextWindow</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">128000</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">maxTokens</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">8192</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        ]</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama-2</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">baseUrl</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">&lt;https://guava-thyme-3rjbiukd7h54hj0z.salad.cloud/v1&gt;</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">apiKey</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama-local</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">api</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">openai-completions</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">models</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> [</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">id</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">gpt-oss:20b</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">name</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">gpt-oss:20b</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">reasoning</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #81A1C1;">false</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">input</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> &#91;</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">text</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;">&#93;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cost</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">input</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">output</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cacheRead</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">              </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cacheWrite</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">0</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">contextWindow</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">128000</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">            </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">maxTokens</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">8192</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        ]</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">agents</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;">: </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">defaults</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">model</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">primary</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama/glm-4.7-flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">fallbacks</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> &#91;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama-2/gpt-oss:20b</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        &#93;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">models</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama/glm-4.7-flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">alias</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama-glm</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama-2/gpt-oss:20b</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">alias</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama-gpt-oss</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">google/gemini-3-flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">alias</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">gemini-flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">deepseek/deepseek-chat</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">alias</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">deepseek</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">heartbeat</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">every</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">30m</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">model</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama/glm-4.7-flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">target</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">last</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">activeHours</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">start</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">08:00</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">end</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">23:00</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">subagents</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">model</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama/glm-4.7-flash</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">maxConcurrent</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">4</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">archiveAfterMinutes</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">60</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">contextTokens</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">100000</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">contextPruning</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">mode</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">cache-ttl</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ttl</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">5m</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">compaction</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">mode</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">safeguard</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">memoryFlush</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">enabled</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #81A1C1;">true</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">softThresholdTokens</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">40000</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #ECEFF4;">}</span></span>
<span class="line"></span></code></pre></div>



<p>Here&#8217;s what each part of this configuration achieves:</p>



<p><strong>Primary model on SaladCloud.</strong>&nbsp;All default interactions use your self-hosted GLM-4.7-Flash model running on SaladCloud with zero per-token cost.</p>



<p><strong>Multiple SaladCloud providers.</strong> Notice the two separate providers-  <code>ollama</code> and <code>ollama</code>-2, each pointing to a different SaladCloud deployment URL. This is how you run different models on separate container groups (servers) and reference them independently. Each provider gets its own name, and you reference models as <code>provider-name/model-id</code>.</p>



<p><strong>Heartbeats on SaladCloud with active hours.</strong> Instead of burning cloud credits every 30 minutes around the clock, heartbeats hit your flat-rate SaladCloud instance spending nothing on top of compute usage. For hours when you do not use your agents, set SaladCloud autoscaler to scale down to 0 and set <code>activeHours</code> in OpenClaw config to only the hours your model runs.</p>



<p><strong>Cloud fallbacks for resilience.</strong> If all your SaladCloud deployments are temporarily unavailable (node reallocation, for example), the agent gracefully falls back to Gemini, then DeepSeek.</p>



<p>Config file goes in your home directory: <code>~/.openclaw/openclaw.json</code>. Save it and restart the gateway.</p>



<p>You can also switch models on the fly without editing your config:</p>



<div class="wp-block-kevinbatdorf-code-block-pro"><span><svg height="14" viewBox="0 0 54 14" width="54" xmlns="http://www.w3.org/2000/svg"><g fill="none" fill-rule="evenodd" transform="translate(1 1)"><circle cx="6" cy="6" fill="#FF5F56" r="6" stroke="#E0443E" stroke-width=".5"></circle><circle cx="26" cy="6" fill="#FFBD2E" r="6" stroke="#DEA123" stroke-width=".5"></circle><circle cx="46" cy="6" fill="#27C93F" r="6" stroke="#1AAB29" stroke-width=".5"></circle></g></svg></span><span class="code-block-pro-copy-button" style="color: #d8dee9ff; display: none;" tabindex="0"><pre class="code-block-pro-copy-button-pre"><textarea class="code-block-pro-copy-button-textarea" readonly="readonly" tabindex="-1">/models #Shows a picker with all your models 

/model &lt;model-name> # Switch to your desired model
</textarea></pre><svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path class="with-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"></path><path class="without-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round" stroke-linejoin="round"></path></svg></span><pre class="shiki nord" style="background-color: #2e3440ff;" tabindex="0"><code><span class="line"><span style="color: #81A1C1;">/</span><span style="color: #D8DEE9;">models</span><span style="color: #D8DEE9FF;"> #</span><span style="color: #D8DEE9;">Shows</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">a</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">picker</span><span style="color: #D8DEE9FF;"> </span><span style="color: #81A1C1;">with</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">all</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">your</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">models</span><span style="color: #D8DEE9FF;"> </span></span>
<span class="line"></span>
<span class="line"><span style="color: #81A1C1;">/</span><span style="color: #D8DEE9;">model</span><span style="color: #D8DEE9FF;"> </span><span style="color: #81A1C1;">&lt;</span><span style="color: #D8DEE9;">model</span><span style="color: #81A1C1;">-</span><span style="color: #D8DEE9;">name</span><span style="color: #81A1C1;">&gt;</span><span style="color: #D8DEE9FF;"> # </span><span style="color: #D8DEE9;">Switch</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">to</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">your</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">desired</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">model</span></span>
<span class="line"></span></code></pre></div>



<p>This gives you real-time cost control. If you are working on something complex that needs deep reasoning, or need to perform a coding task you can just switch to a different model.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Managing Your SaladCloud Deployment</h2>



<p>A few practical tips for running your LLM for OpenClaw</p>



<h4 class="wp-block-heading">Shut Down When Not in Use</h4>



<p>Since you pay by compute time, stop the deployment when you&#8217;re sleeping or away. You can do this manually from the portal, or automate it by using s<a href="https://docs.salad.com/container-engine/how-to-guides/autoscaling/time-of-day-scaling">cheduled scaling</a>. </p>



<h4 class="wp-block-heading">Right-Size Your GPU</h4>



<p>Don&#8217;t pay for an RTX 4090 if you&#8217;re running a 7B model will Ollama that fits comfortably on a cheaper GPU. Match the GPU to the model&#8217;s VRAM requirements.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Choose the Right Model</h2>



<p>Not all models are equally suited for OpenClaw&#8217;s agent workload. The key requirement is reliable tool calling &#8211;  OpenClaw relies heavily on function/tool calls to interact with your system, and models that hallucinate tool calls or produce malformed JSON will cause agent errors.</p>



<p>Based on community experience, here are models that work well with OpenClaw on SaladCloud:</p>



<p><strong>For general conversation and agent tasks:</strong>&nbsp;GLM-4.7-Flash offers strong tool-calling performance with relatively modest VRAM requirements. Community members have reported excellent results. GLM models also support thinking/reasoning when configured with the appropriate parameters.</p>



<p><strong>For coding tasks:</strong>&nbsp;Qwen 2.5 Coder (14B or 32B) and the Qwen 3 Coder series are well-regarded for tool calling and code generation. The 32B variant fits on a single RTX 4090.</p>



<p><strong>For reasoning-heavy tasks:</strong> DeepSeek R1 (32B) is another open-source model that provides solid reasoning capabilities. </p>



<p><strong>Minimum size recommendation:</strong>&nbsp;For the main model use at least a 14B+ parameter model. Models at 8B parameters and below may hallucinate tool calls, lose context in multi-step tasks, or produce corrupted responses during tool-use sequences. The OpenClaw community has converged on 14B as the minimum for reliable agent behavior.</p>



<p><strong>Important:</strong> Use models with a minimum context window of 16K tokens. For comfortable agent operation with tool outputs and conversation history, aim for 32K+ context.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Security Benefits of Self-Hosting</h2>



<p>Beyond cost savings, running your LLM on SaladCloud provides meaningful data privacy advantages. When OpenClaw has elevated permissions such as access to your file system, email, calendar, messaging apps it processes a lot of sensitive data. With cloud APIs, every piece of that context gets sent to Anthropic&#8217;s or OpenAI&#8217;s servers.</p>



<p>By self-hosting your model, the data flows only between your OpenClaw instance and your SaladCloud deployment. Your prompts and responses don&#8217;t touch third-party API providers. This is especially relevant for users who connect OpenClaw to services like email, Slack, or financial tools &#8211; scenarios where the agent might process information you&#8217;d rather not share externally.</p>



<p>Self-hosting also eliminates a prompt injection risk: when OpenClaw processes external content (emails, web pages, documents) through a cloud API, that content is sent to the API provider. With a self-hosted model, external content stays within your infrastructure.</p>



<h2 class="wp-block-heading">Getting Started</h2>



<p>Here&#8217;s the quickest path to cutting your OpenClaw costs with SaladCloud:</p>



<ol class="wp-block-list">
<li><strong>Create a SaladCloud account</strong> at <a href="https://portal.salad.com/">portal.salad.com</a>.</li>



<li><strong>Deploy an Ollama recipe</strong> &#8211; pick a model like GLM-4.7-Flash or Qwen 3.</li>



<li><strong>Wait for nodes to be ready</strong> &#8211; look for green checkmarks in the &#8220;Ready&#8221; column.</li>



<li><strong>Copy your endpoint URL</strong> from the deployment page.</li>



<li><strong>Update your OpenClaw config</strong> at <code>~/.openclaw/openclaw.json</code> using the configuration examples above.</li>



<li><strong>Validate and restart</strong>. Run &#8220;<code>openclaw doctor --fix</code>&#8221; to adjust your config if needed, then <code>"openclaw gateway restart</code>&#8220;.</li>



<li><strong>Test with a simple message</strong> to verify your agent is using the SaladCloud-hosted model. Use <code>/status</code> to confirm the active model or /models to see all models available.</li>



<li><strong>Set up the multi-model routing</strong> so expensive APIs are only called when you explicitly switch or when SaladCloud is unavailable.</li>
</ol>



<p>Once it&#8217;s working, configure active hours on your heartbeat, enable context pruning, and set up automated start/stop schedules for your SaladCloud deployment. Your wallet will thank you.</p>



<p></p>
<p>The post <a href="https://blog.salad.com/reduce-your-openclaw-llm-costs-saladcloud-guide/">Reduce Your OpenClaw LLM Costs: SaladCloud Guide</a> appeared first on <a href="https://blog.salad.com">SaladCloud Blog</a>.</p>
