# CLAUDE.md - Interactive Video Navigator

## Project Overview

**Interactive Video Navigator** is a single-page web application designed to provide enhanced navigation for video content (primarily YouTube) through intelligent indexing and subtitle search capabilities. The application allows users to:

- Navigate videos using timestamped indexes
- Upload index files (.txt, .index, .md) directly
- Load up to 3 subtitle files (SRT) simultaneously
- View live synchronized subtitles while video plays
- Toggle individual subtitle tracks on/off
- Search within video subtitles (SRT files)
- Jump to specific video moments with one click
- Organize content into collapsible sections
- View break/pause markers in training videos

### Primary Use Case
Originally created for navigating long training videos (e.g., 9+ hour ELO training sessions) with detailed timestamps and multilingual content.

### Key Features (Updated 2025-11-17)

#### Multiple Subtitle Support
- **Up to 3 simultaneous subtitle tracks** - Perfect for multilingual learning
- **Live synchronization** - Subtitles update automatically as video plays
- **Individual visibility control** - Toggle each subtitle track on/off independently
- **Color-coded display** - Each subtitle has a distinct color (blue, purple, green)
- **Real-time updates** - Synchronized every 500ms with video playback

#### Index File Upload
- **Direct file upload** - No need to copy/paste index content
- **Multiple formats** - Supports .txt, .index, and .md files
- **Instant loading** - Automatically populates index textarea

---

## Repository Structure

```
Interactive-Video-Navigator/
├── Interactive_Video_Navigator.html    # Main application (standalone HTML)
├── README.md                           # Example index content (ELO training)
├── todo.txt                            # Feature requests and roadmap (Spanish)
├── CLAUDE.md                          # This file
└── .git/                              # Git repository
```

### File Descriptions

#### `Interactive_Video_Navigator.html` (~1130 lines)
- **Purpose**: Complete standalone web application
- **Architecture**: Monolithic HTML file with embedded CSS and JavaScript
- **Dependencies**: External CDNs only (Bootstrap 5.3, Bootstrap Icons, YouTube iframe API)
- **No build process required** - open directly in browser
- **Current Version**: 2.0.0 (displayed in header and console)

#### `README.md`
- Serves as example content and documentation
- Contains detailed 9+ hour training video index
- Demonstrates index format with timestamps in multiple languages (EN, ES, DE)
- Format: Markdown with timestamps in `[HH:MM:SS]` format

#### `todo.txt`
- Feature requests and improvement ideas (Spanish)
- Future development roadmap
- Includes AI integration ideas (summaries, quizzes, PDF parsing)

---

## Technical Architecture

### Technology Stack

| Component | Technology | Version | Notes |
|-----------|-----------|---------|-------|
| UI Framework | Bootstrap | 5.3.0 | Via CDN |
| Icons | Bootstrap Icons | 1.10.0 | Via CDN |
| Video API | YouTube iframe API | Latest | Dynamically loaded |
| JavaScript | Vanilla ES6+ | N/A | No framework dependencies |
| CSS | Custom + Bootstrap | N/A | Gradient-based design |

### Key Design Patterns

1. **Single File Application**
   - Zero build process
   - All code in one HTML file
   - Portable and self-contained

2. **State Management**
   - Simple global variables in JavaScript
   - State variables: `player`, `indexItems`, `srtData`, `currentVideoId`

3. **Event-Driven Architecture**
   - DOM event listeners for user interactions
   - YouTube postMessage API for video control

4. **Functional Programming**
   - Pure utility functions for parsing and formatting
   - No classes, primarily functional approach

---

## Code Organization

### JavaScript Module Structure

The JavaScript code is organized into logical sections:

```javascript
// ============================================
// DEFAULT CONFIGURATION
// ============================================
const DEFAULT_CONFIG = { ... }

// ============================================
// APPLICATION STATE
// ============================================
let player, indexItems, srtData, currentVideoId
let srtData1, srtData2, srtData3  // Multiple subtitle tracks
let subtitleSyncInterval           // Subtitle synchronization timer

// ============================================
// UTILITY FUNCTIONS
// ============================================
getYouTubeVideoId(), timestampToSeconds(), parseIndex(), parseSRT()

// ============================================
// VIDEO PLAYER MANAGEMENT
// ============================================
loadYouTubePlayer(), seekToTime(), updateActiveItem()

// ============================================
// UI RENDERING
// ============================================
renderIndex(), renderSearchResults(), toggleSection()

// ============================================
// EVENT HANDLERS
// ============================================
loadConfiguration(), resetConfiguration(), handleSRTUpload()

// ============================================
// INITIALIZATION
// ============================================
DOMContentLoaded event listener
```

### Key Functions Reference

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `getYouTubeVideoId(url)` | Extract video ID from YouTube URL | URL string | Video ID (11 chars) or null |
| `timestampToSeconds(timestamp)` | Convert HH:MM:SS to seconds | "01:23:45" | 5025 |
| `secondsToTimestamp(seconds)` | Convert seconds to HH:MM:SS | 5025 | "01:23:45" |
| `parseIndex(content)` | Parse markdown index into structured data | Text content | Array of index items |
| `parseSRT(content)` | Parse SRT subtitle format | SRT text | Array of subtitle objects |
| `seekToTime(seconds)` | Navigate video to timestamp | Seconds (number) | void |
| `renderIndex()` | Display index items in UI | None | void |
| `searchSubtitles(term)` | Search within loaded subtitles | Search string | void (updates UI) |

---

## Data Structures

### Index Item Object
```javascript
{
  type: 'item',           // 'item' | 'section' | 'break'
  timestamp: "01:23:45",  // Original timestamp string
  time: 5025,             // Seconds (for seeking)
  text: "Description",    // Display text
  id: "item-12",          // Unique identifier
  section: "PART 1"       // Parent section (if any)
}
```

### Section Object
```javascript
{
  type: 'section',
  title: "PART 1: INTRODUCTION",
  items: [],              // Child items
  id: "section-0"
}
```

### Subtitle Object (SRT)
```javascript
{
  time: 125,              // Seconds
  timestamp: "00:02:05",  // HH:MM:SS
  text: "Subtitle text"   // Caption content
}
```

---

## Index Format Specification

### Supported Formats

The parser supports flexible timestamp formats:

```markdown
# Section headers (detected by keywords)
PART 1: INTRODUCTION (00:02:00 - 01:10:00)
CHAPTER 2: Advanced Topics
SECTION 3: Conclusion

# Index items with timestamps
- [00:02:00] Item with square brackets
- (00:02:00) Item with parentheses
- 00:02:00 - Item with dash separator
[00:02:00] Item without leading dash

# Break markers
⏸ LUNCH BREAK (02:17:30 - 03:08:40) - ~51 minutes
⏸ SHORT BREAK [04:36:47] - [04:39:00]
```

### Timestamp Format
- **Format**: `HH:MM:SS` or `MM:SS`
- **Delimiters**: Square brackets `[]`, parentheses `()`, or none
- **Range**: 00:00:00 to 99:59:59

### Section Detection
Sections are detected by patterns:
- Starting with: `PART`, `CHAPTER`, `SECTION`
- Starting with: Digit followed by period (e.g., `1. Introduction`)

---

## SRT (Subtitle) Format

### Standard SRT Structure
```
1
00:00:02,000 --> 00:00:05,500
Welcome to the training session

2
00:00:06,000 --> 00:00:09,200
Today we'll cover installation procedures
```

### Parser Behavior
- Ignores sequence numbers
- Extracts start time (ignores end time)
- Combines multi-line subtitles into single text
- Converts comma milliseconds to periods
- Truncates to HH:MM:SS (drops milliseconds)

---

## Multiple Subtitle Support (Added 2025-11-17)

