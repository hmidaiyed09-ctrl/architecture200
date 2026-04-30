# Alverlika — AI Chat Interface

> Single-file AI chat application with multi-provider support, web search, Telegram integration, bookmarks, and persistent memory.

---

## 📁 File Structure

The entire application lives in **one file**: `index.html` (~2550 lines with comments).

```
index.html
├── HEAD (L1–998)
│   ├── External Libraries (L8–10)
│   └── CSS Styles (L11–997)
├── BODY HTML (L1000–1487)
│   ├── Background Layer (L1112–1123)
│   ├── Header Bar (L1125–1182)
│   ├── Sidebar (L1184–1230)
│   ├── Settings Modal (L1232–1378)
│   ├── Main Content (L1380–1420)
│   ├── Bookmark Panel (L1422–1441)
│   ├── Edit Modal (L1443–1473)
│   └── Footer (L1475–1478)
└── JAVASCRIPT (L1480–2554)
    ├── 1.  Config & Constants
    ├── 2.  State
    ├── 3.  System Prompt
    ├── 4.  Token & Cost
    ├── 5.  Storage
    ├── 6.  Memory
    ├── 7.  AI Calling
    ├── 8.  Search Functions
    ├── 9.  Tool-Calling Loop
    ├── 10. Telegram
    ├── 11. Bookmarks
    ├── 12. Actions
    ├── 13. Telegram View
    ├── 14. Rendering
    ├── 15. Typewriter
    ├── 16. Event Handlers
    └── 17. Init
```

> **Tip:** Each JS section is marked with `═══` dividers in the source. Use `Ctrl+F` → `═══` to jump between sections.

---

## 🔗 External Libraries

| Library | Purpose |
|---------|---------|
| **Tailwind CSS** (Browser build) | Utility-first CSS framework loaded at runtime |
| **marked.js** | Markdown → HTML parser for AI responses |
| **DOMPurify** | Sanitizes HTML output to prevent XSS attacks |

---

## 🎨 CSS Styles — Section Map

| Sub-section | Description | Related To |
|-------------|-------------|------------|
| **CSS Variables & Base** | `--background`, `--foreground` color vars, body styling | Used globally |
| **Scrollbar** | Custom dark scrollbar | All scrollable containers |
| **Keyframe Animations** | `fadeInUp`, `fadeIn`, `modalIn`, `pulse-dot`, `spin`, `orb1/2`, `telegramPulse`, `bookmark-flash` | Animation utility classes, background orbs, loading indicator |
| **Animation Classes** | `.animate-fade-in-up`, `.animate-fade-in`, `.bookmark-highlight` | Message rendering (§14), bookmark jump |
| **Input Fields** | `.input-field` — dark styled inputs/selects | Settings modal |
| **AI Markdown** | `.ai-markdown` — full markdown rendering styles (headings, code, blockquotes, tables, lists, images, links, hr) | `renderMarkdown()` (§14) |
| **Citation Links** | `.citation-link` — numbered source badges | `renderMarkdown()` source replacement |
| **Image Lightbox** | Full-screen image viewer overlay | `openImageLightbox()` (§14) |
| **YouTube Embeds** | `.yt-embed-card`, `.yt-player-overlay` — video cards & player | `renderMarkdown()`, `openYouTubePlayer()` |
| **Code Blocks** | `.code-block-wrapper`, `.copy-btn` | `renderMarkdown()` code block replacement, `copyCode()` |
| **Input Glow** | `.input-glow` focus/drag effects | `createInputHTML()` (§14) |
| **Bookmark Panel** | `.bookmark-panel` slide-in panel | `openBookmarkPanel()` (§11) |
| **Sidebar Search** | `.sidebar-search` input with icon | `handleSidebarSearch()` (§16) |
| **Memory Items** | `.memory-item`, `.memory-delete-btn` | `renderMemoryList()` (§14) |
| **Sidebar Tabs** | `.sidebar-tab` active states | `switchSidebarTab()` (§14) |
| **Pin Controls** | `.pin-btn`, `.pinned-indicator`, section headers | `renderSidebar()` (§14), `togglePinThread()` (§5) |
| **Message Actions** | `.msg-actions-bottom`, `.msg-actions-side`, `.msg-action-btn` | `createMessageHTML()` (§14) |
| **Edit Modal** | `.edit-modal` overlay | `openEditModal()` (§16) |
| **Plus Menu** | `.plus-menu` floating attachment menu | `togglePlusMenu()` (§16) |
| **Session Tokens** | `.session-tokens`, `.token-cost-badge` | `renderSessionTokenCounter()` (§4) |
| **Telegram Badge** | `.tg-msg-badge` | `renderTelegramList()` (§14), Telegram view (§13) |

