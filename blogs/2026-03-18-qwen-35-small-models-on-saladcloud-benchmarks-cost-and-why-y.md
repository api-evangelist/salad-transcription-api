---
title: "Qwen 3.5 Small Models on SaladCloud: Benchmarks, Cost, and Why You Don’t Need a Mac Mini"
url: "https://blog.salad.com/qwen-3-5-small-models-on-saladcloud-benchmarks-cost-and-why-you-dont-need-a-mac-mini/"
date: "Wed, 18 Mar 2026 13:29:44 +0000"
author: "Maksim Gorkii"
feed_url: "https://blog.salad.com/feed/"
---
<p></p>



<p>On March 2, 2026, Alibaba&#8217;s Qwen team shipped Qwen 3.5 Small Model Series: the 0.8B, 2B, 4B, and 9B. The benchmark numbers they shared are hard to ignore. The 9B variant is very close or even outperforms models that are 10 times its size across language, vision, and agentic benchmarks while running on a single consumer GPU. The 4B performs far above its weight class, matching the performance of last-generation models with nearly 8x more parameters on visual agent tasks. All four are Apache 2.0 licensed, natively multimodal, and support 262K token context windows.</p>



<figure class="wp-block-image size-large"><img alt="" class="wp-image-1456" height="485" src="https://blog.salad.com/wp-content/uploads/2026/03/qwen_benchmark-1024x485.png" width="1024" /></figure>



<p>These aren&#8217;t shrunken versions of a bigger model. The Qwen 3.5 small series was built from the ground up using the same Gated DeltaNet hybrid architecture behind the 397B flagship: a 3:1 ratio of linear attention to full softmax attention, multi-token prediction, and early-fusion multimodal training on trillions of tokens. We won&#8217;t dig deep into the architecture here since plenty of articles already cover that. The result is a family of models that set a new bar for intelligence per parameter.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Why This Matters for Agentic Workflows</h2>



<p>The Qwen 3.5 small models aren&#8217;t the only notable release of early 2026, but they landed at a moment when they matter most. Over the past couple of years, new models and benchmarks have been dropping weekly with each model slightly better than the last and most people stopped paying attention. But the past few months were different.</p>



<p>One of the biggest shifts was OpenClaw, which pushed the conversation away from chatbots and toward always-on, tool-using agents that people can actually run themselves. OpenClaw, a self-hosted AI agent framework, and OpenCode, an open-source terminal-based coding agent, have each attracted thousands of GitHub stars and together serve millions of developers monthly. Some now describe OpenClaw as the next step in the AI revolution: a self-hosted agent that connects to WhatsApp, Slack, or Telegram, manages calendars, triages inboxes, writes code, and runs automated workflows 24/7, with everything except the model itself running on your own hardware in many setups.</p>



<p>That shift matters because these systems are no longer just “one prompt in, one answer out.” They run continuously, chaining dozens of calls into a single workflow, and that creates the problem the community has been running into: cost and scalability. OpenClaw needs at least 64K tokens of context per prompt cycle to operate effectively as an agent, assembling system instructions, conversation history, tool schemas, skills, and memory into every request. Running frontier cloud models this way gets expensive fast. One developer even described coming back to a $5,000 bill after leaving cloud-based agents running unsupervised for only a few hours.</p>



<p>This is where the economics start to matter. If every step in an agent loop runs on a frontier API model, costs rise quickly, especially for workflows that stay active all day, maintain long histories, or repeatedly call tools. Fully self-hosting everything on a large local model introduces a different set of tradeoffs around VRAM, throughput, concurrency and scalability.</p>



<p>As a result, more teams are moving toward a tiered inference architecture. In this setup, a frontier model handles planning and the highest-stakes decisions, a mid-tier model takes on heavier coding and reasoning work, and a small, fast, low-cost model handles the constant background operations that keep agent systems running. Tasks like summarization, transcript processing, classification, data organization, context management, and tool-call preparation often account for 70% or more of total token usage in a typical agentic workflow. Moving those steps to a 4B or 9B model instead of a frontier API can reduce costs by an order of magnitude with minimal impact on output quality.</p>



<p>This is where the Qwen 3.5 small models become especially compelling. They are not meant to replace frontier models across the board, but they can now cover far more than we usually expect from a “small” model. Beyond lightweight agent tasks, they are increasingly capable in coding, reasoning, and math as well. They still will not outperform the strongest frontier models on every benchmark, but they do not need to. In narrower, well-scoped workflows, a smaller specialized model can be the better tool for the job. That is what makes this release so well timed: the Qwen 3.5 small series is not just efficient &#8211; it is capable enough to handle a meaningful share of real-world agent work.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">The Mac Mini Question</h2>



<p>This shift toward local inference has started a hardware gold rush. Developers are spending $600 to $1,200+ on Mac Minis to run models locally via Ollama, LM Studio, and other inference servers.&nbsp;It is an appealing setup: you can run agents privately, keep them online 24/7, and avoid paying high API costs to external LLM providers.</p>



