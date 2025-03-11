---
title: "Your Tech Stack Doesn’t Matter (As Much As You Think)"
seoTitle: "Tech Stack Importance: Myths vs. Reality"
seoDescription: "The real challenge in 2025 isn’t React Native vs. Native, it’s building software that lasts. Here’s what truly matters for future-proof development."
datePublished: Sat Mar 08 2025 02:22:50 GMT+0000 (Coordinated Universal Time)
cuid: cm7zkxrh4000009jl9ukp67va
slug: your-tech-stack-doesnt-matter-as-much-as-you-think
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/zEqkUMiMxMI/upload/ec2ea0347850d74c5afde9fcdec230d5.jpeg
tags: microservices, software-architecture, automation, scalability, techstack, aiincoding

---

### The Debate That Didn’t Matter

In 2017, everyone - including yours truly - was passionately caught up debating the virtues of R[eact Native versus going fully Native](https://www.inovex.de/de/blog/react-native-vs-native-development/). I even penned a fiery rant about it [here](https://geekist.co/hybrid-vs-native), convinced I'd cracked the code myself on app development.

But looking back now, it feels like we were totally arguing about who had the cooler shoes in primary school which as fun as it was at the time, was ultimately meaningless. The truth is, it wasn't frameworks or flashy tech that determined success; it was something *far more mundane*.

Real success hinged on fundamentals like [choosing an architecture](https://www.archdaily.com/904882/understanding-and-using-architectural-scales) that [scales](https://aosabook.org/en/v2/distsys.html), keeping logic decoupled and resisting those tempting short-term optimisations that always seem clever until you're buried under technical debt months later.

Turns out, frameworks come and go, but good architecture? That sticks around.

**What Actually Matters for Future-Proof Software**

If I could distill the years between 2017 and today into a single insight, it’d be this:

> Frameworks fade; the way you structure your software endures.

### **AI and Automation Changed Everything**

At the time, having [JavaScript run everywhere was groundbreaking](https://naturaily.com/blog/react-native-things-to-know). Today, AI and automation have transformed the landscape completely. Debugging, for example, used to mean long nights hunting obscure errors. Now, AI-assisted tools help pinpoint common issues in minutes, making troubleshooting more efficient - but its far from perfect.

Automated CI/CD pipelines have made updates effortless across every platform, no longer making deployment feel like a stressful space launch. Even components and tests can now be generated with AI, freeing developers from tedious, repetitive tasks to focus on higher-level design.

The future doesn’t care if your stack is cool; it cares how quickly and reliably you can adapt.

**Security Finally Gets Its Due**

Security was still a hot topic then too but not many knew what exactly to do beyond the understood norms and it often felt like an afterthought - we built fast and hoped nothing was vulnerable. Today, security isn't optional; it's essential from day one.

Modern threats, like AI-powered cyberattacks and vulnerabilities hidden deep in supply chains, weren't even on our radar back then. [Regulatory compliance](https://developers.google.com/solutions/content-driven/data-storage/regulatory-compliance) now also demands that security be baked into the foundation of every application. Building resilient software isn't about paranoia, (well it is slightly) it's about responsibility and longevity.

### AI in Coding went from Novelty to Necessity (Almost)

[AWS Lambda](https://siliconangle.com/2017/08/15/a-cloud-guru-uses-lambda-and-api-gateway-to-build-serverless-company-awssummit/) and serverless computing? Pure Revolution. Today we have coding platforms that generate entire apps for you (It can, but the stuff it generates is quite rudimentary). I have never once thought (nor will I believe) that coding can be completely replaced by AI - yet!

The reason I say this is because I have spent much more time debugging AI-generated code than I would my own. To start with, I wouldn’t have written many things the way it would, having learnt so much over the years, I structure things wildly different from the common scenarios that LLMs are trained with. But while AI-generated code still has a long way to go, its real value emerges elsewhere…

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741407913059/f67a9ecd-8ff9-4fb7-8a7d-dfd7763f5aa5.png align="center")

Today, we have low-code and no-code tools that can generate prototypes in hours instead of weeks. And it is things like this that make AI shine. The heavy lifting, the project starters, the strategic overview. After that it becomes a companion at best. This converts developers into strategic architects, crafting scalable systems rather than wrestling with mundane details. If anything, these tools elevated our roles by freeing us from the busy work.

Personally, I prefer using AI to handle specific parts of the code where structure or organisation can be enhanced, rather than relying on it entirely

I’d say LLM generated code has a long way to go and even if it gets there, you’d still need humans in the mix - and by definition - these humans are likely coders themselves because who else is going to debug AI’s mistakes?

I am hopeful though, and I am really looking forward to it because that means I can focus on things that really matter and leave the grunt-work to something else.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">I’ll write about some of the tools I have reviewed in an upcoming article, giving you a better understanding on what’s out there now and how it can complement your workflow.</div>
</div>

### Microservices vs. Monoliths: Déjà Vu All Over Again

Just as 2017 had its React Native drama, today's hot debate is "microservices or monolith?" It's tempting to pick a side and argue until you're blue in the face but here's what history has taught us:

> Neither one is a silver bullet.

Sure, a monolith might get your MVP out the door faster, but as complexity grows, it starts to feel like lugging around an increasingly heavy backpack. Microservices offer flexibility and modularity, but demand strict discipline to keep complexity in check.

*The trick is to build a monolith with the future in mind. Code structured around isolated business units or features stands the test of time.*

(Crap! That rhymed!)

Practically speaking, your code should communicate through clearly defined interfaces - a common protocol if you will. This could be REST, GraphQL, or an event-driven architecture, depending heavily on your infrastructure, team expertise and available resources

If each feature-driven section of your codebase can live and operate independently within your stack, transitioning from a monolithic structure to a standalone microservice becomes a surprisingly smooth process.

**The Real Lesson is that The Framework Was Never the Point**

I wasted way too many hours back then debating the merits of frameworks, only to realise years later how pointless it was. Winning arguments didn't make software any better; understanding longevity did.

Since then, I've been fortunate to lead numerous mobile development projects. One highlight was at Prudential, where we built federated React Native components used across various markets. The framework itself? Just a means, never the end.

### A Final Thought, a Look Beyond Today's Trends

When picking your tech stack today, don't ask yourself, "Will this still be cool in 2025?" Instead, challenge yourself with, "Will this still be relevant in 2030?" If your answer depends heavily on a particular framework, you might already be falling behind.

Remember, tech stacks are transient, but good architecture and thoughtful design decisions are timeless.