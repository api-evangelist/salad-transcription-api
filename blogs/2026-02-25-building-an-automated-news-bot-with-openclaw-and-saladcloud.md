---
title: "Building an Automated News Bot with OpenClaw and SaladCloud: A Real-World Cost Breakdown"
url: "https://blog.salad.com/building-an-automated-news-bot-with-openclaw-and-saladcloud-a-real-world-cost-breakdown/"
date: "Wed, 25 Feb 2026 20:21:41 +0000"
author: "Maksim Gorkii"
feed_url: "https://blog.salad.com/feed/"
---
<p>We wanted to build something practical with OpenClaw not a demo, but an actual workflow we can use every day. The idea: an agent that continuously pulls news about topics we are interested in, summarizes them using a Salad-hosted LLM, and delivers the summaries straight to a Telegram chat. Every five minutes, around the clock.</p>



<p>Here&#8217;s how we set it up, what it actually costs, and why the numbers make running your own model on SaladCloud an easy choise.</p>



<h2 class="wp-block-heading">The Setup</h2>



<p>The architecture has three parts:</p>



<p><strong>SaladCloud deployment.</strong>&nbsp;We deployed an Ollama container running gpt-oss:20b model on SaladCloud using an RTX 3090 on the lowest priority tier, which costs $0.09 per hour. This model handles all the summarization work.</p>



<p><strong>OpenClaw running locally.</strong>&nbsp;We installed OpenClaw on a local machine and configured it with two model providers: our SaladCloud-hosted model as the primary model, and Claude Opus 4.5 available for any tasks that need heavier reasoning.</p>



<p><strong>Telegram integration.</strong>&nbsp;OpenClaw connects directly to Telegram. The agent sends news summaries to a designated chat, so we do not miss anything of our interest. We can also pull new posts from our channels on Telegram and have the agent summarize those too but we will keep this of this project for now.</p>



<h2 class="wp-block-heading">The Workflow</h2>



<p>Every five minutes, the agent:</p>



<ol class="wp-block-list">
<li>Pulls the latest news on several topics we defined.</li>



<li>Sends the content to our gpt-oss:20b model on SaladCloud for summarization.</li>



<li>Posts the summary to our Telegram chat.</li>
</ol>



<h2 class="wp-block-heading">The Numbers</h2>



<p>After running the workflow, we measured what a typical summarization request actually costs in tokens. Each request uses roughly&nbsp;<strong>8,000 input tokens</strong>&nbsp;(the raw news content being summarized) and&nbsp;<strong>500 output tokens</strong>&nbsp;(the summary itself). With a request every five minutes, that gets to:</p>



<ul class="wp-block-list">
<li><strong>102,000 tokens per hour</strong> (96K input + 6K output)</li>



<li><strong>~2.4 million tokens over 24 hours</strong></li>
</ul>



<p>Now here&#8217;s where the cost comparison gets interesting.</p>



<p><strong>If we ran this on Claude Opus 4.5</strong>&nbsp;at $5 per million input tokens and $25 per million output tokens, the daily bill would be roughly&nbsp;<strong>$15 per day</strong>&nbsp;&#8211; or about&nbsp;<strong>$450 per month</strong>&nbsp;just for automated news summaries.</p>



<p><strong>On SaladCloud</strong>, we&#8217;re running an RTX 3090 at $0.09/hour on the lowest priority tier. Running it 24 hours a day, that&#8217;s&nbsp;<strong>$2.16 per day</strong>&nbsp;&#8211; or about&nbsp;<strong>$65 per month</strong>. That&#8217;s an 86% cost reduction.</p>



<p>We are also only using the model once every five minutes. The GPU sits idle between requests. We could run far more summarization tasks, add more topics, summarize Telegram channels, or run entirely different workloads on the same deployment &#8211; all without spending a single dollar more. On SaladCloud, you pay per hour of compute, not per token. Whether you send 100 requests or 10,000, the cost is the same.</p>



<h2 class="wp-block-heading">Why Smaller Models Work Here</h2>



<p>A 20B parameter model is more than capable of producing high-quality summaries. Summarization is a well-understood task that doesn&#8217;t require frontier-model reasoning since the model needs to read content, identify what matters, and condense it clearly. Modern open-source models at 14B–20B parameters do this extremely well. Same model can be used for other purposes as well, translation for example, or key points extraction.</p>



<p>Where you might still want a bigger model is for the initial setup. Defining the workflow, writing the prompts, configuring the agent behavior, and debugging edge cases might be much quicker and easy on the big models. For that, having Opus 4.5 available as an option in the same OpenClaw config is useful. However once the workflow is running the self-hosted model handles the repetitive summarization work without any quality issues.</p>



<h2 class="wp-block-heading">The Config</h2>



<p>Here&#8217;s the relevant portion of the OpenClaw configuration we used:</p>



