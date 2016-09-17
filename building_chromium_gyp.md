## Building Chromium x64 with Visual Studio 2015 Update 3
 
#### Requirements: 
    - Windows x64
    - 8GB RAM or more
    - free space ~30GB (sources ~20GB, each build (release/debug x32/x64) ~10GB)
    - MS Visual Studio 2015 with Update 3 (custom install, check "Visual C++" and "Win10 SDK")
 
Download https://storage.googleapis.com/chrome-infra/depot_tools.zip and extract it (i.e. D:\depot_tools)
 
##### you can build with API keys or without:
1. without keys you must alter GYP_DEFINES to:
    - google_api_key='no' google_default_client_id='no' google_default_client_secret='no'
2. with your API keys set:
    - google_api_key='<your_api_key>' google_default_client_id='<your_client_id>' google_default_client_secret='<your_client_secret>'
    - *more info: https://www.chromium.org/developers/how-tos/api-keys*
3. with google API keys set use_official_google_api_keys=1 and remove parameters above
    - *IMPORTANT: you need google API keys in D:\depot_tools\src\google_apis\internal\google_chrome_api_keys.h*
 
##### let's try to build the chromium without a google services:
- run CMD.exe

```batch
cd /D D:\depot_tools
set PATH=D:\depot_tools;%PATH%
set DEPOT_TOOLS_WIN_TOOLCHAIN=0
set GYP_DEFINES=target_arch=x64 proprietary_codecs=1 fastbuild=2 remove_webcore_debug_symbols=1 ffmpeg_branding=Chrome google_api_key='no' google_default_client_id='no' google_default_client_secret='no'
set GYP_MSVS_VERSION=2015
gclient
fetch --nohooks chromium --nosvn=True
```
    - *it will be downloaded ~20GB*
```batch
cd /D D:\depot_tools\src
git checkout lkgr
git pull
gclient sync --with_branch_heads -f -R
ninja -C out/Release_x64 mini_installer.exe
```
 
#### now we have two files:
    - D:\depot_tools\src\out\Release_x64\mini_installer.exe
    - D:\depot_tools\src\out\Release_x64\chrome.7z
 
#### update the sources and make the new build:
- run CMD.exe
 
```batch
cd /D D:\depot_tools\src
set PATH=D:\depot_tools;%PATH%
set DEPOT_TOOLS_WIN_TOOLCHAIN=0
set GYP_DEFINES=target_arch=x64 proprietary_codecs=1 fastbuild=2 remove_webcore_debug_symbols=1 ffmpeg_branding=Chrome google_api_key='no' google_default_client_id='no' google_default_client_secret='no'
set GYP_MSVS_VERSION=2015
git checkout lkgr
git pull
gclient sync --with_branch_heads -f -R
ninja -C out/Release_x64 mini_installer.exe
```

#### we've got two updated files:
    - D:\depot_tools\src\out\Release_x64\mini_installer.exe
    - D:\depot_tools\src\out\Release_x64\chrome.7z
 
## Done!
