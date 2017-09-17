## Build libchromiumcontent library with H265/HEVC for electron in Windows
> [https://github.com/electron/libchromiumcontent](libchromium) has many useful scripts to generate gn files and ninja build files

### First get the fuck-big code
- use mirrors: e.g. https://beijing.source.codeaurora.org
- use agent: for python downing some tools from google

### Import file structure
```
|-- chromiumcontent: Main gn base file for build libchromiumcontent
|---- args:
|------ ffmpeg.gn: config ffmpeg
|------ shared_library.gn 
|------ static_library.gn
|-- patches: Your changes
|-- script: Main python scripts for build
|---- update: When gn/gni files changed, this scripts can update ninja build files (Not only for update sources and tools)
|---- build: build library for any platform by ninja
|---- create-dist: pack mess library files into zips
|---- upload: automate scripts for uploading builds to S3 amazon server
|-- src: All source files of chromium
|---- chromiumcontent: update script will sync this dir to /chromiumcontent in root dir
|---- build/config: base config for all
|------ BUILDCONFIG.gn: is_electron_
|---- third_party/ffmpeg: the dir we make change
|------ chromium/config:
|-------- Chrome(ChromeOS Chromium):
|---------- win/x64: config.asm config.h
|------ chromium/scripts:
|------ ffmpeg_generated.gni: it's easy to change it directly rather than by scripts
|-- vendor: depot_tools
```

### Add HEVC support

### Start build
#### update gn files and generate new ninja build files
#### build by ninja
#### package it

### Add to electron
#### replace libchromiumcontent library
electron v1.7.3 source directory:
#### compile electron


> Add more details later
