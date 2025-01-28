---
title: "It's a Security Thing."
seoTitle: "It's a Security Thing."
seoDescription: "A high-stakes demo, MongoDB, and a critical mistake? Discover how I reset an entire database minutes before a VIP arrive and what happened after..."
datePublished: Sun Dec 15 2024 09:10:10 GMT+0000 (Coordinated Universal Time)
cuid: cm4pdxwpl000009mk2mql1fx3
slug: its-a-security-thing
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734253466265/e57e12db-b884-48cc-926a-a98a15843c21.png
tags: mongodb, meteor, programming-fail

---

My old Heardable Inc boss was formerly in TV production. Heardable, as you might have heard of, was a brand analytics company way ahead of its time.

We were setting up a high-stakes professional [YouTube interview with the Global CEO (David Sable)](https://www.youtube.com/watch?v=BqUhLWgfGXI) of Y&R (one of our incubators at the time), and we wanted to use this event to showcase our brand-new Heardable product - where I was CTO.

Everything was set up, and we were awaiting the arrival of David and other important folks.

I knew the app had a bug that required the Meteor Mongo DB to be reset to clear the cache before the demo.

You see, the thing about Meteor & Mongo is that in the early days of 2015, there was no way of telling their REPLs apart, Meteor's shell looked almost exactly the same as Mongo's shell.

So instead of typing this in the *Meteor Shell*:

```javascript
> mongo reset
```

I typed this in the *Mongo Shell*

```javascript
> mongo reset
```

Which. Well... It kinda... Uhm... Reset the entire DB.

*Seriously though, which part of the above looked bloody different to you??*

Right! I thought. Ten minutes to his arrival, and I just cleared out the DB.

Golden.

Okay, no problem, you got this. There's a backup you did a couple days ago. Grab it and restore it and everything's gonna be right as day.

But there was one problem.

My boss, whose account was going to be used for the demo, had changed her password after the backup, meaning she would have to "Reset Password" before the demo because the backup contained her older one.

Damn, I thought. Why in the world would someone change a password for a non-production server?

Two minutes to his arrival, and my boss walks in.

"All good?" she asked.

"Yes!" I said, "Just that… I need you to do something real quick… Can you reset your password?"

"Now?!" she asked. "You kidding?", she continues, in absolute disbelief and exasperation.

Calmly, I said, "Francine? Yes. Now."

She reset the password, and Ron Hart (the CTO of DropSuite) walked in just as the drama was ending.

"What's going on?" he asked. "Nothing," I said. "All good."

But Francine (my boss) couldn't resist chiming in, "He just made me reset my password!"

"Just a *security thing*…" I said, lying through my teeth. Ron didn’t say a word, but the look on his face said it all:

*Yeah, sure it is…*

Meanwhile, David Sable arrives, and Francine leaves my office. I couldn't take the pressure and went out the back door for a smoke.

Ron came out to join me. He lit himself a cigarette, looked out into the car park in the same direction I was facing - and without turning his head - took a puff and asked,

"You fucked up, didn't ya?"

I took a puff and said, "Yep."

He continues, in the same posture, "How bad?"

I said, "Well. How'd you rate resetting the DB?"

He replies, "Yep. Bad."

We’re still not facing each other, casually looking out into the distance.

He takes another puff, then continues: "You fixed it?"

I replied, after another puff, "Yeah, restored a backup from a couple days ago."

He pauses for a bit, thinking about it, then asks absurdly,

"So you mean between the backup and today, she changed her password?"

I replied, "Yeapp."

He then shrugs, puts out his cigarette, and declares, "Yep… totally a *security thing*… Let's get back inside."

"Yep," I said, putting out my cigarette.

And as if it was nothing more than a smoke break, we walked inside to greet the VIPs and show off the new product demo.

---

Later, reflecting on the chaos of that day, I realised something. Handling high-pressure situations like this didn’t come from my technical skills, it came from DJing in clubs in my younger days.

When DJing, your time bomb resets every new song you play. Especially when you’re playing *uncharted* genres, it’s not bizarre to have just 30 seconds on the clock and not have a next song.

You’d have to pick a song, put in a CD, cue it to the point you wanna play it, say something ridiculous to hype up the crowd, and play the next track - all as if it were deliberate and perfectly intended.

There were times I had to do all of that in just about 10 seconds too.

In those moments, you’re not just playing music - you’re a freggin' time magician, convincing everyone that *this* is exactly what they came for.

Because fundamentally, the clock ain’t gonna stop for no one.

It's *what you do* in that time that matters.

---

Right in the neck that was. Phew