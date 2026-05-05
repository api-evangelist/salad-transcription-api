---
title: "Nockchain ZK Proving on SaladCloud"
url: "https://blog.salad.com/nock-on-saladcloud/"
date: "Sun, 21 Dec 2025 17:13:43 +0000"
author: "Maksim Gorkii"
feed_url: "https://blog.salad.com/feed/"
---
<h2 class="wp-block-heading">Mining Digital Gold: Inside Nockchain&#8217;s Zero-Knowledge Revolution</h2>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">When Tools Reshape Reality</h3>



<p>“Rethink Technology at the Civilization Level”</p>



<p>Friedrich Nietzsche noticed something peculiar when he switched from pen to typewriter in the 1880s. His prose transformed. It got tightened, sharpened, became more aphoristic. &#8220;Our writing tools are also working on our thoughts,&#8221; he observed. The typewriter didn&#8217;t just speed up his work, it fundamentally altered how he thought.</p>



<p>Today, we&#8217;re experiencing a similar transformation, even though we do not think about it too much. The digital infrastructure we navigate daily, such as banking systems, social networks, identity platforms &#8211; all shape not just our behavior but our conception of what&#8217;s possible. We learn &#8220;normal&#8221; from our environment: what counts as legitimate, what feels real, what seems unchangeable.</p>



<p>Most of these systems &#8220;just work,&#8221; and that seamlessness makes them invisible. We stop asking how they function. We forget there&#8217;s always an underlying mechanism deciding what we can do, own, or become.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">The Permission Problem</h3>



<p>Consider a simple transaction. When you transfer money, you&#8217;re not moving physical objects, you&#8217;re asking a bank to update its ledger. The bank verifies your identity, checks your balance, and records the change. This system works, but it requires trust in the institution maintaining the records.</p>



<p>What if trust wasn&#8217;t a prerequisite? Not because people are better or institutions kinder, but because the system itself makes honesty the default outcome?</p>



<p>This is the philosophical core of decentralization: agreement without permission, continuity without a custodian, and security without a single point of failure. A decentralized system doesn&#8217;t ask you to believe. It gives you a method to check.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">Enter Nockchain</h3>



<p>Launched on May 21, 2025, <a href="https://www.nockchain.org">Nockchain</a> aims to extend the foundational principles of Bitcoin into the era of verifiable computation and scalable smart contracts. But unlike Bitcoin&#8217;s simple hash-based mining, Nockchain introduces something different: Zero-Knowledge Proof-of-Work (ZKPoW).</p>



<p>Traditional Bitcoin mining is computationally expensive but conceptually simple &#8211; miners compete to find random values that produce specific hash outputs. In Nockchain&#8217;s ZKPoW, miners must compute and submit a zero-knowledge proof of a deterministic computation task, with the hash of that proof serving as the block&#8217;s nonce. This means mining power corresponds to performing provably useful computation rather than pure energy expenditure.</p>



<p>Think of it as the difference between digging holes and filling them versus building infrastructure. Both require work, but only one produces lasting value.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">The Fair Launch Philosophy</h3>



<p>In an industry notorious for insider allocations and venture capital windfalls, Nockchain took a different path. The project launched with no pre-mine, excluding early token distribution to founders or investors. Every NOCK token entered circulation through mining, earned by those who showed up, ran the software, and competed.</p>



<p>This choice carries social weight. It distributes power through an open mining market from day one, pushing toward broader participation rather than concentrated ownership. The trade-off? A more demanding bootstrapping phase with no guaranteed resources.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">The Technical Foundation</h3>



<p>Nockchain’s core technical highlights are its&nbsp;<a href="https://docs.nockchain.org/architecture/what-is-nockchain/zero-knowledge-proofs">Zero-Knowledge Proof-of-Work </a>(ZKPoW)&nbsp;mechanism and its&nbsp;<a href="https://docs.nockchain.org/reference-runtime/nockvm">Zero-Knowledge Virtual Machine (ZKVM)</a>&nbsp;strategy.</p>



<figure class="wp-block-image aligncenter size-full"><img alt="" class="wp-image-1417" height="363" src="https://blog.salad.com/wp-content/uploads/2025/12/image-3.png" width="471" /></figure>



<h4 class="wp-block-heading">A New Kind of Virtual Machine</h4>



<p>At Nockchain&#8217;s core sits NockVM, a Zero-Knowledge Virtual Machine designed for efficient proof generation. Traditional blockchain VMs execute transactions; NockVM does this while making every computation verifiable through zero-knowledge proofs.</p>



<p>The practical implication: you can verify that a calculation was performed correctly without re-executing it or even knowing what the inputs were. This enables privacy-preserving applications, scalable computation, and trust-minimized bridges between different systems.</p>



<h4 class="wp-block-heading">Zero-Knowledge Proof-of-Work (ZKPoW)</h4>



