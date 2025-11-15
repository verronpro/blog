---
layout: article
title: "Nextcloud self‑hosting: interests and hurdles"
date: 2023-08-01 09:00:00
categories: [ nextcloud, hosting ]
author: Joseph
tags: [ nextcloud, self-hosting, privacy, hosting ]
description: "Self‑hosting Nextcloud for family needs on a Gandi host: what worked, what didn’t, and the operational cost of running it with a provider."
---

TL;DR — I self‑hosted Nextcloud on my Gandi host for family file sharing, calendars, and photos. It was a practical tool that delivered on privacy and flexibility, but still required steady maintenance (updates, backups, uptime). I later retired the instance for personal/professional reasons, not due to product dissatisfaction.

As a software engineer who prefers owning the tooling and data, I hosted a small Nextcloud instance on my Gandi infrastructure for my family. It let us run a truly practical shared tool without relying on a consumer SaaS. Below is a concise record of the interests that made it appealing, and the hurdles that matter if you operate it yourself.

## Why Nextcloud (the interests)

- Data ownership and privacy: all files and calendars live under an account/infrastructure I control; no third‑party retention surprises.
- Family collaboration: file sharing, calendar sync, and notes are good enough out of the box, with mature mobile/desktop clients.
- Flexibility: apps and integrations let you tune the feature set; you decide the trade‑offs (features vs. simplicity).

## The hurdles that matter

- Initial setup: web server, TLS, database, storage layout on the host. Nothing exotic, but you need working backups and tested restores from day one.
- Ongoing maintenance: regular security updates (OS + Nextcloud + apps), rotating credentials, and monitoring for disk usage/quotas at the provider.
- Reliability and availability: provider incidents and planned maintenance happen. Plan for downtime windows and communicate with users; mind DNS/SMTP constraints when running on a shared host.

## What worked well

- Keep the stack simple (reverse proxy + Nextcloud + database + object/file storage).
- Automate upgrades and backups; test restoring a snapshot before you need it.
- Limit extra apps to the ones you truly use; fewer moving parts equals fewer surprises.

## Wrapping up

Self‑hosting Nextcloud does what it says on the tin: you get control and useful collaboration features, in exchange for operational responsibility. If that trade‑off fits your goals and time budget, it is a solid option.

## Addendum (2025‑11)

I stopped maintaining this particular Nextcloud instance. The decision was not about product quality—I remained satisfied with Nextcloud itself. It simply no longer aligned with my professional workload, and my personal plan to move to China, where my priorities and constraints changed. If I need a private cloud again under similar conditions, I would
consider Nextcloud without hesitation.
