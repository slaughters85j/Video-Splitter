# Video Splitter

A Python script that splits video files into segments while preserving quality and providing options for frame rate conversion. Optimized for Apple Silicon with VideoToolbox hardware acceleration support.

## Features

- 🎬 Split videos by number of segments or specific duration
- 🔄 Custom output frame rate support
- 🎯 Preserves original video quality (bit rate and resolution)
- ⚡ Hardware acceleration support (VideoToolbox on Apple Silicon)
- 🔊 Preserves original audio without re-encoding
- 🎯 Handles file paths with spaces (no quotes needed)
- 📊 Detailed video information display
- ✨ Interactive command-line interface

## Requirements

- Python (Tested with Python 3.12.3, but should work with other Python 3 versions)
- FFmpeg
- ffmpeg-python package

## Installation

1. Install FFmpeg if you haven't already:
   ```bash
   # On macOS using Homebrew
   brew install ffmpeg

   # On Ubuntu/Debian
   sudo apt-get install ffmpeg
   ```

2. Install the required Python package:
   ```bash
   pip install ffmpeg-python
   ```

3. Download the script:
   ```bash
   curl -O https://raw.githubusercontent.com/YOUR_USERNAME/video-splitter/main/video_splitter.py
   ```

## Usage

1. Run the script:
   ```bash
   python video_splitter.py
   ```

2. Follow the interactive prompts:
   - Enter the path to your video file
   - Choose splitting method (by number of segments or duration per segment)
   - Set desired output frame rate
   - Review and confirm settings

### Example Session

```
Welcome to Video Splitter!

Enter the path to your video file (does not require quotations around path): my video.mp4

Analyzing video: my video.mp4

Video information:
  Current frame rate: 29.97 fps
  Current bit rate: 8.50 Mbps
  Resolution: 1920x1080
  Duration: 300.50 seconds

Do you want to split by (1) number of segments or (2) duration per segment? Enter 1 or 2: 2

Enter the duration (in seconds) for each segment: 60

What frame rate would you like for the output? (e.g., 30): 30

Settings Summary:
  Input file: my video.mp4
  Output directory: my video_parts
  Segment duration: 60.00 seconds
  Number of segments: 6
  Target frame rate: 30 fps
  Hardware acceleration: Available

Proceed with these settings? (y/n): y
```

## Output

- Creates a new directory named `[original_filename]_parts` in the same location as the input video
- Segments are named `[original_filename]_part001.[extension]`, `[original_filename]_part002.[extension]`, etc.
- Maintains original video codec (H.264) and container format
- Uses hardware acceleration when available (VideoToolbox on Apple Silicon)

## Features in Detail

### Hardware Acceleration
- Automatically detects and uses VideoToolbox on Apple Silicon Macs
- Falls back to software encoding (libx264) if hardware acceleration is unavailable

### Video Quality
- Preserves original bit rate
- Maintains original resolution
- Allows custom output frame rate
- Copies audio stream without re-encoding

### Splitting Options
1. By Number of Segments:
   - Enter total number of segments desired
   - Script calculates optimal segment duration
2. By Duration:
   - Specify exact duration for each segment
   - Script calculates number of resulting segments

## Performance

- Hardware acceleration (when available) provides significantly faster encoding
- Minimal quality loss due to direct stream copying when possible
- Efficient handling of large files

## Known Limitations

- Output segments might be a few frames longer than specified due to keyframe alignment
- Hardware acceleration is currently optimized for Apple Silicon (VideoToolbox)

## Contributing

Feel free to open issues or submit pull requests with improvements!

## License

MIT License - feel free to use and modify as needed.

## Acknowledgments

- FFmpeg for the underlying video processing capabilities
- ffmpeg-python for the Python FFmpeg interface
