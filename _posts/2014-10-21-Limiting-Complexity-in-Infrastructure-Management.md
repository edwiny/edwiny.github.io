---
layout: post
title: Limiting Complexity in Infrastructure Management
---

_This is going to be easy_, was my first thought when I took over the infrastructure management responsibilities for a Internet destination with millions of users. I had been a programmer up to that point and sure of myself that operations type work is less challenging than coding.

Sure. 

What followed instead was the five most challenging years of my career.

I wasn't prepared for the volume and diversity of the issues facing day-to-day operations. From the smallest issues such as helping a user clean up disk space on his workstation, right through to big issues like data center migrations, and everything in between software and hardware become my problem.

To my dismay I found that the automation scripts I wrote actually just made things worse. Not because they did not work well, but because of it. Once they established their utility, they became one more cog in the Rube Goldberg machine that is the operating environment of a medium sized online business.

You see, it was the complexity of it all that got me. Vast, messy, scary complexity, the kind that cannot be discovered by inspecting source code and can only, painfully, be learned by continuous trial-and-error over months, years even. 

There are processes, undocumented and costly with prerequisites unknowable, servers of varying configurations in different networks that cannot talk to each other, software in dependency hell, tools that developers build on that old desktop PC under their desk that provides some critical data used for a executive report, firewall rules that you could not know about until it blocks that oh-so-important product launch, DNS records that must be updated with infrastructure changes, non-reproducible legacy systems with no engineering ownership that supply business-critical utility, out-of-date testing environments with configuration subtlety different from each other or from production, databases that require downtime to change, passwords that are forgotten or laughably easy to guess, critical security vulnerabilities that need to be patched, access control lists that continuously have to be updated and for which you get endless petitions for so-and-so to be added to, hundreds of services that need to be monitored, thousands of monitoring alerts, a endless procession of end-of-life software migrations.

After I've been struggling with this for some time, I one day caught up with a friend and his wife at a social gathering. He was an IT manager at a firm in the manufacturing industry. We were swapping war stories when he asked me how many servers I manage. I said around 800 with a team of three people. His wife exclaimed to him, "Wow, and you have only 30 servers but twice as many people!" I felt embarrassed for him but that did not stop me from enjoying the moment. 

I could have explained to my friend's wife that, even though 800 servers seem like a lot, most of those servers were completely identical to each other, and that I had the most wonderful tools at my disposal to easily configure a new server based off a existing configuration. Overall, there may have only been around 15 distinct configuration profiles, so in a way, I only had 15 - logical - servers to maintain.

In his case, 30 servers were all configured individually for different tasks, and thus he had twice the number of distinct configurations to maintain.

I had been taking this concept of _distinct configuration profiles_ for granted. Up until then it was just something I had inherited from my predecessor. And he from his, and so on. But clearly, it deserved a bit more attention, because it allowed me to successfully manage one particular aspect of system configuration at scale, even while so many others caused me trouble to no end. I wondered if this concept could apply to managing complexity.

Complexity, I decided, is related to the number of moving parts in a system. Once I understood this simple concept and started applying it in my day-to-day work,  I started to make some real progress. Here is what I've learned.


## Digital Realms of Information

I once tried to explain to my elderly grandmother what I do for a living. She knew that I "worked with computers." But, how to explain that the computers were all in a data center far from the office, and except for that one time I almost put out my back helping one of our operations guys lift and move a 80kg router, I never once physically touched any of them?

Then how did I work with computers? After some reflection, I came up with a non-technical explanation that helped to put my job into perspective.

You see, computers can transform and store information in useful ways. You have to encode or buy from someone lists of computer instructions to harness the transformative capability or to provide access to the stored information. Computers are limited in resources but you can connect any number of them together such that they help each other achieve the tasks you set them. The connecting equipment, which are just special computers themselves, together with the computers they connect all have to be tuned in very specific ways for everything to work well together. These tuning efforts can be captured as configuration artifacts and are stored inside the computers where they provide operating instructions for the system.

The computers, the networking equipment, the information stored, the instruction lists, and the configuration artifacts all form part of the digital operating environment of the business I work for. 
This business transform a particular set of information in a particular way that is useful to some people, such that they are willing to hand over money or services in exchange for interacting with our digital products. In order for the business to continue to do this, I have to ensure this digital environment operates smoothly. 

