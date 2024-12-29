# [Designing Netflix](https://highscalability.com/designing-netflix/)

## Capacity Planning

* Active users: 100 million
* Average size of video content uploaded every minute: 2.5 GB
* Resolution and codec formats combinations to support: 10
* Average videos users watch daily: 3
* Most frequently requested service will be the playback service
* Traffic might have peak / trough behavior where many viewers when shows are released
* Size of content stored daily: 2.5 GB x 10 combinations of resolution/codex x 1440 seconds in day -> 36 TB

## High Level Design

* Need to support content creators uploading video content and viewers watching video