<p>In traditional Bitcoin mining, miners compete to record transactions by solving hash puzzles. Nockchain’s ZKPoW reframes that competition: miners race to solve a&nbsp;proof puzzle&nbsp;for a candidate block that bundles transactions, then propagate the block so the network can validate it and move forward. The miner who finds the valid solution first wins the block reward.</p>



<p>If you want an intuition for “zero-knowledge proof,” it’s like proving you know a secret without revealing the secret itself. In Nockchain, miners generate and verify ZK proofs to demonstrate computational work in a way the network can check efficiently.</p>



<h4 class="wp-block-heading">Layer 1 base chain and the NockApp framework</h4>



<p>Nockchain operates as  an independent Layer 1, not an add-on to another chain. It aims to provide a scalable, lightweight proof platform as programmable money’s base infrastructure.</p>



<p>Its runtime supports the <a href="https://docs.nockchain.org/nockapp/what-is-nockapp">NockApp</a> framework, allowing developers to deploy application-specific rollups anchored to the main chain. Each rollup can perform complex off-chain computations and post a succinct validity proof to Nockchain&#8217;s mainnet. This design lets the network evolve beyond simple payments into  a &#8220;verifiable compute layer&#8221; &#8211; infrastructure for privacy-preserving applications and zero-knowledge systems that don&#8217;t compromise on verifiability.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">Tokenomics</h3>



<p><a href="https://docs.nockchain.org/usdnock-asset/what-is-usdnock">NOCK</a> tokenomics are designed around the chain’s main objective: private, scalable, decentralized, verifiable computation secured by proof-of-work incentives. The schedule is structured to reward early participation while leaving room for long-term competition to be driven by software efficiency and real-world proving capacity, not just brute hardware scaling.</p>



<p>The total supply is capped at 2³² NOCK tokens (approximately 4.29 billion), subdivisible into smaller units. The issuance follows a Bitcoin-style halving schedule, creating predictable scarcity over time. The chain targets 10-minute block times with dynamic difficulty adjustments, maintaining network stability as mining power fluctuates.</p>



<p>As of December 2025, NOCK trades around $0.04 on exchanges like SafeTrade. The project is currently on the early-stage territory with corresponding volatility and opportunity.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">Mining NOCK: The Practical Reality</h3>



<h4 class="wp-block-heading">Why Pool Mining Matters</h4>



<p>Mining pools solve this problem by combining resources. Participants share computational power, and when the pool finds a block, rewards are distributed proportionally. The result: smaller, more predictable payouts instead of rare jackpots.</p>



<figure class="wp-block-image size-large"><img alt="" class="wp-image-1413" height="467" src="https://blog.salad.com/wp-content/uploads/2025/12/image-1024x467.png" width="1024" /></figure>



<p>Solo mining a proof-of-work blockchain today resembles playing the lottery &#8211; technically possible, practically improbable. The computational difficulty means individual miners might wait months or years to find a valid block.</p>



<h4 class="wp-block-heading">NockPool: The Gateway</h4>



<p><a href="https://nockpool.com">NockPool</a> is Nockchain&#8217;s community mining pool. The pool hosts hundreds active miners processing over 20 thousand proofs per second, operating on a Pay-Per-Last-N-Shares (<a href="https://nockpool.com/faq">PPLNS</a>) payout model.</p>



<p>PPLNS rewards miners based on their recent contributions rather than instantaneous snapshots. This design discourages &#8220;pool hopping&#8221; &#8211; the practice of jumping between pools chasing short-term profits &#8211; and encourages consistent participation. NockPool uses a sliding window of the most recent 10,000 shares globally. When a block is found, rewards are distributed proportionally based on miners shares within this window.</p>



<p>NockPool charges a flat 4% fee on withdrawals, with transparent telemetry showing real-time proof rates and pool performance. The service requires KYC verification for withdrawals, maintains a 50 NOCK minimum payout threshold, and reserves 20 NOCK for network transaction fees.</p>



<h4 class="wp-block-heading">Getting started: account, wallet, and payout address</h4>



<p>Getting started requires three components:</p>



<p><strong>1. A NockPool Account</strong><br />Create an account at nockpool.com using standard authentication (Google, GitHub, email, etc.) and generate an account token for miner authentication.</p>



<p><strong>2. A Nockchain Wallet</strong><br />The ecosystem recommends Iris Wallet, a browser-based option where you control your private keys locally. Critical security note: NockPool doesn&#8217;t custody your keys, making seed phrase backup your responsibility. Lost keys mean lost funds &#8211; no recovery mechanism exists.</p>



<p><strong>3. Mining Hardware</strong><br />While Nockchain can theoretically run on various systems, GPU mining dominates in practice. The proof-generation workload suits parallel processing, giving modern graphics cards a substantial advantage over CPUs.</p>



<h4 class="wp-block-heading">Withdrawing earnings (payouts)</h4>



<p>Withdrawals are handled as payout requests from NockPool to your configured payout address, with a few constraints:</p>



