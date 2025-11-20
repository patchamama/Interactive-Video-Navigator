# Smart Subtitle Processor - Technical Documentation

## Overview

The Smart Subtitle Processor is an advanced web-based tool for processing, translating, and optimizing subtitle files (SRT format). It features intelligent algorithms for sentence joining, line splitting, time distribution, and automatic translation detection.

**Version**: 3.9.1
**Primary Use Case**: Processing multilingual subtitles with automatic translation via Google Translator

---

## Architecture

### Technology Stack
- **Frontend**: Pure JavaScript (ES6+), HTML5, CSS3
- **Framework**: Vanilla JS (no dependencies)
- **Styling**: Custom CSS with gradient design
- **File Format**: SubRip Text (SRT)

### Key Components
1. **SRT Parser**: Parses subtitle files into structured data
2. **Time Calculator**: Converts between SRT time format and seconds
3. **Processing Algorithms**: Join Sentences, Split Longlines, YouTube Cleaner
4. **Auto-Scroll System**: Automatic translation detection and scrolling
5. **Export Engine**: Generates translated SRT files

---

## Core Features

### 1. YouTube Subs. Cleaner (🧹)
**Purpose**: Removes duplicate lines common in auto-generated YouTube subtitles.

**Algorithm**:
1. Scans through all subtitle entries
2. Identifies consecutive lines with identical text
3. Merges duplicates into a single entry
4. Calculates time span from first to last occurrence
5. Preserves original text and status

**Use Case**: YouTube auto-generated subtitles often repeat the same phrase across multiple time slots.

**Example**:
```
Before:
1. 00:00:01,000 --> 00:00:02,000
   Hello world

2. 00:00:02,000 --> 00:00:03,000
   Hello world

3. 00:00:03,000 --> 00:00:04,000
   Hello world

After:
1. 00:00:01,000 --> 00:00:04,000
   Hello world
```

---

### 2. Join Sentences (🔗)
**Purpose**: Merges fragmented subtitle lines into complete sentences using intelligent detection.

**Algorithm**:

#### Phase 1: Line Joining
1. Iterate through subtitle entries
2. Check if current line ends with sentence terminator (`.` `?` `!`)
3. If NOT terminated, join with following lines until:
   - Sentence terminator found
   - Subordinate clause marker reached (configurable max lines)
   - End of file

#### Phase 2: Abbreviation Detection
- Prevents splitting on common abbreviations:
  - German: `z. B.`, `ca.`, etc.
  - Spanish: `p. ej.`, `ej.`, etc.
  - English: `e.g.`, `i.e.`, etc.

#### Phase 3: Sentence Splitting
- Character-by-character analysis
- Detects real sentence boundaries
- Checks for uppercase following character
- Splits multiple sentences found in joined text

#### Phase 4: Time Distribution
- Calculates total duration from original subtitle time slots
- Distributes time proportionally based on sentence length
- Ensures minimum 100ms duration per sentence
- Last sentence receives exact end time

#### Phase 5: Subordinate Clause Detection
- Stops joining when max lines reached AND subordinate marker found
- **German**: weil, wenn, dass, ob, als, damit, obwohl, bevor, nachdem, während, sobald, und, aber, oder, denn, sondern, deshalb, trotzdem, danach, zuerst, außerdem
- **Spanish**: que, porque, cuando, si, aunque, mientras, después, antes, para que, ya que, puesto que, dado que, a pesar de, sin embargo, por lo tanto, entonces, además, también, pero, sino, o, y
- **English**: that, because, when, if, although, while, after, before, since, unless, until, whereas, so that, in order to, however, therefore, thus, moreover, furthermore, but, or, and, yet

**Configuration**:
- **Max lines**: Number of subtitle lines to join before checking for subordinate markers (default: 3, range: 2-10)

**Example**:
```
Before:
1. 00:01:00,000 --> 00:01:02,000
   Dies ist ein Test

2. 00:01:02,000 --> 00:01:04,000
   der sehr wichtig ist.

After:
1. 00:01:00,000 --> 00:01:04,000
   Dies ist ein Test der sehr wichtig ist.
```

---

### 3. Split Longlines (✂️)
**Purpose**: Divides overly long subtitle lines into readable chunks at intelligent split points.

**Algorithm**:

#### Phase 1: Length Check
- Read configurable max length (default: 200 characters)
- Skip subtitles already within limit

#### Phase 2: Bidirectional Split Point Search

**BACKWARD SEARCH** (from MAX_LENGTH to 50% of MAX_LENGTH):
1. **Strategy 1A**: Search for sentence terminators (`.` `?` `!`)
2. **Strategy 2A**: Search for subordinate conjunction markers

