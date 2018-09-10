---
title: How to create notes and occurrences
overview: Describes how to create notes and occurrences once you have a running Grafeas server.

order: 15

layout: docs
type: markdown
---

Before you can create [notes](/_docs/concepts/what-is-grafeas/overview.md) or
[occurrences](/_docs/concepts/what-is-grafeas/overview.md), you'll need a
running Grafeas server, which you can set up following
[these instructions](https://github.com/grafeas/grafeas/blob/master/docs/running_grafeas.md).
The examples below assume that you have a local server running on port 8080.

## Create a project

All notes and occurrences must be stored in a project. Generally, you should
create separate projects for notes and occurrences in order to make it easier to
set up more fine-grained access control.

To create a `provider_example` project to store notes in, run the following
command:

```bash
curl -X POST http://localhost:8080/v1alpha1/projects \
     -H "Content-Type: application/json" \
     --data '{"name":"projects/provider_example"}'
```

Next, create an `occurrence_example` project:

```bash
curl -X POST http://localhost:8080/v1alpha1/projects \
     -H "Content-Type: application/json" \
     --data '{"name":"projects/occurrence_example"}'
```

## Using curl

### Create a note

First, create a `note.json` file with the information you want your note to
contain:

```json
{
    "name" : "projects/provider_example/notes/test",
    "shortDescription": "A brief description of the note",
    "longDescription": "A longer description of the note",
    "kind": "PACKAGE_VULNERABILITY",
    "vulnerabilityType": {
        "details": [
        {
            "package": "libexempi3",
            "cpeUri": "cpe:/o:debian:debian_linux:7",
            "minAffectedVersion": { "name": "2.5.7", "revision": "1"},
            "maxAffectedVersion": { "name": "2.5.9", "revision": "1"}
        }
        ]
    }
}
```

Now create the note with the following command:

```bash
curl http://localhost:8080/v1alpha1/projects/provider_example/notes?note_id=testNote
    -X POST -H "Content-Type: application/json" -d @note.json
```

This creates a note in the `provider_example` project. If you want to verify
that your note was created, you can list all notes in a given project with the
command:

```bash
curl http://localhost:8080/v1alpha1/projects/provider_example/notes
```

### Create an occurrence

First, create an `occurrence.json` file describing the occurrence you want to
create. The following example creates an occurrence of the note you created
above, with some sample data about where the vulnerability occurred.

```json
{
  "resourceUrl": "https://gcr.io/project/image@sha256:foo",
  "noteName": "projects/provider_example/notes/test",
  "kind": "PACKAGE_VULNERABILITY",
  "vulnerabilityDetails": {
    "packageIssue": [
      {
        "affectedLocation": {
          "cpeUri": "7",
          "package": "a",
          "version": {
            "name": "v1.1.1",
            "kind": "NORMAL",
            "revision": "r"
          }
        },
        "fixedLocation": {
          "cpeUri": "cpe:/o:debian:debian_linux:7",
          "package": "a",
          "version": {
            "name": "namestring",
            "kind": "NORMAL",
            "revision": "1"
          }
        }
      }
    ]
  }
}
```

Then create an occurrence with the following command:

```bash
curl http://localhost:8080/v1alpha1/projects/occurrence_example/occurrences \
     -X POST -H "Content-Type: application/json" -d @occurrence.json
```

To verify that you successfully created the occurrence, you can list all
occurrences on your project with the following command:

```bash
curl http://localhost:8080/v1alpha1/projects/occurrence_example/occurrences
```

You can find information about other available actions in the
[Grafeas API documentation](https://github.com/grafeas/grafeas/blob/669d9cdc0ca804bf7d29dcf6d66bb9d8e94b08b6/v1alpha1/docs/GrafeasApi.md).

## Using Java

The code sample below uses Java, but the Go, Python, and Ruby client libraries
work in a similar way.

You can add all of the following methods to your
[GrafeasApiExample](https://github.com/nhayes/client-java/#getting-started)
class, or the equivalent class that you're using to talk to the client library.

### Create a note

Add a note to the `provider_example` project as follows:

```java
/**
   * Creates and returns a new note
   *
   * @param parent The project containing the note, of the format
   *        "projects/<project_id>".
   * @param name A user-specified identifier for the note
   * @return a Note object representing the new note
   * @throws Exception on errors while closing the client
   */
  public static ApiNote createNote(String parent, String name)
         throws ApiException {
    ApiNote body = new ApiNote();
    body.setName(String.format("%s/notes/%s", parent, name));
    body.setKind(ApiNoteKind.PACKAGE_VULNERABILITY);

    ApiVulnerabilityType vulnerabilityType = new ApiVulnerabilityType();
    vulnerabilityType.setCvssScore(0.3f);
    body.setVulnerabilityType(vulnerabilityType);

    return apiInstance.createNote(parent, body);
  }
```

To verify that you successfully created your note, you can list all of the notes
in a given project:

```java
/**
   * Prints the notes in the specified project.
   *
   * @param parent the project to fetch notes from, of the form "projects/<project_id>".
   * @throws Exception on errors while closing the client
   */
  public static void listNotes(String parent) throws ApiException {
    String filter = ""; // Optional; filter expression.
    int pageSize = 10; // Number of notes to return in the list.
    String pageToken = ""; // Optional; skip to a particular spot in the list.
    try {
      ApiListNotesResponse result = apiInstance.listNotes(parent, filter, pageSize, pageToken);
      System.out.println(result.getNotes());
    } catch (ApiException e) {
      System.err.println("Exception when calling GrafeasApi#listNotes");
      e.printStackTrace();
    }
  }
```

### Create an occurrence

You can create an occurrence with the following code. Note that you need to pass
in a valid note name for an existing note.

```java
/**
  * Creates and returns a new occurrence.
  *
  * @param resourceUrl the Container Registry URL associated with the image
  *                 example: "https://gcr.io/project/image@sha256:foo"
  * @param parent the project to store this occurrence in, of the form
  * "projects/<project_id>".
  * @param noteName the full name of the note this occurrence is attached to, of
  * the form "projects/<project_id>/notes/<note_id>"
  * @return an ApiOccurrence object representing the new occurrence
  * @throws Exception on errors while closing the client
  */
  public static ApiOccurrence createOccurrence(String resourceUrl,
      String parent,
      String noteName) throws ApiException {
    ApiOccurrence body = new ApiOccurrence();
    body.setResourceUrl(resourceUrl);
    body.setNoteName(noteName);

    return apiInstance.createOccurrence(parent, body);
  }
```

You can list occurrences in a similar manner:

```java
/**
   * Prints the notes in the specified project.
   *
   * @param parent the project to fetch notes from, of the form "projects/<project_id>".
   * @throws Exception on errors while closing the client
   */
  public static void listOccurrences(String parent) {
    String filter = ""; // The filter expression.
    int pageSize = 56; // Number of occurrences to return in the list.
    String pageToken = "";
    try {
      ApiListOccurrencesResponse result =
          apiInstance.listOccurrences(parent, filter, pageSize, pageToken);
      System.out.println(result);
    } catch (ApiException e) {
      System.err.println("Exception when calling GrafeasApi#listOccurrences");
      e.printStackTrace();
    }
  }
```

You can find information about other available actions in the
[Grafeas API documentation](https://github.com/grafeas/grafeas/blob/669d9cdc0ca804bf7d29dcf6d66bb9d8e94b08b6/v1alpha1/docs/GrafeasApi.md).
