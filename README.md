# HTML Comments Skill 💬

A **lightweight, zero-auth commenting skill** for HTML pages. Add collaborative feedback directly to your mockups, prototypes, or documentation using a browser bookmarklet. Perfect for design reviews, feedback collection, and iterative development.

## Features

✨ **Bookmarklet-based** — Works on any localhost HTML page  
💾 **File-based storage** — Comments saved as plain JSON (no database needed)  
🎯 **Element selection** — Click any element to attach feedback  
🔄 **AI-ready** — Export pending comments for Claude Code to apply changes  
📝 **Threaded replies** — Discuss changes in context before applying  
🎨 **Visual highlighting** — See which elements have comments  
📍 **Pin positions** — Comments follow elements during scroll/zoom  

## Quick Start

### 1. Install & Start

```bash
cd comments-skill
npm install
npm start
```

Server runs on `http://localhost:3001`

### 2. Serve Your HTML

In another terminal, serve your HTML files on localhost:8000:

```bash
# Using Python
python3 -m http.server 8000 --directory /path/to/your/html

# Or use any other local server
```

### 3. Install Bookmarklet

Open `http://localhost:3001/bookmarklet` and drag **"HTML Comments"** to your bookmarks bar.

### 4. Add Comments

1. Open any HTML page on localhost:8000
2. Click the bookmarklet
3. Click any element to comment
4. Type feedback and submit
5. See numbered pins appear on the page

## API Quick Reference

| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/api/comments?page_url=...` | Fetch comments for a page |
| POST | `/api/comments` | Create a comment |
| POST | `/api/comments/:id/reply` | Add reply to comment |
| PATCH | `/api/comments/:id` | Update status (for AI apply workflow) |
| DELETE | `/api/comments/:id` | Delete comment |

## Apply with Claude Code

Mark comments as **"pending-apply"** in the UI, then tell Claude Code:

```
Apply pending comments from pending-apply.json. Read the file,
apply each requested change to its HTML file, add AI replies to comments.json,
and clear pending-apply.json when done.
```

Claude will:
- Read `pending-apply.json` (auto-generated work queue)
- Edit HTML files on disk
- Update `comments.json` with AI replies
- Mark comments as ✓ applied

See [CLAUDE_CODE_INTEGRATION.md](comments-skill/CLAUDE_CODE_INTEGRATION.md) for details.

## File Structure

```
comments-skill/
├── server.js              # Express API server
├── inject.js              # Browser overlay & UI (~22KB)
├── comments.json          # All comments (auto-created)
├── pending-apply.json     # Work queue for Claude Code
├── config.json            # Configuration
├── package.json           # Dependencies
├── README.md              # Full documentation
├── QUICK_REFERENCE.md     # Quick lookup
└── CLAUDE_CODE_INTEGRATION.md  # AI apply workflow

test.html                 # Example/demo page
```

## Configuration

Edit `comments-skill/config.json`:

```json
{
  "project_root": "../",            // Path to HTML files
  "server_port": 3001,              // Server port
  "url_base": "http://localhost:8000", // Your local server URL
  "comments_file": "./comments.json" // Where comments are stored
}
```

## Storage

- **comments.json** — All comments with full history (append-only)
- **pending-apply.json** — Work queue (auto-managed, cleared after applying)

Both are plain JSON — edit directly if needed.

## Example Comment

```json
{
  "id": "c_1718450000_abc123",
  "page_url": "http://localhost:8000/test.html",
  "html_file_path": "./test.html",
  "element_selector": "#hero h1",
  "author": "Alice",
  "text": "Change font to Inter",
  "status": "open",
  "created_at": "2026-06-16T10:00:00Z",
  "replies": [
    {
      "id": "r_1718450100_xyz",
      "author": "Bob",
      "text": "Agreed, looks better",
      "status": "open"
    },
    {
      "id": "r_1718450200_ai",
      "author": "AI",
      "text": "Applied ✓ — Changed font-family to Inter",
      "status": "applied"
    }
  ]
}
```

## Workflow

1. **Add** — Click element, type feedback, submit
2. **Discuss** — Reply in context with team
3. **Mark** — Click "Mark Apply" when ready for AI
4. **Apply** — Tell Claude Code "apply pending comments"
5. **Done** — AI replies appear, changes live, status → ✓

## Why This Approach?

- **No database** — Plain JSON files, easy to version control
- **No auth** — Local team skill, no user management needed
- **No external API** — Claude Code applies changes locally
- **Portable** — Ship with your project, works anywhere
- **Transparent** — See exactly what's being applied

## Limitations

- Local/localhost only (not internet-facing)
- Single-process concurrency (file-based writes)
- No real-time sync across multiple users
- Node.js 14+ required

## Troubleshooting

**Server won't start?**
```bash
# Check port
lsof -i :3001

# Kill if stuck
kill -9 <PID>

# Start fresh
npm start
```

**Bookmarklet not loading?**
- Check browser console (F12)
- Verify `config.json` url_base matches your server
- Ensure CORS is enabled (it is by default)

**Comments not showing?**
- Refresh page
- Check `comments-skill/comments.json` exists
- Verify paths in config are correct

## Creator

**Osama Eldrieny**
- 🌐 [Website](https://www.osamaeldrieny.com/)
- 💼 [LinkedIn](https://www.linkedin.com/in/osamaeldrieny/)

## License

MIT

## See Also

- [Full Documentation](comments-skill/README.md)
- [Quick Reference](comments-skill/QUICK_REFERENCE.md)
- [Claude Code Integration](comments-skill/CLAUDE_CODE_INTEGRATION.md)
- [Setup Guide](comments-skill/SKILL_SETUP.md)
