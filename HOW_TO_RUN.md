# How to Run Interactive Video Navigator

The Interactive Video Navigator is a standalone HTML application that can be opened directly in a web browser. However, for some features (like loading local video files), you may need to run a local web server.

## Method 1: Direct Opening (Simplest)

Simply double-click the `Interactive_Video_Navigator.html` file or open it directly in your browser:

```bash
# Linux/Mac
open Interactive_Video_Navigator.html

# Windows
start Interactive_Video_Navigator.html
```

**Note:** Some features like local video file loading require running a web server.

---

## Method 2: Python Web Server (Recommended)

### Python 3.x (Recommended)

```bash
# Navigate to the project directory
cd /path/to/Interactive-Video-Navigator

# Start the server
python3 -m http.server 8000

# Or on Windows
python -m http.server 8000
```

Then open in your browser:
```
http://localhost:8000/Interactive_Video_Navigator.html
```

### Python 2.x (Legacy)

```bash
python -m SimpleHTTPServer 8000
```

---

## Method 3: PHP Built-in Server

If you have PHP installed:

```bash
# Navigate to the project directory
cd /path/to/Interactive-Video-Navigator

# Start the server
php -S localhost:8000
```

Then open in your browser:
```
http://localhost:8000/Interactive_Video_Navigator.html
```

---

## Method 4: Node.js with http-server

First, install http-server globally:

```bash
npm install -g http-server
```

Then run:

```bash
# Navigate to the project directory
cd /path/to/Interactive-Video-Navigator

# Start the server
http-server -p 8000
```

Then open in your browser:
```
http://localhost:8000/Interactive_Video_Navigator.html
```

### Alternative: Using npx (no installation needed)

```bash
npx http-server -p 8000
```

---

## Features Available

### With Direct File Opening
✅ YouTube video playback
✅ Index navigation
✅ Subtitle upload and display
✅ Search functionality
✅ Theater mode

### Requires Web Server
✅ Local video file playback
✅ Auto-loading of subtitle files
✅ Auto-loading of index files
✅ CORS-protected features

---

## Troubleshooting

### CORS Errors
If you see CORS errors in the browser console when trying to load local files:
- Make sure you're running a web server (Python/PHP/Node)
- Do not open the file directly with `file://` protocol

### Port Already in Use
If port 8000 is already in use, try a different port:

```bash
# Python
python3 -m http.server 8080

# PHP
php -S localhost:8080

# Node.js
http-server -p 8080
```

### Video Not Playing
- For YouTube videos: Check your internet connection
- For local videos: Ensure the video file is in a supported format (MP4, WebM)
- Make sure you're running a web server

---

## Local Video File Support

When using local video files, the application will automatically search for:

### Subtitle Files (in order of priority)
1. `videoname.de.srt` (German subtitles)
2. `videoname.srt` (Default subtitles)
3. `videoname.es.srt` (Spanish subtitles)
4. `videoname.en.srt` (English subtitles)

The first 3 found files will be loaded into Subtitle 1, 2, and 3 respectively.

### Index Files
- `videoname.index` - Automatically loaded if found

Example:
```
my-video.mp4
my-video.de.srt    → Loaded as Subtitle 1
my-video.es.srt    → Loaded as Subtitle 2
my-video.en.srt    → Loaded as Subtitle 3
my-video.index     → Loaded as Index
```

---

## Browser Compatibility

Tested and working on:
- ✅ Chrome/Chromium (Recommended)
- ✅ Firefox
- ✅ Safari
- ✅ Edge

---

## Quick Start

1. Choose your preferred server method (Python recommended)
2. Start the server in the project directory
3. Open `http://localhost:8000/Interactive_Video_Navigator.html`
4. Load your YouTube URL or local video file
5. Upload subtitle and index files (or use auto-detection for local videos)
6. Enjoy enhanced video navigation!
