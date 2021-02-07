---
title: "Viewing image metadata"
date: 2021-01-06T16:05:08-06:00
toc: false
images:
categories:
  - tech
tags: 
  - exif
  - image
  - metadata
---

# Overview

In addition to the images themselves, digital pictures contain a wealth of metadata information from the type of camera, resolution, GPS coordinates, date, etc.

Below is output from the following tools: `macOS preview`, `exif tools`, and `imagemagick`.  Some tools, mainly from the CLI, tend to return more information.  


# Details

## macOS Preview

![](/images/2021-01-06-16-18-33.png)

## EXIF tools

```
jemurray@shell:~$ exif DSC02149.JPG
EXIF tags in 'DSC02149.JPG' ('Intel' byte order):
--------------------+----------------------------------------------------------
Tag                 |Value
--------------------+----------------------------------------------------------
Image Description   |
Manufacturer        |SONY
Model               |CYBERSHOT
Orientation         |Top-left
X-Resolution        |72
Y-Resolution        |72
Resolution Unit     |Inch
Date and Time       |2007:12:05 12:04:18
YCbCr Positioning   |Co-sited
Compression         |JPEG compression
Manufacturer        |SONY
Model               |CYBERSHOT
Orientation         |Top-left
X-Resolution        |72
Y-Resolution        |72
Resolution Unit     |Inch
Date and Time       |2007:12:05 12:04:18
Exposure Time       |1/400 sec.
F-Number            |f/2.8
Exposure Program    |Normal program
ISO Speed Ratings   |100
Exif Version        |Exif Version 2.1
Date and Time (Origi|2007:12:05 12:04:18
Date and Time (Digit|2007:12:05 12:04:18
Components Configura|Y Cb Cr -
Compressed Bits per | 2
Exposure Bias       |0.00 EV
Maximum Aperture Val|2.00 EV (f/2.0)
Metering Mode       |Spot
Light Source        |Unknown
Flash               |Flash did not fire
Focal Length        |15.8 mm
Maker Note          |54 bytes undefined data
FlashPixVersion     |FlashPix Version 1.0
Color Space         |sRGB
Pixel X Dimension   |1600
Pixel Y Dimension   |1200
File Source         |DSC
Scene Type          |Directly photographed
Interoperability Ind|R98
Interoperability Ver|0100
--------------------+----------------------------------------------------------
EXIF data contains a thumbnail (4257 bytes).
```

## Imagemagick

