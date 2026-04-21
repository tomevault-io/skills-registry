---
name: social-community
description: Build social features including posts, comments, likes, and user interactions. READ BEFORE MODIFYING ANY SOCIAL MEDIA CODE. Use when this capability is needed.
metadata:
  author: smarter-poker
---

# 🛡️ Social Media Protection & Development Guide

> **🚨 CRITICAL**: This skill documents ALL working features in the social hub. Before making ANY change to social-media.js or related files, you MUST verify all features still work after your changes.

## Why This Exists

The social media page (`/hub/social-media.js`) is a 2700+ line file with 60+ functions that powers the entire social experience. It has been broken and rebuilt MULTIPLE times. Small changes have catastrophic consequences.

---

## Complete Feature Registry

### 📰 Feed & Posts (WORKING - DO NOT BREAK)

| Feature | Location | State Variables | Props |
|---------|----------|----------------|-------|
| Post Feed | Lines 1669-1866 | `posts`, `feedOffset`, `hasMorePosts` | - |
| Post Card | Lines 1072-1300 | - | `onOpenArticle`, `onLike`, `onComment` |
| Create Post | Lines 850-1070 | `isPosting`, `postContent`, `media` | - |
| Infinite Scroll | Lines 1868-1919 | `loadingMore`, `feedCycle` | - |
| Like/React | Lines 2008-2020 | - | via PostCard |
| Delete Post | Lines 2022-2025 | - | - |

### 📖 Stories (WORKING - DO NOT BREAK)

| Feature | Location | State | Component |
|---------|----------|-------|-----------|
| Stories Bar | Line ~2330 | stories from API | `<StoriesBar />` |
| Story Viewer | External component | - | `StoriesBar` handles |

### 🎬 Reels (WORKING - DO NOT BREAK)

| Feature | Location | Notes |
|---------|----------|-------|
| Reels Carousel | Line ~2510 | Inserted after every 3 posts |
| Reel Viewer | External component | Opens full-screen |

### 📰 Article Reader (WORKING - DO NOT BREAK)

| Feature | Location | State | Dependencies |
|---------|----------|-------|--------------|
| Article Card | PostCard ~1186-1200 | - | onOpenArticle prop |
| Reader Modal | Line ~2509 | `articleReader: {open, url, title}` | ArticleReaderModal |
| In-App Display | - | - | `/api/proxy` route |

**Critical Flow:**
```
PostCard → onOpenArticle → setArticleReader → ArticleReaderModal
```

### 🔴 Live Streaming (WORKING - DO NOT BREAK)

| Feature | Location | State | Components |
|---------|----------|-------|------------|
| Go Live Button | Line ~2360 | `showGoLiveModal` | GoLiveModal |
| Live Stream Cards | Line ~2380 | `liveStreams` | LiveStreamCard |
| Stream Viewer | Line ~2400 | `watchingStream` | LiveStreamViewer |

### 💬 Chat Windows (WORKING - DO NOT BREAK)

| Feature | Location | State |
|---------|----------|-------|
| Chat Popups | Line ~2668 | `openChats`, `chatMsgs` |
| Send Message | Line 2119-2126 | via fn_send_message |

### 🔔 Notifications (WORKING - DO NOT BREAK)

| Feature | Location | State |
|---------|----------|-------|
| Notification Bell | Line ~2650 | `notifications` |
| Unread Count | Line ~2652-2653 | filtered from notifications |
| Mark as Read | Lines 1655-1667 | auto on dropdown open |

### 🔍 Search (WORKING - DO NOT BREAK)

| Feature | Location | State |
|---------|----------|-------|
| User Search | Lines 2041-2050 | `searchResults` |
| Global Search | Lines 2052-2098 | `globalSearchResults` |

---

## Verification Checklist

Before committing ANY change, manually verify:

- [ ] Feed loads (scroll should show posts)
- [ ] Posts have author avatar and name
- [ ] Posts with images show images
- [ ] Posts with videos show video player
- [ ] Posts with articles show proper thumbnail card
- [ ] Clicking article opens IN-APP modal (NOT new tab!)
- [ ] Like button works (updates count)
- [ ] Comment button opens comment section
- [ ] Stories bar shows at top
- [ ] Clicking story opens viewer
- [ ] Reels carousel appears between posts
- [ ] Notifications bell shows count
- [ ] Search finds users
- [ ] Chat windows open/work
- [ ] New post creation works

---

## Common Breakages & How to Avoid

### The Article Reader Pattern

```javascript
// ❌ BREAKS IT - removes the modal handler
<PostCard post={p} />

// ✅ CORRECT - passes the modal handler
<PostCard 
    post={p} 
    onOpenArticle={(url, title) => setArticleReader({ open: true, url, title })} 
/>
```

### The FK Join Pattern

```javascript
// ❌ BREAKS IT - crashes on null profile
const author = authorMap[post.author_id].username;

// ✅ CORRECT - handles null gracefully
const author = authorMap[post.author_id]?.username || 'Player';
```

### The Filter Pattern

```javascript
// ❌ BREAKS IT - silently removes items
items.filter(item => item.profile);

// ✅ CORRECT - keeps with fallback
items.map(item => ({ ...item, profile: item.profile || DEFAULT }));
```

---

## Files That Affect Social Media

```
pages/hub/social-media.js          # Main file - 2700+ lines
src/components/social/Stories.jsx   # Stories component
src/components/social/PostCard.jsx  # If extracted
src/components/social/ArticleReaderModal.jsx
src/services/UnifiedSocialService.js
services/MessagingService.js
src/stores/socialStore.js
pages/api/proxy.js                 # Article reader backend
```

---

## Before You Start

1. **Read the workflow**: `/social-feed-protection`
2. **Run tests**: `node scripts/test-article-reader.js`
3. **Understand the feature you're changing**: Read this skill
4. **Make minimal changes**: One feature at a time
5. **Test EVERYTHING**: Use the checklist above

---

## The Golden Rule

> **If you don't understand how a feature works, DON'T MODIFY IT.**
> 
> Ask questions first. Break nothing. Test everything.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/smarter-poker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