---

## 🏗️ HTML Body — Element Map

### Header Bar
| Element ID | Purpose | Updated By (JS) |
|------------|---------|-----------------|
| `btn-sidebar` | Opens/closes sidebar | `openSidebar()` / `closeSidebar()` |
| `session-token-counter` | Shows session token/cost count | `renderSessionTokenCounter()` (§4) |
| `tg-header-status` | Telegram connection badge | `updateTelegramStatus()` (§10) |
| `memory-indicator-count` | Memory item count | `updateMemoryIndicator()` (§6) |
| `btn-new-chat` | Creates new conversation | `newChat()` (§12) |
| `btn-bookmarks` | Opens bookmark panel | `openBookmarkPanel()` (§11) |
| `header-bookmark-count` | Bookmark count badge | `updateHeaderBookmarkCount()` (§11) |
| `btn-settings` | Opens settings modal | `openSettings()` (§16) |

### Sidebar
| Element ID | Purpose | Updated By (JS) |
|------------|---------|-----------------|
| `sidebar-backdrop` | Click-to-close overlay | `openSidebar()` / `closeSidebar()` |
| `sidebar` | Slide-in panel | `openSidebar()` / `closeSidebar()` |
| `tab-chats` / `tab-telegram` / `tab-memory` | Tab buttons | `switchSidebarTab()` (§14) |
| `tg-tab-count` | Telegram message count | `updateTelegramTabCount()` (§10) |
| `memory-tab-count` | Memory item count | `updateMemoryIndicator()` (§6) |
| `sidebar-search-input` | Search conversations | `handleSidebarSearch()` (§16) |
| `thread-list` | Chat thread list container | `renderSidebar()` (§14) |
| `memory-list` | Source memory container | `renderMemoryList()` (§14) |
| `telegram-list` | Telegram messages container | `renderTelegramList()` (§14) |

### Settings Modal
| Element ID | Purpose | Saved To (state) |
|------------|---------|------------------|
| `setting-provider` | AI provider dropdown | `state.settings.provider` |
| `setting-model` | Model name input | `state.settings.model` |
| `setting-api-key` | API key input | `state.settings.apiKey` |
| `setting-base-url` | Custom provider URL | `state.settings.baseUrl` |
| `setting-serp-key` | SerpAPI key | `state.settings.serpApiKey` |
| `setting-tavily-key` | Tavily key | `state.settings.tavilyApiKey` |
| `setting-tg-token` | Telegram bot token | `state.settings.telegramToken` |
| `setting-tg-chat-id` | Telegram chat ID | `state.settings.telegramChatId` |
| `new-memory-input` | Add permanent memory | `state.permanentMemory` |
| `permanent-memory-list` | Memory list display | `renderPermanentMemoryList()` |
| `depth-light-btn` / `depth-deep-btn` | Search depth toggle | `state.settings.searchDepth` |
| `serp-status-badge` | SerpAPI status | `updateSettingsUI()` |
| `tavily-status-badge` | Tavily status | `updateSettingsUI()` |
| `tg-status-badge` | Telegram status | `updateSettingsUI()` |

