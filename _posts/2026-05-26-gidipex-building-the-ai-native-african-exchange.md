---
layout: post
current: post
cover: https://gidipex.com/og-image.png
navigation: True
title: "Why I'm building Gidipex"
date: 2026-05-26 03:50:00
tags: [tech]
class: post-template
subclass: 'post tag-tech'
author: ovac4u
---

I'm Victor. I run Gidipex.

Gidipex is a crypto exchange. Spot, futures, OTC, P2P, contests, and an AI assistant in the same window. You can use it from the web or by talking to it on WhatsApp.

Here's what I'm actually doing, and why.

## Most exchanges don't fit the users I want to serve

I've worked in payments and crypto since 2013. Started on Hubtel Mobile Money's PHP client, then a stretch at PayPlux Ghana on the engineering side. The pattern is always the same: someone builds an exchange for a generic global user, then the local team gets asked to staple payment rails on afterwards.

That works until something breaks. Then you find out the support team has no visibility into the local rail, the KYC vendor doesn't recognise the user's ID format, and bank-transfer reconciliation turns out to be a spreadsheet someone in the back office rebuilds by hand.

I wanted to build it the other way around. Settlement first, exchange behind it.

## What's actually in the box

A full exchange. Spot pairs, leveraged futures, OTC desk, P2P settlement. Not a thin on-ramp that punts you to somewhere else once you're past your first $100.

![Gidipex Android app command center](https://play-lh.googleusercontent.com/pNAJc8tLWgCcL-iOU9ynIjZX91fEsKdxpVVMxKCQVCSPurLKb5m-UsCrSemSKqOwvlp5zTbiy6wsOz-2HIdYAB8=w1052-h592)

The AI piece — what shows up as "vibe trading" on the site — isn't the product. The exchange is. The AI just makes it easier to talk to. It reads your portfolio, pulls a rate, walks you through opening a position, explains why a futures position got liquidated. It doesn't trade for you. It isn't a signals service.

Mobile money corridors and local bank rails are first-class on the backend. So is the operator tooling. If a withdrawal sits, the person on support should be able to trace it across every hop without opening four dashboards.

## Round 1 of the contest

The first trading contest ran from late February to the end of April. 33 traders entered. 5 finished in payout positions. Top prize was $600 USDT. Total paid out, just over $1,300.

That's small on purpose. The contest is there so a user can feel the platform before funding an account. Round 2 opens June 1 and runs for 30 days. Free entry is open at [gidipex.com/contests](https://gidipex.com/contests).

## What I'm not pretending

We're pre-launch in the paid-acquisition sense. The platform is live, but I'm not running ads yet. I'm not going to claim NGN withdrawals always clear in seconds, because Nigerian bank rails sometimes don't. There are days where a withdrawal sits for an hour while the bank settles its end. I'd rather be plain about that than slap "instant" on a marketing page.

Same for futures. Leverage can amputate an account in an afternoon. The product shows you your liquidation distance, your margin headroom, take-profit and stop-loss controls. It won't nudge you into a bigger position than you can afford. The risk visibility is for the user, not for us.

## Where I'm going

Short version: an exchange where a Nigerian user — or Kenyan, or Ghanaian — doesn't have to mentally translate between their bank, their phone, and their portfolio. The AI surface is the easy part. The boring infrastructure underneath is what decides whether this works.

Gidipex Limited is registered in Lagos. The team is here. The compliance work is happening here. None of that is decoration on a deck.

If you trade, look around at [gidipex.com](https://gidipex.com). Round 2 of the contest is live. Free entry.

— Victor
