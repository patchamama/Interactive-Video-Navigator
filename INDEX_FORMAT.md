# Index File Format Guide

This guide explains how to create index files for the Interactive Video Navigator.

## Supported Formats

The application supports flexible timestamp formats for maximum compatibility.

### Basic Format (Recommended)

The simplest format is:
```
[HH:MM:SS] Chapter title or description
```

### Example Index File

```markdown
# My Training Video Index

## PART 1: INTRODUCTION

[00:00:10] Welcome and overview
[00:02:30] Course objectives
[00:05:15] Prerequisites and requirements
[00:08:45] Development environment setup

## PART 2: CORE CONCEPTS

[00:12:00] Understanding the basics
[00:15:30] Key terminology
[00:18:20] Common patterns
[00:22:10] Best practices

⏸ COFFEE BREAK [00:25:00] - [00:30:00]

## PART 3: PRACTICAL EXAMPLES

[00:30:15] Example 1: Simple implementation
[00:35:40] Example 2: Advanced use case
[00:42:00] Example 3: Real-world scenario
[00:48:30] Common mistakes to avoid

## CONCLUSION

[00:52:00] Summary and recap
[00:55:00] Additional resources
[00:57:30] Q&A session
```

## Supported Timestamp Formats

The parser accepts multiple timestamp formats:

### With Brackets (Recommended)
```
[00:02:30] Chapter title
```

### With Parentheses
```
(00:02:30) Chapter title
```

### With Dash Separator
```
00:02:30 - Chapter title
```

### Without Delimiters
```
00:02:30 Chapter title
```

## Time Format

- **Format**: `HH:MM:SS` or `MM:SS`
- **Hours**: 00-99 (optional, can be single digit)
- **Minutes**: 00-59 (required)
- **Seconds**: 00-59 (required)

Examples:
- `[00:02:30]` - 2 minutes 30 seconds
- `[1:15:00]` - 1 hour 15 minutes
- `[2:30]` - 2 minutes 30 seconds (short format)

## Section Headers

The parser automatically detects section headers based on keywords:

### Automatic Detection
Lines starting with these keywords become sections:
- `PART` - e.g., "PART 1: INTRODUCTION"
- `CHAPTER` - e.g., "CHAPTER 2: ADVANCED TOPICS"
- `SECTION` - e.g., "SECTION 3: CONCLUSION"
- Numbered - e.g., "1. Getting Started"

### Markdown Headers
You can also use markdown headers:
```markdown
## PART 1: INTRODUCTION
### Subsection 1.1
```

## Break Markers

Mark breaks or pauses in your video:

```
⏸ LUNCH BREAK [02:17:30] - [03:08:40]
⏸ COFFEE BREAK [01:15:00] - [01:20:00]
BREAK [00:45:00] - [00:50:00]
```

Keywords that trigger break detection:
- `⏸` (pause symbol)
- `PAUSE`
- `BREAK`
- `LUNCH`

## Complete Example with All Features

```markdown
# Complete Training Course - 3 Hours

This is my comprehensive training on advanced topics.

## PART 1: FUNDAMENTALS

[00:00:00] Course introduction
[00:03:20] What we'll cover today
[00:06:45] Setting up your workspace
[00:10:15] Installing required tools
[00:15:30] First project setup

⏸ SHORT BREAK [00:20:00] - [00:25:00]

## PART 2: INTERMEDIATE CONCEPTS

[00:25:15] Understanding the architecture
[00:30:45] Component structure
[00:35:20] State management basics
[00:40:00] Handling user interactions
[00:45:30] Data flow patterns

## 2.1 Advanced Patterns

[00:50:00] Custom hooks implementation
[00:55:15] Performance optimization
[01:00:30] Memory management
[01:05:45] Testing strategies

⏸ LUNCH BREAK [01:10:00] - [01:55:00]

## PART 3: ADVANCED TOPICS

[01:55:15] Scalability considerations
[02:00:30] Security best practices
[02:05:45] Deployment strategies
[02:10:20] Monitoring and logging
[02:15:00] Error handling

## PART 4: REAL-WORLD EXAMPLES

[02:20:00] Building a complete application
[02:30:15] Integrating with APIs
[02:40:30] Database interactions
[02:50:00] Authentication implementation
[02:58:15] Final touches and polish

## CONCLUSION

[03:05:00] Course summary
[03:10:30] Next steps and resources
[03:15:00] Q&A session
```