### Main Content Area
| Element ID | Purpose | Updated By (JS) |
|------------|---------|-----------------|
| `hero-view` | Landing page with input + suggestions | `switchToHero()` / `switchToChat()` |
| `hero-input-wrapper` | Hero input container | `renderInputArea()` (§14) |
| `suggested-prompts` | Suggestion buttons | `renderSuggestedPrompts()` (§16) |
| `chat-view` | Active conversation view | `switchToChat()` / `switchToHero()` |
| `tg-view-banner` | Telegram history banner | `viewTelegramHistory()` / `exitTelegramView()` (§13) |
| `messages-scroll` | Scrollable messages wrapper | Scroll detection (§16) |
| `messages-list` | Message DOM container | `appendMessage()`, `renderAllMessages()` (§14) |
| `loading-indicator` | AI loading/search status | `renderLoadingIndicator()` (§14) |
| `scroll-anchor` | Auto-scroll target | `scrollToBottom()` (§15) |
| `chat-input-wrapper` | Chat mode input container | `renderInputArea()` (§14) |

### Bookmark Panel
| Element ID | Purpose | Updated By (JS) |
|------------|---------|-----------------|
| `bookmark-panel` | Slide-in bookmark list | `openBookmarkPanel()` / `closeBookmarkPanel()` |
| `bookmark-panel-count` | Bookmark count | `renderBookmarkPanel()` (§11) |
| `bookmark-panel-list` | Bookmark entries | `renderBookmarkPanel()` (§11) |

### Edit Modal
| Element ID | Purpose | Updated By (JS) |
|------------|---------|-----------------|
| `edit-modal` | Edit overlay | `openEditModal()` / `closeEditModal()` |
| `edit-textarea` | Edit text input | `openEditModal()`, `submitEditedMessage()` |

---

## ⚡ JavaScript — Section Deep Dive

### §1. Configuration & Constants

```
CORS_PROXIES[]        → Array of proxy URL builders for bypassing CORS
                        Used by: fetchWithProxies() (§8), performSearch() (§8)

DEPTH_LIMITS          → { 1: {searches:1, reads:3}, 3: {searches:2, reads:3} }
                        Used by: fetchWithToolCalling() (§9)

normalizeSearchDepth() → Clamps depth to 1 or 3
                        Used by: fetchWithToolCalling() (§9), createInputHTML() (§14)

AI_TOOLS[]            → Tool definitions for OpenAI function calling
                        Tools: web_search, read_url, youtube_search
                        Used by: fetchWithToolCalling() (§9)
```

### §2. State — ⭐ CENTRAL HUB

**Every section reads/writes `state`.** This is the single source of truth.

| Property | Type | Used By Sections |
|----------|------|-----------------|
| `messages` | Array | §7, §9, §11, §12, §14 |
| `settings.provider` | String | §7 (callAI), §5 (save/load) |
| `settings.apiKey` | String | §7 (callAI) |
| `settings.model` | String | §7 (callAI), §4 (pricing) |
| `settings.baseUrl` | String | §7 (callAI custom provider) |
| `settings.searchDepth` | Number | §9 (fetchWithToolCalling) |
| `settings.serpApiKey` | String | §8 (performSearch, searchYouTube) |
| `settings.tavilyApiKey` | String | §8 (performSearch) |
| `settings.telegramToken` | String | §10 (all Telegram functions) |
| `settings.telegramChatId` | String | §10 (all Telegram functions) |
| `settings.providerConfigs` | Object | §16 (provider switch saves/restores) |
| `threads` | Array | §5 (persistence), §12 (loadThread), §14 (renderSidebar) |
| `activeThreadId` | String | §5, §11, §12 |
| `isLoading` | Boolean | §12, §14, §16 |
| `searchStatus` | Object | §9, §14 (renderLoadingIndicator) |
| `abortController` | AbortController | §12 (stopGeneration, sendMessage) |
| `attachments` | Array | §14 (createInputHTML), §16 (processFiles) |
| `inputValue` | String | §14, §16 |
| `typewriter` | Object | §15 |
| `bookmarks` | Object | §11 (all bookmark functions) |
| `searchMemory` | Array | §6, §14 (renderMemoryList) |
| `sidebarTab` | String | §14 (switchSidebarTab) |
| `sessionTokens` | Object | §4 (token tracking) |
| `pinnedThreads` | Array | §5, §14 (renderSidebar) |
| `permanentMemory` | Array | §3 (system prompt), §5 (persistence) |
| `userScrolledUp` | Boolean | §15 (scrollToBottom) |
| `telegramMessages` | Array | §10, §13 |
| `telegramSearchMemory` | Array | §6, §10 |
| `telegramPollingTimer` | Timer | §10 |
| `telegramOffset` | Number | §10 |
| `telegramProcessing` | Boolean | §10 |
| `viewingTelegram` | Boolean | §13 |
| `savedWebState` | Object | §13 (saves web state when viewing TG) |

