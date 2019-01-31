# 外挂字幕组件库（subtitle）集成文档

## 概述
subtitle是一个Android平台视频播放多字幕支持库，几乎支持所有的Android版本，可以需要外挂字幕中的项目集成，支持.SRT、.SCC、.ASS、.STL、.TTML格式的字幕文件。

## 集成方式
1. 在播放器布局文件中添加`SimpleSubtitleView`

```
<com.fonsview.subtitle.widget.SimpleSubtitleView
        android:id="@+id/subtitle_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:gravity="center"
        android:text="字幕将在这里显示"
        android:textColor="#ffffff"
        android:textSize="26sp"
        android:textStyle="bold"/>

```

2. 绑定`MediaPlayer`到`SimpleSubtitleView`

```
 private SimpleSubtitleView mSubtitleView;
    ....省略无关代码.....
  mVideoView.setOnPreparedListener(new MediaPlayer.OnPreparedListener() {
             @Override
             public void onPrepared(final MediaPlayer mp) {
                 // 绑定MediaPlayer
                 mSubtitleView.bindToMediaPlayer(mp);
                 // 设置字幕
                 mSubtitleView.setSubtitlePath(SUBTITLE_URL);
             }
         });
   mVideoView.setVideoURI(Uri.parse(VIDEO_URL));

    ....省略无关代码.....


   @Override
   protected void onDestroy() {
         mSubtitleView.destroy(); // 记得销毁
         super.onDestroy();
   }

```

`SimpleSubtitleView`还有其他与`Activity`生命周期相似的方法：`start()`，`pause()`，`resume`，`stop`，`reset()` 可以根据具体集成情况在适当的地方进行调用。

## 自定义字幕显示控件
如果不想使用提供的`SimpleSubtitleView`控件，你还可以轻松自定义你自己的显示控件，只需通过
`DefaultSubtitleEngine`来辅助就能办到

```
....
private SubtitleEngine mSubtitleEngine = new DefaultSubtitleEngine();

mSubtitleEngine.setOnSubtitlePreparedListener(new OnSubtitlePreparedListener() {
        @Override
        public void onSubtitlePrepared(@Nullable final List<Subtitle> subtitles) {
                // 启动SubtitleEngine
                mSubtitleEngine.start();
           }
        });

mSubtitleEngine.setOnSubtitleChangeListener(new OnSubtitleChangeListener() {
        @Override
         public void onSubtitleChanged(@Nullable final Subtitle subtitle) {
                // 拿到Subtitle对象来刷新你自定义过的字幕显示控件，注意subtitle可能为空
                // 当subtitle为空时，你应该清除自定义控件已显示的字幕显示
                .......
           }
        });
....
```

自定义的最后一步就是通过`DefaultSubtitleEngine`的生命周期相应方法：`start()`，`pause()`，`resume`，`stop`，`reset()`处理好控件的生命周期，以免导致bug。