---
layout: post
title: "Transactions to Streams"
comments: true
tags:
 - video
 - observations
---

I watched this today

[https://www.infoq.com/presentations/event-streams-kafka](https://www.infoq.com/presentations/event-streams-kafka)

Really meshes with how I’ve been thinking about mobile clients staying in sync and provides some good ideas … I wonder if I would need something like kafka or if I would something simpler would work? 

Elixir has been my target for this, but maybe since kafka is built on scala I need to consider that more. 

If you uses somethign like kafka for this, then I think each mobile 'client' would have their own view/database on a server. something that would ensure a single writer. Then on a sync it would consume any new events from kafka and send the correct state back to the client.