So, "maintaining the digital operating environment" would have been a more accurate job description, but since I couldn't think of this explanation at the time, I stuck with "working with computers."

This digital operating environment is quite interesting. Although some areas within the environment may have very strong affiliation to specific hardware components, for the most part the hardware infrastructure is just a temporary container for the body of information contained within it. Much like all of the cells in our bodies regenerate several times over during our lifetime, individual servers also die and get replaced - yet the digital environment and the information contained within persists.

In a way, this digital operating environment transcends the physical world to establish a persistent identity in the digital realms of information. No where is this phenomenon more apparent than in the Cloud, where infrastructure becomes just more information.

## Configuration Profiles

The body of information contained within the digital operating environment of a average-sized business is huge. Managing the configuration artifacts to maintain this environment is a similarly large undertaking, the full scope of which is often poorly understood by business decision makers.

What should be understood is that every configuration artifact carries with it a support cost. Just like every line of code written has to be maintained, so every byte of configuration will need to be maintained.

Most configuration artifacts form part of a larger collection of artifacts that work together to form some logical function.

An easy example to see this in action is the service. It's easy to think of a service only in term of its' code. But in order for the code to be useful, it has to leave the version control repository and be deployed somewhere. Typically it is transformed in the build process into binaries or software packages. From there it accumulates additional configuration related to it, for example host names where dependent services are located, access control lists, name server records, etc. Before it can actually become useful it needs data. All these items combine to form a configuration profile.

These profiles often repeat through out the operating environment.

For example, in the above case of a service, assume it is horizontally scalable and deployed to a cluster of 500 hosts. The same configuration, with minor differences perhaps like IP address, repeat 499 times.

Another example is user access control lists. If there are ten engineers in the team supporting the above service, the same ten logins have to be created on all 500 severs.

The concept of repeating configuration profiles isn't exactly a ground breaking discovery; however it occurs so pervasively throughout the typical operating environment that it may not always be recognised for what it is.

Here are some more examples:

- *Database schemas* - there are typically many versions of the same database running on the network which live outside of normal change control or provisioning procedures and almost always require heaps of manual support effort.
- *Network devices* - Routers are often deployed in pairs for redundancy, with each pair sharing almost identical configuration.
- *Server images* - It's desirable to have the exact same version of the operating system and system level utilities deployed to groups of hosts. 
- *Applications* - If a application or script is running in multiple instances, you typically want them all to be at the exact same version.
- *Content templates* - HTML templates, code snippets, word processing documents, presentations, spreadsheets - seemingly the same version is replicated over and over.
- *Build jobs* - Many jobs on the typical continuous integration server are almost identical bar a minor configuration difference.



## Logical definition

Every time a configuration profile repeats, it adds to the overall body of information maintained in the digital operating environment - and thus increase the support costs - unless each instance of the profile can be built from the same logical definition.

Every distinct configuration profile has a logical definition. It may exist only informally as a set of instructions in the runbook documentation, or notes scribbled down by the engineer who deployed the first instance, or the runtime state of production server. Ideally, it should exist in a central data store as a set of structured configuration directives that can be passed to a automated provisioning system.

Regardless of the format, using the logical definition as a plan, the profile can be translated into a deployed instance, which is to say the information can be replicated and pushed to its' runtime environment.

The important bit to understand about these repeating configuration profiles are: where they can be replicated in a reliable way from logical definition, the support costs of every deployed instance collapse into one. Where there is no logical definition, every deployed instance of the profile counts its own weight towards the total support cost of the system

Going back to the example of the service deployed to 500 servers; if I have provisioning tools available to automate the setup, then I have only one (logical) server to maintain. If I have to setup each server individually, there are 500 servers to maintain.


## Patterns of work

The primary task of the DevOps engineer is to maintain the digital operating environment in which a business' products run. So far we've established this environment contains a number of repeating configuration profiles.

There are four operational pattens that recur during the maintenance of these profiles:

1. Creation of new profiles - when a new product is developed, new configuration profiles are created.
2. Changing a existing profile - for example doing software upgrades.
3. Duplication of a profile - such as adding another server to a existing cluster of servers.
4. Suspending a profile - retiring a service.

