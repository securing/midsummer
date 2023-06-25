[[ 🡅 Move up ]](../)</br>
[[ 🡄 Previous ]](../sfi18_ctf%20by%20SFI/) **The Midsummer Corp Hack 2023 by SecuRing**

# The Midsummer Corp Hack

> Oh, my dear child, the fern flower is indeed a wondrous thing. It is said to be a rare and magical plant that blooms only once a year and only for a brief moment at that. Its beauty is unparalleled, with delicate petals that shimmer and glow in the moonlight.

## Table of Contents
- [Getting started with the writeup](#getting-started-with-the-writeup)
- [Event info](#event-info)
- [Placement](#placement)
- [Personal thoughts](#personal-thoughts)
- [What I learned](#what-i-learned)

## Getting started with the writeup
Head into [00: Writeup Structure](./00_Structure/README.md) to begin your journey.
## Event info

**Authors**: SecuRing | [Website](https://www.securing.pl/) ☆ [Facebook](https://www.facebook.com/SecuRingPL) ☆ [LinkedIn](https://www.linkedin.com/company/securing/) ☆ [Twitter](https://twitter.com/SecuRingPL) </br>
**Took place**: Online </br>
**CTF website**: https://tryhackme.com/room/midsummer </br>

**Summary**: In this CTF based around [NextCloud server](https://github.com/nextcloud/server) we are tasked with stoping the Midsummer Corp before they get their hands on the mystical Fernflower and bring the end to the world as we know it. You need to infiltrate the company and gather all the clues you can about the Flower and find it before they will. Can you complete the hack before it's too late?

**CTF type**: Jeopardy </br>
**Category**: Web </br>
**Difficulty**: Medium </br>
**Task count**: 7 stages with 25 tasks total

**Start date**: June 5, 2023, 9:00 AM </br>
**End date**: *The event seems to last indefinately*

## Placement

Achieved 1<sup>st</sup> place (ex aequo) out of 105 active participants (ie. people who completed at least 1 task) (stats for July 22, 2023)

![Placement - 1st place alongside rubicon02](./media/placement.png)

## Personal thoughts

Let me get this out of the way - this CTF was a blast! The setting, the challenges, the difficulty ramp-up - everything was just spot on.

Each Stage contains a quick summary of the vulnerability you will exploit, alongside useful resourcer in case you want to understand the topic even better. Tasks provided in each Stage guide you through the process of discovering the weakpoints of the system, helping you achieve this "click" moment when everything starts to make sense.

As someone who spent a lot of time dealing with web-based stuff, I learned a lot by exploiting the system both in easy and borderline stupid, as well as sophisticated ways.

I am looking forward to the next edition, for sure!

## What I learned

- I learned how to search the source code for weakpoints and faulty logic
- I understood how MFA works, why do we need to scan those weird QR code thingies and how is it possible that server knows if the code I entered from my phone is the correct one
- I learned how rate limiting can be implemented and how easily it can be bypassed if done naively
- I discovered new tools, namely [ModHeader](https://modheader.com/) for modifying headers inside the browser and [Postman Interceptor](https://learning.postman.com/docs/sending-requests/capturing-request-data/interceptor/) for synchronizing cookies between the browser and Desktop client
- I understood the logic behind SSO + SAML and why you always want to sign claim-based data, whether it's SAML XML or JWT Token
- I refined my knowledge about OAuth workflow and common pitfalls

</br>
</br>
</br>

[[ 🡅 Move up ]](../)</br>
[[ 🡄 Previous ]](../sfi18_ctf%20by%20SFI/) **The Midsummer Corp Hack 2023 by SecuRing**