<p>But the idea comes with another set of questions. Do you really want to buy hardware just to run agents? What happens if a new model comes out next month and that machine suddenly feels outdated or unnecessary? And what if your workload grows? Are you going to buy more boxes and slowly turn your home or office into a mini datacenter? The more practical question is this:<strong>&nbsp;</strong>for the price of a single Mac Mini, how long could you run the same models in the cloud instead?</p>



<p>That is exactly what we set out to answer. We deployed several Qwen 3.5 variants on SaladCloud and ran a series of benchmarks across those small models. We measured latency, throughput, time per task, and price per token to understand  how fast these models run and how much useful work they can deliver for the money.</p>



<p>The answer is that at SaladCloud pricing, you can run Qwen 3.5 models continuously, 24/7, for&nbsp;months&nbsp;for the cost of a single Mac Mini. And unlike a Mac Mini sitting on your desk, SaladCloud gives you elastic scaling, no hardware maintenance, no upfront expenses, and the ability to scale usage up or down based on your needs.</p>



<p>In this post, we will walk through  benchmark results, real performance numbers, cost breakdowns, and practical guidance on how these models fit into agentic workflows using tools like OpenClaw, Claude Code, and more. Whether you are a solo developer running a swarm of agents or a team trying to offload high-volume inference from an expensive frontier-model budget, the data points in the same direction.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Deploying the Models on SaladCloud</h2>



<p>SaladCloud maintains ready-to-deploy recipes for several of the most widely used LLM inference servers, including Ollama, vLLM, TGI (Text Generation Inference), and llama.cpp. Each recipe exposes an OpenAI-compatible&nbsp;<code>/v1/chat/completions</code>&nbsp;endpoint, which standardizes how downstream tools interact with the model regardless of which server is running underneath. If you want a full walkthrough for using SaladCloud-hosted LLMs with OpenClaw, see this <a href="https://blog.salad.com/reduce-your-openclaw-llm-costs-saladcloud-guide/">guide</a>.</p>



<p>The deployment process is straightforward. SaladCloud currently offers GPUs with up to 32GB of VRAM (RTX 5090 at the high end).&nbsp;For most models and their versions you can check VRAM requirement on its Hugging Face model card &#8211; most list the memory requirement for each quantization level, making it simple to determine which models fit on available hardware. All of the Qwen 3.5 small variants (0.6B, 0.8B, 2B, 4B, and 9B) run comfortably within SaladCloud&#8217;s GPU range, even at higher precision. The 27B Unsloth-quantized variant also fits on a 24GB card.</p>



<p>For each model in our benchmark, the steps were:</p>



<ol class="wp-block-list">
<li>Select an inference server <a href="https://docs.salad.com/container-engine/explanation/core-concepts/recipes-overview#what-are-recipes">recipe</a> in the <a href="http://portal.salad.com">SaladCloud portal</a> (Ollama, vLLM, or llama.cpp depending on the test configuration).</li>



<li>Configure the model name, GPU tier, and replica count.</li>



<li>Wait for node allocation &#8211; SaladCloud finds qualified nodes, downloads the container image and model weights, and reports readiness with a green status mark.</li>



<li>Verify the endpoint is live using the&nbsp;<code>curl</code>&nbsp;command provided on the recipe page, which sends a test chat completion request to the deployed URL.</li>
</ol>



