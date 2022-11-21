# How to convert audio files to a raw file with the Shazam API in the Shazzboard project?

## Table of content
- [Intro](#intro)
- [What is a .raw file?](what-is-a-raw-file?)
- [What types of audio files are relevant?](what-types-of-audio-files-are-relevant?)
- [What is an uncompressed audio format?](what-is-an-uncompressed-audio-format?)
  

## Intro
In my IP for this semester, I want to add a functionality which allows a user to input an audio file to detect and add to their history of (played) songs. The API I want to use requires the input file to be a `.raw` file for it to detect. To implement this, I will need to research how I can get an `.raw` file from other file formats.

## What is a .raw file?
`.raw` audio files containing uncompressed pulse-code modulation (PCM) values without a metadata header and signature. The stored information within these files can be in any bit depth, sampling rate, ordering, endian-ness and channels.<sup>[[1]](#fmtz-raw-audio-file)</sup>

## What types of audio files are relevant?
Audio file formats can be categorized in three groups: <sup>[[2]](#composerfocus-audio-file-formats)</sup><sup>[[3]](#newsandviews-common-audio-formats)</sup>
- Uncompressed audio format: audio completely un-altered from its original state.
- Compressed audio format with lossless compression: compressed audio without loss of audio data.
- Compressed audio format with lossy compression: compressed audio with date removed to create an even smaller file than with lossless compression.

Since uncompressed audio files are completely un-altered from it's original state and `.raw` is also part of this group, this one seems to be the most relevant in my use-case. While it is possible to retrieve the original data from lossless compressed audio formats, this falls outside of this research's scope and will not be taken into consideration. Lossy compressed audio has some data removed and does not contain the original audio even after decompression, so this will also not be taken into consideration for this research.

## What is an uncompressed audio format?
Uncompressed audio format is is a file that contains PCM data. One major uncompressed audio format is LPCM, a specific type of PCM. Besides a `.raw` file, the LPCM can also be stored in a `.wav` and `.aiff` files on Windows and macOS respectively. Both `.wav` and `.aiff` can store uncompressed, lossless and lossy audio files. The difference that set `.wav` and `.aiff` apart from a `.raw` format is the metadata header contained within the files, placed before the audio data, which contains the data for sample rate, bit depth, endianness and channels. <sup>[[4]](#wikipedia-audio-file-format)</sup>

From the information above, the possible conclusion can be made that uncompressed audio file formats like .wav and .aiff contain the same PCM data as a raw audio file, with an added header containing information such as sample rate, bit depth, endianness and number of channels.

## Conclusion
To get an answer to the research question, I've made a [proof of concept](https://github.com/rmzhen/PoC_AudioConversion) with Java's Java Sound API documentation<sup>[[5]](#java-sound-api)</sup> and Micha Pringle's answer on "How do I convert audio from one format to wav or raw in Java?", where they state that you can create a `.raw` file by stripping the header from a `.wav` file.<sup>[[6]](so-convert-wav-to-raw)</sup> As a test of the PoC, I've recorded a snippet of Efteling's [Indische Waterlelies](https://www.youtube.com/watch?v=RzWPvgccYQ8) song. The PoC returns a `.txt` file in Byte array and Base64 string for both the converted `.wav` and `.raw` files. These files can be found here:
- [Byte array `.wav` file](https://github.com/rmzhen/S3-Portfolio/blob/main/files/Efteling.wav-WAV-BA.md)
- [Base64 `.wav` file](https://github.com/rmzhen/S3-Portfolio/blob/main/files/Efteling.wav-WAV-B64.md)
- [Byte array `.raw` file](https://github.com/rmzhen/S3-Portfolio/blob/main/files/Efteling.wav-RAW-BA.md)
- [Base64 `.raw` file](https://github.com/rmzhen/S3-Portfolio/blob/main/files/Efteling.wav-RAW-B64.md)

To test if it works with the Shazam API I'm using for my IP, I've taken the contents of the Base64 `.raw` file and given it as an input in the API's detect endpoint. The API returned the song [Afrikaan Beat by Bert Kaempfert](https://www.youtube.com/watch?v=vGmR2dJSDvo), which is the same song as the Indische Waterlelies song I've used as input. 

![ShazamAPI-result](https://github.com/rmzhen/S3-Portfolio/blob/main/images/audio-conversion-poc-result.png)
*Snippet of the result from ShazamAPI.*

With the answers to the subquestions and the result of my proof of concept, my conclusion is you can convert an audio file to a `.raw` audio file by stripping the header from a uncompressed audio format.

## References
>[1] <a name="fmtz-raw-audio-file"></a> _- Fmtz File Formats Information and Specifications_. (n.d.). https://web.archive.org/web/20160525083851/http://www.fmtz.com/misc/raw-audio-file-formats

>[2] <a name="composerfocus-audio-file-formats"></a> Masalar, M. (2019, February 26). _Audio File Formats: Everything You Need To. . ._ Composer Focus. https://composerfocus.com/audio-file-formats/

>[3] <a name="newsandviews-common-audio-formats"></a> Aleksandersen, D. (n.d.). _Common audio formats: Which one to use?_ https://newsandviews.dataton.com/common-audio-formats-which-one-to-use

>[4] <a name="wikipedia-audio-file-format"></a> Wikipedia contributors. (2022, November 19). _Audio file format_. Wikipedia. https://en.wikipedia.org/wiki/Audio_file_format

>[5] <a name="java-sound-api"></a> _Java Sound Programmer Guide_. (n.d.). https://docs.oracle.com/javase/8/docs/technotes/guides/sound/programmer_guide/contents.html

>[6] <a name="so-convert-wav-to-raw"></a> _How do I convert audio from one format to wav or raw in Java?_ (2020b, March 10). Stack Overflow. https://stackoverflow.com/questions/60626467/how-do-i-convert-audio-from-one-format-to-wav-or-raw-in-java/60627602