<div class="wp-block-kevinbatdorf-code-block-pro"><span><svg height="14" viewBox="0 0 54 14" width="54" xmlns="http://www.w3.org/2000/svg"><g fill="none" fill-rule="evenodd" transform="translate(1 1)"><circle cx="6" cy="6" fill="#FF5F56" r="6" stroke="#E0443E" stroke-width=".5"></circle><circle cx="26" cy="6" fill="#FFBD2E" r="6" stroke="#DEA123" stroke-width=".5"></circle><circle cx="46" cy="6" fill="#27C93F" r="6" stroke="#1AAB29" stroke-width=".5"></circle></g></svg></span><span class="code-block-pro-copy-button" style="color: #d8dee9ff; display: none;" tabindex="0"><pre class="code-block-pro-copy-button-pre"><textarea class="code-block-pro-copy-button-textarea" readonly="readonly" tabindex="-1">{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "&lt;https://your-salad-deployment-url.salad.cloud/v1>",
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
        "primary": "ollama/gpt-oss:20b",
        "fallbacks": &#91;
          "anthropic/claude-opus-4-5"
        &#93;
      },
      "models": {
        "ollama/gpt-oss:20b": { "alias": "gpt-oss" },
        "anthropic/claude-opus-4-5": { "alias": "opus" }
      },
      "heartbeat": {
        "every": "5m",
        "model": "ollama/gpt-oss:20b",
        "target": "last"
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
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">primary</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama/gpt-oss:20b</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">fallbacks</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> &#91;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">anthropic/claude-opus-4-5</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        &#93;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">models</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama/gpt-oss:20b</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">alias</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">gpt-oss</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">anthropic/claude-opus-4-5</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">alias</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">opus</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">heartbeat</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">every</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">5m</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">model</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">ollama/gpt-oss:20b</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">target</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">last</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #ECEFF4;">}</span></span>
<span class="line"></span></code></pre></div>



<p>The SaladCloud-hosted model is the primary for all automated work. Opus is there when we need it &#8211; switch with&nbsp;<code>/model opus</code>&nbsp;&#8211; but the daily grind of summarization runs entirely on our $0.09/hour GPU.</p>



<h2 class="wp-block-heading">Cost Summary</h2>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th></th><th>Claude Opus 4.5</th><th>SaladCloud (RTX 3090)</th></tr></thead><tbody><tr><td>Billing model</td><td>Per token</td><td>Per hour</td></tr><tr><td>Hourly cost (this workload)</td><td>~$0.63</td><td>$0.09</td></tr><tr><td>Daily cost (24h)</td><td>~$15</td><td>$2.16</td></tr><tr><td>Monthly cost</td><td>~$453</td><td>~$65</td></tr><tr><td>Additional usage</td><td>Costs scale linearly</td><td>Already included</td></tr></tbody></table></figure>



<p>The SaladCloud cost stays flat regardless of how much more we use the model. The Opus cost scales with every additional token.</p>



<h2 class="wp-block-heading">Takeaway</h2>



<p>For repetitive, well-defined tasks like news summarization, a smaller model hosted on SaladCloud is dramatically cheaper than using a model API and the quality is more than sufficient. In addition hourly compute model means you can keep adding workloads without extra cost. Our news bot runs every five minutes, but the same deployment could simultaneously handle Telegram channel digests, document summaries, emails, or any other summarization or other task we give it.</p>



<p>Summarization is just one example. Smaller self-hosted models are capable of handling a wide range of everyday agent tasks:</p>



<ul class="wp-block-list">
<li><strong>Email drafting.</strong> Have the agent scan your inbox on a schedule, flag what&#8217;s urgent, and draft replies for routine messages. The input/output pattern is similar to news summarization &#8211; mostly reading, with short structured output.</li>



<li><strong>Code review.</strong> Point OpenClaw at a git diff and ask for a review. Models at 14B–20B can catch bugs, suggest improvements, and flag style issues reliably, especially coding-focused models like Qwen Coder.</li>



<li><strong>Meeting prep and follow-ups.</strong> Pull calendar events and related documents, generate briefing notes before meetings, and draft follow-up action items from notes afterward.</li>



<li><strong>Content repurposing.</strong> Take a blog post and have the agent generate social media posts, email newsletter, or internal summaries &#8211; all different output formats from the same source material.</li>



<li><strong>And many more.</strong> The range of options is huge. Most agents need far less “thinking” than LLM’s you talk to directly, if you provide well-defined instructions. Also new open-source models get released daily and improve quickly.</li>
</ul>



<p>The common thing is that these are all well-defined, repeatable tasks where the model&#8217;s job is to read, process, and produce structured output &#8211; not to reason about novel problems. That&#8217;s the sweet spot for self-hosted models on affordable hardware.</p>



<p>The frontier model might still be needed for building the workflow, handling complex reasoning, and tackling tasks that need it. But for the 90% of agent work that&#8217;s routine, a 20B model on a $0.09/hour GPU gets the job done.</p>
<p>The post <a href="https://blog.salad.com/building-an-automated-news-bot-with-openclaw-and-saladcloud-a-real-world-cost-breakdown/">Building an Automated News Bot with OpenClaw and SaladCloud: A Real-World Cost Breakdown</a> appeared first on <a href="https://blog.salad.com">SaladCloud Blog</a>.</p>
