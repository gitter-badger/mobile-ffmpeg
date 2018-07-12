# MobileFFmpeg [![Join the chat at https://gitter.im/mobile-ffmpeg/Lobby](https://badges.gitter.im/mobile-ffmpeg/Lobby.svg)](https://gitter.im/mobile-ffmpeg/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Source code and scripts to build FFmpeg for Android and IOS platforms

### 1. Features
- Builds both Android and IOS
- Supports 
    - FFmpeg `v3.4.x` and `v4.0.x` releases
    - 23 external libraries.
    
        `fontconfig`, `freetype`, `fribidi`, `gmp`, `gnutls`, `kvazaar`, `lame`, `libaom`, `libass`, `libiconv`, 
        `libilbc`, `libtheora`, `libvorbis`, `libvpx`, `libwebp`, `libxml2`, `opencore-amr`, `opus`, `shine`, `snappy`, 
        `soxr`, `speex`, `wavpack`
    
    - 2 external libraries with GPL license
    
        `x264`, `xvidcore`

- Exposes FFmpeg capabilities both directly from FFmpeg libraries and through MobileFFmpeg wrapper library
- Creates shared libraries (.so for Android, .dylib for IOS)
- Includes cross-compile instructions for 35 open-source libraries
    
    `expat`, `ffmpeg`, `fontconfig`, `freetype`, `fribidi`, `giflib`, `gmp`, `gnutls`, `kvazaar`, `lame`, `libaom`,
    `libass`, `libiconv`, `libilbc`, `libjpeg`, `libjpeg-turbo`, `libogg`, `libpng`, `libtheora`, `libuuid`, `libvorbis`, 
    `libvpx`, `libwebp`, `libxml2`, `nettle`, `opencore-amr`, `opus`, `shine`, `snappy`, `soxr`, `speex`, `tiff`, 
    `wavpack`, `x264`, `xvidcore`
    
- Prebuilt binaries under `JCenter` and `CocoaPods`
- Licensed under LGPL 3.0, can be customized to support GPL v3.0
#### 1.1 Android
- Supports `arm-v7a`, `arm-v7a-neon`, `arm64-v8a`, `x86` and `x86_64` architectures
- Creates Android archive with .aar extension
#### 1.2 IOS
- Supports `armv7`, `armv7s`, `arm64`, `i386` and `x86_64` architectures
- Builds with `-fembed-bitcode` flag
- Creates IOS dynamic universal (fat) library
- Creates IOS dynamic framework for IOS 8 or later

