## Build libchromiumcontent library with H265/HEVC for electron in Windows
> [https://github.com/electron/libchromiumcontent](libchromium) has many useful scripts to generate gn files and ninja build files

### First get the fuck-big code
- use mirrors: e.g. https://beijing.source.codeaurora.org
- use agent: for python downing some tools from google

### Build Overview
> chromium use `ninja` build system using `gn` to generate ninja build files

The build pipe line:
```
|-------------|  Python    |-------------|   gn   |-------------------|  ninja       |static_library
| Source code | =========> | gn/gni files| =====> | ninja build files | ======> out/ |ffmpeg
|-------------|  Scripts   |-------------|        |-------------------|              |shared_library
```

### Important file structure
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
#### change chromiumcontent args
> ref to [args_gn.md](args_gn.md) 
ffmpeg.gn shared_library.gn static_library.gn

There is some important args:
```
is_dubug = false
proprietary_codecs = true
remove_webcore_debug_symbols = true
target_cpu = "x64"
symbol_level = 0
```

##### this part is almost the same with [hevc_support.md](hevc_support.md)
- src/third_party/ffmpeg/ffmpeg_generated.gni
- src/third_party/ffmpeg/chromium/config/chromium/win/x64


### Start build
#### update gn files and generate new ninja build files
There are two ways:

- Use gn cmd, and write your args in opened notepad
> we should manally change `src/chromcontent/args/*gn` files first
```
gn args out-x64/xxx
```

- Use script: `script/update -t x64`
> this file is very easy to change, we only need two function in `main`
``` python
copy_chromiumcontent_files()
run_gn(target_arch, args.defines)
```

#### build by ninja
once the ninja build files are ready, we can use ninja to build, I prefer to
```
python script/build -t x64
```

#### package it
we can manually copy or use the follwing script to package `libchromiumcontent`
```
python create-dist -t x64
```

### Add to electron
#### replace libchromiumcontent library
electron v1.7.3 source directory: /vender/download/libchromiumcontent/
- ffmpeg
- static_library
- shared_library

#### compile electron
```
python script/create-dist.py
```

> Add more details later