### §3. System Prompt

```
buildSystemPrompt()          → Web chat system prompt
                               Injects: state.permanentMemory
                               Called by: fetchWithToolCalling() (§9)

buildTelegramSystemPrompt()  → Telegram-specific system prompt (shorter)
                               Injects: state.permanentMemory
                               Called by: fetchWithToolCalling() (§9, isTelegram=true)
```

### §4. Token & Cost Tracking

```
estimateTokens(text)         → ~chars/4 estimation
MODEL_PRICING                → Per-model costs per 1M tokens
                               Keys: gpt-4o, gpt-4o-mini, llama3.1-8b, deepseek, default
getModelPricing(model)       → Looks up pricing by partial name match
formatCost(c) / formatTokens(t)  → Display formatters
updateSessionTokens(i,o,m)   → Accumulates into state.sessionTokens
renderSessionTokenCounter()  → Updates #session-token-counter in header
```

### §5. Storage / Persistence

| Function | localStorage Key | Data |
|----------|-----------------|------|
| `loadSettings()` / `saveSettings()` | `alv_settings` | `state.settings` |
| `loadThreads()` / `saveThreads()` | `alv_threads` | `state.threads` (auto-trims oldest on quota) |
| `loadBookmarks()` / `saveBookmarks()` | `alv_bookmarks` | `state.bookmarks` |
| `loadPinnedThreads()` / `savePinnedThreads()` | `alv_pinned` | `state.pinnedThreads` |
| `loadPermanentMemory()` / `savePermanentMemory()` | `alv_pmem` | `state.permanentMemory` |
| `loadTelegramMessages()` / `saveTelegramMessages()` | `alv_tg_msgs` | `state.telegramMessages` (halves on quota) |
| `loadTelegramOffset()` / `saveTelegramOffset()` | `alv_tg_offset` | `state.telegramOffset` |

**Helper:** `safeLSSet(key, value)` — wraps `localStorage.setItem` with try/catch, returns boolean.

**Thread management:**
- `saveCurrentThread()` — saves current messages to thread, auto-generates title on 2nd message
- `isThreadPinned(id)` / `togglePinThread(id)` — manages pinned threads

### §6. Memory

```
generateMemoryId()           → Creates unique ID like "mem_abc123_xyz789"
appendSearchMemory(entry)    → Pushes to searchMemory or telegramSearchMemory
                               Called by: fetchWithToolCalling() (§9) on each search/read
deleteMemoryItem(id)         → Removes from searchMemory, re-renders
clearAllMemory()             → Clears all searchMemory with confirm()
updateMemoryIndicator()      → Updates #memory-indicator-count and #memory-tab-count
```

### §7. AI Calling

```
callAI(settings, msgs, signal, tools)
  → Builds API URL based on provider:
      openai       → https://api.openai.com/v1/chat/completions
      cerebras     → https://api.cerebras.ai/v1/chat/completions
      pollinations → https://gen.pollinations.ai/v1/chat/completions
      custom       → settings.baseUrl + /chat/completions
  → Returns raw API JSON response
  → Called by: fetchWithToolCalling() (§9), generateThreadTitle() (§16)

parseAIResponse(data, inputText, model)
  → Extracts content, thinking (<think> blocks), calculates tokens
  → Returns: { id, role, content, thinking, tokens }
```

### §8. Search Functions

