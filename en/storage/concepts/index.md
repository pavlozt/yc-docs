---
title: '{{ objstorage-full-name }} concepts'
description: This article explains the basic {{ objstorage-name }} concepts.
---

# {{ objstorage-name }} concepts

{{ objstorage-name }} uses the following concepts:


Concept | Description
--------|----------
[Bucket](bucket.md) | {{ objstorage-name }} unit allocated for user data.
[Object](object.md)  | File with data in any format.
[Bucket versioning](versioning.md) | Feature that allows you to store multiple copies of an object in a bucket.
[Object lock](object-lock.md) | Feature that protects an object version against deletion or overwrite.
[Partial object updates](object-patch.md) | Feature that enables partial updating and appending of objects data.
[Encryption](encryption.md) | Data encryption algorithm.
[Bucket object lifecycles](lifecycles.md) | Rules for automatically deleting objects and changing their storage class.
[Pre-signed URLs](pre-signed-urls.md) | Method for granting anonymous users access to storage operations.
[Multipart upload](multipart.md) | Method for uploading large objects.
[Access control list](acl.md) | Tool for granting access to objects and buckets.
[Access policy](policy.md) | Tool for managing permissions to perform actions with objects and buckets.
[Uploading data via an HTML form](presigned-post-forms.md) | Method for uploading objects to a storage from a browser that allows anonymous users to upload objects to buckets.
[Storage class](storage-class.md) | Way to reduce the cost of storing objects by dividing them into frequently accessed objects and long-term storage objects.
[Backups](backup.md) | Process for backing up data stored in {{ objstorage-name }}.
[Labels](tags.md) | Key-value pairs for logical bucket tagging.
[Quotas and limits](limits.md) | Service use restrictions.




See also the {{ yandex-cloud }} [general resource hierarchy](../../resource-manager/concepts/resources-hierarchy.md).