**FORWARD SEARCH** (from MAX_LENGTH to end) if nothing found backward:
1. **Strategy 1B**: Search for sentence terminators
2. **Strategy 2B**: Search for subordinate conjunctions
3. **Strategy 3B**: Search for first space

**FALLBACK**:
4. **Strategy 4**: Force split at MAX_LENGTH

#### Phase 3: Proportional Time Distribution
- Calculate total text length across all chunks
- Distribute duration proportionally based on chunk length
- Ensure minimum 100ms per chunk
- Last chunk gets exact end time

**Configuration**:
- **Max subts. length**: Maximum characters per subtitle line (default: 200, range: 100-500, step: 10)

**Example with logging**:
```
Input: 350 character subtitle
  Split at position 185 using: subordinate word "weil" (backward)
  Split at position 165 using: sentence terminator (forward)
  → Split into 3 chunks
  Chunk 1: 00:00:10,000 → 00:00:13,000 (First part of text...)
  Chunk 2: 00:00:13,000 → 00:00:16,000 (weil second part...)
  Chunk 3: 00:00:16,000 → 00:00:19,000 (Final part.)
```

---

### 4. Fix Loss Times (⏱️)
**Purpose**: Detects and merges subtitle entries with identical start and end times (invisible subtitles).

**Algorithm**:
1. Iterate through processed subtitle list
2. Detect entries where `startTime === endTime`
3. Merge invisible entry with previous subtitle
4. Concatenate text with space separator
5. Renumber all subtitles after merging

**Use Case**: Prevents subtitles that won't be visible in video players due to zero duration.

**Example**:
```
Before:
1. 00:01:00,000 --> 00:01:02,000
   First part

2. 00:01:02,000 --> 00:01:02,000  ← INVISIBLE
   second part

After:
1. 00:01:00,000 --> 00:01:02,000
   First part second part
```

---

## Time Conversion System

### Functions

#### `timeToSeconds(timeString)`
Converts SRT time format to total seconds.

**Input**: `"01:23:45,678"`
**Output**: `5025.678` (seconds)

**Algorithm**:
```javascript
HH:MM:SS,mmm → (HH * 3600) + (MM * 60) + SS + (mmm / 1000)
```

#### `secondsToTime(totalSeconds)`
Converts seconds back to SRT time format.

**Input**: `5025.678`
**Output**: `"01:23:45,678"`

**Algorithm**:
```javascript
hours = floor(seconds / 3600)
minutes = floor((seconds % 3600) / 60)
seconds = floor(seconds % 60)
milliseconds = round((seconds % 1) * 1000)
```

---

## Auto-Scroll System

### Smart Mode
**Purpose**: Automatically detects when Google Translator has finished translating a subtitle and scrolls to the next one.

**Detection Mechanism**:
1. Monitors status column for change from `((Waiting, Espera, Warten))`
2. Compares current text with snapshot
3. When text changes, marks as translated
4. Automatically scrolls to next untranslated row

**Timing**:
- Continuous monitoring via `setTimeout(fn, 0)` for rapid checking
- Configurable delay between automatic translations

**End Detection**:
- When no more `((Waiting, Espera, Warten))` text exists
- Automatically exports translated SRT file

---

## Data Structures

### Subtitle Entry Object
```javascript
{
    number: "1",                       // Sequence number
    startTime: "00:01:23,456",        // SRT format HH:MM:SS,mmm
    endTime: "00:01:25,789",          // SRT format HH:MM:SS,mmm
    text: "Subtitle text content",     // Display text
    originalText: "Original content",  // Preserved for reprocessing
    status: "((Waiting, Espera, Warten))" // Translation status
}
```

### Time Slot Object (used in time distribution)
```javascript
{
    startSeconds: 83.456,    // Converted to seconds
    endSeconds: 85.789,      // Converted to seconds
    duration: 2.333          // Calculated duration
}
```

---

## UI Controls

