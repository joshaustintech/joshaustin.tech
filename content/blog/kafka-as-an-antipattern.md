---
title: "Kafka As An Antipattern"
date: 2023-09-24T09:28:51-05:00
tags: ["kafka", "java"]
summary: "We wouldn't tolerate this with a database."
cover:
    image: "images/undraw_Notify_re_65on.png"
    alt: "drawing of a person startled by a warning symbol"
---

# Introduction

Kafka is a deceptively deep topic. No pun intended. I've used Kafka in various contexts. I want to cover two experiences
where I've had two very different outcomes to point out at least one scenario where I believe Kafka is an antipattern.

# When Kafka worked great

During my time contributing to the order visibility APIs and UI on the [Movement](https://joinmovement.project44.com/) platform, we used Kafka to queue and process thousands of messages a minute. It was mission-critical to render accurate, real-time order visibility to recognizable brands including Dollar General and Starbucks. Batching this data was out of
the question, since it would be potentially stale by the time it was rendered.

The architecture was simple and yet highly scalable:

1. A REST Endpoint would receive the data and immediately emit a 200 OK if the format was valid.
2. The data was immediately serialized and sent to the first Kafka topic for processing.
3. A processing microservice (often multiple instances of it) would pick up the data as quickly as possible and act as
    a "traffic controller", directing the data to where it needed to go next.
4. The data would be sent to a Kafka data sink to land in a database or else another Kafka topic to be processed by another microservice.

For this use case, Kafka worked great. Data was processed as soon as possible. Outages were handled gracefully, since the data was stored in a log. The data was processed in the order it was received, or (rarely) rewound as needed.

One problem I recalled was some recurrent quirks when mapping Avro schemas to Java classes. This was a problem that seemed
inherent with Avro, not Kafka per se. The consensus from this experience among my team was that plaintext JSON
messages were better.

This is where I can see the potential in Kafka as the backbone of an event-driven architecture. However, I was on the
application side of that benefit, and didn't do very much with configuring the Kafka infrastructure itself.

# When Kafka was a nightmare

While working for a client at a consulting firm, I was tasked with a "future-proof" family of microservices that would
handle roughly 750,000 Kafka messages a year, give or take. A normal day would be in the ballpark of 2,000 messages a day,
but at peak it would be closer to 8,000 messages a day. We were to integrate with a pair of Kafka topics managed by
another team.

The flow was simple:
1. Pull messages from the database.
2. Send the messages up one Kafka topic.
3. Listen to another Kafka topic, update the database with the results if appropriate.

Ignoring the part where the other team's communication and documentation were lacking, speaking from a technical
point of view, every step was TORTURE!
- We couldn't create a Kafka source connector from the database holding the messages, which defeated idempotency,
so we had to implement idempotency ourselves through table-locking queries.
- The other team wanted custom Kafka message headers for some reason, but when we implemented their headers we had extreme
difficulty troubleshooting when their system refused our best understanding of said custom headers.
- Authentication with Kafka, and authentication to encrypt confidential data in Kafka, and getting the Avro schema
from the schema registry were all separate and painful steps. These all took more time than the actual business logic.

This is when I realized...**We would never tolerate this level of complexity with a database**, why tolerate it with
Kafka??? In my situation, it became an utter violation of the
[KISS Principle](https://en.wikipedia.org/wiki/KISS_principle).

It's time for something better. That something better could be small, simple JSON messages for Kafka. It could
be to use a different message broker. It could just be to use the database as a simple queue.

While my client was in a situation where we had no choice but to use this monstrosity of Kafka, Avro, and custom
message headers, I would never recommend this usage of Kafka if I had the option.

I would reserve Kafka for small, simple JSON messages for events and data that must not be lost in transit in the event
of an outage. I would also reserve Kafka for high-volume data that must be processed in the order it was received.

# Conclusion
- If you are spending more time on the infrastructure of your message queue than on the application itself,
your organization is probably doing it wrong.
- If you are handling thousands of messages a day, a simple database-driven queue might be better than Kafka.
- If you are handling thousands of messages a minute, Kafka is better than a simple database-driven queue.
- No idea yet where the cutoff is between the above two situations.
- Just say no to Avro.