### Overview
The application now supports loading and displaying up to **3 simultaneous subtitle tracks**. This is perfect for:
- Multilingual video content (e.g., English, Spanish, German)
- Comparing different translations
- Learning languages by viewing multiple subtitles
- Professional training with multi-language support

### How It Works

#### Loading Subtitles
1. Use the **3 file upload inputs** in the control panel
2. Each input accepts `.srt` format files
3. Subtitles load independently - you can load 1, 2, or all 3
4. Each subtitle track is stored separately: `srtData1`, `srtData2`, `srtData3`

#### Live Display
- Subtitles appear **below the video player** in color-coded boxes
- **Subtitle 1**: Blue gradient (`#e3f2fd` to `#bbdefb`)
- **Subtitle 2**: Purple gradient (`#f3e5f5` to `#e1bee7`)
- **Subtitle 3**: Green gradient (`#e8f5e9` to `#c8e6c9`)

#### Synchronization
- **Update frequency**: Every 500ms
- **YouTube API integration**: Uses `postMessage` to get current playback time
- **Smart matching**: Finds subtitle with 2-second time window
- **Automatic updates**: No manual refresh needed

#### Visibility Controls
- **Individual checkboxes** for each subtitle track
- Check/uncheck to show/hide specific subtitles
- Changes apply immediately without reloading
- Subtitle boxes only appear when files are loaded

### Data Structure (Multiple Subtitles)

```javascript
// Global state variables
let srtData1 = [];  // First subtitle track
let srtData2 = [];  // Second subtitle track
let srtData3 = [];  // Third subtitle track
let srtData = [];   // Backward compatibility (points to srtData1)

// Subtitle entry structure (same for all tracks)
{
  time: 125,              // Seconds
  timestamp: "00:02:05",  // HH:MM:SS
  text: "Subtitle text"   // Caption content
}
```

### Key Functions

| Function | Purpose | Parameters |
|----------|---------|------------|
| `handleSRTUpload(fileInputId, subtitleNumber)` | Upload handler factory | Returns function for specific subtitle |
| `updateLiveSubtitleVisibility()` | Show/hide subtitle boxes | None (reads checkboxes) |
| `getCurrentSubtitle(srtArray, currentTime)` | Find active subtitle | Array, time in seconds |
| `updateLiveSubtitles(currentTime)` | Update all subtitle displays | Current video time |
| `startSubtitleSync()` | Begin time tracking | None |
| `stopSubtitleSync()` | Stop synchronization | None |

### CSS Classes (Subtitle Display)

```css
.live-subtitle-box        /* Container for subtitle */
.subtitle-label           /* Label with animated indicator */
.subtitle-text            /* Actual subtitle content */
#liveSub1, #liveSub2, #liveSub3  /* Individual colored boxes */
```

### Usage Example

```javascript
// User loads 3 SRT files via UI
// System automatically:
1. Parses each SRT file
2. Stores in srtData1, srtData2, srtData3
3. Shows visibility checkboxes
4. Starts synchronization when video loads
5. Updates subtitle text every 500ms

// User can:
- Toggle visibility with checkboxes
- See all 3 subtitles simultaneously
- Search within any loaded subtitle
```

### Implementation Notes

**YouTube API Message Handling:**
```javascript
window.addEventListener('message', function(event) {
    if (event.origin !== 'https://www.youtube.com') return;
    const data = JSON.parse(event.data);
    if (data.info && data.info.currentTime) {
        updateLiveSubtitles(data.info.currentTime);
    }
});
```

**Interval Management:**
```javascript
// Request current time every 500ms
subtitleSyncInterval = setInterval(() => {
    iframe.contentWindow.postMessage(JSON.stringify({
        event: 'command',
        func: 'getCurrentTime',
        args: []
    }), '*');
}, 500);
```

**Smart Subtitle Matching:**
```javascript
// Find subtitle within 2-second window
const subtitle = srtArray.find(sub => {
    return currentTime >= sub.time &&
           currentTime < (sub.time + 2);
});
```

