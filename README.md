# GitHub Action to Upload Assets to Azure Blob Storage

This action is designed to use the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) to upload a directory of your choice to your Azure Blob Storage account.

## Usage

### Example

Place in a `.yml` file such as this one in your `.github/workflows` folder. [Refer to the documentation on workflow YAML syntax here.](https://help.github.com/en/articles/workflow-syntax-for-github-actions)

```yaml
name: Upload To Azure Blob Storage
on:
  push:
    branches:
      - master

jobs:
  upload:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master
      - uses: bacongobbler/azure-blob-storage-upload@v1.1.0
        with:
          source_dir: _dist
          container_name: www
          connection_string: ${{ secrets.ConnectionString }}
          extra_args: '--pattern *.tar.gz'
```

If you want to synchronize local and remote state (for example, if you are publishing a static website), use the `sync` flag.

Here's an example that will generate and upload a [Hugo](https://gohugo.io/) static site to Azure Blob Storage on every push to master:

```yaml
name: Upload To Azure Blob Storage
on:
  push:
    branches:
      - master

jobs:
  upload:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master
      - uses: chabad360/hugo-actions@master
        with:
          buildPath: 'public'
          hugoVersion: ''
          args: ''
      - uses: bacongobbler/azure-blob-storage-upload@v1.1.0
        with:
          source_dir: 'public'
          container_name: '$web'
          connection_string: ${{ secrets.ConnectionString }}
          sync: true
```

### Required Variables

| Key                 | Value                                                                      |
|---------------------|----------------------------------------------------------------------------|
| `source_dir`        | The name of the directory you want to upload                               |
| `container_name`    | The name of the storage account container these assets will be uploaded to |
| `connection_string` | Your Azure Blob Storage connection string                                  |

### Optional Variables

| Key          | Value                                                                                                                                   |
|--------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| `extra_args` | Extra arguments that can be passed to `az storage blob upload-batch`. Useful for passing flags like `--pattern` or `--destination-path` |
| `sync`       | Use `az storage blob sync` to synchronize blobs recursively                                                                             |

## License

This project is distributed under the [Apache 2 license](LICENSE.md).
