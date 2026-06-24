---
name: trello
description: Manage Trello boards, lists, and cards via the Trello REST API. Use when this capability is needed.
metadata:
  author: kody-w
---

# Trello

Manage Trello boards, lists, and cards.

## List Boards

```bash
curl -s "https://api.trello.com/1/members/me/boards?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[].name'
```

## Get Lists in a Board

```bash
curl -s "https://api.trello.com/1/boards/BOARD_ID/lists?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" | jq '.[] | {name, id}'
```

## Create a Card

```bash
curl -s -X POST "https://api.trello.com/1/cards" \
  -d "key=$TRELLO_API_KEY&token=$TRELLO_TOKEN&idList=LIST_ID&name=New+Card&desc=Description" | jq '{name, url}'
```

## Move a Card

```bash
curl -s -X PUT "https://api.trello.com/1/cards/CARD_ID" \
  -d "key=$TRELLO_API_KEY&token=$TRELLO_TOKEN&idList=NEW_LIST_ID"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