### Best Practices

✅ **DO:**
- Load subtitles in order of priority (1 = primary language)
- Use consistent SRT format for all files
- Test synchronization with actual video content
- Keep subtitle text concise for readability

❌ **DON'T:**
- Load extremely large SRT files (>10,000 entries)
- Use different timestamp formats between files
- Forget to start video after loading subtitles
- Mix SRT encoding formats (stick to UTF-8)

### Troubleshooting

**Subtitles not appearing:**
- Check that video is playing (iframe loaded)
- Verify SRT files loaded successfully (check stats)
- Ensure checkboxes are checked
- Look for console errors

**Subtitles out of sync:**
- Verify SRT timestamps match video timing
- Check if YouTube API is loaded
- Ensure 2-second window is appropriate
- Consider adjusting sync interval (currently 500ms)

**Multiple subtitles overlapping:**
- This is intentional - all visible subtitles show
- Use checkboxes to hide unwanted tracks
- Adjust CSS spacing if needed (`.live-subtitle-box` margin)

---

## Styling and UI Conventions

### Color Scheme
```css
Primary Gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%)
Background: Purple gradient
Cards: White with rounded corners
Hover: Light gray (#f8f9fa)
Active: Light blue (#e7f3ff)
Break Items: Yellow gradient (#ffeaa7 to #fdcb6e)
Search Results: Light yellow (#fff3cd)
```

### CSS Class Conventions
- `.index-item` - Clickable timestamp items
- `.index-item.active` - Currently playing item
- `.break-item` - Break/pause markers
- `.section-header` - Collapsible section headers
- `.section-content` - Section body (can be collapsed)
- `.search-result` - Subtitle search results
- `.timestamp` - Timestamp display (monospace font)
- `.stats-badge` - Statistics pills

### Responsive Design
- Bootstrap grid system (`.col-lg-5` / `.col-lg-7`)
- Video: 5 columns on large screens, full width on mobile
- Index: 7 columns on large screens, full width on mobile
- Sticky video player on scroll

---

## Development Workflows

### Version Management

**Current Version**: 2.0.0

The application version is managed in two places:

1. **JavaScript constant** (line ~432):
   ```javascript
   const APP_VERSION = '2.0.0';
   ```

2. **Header display** (line ~281):
   ```html
   <small class="opacity-75" style="font-size: 0.75rem;">v2.0.0</small>
   ```

**To update the version:**
1. Change `APP_VERSION` constant
2. Update version in header HTML
3. Update CLAUDE.md (this file)
4. Commit with semantic version message

