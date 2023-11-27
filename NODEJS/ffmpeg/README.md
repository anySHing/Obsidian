# FFmpeg를 활용해 썸네일 생성하기

## FFmpeg란?

공식 문서상의 내용 ⬇️

> A complete, cross-platform solution to record, convert and stream audio and video.
>> 오디오 및 비디오를 녹화, 변환 및 스트리밍하는 완벽한 크로스 플랫폼 솔루션

<br />

이를 node.js 서버에서 쉽게 활용하기 위한 라이브러리로 `fluent-ffmpeg`가 있다.

> 

## 설치

`$ npm i fluent-ffmpeg`

> 라이브러리를 사용하기 전에 시스템에 'ffmpeg'가 설치되어 있어야 한다!

## FFmpeg 커맨드 생성

```ts
import FfmpegCommand from 'fluent-ffmpeg';
const command = new FfmpegCommand();

// 또는 new를 사용하지 않고도 만들 수 있다.
import ffmpeg from 'fluent-ffmpeg';
const command = ffmpeg();

// 그냥 사용할 수도 있음
const myFunction = () => {
  ffmpeg.ffprobe(myVideo, (err, metadata) => {
    ...
  })
}
```

## 비디오 파일 메타데이터 읽어오기

`ffprobe` 메서드를 활용하여 메타데이터를 읽어올 수 있다.

```ts
ffmpeg.ffprobe('/path/to/file.avi', function(err, metadata) {
    console.dir(metadata);
/* 이런 형식의 데이터가 담겨있다.
  {
   streams: [
     {
       index: 0,
       codec_name: 'h264',
       codec_long_name: 'H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10',
       profile: 'High',
       codec_type: 'video',
       codec_tag_string: 'avc1',
       codec_tag: '0x31637661',
       width: 1920,
       height: 1080,
       coded_width: 1920,
       coded_height: 1080,
       closed_captions: 0,
       film_grain: 0,
       has_b_frames: 2,
       sample_aspect_ratio: 'N/A',
       display_aspect_ratio: 'N/A',
       pix_fmt: 'yuv420p',
       level: 40,
       color_range: 'tv',
       color_space: 'bt709',
       color_transfer: 'bt709',
       color_primaries: 'bt709',
       chroma_location: 'left',
       field_order: 'progressive',
       refs: 1,
       is_avc: 'true',
       nal_length_size: 4,
       id: '0x1',
       r_frame_rate: '30/1',
       avg_frame_rate: '30/1',
       time_base: '1/30',
       start_pts: 0,
       start_time: 0,
       duration_ts: 298,
       duration: 9.933333,
       bit_rate: 4987779,
       max_bit_rate: 'N/A',
       bits_per_raw_sample: 8,
       nb_frames: 298,
       nb_read_frames: 'N/A',
       nb_read_packets: 'N/A',
       extradata_size: 49,
       tags: [Object],
       disposition: [Object]
     }
   ],
   format: {
     filename: 'uploads/1697779716773_1575633531391_Space - 21542.mp4',
     nb_streams: 1,
     nb_programs: 0,
     format_name: 'mov,mp4,m4a,3gp,3g2,mj2',
     format_long_name: 'QuickTime / MOV',
     start_time: 0,
     duration: 9.933333,
     size: 6197135,
     bit_rate: 4990981,
     probe_score: 100,
     tags: {
       major_brand: 'mp42',
       minor_version: '0',
       compatible_brands: 'mp42mp41isomavc1',
       creation_time: '2019-02-22T12:22:26.000000Z'
     }
   },
   chapters: []
 }
*/
});
```

> 출처

- [ffmpeg.org](https://www.ffmpeg.org/)
- [fluent-ffmpeg](https://www.npmjs.com/package/fluent-ffmpeg)