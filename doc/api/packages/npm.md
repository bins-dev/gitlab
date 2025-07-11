---
stage: Package
group: Package Registry
info: To determine the technical writer assigned to the Stage/Group associated with this page, see https://handbook.gitlab.com/handbook/product/ux/technical-writing/#assignments
title: npm API
---

{{< details >}}

- Tier: Free, Premium, Ultimate
- Offering: GitLab.com, GitLab Self-Managed, GitLab Dedicated

{{< /details >}}

This is the API documentation for [npm Packages](../../user/packages/npm_registry/_index.md).

{{< alert type="warning" >}}

This API is used by the [npm package manager client](https://docs.npmjs.com/)
and is not meant for manual consumption.

{{< /alert >}}

For instructions on how to upload and install npm packages from the GitLab
package registry, see the [npm package registry documentation](../../user/packages/npm_registry/_index.md).

{{< alert type="note" >}}

These endpoints do not adhere to the standard API authentication methods.
See the [npm package registry documentation](../../user/packages/npm_registry/_index.md)
for details on which headers and token types are supported. Undocumented authentication methods might be removed in the future.

{{< /alert >}}

## Download a package

Downloads the npm package. This URL is provided by the [metadata endpoint](#metadata).

```plaintext
GET projects/:id/packages/npm/:package_name/-/:file_name
```

| Attribute         | Type   | Required | Description |
| ----------------- | ------ | -------- | ----------- |
| `id`              | string | yes      | The ID or full path of the project. |
| `package_name`    | string | yes      | The name of the package. |
| `file_name`       | string | yes      | The name of the package file. |

```shell
curl --header "Authorization: Bearer <personal_access_token>" "https://gitlab.example.com/api/v4/projects/1/packages/npm/@myscope/my-pkg/-/@my-scope/my-pkg-0.0.1.tgz"
```

Write the output to a file:

```shell
curl --header "Authorization: Bearer <personal_access_token>" "https://gitlab.example.com/api/v4/projects/1/packages/npm/@myscope/my-pkg/-/@my-scope/my-pkg-0.0.1.tgz" >> @myscope/my-pkg-0.0.1.tgz
```

This writes the downloaded file to `@myscope/my-pkg-0.0.1.tgz` in the current directory.

## Upload a package file

Upload a package.

```plaintext
PUT projects/:id/packages/npm/:package_name
```

| Attribute      | Type   | Required | Description                         |
|----------------|--------|----------|-------------------------------------|
| `id`           | string | yes      | The ID or full path of the project. |
| `package_name` | string | yes      | The name of the package.            |
| `versions`     | string | yes      | Package version information.        |

```shell
curl --request PUT
     --header "Content-Type: application/json"
     --data @./path/to/metadata/file.json
     --header "Authorization: Bearer <personal_access_token>" \
     "https://gitlab.example.com/api/v4/projects/1/packages/npm/@myscope%2fmy-pkg"
```

The metadata file content is generated by npm, but looks something like this:

```json
{
    "_attachments": {
        "@myscope/my-pkg-1.3.7.tgz": {
            "content_type": "application/octet-stream",
            "data": "H4sIAAAAAAAAE+1TQUvDMBjdeb/iI4edZEldV2dPwhARPIjiyXlI26zN1iYhSeeK7L+bNJtednMg4l4OKe+9PF7DF0XzNS0ZVmEfr4wUgxODEJLEMRzjPRJyCYPJNCFRlCTE+dzH1PvJqYscQ2ss1a7KT3PCv8DX/kfwMQRAgjYMpYBuIoIzKtwy6MILG6YNl8Jr0XgyvgpswUyuubJ75TGMDuSaUcsKyDooa1C6De6G8t7GRcG2br4CGxKME3wDR1hmrLexvJKwQLdaS52CkOAFMIrlfMlZsUAwGgHbcgsRcid3fdqade9SFz7u9a1naGsrqX3gHbcPNINDyydWcmN1By+W19x2oU7NcyZMfwn3z/PAqTaruanmUix5+V3UXVKq9yEoRZW1yqQYl9zWNBvnssFUcbyJsdJyxXJrcHQdz8gsTg6PzGChGty3H+6Gvz0BZ5xxxn/FJ1EDRNIACAAA",
            "length": 354
        }
    },
    "_id": "@myscope/my-pkg",
    "description": "Package created by me",
    "dist-tags": {
        "latest": "1.3.7"
    },
    "name": "@myscope/my-pkg",
    "readme": "ERROR: No README data found!",
    "versions": {
        "1.3.7": {
            "_id": "@myscope/my-pkg@1.3.7",
            "_nodeVersion": "12.18.4",
            "_npmVersion": "6.14.6",
            "author": {
                "name": "GitLab package registry Utility"
            },
            "description": "Package created by me",
            "dist": {
                "integrity": "sha512-loy16p+Dtw2S43lBmD3Nye+t+Vwv7Tbhv143UN2mwcjaHJyBfGZdNCTXnma3gJCUSE/AR4FPGWEyCOOTJ+ev9g==",
                "shasum": "4a9dbd94ca6093feda03d909f3d7e6bd89d9d4bf",
                "tarball": "https://gitlab.example.com/api/v4/projects/1/packages/npm/@myscope/my-pkg/-/@myscope/my-pkg-1.3.7.tgz"
            },
            "keywords": [],
            "license": "ISC",
            "main": "index.js",
            "name": "@myscope/my-pkg",
            "publishConfig": {
                "@myscope:registry": "https://gitlab.example.com/api/v4/projects/1/packages/npm"
            },
            "readme": "ERROR: No README data found!",
            "scripts": {
                "test": "echo \"Error: no test specified\" && exit 1"
            },
            "version": "1.3.7"
        }
    }
}
```

## Route prefix

For the remaining routes, there are two sets of identical routes that each make requests in
different scopes:

- Use the instance-level prefix to make requests in the scope of the entire instance.
- Use the project-level prefix to make requests in a single project's scope.
- Use the group-level prefix to make requests in a group's scope.

The examples in this document all use the project-level prefix.

### Instance-level

```plaintext
/packages/npm
```

### Project-level

```plaintext
/projects/:id/packages/npm
```

| Attribute | Type   | Required | Description |
| --------- | ------ | -------- | ----------- |
| `id`      | string | yes      | The project ID or full project path. |

### Group-level

{{< history >}}

- [Introduced](https://gitlab.com/gitlab-org/gitlab/-/issues/299834) in GitLab 16.0 [with a flag](../../administration/feature_flags/_index.md) named `npm_group_level_endpoints`. Disabled by default.
- [Generally available](https://gitlab.com/gitlab-org/gitlab/-/merge_requests/121837) in GitLab 16.1. Feature flag `npm_group_level_endpoints` removed.

{{< /history >}}

```plaintext
/groups/:id/-/packages/npm
```

| Attribute | Type   | Required | Description |
| --------- | ------ | -------- | ----------- |
| `id`      | string | yes      | The group ID or full group path. |

## Metadata

Returns the metadata for a given package.

```plaintext
GET <route-prefix>/:package_name
```

| Attribute      | Type   | Required | Description |
| -------------- | ------ | -------- | ----------- |
| `package_name` | string | yes      | The name of the package. |

```shell
curl --header "Authorization: Bearer <personal_access_token>" "https://gitlab.example.com/api/v4/projects/1/packages/npm/@myscope/my-pkg"
```

Example response:

```json
{
  "name": "@myscope/my-pkg",
  "versions": {
    "0.0.2": {
      "name": "@myscope/my-pkg",
      "version": "0.0.1",
      "dist": {
        "shasum": "93abb605b1110c0e3cca0a5b805e5cb01ac4ca9b",
        "tarball": "https://gitlab.example.com/api/v4/projects/1/packages/npm/@myscope/my-pkg/-/@myscope/my-pkg-0.0.1.tgz"
      }
    }
  },
  "dist-tags": {
    "latest": "0.0.1"
  }
}
```

The URLs in the response have the same route prefix used to request them. If you request them with
the instance-level route, the returned URLs contain `/api/v4/packages/npm`.

## Dist-Tags

### List tags

Lists the dist-tags for the package.

```plaintext
GET <route-prefix>/-/package/:package_name/dist-tags
```

| Attribute      | Type   | Required | Description |
| -------------- | ------ | -------- | ----------- |
| `package_name` | string | yes      | The name of the package. |

```shell
curl --header "Authorization: Bearer <personal_access_token>" "https://gitlab.example.com/api/v4/projects/1/packages/npm/-/package/@myscope/my-pkg/dist-tags"
```

Example response:

```json
{
  "latest": "2.1.1",
  "stable": "1.0.0"
}
```

The URLs in the response have the same route prefix used to request them. If you request them with
the instance-level route, the returned URLs contain `/api/v4/packages/npm`.

### Create or update a tag

Create or update a dist-tag.

```plaintext
PUT <route-prefix>/-/package/:package_name/dist-tags/:tag
```

| Attribute      | Type   | Required | Description |
| -------------- | ------ | -------- | ----------- |
| `package_name` | string | yes      | The name of the package. |
| `tag`          | string | yes      | The tag to be created or updated. |
| `version`      | string | yes      | The version to be tagged. |

```shell
curl --request PUT --header "Authorization: Bearer <personal_access_token>" "https://gitlab.example.com/api/v4/projects/1/packages/npm/-/package/@myscope/my-pkg/dist-tags/stable"
```

This endpoint responds successfully with `204 No Content`.

### Delete a tag

Delete a dist-tag.

```plaintext
DELETE <route-prefix>/-/package/:package_name/dist-tags/:tag
```

| Attribute      | Type   | Required | Description |
| -------------- | ------ | -------- | ----------- |
| `package_name` | string | yes      | The name of the package. |
| `tag`          | string | yes      | The tag to be created or updated. |

```shell
curl --request DELETE --header "Authorization: Bearer <personal_access_token>" "https://gitlab.example.com/api/v4/projects/1/packages/npm/-/package/@myscope/my-pkg/dist-tags/stable"
```

This endpoint responds successfully with `204 No Content`.