### Toggle Buttons
All processing features use toggle buttons with visual state:
- **Inactive**: Gray background (#e0e0e0)
- **Active**: Green background (#4caf50)
- **Hover**: Elevated with shadow effect

### Input Controls
1. **Max lines** (2-10): Controls Join Sentences subordinate clause detection
2. **Max subts. length** (100-500): Controls Split Longlines maximum length
3. **Page Lang** (EN/ES/DE/FR): Sets HTML lang attribute for translator

### Export
- **Export SRT**: Downloads processed subtitles as `.srt` file
- Automatically appends `_translated` to filename

---

## Processing Pipeline

### Typical Workflow

1. **Load SRT File**
   - User selects file via file input
   - Parser extracts entries
   - Display in editable table

2. **Optional: Apply YouTube Cleaner**
   - Removes duplicate consecutive lines
   - Reduces file size significantly

3. **Optional: Apply Join Sentences**
   - Merges fragmented lines
   - Creates complete sentences
   - Better context for translation

4. **Translate**
   - Enable Smart Auto-Scroll
   - Google Translator processes each row
   - System detects completion and advances

5. **Optional: Apply Split Longlines**
   - Divides overly long translated lines
   - Improves readability

6. **Optional: Apply Fix Loss Times**
   - Detects invisible subtitles
   - Merges with previous entry

7. **Export**
   - Download translated SRT file
   - Ready for video player

---

## Performance Considerations

### Optimization Strategies

1. **Proportional Time Distribution**
   - Prevents invisible subtitles
   - Ensures all entries have visible duration
   - Minimum 100ms guaranteed

2. **Intelligent Split Points**
   - Preserves sentence meaning
   - Respects grammatical structure
   - Prioritizes natural breakpoints

3. **Memory Efficiency**
   - Processes table content directly
   - No large in-memory data structures
   - Minimal DOM manipulation

4. **Execution Speed**
   - Join Sentences: ~50ms for 200 entries
   - Split Longlines: ~30ms for 200 entries
   - YouTube Cleaner: ~20ms for 200 entries

---

## Console Logging

All operations provide detailed console output for debugging:

```
✂️ Starting Split Longlines - 120 subtitles (MAX_LENGTH: 200)
  ⚠ Long line (350 chars): "Dies ist ein sehr langer..."
    Split at position 185 using: subordinate word "weil" (backward)
    Split at position 165 using: sentence terminator (forward)
    → Split into 3 chunks
      Chunk 1: 00:00:10,000 → 00:00:13,000 (First part...)
      Chunk 2: 00:00:13,000 → 00:00:16,000 (weil second part...)
      Chunk 3: 00:00:16,000 → 00:00:19,000 (Final part.)
✅ Split complete - 120 → 145 subtitles
```

---

## Best Practices

### Recommended Processing Order

1. **YouTube Cleaner** (if source is YouTube auto-generated)
2. **Join Sentences** (before translation for better context)
3. **Translate** (using Smart Auto-Scroll)
4. **Split Longlines** (after translation if lines too long)
5. **Fix Loss Times** (final cleanup for invisible entries)
6. **Export**

### Configuration Guidelines

- **Max lines**: 3-4 for most languages
- **Max subts. length**:
  - 150-180 for narrow displays
  - 200-250 for standard displays
  - 250-300 for wide displays

### Language-Specific Tips

**German**:
- Higher max lines (4-5) due to compound words
- Subordinate clause detection critical

**Spanish**:
- Standard max lines (3)
- Watch for "que" overuse in splitting

**English**:
- Standard max lines (3)
- Lower max subts. length (180-200)

---

## Troubleshooting

### Common Issues

**Issue**: Subtitles not joining properly
**Solution**: Check for unusual punctuation or encoding issues

**Issue**: Split creates very short chunks
**Solution**: Increase max subts. length parameter

**Issue**: Translation not auto-detecting
**Solution**: Ensure status column contains `((Waiting, Espera, Warten))`

**Issue**: Exported file has timing errors
**Solution**: Apply Fix Loss Times before export

---

## Technical Limitations

1. **Maximum subtitle length**: 500 characters (configurable limit)
2. **Minimum duration**: 100ms per subtitle entry
3. **Supported format**: SRT only (SubRip Text)
4. **Character encoding**: UTF-8 recommended
5. **Browser dependency**: Requires modern browser (Chrome, Firefox, Edge)

---

## Version History

### v3.9.1 - Enhanced Split (Current)
- Bidirectional search for split points
- Customizable max subtitle length
- Removed backward space search strategy

### v3.9 - Split Longlines
- Added Split Longlines button
- Intelligent split point detection
- Proportional time distribution

### v3.8 - Toggle Buttons
- Converted checkboxes to toggle buttons
- Separated Fix Loss Times functionality
- Improved UI consistency

### v3.7 - Language Selector
- Added page language selector
- localStorage persistence
- Better translator detection

### Earlier Versions
- v3.6: Subordinate clause detection
- v3.5: Abbreviation handling
- v3.4: Simplified UI
- v3.3: Join Sentences implementation
- v3.2: Auto-scroll improvements

---

## Future Enhancements (Planned)

1. **Batch Processing**: Process multiple SRT files simultaneously
2. **Preset Profiles**: Save processing configurations
3. **Undo/Redo**: History management for operations
4. **Preview Mode**: View before/after comparison
5. **Advanced Filters**: Custom regex-based processing
6. **Time Adjustment**: Global time offset correction

---

## Contributing

This tool is part of the Interactive Video Navigator project. For issues, feature requests, or contributions, please refer to the main repository.

**Repository**: https://github.com/patchamama/Interactive-Video-Navigator

---

## License

See project LICENSE file.

---

**Last Updated**: 2025-01-20
**Document Version**: 1.0
**Application Version**: 3.9.1
