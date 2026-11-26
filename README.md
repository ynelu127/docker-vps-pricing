# Docker VPS: what $2.48 to $133 a month buys, how much RAM your Compose stack needs, and where SixtyNet's LAX plans fit (full plan table)

A Docker VPS runs from about **$2.48 a month** at the entry end to roughly **$53 a month** for 16 GB of RAM and 8 vCPUs at the top of the mainstream range. Price tracks three things: memory, NVMe capacity, and how much of the Docker setup the host has already done for you. SixtyNet prices its four public-web plans at **$200 to $1,600 per year**, which is $16.67 to $133.33 a month, and sells something narrower than the average container host: an LAX-prefixed line with DDoS protection running on China Unicom's premium AS9929 route.

This page covers SixtyNet's full plan table, how to size RAM for a real compose stack, what annual prepay actually costs you over time, the three ways hosts deliver Docker, and the two restrictions on SixtyNet's residential line that decide whether it's usable for you at all.

> **Quick verdict.** An app plus Postgres plus Redis fits in 4 GB. A single container or a static site behind a reverse proxy fits in 1 GB. A K3s control plane wants 4 GB before you add a single pod. On SixtyNet, LAX-MICRO at **$400/year** is the tier that matches a working compose stack, and you buy it for the China route and the DDoS protection rather than for price per gigabyte. If you just want a cheap Docker box, Cloudzy advertises plans from $2.48/month and LightNode's 2 GB tier is $7.71/month.
> 👉 [Check SixtyNet's current LAX plans and annual pricing](https://sixtynet.net/aff.php?aff=39)

## SixtyNet's full plan line

All four public-web plans carry an LAX prefix and include DDoS protection. Prices are annual prepay with 17% off the list figure.

| Plan | vCPU · RAM · NVMe | Traffic · network | Price (annual) | Order |
| --- | --- | --- | --- | --- |
| LAX-STARTER | 2 cores · 2 GB · 40 GB NVMe (12,500 IOPS) | 5 TB · 2 Gbps premium | **$200/yr** (list $240) ≈ $16.67/mo | [Order LAX-STARTER annually](https://sixtynet.net/aff.php?aff=39) |
| LAX-MICRO | 4 cores · 4 GB · 80 GB NVMe (12,500 IOPS) | 10 TB · 4 Gbps premium | **$400/yr** (list $480) ≈ $33.33/mo | [Order LAX-MICRO annually](https://sixtynet.net/aff.php?aff=39) |
| LAX-MEDIUM | 8 cores · 8 GB · 160 GB NVMe (25,000 IOPS) | 20 TB · 10 Gbps premium | **$800/yr** (list $960) ≈ $66.67/mo | [Order LAX-MEDIUM annually](https://sixtynet.net/aff.php?aff=39) |
| LAX-LARGE | 8 cores · 16 GB · 320 GB NVMe (25,000 IOPS) | 40 TB · 10 Gbps premium | **$1,600/yr** (list $1,920) ≈ $133.33/mo | [Order LAX-LARGE annually](https://sixtynet.net/aff.php?aff=39) |

Two things in that table are worth reading twice. LAX-LARGE doubles RAM, disk and traffic over LAX-MEDIUM but keeps the same 8 cores, so the extra money buys memory rather than compute. And the network bullets on the product page add detail the plan cards don't: a 100M base with a 3× burst rate, billed at the 95th percentile, free BGP session setup with limited BGP technical support. Ninety-fifth-percentile billing rewards steady traffic and punishes sustained saturation, which matters if you're pulling large images or running continuous backups.

### The residential-IP line

SixtyNet also sells four plans with residential IPs on the same core counts and NVMe sizes: 2 cores/2 GB/40 GB with 5 TB, 4 cores/4 GB/80 GB with 10 TB, 8 cores/8 GB/160 GB with 20 TB, and 16 cores/16 GB/320 GB with 40 TB. Note the top tier: residential LARGE lists 16 cores where LAX-LARGE lists 8.

Two warnings sit directly on those plans, and both matter for Docker:

- **Ports 80 and 443 are closed** on residential IPs, for IP risk-control reasons. A container that publishes a website will not be reachable from the internet.
- On the AS9929 residential variants, the page states that refunds are issued **as website balance only**, not back to your payment method.

Pricing for the residential tiers isn't shown in the plan table I reviewed; a third-party review lists $140 to $1,540 a year across the four, below their LAX equivalents. Confirm the current number before you commit.

## What the plan specs don't tell you

The page lists a Docker VPS buyer's most important spec last: nothing in the plan cards mentions a Docker image, a container template or any container tooling. Treat SixtyNet as a self-managed VPS where you install Docker Engine and the Compose plugin yourself. On Ubuntu that's a handful of commands and about twenty minutes, but it also means no control-panel container view, no built-in Traefik routing, and no backup schedule handed to you.

Also absent from the plan specs: the CPU model, a monthly price (only quarterly and annual discounts are advertised), and an upgrade policy. If you need to resize mid-term, ask support before you pay rather than after.

👉 [Read the full SixtyNet VPS spec sheet before picking a tier](https://sixtynet.net/aff.php?aff=39)

## How much RAM your compose stack actually needs

Memory decides the tier; vCPU almost never does at this scale. Disk comes second, because images accumulate.

**One container, or a static site behind Caddy or Traefik: 1 GB.** Cloudzy positions its 1 GB DDR5 tier (1 vCPU, 25 GB NVMe, 1 TB traffic, dedicated IPv4 and IPv6) at exactly this workload. Prune old images or a 25 GB disk fills within months.

**App plus Postgres plus Redis: 2 GB minimum, 4 GB comfortable.** Postgres allocates shared buffers, application runtimes balloon under load, and a `docker build` on the same box competes for the same memory. SixtyNet's LAX-STARTER gives you 2 GB, which runs this stack at low traffic and leaves nothing spare for builds. LAX-MICRO's 4 GB is the tier where you stop thinking about it.

**K3s with a few workloads: 4 GB.** A single 4 GB machine runs a K3s control plane plus a handful of pods; adding nodes means more VPSs plus networking configuration, and floating IPs if you want stable addresses between them.

**CI runners and multi-arch builds: rent by the hour.** Buildx compiling for two architectures roughly doubles peak memory, and the box sits idle the rest of the day. LightNode bills from $0.012/hour, and Cloudzy offers hourly, monthly and yearly terms with no egress fees.

**Compose stack with Prometheus, Grafana and Loki: 4 to 8 GB.** Observability containers are cheap on CPU and expensive on RAM and disk.

On SixtyNet the entry tier gives 2 cores to 2 GB of RAM, which is generous on cores and tight on memory for containers. If your stack is the constraint rather than CPU, LAX-MICRO is the better $200 upgrade than LAX-LARGE is over LAX-MEDIUM.

## Annual prepay, hourly billing, and what the 17% is worth

SixtyNet's discount is real money but it arrives with a commitment. Paying $200 instead of $240 saves $40 a year, and you pay all twelve months on day one. For comparison, LightNode's 2 GB tier at $7.71/month costs $23.13 for three months. A project you abandon in month three costs $200 on SixtyNet's terms and $23 on monthly terms.

Use annual prepay for services you expect to run for a year: a production site, a database you're migrating to, anything with users depending on it. Use hourly or monthly billing for experiments, builds and anything you might tear down.

Traffic allowances change the math too. SixtyNet sells 5 TB to 40 TB per plan and bills bandwidth at the 95th percentile. Cloudzy bundles egress into the monthly transfer allotment and charges only for floating IPs at $2.50/month and snapshots beyond the free quota. If your workload is egress-heavy, compare allowances rather than sticker prices.

One question worth putting to SixtyNet's support: whether the 17% repeats on renewal or only applies to the first term. The page advertises the discounted annual figure without saying.

👉 [Compare the annual SixtyNet price against monthly billing elsewhere](https://sixtynet.net/aff.php?aff=39)

## Three ways hosts hand you Docker

"Pre-installed Docker" covers a wide range of hand-holding, and the difference shows up on day two, not day one.

**Engine installed, nothing else.** LightNode enables Docker by default on its VPS with no action required from you, and OVHcloud ships a ready Docker image with dedicated CPU, RAM and storage plus anti-DDoS protection. You get a working daemon and full root. Everything above it is your problem.

**A graphical manager.** Hostinger's Docker Manager deploys from compose files stored in GitHub or GitLab, adds built-in Traefik routing and HTTPS, and runs weekly automated backups, with an Ubuntu 24.04 plus Docker template as the starting image. Its promotional pricing starts at $6.49/month; the 8 vCPU, 32 GB, 400 GB configuration shown on the same page is a different tier at a different price.

**Templates plus orchestration.** Cloudzy installs Engine and Compose in one click and also offers Buildx for multi-arch images, Swarm, K3s and a self-hosted private registry so you don't pay per pull, plus custom ISO upload on every plan.

SixtyNet sits in the first category, minus the pre-install. You bring your own `docker-compose.yml` and your own SSH session.

## Where SixtyNet fits, and where it doesn't

Buy it if you serve users in mainland China and want the AS9929 premium route under your containers, if you need DDoS protection on a public web host at this price, if you announce your own IP space and want free BGP session setup, or if you want a lot of cores, RAM and traffic on annual terms from a single vendor.

Skip it if you need a European or Asian location, if you want a panel that manages containers for you, if you need month-to-month flexibility, or if you were eyeing the residential line for web hosting. Ports 80 and 443 being closed rules that out completely.

## How the alternatives compare

| Provider | Entry configuration and price | How Docker arrives | Refund and support boundary |
| --- | --- | --- | --- |
| Cloudzy | Plans from $2.48/mo across 512 MB to 64 GB DDR5, all NVMe; 1 GB tier is 1 vCPU, 25 GB NVMe, 1 TB, dedicated IPv4 and IPv6 | One-click Engine and Compose; Buildx, Swarm, K3s, private registry, custom ISO | 14-day money-back, 99.95% SLA, 13 regions |
| LightNode | $7.71/mo for 2 GB, 1 vCPU, 50 GB NVMe, 1 TB; $0.012/hr hourly; $52.70/mo for 16 GB and 8 vCPU | Docker enabled by default, no action needed | Upgrade, downgrade or release at any time; 40+ locations |
| Hostinger | Promotional pricing from $6.49/mo | Docker Manager GUI with compose deploys, Traefik and HTTPS, weekly backups | 1 Gbps, AMD EPYC, KVM, Ubuntu 24.04 plus Docker template |
| OVHcloud | Price not stated on the Docker VPS page reviewed | Pre-installed Docker image, dedicated resources, anti-DDoS, SLA | Full root, scalable resources |
| Bluehost | Price not stated on the page reviewed; NVMe 2 entry tier is 1 vCPU, 2 GB DDR5, 50 GB NVMe | You install Docker, Compose and Kubernetes yourself with root | Infrastructure only: hardware, network and virtualization, 24/7; OS and containers are yours |

## Before you pay: four things to confirm

**Refund window.** Cloudzy publishes 14 days, no questions asked. A third-party SixtyNet review reports a 3-day window, and the AS9929 residential plans refund to account balance only. Three days is short enough that you should test the box the day it comes up.

**Payment method.** The same review reports that credit-card payments were suspended for fraud prevention at the time of writing. Check what the checkout page actually accepts before you plan around a card.

**Upgrade path.** LightNode and Cloudzy both resize RAM, vCPU and storage from the panel, with Cloudzy claiming most upgrades finish in under 60 seconds on live containers. SixtyNet doesn't document a resize policy, so ask.

**IP allocation.** Cloudzy includes a dedicated static IPv4 plus IPv6 on every plan, which is what you need to bind containers to 80 and 443 or run a reverse proxy. SixtyNet's LAX plans don't spell out IP details on the page; the residential line advertises residential IPs with the port restriction described above.

👉 [Open SixtyNet's VPS page and confirm the terms at checkout](https://sixtynet.net/aff.php?aff=39)

## FAQ

**Does SixtyNet pre-install Docker?**
No Docker image or container template appears on its plan page. You install Engine and Compose yourself on a self-managed VPS.

**Can I run Compose, Swarm or K3s on a 2 GB box?**
Compose yes, for a small app plus database. Swarm and K3s will start, but a control plane plus workloads is happier at 4 GB. SixtyNet's LAX-MICRO is the 4 GB tier.

**What's the cheapest way to run a single container?**
Cloudzy advertises plans from $2.48/month, and LightNode bills $0.012/hour if you only need the machine for a few hours a week.

**Is a 40 GB NVMe disk enough?**
For two or three application images plus a modest database volume, yes, with regular pruning. For a private registry, multi-arch builds or log retention, start at 80 GB.

**Can I host a public website on the residential plans?**
No. Ports 80 and 443 are closed on residential IPs. Use the LAX plans for anything that serves HTTP.

## Which plan to pick

One container or a small site, cheapest possible: Cloudzy from $2.48/month, or LightNode hourly if it's intermittent. A real compose stack with users on it: 4 GB, which on SixtyNet means **LAX-MICRO at $400/year**. China-facing traffic with DDoS protection: start at LAX-STARTER's $200/year and move to LAX-MICRO when 10 TB of traffic or 4 GB of RAM becomes the limit. A 16 GB database working set: LAX-LARGE, and only then, since it costs double LAX-MEDIUM for the same 8 cores.

Whichever tier you pick, put the box through its paces inside the refund window, confirm the IP and port situation on day one, and check whether the annual discount survives renewal.

👉 [Start with SixtyNet's LAX-STARTER if the entry tier fits your stack](https://sixtynet.net/aff.php?aff=39)
