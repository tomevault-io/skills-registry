---
name: ewa-dart-runtime
description: Dart ile çok platformlu (minimal RAM) Edge Workflow Agent runtime’ını (YAML job engine + BLE + local KV + sadece MQTT) scaffold etmek, güncellemek ve doğrulamak için kullan. Use when this capability is needed.
metadata:
  author: mskayali
---

# EWA Dart Runtime Skill

## Amaç
Bu skill, bu repoda "Edge Workflow Agent (EWA) — Agent Model v1" mimarisine uygun bir Dart runtime üretir/uygular:
- YAML ile job/trigger/step
- BLE/GATT adapter (universal_ble)
- Network: sadece MQTT
- Local KV (memory/disk/hybrid), TTL, düşük RAM
- Raw payload log opsiyonel (segment files)
- Sync/async (spawn/await), try/catch, if/switch

## Kesin Kurallar (Minimal + Optimize)
- Ağ protokolü sadece MQTT olacak. HTTP/REST/Websocket ekleme.
- Reflection/dynamic invocation yok: step runner switch-case.
- Bounded queue + bounded concurrency: event/task/job limitleri şart.
- Default davranış: job step’leri sıralı (sync). Async sadece açıkça istenirse.
- Persist KV opsiyonel olacak (store.mode = memory|disk|hybrid).
- Raw payload saklama default kapalı; açılırsa segment log (append-only + rotate).
- Her yeni feature eklemeden önce mevcut modeli stabilize et.

## Kullanım Senaryoları (Ne zaman tetiklenmeli)
- "EWA runtime skeletonunu oluştur"
- "YAML step/trigger ekle ama minimal kalsın"
- "KV persist/hybrid store ekle"
- "try/catch + if + async spawn/await semantiğini uygula"
- "RAM tüketimini azalt / bounded queue uygula"
- "Örnek YAML config üret ve test ekle"

## Yapılacaklar (Uygulama Planı)
1) Repo içinde `.agent/skills/ewa-dart-runtime/resources/agent-model-v1.md` dosyasını kaynak doğrusu kabul et.
2) `resources/project-layout.txt` ile aynı path yapısını oluştur/koru.
3) Dart projesi yoksa CLI tabanlı oluştur (UI yok).
4) Core arayüzleri üret:
   - BleAdapter, MqttAdapter, Store(KV+stream+queue), Codec
5) Engine’i üret:
   - Trigger dispatcher: startup/interval/mqtt/ble_notify
   - Step runner: ble.*, mqtt.*, parse.*, store.*, control (if/try/await)
   - Task registry: spawn/await (bounded)
   - Event bus: bounded queue + coalesce policy
6) Store:
   - memory store: LRU+TTL
   - disk store: SQLite KV (minimal indexes) veya eşdeğer
   - hybrid store: RAM cache + disk backing
   - spool queue: MQTT offline publish
7) Örnek YAML’ı `resources/sample-config.yaml` ile hizala.
8) Test:
   - mock BLE + mock MQTT
   - engine step/trigger semantiği (try/catch, spawn/await)
9) Çıktıyı doğrula:
   - `dart analyze`
   - temel unit testler yeşil
   - RAM patlaması oluşturacak sınırsız kuyruk yok

## Çıktı Formatı
- Uyguladığın/oluşturduğun dosyaları bir liste olarak raporla.
- Önemli kararları 5-10 satırda özetle (özellikle concurrency/store).
- Ekstra feature önerme; sadece istenen kapsam.

## Not
Antigravity Skills formatı: SKILL.md YAML frontmatter + markdown body. :contentReference[oaicite:3]{index=3}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mskayali) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