```
fetchWithProxies(url)        → Direct fetch → CORS proxy fallback chain
extractTextFromHtml(html)    → Strips scripts/nav, extracts main content
extractPageTitle(html)       → Gets <title> or <h1>

fetchUrlContent(url)         → 1. Jina Reader API (primary)
                               2. CORS proxies fallback
                               Returns: { content, title }

performSearch(query, n, serpKey, tavilyKey, onProgress)
  → Search cascade:  1. SerpAPI  →  2. Tavily  →  3. Jina Search (free fallback)
  → Returns: { sources[], allSnippets[] }

searchYouTube(query, serpKey) → SerpAPI YouTube engine only
  → Returns: video[] with { title, url, videoId, thumbnail, channel }
```

### §9. Tool-Calling Loop — ⭐ CORE ENGINE

```
fetchWithToolCalling(messages, settings, signal, options)
  → THE main AI interaction function, shared by web AND Telegram
  → options.isTelegram controls system prompt choice
  → Flow:
      1. Build system prompt + format messages
      2. Call AI with tools enabled
      3. Loop (max 8 rounds):
         - Process tool_calls:
             web_search     → performSearch()
             read_url       → fetchUrlContent()
             youtube_search → searchYouTube()
         - Append tool results to conversation
         - Call AI again with updated context
      4. Parse final response, attach deduplicated sources
  → Enforces DEPTH_LIMITS per search depth setting
  → Called by:
      sendMessage() (§12)              — web chat
      handleTelegramAIResponse() (§10) — Telegram
      regenerateResponse() (§16)       — message regeneration
```

### §10. Telegram

```
updateTelegramStatus()       → Shows "TG Online" badge in header
updateTelegramTabCount()     → Updates sidebar TG tab count
sendTelegramMessage(text)    → Splits into 4000-char chunks, sends via Bot API
startTelegramPolling()       → Starts 3-second polling interval
stopTelegramPolling()        → Clears polling timer
pollTelegram()               → Fetches updates, dispatches to handler
handleTelegramAIResponse()   → Calls fetchWithToolCalling(isTelegram=true)
detectTelegramChatId()       → Auto-detects chat ID
testTelegramBot()            → Sends test message
```

### §11. Bookmarks

```
isMessageBookmarked(id)      → Checks state.bookmarks[activeThreadId]
getThreadBookmarkCount(id)
updateHeaderBookmarkCount()  → Updates #header-bookmark-count badge
toggleBookmark(id)           → Add/remove message from bookmarks
openBookmarkPanel() / closeBookmarkPanel()
renderBookmarkPanel()        → Renders bookmark entries in panel
jumpToMessage(id)            → Scrolls to message, adds flash animation
```

### §12. Actions

```
stopGeneration()             → Aborts fetch, stops typewriter, resets loading
sendMessage(content, atts)   → Main send flow → fetchWithToolCalling → render
newChat()                    → Resets all state, switches to hero view
loadThread(id)               → Loads thread messages, renders all
deleteThread(id)             → Removes thread + bookmarks
```

### §13. Telegram View

```
viewTelegramHistory()        → Saves web state, renders TG messages in main area
exitTelegramView()           → Restores saved web state
clearTelegramHistory()       → Clears all TG messages with confirm()
```

### §14. Rendering — ⭐ LARGEST SECTION

```
escapeHtml(s) / escapeAttr(s) → XSS prevention helpers
renderMarkdown(text, sources) → marked + DOMPurify + citations + images + YouTube + code copy
createInputHTML(instanceId)   → Chat input (TWO instances: "hero" and "chat")
createMessageHTML(msg)        → User bubble or AI response with all UI
renderInputArea()             → Updates both input wrappers
switchToHero() / switchToChat()
appendMessage() / renderAllMessages()
renderLoadingIndicator()      → Search/thinking animation
renderSidebar()               → Thread list (pinned + unpinned)
renderMemoryList()            → Search memory items
renderTelegramList()          → Recent TG messages in sidebar
switchSidebarTab(tab)         → chats / telegram / memory
```

### §15. Typewriter & Scroll

