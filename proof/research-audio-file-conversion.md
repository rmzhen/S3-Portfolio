# How to convert audio files to a raw file with the Shazam API in the Shazzboard project?

## Table of content

## Intro


## What is a .raw file?
>Raw files concerned with audio or sampled signals are formats that may be used by some applications or embedded devices to input, process and/or output information. The information is either stored the way it is because:
>
>-   the format is ad-hoc for a one time use,
>-   existing interchange formats cannot preserve the information, or
>-   corresponds to a certain memory organization without decoding.
>
>Generally the discerning aspect of a real raw audio file is the lack of a _signature_ and a _header_. This aspect makes determining a raw audio file's file format much more harder than interchange formats because of a lack of a signature. Also, the lack of a commonly understood header makes determining audio parameters difficult.
>
>The information stored may be in any kind of depth, ordering, endian-ness and alignment. Note that the information could be in the frequency domain with information on partials for each window, as opposed to a time domain sampled sound, depending on the use of the file.
>
>Its common that a _set_ of raw files will have the same sample rate, encoding type, bit depth, alignment, etc. (though its not always the case) That is, a bunch of raw files in a folder and/or similar filenames will have raw files encoded the same way.
>
>Generally raw files will probably be uncompressed (but this is not always true), and they can be 4-bit through to n-bit depths, they may have any amount of channels and they may or may not be interleaved.

*Source: https://web.archive.org/web/20160525083851/http://www.fmtz.com/misc/raw-audio-file-formats*

## What types of audio files exists?
>There are three major groups of audio file formats:
>
>- Uncompressed audio formats, such as WAV, AIFF, AU or raw header-less PCM;
>- Formats with lossless compression, such as FLAC, Monkey's Audio (filename extension .ape), WavPack (filename extension .wv), TTA, ATRAC Advanced Lossless, ALAC (filename extension .m4a), MPEG-4 SLS, MPEG-4 ALS, MPEG-4 DST, Windows Media Audio Lossless (WMA Lossless), and Shorten (SHN).
>- Formats with lossy compression, such as Opus, MP3, Vorbis, Musepack, AAC, ATRAC and Windows Media Audio Lossy (WMA lossy).

*Source: https://en.wikipedia.org/wiki/Audio_file_format#Format_types*

## What is an uncompressed audio format?
>One major uncompressed audio format, LPCM, is the same variety of PCM as used in Compact Disc Digital Audio and is the format most commonly accepted by low level audio APIs and D/A converter hardware. Although LPCM can be stored on a computer as a raw audio format, it is usually stored in a .wav file on Windows or in a .aiff file on macOS. The Audio Interchange File Format (AIFF) format is based on the Interchange File Format (IFF), and the WAV format is based on the similar Resource Interchange File Format (RIFF). WAV and AIFF are designed to store a wide variety of audio formats, lossless and lossy; they just add a small, metadata-containing header before the audio data to declare the format of the audio data, such as LPCM with a particular sample rate, bit depth, endianness and number of channels. Since WAV and AIFF are widely supported and can store LPCM, they are suitable file formats for storing and archiving an original recording. 

*Source: https://en.wikipedia.org/wiki/Audio_file_format#Uncompressed_audio_format*

>The Java sound api does not convert to RAW audio as far as I know. It does convert to WAV, which is [RAW audio with a 44-byte header](https://wiki.fileformat.com/audio/wav/). Once you understand this, you can break the problem down into 2 parts:
>
>**1. Convert audio from any format to a WAV format.**
>
>The code example below has only been tested from WAV to WAV (different audio format), but in theory the call to `AudioSystem.getAudioInputStream(audioFormat, originalAudioStream);` should find the appropriate codec if it has it.
>
>This method will convert audio bytes to the given format and produce a byte[] result as a WAV.
>
>**2. Strip the header from the WAV file created in step 1.**

*Source: Micha Pringle on [How do I convert audio from one format to wav or raw in Java?](https://stackoverflow.com/questions/60626467/how-do-i-convert-audio-from-one-format-to-wav-or-raw-in-java)*

>RAW files are already decoded PCM audio, but `Audio` elements can't play PCM directly. You'll need to append a RIFF/WAV header to the PCM bytes first.

*Source: AnthumChris on [How to Play RAW Audio Files?](https://stackoverflow.com/questions/62093473/how-to-play-raw-audio-files)*

From the information above, the possible conclusion can be made that uncompressed audio file formats like .wav and .aiff contain the same PCM data as a raw audio file, with an added header containing information such as sample rate, bit depth, endianness and number of channels.

## Proof of Concept
I've made a [proof of concept](https://github.com/rmzhen/PoC_AudioConversion) with Java's [Java Sound API documentation](https://docs.oracle.com/javase/8/docs/technotes/guides/sound/programmer_guide/chapter7.html#a114527) and [Micha Pringle's answer on "How do I convert audio from one format to wav or raw in Java?"](https://stackoverflow.com/questions/60626467/how-do-i-convert-audio-from-one-format-to-wav-or-raw-in-java). To test the functionality of the PoC, I've recorded a snippet of Efteling's [Indische Waterlelies](https://www.youtube.com/watch?v=RzWPvgccYQ8) song. The PoC returns a `.txt` file in Byte array and Base64 string for both the converted `.wav` and `.raw` files. These files can be found here:
- [Byte array `.wav` file](https://github.com/rmzhen/S3-Portfolio/blob/main/files/Efteling.wav-WAV-BA.md)
- [Base64 `.wav` file](https://github.com/rmzhen/S3-Portfolio/blob/main/files/Efteling.wav-WAV-B64.md)
- [Byte array `.raw` file](https://github.com/rmzhen/S3-Portfolio/blob/main/files/Efteling.wav-RAW-BA.md)
- [Base64 `.raw` file](https://github.com/rmzhen/S3-Portfolio/blob/main/files/Efteling.wav-RAW-B64.md)

To test if it works with the Shazam API I'm using for my IP, I've taken the contents of the Base64 `.raw` file and given it as an input in the API's detect endpoint. The API returned the song [Afrikaan Beat by Bert Kaempfert](https://www.youtube.com/watch?v=vGmR2dJSDvo), which is the same song as the Indische Waterlelies song I've used as input. 

![ShazamAPI-result](https://github.com/rmzhen/S3-Portfolio/blob/main/images/audio-conversion-poc-result.png)
*Snippet of the result from ShazamAPI.*

## Conclusion
