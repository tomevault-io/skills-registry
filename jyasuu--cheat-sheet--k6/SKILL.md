---
name: k6
description: Load testing tool with installation and test execution commands. Use when this capability is needed.
metadata:
  author: jyasuu
---

# k6 — Load Testing

**Install**

```bash
sudo gpg -k
sudo gpg --no-default-keyring --keyring /usr/share/keyrings/k6-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69
echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
sudo apt-get update
sudo apt-get install k6
```

**Run Test**

```bash
k6 run --out influxdb=http://localhost:8086 k6.js
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jyasuu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