```
startTypewriter(msgId, content) → Fade-in animation for AI response
scrollToBottom(force)           → Smart scroll (respects userScrolledUp)
forceScrollToBottom()           → Always scrolls
```

### §16. Event Handlers

```
handleInputChange / handleInputKeydown / handleSend / handleSuggestedPrompt
toggleSearchDepth / removeAttachment / processFiles
handleDrop / handleFileChange / handlePaste
showToast / openSidebar / closeSidebar / openSettings / closeSettings
updateSettingsUI / saveSettingsFromModal
addPermanentMemory / deletePermanentMemory / renderPermanentMemoryList
handleSidebarSearch / clearSidebarSearch
openEditModal / closeEditModal / submitEditedMessage
regenerateResponse / copyMessageContent / renameThread / generateThreadTitle
renderSuggestedPrompts / togglePlusMenu / closePlusMenu
```

### §17. Init

```
Execution order:
  1. Load all stored data (settings, threads, bookmarks, pinned, memory, TG)
  2. Render initial UI (prompts, input, counters, status)
  3. Start Telegram polling if configured
```

---

## 🔄 Data Flow Diagrams

### Web Chat Flow
```
User Input → handleSend() → sendMessage()
  → fetchWithToolCalling() [§9]
    → callAI() [§7] (with AI_TOOLS)
    → Loop: tool_calls?
      → web_search     → performSearch() [§8]
      → read_url       → fetchUrlContent() [§8]
      → youtube_search → searchYouTube() [§8]
      → callAI() again with tool results
    → parseAIResponse() [§7]
  → appendMessage() + startTypewriter() [§14, §15]
  → saveCurrentThread() [§5]
```

### Telegram Flow
```
pollTelegram() [every 3s] → new message?
  → handleTelegramAIResponse()
    → fetchWithToolCalling(isTelegram=true) [§9]
      → (same tool loop as web chat)
    → sendTelegramMessage() → Telegram Bot API
    → saveTelegramMessages() [§5]
```

### Provider Configuration Flow
```
Settings Modal → saveSettingsFromModal() [§16]
  → state.settings updated
  → saveSettings() [§5] → localStorage
  → stopTelegramPolling() / startTelegramPolling() [§10]
  → callAI() [§7] builds URL based on provider:
      openai       → api.openai.com
      cerebras     → api.cerebras.ai
      pollinations → gen.pollinations.ai (no key needed)
      custom       → user-provided baseUrl
```

---

## 🔧 How To: Add a New AI Provider

1. **Settings Modal HTML** — Add `<option>` to `#setting-provider` select
2. **State** — Add default config to `state.settings.providerConfigs`
3. **AI Calling `callAI()`** — Add `else if` for new provider URL
4. **Token Pricing `MODEL_PRICING`** — Add entry if needed
5. **Settings UI `updateSettingsUI()`** — Update if provider needs special fields

## 🔧 How To: Add a New Search Provider

1. **`performSearch()`** — Add a new try/catch block in the cascade (before Jina fallback)
2. **Settings Modal HTML** — Add key input field
3. **State** — Add key to `state.settings`
4. **`saveSettingsFromModal()`** — Collect new key from modal

## 🔧 How To: Add a New Tool

1. **`AI_TOOLS[]`** — Add tool definition object
2. **`fetchWithToolCalling()`** — Add `else if` handler for tool name
3. **System Prompt `buildSystemPrompt()`** — Document the tool in the prompt
4. **`DEPTH_LIMITS`** — Adjust limits if needed

---

## 💾 localStorage Keys Reference

| Key | Content | Max Size Handling |
|-----|---------|-------------------|
| `alv_settings` | Provider, keys, model, depth, TG config | None |
| `alv_threads` | All conversation threads + messages | Auto-trims oldest non-pinned threads |
| `alv_bookmarks` | Message bookmarks per thread | None |
| `alv_pinned` | Pinned thread IDs | None |
| `alv_pmem` | Permanent memory strings (max 20) | Capped at 20 items |
| `alv_tg_msgs` | Telegram message history | Halves on quota exceeded |
| `alv_tg_offset` | Telegram polling offset | None |
