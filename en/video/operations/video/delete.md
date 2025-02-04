---
title: How to delete a video in {{ video-full-name }}
description: Follow this guide to delete a {{ video-full-name }} video.
---

# Deleting a video

{% list tabs group=instructions %}

- {{ video-name }} interface {#console}

  1. Open the {{ video-name }} [home page]({{ link-video-main }}).
  1. Select a channel.
  1. On the ![image](../../../_assets/console-icons/circle-play.svg) **{{ ui-key.yacloud_video.videos.title_videos }}** tab, in the row with the video you need, click ![image](../../../_assets/console-icons/ellipsis.svg) and select **{{ ui-key.yacloud_video.common.action_delete }}**.
  1. Click **{{ ui-key.yacloud_video.common.action_delete }}**.

- API {#api}

  Use the [delete](../../api-ref/Video/delete.md) REST API method for the [Video](../../api-ref/Video/index.md) resource or the [VideoService/Delete](../../api-ref/grpc/Video/delete.md) gRPC API call.


{% endlist %}