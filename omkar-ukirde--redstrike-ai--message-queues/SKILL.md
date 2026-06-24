---
name: message-queues
description: Skills for attacking message queue and IoT messaging services including MQTT, AMQP, and Kafka. Use when this capability is needed.
metadata:
  author: omkar-ukirde
---

# Message Queues

Message broker and pub/sub system exploitation.

## Skills

- [MQTT Pentesting](references/mqtt-pentesting.md) - MQTT broker (1883)
- [AMQP/RabbitMQ](references/amqp-rabbitmq-pentesting.md) - RabbitMQ (5672/15672)
- [Kafka Pentesting](references/kafka-pentesting.md) - Apache Kafka (9092)

## Quick Reference

| Service | Port | Key Attack |
|---------|------|------------|
| MQTT | 1883 | Subscribe #, no auth |
| RabbitMQ | 5672 | guest:guest |
| Kafka | 9092 | No auth, consume topics |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/omkar-ukirde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
