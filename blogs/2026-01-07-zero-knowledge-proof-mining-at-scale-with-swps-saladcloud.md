---
title: "Zero-Knowledge Proof Mining at Scale with SWPS & SaladCloud"
url: "https://blog.salad.com/zero-knowledge-proof-mining-at-scale-with-swps-saladcloud/"
date: "Wed, 07 Jan 2026 14:00:24 +0000"
author: "Maksim Gorkii"
feed_url: "https://blog.salad.com/feed/"
---
<p></p>



<h3 class="wp-block-heading">The Compute Challenge in Web3</h3>



<p>Zero-knowledge proofs are becoming one of the most demanding workloads in Web3 infrastructure. As proof systems mature and adoption increases, networks are pushing more computation off-chain while still requiring cryptographic guarantees on correctness, privacy, and verifiability.</p>



<p>This shift has made GPU compute a foundational dependency. Proof generation now competes directly with AI, simulation, and analytics workloads for the same constrained resources: parallel compute, memory bandwidth, and power efficiency. As a result, infrastructure decisions increasingly shape what kinds of proof-based systems are viable at scale.</p>



<p>The cryptographic models are inseparable from the compute infrastructure that executes them. Proof systems built to minimize trust and inefficiency risk reintroducing those same constraints if they rely on rigid, capital-intensive compute models.</p>



<p>This is the context in which Nockchain, NockPool, and SaladCloud operate.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">SWPS: Building the Nockchain Ecosystem</h3>



<p>When Nockchain launched its mainnet on May 21, 2025, it introduced a novel approach to blockchain infrastructure: Zero-Knowledge Proof-of-Work (ZKPoW). Unlike Bitcoin&#8217;s hash-based mining, Nockchain miners must compute and submit zero-knowledge proofs of deterministic computation tasks making mining power correspond to performing provably useful computation rather than pure energy expenditure.</p>



<p>But launching a new Layer 1 blockchain requires more than just protocol innovation. It needs infrastructure: block explorers, wallets, mining pools, and developer tools. This is where Southwestern Pool Supply Co. (SWPS) stepped in.</p>



<p>Based in Austin, Texas, SWPS emerged as one of Nockchain&#8217;s key ecosystem builders, developing three critical pieces of infrastructure:</p>



<p><strong>NockBlocks</strong>&nbsp;&#8211; The network&#8217;s first comprehensive block explorer and licensable API, providing real-time visibility into block production, transaction flow, address analytics, and network statistics. It translates Nockchain&#8217;s Nock combinator calculus messages into human-readable format.</p>



<p><strong>Fletch</strong>&nbsp;&#8211; A browser extension wallet solution enabling users to create, import, and manage Nockchain keys locally with an accessible interface. Currently in closed Beta.</p>



<p><strong>NockPool</strong>&nbsp;&#8211; The mining pool that became central to democratizing Nockchain participation.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">The Mining Pool Problem</h3>



<p>Nockchain&#8217;s fair no pre-mine, no private allocations launch design meant every NOCK token would enter circulation through mining. This created an opportunity for broad participation but also introduced a challenge of computational difficulty of solo mining.</p>



<p>Solo mining a proof-of-work blockchain is like playing a lottery &#8211; technically possible, practically improbable. As network difficulty adjusts upward, individual miners might wait months or years to find a valid block, making the effort economically unviable for most participants.</p>



<p>Mining pools solve this by combining computational power. When the pool finds a block, rewards are distributed proportionally among contributors. As a result participants get smaller, but more predictable payouts instead of rare jackpots.</p>



<p>NockPool launched in August 2025, positioning itself around transparent approach, a flat 4% pool fee, and Pay-Per-Last-N-Shares (PPLNS) payouts &#8211; a model that rewards consistent participation over short-term pool hopping.</p>



<p>But launching a mining pool is only half the solution. The other half is compute infrastructure.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">The Infrastructure Economics Problem</h3>



<p>Zero-knowledge proof generation is computationally intensive. The workload involves:</p>



<ul class="wp-block-list">
<li><strong>Large polynomial transforms</strong> (FFT/NTT operations)</li>



<li><strong>Elliptic-curve multi-scalar multiplications</strong> (MSMs)</li>



<li><strong>Parallel processing</strong> across thousands of threads</li>



<li><strong>High memory bandwidth</strong> requirements</li>
</ul>



<p>These characteristics make GPUs ideal for ZK proving. Modern graphics cards excel at exactly this type of parallel computation. But accessing GPU infrastructure at scale presents its own challenges.</p>



<p>Traditional cloud providers charge premium rates for GPU instances, often dollars per hour for high-end cards. For mining operations these costs can quickly exceed potential earnings, making participation economically unfeasible for smaller miners.</p>



<p>The challenge becomes circular. You need accessible mining infrastructure to decentralize participation, but providing that infrastructure at costs miners can afford requires a fundamentally different compute model.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">Why SaladCloud fits ZK Proving</h3>



<p>SaladCloud operates on a different premise than traditional cloud providers. Rather than building massive data centers filled with enterprise GPUs, the platform leverages over 60,000 distributed GPUs from consumer hardware globally.</p>



<p>The model activates latent computing resources, such as personal computers and servers that sit idle most of the day. GPU owners share their unused capacity in exchange for rewards, while customers access affordable compute at affordable price.</p>



<p>For zero-knowledge proof generation specifically, this approach offers huge advantage.</p>



