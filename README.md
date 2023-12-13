# IIPImage Server
## Usage:
Example url:
http://localhost:34010/fcgi-bin/iipsrv.fcgi?IIIF={dataDivider}/{resource-id}/info.json
http://localhost:34010/fcgi-bin/iipsrv.fcgi?FIF=systemOne/binary:binary:89071241629273-jp2&SDS=1&JTL=4,5

## Server Configuration
Startup variables

There are a number of configuration options for the server. However, these are all optional and the default settings are sufficient in most cases. If you require logging, you may wish to set the LOGFILE and VERBOSITY parameters. The full list of options are the following:

- LOGFILE The log file the module will (attempt) to write to. If no value is given, no log will be written. Make sure the server process has write access to this directory. Paths with spaces in them may not work correctly. Version 1.1 and later of iipsrv also supports logging to syslog if “syslog” is given as the value.
- VERBOSITY The level of logging. 0 means no logging, 1 is minimal logging, 2 lots of debugging stuff and 3 even more debugging stuff and 6 a very large amount indeed 😉 Logging is only enabled if LOGFILE has also been defined.
- JPEG_QUALITY The default JPEG quality factor for compression when the client does not specify one. The value should be between 1 (highest level of compression) and 100 (highest image quality). The default is 75.
- PNG_QUALITY (iipsrv-1.2 or later) The default PNG quality factor for compression when the client does not specify one. The value should be between 1 (highest level of compression) and 9 (highest image quality). The default is 1.
- WEBP_QUALITY (iipsrv-1.2 or later) The default WebP quality factor for compression when the client does not specify one. The value should be between 0 (highest level of compression) and 100 (highest image quality). The default is 50.
- MAX_IMAGE_CACHE_SIZE Max image cache size to be held in RAM in MB. This is a cache of the compressed JPEG image tiles requested by the client. The default is 10MB.
- FILESYSTEM_PREFIX This is a prefix automatically added by the server to the beginning of each file system path. This can be useful for security reasons to limit access to certain sub-directories. For example, with a prefix of “/home/images/” set on the server, a request by a client for “image.tif” will point to the path “/home/images/image.tif”.  Any reverse directory path component such as ../ is also filtered out. No default value.
- FILESYSTEM_SUFFIX (iipsrv-1.2 or later) This  is a suffix added to the end of each file system path. It can be combined with FILESYSTEM_PREFIX. It is not used in combination with FILENAME_PATTERN. If e.g. this is set to “.tif”, an image URL such as  “/UUID” will look for  “${FILESYSTEM_PREFIX}/UUID.tif”. In the IIIF info.json document, the image @id will be set without the “.tif” suffix.
- MAX_CVT Limits the maximum image dimensions in pixels (the WID or HEI commands) allowable for dynamic JPEG export via the CVT command. This prevents huge requests from overloading the server. The default is 5000.
- MAX_LAYERS The maximum number of quality layers to decode for images that support progressive quality encoding, such as JPEG2000. Ignored for other file formats. If not set, half of the available quality layers will be decoded by default. If set to -1, all the available layers will be decoded by default.
- FILENAME_PATTERN Pattern that follows the name stem for a multispectral or 3D panoramic image sequence. eg: “_pyr_” for FZ1_pyr_000_090.tif. In this example, just supply FZ1 to the FIF command. The “000” indicates the vertical angle and “090” the horizontal. This is only relevent to such multiple image sources. The default is “_pyr_”.
- WATERMARK (iipsrv-0.9.9 or later) TIFF image to use as watermark file. This image should be not be bigger the tile size used for TIFF tiling. If bigger, it will simply be cropped to the tile size. If smaller, the watermark will be positioned randomly within the available space. The image can be either colour or grayscale. See this section on watermarking for more information on how to create and use watermarks with iipsrv.
- WATERMARK_PROBABILITY (iipsrv-0.9.9 or later) The probability that a particilar tile will have a watermark applied to it. 0 means never, 1 means always.
- WATERMARK_OPACITY (iipsrv-0.9.9 or later) The opacity (between 0 and 1) applied to the watermark image.
- MEMCACHED_SERVERS (iipsrv-0.9.9 or later) A comma-delimitted list of memcached servers with optional port numbers. For example: “localhost,192.168.0.1:8888,192.168.0.2”.
- MEMCACHED_TIMEOUT (iipsrv-0.9.9 or later) Time in seconds that cache remains fresh. Default is 86400 seconds (24 hours).
- INTERPOLATION (iipsrv-1.0 or later) Interpolation method to use for rescaling when using image export. Integer value. 0 for fastest nearest neighbour interpolation. 1 for bilinear interpolation (better quality but about 2.5x slower). Bilinear by default.
- CORS (iipsrv-1.0 or later) Cross Origin Resource Sharing setting. Sets the Access-Control-Allow-Origin status on the server for AJAX requests. Use “*” for full public access or specify a particular IP address or domain. Not set by default. See the W3C specification or this tutorial for more details.
- BASE_URL (iipsrv-1.0 or later) Set the public base URL the server is accessed from in case URL rewriting is taking place. Used for certain metadata responses with, for example, the IIIF protocol.
- CACHE_CONTROL (iipsrv-1.0 or later) Set the HTTP Cache-Control header. See http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9 for a full list of options. If not set, header defaults to “max-age=86400” (24 hours).
- ALLOW_UPSCALING (iipsrv-1.1 or later) Determines whether an image may be rendered at a size greater than that of the source image. A value of 0 will prevent upscaling. The default is 1 (upscaling is allowed).
- EMBED_ICC (iipsrv-1.1 or later) Set whether the ICC profile is embedded within the output image. 0 to strip profile, 1 to embed profile. The default is 1 (embedded profiles).
- URI_MAP (iipsrv-1.1 or later) Set a mapping from a URL prefix to a supported protocol. This enables iipsrv to be able to work without requiring full CGI query strings. Map must be of the form “prefix=>protocol” where prefix can be either empty or any string prefix and protocol must be one of IIP, IIIF, DeepZoom, Zoomify. Used, for example, to map requests of the form http://server/iiif/ to the IIIF protocol handler without requiring web server rewriting. See the clean URLs section for further details.
- KAKADU_READMODE  (iipsrv-1.1 or later) Set the Kakadu JPEG2000 read-mode. 0 for ‘fast’ mode with minimal error checking (default), 1 for ‘fussy’ mode with no error recovery, 2 for ‘resilient’ mode with maximum recovery from codestream errors. See the Kakadu documentation for further details.
- OMP_NUM_THREADS Set the number of OpenMP threads to be used by the iipsrv image processing routines (See OpenMP specification for details). All available processor threads are used by default.
- IIIF_VERSION (iipsrv-1.2 or later) Set the major IIIF Image API version. Values should be a single digit. For example: 2 for versions 2 or 2.1 etc. 3 for IIIF version 3.x. If not set, defaults to version IIIF 3.x
- IIIF_DELIMITER (iipsrv-1.3 or later) Set delimiter to enable page or slice selection for a multi-page or image stack for IIIF requests. Delimiter can be a single character or an arbitrary string. Disabled by default.
- DECODER_MODULES Comma separated list of external modules for decoding other image formats. This is only necessary if you have activated --enable-modules for ./configure and written your own image format handler(s).

Read more here: https://iipimage.sourceforge.io/documentation/server
https://iipimage.sourceforge.io/docs/iipsrv/index.html