```
jemurray@shell:~$ identify -verbose DSC02149.JPG
Image: DSC02149.JPG
  Format: JPEG (Joint Photographic Experts Group JFIF format)
  Mime type: image/jpeg
  Class: DirectClass
  Geometry: 1600x1200+0+0
  Resolution: 72x72
  Print size: 22.2222x16.6667
  Units: PixelsPerInch
  Colorspace: sRGB
  Type: TrueColor
  Base type: Undefined
  Endianess: Undefined
  Depth: 8-bit
  Channel depth:
    red: 8-bit
    green: 8-bit
    blue: 8-bit
  Channel statistics:
    Pixels: 1920000
    Red:
      min: 0  (0)
      max: 255 (1)
      mean: 117.89 (0.462316)
      standard deviation: 59.8836 (0.234838)
      kurtosis: -1.09441
      skewness: 0.279962
      entropy: 0.950011
    Green:
      min: 0  (0)
      max: 251 (0.984314)
      mean: 117.175 (0.459511)
      standard deviation: 61.3972 (0.240773)
      kurtosis: -1.13271
      skewness: 0.30788
      entropy: 0.946928
    Blue:
      min: 0  (0)
      max: 255 (1)
      mean: 117.032 (0.45895)
      standard deviation: 68.8315 (0.269928)
      kurtosis: -1.15877
      skewness: 0.39023
      entropy: 0.958954
  Image statistics:
    Overall:
      min: 0  (0)
      max: 255 (1)
      mean: 117.366 (0.460259)
      standard deviation: 63.3708 (0.248513)
      kurtosis: -1.10722
      skewness: 0.335183
      entropy: 0.951965
  Rendering intent: Perceptual
  Gamma: 0.454545
  Chromaticity:
    red primary: (0.64,0.33)
    green primary: (0.3,0.6)
    blue primary: (0.15,0.06)
    white point: (0.3127,0.329)
  Background color: white
  Border color: srgb(223,223,223)
  Matte color: grey74
  Transparent color: black
  Interlace: None
  Intensity: Undefined
  Compose: Over
  Page geometry: 1600x1200+0+0
  Dispose: Undefined
  Iterations: 0
  Compression: JPEG
  Quality: 94
  Orientation: TopLeft
  Properties:
    date:create: 2021-01-06T22:02:35+00:00
    date:modify: 2021-01-06T22:02:35+00:00
    exif:ColorSpace: 1
    exif:ComponentsConfiguration: 1, 2, 3, 0
    exif:CompressedBitsPerPixel: 2/1
    exif:DateTime: 2007:12:05 12:04:18
    exif:DateTimeDigitized: 2007:12:05 12:04:18
    exif:DateTimeOriginal: 2007:12:05 12:04:18
    exif:ExifOffset: 218
    exif:ExifVersion: 48, 50, 49, 48
    exif:ExposureBiasValue: 0/10
    exif:ExposureProgram: 2
    exif:ExposureTime: 10/4000
    exif:FileSource: 3
    exif:Flash: 0
    exif:FlashPixVersion: 48, 49, 48, 48
    exif:FNumber: 28/10
    exif:FocalLength: 158/10
    exif:ImageDescription:
    exif:InteroperabilityOffset: 642
    exif:LightSource: 0
    exif:Make: SONY
    exif:MakerNote: 83, 79, 78, 89, 32, 68, 83, 67, 32, 0, 0, 0, 1, 0, 0, 14, 7, 0, 28, 0, 0, 0, 102, 2, 0, 0, 80, 114, 105, 110, 116, 73, 77, 0, 48, 49, 48, 48, 0, 0, 2, 0, 2, 0, 1, 0, 0, 0, 1, 1, 0, 0, 0, 0
    exif:MaxApertureValue: 20/10
    exif:MeteringMode: 3
    exif:Model: CYBERSHOT
    exif:Orientation: 1
    exif:PhotographicSensitivity: 100
    exif:PixelXDimension: 1600
    exif:PixelYDimension: 1200
    exif:ResolutionUnit: 2
    exif:SceneType: 1
    exif:thumbnail:Compression: 6
    exif:thumbnail:DateTime: 2007:12:05 12:04:18
    exif:thumbnail:InteroperabilityIndex: R98
    exif:thumbnail:InteroperabilityVersion: 48, 49, 48, 48
    exif:thumbnail:JPEGInterchangeFormat: 851
    exif:thumbnail:JPEGInterchangeFormatLength: 4257
    exif:thumbnail:Make: SONY
    exif:thumbnail:Model: CYBERSHOT
    exif:thumbnail:Orientation: 1
    exif:thumbnail:ResolutionUnit: 2
    exif:thumbnail:XResolution: 72/1
    exif:thumbnail:YResolution: 72/1
    exif:XResolution: 72/1
    exif:YCbCrPositioning: 2
    exif:YResolution: 72/1
    icc:copyright: Copyright 2003 Apple Computer Inc., all rights reserved.
    icc:description: Camera RGB Profile
    jpeg:colorspace: 2
    jpeg:sampling-factor: 2x1,1x1,1x1
    signature: 69746be0dd2ec1e5e8658391db4e9faa64168ac47249d92a7a877a0f7c5350d1
  Profiles:
    Profile-exif: 5115 bytes
    Profile-icc: 1352 bytes
  Artifacts:
    filename: DSC02149.JPG
    verbose: true
  Tainted: False
  Filesize: 897923B
  Number pixels: 1920000
  Pixels per second: 48MB
  User time: 0.040u
  Elapsed time: 0:01.040
  Version: ImageMagick 6.9.10-23 Q16 x86_64 20190101 https://imagemagick.org
```