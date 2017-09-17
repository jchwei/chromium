## HEVC support ##

### For version 61 and above ###

in the file:
- **src/third_party/ffmpeg/ffmpeg_generated.gni**

append to your condition:

```c
ffmpeg_c_sources += [
    "libavcodec/bswapdsp.c",
    "libavcodec/autorename_libavcodec_hevcdec.c",
    "libavcodec/hevc_cabac.c",
    "libavcodec/hevc_data.c",
    "libavcodec/hevc_filter.c",
    "libavcodec/hevc_mvs.c",
    "libavcodec/hevc_parse.c",
    "libavcodec/hevc_parser.c",
    "libavcodec/hevc_ps.c",
    "libavcodec/hevc_refs.c",
    "libavcodec/hevc_sei.c",
    "libavcodec/hevcdsp.c",
    "libavcodec/hevcpred.c",
    "libavcodec/x86/bswapdsp_init.c",
    "libavcodec/x86/hevcdsp_init.c",
    "libavformat/autorename_libavformat_hevc.c",
    "libavformat/hevcdec.c",
]
ffmpeg_yasm_sources += [
    "libavcodec/x86/bswapdsp.asm",
    "libavcodec/x86/hevc_deblock.asm",
    "libavcodec/x86/hevc_idct.asm",
    "libavcodec/x86/hevc_mc.asm",
    "libavcodec/x86/hevc_add_res.asm",
    "libavcodec/x86/hevc_sao.asm",
    "libavcodec/x86/hevc_sao_10bit.asm",
]
```
**then go to `src/third_party/ffmpeg` folder and copy:**
 - `libavcodec/hevcdec.c` to `libavcodec/autorename_libavcodec_hevcdec.c`
 - `libavformat/hevc.c` to `libavformat/autorename_libavformat_hevc.c`

in the files:
- **src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/config.h**
- **src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/config.asm**

add or replace some parameters..

add to existing:
```c
#define FFMPEG_CONFIGURATION "******** --enable-decoder='hevc' --enable-demuxer='hevc' --enable-parser='hevc'"
```

replace to:
```c
#define CONFIG_HEVC_DECODER 1
#define CONFIG_HEVC_DEMUXER 1
#define CONFIG_HEVC_PARSER 1
```

### For version v60 and below ###

in the file:
- **src/third_party/ffmpeg/ffmpeg_generated.gni**

append to your condition:

```c
ffmpeg_c_sources += [
    "libavcodec/bswapdsp.c",
    "libavcodec/hevc.c",
    "libavcodec/hevc_cabac.c",
    "libavcodec/hevc_data.c",
    "libavcodec/hevc_filter.c",
    "libavcodec/hevc_mvs.c",
    "libavcodec/hevc_parse.c",
    "libavcodec/hevc_parser.c",
    "libavcodec/hevc_ps.c",
    "libavcodec/hevc_refs.c",
    "libavcodec/hevc_sei.c",
    "libavcodec/hevcdsp.c",
    "libavcodec/hevcpred.c",
    "libavcodec/x86/bswapdsp_init.c",
    "libavcodec/x86/hevcdsp_init.c",
    "libavformat/hevcdec.c",
]
ffmpeg_yasm_sources += [
    "libavcodec/x86/bswapdsp.asm",
    "libavcodec/x86/hevc_deblock.asm",
    "libavcodec/x86/hevc_idct.asm",
    "libavcodec/x86/hevc_mc.asm",
    "libavcodec/x86/hevc_res_add.asm",
    "libavcodec/x86/hevc_sao.asm",
    "libavcodec/x86/hevc_sao_10bit.asm",
]
```

in the files:
- **src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/config.h**
- **src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/config.asm**

add or replace some parameters..

add to existing:
```c
#define FFMPEG_CONFIGURATION "******** --enable-decoder='hevc' --enable-demuxer='hevc' --enable-parser='hevc'"
```

replace to:
```c
#define CONFIG_HEVC_DECODER 1
#define CONFIG_HEVC_DEMUXER 1
#define CONFIG_HEVC_PARSER 1
```
