---
title: 'warn: toktx: Texture dimensions 320x320 are NPOT, and may fail in older APIs (including WebGL 1.0) on certain devices.'
date: 2022-05-06
category: 'Error'
draft: false
---

- (`gltf-transform` 노드 모듈을 사용하여) `toktx.exe`가 실행될 때, 일반 gltf 모델을 [ktx2](https://github.khronos.org/KTX-Specification/)를 통해 텍스쳐를 압축한 ktx gltf모델로 변환하던 중 발생할 수 있는 에러
- 다음 에러가 발생한 후 ktx gltf로 변환된 모델을 확인하면 텍스쳐가 붙어있으나, [3d tiles](https://github.com/CesiumGS/3d-tiles) 로 변환했을 때 텍스쳐가 올바르게 붙지 않음을 확인함

## 해결책

```
node gltf-transform etc1s 입력경로 출력경로 --power-of-two

```

- 이와 같이 실행하여 만든 ktx gltf를 tileset으로 변환하면 올바르게 텍스쳐가 붙는 것을 확인할 수 있음

### 참고

`gltf-transform etc1s --help`
를 치면 다음과 같이 ect1s 기능에 대한 옵션값의 정보가 나열된다.

```
  cli.js 2.1.5 — Commandline interface for the glTF-Transform SDK.

  USAGE — etc1s

    ▸ cli.js etc1s <input> <output> [OPTIONS...]


  SUMMARY

    Compresses textures in the given file to .ktx2 GPU textures using the
    ETC1S Basis Universal bitstream. GPU textures offer faster GPU upload
    and less GPU memory consumption than traditional PNG or JPEG textures,
    which are fully uncompressed in GPU memory. GPU texture formats require
    more attention to compression settings to get similar visual results.

    ETC1S, one of the two Basis Universal bitstreams, offers lower size and lower
    quality than UASTC. In some cases it may be useful to increase the resolution
    of the texture, to minimize compression artifacts while still retaining an
    overall smaller filesize. Consider using less aggressive compression settings
    for normal maps than for other texture types: you may want to use UASTC for
    normal maps and ETC1S for other textures, for example.

    Documentation
    https://gltf-transform.donmccurdy.com/extensions.html#khr_texture_basisu

    Dependencies
    KTX-Software (https://github.com/KhronosGroup/KTX-Software/)


  ARGUMENTS

    <input>                              Path to read glTF 2.0 (.glb, .gltf) model
    <output>                             Path to write output

  OPTIONS

    --compression <clevel>               Compression level, an encoding speed vs. quality
                                         tradeoff. Higher values are slower, but give higher
                                         quality. Try --quality before experimenting with this
                                         option.
                                         one of 0,1,2,3,4,5, default: 1
    --filter <filter>                    Specifies the filter to use when generating mipmaps.
                                         one of
                                         "box","tent","bell","b-spline","mitchell",
                                         "lanczos3","lanczos4","lanczos6","lanczos12",
                                         "blackman","kaiser","gaussian","catmullrom",
                                         "quadratic_interp","quadratic_approx","quadratic_
                                         mix", default: "lanczos4"
    --filter-scale <fscale>              Specifies the filter scale to use when generating
                                         mipmaps.
                                         number, default: 1
    --jobs <num_jobs>                    Spawns up to num_jobs instances of toktx
                                         number, default: 12
    --max-endpoints <max_endpoints>      Manually set the maximum number of color endpoint
                                         clusters from 1-16128.
                                         number
    --max-selectors <max_selectors>      Manually set the maximum number of color selector
                                         clusters from 1-16128.
                                         number
    --power-of-two                       Resizes any non-power-of-two textures to the closest
                                         power-of-two dimensions, not exceeding 2048x2048px.
                                         Required for  compatibility on some older devices and
                                         APIs, particularly  WebGL 1.0.
                                         boolean
    --quality <qlevel>                   Quality level. Range is 1 - 255. Lower gives better
                                         compression, lower quality, and faster encoding. Higher
                                         gives less compression, higher quality, and slower
                                         encoding. Quality level determines values of
                                         --max_endpoints and --max-selectors, unless those
                                         values are explicitly set.
                                         number, default: 128
    --rdo-off                            Disable endpoint and selector RDO (slightly faster,
                                         less noisy output, but lower quality per output bit).
                                         boolean
    --rdo-threshold <rdo_threshold>      Set endpoint and selector RDO quality threshold. Lower
                                         is higher quality but less quality per output bit (try
                                         1.0-3.0). Overrides --quality.
                                         number
    --slots <slots>                      Texture slots to include (glob)
                                         default: "*"

  GLOBAL OPTIONS

    -h, --help                           Display global help or command-related help.
    -V, --version                        Display version.
    -v, --verbose                        Verbose mode: will also output debug messages.
    --allow-http                         Allows reads from HTTP requests.
                                         boolean
    --vertex-layout <layout>             Vertex buffer layout preset.
                                         one of "interleaved","separate", default:
                                         "interleaved"
```