### 2. Using
Prebuilt libraries are available under [Github](https://github.com/tanersener/mobile-ffmpeg/releases), [JCenter](https://bintray.com/bintray/jcenter) and [CocoaPods](https://cocoapods.org)

There are six different prebuilt packages. Below you can see which external libraries are enabled in each of them.

|        | min | min-gpl | https | https-gpl | full | full-gpl |
| :----: | :----: | :----: | :----: | :----: | :----: | :----: |
| external <br/> libraries <br/> enabled |  -  |  x264* <br/> xvidcore*  |  gnutls  |  gnutls <br/> x264* <br/> xvidcore*  |  fontconfig <br/> freetype <br/> fribidi <br/> gmp <br/> gnutls <br/> kvazaar <br/> lame <br/> libaom** <br/> libass <br/> libiconv <br/> libilbc* <br/> libtheora <br/> libvorbis <br/> libvpx <br/> libwebp <br/> libxml2 <br/> opencore-amr <br/> opus* <br/> shine <br/> snappy* <br/> soxr** <br/> speex <br/> wavpack  | fontconfig <br/> freetype <br/> fribidi <br/> gmp <br/> gnutls <br/> kvazaar <br/> lame <br/> libaom** <br/> libass <br/> libiconv <br/> libilbc* <br/> libtheora <br/> libvorbis <br/> libvpx <br/> libwebp <br/> libxml2 <br/> opencore-amr <br/> opus* <br/> shine <br/> snappy* <br/> soxr** <br/> speex <br/> wavpack <br/> x264* <br/> xvidcore*  |

\* - Supported since `v1.1`

\*\* - Supported since `v2.0`

#### 2.1 Android
1. Add MobileFFmpeg dependency from `jcenter()`
    ```
    dependencies {`
        implementation 'com.arthenica:mobile-ffmpeg-full:2.0'
    }
    ```

2. Use the following source code to execute commands.
    ```
    import com.arthenica.mobileffmpeg.FFmpeg;

    int rc = FFmpeg.execute("-i", "file1.mp4", "-c:v", "libxvid", "file1.avi");
    Log.i(Log.TAG, String.format("Command execution %s.", (rc == 0?"completed successfully":"failed with rc=" + rc));
    ```
#### 2.2 IOS
1. Add MobileFFmpeg pod to your `Podfile`
    ```
    pod 'mobile-ffmpeg-full', '~> 2.0'
    ```

2. Create and execute commands using the following `Objective-C` example.
    ```
    #import <mobileffmpeg/mobileffmpeg.h>

    NSString* command = @"-i file1.mp4 -c:v libxvid file1.avi";
    NSArray* commandArray = [command componentsSeparatedByString:@" "];
    char **arguments = (char **)malloc(sizeof(char*) * ([commandArray count]));
    for (int i=0; i < [commandArray count]; i++) {
        NSString *argument = [commandArray objectAtIndex:i];
        arguments[i] = (char *) [argument UTF8String];
    }

    int result = mobileffmpeg_execute((int) [commandArray count], arguments);

    NSLog(@"Process exited with rc %d\n", result);
    
    free(arguments);
    ```
#### 2.3 Test Application
You can see how MobileFFmpeg is used inside an application by running test applications provided.
There is an Android test application under the `android/test-app` folder and an IOS test application under the 
`ios/test-app` folder. Both applications are identical and supports command execution and video encoding.

### 3. Versions

- `MobileFFmpeg v1.x` is the current stable, includes `FFmpeg v3.4.2`

- `MobileFFmpeg v2.x` is the next stable, includes `FFmpeg v4.0.1`

### 4. Building
#### 4.1 Prerequisites
1. Use your package manager (apt, yum, dnf, brew, etc.) to install the following packages.

    ```
    autoconf automake libtool pkg-config curl cmake gcc gperf texinfo yasm nasm
    ```
Some of these packages are not mandatory for the default build.
Please visit [Android Prerequisites](https://github.com/tanersener/mobile-ffmpeg/wiki/Android-Prerequisites) and
[IOS Prerequisites](https://github.com/tanersener/mobile-ffmpeg/wiki/IOS-Prerequisites) for the details.

2. Android builds require these additional packages.
    - **Android SDK 5.0 Lollipop (API Level 21)** or later
    - **Android NDK r16b** or later with LLDB and CMake
    - **gradle 4.4** or later

3. IOS builds need these extra packages and tools.
    - **IOS SDK 7.0.x** or later
    - **Xcode 8.x** or later
    - **Command Line Tools**
    - **lipo** utility

#### 4.2 Build Scripts
Use `android.sh` and `ios.sh` to build MobileFFmpeg for each platform.
After a successful build, compiled FFmpeg and MobileFFmpeg libraries can be found under `prebuilt` directory.

Both `android.sh` and `ios.sh` can be customized to override default settings,
[android.sh](https://github.com/tanersener/mobile-ffmpeg/wiki/android.sh) and
[ios.sh](https://github.com/tanersener/mobile-ffmpeg/wiki/ios.sh) wiki pages include all available build options.
##### 4.2.1 Android 
```
export ANDROID_NDK_ROOT=<Android NDK Path>
./android.sh
```
##### 4.2.2 IOS
```
./ios.sh    
```

#### 4.3 GPL Support
Since`v1.1`, it is possible to enable to GPL licensed libraries `x264` and `xvidcore` from the top level build scripts.
Their source code is not included in the repository and downloaded when enabled.

#### 4.4 External Libraries
`build` directory includes build scripts for external libraries. There are two scripts for each library, one for Android
and one for IOS. They include all options/flags used to cross-compile the libraries. `ASM` is enabled by most of them, 
exceptions are listed under the [ASM Support](https://github.com/tanersener/mobile-ffmpeg/wiki/ASM-Support) page.

### 5. Documentation

A more detailed documentation is available at [Wiki](https://github.com/tanersener/mobile-ffmpeg/wiki).

### 6. License

This project is licensed under the LGPL v3.0. However, if source code is built using optional `--enable-gpl` flag or 
prebuilt binaries with `-gpl` postfix are used then MobileFFmpeg is subject to the GPL v3.0 license.

Source code of FFmpeg and external libraries is included in compliance with their individual licenses.

`strip-frameworks.sh` script included and distributed is published under the Apache License version 2.0.

Digital assets used in test applications are published in the public domain.

Please visit [License](https://github.com/tanersener/mobile-ffmpeg/wiki/License) page for the details.

### 7. Contributing

If you have any recommendations or ideas to improve it, please feel free to submit issues or pull requests. Any help is appreciated.

### 8. See Also

- [libav gas-preprocessor](https://github.com/libav/gas-preprocessor/raw/master/gas-preprocessor.pl)
- [FFmpeg API Documentation](https://ffmpeg.org/doxygen/3.4/index.html)
- [FFmpeg License and Legal Considerations](https://ffmpeg.org/legal.html)