<ul class="wp-block-list">
<li>Payout requests require completing&nbsp;KYC&nbsp;in NockPool first.</li>
</ul>



<ul class="wp-block-list">
<li>Payouts are processed&nbsp;once per day&nbsp;(based on submissions before&nbsp;12AM GMT).</li>
</ul>



<ul class="wp-block-list">
<li>There’s a&nbsp;minimum withdrawal of 50.00 NOCK, and you need to keep&nbsp;20.00 NOCK available for network fees.</li>
</ul>



<ul class="wp-block-list">
<li>NockPool charges a&nbsp;4% fee&nbsp;&nbsp;when earnings are withdrawn.</li>
</ul>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">The SaladCloud Advantage</h3>



<p>This is where the economics get interesting.</p>



<p>Traditional cloud providers charge premium rates for GPU instance &#8211; dollars per hour for high-end cards. For a mining operation with uncertain returns, these costs can quickly exceed potential earnings.</p>



<p><a href="https://salad.com">SaladCloud</a> operates differently, leveraging over 60,000 distributed GPUs from consumer hardware globally. By aggregating idle computing power from individuals&#8217; personal machines, the platform achieves pricing that traditional data centers can&#8217;t match.</p>



<p><a href="https://salad.com/pricing">Current rates</a> start from $0.02 per hour for basic instances, with high-end RTX 4090s available around $0.16 per hour which is 85-90% savings versus major cloud providers.</p>



<h4 class="wp-block-heading">The Distributed Computing Model</h4>



<p>SaladCloud&#8217;s approach reflects a broader shift in computing infrastructure. Rather than building massive data centers, the platform utilizes idle resources already distributed worldwide. The model benefits both sides: GPU owners earn rewards for sharing unused capacity, while customers access affordable compute without traditional cloud markup.</p>



<p>For Nockchain miners, this matters because proof-of-work economics depend on a simple calculation: does the value of mined tokens exceed the cost of producing them? Lower compute costs directly improve this equation.</p>



<h4 class="wp-block-heading">Deployment: The Fast Path</h4>



<p><a href="https://portal.salad.com">SaladCloud</a> includes a pre-configured <a href="https://docs.salad.com/container-engine/reference/recipes/nockpool-miner">NockPool GPU Prover recipe</a> eliminating setup complexity.</p>



<p>The workflow:</p>



<ol class="wp-block-list">
<li>Register or sign up to portal.salad.com</li>



<li>Create or open existing project and select &#8220;Deploy Container Group&#8221;</li>



<li>Choose the NockPool GPU Prover recipe from available templates</li>



<li>Provide a unique container group name</li>



<li>Paste your NockPool account token</li>



<li>Select desired replica count</li>



<li>Deploy</li>
</ol>



<p>Each container instance authenticates using your token and begins proving work. The distributed nature of SaladCloud means you can scale from testing with a single GPU to running dozens or hundreds across the network, paying only for active compute time. If a node goes offline &#8211; say, because the underlying hardware owner needs their GPU, Salad&#8217;s orchestration automatically relocates your container to another available machine.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h3 class="wp-block-heading">Conclusion</h3>



<p>Nockchain represents one thread in a larger narrative about computational infrastructure and economic coordination. The vision of &#8220;programmable sound money&#8221; &#8211; combining Bitcoin&#8217;s scarcity properties with modern cryptographic research addresses a specific problem: how to create digital systems that are simultaneously secure, private, scalable, and verifiable.</p>



<p>Zero-knowledge proofs are the technical mechanism making this possible. They let you prove computations happened correctly without revealing the computation itself, enabling privacy-preserving smart contracts and off-chain scaling without sacrificing security guarantees.</p>



<p>Whether this particular implementation succeeds depends on factors beyond technology: network effects, developer adoption, regulatory clarity, and competition from established and emerging protocols. The project is making strides with recent advancements including improved tracing kits, enhanced mining analytics, and streamlined app development features.</p>



<p>For miners, the calculation is more immediate: can I profitably generate $NOCK tokens given my costs and the current market price? The combination of fair-launch tokenomics, efficient mining pools like NockPool, and affordable compute through platforms like SaladCloud creates an accessible entry point for participating in this experiment. And who knows how far the token can get.</p>



<p>Tools shape thoughts. Infrastructure shapes possibilities. The systems we build and use don&#8217;t just solve technical problems &#8211; they define the boundaries of what we consider normal, legitimate, and real.</p>



<p>Nockchain&#8217;s experiment asks: what changes when you can verify computations without trust? When money is programmable but provably scarce? When mining rewards go to those who compute rather than those who invested early?</p>



<p>The answers remain uncertain. But the questions and the tools being built to explore them may reshape more than we realize.</p>



<p></p>
<p>The post <a href="https://blog.salad.com/nock-on-saladcloud/">Nockchain ZK Proving on SaladCloud</a> appeared first on <a href="https://blog.salad.com">SaladCloud Blog</a>.</p>
