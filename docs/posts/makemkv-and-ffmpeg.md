---
draft: false
date: 2025-02-17
authors:
  - me
categories:
  - Home Media
---

# MakeMKV and FFmpeg
All of the physical media I own gets backed up to a [NAS](./home-network.md/#NAS) which is also a media server running [Jellyfin](https://jellyfin.org/). Recently, I [upgraded](https://www.amazon.com/dp/B08KQBYM1Y?ref=ppx_yo2ov_dt_b_fed_asin_title) my version of The Lord of the Rings. Here are some of my notes on ripping that media, and how I used FFmpeg to combine the multi-part films.<!-- more -->

## The Hardware and Software
The landscape for ripping Bluray media is a bear to navigate. It's a constant game of cat & mouse knowing which drives are compatible, and what for... Especially in regards to UHD media. The MakeMKV forums are the best source I've found for help. Here's the latest from them: [Flashing Guide Updated 2025](https://forum.makemkv.com/forum/viewtopic.php?t=19634).

### Hardware
I own the following drives:

 - [Pioneer BDR-XD08UMB-S](https://www.amazon.com/gp/product/B0BN65PZS9/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1) _I cannot read UHD media with this drive. The FW is too new_
 - [LG WP50NB40](https://www.amazon.com/gp/product/B011327QXC/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&th=1)

I can confirm, the LG drive with custom Firmware works great for ripping UHD media. I also have a [Pioneer BDR-213/2213](https://www.amazon.com/PIONEER-Internal-BDR-2213-Reliability-PureRead/dp/B0BN66Q2CF/ref=sr_1_3?crid=2K7DSA8XQQW5V&dib=eyJ2IjoiMSJ9.1QpUoJAZRBsCmyVV5Kj-TAapFzGfENnokOcwf3nnQyDvS3Q1ngigw27b2HA0BIpiSsHgdKsdXQQ4aDNY7rT-xQ1ZE6tWU5a-n6YwMz0qq6kt8qGiiR9WX8O6SrBCOPaZ6-N8VjAQhzIH6uoUxBVWGqzBju5aWc7xMSPAT53RCbosVEJ2TQG4-KbaQQJFCoYm.QktwDPcjzf_GliGr0ld0ib3qY3fZg_x6xLiB3kTzGqk&dib_tag=se&keywords=Pioneer+BDR-213%2F2213&qid=1739797679&sprefix=pioneer+bdr-213%2F2213%2Caps%2C99&sr=8-3) on order with hopes of building a desktop loaded with 5.25" drives.

### Software
These three titles will get you a long way. The only one you must buy a license for is MakeMKV, and it's well worth it.

 - [MakeMKV](https://www.makemkv.com/) - Ripping from disc to `movie.mkv` file
 - [HandBrake](https://handbrake.fr/) - Transcoding
 - [FFmpeg](https://www.ffmpeg.org/) - Post processing utility

## Combining titles
The Lord of the Rings is long. Each of the three films span multiple DVDs/Blurays. I don't want the films to be divvied into Part 1/Part 2 and luckily, they don't need to be once ripped to my NAS.  

There are a couple things to consider:

 - The two parts need to be combined
 - The chapters need to be maintained
 - The subtitle tracks need to be kept

FFmpeg makes all this possible.

### Getting Chapter Data
Once you have the films as `.mkv` files, the chapter data can be extracted with these commands:

```
ffmpeg -i .\part1.mkv -f ffmetadata part1_chapters.txt
ffmpeg -i .\part2.mkv -f ffmetadata part2_chapters.txt
```

Here's a snipped of what the data looks like:

```
;FFMETADATA1
title=The Lord of the Rings: The Two Towers (EXT.) PT. 1
encoder=Lavf60.16.100
[CHAPTER]
TIMEBASE=1/1000000000
START=0
END=248373125000
title=Chapter 01
[CHAPTER]
TIMEBASE=1/1000000000
START=248373125000
END=370703666666
title=Chapter 02
[CHAPTER]
TIMEBASE=1/1000000000
START=370703666666
END=884925708333
title=Chapter 03
```

The timestamps are in nanoseconds. In order to maintain chapter data through both parts of the film, we need to append the Part 2 data to the end of Part 1 accounting for the delta in nanoseconds of the first part. Claude did a fantastic job writing a script to do this very task:

```python
def combine_chapter_files(part1_path, part2_path, output_path):
    """
    Combines two FFmpeg chapter metadata files, adjusting timestamps for the second file
    based on the duration of the first file.
    """
    def parse_chapter_file(filepath):
        chapters = []
        current_chapter = None
        with open(filepath, 'r', encoding='utf-8') as f:
            header = []
            for line in f:
                line = line.strip()
                if line.startswith(';FFMETADATA1'):
                    header.append(line)
                elif line.startswith('title=') and not current_chapter:
                    header.append(line)
                elif line.startswith('encoder='):
                    header.append(line)
                elif line == '[CHAPTER]':
                    if current_chapter:
                        chapters.append(current_chapter)
                    current_chapter = {'timebase': None, 'start': None, 'end': None, 'title': None}
                elif current_chapter is not None:
                    if line.startswith('TIMEBASE='):
                        current_chapter['timebase'] = line
                    elif line.startswith('START='):
                        current_chapter['start'] = int(line.split('=')[1])
                    elif line.startswith('END='):
                        current_chapter['end'] = int(line.split('=')[1])
                    elif line.startswith('title='):
                        current_chapter['title'] = line
            if current_chapter:
                chapters.append(current_chapter)
        return header, chapters

    # Parse both files
    header1, chapters1 = parse_chapter_file(part1_path)
    _, chapters2 = parse_chapter_file(part2_path)

    # Get the end time of the last chapter in part 1
    offset = chapters1[-1]['end']

    # Adjust timestamps for part 2 chapters
    for chapter in chapters2:
        chapter['start'] += offset
        chapter['end'] += offset

    # Write combined chapters to output file
    with open(output_path, 'w', encoding='utf-8') as f:
        # Write header (using header from part 1)
        f.write('\n'.join(header1) + '\n\n')

        # Write all chapters
        for chapter in chapters1 + chapters2:
            f.write('[CHAPTER]\n')
            f.write(chapter['timebase'] + '\n')
            f.write(f"START={chapter['start']}\n")
            f.write(f"END={chapter['end']}\n")
            f.write(chapter['title'] + '\n\n')

# Example usage:
combine_chapter_files('part1_chapters.txt', 'part2_chapters.txt', 'combined_chapters.txt')
```

### Combining the Parts
Now that we have all the chapter data, the parts can easily be combined into one epic film. First, I create a `filelist.txt` file like so:

```
file 'part1.mkv'
file 'part2.mkv'
```

Finally, we can combine the files...

```bash
ffmpeg \
    -f concat -safe 0 -i filelist.txt \
    -i combined_chapters.txt \
    -map 0:v -map 0:a -map 0:s? -map_metadata 1 \
    -c copy -analyzeduration 100M -probesize 100M \
    "The Two Towers (2002).mkv"
```

Here's a summary of what all that command is doing:  

This command performs a concatenation (joining) of video files with some specific settings:

1. The first line with `-f concat -safe 0 -i filelist.txt` tells ffmpeg to:
    - Use the concat demuxer format (`-f concat`)
    - Disable "safe mode" (`-safe 0`) to allow absolute paths in the filelist
    - Read the list of input files from `filelist.txt`
2. `-i combined_chapters.txt` loads a second input file containing metadata about chapters
3. The mapping options (`-map` flags) specify which streams to include:
    - `-map 0:v` includes all video streams from the first input
    - `-map 0:a` includes all audio streams
    - `-map 0:s?` includes all subtitle streams (if any exist)
    - `-map_metadata 1` copies metadata from the second input file
4. The final settings:
    - `-c copy` uses stream copying (no re-encoding)
    - `-analyzeduration 100M` and `-probesize 100M` increase the analysis buffer size for large files
    - The output is saved as "The Two Towers (2002).mkv"

And that's a wrap.
