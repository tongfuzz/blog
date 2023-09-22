<!--
    title:ffmpeg命令
    subtitle:ffmpeg命令使用记录
    createDate:2021-9-15
    updateDate:2023-8-12
    tags:音视频,ffmpeg
    imagePath:/img/ffmpeg命令
 -->

## ffmpeg 命令

![ffmpeg命令](ffmpeg%E5%91%BD%E4%BB%A4%E5%A4%A7%E5%85%A8.png)

1.  截取视频

    1.  简单命令：

        > ffmpeg -i input.mp4 -ss 1:05 -t 10 output.mp4

        -ss 1:05 指定从输入视频第 1:05 秒开始截取，-t 10 指明最多截取 10 秒

    2.  较快命令:

        > ffmpeg -ss 1:05 -i input.mp4 -t 10 -c:v copy -c:a copy output.mp4

        把-ss 1:05 放到-i 前面，与原来的区别是，这样会先跳转到第 1:05 秒在开始解码输入视频，而原来的会从开始解码，只是丢弃掉前 1:05 秒的结果。

        -c:v 和 -c:a 分别指定视频和音频的编码格式。  
        -c:v copy -c:a copy 标示视频与音频的编码不发生改变，而是直接复制，这样会大大提升速度
