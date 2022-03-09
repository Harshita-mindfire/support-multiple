---
id: 4xdlz1emgdwul6kq5a6w8sc
title: Async Approach to Move Export Metadata Outside Note
desc: ''
updated: 1646416260011
created: 1646416172370
documentId: 10akjAtvKPwU92odpXoSlvrHTFPEWDC2_5iI5prQEWyU
revisionId: >-
  ALm37BVSYnKDpJ6Fe1Bee4O-BEkPpKpppXYObs7hAcYpJActufmqsExjqJ2JLNQIcI_qQnQxPfZRVjJoLWj241A
---

## Summary

Implementation approach to support exporting to multiple destinations.

## Prerequisites
[[Pod Export Note to Multiple Destinations|dendron://private/user.joshi.async.pod-export-note-to-multiple-destinations]]

## Steps
Today the information related to pod export are added to the frontmatter of a note(airtableId, documentId etc). The following approach aims to remove the metadata surrounding pod operations to a metadata json file.

### Workflow
- Each metadata.json file is associated with a saved custom pod configuration. The idea here is that we need to be able to associate a pod export operation to a specific metadata set. For `config.dendron.task.yml` --> we have `dendron.task.metadata.json`
- The metadata files are stored inside a vault.


#### Update in srcFieldMapping for linked Records
Airtable export also supports exporting a note of type `linkedRecord`. Airtable’s linked record field allows you to create connections between different records. For example: Projects table(https://airtable.com/appKOgvtfSzZyj1YM/tblOgyKgxN8Jd1JmW/viwibOmbClMKgWrKy?blocks=hide)

Before we start building relationships between records(Projects mapping with Tasks), we want to make sure all the tasks are already present in Airtable. For this, we would require the custom pod config Id we used for exporting tasks.

```yml
sourceFieldMapping:  {
  DendronId: id, 
  Name: title,
  Owner: owner,
  Tasks: {
    type: "linkedRecord",
    filter: "task.*",
    configId: "dendron.task" <------------- metadata.json to search
  }
}
```

## Examples
- Creating a record in Airtable: Exporting a single note to airtable using `dendron.task` config will result in updating `dendron.task.metadata.json` file with following entry.

```json
[
    {
        "dendronId": "bhjqg7wvepjqpm4xtcr9voo",
        "airtableId": "xyhsvsyj"

    }
]
```
- Update a note in Airtable: Before exporting a note to Airtable, the pod searches the note id in `dendron.task.metadata.json` file. If the id is present, the note is updated in Airtable.

## Conclusion
This would allow us to export note A to both table `table1` and `table2`. 
- when exporting note A with podId `dendron.task`, the note metadata is added in dendron.task.metadata.json
- when exporting note A with podId `dendron.features`, the note metadata is updated in dendron.features.metadata.json


## Lookup
[feat(pods): Airtable export v2 to support multiple ids by Harshita-mindfire](https://github.com/dendronhq/dendron/pull/2506)