## How to Use

### Option 1: Paste Content
1. Copy your index content
2. Paste into the "Index Content" textarea
3. Click "Load Configuration"

### Option 2: Upload File
1. Save your index as a `.txt`, `.md`, or `.index` file
2. Click "Upload Index File"
3. Select your file

### Option 3: Auto-Load (Web Server Only)
If running from a web server (`python3 -m http.server 8000`):
1. Save your index as `videoname.index` in the same folder as your video
2. Select your local video file
3. The index loads automatically

## Tips for Creating Good Indexes

### Be Descriptive
```
✓ Good: [00:15:30] Installing Node.js and npm on Windows
✗ Bad:  [00:15:30] Installation
```

### Use Consistent Formatting
```
✓ Good: All timestamps with brackets [HH:MM:SS]
✗ Bad:  Mix of [00:10:00] and (00:15:00) and 00:20:00
```

### Group Related Topics
```markdown
## DATABASE OPERATIONS

[01:00:00] Connecting to database
[01:05:30] Creating tables
[01:10:15] Insert operations
[01:15:00] Query optimization
```

### Mark Breaks Clearly
```
⏸ BREAK [02:30:00] - [02:45:00] - 15 minute break
```

## Integration with Subtitles

The index works alongside subtitles:
- Index shows chapter navigation
- Subtitles show spoken content
- Both sync with video playback
- Both searchable independently

## File Naming Convention

For automatic loading when using local videos:

```
myvideo.mp4          ← Your video file
myvideo.index        ← Index file (auto-loaded)
myvideo.de.srt       ← German subtitles (auto-loaded as Subtitle 1)
myvideo.srt          ← Default subtitles (auto-loaded as Subtitle 2)
myvideo.es.srt       ← Spanish subtitles (auto-loaded as Subtitle 3)
```

## Troubleshooting

### Timestamps Not Detected
- Ensure format is `[HH:MM:SS]` or `(HH:MM:SS)`
- Check that colons (`:`) are used, not periods (`.`)
- Make sure hours/minutes/seconds are properly padded (`00:02:30` not `0:2:30`)

### Sections Not Creating
- Check that section headers start with `PART`, `CHAPTER`, `SECTION`, or a number
- Use uppercase for better detection
- Ensure section header is on its own line

### Items Not Clickable
- Verify timestamp format is correct
- Check that the index has been loaded (click "Load Configuration")
- Look for errors in browser console (F12)

## Advanced: Programmatic Generation

You can generate index files programmatically:

```python
# Python example
def generate_index(chapters):
    with open('video.index', 'w') as f:
        f.write('# Auto-Generated Index\n\n')
        for section, items in chapters.items():
            f.write(f'## {section}\n\n')
            for timestamp, title in items:
                f.write(f'[{timestamp}] {title}\n')
            f.write('\n')

# Usage
chapters = {
    'PART 1: INTRO': [
        ('00:00:00', 'Welcome'),
        ('00:05:00', 'Overview'),
    ],
    'PART 2: CONTENT': [
        ('00:10:00', 'Main topic'),
        ('00:20:00', 'Examples'),
    ]
}

generate_index(chapters)
```

## See Also

- [HOW_TO_RUN.md](HOW_TO_RUN.md) - How to run the application
- [CLAUDE.md](CLAUDE.md) - Complete technical documentation
- [README.md](README.md) - Project overview