<h4 class="wp-block-heading">Cost Efficiency</h4>



<p>Current SaladCloud pricing starts from $0.02 per hour for basic instances, with high-end RTX 4090s available around $0.16 per hour. You also do not pay any additional cost for RAM or CPU, which are still required for the process, but do not pay such a significant role as GPU. Compared to traditional providers, this represents significant cost savings for ZKP workloads, a difference that fundamentally changes the economics of mining participation.</p>



<p>A recent analysis show that running the same jobs on SaladCloud costs up to 90% less than prices reported on other providers, depending on priority level and configuration. For Nockchain miners proof-of-work economics depend on a simple calculation “does the value of mined tokens exceed the cost of producing them?” In that scenario savings directly impact viability.</p>



<h4 class="wp-block-heading">Parallel Scaling</h4>



<p>ZK proof generation parallelizes naturally. Unlike sequential tasks, each proof calculation can happen independently. SaladCloud&#8217;s distributed architecture means miners can scale from testing with a single GPU to running dozens or hundreds across the network, paying only for active compute time.</p>



<p>When a single node goes offline because the underlying hardware owner needs their GPU SaladCloud&#8217;s orchestration automatically relocates containers to another available machine.</p>



<h4 class="wp-block-heading">GPU Diversity</h4>



<p>Nockchain&#8217;s ZKPoW algorithm runs efficiently on various GPU classes. SaladCloud&#8217;s diverse hardware pool means miners can match their budget and performance requirements to available resources, rather than being locked into expensive enterprise GPU’s.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">The NockPool GPU Prover Recipe</h3>



<p>SWPS recognized that lowering the barrier to entry isn’t just about pool economics it is also about deployment simplicity. Most potential miners aren&#8217;t infrastructure engineers. They need a path from &#8220;interested in mining&#8221; to &#8220;actively generating proofs&#8221; that doesn&#8217;t require deep technical knowledge.</p>



<p>Working together SaladCloud and SWPS created a pre-configured NockPool GPU Prover recipe -a containerized deployment that eliminates setup complexity entirely.</p>



<h4 class="wp-block-heading">How It Works</h4>



<p>The recipe follows SaladCloud&#8217;s standard container deployment pattern:</p>



<ol class="wp-block-list">
<li><strong>Register at <a href="http://nockpool.com/">nockpool.com</a> </strong>and obtain a unique account token.</li>



<li><strong>Navigate to the SaladCloud portal</strong> and select &#8220;Deploy Container Group&#8221;</li>



<li><strong>Choose the NockPool GPU Prover recipe</strong> from available templates</li>



<li><strong>Provide configuration:</strong>
<ul class="wp-block-list">
<li>Unique container group name</li>



<li>NockPool account token (generated from <a href="http://nockpool.com/">nockpool.com</a>)</li>
</ul>
</li>



<li>Update hardware configuration if desired and hit “deploy”</li>
</ol>



<p>Each container instance authenticates using the provided token and begins submitting proof shares to NockPool. The distributed nature of SaladCloud means scaling happens horizontally-add more replicas to increase proof generation capacity.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">Results of SaladCloud + SWPS collaboration</h3>



<p>The NockPool and SaladCloud integration has transformed what&#8217;s economically viable for Nockchain participants, turning fair-launch principles into practical reality.</p>



<h4 class="wp-block-heading">Economic Impact</h4>



<p>The cost savings are substantial. A miner running a single RTX 4090 on SaladCloud at $0.16/hour costs roughly $115/month for continuous operation. The same GPU on traditional cloud providers might cost $300-400/month, a difference that determines whether mining remains profitable as NOCK price and network difficulty fluctuate. At scale savings can get to thousands of dollars.</p>



<h4 class="wp-block-heading">Ecosystem Growth</h4>



<p>Beyond raw numbers, the SWPS + SaladCloud collaboration demonstrates how accessible infrastructure enables ecosystem development. By removing compute cost barriers, the partnership allows:</p>



<ul class="wp-block-list">
<li><strong>Geographic distribution:</strong> Miners worldwide can participate without local hardware investments</li>



<li><strong>Scaling flexibility:</strong> Miners can adjust capacity based on market conditions without commitments</li>



<li><strong>Entry accessibility:</strong> New participants can start mining within minutes rather than days<br /></li>
</ul>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">Why This Matters for Web3</h3>



<p>The SWPS case study illustrates a broader principle about blockchain infrastructure: decentralization isn&#8217;t just about protocol design-it&#8217;s about the entire stack, including the compute layer that makes participation viable.</p>



<p>Nockchain&#8217;s technical innovation (ZKPoW) creates the possibility of useful computational work replacing wasteful hashing. But that possibility only becomes reality if miners can actually afford to generate those proofs at scale. SaladCloud&#8217;s distributed compute model makes this economically feasible.</p>



<p>As zero-knowledge proofs continue expanding across Web3 from scaling solutions like rollups to privacy-preserving applications-the compute infrastructure supporting proof generation will shape what&#8217;s possible. Systems that require expensive, centralized data center resources naturally tend toward consolidation. Systems that can leverage distributed, affordable compute can remain meaningfully decentralized.</p>



<p></p>
<p>The post <a href="https://blog.salad.com/zero-knowledge-proof-mining-at-scale-with-swps-saladcloud/">Zero-Knowledge Proof Mining at Scale with SWPS &amp; SaladCloud</a> appeared first on <a href="https://blog.salad.com">SaladCloud Blog</a>.</p>
