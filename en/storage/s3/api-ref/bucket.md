---
title: What S3 API methods can be used to manage {{ objstorage-full-name }} buckets
description: In this article, you will learn what S3 API methods can be used to manage buckets.
---

# All Bucket methods

#|
|| **Method** | **Description** ||
|| [create](bucket/create.md) | Creates a bucket. ||
|| [getMeta](bucket/getmeta.md) | Checks the existence of a bucket and access to it. ||
|| [listObjects](bucket/listobjects.md) | Returns a list of bucket objects. ||
|| [listBuckets](bucket/list.md) | Returns a list of buckets. ||
|| [deleteBucket](bucket/delete.md) | Deletes a bucket. ||


|| [getBucketEncryption](bucket/getbucketencryption.md) | Checks whether the bucket is encrypted. ||
|| [deleteBucketEncryption](bucket/deletebucketencryption.md) | Removes encryption from the bucket. ||
|| [putBucketEncryption](bucket/putbucketencryption.md) | Adds encryption to the bucket. ||


|| [putBucketVersioning](bucket/putBucketVersioning.md) | Enables or suspends versioning of the bucket. ||
|| [getBucketVersioning](bucket/getBucketVersioning.md) | Returns the versioning status. ||
|| [putBucketLogging](bucket/putBucketLogging.md) | Enables and disables bucket logging.


||
|| [getBucketLogging](bucket/getBucketLogging.md) | Returns the settings for bucket logging.


||
|| [listObjectVersions](bucket/listObjectVersions.md) | Returns metadata for all versions of objects in the bucket. ||
|| [putObjectLockConfiguration](bucket/putobjectlockconfiguration.md) | Sets up the mechanism of [object version locks](../../concepts/object-lock.md) in a [versioned bucket](../../concepts/versioning.md). ||
|| [getObjectLockConfiguration](bucket/getobjectlockconfiguration.md) | Returns the lock configuration. ||
|| [putBucketTagging](bucket/putbuckettagging.md) | Adds [labels](../../concepts/tags.md) to a bucket. ||
|| [getBucketTagging](bucket/getbuckettagging.md) | Returns bucket labels. ||
|| [deleteBucketTagging](bucket/deletebuckettagging.md) | Deletes bucket labels. ||
|#

{% include [the-s3-api-see-also-include](../../../_includes/storage/the-s3-api-see-also-include.md) %}