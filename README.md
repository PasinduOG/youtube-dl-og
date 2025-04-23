# youtube-dl-og

[![npm version](https://img.shields.io/npm/v/youtube-dl-og.svg)](https://www.npmjs.com/package/youtube-dl-og)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Downloads](https://img.shields.io/npm/dt/youtube-dl-og.svg)](https://www.npmjs.com/package/youtube-dl-og)

A powerful and easy-to-use Node.js package for downloading YouTube videos in multiple quality levels.

## Table of Contents

- [Installation](#installation)
- [Features](#features)
- [Usage](#usage)
  - [Basic Usage](#basic-usage)
  - [Download with Progress Tracking](#download-with-progress-tracking)
  - [Downloading Videos in Specific HD Quality](#downloading-videos-in-specific-hd-quality-720p-1080p)
  - [Downloading Videos in Multiple Quality Levels](#downloading-videos-in-multiple-quality-levels)
  - [Downloading Audio Only](#downloading-audio-only)
  - [Getting Video Information](#getting-video-information)
  - [Validating YouTube URLs](#validating-youtube-urls)
- [API Reference](#api-reference)
- [Requirements](#requirements)
- [Dependencies](#dependencies)
- [Contributing](#contributing)
- [License](#license)

## Installation

```bash
# Install via npm
npm install youtube-dl-og

# Or using yarn
yarn add youtube-dl-og

# Or using pnpm
pnpm add youtube-dl-og
```

## Features

- Download YouTube videos in various formats and qualities
- Download videos in specific quality levels (144p, 360p, 720p, 1080p, 4K)
- Download multiple quality versions of a video in a single command
- Download audio-only streams
- Real-time download progress tracking
- Get detailed information about YouTube videos
- Validate YouTube URLs
- Built-in error handling and fallback mechanisms

## Usage

### Basic Usage

```javascript
const ytDownloader = require('youtube-dl-og');

// Download a video with default options (highest quality, mp4 format)
ytDownloader.downloadVideo('https://www.youtube.com/watch?v=VIDEO_ID', {
  output: './my-video.mp4'
})
.then(result => {
  console.log(`Downloaded: ${result.title}`);
  console.log(`Saved to: ${result.filePath}`);
})
.catch(err => {
  console.error('Error:', err.message);
});
```

### Download with Progress Tracking

```javascript
const ytDownloader = require('youtube-dl-og');

// Download with progress tracking
ytDownloader.downloadVideo('https://www.youtube.com/watch?v=VIDEO_ID', {
  output: './my-video.mp4',
  showProgress: true // Enable progress bar
})
.then(result => {
  console.log(`\nDownloaded: ${result.title}`);
  console.log(`Saved to: ${result.filePath}`);
})
.catch(err => {
  console.error('Error:', err.message);
});

// Custom progress handler
ytDownloader.downloadVideo('https://www.youtube.com/watch?v=VIDEO_ID', {
  output: './my-video.mp4',
  onProgress: (progress) => {
    // Progress is a number between 0 and 100
    console.log(`Download progress: ${progress.toFixed(2)}%`);
    console.log(`Downloaded: ${progress.downloadedBytes} / ${progress.totalBytes} bytes`);
    console.log(`Speed: ${progress.downloadSpeed} KB/s`);
    console.log(`ETA: ${progress.eta} seconds`);
  }
})
.then(result => {
  console.log(`Download complete: ${result.title}`);
});
```

### Downloading Videos in Specific HD Quality (720p, 1080p)

```javascript
const ytDownloader = require('youtube-dl-og');

// Download a video in 720p quality
ytDownloader.downloadVideo('https://www.youtube.com/watch?v=VIDEO_ID', {
  output: './my-video-720p.mp4',
  formatOption: 'bestvideo[height=720]+bestaudio/best',
  showProgress: true // Show progress bar
})
.then(result => {
  console.log(`Downloaded 720p video: ${result.title}`);
})
.catch(err => {
  console.error('Error:', err.message);
});

// For 1080p quality
ytDownloader.downloadVideo('https://www.youtube.com/watch?v=VIDEO_ID', {
  output: './my-video-1080p.mp4',
  formatOption: 'bestvideo[height=1080]+bestaudio/best',
  showProgress: true
});
```

### Downloading Videos in Multiple Quality Levels

```javascript
const ytDownloader = require('youtube-dl-og');

// Download a video in multiple quality levels at once
ytDownloader.downloadAllQualities('https://www.youtube.com/watch?v=VIDEO_ID', {
  outputDir: './downloads',
  format: 'mp4',
  qualities: ['360p', '720p', '1080p'], // Specific qualities
  includeAudioOnly: true,
  showProgress: true // Show progress bar for each download
})
.then(results => {
  results.forEach(result => {
    if (result.error) {
      console.log(`Failed to download ${result.quality}: ${result.error}`);
    } else {
      console.log(`Successfully downloaded ${result.quality} to ${result.filePath}`);
    }
  });
})
.catch(err => {
  console.error('Error:', err.message);
});

// You can also use special quality indicators 'highest' and 'lowest'
ytDownloader.downloadAllQualities('https://www.youtube.com/watch?v=VIDEO_ID', {
  outputDir: './downloads',
  qualities: ['lowest', 'highest'], // Get both lowest and highest quality
  includeAudioOnly: true,
  showProgress: true
});
```

### Downloading Audio Only

```javascript
ytDownloader.downloadVideo('https://www.youtube.com/watch?v=VIDEO_ID', {
  output: './audio-only.mp3',
  audioOnly: true,
  showProgress: true // Show progress bar
})
.then(result => {
  console.log(`Downloaded audio for: ${result.title}`);
})
.catch(err => {
  console.error('Error:', err.message);
});
```

### Getting Video Information

```javascript
ytDownloader.getVideoInfo('https://www.youtube.com/watch?v=VIDEO_ID')
.then(info => {
  console.log('Title:', info.title);
  console.log('Duration:', info.lengthSeconds, 'seconds');
  console.log('View count:', info.viewCount);
  console.log('Available formats:', info.formats.length);
  
  // Get available quality levels
  const qualities = new Set();
  info.formats.forEach(format => {
    if (format.hasVideo) {
      const height = format.resolution.split('x')[1];
      if (height) {
        qualities.add(`${height}p`);
      }
    }
  });
  console.log('Available qualities:', [...qualities].join(', '));
})
.catch(err => {
  console.error('Error:', err.message);
});
```

### Validating YouTube URLs

```javascript
const isValid = ytDownloader.isValidYoutubeUrl('https://www.youtube.com/watch?v=VIDEO_ID');
console.log('Is valid YouTube URL:', isValid); // true
```

## API Reference

### downloadVideo(url, options)

Downloads a YouTube video.

**Parameters:**

- `url` (string): The YouTube video URL
- `options` (object):
  - `quality` (string): Video quality ('highest', 'lowest', or specific itag). Default: 'highest'
  - `format` (string): Video format ('mp4', 'webm', etc.). Default: 'mp4'
  - `output` (string): Output file path. Default: './video.mp4'
  - `audioOnly` (boolean): Download audio only. Default: false
  - `formatOption` (string): Format option for youtube-dl-exec. Default: null
  - `showProgress` (boolean): Whether to display a progress bar in the console. Default: false
  - `onProgress` (function): Callback function that receives progress updates. Default: null

**Returns:**

- Promise that resolves with an object containing:
  - `title` (string): Video title
  - `filePath` (string): Path where the file was saved

### downloadAllQualities(url, options)

Downloads a YouTube video in multiple quality levels.

**Parameters:**

- `url` (string): The YouTube video URL
- `options` (object):
  - `outputDir` (string): Output directory path. Default: './downloads'
  - `format` (string): Video format ('mp4', 'webm', etc.). Default: 'mp4'
  - `qualities` (array): Quality levels to download (e.g. ['144p', '360p', '720p', '1080p', 'highest', 'lowest']). Default: ['720p']
  - `includeAudioOnly` (boolean): Whether to include audio-only version. Default: true
  - `showProgress` (boolean): Whether to display progress bars for each download. Default: false

**Returns:**

- Promise that resolves with an array of objects, each containing:
  - `quality` (string): The quality level of the download
  - `title` (string): Video title
  - `filePath` (string): Path where the file was saved
  - `error` (string, optional): Error message if download failed

### getVideoInfo(url)

Gets detailed information about a YouTube video.

**Parameters:**

- `url` (string): The YouTube video URL

**Returns:**

- Promise that resolves with an object containing:
  - `title` (string): Video title
  - `description` (string): Video description
  - `lengthSeconds` (string): Video duration in seconds
  - `viewCount` (string): Number of views
  - `author` (object): Channel information
  - `formats` (array): Available video formats

### isValidYoutubeUrl(url)

Checks if a URL is a valid YouTube URL.

**Parameters:**

- `url` (string): The URL to check

**Returns:**

- `boolean`: True if the URL is a valid YouTube URL, false otherwise

## Progress Updates

When using the `showProgress` option or `onProgress` callback, you'll receive progress information with the following properties:

- `percent` (number): Download progress percentage (0-100)
- `downloadedBytes` (number): Number of bytes downloaded so far
- `totalBytes` (number): Total size in bytes (may be estimated)
- `downloadSpeed` (number): Download speed in KB/s
- `eta` (number): Estimated time remaining in seconds

## Requirements

- Node.js >= 14.0.0

## Dependencies

- ytdl-core: Core library for YouTube video downloading
- youtube-dl-exec: Provides robust fallback downloading capabilities
- ffmpeg-static: For audio extraction and format conversion
- progress: For displaying download progress bars in the terminal

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Setup

```bash
# Clone the repository
git clone https://github.com/PasinduOG/youtube-dl-og.git

# Install dependencies
cd youtube-dl-og
npm install

# Run tests
npm test
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

If you find this package useful, please consider ⭐️ starring the repo on GitHub!

[https://github.com/PasinduOG/youtube-dl-og](https://github.com/PasinduOG/youtube-dl-og)