Many of the common tasks DevOps engineers face is just variations on these patterns: supporting a new product launch (creating a new profile), building test environments (duplication of a existing profile), building fail-over capability (duplication), rebuilding failed components (duplication), supporting software releases (changing a existing profile), and retiring legacy systems (suspending a profile). These tasks are typically required several times over the life cycle of a service. 

These patterns are generally performed to a specific configuration profile. The configuration profile, therefore, represents a unit of work in the DevOps team. 

Being conscious of repeating configuration profiles and taking steps to collapse them into logical definitions from where they can be reproduced reliably is a critical step towards managing complexity in your digital operating environment. 


## Reproducibility

When a system component can be easily and consistently rebuilt from bare metal to full operation within a fixed amount of time, whether using automation or runbook documentation, the component is reproducible. 

Reproducibility is key to managing complexity at scale.

When a profile can be reliably instantiated from logical definition, repetitions of the same distinct profile in the digital operating environment becomes free in terms of support cost.

Each of the operational patterns described earlier are in their own ways just variations of the basic pattern of translating the logical profile to a deployed state. For example, in the case of a software release, changes are first made to the logical form of the component, then the new revision is _pushed_ to a deployed state. When a server fails and a new one is provisioned, the same _push_ action is taken.

When a system component is not easily reproducible, not only do repetitions of it add to the overall support costs, but it also increases the overall complexity of a system - seemingly in such a way where complexity begets more complexity.

Standardisation yields efficiency.  When a system component is non-reproducible,  it is likely to be non-standard. Risk of failure will stifle change to the system; when standards in the organisation move on, this component is left behind. What's more, dependencies on this component are likely to be poorly understood, testing environment for the component is probably of low quality or non-existent, and specialised skills are required to support it. This results in a high support cost for the component, but this cost often remains hidden to the operations manager who ends up under-staffing the team. Overcommitted support engineers make mistakes and take shortcuts which lead to fragmentation (see section below.) 



## Fragmentation

Over time, business pressures and natural entropy will fragment configuration profiles into smaller, more numerous profiles.

Breaking up a more general profile that is shared across many components into smaller profiles that are shared by fewer components may initially seem like a good idea because individual components get a closer configuration fit, but in reality there is no or little benefit in doing so, and it increases the total number of distinct configuration profiles, thus adding support load and complexity.


Here are some examples of fragmentation:

- tailoring the list of software installed for each server cluster in stead of installing the same list of software to all clusters
- creating many fine-grained user access control lists in stead fewer, broader lists
- creating a test environment based on the logical definition of the production environment, but removing software or configuration that is deemed to be production-only, like monitoring. 

There is of course a balance to be struck between customising configuration to increase its' relevancy to individual components and generalised, install-everywhere configuration. Unless there is measurable reward for customisation, it is better to lean towards generalisation.

Here is a another example of fragmentation occurring: suppose you support a product the infrastructure of which is mirrored across different hosting locations. The product expands functionality and infrastructure is provisioned in the primary serving location for a new backend component. However, due to time or budget constraints, the additional infrastructure is not provisioned in the fail over location. A decision is made to co-host the new component on the infrastructure of a existing component. This amalgamated component breaks configuration parity with the primary location, and fragments the system configuration into two distinct profiles.


A more subtle form of fragmentation occurs when changes are made to a deployed instance of a configuration profile, but not back-factored into the logical definition, for example when a change is made to a production server without updating the image definition. Now the logical definition cannot be synced to the production server without overwriting the manual changes. Until the changes can be factored into the logical definition, and any changes that may have accumulated in the logical definition pushed to the changed deployed instance, the changed deployed instance essentially forms a additional, distinct configuration profile.


## Conclusion

The IT infrastructure of any business consist of a physical infrastructure component and a digital infrastructure component. The digital infrastructure requires as much coordinated effort to maintain as the physical infrastructure. Configuration patterns repeat across the digital operating environment; organising them into reproducible configuration profiles will reduce complexity and enable management efficiencies.

Being conscious of repeating configuration profiles and taking steps to collapse them into singular, deployable, logical profiles is a important step towards managing configuration at scale.


