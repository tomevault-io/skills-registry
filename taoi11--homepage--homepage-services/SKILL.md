---
name: homepage-services
description: Configure services.yaml for Homepage dashboard - service definitions, widgets, Docker integration, icons, and monitoring Use when this capability is needed.
metadata:
  author: taoi11
---

# Homepage services.yaml Configuration

Reference for configuring services in Homepage dashboard.

## Structure

```yaml
---
- GroupName:
    - ServiceName:
        href: http://service.url
        # Additional properties...
```

## Service Properties

| Property      | Type    | Required | Description                    |
| ------------- | ------- | -------- | ------------------------------ |
| `href`        | string  | **Yes**  | URL to open when clicking      |
| `description` | string  | No       | Text under service name        |
| `icon`        | string  | No       | Icon name or URL               |
| `server`      | string  | No       | Docker server from docker.yaml |
| `container`   | string  | No       | Docker container name          |
| `ping`        | string  | No       | Hostname for ICMP ping         |
| `siteMonitor` | string  | No       | URL for HTTP monitoring        |
| `statusStyle` | string  | No       | `dot`, `basic`, or empty       |
| `showStats`   | boolean | No       | Show Docker stats expanded     |
| `target`      | string  | No       | `_blank`, `_self`, `_top`      |
| `id`          | string  | No       | Custom ID for CSS/JS           |
| `widget`      | object  | No       | Widget configuration           |
| `widgets`     | array   | No       | Multiple widgets               |

## Nested Groups

```yaml
---
- Parent Group:
    - Parent Service:
        href: http://service.url
    - Child Group:
        - Child Service:
            href: http://child.url
```

## Single Widget

```yaml
- Jellyfin:
    icon: jellyfin.png
    href: http://jellyfin.host:8096
    server: my-docker
    container: jellyfin
    widget:
      type: jellyfin
      url: http://jellyfin.host:8096
      key: { { HOMEPAGE_VAR_JELLYFIN_API_KEY } }
      fields: ["activeStreams", "totalMovies"]
```

## Multiple Widgets

```yaml
- Jellyfin:
    widgets:
      - type: jellyfin
        url: http://jellyfin.host:8096
        key: { { HOMEPAGE_VAR_JELLYFIN_API_KEY } }
      - type: uptimekuma
        url: http://uptimekuma.host
        slug: jellyfin-status
```

## Widget Properties

| Property     | Type    | Description                              |
| ------------ | ------- | ---------------------------------------- |
| `type`       | string  | Widget type (see homepage-widgets skill) |
| `url`        | string  | Service API URL                          |
| `key`        | string  | API key using `{{HOMEPAGE_VAR_*}}`       |
| `fields`     | array   | Fields to display                        |
| `headers`    | object  | Custom HTTP headers                      |
| `hideErrors` | boolean | Hide error messages                      |

## Field Visibility

```yaml
- Sonarr:
    widget:
      type: sonarr
      fields: ["wanted", "queued"]
      url: http://sonarr.host
      key: { { HOMEPAGE_VAR_SONARR_API_KEY } }
```

## Block Highlighting

```yaml
- Sonarr:
    widget:
      type: sonarr
      url: http://sonarr.host
      key: { { HOMEPAGE_VAR_SONARR_API_KEY } }
      highlight:
        queued:
          numeric:
            - level: danger
              when: gte
              value: 20
            - level: warn
              when: gte
              value: 5
            - level: good
              when: eq
              value: 0
```

**Numeric operators**: `gt`, `gte`, `lt`, `lte`, `eq`, `ne`, `between`, `outside`
**String operators**: `equals`, `includes`, `startsWith`, `endsWith`, `regex`

## Icons

```yaml
icon: sonarr.png           # Dashboard Icons
icon: si-youtube           # Simple Icons
icon: mdi-flask-outline    # Material Design Icons
icon: sh-plex              # selfh.st icons
icon: si-youtube-#a712a2   # Custom color
icon: https://example.com/icon.png  # Remote URL
icon: /icons/custom.png    # Local (mount /app/public/icons)
```

## Docker Integration

```yaml
- Emby:
    href: http://emby.home/
    server: my-docker # Must match docker.yaml key
    container: emby # Container name
    showStats: true # Show CPU/memory/network
```

## Ping / Site Monitor

```yaml
- Sonarr:
    ping: sonarr.host # ICMP ping

- Radarr:
    siteMonitor: http://radarr.host/ # HTTP HEAD/GET
```

## Common Patterns

### Media Service with Widget

```yaml
- Jellyfin:
    href: http://jellyfin.host:8096
    icon: jellyfin.png
    server: Unraid
    container: jellyfin
    showStats: true
    widget:
      type: jellyfin
      url: http://jellyfin.host:8096
      key: { { HOMEPAGE_VAR_JELLYFIN_API_KEY } }
      enableBlocks: true
      enableNowPlaying: true
```

### Torrent Client

```yaml
- Transmission:
    href: http://transmission.host:9091
    server: Unraid
    container: transmission
    widget:
      type: transmission
      url: http://transmission.host:9091
      username: { { HOMEPAGE_VAR_TRANSMISSION_USERNAME } }
      password: { { HOMEPAGE_VAR_TRANSMISSION_PASSWORD } }
      fields: ["leech", "download", "seed", "upload"]
```

### Simple Link (No Widget)

```yaml
- LocalAI:
    href: http://localai.host:8082
    icon: mdi-robot
    description: Local LLM server
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/taoi11) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
