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
* CDN for storing content across the globe
* Content Uploading Service that consumes created content and then distributes it across CDNs
  * Will also need to store video content metadata in data storage
* End user facing component for playing video content
  * Playback service: responsible for serving playback requests
  * Steering service: determines the optimal CDN urls to serve playback requests

## Data Model

* Need to persist video metadata and subtitles
* Since this metadata might need to be updated frequently, use a document-based store
* Time-series database to store subtitles
  * "Events" are stored with start / end timestamps and subtitle values
* Netflix has a media database which is modeled around the notion of a media timeline

## Component Design

### Content Uploader

* Content creator uploads raw video content
* Content storage service segments the raw video file into chunks, persisting the segments on a file storage system
