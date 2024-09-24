# Skap — document management system

Word "skap" is a variation of Germanic words "skab"/"schap"/"schaf".
Here it means storage closet with documents or books.

Skap manages local copy of source documents published in git repositories.
You may use Skap to track changes in source documents and to store revisions (versions)
of your works based on these source documents, for example abstracts.

## Available commands

```plain
help
    Show help message about supported commands.
init
init DIRECTORY_PATH
    Create configuration files in current directory or in DIRECTORY_PATH.
sources
    add DIRECTORY REPO BRANCH
        Add git submodule into DIRECTORY from REPO and track BRANCH by default.
    delete DIRECTORY
        Delete git submodule from DIRECTORY.
    update
    update DIRECTORY ...
        Update git submodule from upstream in DIRECTORY or in all git submodules if DIRECTORY
        is not specified. You may pass one or more directory paths (DIRECTORY ...) to update their
        contents.
works
    covered
        List files of sources which have been used for published works.
    ignored
        List ignored files.
    outdated
        List documents which may contain outdated information.
    publish DOCUMENT
    publish DOCUMENT FILE_PATH ...
        Save record about abstract (DOCUMENT) and pass list of file paths of sources (FILE_PATH ...)
        which relate to this abstract. You should prepend minus sign to file path to exclude it from
        list of related sources. Examples:
        works publish _/docker/compose.md docs.docker.com/content/manuals/compose/**/*.md
        works publish _/docker/compose.md -docs.docker.com/**/*.md
    uncovered
        List files of sources which have NOT been used for published works.
    unknown
        List files of sources which may be used for works or ignored.
```

## Suggested workflow

1. Install Skap: `gem install skap`
2. Initialize storage: `skap init ~/docs` (pass any path to storage)
3. Add sources: `skap sources add docker https://github.com/docker/docs.git main`
  (see command description in "Available commands")
4. Look into downloaded source files and fill entries in file "sources.yaml" in your storage.
5. Create file with your text (here it's known as "work") that relates somehow to source documents.
6. Save revision of source documents with current state of "work":
  `skap works publish _/docker/overview.md docker/content/get-started/docker-overview.md`
  (here "_/docker/overview.md" is path to your "work")
7. Commit changes into current git repository in your storage: `git commit ...` (see manual for git)

Additionally you may push your changes into remote repository — see manual for git:
git remote, git push.

## Additional files in storage

You should store changes in these files with "git commit".

Schema of file "sources.yaml":

```yaml
%directory-name%:
  file-extensions: [%ext%, %ext%]
  ignored:
    - %file-path-pattern-in-this-directory%
  indexed:
    - %file-path-pattern-in-this-directory%
```

Example of file "sources.yaml":

```yaml
docker:
  file-extensions: [md, yaml]
  ignored:
    - "*.md"
    - compose.yaml
  indexed:
    - content/get-started/**/*.md
```

Schema of file "versions.yaml":

```yaml
%work-file-path%:
  date: %iso-date%
  sources:
    %source-file-path%:
      date: %iso-date%
      sha: %commit-sha%
```

Example of file "versions.yaml":

```yaml
_/docker/overview.md:
  date: 2024-12-31
  sources:
    docker/content/get-started/docker-overview.md:
      date: 2024-12-31
      sha: fc77b05ffe69070796a6a8630802e62b75304455
```