<p>Once each endpoint responded correctly, we had a working inference API at a SaladCloud URL (e.g.,&nbsp;<code>https://vegetable-words-xxxx.salad.cloud</code>) ready for benchmark traffic.</p>



<p>The specific configurations we tried to run:</p>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>Model</th><th>Inference Server</th><th>Notes</th></tr></thead><tbody><tr><td>Qwen 3.5 9B</td><td>Ollama</td><td>Q4_K_M quantization (Ollama default)</td></tr><tr><td>Qwen 3.5 4B</td><td>Ollama</td><td>Q4_K_M quantization</td></tr><tr><td>Qwen 3.5 2B</td><td>Ollama</td><td>Q4_K_M quantization</td></tr><tr><td>Qwen 3.5 4B</td><td>vLLM</td><td>FP16 precision; instruct variant required for fair comparison</td></tr><tr><td>Qwen 3.5 9B</td><td>llama.cpp</td><td>GGUF format</td></tr><tr><td>Qwen 3.5 27B (Unsloth)</td><td>llama.cpp</td><td>4-bit quantization, fits 24GB VRAM</td></tr></tbody></table></figure>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">A Note on Ollama&#8217;s Silent Quantization</h3>



<p>One detail worth calling out, because it caused us real confusion early on: Ollama applies Q4_K_M quantization by default. This is documented in the model description if you look for it, but it&#8217;s not reflected in the model name, the main model card page, or the pull command. When you run&nbsp;<code>ollama pull qwen3.5:9b</code>, what you get is a 4-bit quantized version not the full BF16 model. More importantly, the benchmark scores displayed on Ollama&#8217;s model cards are the official numbers from the full-precision model. It is not specified on the page that these scores weren&#8217;t produced with the quantized variant you&#8217;re actually downloading.</p>



<p>When we ran our first benchmarks on the Ollama deployments and compared the results to the numbers on the model card, there was a gap. It took some investigation to determine that the discrepancy wasn&#8217;t a configuration error or a deployment issue on our end it was simply that we were evaluating a Q4_K_M model against full-precision reference scores. Once we understood this, the results made sense, but the initial mismatch was a source of unnecessary debugging.</p>



<p>vLLM, by contrast, runs at FP16 by default. That means direct benchmark comparisons between Ollama and vLLM deployments of the same model size&nbsp;aren&#8217;t apples-to-apples since the vLLM variant retains more precision and will generally score higher on accuracy benchmarks. If you run the same weights with the same precision, quantization, and decoding settings, you should expect the same accuracy. What changes across servers and hardware is performance: latency, throughput, memory efficiency, concurrency, startup time, and format support. When scores differ, the reason is usually not the server itself, but differences in the model variant actually being served.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Benchmark Setup</h2>



<h3 class="wp-block-heading">Evaluation Framework</h3>



<p>We used&nbsp;<a href="https://github.com/EleutherAI/lm-evaluation-harness">lm-evaluation-harness</a>&nbsp;(EleutherAI) as our primary evaluation framework. It provides a standardized interface for running LLM benchmarks against OpenAI-compatible endpoints via its&nbsp;local-chat-completions&nbsp;backend, which made it a natural fit for remote deployments on SaladCloud.</p>



<h3 class="wp-block-heading">What We Actually Ran</h3>



<p>Not every benchmark in lm-eval works with a chat completions endpoint. Many classic benchmarks including HellaSwag, ARC-Challenge, TriviaQA, and standard MMLU rely on loglikelihood scoring, which requires token-level probability outputs that chat APIs do not expose. In practice, these tasks fail with a&nbsp;<code>NotImplementedError</code>, sometimes only after hours of generation.</p>



<p>After testing multiple datasets we focused on two benchmarks that work reliably with OpenAI-compatible chat endpoints:&nbsp;GSM8K<strong>&#8211;</strong>CoT for math reasoning and&nbsp;IFEval&nbsp;for instruction following. That gave us two strong signals that matter for real-world agentic workloads: whether the model can follow directions consistently, and whether it can reason its way through structured multi-step problems.</p>



<p>We did not try to force a larger benchmark suite simply for the sake of having more rows in a table. Qwen 3.5 already comes with extensive official benchmark coverage across language, vision, coding, and agentic tasks. Our goal was to measure how these models behave when deployed the way many developers would actually run them today. Those two benchmarks gave us enough to check capability and deployment quality, while also producing enough requests to calculate meaningful average performance metrics such as throughput, latency, token usage, and calculate cost per request. In other words, even with only two primary datasets, we still generated a benchmark workload large enough to evaluate not just accuracy, but real operating characteristics.</p>



<p>There was also a technical  reason to keep the set narrow. Different models and inference servers return outputs in different formats, especially when thinking mode is involved. Some place reasoning in a separate field, some inline it, and some wrap it in custom tags. That makes it difficult to adapt a benchmarking tool across every model and endpoint style without writing model-specific parsing logic. </p>



<h3 class="wp-block-heading">What We Had to Do to Make It Work</h3>



<p>Out of the box, lm-eval does not work cleanly with remote Ollama or vLLM endpoints. We had to solve several issues before the runs were usable.</p>



<p><strong>C</strong>ontext window defaults.&nbsp;Ollama defaults to a 2048-token context window regardless of the model’s actual capacity. Qwen 3.5 supports up to 262K tokens natively, so longer benchmark prompts were being silently truncated. We had to force a larger&nbsp;<code>num_ctx</code>&nbsp;in every request.</p>



<p><strong>l</strong>m-eval’s internal max length cap<strong>.</strong>&nbsp;lm-eval also caps context at 2048 tokens unless overridden. We fixed this by passing&nbsp;<code>max_length </code>in&nbsp;<code>model_args</code>.</p>



<p>lm-eval’s hardcoded&nbsp;<code>max_tokens=256</code>.&nbsp;By default, lm-eval limits each request to 256 output tokens. For chain-of-thought tasks, that is far too short and cuts the model off before it reaches an answer. We solved this by forcing&nbsp;<code>max_tokens </code>at the proxy layer.</p>



<p>Thinking-mode response formatting<strong>.</strong>&nbsp;Qwen 3.5 models run in thinking mode by default, but different inference servers expose that thinking content differently. Ollama places it in a separate&nbsp;<code>reasoning</code>&nbsp;field and may leave&nbsp;<code>content</code>&nbsp;empty. vLLM includes it inline inside&nbsp;<code>&lt;think&gt;...&lt;/think&gt;</code>&nbsp;tags before the final answer. Neither format matches what lm-eval expects to score.</p>



<h3 class="wp-block-heading">The Proxy</h3>



<p>Rather than patching lm-eval directly, we placed a lightweight Flask proxy between lm-eval and the deployed model endpoints. All benchmark requests hit&nbsp;<code>localhost</code>, the proxy normalized both the request and the response, and then forwarded traffic to the actual SaladCloud endpoint.</p>



<p>This solved the compatibility issues above and gave us one more useful benefit: per-request logging of latency and token usage to CSV. That data became the basis for the cost and performance analysis that follows.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Results: Qwen 3.5 9B on Ollama</h2>



<p>This is the configuration many developers will try first: the largest of the small models, deployed through Ollama, which appeared to default to Q4_K_M quantization. We ran full benchmark suites for GSM8K-CoT (1,319 samples) and IFEval (541 samples) against a single SaladCloud endpoint.</p>



<h3 class="wp-block-heading">Accuracy</h3>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>Benchmark</th><th>Metric</th><th>Our Score (Q4_K_M)</th><th>Official Score (BF16)</th></tr></thead><tbody><tr><td>GSM8K-CoT</td><td>exact_match (strict)</td><td>82.8% </td><td>—</td></tr><tr><td>GSM8K-CoT</td><td>exact_match (flexible)</td><td>80.1% </td><td>—</td></tr><tr><td>IFEval</td><td>inst_level_strict_acc</td><td>89.2%</td><td>91.5%</td></tr><tr><td>IFEval</td><td>inst_level_loose_acc</td><td>90.9%</td><td>—</td></tr><tr><td>IFEval</td><td>prompt_level_strict_acc</td><td>85.6% </td><td>—</td></tr><tr><td>IFEval</td><td>prompt_level_loose_acc</td><td>87.6% </td><td>—</td></tr></tbody></table></figure>



<p>The IFEval results are especially strong. Our Q4_K_M deployment scored 89.2% on instruction-level strict accuracy, only 2.3 points below the official BF16 score of 91.5%. That is a relatively small quantization penalty for a 4-bit model, and 89.2% remains competitive with much larger models.</p>



<p>Qwen does not publish an official GSM8K-CoT score for the 9B model, so there is no direct apples-to-apples comparison here. Still, 82.8% strict-match on 8-shot chain-of-thought math reasoning is a strong result for a quantized 9B model.</p>



<p>The gap between strict-match and flexible-extract is also interesting. In theory, flexible extraction should help by pulling the numeric answer from a less structured response. In our case, strict-match was actually slightly higher. That suggests the model generally formats its final answers well, while the flexible extractor occasionally misparsed responses shaped by thinking-mode output.</p>



<h3 class="wp-block-heading">What the Responses Look Like</h3>



<p>Here are two examples from the GSM8K run. These show only the final scored output. The proxy stripped the model’s internal thinking content before passing the response to lm-eval, but the full raw generations were much longer.</p>



<p><strong>Correct answer — straightforward arithmetic</strong></p>



<blockquote class="wp-block-quote is-layout-flow wp-block-quote-is-layout-flow">
<p><strong>Question:</strong>&nbsp;Janet&#8217;s ducks lay 16 eggs per day. She eats three for breakfast every morning and bakes muffins for her friends every day with four. She sells the remainder at the farmers&#8217; market daily for $2 per fresh duck egg. How much in dollars does she make every day at the farmers&#8217; market?</p>



<p><strong>Model response:</strong>&nbsp;Janet&#8217;s ducks lay 16 eggs per day. She eats 3 for breakfast and uses 4 for muffins, so she uses 7 eggs in total. She has 9 eggs left to sell. Each egg sells for $2. So she makes 18 dollars. The answer is 18.</p>



<p><strong>Correct answer:</strong>&nbsp;18</p>
</blockquote>



<p>This is representative of most GSM8K successes: clear reasoning, correct arithmetic, correct formatting.</p>



<p><strong>Incorrect answer — semantic off-by-one error</strong></p>



<blockquote class="wp-block-quote is-layout-flow wp-block-quote-is-layout-flow">
<p><strong>Question:</strong>&nbsp;Carlos is planting a lemon tree. The tree will cost $90 to plant. Each year it will grow 7 lemons, which he can sell for $1.5 each. It costs $3 a year to water and feed the tree. How many years will it take before he starts earning money on the lemon tree?</p>



<p><strong>Model response:</strong>&nbsp;Carlos spent $90 to plant the tree. Each year he earns 7 × 1.5 = $10.5 and spends $3 on maintenance, so his net profit per year is $7.5. To recover the initial cost, it takes 90 / 7.5 = 12 years. The answer is 12.</p>



<p><strong><strong>Correct answer</strong>:</strong>&nbsp;13 </p>
</blockquote>



<p>The arithmetic is correct, but the semantic interpretation is not. After 12 years, Carlos has only broken even. Year 13 is when he starts making money. This kind of failure is caused by correct math but incorrect interpretation of the question which appears quite often among GSM8K failed tasks.</p>



<h3 class="wp-block-heading">Throughput and Latency</h3>



<p>We logged every request through the proxy during the benchmark run across all 1,860 requests:</p>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>Metric</th><th>Value</th></tr></thead><tbody><tr><td>Median tokens/sec (completion)</td><td>93.1</td></tr><tr><td>Mean tokens/sec</td><td>84.7</td></tr><tr><td>P25 / P75 tokens/sec</td><td>54.1 / 117.9</td></tr><tr><td>Median request duration</td><td>28.5 sec</td></tr><tr><td>Mean request duration</td><td>35.2 sec</td></tr><tr><td>P95 request duration</td><td>84.6 sec</td></tr><tr><td>Mean completion tokens per request</td><td>2,725</td></tr><tr><td>Mean prompt tokens per request</td><td>630</td></tr><tr><td>Total completion tokens generated</td><td>5,067,826</td></tr><tr><td>Total prompt tokens processed</td><td>1,171,527</td></tr><tr><td>Total wall-clock time</td><td>~6.2 hours</td></tr></tbody></table></figure>



<p>A median throughput of 93.1 tokens/sec is a solid result for a Q4_K_M model running through Ollama on a single GPU. The spread between P25 and P75 reflects the mix of tasks: short completions finish quickly, while chain-of-thought prompts spend much longer generating reasoning before producing a final answer.</p>



<p>The mean completion length of 2,725 tokens per request is high relative to the visible answer. That is the cost of thinking mode. In per token pricing schemas it matters a lot more then on SaladCloud: thinking mode can easily double or triple token consumption.</p>



<h3 class="wp-block-heading">Cost: SaladCloud vs. Mac Mini</h3>



<p>We initially ran this benchmark on a single RTX 5090 node at $0.25/hour on SaladCloud’s lowest tier. For Qwen 3.5 9B Q4_K_M model that was more GPU than the model actually required. We then reran the same model on an RTX 3090 (24GB, $0.09/hour) to see how a lower-cost card would perform.</p>



<p>The 3090 was slower &#8211; median throughput dropped from 93.1 to 70.7 tokens/sec but the lower hourly price still made it better value for this model size.</p>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th></th><th>RTX 5090</th><th>RTX 3090</th></tr></thead><tbody><tr><td>SaladCloud hourly rate</td><td>$0.25</td><td>$0.09</td></tr><tr><td>Daily cost (24/7)</td><td>$6.00</td><td>$2.16</td></tr><tr><td>Median tokens/sec</td><td>93.1</td><td>70.7</td></tr><tr><td>Completion tokens/hour</td><td>~820K</td><td>~583K</td></tr><tr><td>Requests/hour (sustained)</td><td>~301</td><td>~230</td></tr><tr><td>Cost per 1M completion tokens</td><td>$0.30</td><td>$0.15</td></tr><tr><td>Cost per request (avg)</td><td>$0.0008</td><td>$0.0004</td></tr></tbody></table></figure>



<p>Mac Minis range from $599 for the base 16GB model to $1,199 or more for a 32GB configuration. Many developers buying one specifically for local inference lean toward the higher-memory option, but even the base model can run smaller Qwen variants. Here is what the same money buys on SaladCloud:</p>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>Mac Mini config</th><th>Cost</th><th>SaladCloud GPU</th><th>Days of 24/7</th><th>Completion tokens</th><th>Requests</th></tr></thead><tbody><tr><td>16GB (base)</td><td>$599</td><td>RTX 3090</td><td><strong>277 days (9.2 months)</strong></td><td><strong>3.9 billion</strong></td><td><strong>1.5 million</strong></td></tr><tr><td>16GB (base)</td><td>$599</td><td>RTX 5090</td><td>100 days (3.3 months)</td><td>2.0 billion</td><td>0.7 million</td></tr><tr><td>32GB</td><td>$1,199</td><td>RTX 3090</td><td><strong>555 days (18.5 months)</strong></td><td><strong>7.8 billion</strong></td><td><strong>3.1 million</strong></td></tr><tr><td>32GB</td><td>$1,199</td><td>RTX 5090</td><td>200 days (6.7 months)</td><td>3.9 billion</td><td>1.4 million</td></tr></tbody></table></figure>



<p>The standout number is simple: for the price of a 32GB Mac Mini, you get more than&nbsp;<strong>18 months</strong>&nbsp;of continuous 24/7 inference on an RTX 3090, generating 7.8 billion tokens across 3.1 million requests. Even the base $599 Mac Mini buys more than 9 months of cloud inference on the same GPU. And that comparison assumes the Mac Mini is actually being used for local inference around the clock for months at a time, which is unlikely for most people.</p>



<p>At $0.15 per million completion tokens on an RTX 3090, the cost also compares favorably with hosted APIs. You still give up some advantages of local hardware &#8211; near-zero marginal cost after purchase, full local privacy, and no recurring bill but you gain no upfront capital expense, elastic scaling, no maintenance, and the freedom to change GPUs or models without buying new hardware.</p>



<p>For someone running agents continuously for years, local hardware will eventually win on raw cost. For a team validating a use case, handling variable demand, or avoiding a hardware commitment, 9 to 18 months of cloud inference for the same money is a compelling trade.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Results: Qwen 3.5 4B on Ollama</h2>



<p>The 4B is arguably the most interesting model in the Qwen 3.5 small series for agentic workloads. It fits on a wide range of modern GPUs but what makes it stand out is its capability relative to size: the official benchmarks show the 4B matching the performance of last-generation Qwen3-VL-30B-A3B (a model with nearly 8x the parameters) on visual agent tasks like ScreenSpot Pro, and outperforming some mainstream models on tool invocation benchmarks like TIRE-Bench. On IFEval, the full-precision 4B scores 89.8% which is higher than GPT-OSS-120B (88.9%), a model 30x its size. This positions the 4B as a serious candidate for the &#8220;workhorse&#8221; tier of a multi-model agent stack: handling routine summarization, classification, data extraction, context management, and simple tool calls at minimal compute cost.</p>



<p>We ran this model on SaladCloud with Ollama&#8217;s default Q4_K_M quantization across two GPU tiers &#8211; the RTX 4090 ($0.16/hour) and RTX 3090 ($0.09/hour). The evaluation included GSM8K-CoT, IFEval.</p>



<h3 class="wp-block-heading">Accuracy</h3>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>Benchmark</th><th>Metric</th><th>Our Score (Q4_K_M, 100 samples)</th><th>Official Score (BF16)</th></tr></thead><tbody><tr><td>GSM8K-CoT</td><td>exact_match (strict)</td><td>73.0% </td><td>—</td></tr><tr><td>GSM8K-CoT</td><td>exact_match (flexible)</td><td>73.0% </td><td>—</td></tr><tr><td>IFEval</td><td>inst_level_strict_acc</td><td>84.0%</td><td>89.8%</td></tr><tr><td>IFEval</td><td>inst_level_loose_acc</td><td>85.3%</td><td>—</td></tr><tr><td>IFEval</td><td>prompt_level_strict_acc</td><td>77.0% </td><td>—</td></tr><tr><td>IFEval</td><td>prompt_level_loose_acc</td><td>78.0% </td><td>—</td></tr></tbody></table></figure>



<p>The IFEval result of 84.0% instruction-level strict accuracy is 5.8 points below the official BF16 score of 89.8%. This is a larger quantization penalty than the 9B model experienced (2.3 points), which is consistent with what the community reported: smaller models are more sensitive to 4-bit quantization. That said, 84% is still a strong result and is higher than many full-precision models from the previous generation.</p>



<p>The GSM8K-CoT score of 73.0% is lower than the 9B&#8217;s 82.8%, as expected. With 100 samples we tested on, the confidence interval is wider (±4.5%), so the true score likely falls in the 68–78% range. For the tasks the 4B is best suited for such as classification, summarization, structured extraction, tool calling GSM8K math reasoning is not the primary use case.</p>



<p>We also tested the HumanEval but it scored 0.0%, which  is a tooling issue, not a model failure. The model generated correct-looking code in its responses, but the thinking mode output format confused lm-eval&#8217;s code extraction logic. The model&#8217;s response to HumanEval prompts included reasoning about the approach before writing code, and the extraction harness could not reliably separate the solution from the reasoning. </p>



<h3 class="wp-block-heading">Throughput</h3>



<p>The proxy logged 2,730 total requests across the RTX 4090 benchmark run and 1,023 on the RTX 3090. </p>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>Metric</th><th>RTX 4090</th><th>RTX 3090</th></tr></thead><tbody><tr><td>Median tokens/sec</td><td>128.3</td><td>76.4</td></tr><tr><td>Mean tokens/sec</td><td>122.7</td><td>73.0</td></tr><tr><td>P25 / P75 tokens/sec</td><td>113.4 / 133.1</td><td>63.0 / 89.6</td></tr><tr><td>Mean completion tokens/request</td><td>2,654</td><td>2,930</td></tr></tbody></table></figure>



<p>The 4B runs at 128.3 tokens/sec median on the RTX 4090 is significantly faster than the 9B on the RTX 5090 (93.1 tok/s), which is expected given the 4B&#8217;s smaller size. The RTX 3090 delivers 76.4 tok/s on the same model, roughly 60% of the 4090&#8217;s throughput, reflecting the memory bandwidth difference between the two cards.</p>



<h3 class="wp-block-heading">Cost</h3>



<p>The 4B model is small enough to run on a wide range of GPUs. At Q4_K_M quantization fits on anything from an RTX 3060 (12GB) upward. The cost picture improves significantly as you move to cheaper hardware:</p>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>GPU</th><th>SaladCloud $/hr</th><th>Median tok/s</th><th>Cost per 1M completion tokens</th><th>$599 Mac Mini equivalent</th><th>$1,199 Mac Mini equivalent</th></tr></thead><tbody><tr><td>RTX 4090</td><td>$0.16</td><td>128.3</td><td>$0.35</td><td>156 days (5.2 months)</td><td>312 days (10.4 months)</td></tr><tr><td>RTX 3090</td><td>$0.09</td><td>76.4</td><td>$0.33</td><td>277 days (9.2 months)</td><td>555 days (18.5 months)</td></tr></tbody></table></figure>



<p>On the RTX 4090 at $0.16/hour, the cost comes to $0.35 per million completion tokens. On the RTX 3090 at $0.09/hour, it&#8217;s $0.33 nearly the same per-token cost because the lower hourly rate is offset by lower throughput. The price of a base Mac Mini buys over&nbsp;<strong>9 months</strong>&nbsp;of continuous operation on the 3090, while the 32GB Mac Mini equivalent stretches to over&nbsp;<strong>18 months</strong>. For a model designed to handle the high-volume, low-complexity tasks in an agent stack, these economics are difficult to beat.</p>



<h3 class="wp-block-heading">A Note on Qwen 3.5 2B</h3>



<p>We evaluated the 2B model briefly but decided not to run full benchmarks on SaladCloud. The 2B is designed primarily for edge deployment such as phones, tablets, IoT devices where the constraints are power consumption and memory, not cloud GPU cost. At Q4 quantization, it runs on hardware far below what SaladCloud&#8217;s GPU nodes provide, and its benchmark scores (scoring 16 on the Artificial Analysis Intelligence Index, on par with the 7B-class Falcon-H1R) reflect the tradeoffs of that size class. For cloud-based agentic workloads, the 4B is the better starting point. </p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Results: Qwen 3.5 4B on vLLM (Full Precision)</h2>



<p>The Ollama results above reflect a Q4_K_M quantized model — useful for understanding what most developers will deploy in practice, but not a fair comparison to the official benchmarks. To validate how much accuracy the quantization costs, we also ran the Qwen 3.5 4B Base model on vLLM at FP16 precision. This is the same weight format used for the official benchmark numbers on the model card.</p>



<p>We ran evaluations on GSM8K-CoT and IFEval to compare directly against the Ollama results.</p>



<h3 class="wp-block-heading">Accuracy</h3>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>Benchmark</th><th>Metric</th><th>vLLM FP16</th><th>Ollama Q4_K_M</th></tr></thead><tbody><tr><td>GSM8K-CoT</td><td>exact_match (flexible)</td><td><strong>94.0%</strong></td><td>73.0%</td></tr><tr><td>IFEval</td><td>inst_level_strict_acc</td><td>88.6%</td><td>84.0%</td></tr><tr><td>IFEval</td><td>prompt_level_strict_acc</td><td>79.0%</td><td>77.0%</td></tr></tbody></table></figure>



<p>The GSM8K results tell an important story. The flexible-extract score of&nbsp;<strong>94.0%</strong>&nbsp;is excellent &#8211; this confirms that the full-precision model gets the right answer on nearly all math problems. We got 42% strict-match score which is not a reasoning failure, but  a formatting issue. The Base model doesn&#8217;t consistently format its answers the way the strict matcher expects. For example, on a question about egg sales, the model answered &#8220;$18&#8221; instead of &#8220;18&#8221; which is a correct answer, but counts as a wrong format for the scoring harness. The flexible extractor handles this gracefully.</p>



<p>This 94% flexible-extract score is consistent with what we&#8217;d expect from the official benchmarks. It validates that the Qwen 3.5 4B at full precision performs at or near the level Alibaba reports and the gap we saw in the Ollama runs  is primarily a quantization effect, not a model limitation.</p>



<h3 class="wp-block-heading">Throughput</h3>



<p>The throughput number between Ollama and vLLM are almost the same, even though vLLM was serving a more accurate version of the model</p>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>Metric</th><th>vLLM (FP16)</th><th>Ollama (Q4_K_M)</th></tr></thead><tbody><tr><td>Median tokens/sec (substantial)</td><td>129.8</td><td>128.3</td></tr><tr><td>Mean tokens/sec (substantial)</td><td>125.4</td><td>122.7</td></tr></tbody></table></figure>



<p>For production deployments where throughput and quality matters, vLLM is the better choice. For quick evaluation and development, Ollama&#8217;s simplicity is hard to beat.</p>



<h2 class="wp-block-heading">Qwen 3.5 27B and 35B on SaladCloud</h2>



<p>In addition to the small models covered above, we successfully deployed the Qwen 3.5 27B and Qwen 3.5 35B-A3B  on SaladCloud. Both run as quantized variants &#8211; the 27B fits on a 24GB GPU at 4-bit quantization, and the 35B-A3B&#8217;s architecture activates only 3B parameters per forward pass, making it surprisingly efficient despite its total parameter count.</p>



<p>Early testing shows both models perform well on coding tasks, with the 27B in particular gaining attention in the community as a strong base for distilled reasoning models. Running full benchmarks on these larger models requires updating our benchmarking tooling to handle their longer generation patterns and higher memory requirements which we are currently working on. We&#8217;ll be publishing quick-deploy recipes for both models on the SaladCloud portal shortly, along with latency and pricing data.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading">Conclusion</h2>



<p>We set out to answer a simple question: can you run Qwen 3.5 small models on SaladCloud as a practical alternative to buying dedicated hardware or using expensive LLM API&#8217;s? The data says yes and the economics are more favorable than we initially expected.</p>



<p>Here&#8217;s a summary of what we found across all configurations tested:</p>



<figure class="wp-block-table"><table class="has-fixed-layout"><thead><tr><th>Model</th><th>Server</th><th>GPU</th><th>IFEval strict</th><th>Median tok/s</th><th>$/1M tokens</th><th>$599 Mac Mini buys</th><th>$1,199 Mac Mini buys</th></tr></thead><tbody><tr><td>Qwen 3.5 9B (Q4_K_M)</td><td>Ollama</td><td>RTX 5090 ($0.25/hr)</td><td>89.2%</td><td>93.1</td><td>$0.30</td><td>3.3 months</td><td>6.7 months</td></tr><tr><td>Qwen 3.5 9B (Q4_K_M)</td><td>Ollama</td><td>RTX 3090 ($0.09/hr)</td><td>same weights</td><td>70.7</td><td>$0.15</td><td>9.2 months</td><td>18.5 months</td></tr><tr><td>Qwen 3.5 4B (Q4_K_M)</td><td>Ollama</td><td>RTX 4090 ($0.16/hr)</td><td>84.0%</td><td>128.3</td><td>$0.35</td><td>5.2 months</td><td>10.4 months</td></tr><tr><td>Qwen 3.5 4B (Q4_K_M)</td><td>Ollama</td><td>RTX 3090 ($0.09/hr)</td><td>same weights</td><td>76.4</td><td>$0.33</td><td>9.2 months</td><td>18.5 months</td></tr><tr><td>Qwen 3.5 4B (FP16 Base)</td><td>vLLM</td><td>RTX 4090 ($0.16/hr)</td><td>88.6%</td><td>129.8</td><td>$0.34</td><td>5.2 months</td><td>0</td></tr></tbody></table></figure>



<p></p>



<p>For the cost of a base Mac Mini ($599), you get&nbsp;<strong>5 to 10 months</strong>&nbsp;of continuous cloud inference depending on model and GPU choice. For the price of a 32GB Mac Mini ($1,199), you get&nbsp;<strong>10 to 20 months</strong>. During that time, you generate billions of tokens across millions of requests &#8211; more than enough to validate a use case, run an agent stack through its early lifecycle, or handle production workloads for a team that doesn&#8217;t need hardware running 24/7 indefinitely.</p>



<p>It&#8217;s also worth noting that SaladCloud&#8217;s pricing is based on compute hours, not tokens. You pay for the GPU time, and however many tokens you can push through it are yours. To put that in perspective: at $0.15 per million tokens on an RTX 3090, you&#8217;re paying over&nbsp;<strong>150x less</strong>&nbsp;than Claude Opus ($25/M output tokens) for every token generated. Of course, a 9B model isn&#8217;t replacing Opus for complex reasoning but for the general routine or coding tasks it is good enough. That means the per-token costs in the table above also aren&#8217;t fixed. If a faster inference server comes along, or a future model architecture generates tokens more efficiently on the same hardware, your effective cost per token drops automatically without any pricing change on SaladCloud&#8217;s side. You&#8217;re renting the GPU, not buying tokens.</p>



<p>A few broader takeaways from the benchmarking process:</p>



<p><strong>Quantization matters  but so does knowing what you&#8217;re running.</strong>&nbsp;The same model weights will always produce the same accuracy, regardless of inference server or GPU. If you want to reproduce the scores you see on an official model card, you need to make sure you&#8217;re actually running the same weights. This can be trickier than it sounds- if you pull a model from a non-official source or use a tool that applies quantization by default, you might be running a different set of weights without realizing it. Do your research before drawing conclusions from benchmark comparisons. That said, as our results showed, quantized models aren&#8217;t dramatically worse overall &#8211; the 9B lost 2.3 points on IFEval at Q4_K_M, and even the more sensitive 4B remained strong and they&#8217;re significantly easier to run on consumer hardware. Only testing on your specific tasks can tell you whether full precision is worth the extra overhead.</p>



<p><strong>Thinking mode is a hidden cost multiplier.</strong>&nbsp;Qwen 3.5 models default to thinking mode, which generates internal reasoning before the visible answer. Our 9B benchmark averaged 2,725 completion tokens per request &#8211; the majority of which was thinking content stripped by the proxy before scoring. In reality this means your token budget is 2–3x what the visible output suggests and you either need to count that or use a pricing model that is not token based.</p>



<p><strong>These models are a new reality.</strong>&nbsp;The Qwen 3.5 9B at 89.2% IFEval (quantized) and the 4B at 94% GSM8K (full precision) aren&#8217;t demo-quality curiosities. They are production-capable models that can handle the routine inference tasks that consume the bulk of an agentic workflow&#8217;s token budget at a fraction of the cost of frontier APIs. Whether you run them on SaladCloud, a Mac Mini, or any other infrastructure, the underlying capability is there.</p>



<p>The recipes for all models tested in this post are available on the&nbsp;<a href="https://portal.salad.com/">SaladCloud portal</a>. Recipes for the Qwen 3.5 27B and 35B-A3B will be published shortly.</p>



<p></p>
<p>The post <a href="https://blog.salad.com/qwen-3-5-small-models-on-saladcloud-benchmarks-cost-and-why-you-dont-need-a-mac-mini/">Qwen 3.5 Small Models on SaladCloud: Benchmarks, Cost, and Why You Don’t Need a Mac Mini</a> appeared first on <a href="https://blog.salad.com">SaladCloud Blog</a>.</p>
