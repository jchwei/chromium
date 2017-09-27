## Build libchromiumcontent library with H265/HEVC for electron in Windows
> [libchromiumcontent](https://github.com/electron/libchromiumcontent) has many useful scripts to generate gn files and ninja build files, related issues [buiding with alternative libffmpeg](https://github.com/electron/libchromiumcontent/issues/198)

### First get the fuck-big code
> new libchromiumcontent script will sync the whole repo
- use mirrors: e.g. https://beijing.source.codeaurora.org
- use agent: for python downing some tools from google

### OR just get source tarball of some stable version
> old libchromiumcontent script support download source tarballs from github release [chromium source tarballs](https://gsdview.appspot.com/chromium-browser-official/?marker=chromium-57.0.2987.97.tar.x%40)


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
> ref to [args_gn.md](args_gn.md), when I add `is_official_build = true`, I got lnk 1248 error: image size are greater than FFFFFFFF

- ffmpeg.gn
```
root_extra_deps = [ "//chromiumcontent:chromiumcontent" ]
is_component_build = false
is_debug = false
enable_nacl = true
enable_widevine = true
proprietary_codecs = true
is_component_ffmpeg = true
ffmpeg_branding = "Chrome"
use_gold = false
enable_ac3_eac3_audio_demuxing = true
enable_hevc_demuxing = true
```

- shared_library.gn
```
root_extra_deps = [ "//chromiumcontent:chromiumcontent" ]
is_electron_build = true
is_component_build = true
is_debug = false
symbol_level = 2
enable_nacl = true
enable_widevine = true
proprietary_codecs = true
is_component_ffmpeg = true
ffmpeg_branding = "Chrome"
use_gold = false
enable_ac3_eac3_audio_demuxing = true
enable_hevc_demuxing = true
```

- static_library.gn
```
root_extra_deps = [ "//chromiumcontent:chromiumcontent" ]
is_electron_build = true
is_component_build = false
is_debug = false
symbol_level = 0
enable_nacl = true
enable_widevine = true
proprietary_codecs = true
is_component_ffmpeg = true
ffmpeg_branding = "Chrome"
use_gold = false
enable_ac3_eac3_audio_demuxing = true
enable_hevc_demuxing = true
```

There is some important args:
```
is_dubug = false
proprietary_codecs = true
enable_nacl = true
remove_webcore_debug_symbols = true
target_cpu = "x64"
symbol_level = 0
ffmpeg_branding = "Chrome"
```

##### this part is almost the same with [hevc_support.md](hevc_support.md)
- src/third_party/ffmpeg/ffmpeg_generated.gni
- src/third_party/ffmpeg/chromium/config/chromium/win/x64/config.h(config.asm)
- src/third_party/ffmpeg/chromium/config/chrome/win/x64/config.h(config.asm)

config.asm
```
%define CONFIG_HEVC_DECODER 1
%define CONFIG_HEVC_DEMUXER 1
%define CONFIG_HEVC_PARSER 1
```

config.h
```
#define FFMPEG_CONFIGURATION "--disable-everything --disable-all --disable-doc --disable-htmlpages --disable-manpages --disable-podpages --disable-txtpages --disable-static --enable-avcodec --enable-avformat --enable-avutil --enable-fft --enable-rdft --enable-static --enable-libopus --disable-bzlib --disable-error-resilience --disable-iconv --disable-lzo --disable-network --disable-schannel --disable-sdl --disable-symver --disable-xlib --disable-zlib --disable-securetransport --disable-d3d11va --disable-dxva2 --disable-vaapi --disable-vda --disable-vdpau --disable-videotoolbox --disable-nvenc --disable-cuda --disable-cuvid --enable-decoder='vorbis,libopus,flac' --enable-decoder='pcm_u8,pcm_s16le,pcm_s24le,pcm_s32le,pcm_f32le' --enable-decoder='pcm_s16be,pcm_s24be,pcm_mulaw,pcm_alaw' --enable-demuxer='ogg,matroska,wav,flac' --enable-parser='opus,vorbis,flac' --enable-decoder='hevc' --enable-demuxer='hevc' --enable-parser='hevc --extra-cflags=-I/cygdrive/d/src/src/third_party/opus/src/include --optflags='\"-O2\"' --enable-decoder='theora,vp8' --enable-parser='vp3,vp8' --toolchain=msvc --cpu=opteron --enable-yasm --extra-cflags=-I/cygdrive/d/src/src/third_party/ffmpeg/chromium/include/win --cc='cygwin-wrapper cl' --ld='cygwin-wrapper link' --nm='cygwin-wrapper dumpbin -symbols' --ar='cygwin-wrapper lib'"
#define CONFIG_HEVC_DECODER 1
#define CONFIG_HEVC_DEMUXER 1
#define CONFIG_HEVC_PARSER 1
```


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
> otherwise, you can direct use `script/update -t x64`

#### build by ninja
once the ninja build files are ready, we can use ninja to build, I prefer to
```
python script/build -t x64
```

#### package it
we can manually copy or use the follwing script to package `libchromiumcontent`
```
python create-dist -t x64 --no_zip
```
> if no `--no_zip` parameters, this script will also zip the libraries for later uploading to s3

It will package libs in *dist/main* directory, include `ffmpeg`, `shared_library`, `static_library`, `src` and `LICENSES.chromium.html`, that's what we need to replace in electron default libchromium content libs.


### Add to electron
#### replace libchromiumcontent library
electron v1.7.3 source directory: */vender/download/libchromiumcontent/*
- ffmpeg
- static_library
- shared_library

#### compile electron
```
python script/build.py -c Release
python script/create-dist.py
```

When successed, you will find electron builds in `out/R` and `dist` directories.


## Now Enjoy your electron with full codecs enabled

