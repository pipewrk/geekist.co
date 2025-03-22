---
title: "Why Your Tech Stack is Overrated (And What Actually Matters)"
seoTitle: "Tech Stack Importance: Myths vs. Reality"
seoDescription: "The real challenge in 2025 isn’t React Native vs. Native, it’s building software that lasts. Here’s what truly matters for future-proof development."
datePublished: Sat Mar 08 2025 02:22:50 GMT+0000 (Coordinated Universal Time)
cuid: cm7zkxrh4000009jl9ukp67va
slug: your-tech-stack-doesnt-matter-as-much-as-you-think
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/zEqkUMiMxMI/upload/ec2ea0347850d74c5afde9fcdec230d5.jpeg
tags: microservices, software-architecture, automation, scalability, techstack, aiincoding

---

### The Debate That Didn’t Matter

In 2017, everyone - including yours truly - was passionately caught up debating the virtues of [React Native versus going fully Native](https://www.inovex.de/de/blog/react-native-vs-native-development/). I even penned a fiery rant about it [here](https://geekist.co/hybrid-vs-native), convinced I'd cracked the code myself on app development.

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

AWS Lambda and serverless computing were revolutionary. Now we’ve got platforms that promise to generate entire apps for you. Technically, they can. Realistically? What they produce is usually somewhere between “basic” and “well... that’s not how I’d have done it.”

I’ve never truly believed that AI will replace developers. At least, not yet. Mostly because I’ve spent more time fixing AI-generated code than I would have spent writing it myself. It doesn’t think like a developer. It thinks like someone who skimmed Stack Overflow for 20 minutes and decided to wing it.

A lot of what it generates feels oddly generic. It follows patterns, sure, but not always ones that make sense in the real world. After writing software for years, you start making deliberate architectural decisions. Ones that just don’t show up in a model’s training data. That’s why the real value of AI in coding lies somewhere else entirely.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741407913059/f67a9ecd-8ff9-4fb7-8a7d-dfd7763f5aa5.png align="center")

Where AI *does* shine is in speeding up the boring parts. Low-code and no-code tools now give you prototypes in hours instead of weeks. You get a decent starting point, something to iterate on, without burning time reinventing the same wheel for the fifth time this year.

From there, AI becomes more of a helpful co-pilot than a lead engineer. It handles grunt work. You get to focus on architecture, user experience, and solving actual problems. It hasn’t reduced our roles, it’s helped refine them. We’ve moved from line-by-line coders to strategic designers who just happen to type faster now.

Personally, I use AI to tidy things up, refactor messy sections, or even nudge me when I’m stuck. But I wouldn’t let it write something mission-critical. Not unless I want my future self yelling at my past self in Git blame.

AI-generated code still needs human eyes. Someone has to spot the subtle bugs, the edge cases, the stuff that "probably works" until it really doesn't. And who better to do that than the people who already know where the skeletons usually hide?

I’m genuinely excited about where it’s headed. Not because it will replace me, but because it might finally let me stop writing boilerplate for good. And honestly? That’s the dream.

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