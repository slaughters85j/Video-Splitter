# Video Splitter

A Python script that splits video files into segments while preserving quality and providing options for frame rate conversion and bit rate control. Optimized for Apple Silicon with VideoToolbox hardware acceleration support for VBR encoding.

## Features

- 🎬 Split videos by number of segments or specific duration
- 🔄 Custom output frame rate support (or use original by pressing Enter)
- 🎛️ Choose between Constant Bit Rate (CBR) or Variable Bit Rate (VBR) encoding
- 🎯 Preserves original video quality (resolution, target bit rate)
- ⚡ Hardware acceleration (`VideoToolbox` on Apple Silicon) used for VBR encoding if available
- ✅ Accurate CBR encoding using `libx264` software encoder
- 🔊 Preserves original audio without re-encoding
- 🎯 Handles file paths with spaces (no quotes needed)
- 📊 Detailed video information display before processing
- ✨ Interactive command-line interface
- 📋 Final job summary including input/output details and output file verification

## Requirements

- Python (Tested with Python 3.12.3, but should work with other Python 3 versions)
- FFmpeg (Ensure it's installed and in your system's PATH)
- `ffmpeg-python` package

## Installation

1.  **Install FFmpeg:**
    *   **macOS (using Homebrew):** `brew install ffmpeg`
    *   **Ubuntu/Debian:** `sudo apt-get update && sudo apt-get install ffmpeg`
    *   **Windows:** Download from [ffmpeg.org](https://ffmpeg.org/download.html) and add to PATH.

2.  **Install Python package:**
    ```bash
    pip install ffmpeg-python
    ```

3.  **Download the script:**
    Save the `video_splitter.py` file to your computer.

## Usage

1.  Run the script from your terminal:
    ```bash
    python video_splitter.py
    ```

2.  Follow the interactive prompts:
    *   Enter the path to your video file.
    *   Choose splitting method (by number of segments or duration per segment).
    *   Set desired output frame rate (Press Enter to use the source frame rate).
    *   Choose bit rate control mode (CBR or VBR).
    *   Review the settings summary.
    *   Confirm to proceed.

### Example Session

```
Welcome to Video Splitter!

Enter the path to your video file: my video.mp4

Analyzing video: my video.mp4

Video information:
  Current frame rate: 29.97 fps
  Current bit rate: 8.50 Mbps
  Resolution: 1920x1080
  Duration: 300.50 seconds

Do you want to split by (1) number of segments or (2) duration per segment? Enter 1 or 2: 2

Enter the duration (in seconds) for each segment: 60

What frame rate would you like for the output? (Press Enter to use input frame rate, or enter a number e.g. 23.97, 30, 60):  # User presses Enter
Using input frame rate: 29.97 fps

Choose bit rate control mode: (1) Constant Bit Rate (CBR) or (2) Variable Bit Rate (VBR)? Enter 1 or 2: 1

Settings Summary:
  Input file: my video.mp4
  Output directory: my video_parts
  Segment duration: 60.00 seconds
  Number of segments: 6
  Target frame rate: 29.97 fps
  Bit rate: 8.50 Mbps
  Bit rate control: Constant (CBR)
  Hardware acceleration: Available (Forced Software for CBR) # Note: Shows status

Proceed with these settings? (y/n): y

Video info:
  Original frame rate: 29.97 fps
  Target frame rate: 29.97 fps
  Bit rate: 8.50 Mbps
  Rate control: CBR
  Resolution: 1920x1080
  Duration: 300.50 seconds
  Splitting into 6 segments of 60.00 seconds each
  Using encoder: libx264 (Software) - Status: Available (Forced Software for CBR)

Enforcing strict CBR mode with bit rate: 8.50 Mbps
Buffer size: 1.06 Mbps (1/8 of bit rate)
Using libx264 with strict CBR parameters and very slow preset

Creating segment 1/6: my video_parts/my video_part001.mp4
... (ffmpeg output) ...

Creating segment 6/6: my video_parts/my video_part006.mp4
... (ffmpeg output) ...

Video splitting complete. Output files in: my video_parts

================================================================================
JOB SUMMARY
================================================================================

INPUT VIDEO DETAILS:
  File: my video.mp4
  Frame rate: 29.97 fps
  Bit rate: 8.50 Mbps
  Resolution: 1920x1080
  Duration: 300.50 seconds

OUTPUT DETAILS:
  Output directory: my video_parts
  Number of segments: 6
  Segment duration: 60.00 seconds
  Target frame rate: 29.97 fps
  Bit rate control: Constant (CBR)
  Hardware acceleration: Used # Note: This reflects if VT is available, not necessarily used for encoding

OUTPUT VERIFICATION:
  Sample file: my video_parts/my video_part001.mp4
  Actual frame rate: 29.97 fps
  Actual bit rate: 8.50 Mbps
================================================================================

```

## Output

- Creates a new directory named `[original_filename]_parts` in the same location as the input video.
- Segments are named `[original_filename]_part001.[extension]`, `[original_filename]_part002.[extension]`, etc.
- Maintains original video codec (H.264 output) and container format.
- Provides a detailed job summary upon completion.

## Features in Detail

### Hardware Acceleration
- Automatically detects VideoToolbox on Apple Silicon Macs.
- **VBR Encoding:** Uses `h264_videotoolbox` hardware encoder if available for faster processing.
- **CBR Encoding:** Intentionally uses `libx264` software encoder to ensure strict adherence to the target bit rate, even if hardware acceleration is available.
- Falls back to `libx264` software encoding if hardware acceleration is unavailable or not applicable (e.g., CBR).

### Video Quality
- Preserves original resolution.
- Allows custom output frame rate (or maintains original).
- Uses the original bit rate as the target for encoding.
- Copies the audio stream directly (`-c:a copy`) without re-encoding to preserve audio quality.

### Bit Rate Control Options
1.  **Constant Bit Rate (CBR):**
    *   Aims to maintain a consistent bit rate throughout the video.
    *   Uses the exact bit rate from the source video as the target.
    *   Uses the `libx264` software encoder with strict parameters (`nal-hrd=cbr`, `force-cfr=1`, small `bufsize`, `veryslow` preset) for maximum accuracy. Ideal when a predictable bitrate is required.
    *   Encoding may be slower than VBR due to software encoding and `veryslow` preset.
2.  **Variable Bit Rate (VBR):**
    *   Allocates bits dynamically based on scene complexity (more bits for complex scenes, fewer for simple scenes).
    *   Generally provides a better quality-to-size ratio compared to CBR.
    *   Uses the original bit rate as the **target average** bit rate, allowing the encoder to exceed this rate for complex scenes (no hard maximum cap is set).
    *   Uses `h264_videotoolbox` hardware encoder if available (setting the target average via `-b:v`).
    *   Uses `libx264` software encoder in Average Bit Rate (ABR) mode if hardware encoding is unavailable (setting target average via `-b:v`).

### Splitting Options
1.  **By Number of Segments:**
    *   Enter the total number of segments desired.
    *   The script calculates the required segment duration.
2.  **By Duration:**
    *   Specify the exact duration (in seconds) for each segment.
    *   The script calculates the total number of segments.

## Performance

- VBR encoding benefits significantly from hardware acceleration when available.
- CBR encoding prioritizes accuracy over speed by using the `veryslow` preset with `libx264`.
- Audio stream copying is very fast and lossless.

## Known Limitations

- Output segments might be slightly longer or shorter than specified due to keyframe placement by the encoder. FFmpeg splits at the nearest keyframe before the specified time for `-ss` and cuts after the duration `-t` is reached.
- Hardware acceleration support is currently focused on Apple Silicon (VideoToolbox). Other hardware acceleration methods (like NVENC, QSV) are not implemented.
- Older versions of FFmpeg might have different behaviors or limitations with hardware encoders.

## Contributing

Feel free to open issues or submit pull requests with improvements!

## License

MIT License - feel free to use and modify as needed.

## Acknowledgments

- FFmpeg development team
- `ffmpeg-python` library maintainers