**Version format**: Use [Semantic Versioning](https://semver.org/)
- **Major** (X.0.0): Breaking changes, major new features
- **Minor** (1.X.0): New features, backward compatible
- **Patch** (1.0.X): Bug fixes, minor improvements

**Console output**: Version displays automatically on page load with styled console message.

### Making Code Changes

1. **Edit the HTML file directly**
   ```bash
   # Open in your editor
   vim Interactive_Video_Navigator.html
   # Or use your preferred editor
   ```

2. **Test locally**
   ```bash
   # Open in browser (no server needed)
   open Interactive_Video_Navigator.html
   # Or double-click the file
   ```

3. **No build process required**
   - Changes are immediately visible
   - Refresh browser to see updates

### Testing Strategy

**Manual Testing Checklist:**
- [ ] Load default configuration (Reset button)
- [ ] Load custom YouTube URL
- [ ] Parse custom index content
- [ ] Upload SRT file
- [ ] Search in subtitles
- [ ] Click timestamp navigation
- [ ] Verify video seeking
- [ ] Test section collapse/expand
- [ ] Check responsive layout (mobile/desktop)
- [ ] Verify active item highlighting

**Browser Compatibility:**
- Chrome/Edge: Primary target
- Firefox: Fully supported
- Safari: Should work (test iframe API)
- Mobile browsers: Responsive design

### Git Workflow

```bash
# Check status
git status

# Create feature branch
git checkout -b feature/new-functionality

# Make changes and commit
git add Interactive_Video_Navigator.html
git commit -m "Add: Description of feature"

# Push to remote
git push -u origin feature/new-functionality
```

### Commit Message Conventions

Use semantic prefixes:
- `Add:` - New feature
- `Fix:` - Bug fix
- `Update:` - Enhance existing feature
- `Refactor:` - Code restructuring
- `Docs:` - Documentation only
- `Style:` - Formatting, no code change

---

## AI Assistant Guidelines

### When Adding Features

1. **Preserve the monolithic structure**
   - Keep everything in one HTML file
   - Avoid creating separate JS/CSS files
   - Maintain zero build process requirement

2. **Follow existing code style**
   - Use section comment headers (`// ====...====`)
   - Group related functions together
   - Use descriptive function names
   - Add JSDoc comments for complex functions

3. **Maintain backward compatibility**
   - Don't break existing index format
   - Support both old and new timestamp formats
   - Preserve default configuration

4. **Consider performance**
   - App handles 9+ hour videos with 200+ index items
   - Search must be responsive (<100ms)
   - Avoid heavy DOM operations

5. **Test with the ELO example**
   - README.md contains real-world test data
   - Verify all timestamps parse correctly
   - Check multilingual content handling

### Code Modification Patterns

#### Adding a New Feature
```javascript
// 1. Add to appropriate section
// ============================================
// YOUR NEW SECTION
// ============================================

/**
 * Your function with JSDoc comment
 */
function yourNewFunction(param) {
    // Implementation
}

// 2. Add UI element in HTML section
// Find appropriate location in template

// 3. Wire up event listener in INITIALIZATION section
document.getElementById('yourButton').addEventListener('click', yourNewFunction);
```

#### Modifying Parser Logic
```javascript
// Always preserve backward compatibility
function parseIndex(content) {
    // Add new logic AFTER existing patterns
    // Don't remove old pattern matching
    // Add feature flags if needed
}
```

#### Adding New Styling
```css
/* Add to <style> section */
/* Follow naming conventions */
.your-new-class {
    /* Use existing color scheme */
    /* Maintain responsive design */
}
```

### Common Modification Scenarios

#### 1. Adding Vimeo Support (from todo.txt)
```javascript
// In UTILITY FUNCTIONS section
function getVimeoVideoId(url) {
    // Extract Vimeo ID
}

function loadVimeoPlayer(videoId) {
    // Implement Vimeo iframe
}

// In loadConfiguration()
// Detect video source and call appropriate loader
```

#### 2. Adding Local Video Support
```javascript
// Add file input for video upload
<input type="file" id="localVideo" accept="video/*">

// In VIDEO PLAYER MANAGEMENT
function loadLocalVideo(file) {
    const videoURL = URL.createObjectURL(file);
    // Use HTML5 <video> element
}
```

#### 3. Implementing Index File Upload
```javascript
// Add file input
<input type="file" id="indexFile" accept=".txt,.md">

// Handler
function handleIndexUpload(event) {
    const file = event.target.files[0];
    const reader = new FileReader();
    reader.onload = (e) => {
        document.getElementById('indexContent').value = e.target.result;
    };
    reader.readAsText(file);
}
```

#### 4. Adding Live Subtitle Display
```javascript
// Add checkbox in UI
<input type="checkbox" id="liveSubtitles">
<div id="currentSubtitle"></div>

// In seekToTime or on timeupdate
function updateLiveSubtitle(currentTime) {
    if (!document.getElementById('liveSubtitles').checked) return;
    const subtitle = srtData.find(s =>
        s.time <= currentTime && s.time + 5 > currentTime
    );
    if (subtitle) {
        document.getElementById('currentSubtitle').textContent = subtitle.text;
    }
}
```

### Important Constraints

#### DO NOT:
- ❌ Split into multiple files (remains single HTML)
- ❌ Add npm packages or build tools
- ❌ Break existing YouTube URL parsing
- ❌ Remove default configuration
- ❌ Change timestamp format requirements
- ❌ Add backend dependencies (remains client-side only)

#### DO:
- ✅ Add features inline in the HTML file
- ✅ Use CDN libraries if absolutely necessary
- ✅ Maintain backward compatibility
- ✅ Add progressive enhancements
- ✅ Test with README.md example content
- ✅ Keep performance in mind (large videos)
- ✅ Follow existing code organization patterns
- ✅ Document complex logic with comments

---

## Configuration and Defaults

### Default Configuration Object
```javascript
const DEFAULT_CONFIG = {
    videoUrl: 'https://www.youtube.com/watch?v=ZT9FunQCcSI',
    indexContent: `PART 1: INTRODUCTION...`,
    srtPath: '' // Optional
};
```

### Customizing Defaults
Edit the `DEFAULT_CONFIG` object in the HTML file (line ~298):

```javascript
const DEFAULT_CONFIG = {
    videoUrl: 'YOUR_DEFAULT_VIDEO_URL',
    indexContent: `YOUR_DEFAULT_INDEX`,
    srtPath: 'path/to/default.srt' // If using local SRT
};
```

### User Configuration Storage
Currently: No persistence (resets on page reload)

**Future Enhancement** (from todo.txt):
- Add localStorage for saving configurations
- Implement "bundle" system for multiple video configs
- Allow importing/exporting configurations

---

## Performance Optimizations

### Passive Event Listeners (Added 2025-11-17)
The application automatically marks scroll-blocking events as passive to improve scrolling performance and eliminate browser warnings.

**Implementation** (lines 737-767):
```javascript
// Patches addEventListener to add { passive: true } to:
// - touchstart, touchmove
// - wheel, mousewheel

// Before: Browser warning about non-passive listeners
// After: Smooth scrolling, no warnings
```

**Why this matters:**
- Eliminates console warning: `[Violation] Added non-passive event listener to a scroll-blocking event`
- Improves scroll performance on touch devices
- Allows browser to optimize scrolling without waiting for `preventDefault()` checks
- Benefits Bootstrap and other third-party libraries automatically

**Technical details:**
- Polyfill runs before DOMContentLoaded
- Patches `EventTarget.prototype.addEventListener` globally
- Only affects touch and wheel events
- Preserves explicit `passive: false` if needed

## Troubleshooting

### Common Issues

#### Passive Event Listener Warnings
**Symptoms**: Console warning about non-passive event listeners
**Fixed**: Automatic passive event listener polyfill (lines 737-767)
**Status**: Resolved as of 2025-11-17

If you still see warnings:
- Check browser console for specific event type
- Verify polyfill is loading (should be before DOMContentLoaded)
- Check if third-party scripts load before the polyfill

#### Video Not Playing
**Symptoms**: Video doesn't load or shows errors
**Causes**:
1. Invalid YouTube URL
2. Video is private/restricted
3. Browser blocking YouTube iframe
4. Missing API permissions

**Solutions**:
- Check browser console for errors
- Verify video is public
- Test with default video URL
- Check CORS/iframe policies

#### Timestamps Not Working
**Symptoms**: Clicking timestamps doesn't seek video
**Causes**:
1. YouTube API not loaded
2. postMessage blocked
3. Invalid timestamp format
4. iframe not ready

**Solutions**:
- Check console for YouTube API errors
- Verify iframe has `enablejsapi=1` parameter
- Check timestamp format (HH:MM:SS)
- Wait for iframe to fully load

#### Index Not Parsing
**Symptoms**: "No index items found" message
**Causes**:
1. Invalid timestamp format
2. Missing or malformed content
3. Parser regex mismatch

**Solutions**:
- Verify timestamp format: `[HH:MM:SS]` or `(HH:MM:SS)`
- Check section headers match patterns
- Test with default index (README.md)
- Look for console errors

#### SRT Search Not Working
**Symptoms**: Search returns no results
**Causes**:
1. SRT file not uploaded
2. Invalid SRT format
3. Character encoding issues
4. Empty search term

**Solutions**:
- Verify SRT file uploaded successfully
- Check SRT format (sequence, timestamps, text)
- Ensure UTF-8 encoding
- Try default search terms from content

---

## Performance Considerations

### Optimization Strategies

1. **Large Index Files**
   - Virtual scrolling for 500+ items (not implemented)
   - Currently handles ~200 items efficiently
   - Consider pagination for very large indexes

2. **SRT Search**
   - Linear search is O(n)
   - Works well for typical subtitle files (<5000 entries)
   - Consider indexing for very large SRT files

3. **DOM Updates**
   - Uses innerHTML for batch updates
   - Minimize reflows during rendering
   - Debounce search input (not implemented)

4. **Video Loading**
   - Lazy load YouTube API
   - Only one video instance at a time
   - No preloading of video content

### Performance Metrics
- Initial load: <500ms (depends on CDN)
- Index parsing: <50ms for 200 items
- Search: <100ms for 5000 subtitles
- Seek operation: <200ms (YouTube API delay)

---

## Future Enhancements (from todo.txt)

### Priority 1: Core Functionality
1. **Fix video playback issues** (check console errors)
2. **Add local video support** (not just YouTube)
3. ✅ **Index file upload** - COMPLETED (2025-11-17)
4. ✅ **Live subtitle display** - COMPLETED (2025-11-17)
   - Supports up to 3 simultaneous subtitle tracks
   - Individual visibility controls with checkboxes
   - Color-coded synchronized display

### Priority 2: Bundle System
5. **Configuration bundles**
   - Multiple video configurations
   - Search across all indexes/subtitles
   - Import/export bundles

### Priority 3: Platform Support
6. **Vimeo and other sources**
   - Vimeo iframe support
   - Generic video player fallback
   - Local file playback

### Priority 4: AI Features
7. **Video summarization** (with AI)
8. **Interactive Q&A** (ask questions about content)
9. **Quiz generation** (from video content)
   - Topic-specific questions
   - Multiple language support
   - Based on watched sections

### Priority 5: Advanced Features
10. **Auto-load associated files** (index + SRT)
    - Checkbox for auto-loading
    - Convention-based file naming
    - Same directory as video

11. **PDF integration** (advanced)
    - Parse PDF index
    - Link video timestamps to PDF sections
    - Pre-process with AI (Gemini/free AI)
    - Save parsed relationships

---

## API Reference

### Global Functions (window scope)

```javascript
// Toggle section expansion
toggleSection(sectionId: string): void

// Seek video to timestamp
seekToTime(seconds: number): void
```

### YouTube iframe API Integration

```javascript
// Seek command via postMessage
iframe.contentWindow.postMessage(JSON.stringify({
    event: 'command',
    func: 'seekTo',
    args: [seconds, true]
}), '*');
```

### Browser APIs Used
- `FileReader` - For SRT/index file uploads
- `localStorage` - Not yet implemented (planned)
- `postMessage` - YouTube iframe control
- `scrollIntoView` - Smooth scrolling
- `setTimeout` - Debouncing alerts

---

## Accessibility Considerations

### Current Implementation
- ✅ Semantic HTML structure
- ✅ Keyboard navigation (partial - clickable divs)
- ✅ ARIA labels on icons
- ✅ Readable color contrast
- ⚠️ No screen reader optimization
- ⚠️ Limited keyboard shortcuts

### Improvements Needed
- Add ARIA landmarks
- Keyboard shortcuts for seek (←/→ arrows)
- Focus management
- Screen reader announcements
- Skip links for navigation

---

## Security Considerations

### Current Security Posture
- ✅ Client-side only (no server vulnerabilities)
- ✅ No user data storage
- ✅ CDN integrity could be improved
- ⚠️ No XSS protection on user input
- ⚠️ No CSP headers

### Recommendations for Production

1. **Add Subresource Integrity (SRI)**
   ```html
   <link href="..." integrity="sha384-..." crossorigin="anonymous">
   ```

2. **Sanitize User Input**
   - Escape HTML in index text
   - Validate timestamp formats
   - Sanitize search terms

3. **Content Security Policy**
   ```html
   <meta http-equiv="Content-Security-Policy"
         content="default-src 'self'; script-src 'self' https://cdn.jsdelivr.net">
   ```

4. **YouTube Embed Security**
   - Already uses `rel=0` (no related videos from other channels)
   - Consider `origin` parameter for stricter control

---

## Browser DevTools Tips

### Useful Console Commands

```javascript
// Inspect current state
console.log('Index items:', indexItems);
console.log('SRT data:', srtData);
console.log('Current video:', currentVideoId);

// Test timestamp parsing
timestampToSeconds('01:23:45')  // Returns: 5025
secondsToTimestamp(5025)        // Returns: "01:23:45"

// Test index parsing
parseIndex(`[00:01:00] Test item\n[00:02:00] Another item`)

// Test seeking
seekToTime(120)  // Jump to 2:00

// Toggle all sections
document.querySelectorAll('.section-header').forEach(h => h.click())
```

### Debugging Video Issues

```javascript
// Check YouTube iframe
const iframe = document.getElementById('ytplayer');
console.log('Iframe:', iframe);
console.log('Iframe src:', iframe?.src);

// Test postMessage
iframe.contentWindow.postMessage(JSON.stringify({
    event: 'command',
    func: 'getCurrentTime',
    args: []
}), '*');
```

---

## License and Attribution

### Current Status
- No explicit license file
- Open source implied (public repository)
- No copyright notices

### Recommendations
- Add LICENSE file (MIT, Apache 2.0, or GPL)
- Add copyright headers
- Document third-party dependencies:
  - Bootstrap 5.3.0 (MIT License)
  - Bootstrap Icons (MIT License)
  - YouTube iframe API (YouTube ToS)

---

## Contact and Support

### Repository Information
- **Repository**: Interactive-Video-Navigator
- **Platform**: Git
- **Branch**: `claude/claude-md-mi3o2xrzfomp2zvp-01XEmis2rytUR85oeZV6xvh6`

### Getting Help
1. Check this CLAUDE.md file
2. Review inline code comments
3. Test with default configuration (README.md content)
4. Check browser console for errors
5. Review todo.txt for known issues

---

## Quick Reference

### File Locations
| Item | Location |
|------|----------|
| Main app | `Interactive_Video_Navigator.html` |
| Example content | `README.md` |
| Feature requests | `todo.txt` |
| This guide | `CLAUDE.md` |

### Key Line Numbers (Interactive_Video_Navigator.html)
| Feature | Lines |
|---------|-------|
| Styles | 13-266 |
| Live subtitle styling | 201-265 |
| HTML structure | 269-400 |
| Subtitle display UI | 318-332 |
| Default config | 424-446 |
| State variables | 450-459 |
| Utilities | 464-582 |
| Video management | 588-717 |
| UI rendering | 724-863 |
| Event handlers | 806-1012 |
| Multiple subtitle functions | 846-1012 |
| Passive listener fix | 1019-1042 |
| Initialization | 1074-1108 |

### Common Tasks
```bash
# Test locally
open Interactive_Video_Navigator.html

# Make changes
edit Interactive_Video_Navigator.html

# Commit changes
git add Interactive_Video_Navigator.html
git commit -m "Add: New feature description"
git push

# View example content
cat README.md
```

---

**Last Updated**: 2025-11-17
**Application Version**: 2.0.0
**Documentation Version**: 1.1
**Maintained by**: Claude AI Assistant
