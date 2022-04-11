# install-hyperscan

This action download, build and install [Hyperscan](https://github.com/intel/hyperscan/) library for your build.

## Usage

See [action.yml](action.yml) metadata and [ci.yml](https://github.com/flier/gohs/blob/master/.github/workflows/ci.yml) example.

### Basic

```yaml
jobs:
  build-and-test:
    runs-on: ubuntu-20.04
    steps:
      - uses: flier/install-hyperscan@v1
        with:
          hyperscan_version: 5.4.0    # The Hyperscan version to download (if necessary) and use.
          pcre: 8.45                  # The PCRE version to download (if necessary) and use to build Chimera.
          install_dependencies: true
```

### Matrix Testing

```yaml
jobs:
  build-and-test:
    strategy:
      matrix:
        include:
          - os: macos-latest
            hyperscan: 5.4.0
            pcre: 8.45
          - os: ubuntu-20.04
            hyperscan: 5.2.1
            pcre: 8.45
          - os: ubuntu-18.04
            hyperscan: 4.7.0
            pcre: 8.41
    runs-on: ${{ matrix.os }}
    steps:
      - name: Install Hyperscan ${{ matrix.hyperscan }} with PCRE ${{ matrix.pcre }}
        uses: flier/install-hyperscan@v1
        with:
          hyperscan_version: ${{ matrix.hyperscan }}
          pcre_version: ${{ matrix.pcre }}
          install_dependencies: true
```

### Cached

```yaml
jobs:
  build-and-test:
    runs-on: ubuntu-20.04
    steps:
      - name: Install Hyperscan ${{ matrix.hyperscan }} with PCRE ${{ matrix.pcre }}
        if: steps.cache-hyperscan.outputs.cache-hit == false
        uses: flier/install-hyperscan@v1
        with:
          hyperscan_version: 5.4.0
          build_static_lib: on
          install_prefix: ${{ github.workspace }}/dist/
          install_dependencies: true
          use_cache: true
```

## Inputs

### Parameters

| name                          | description                                                                       | default        |
| ----------------------------- | --------------------------------------------------------------------------------- | -------------- |
| `hyperscan_version`           | The version of Hyperscan library.                                                 | 5.4.0          |
| `pcre_version`                | The version of PCRE library.                                                      | 8.45           |
| `install_dependencies`        | Install dependencies for building.                                                | Linux or MacOS |
| `src_dir`                     | The directory of Hyperscan source.                                                | hyperscan_src  |
| `build_type`                  | Define which kind of build to generate.                                           | RelWithDebInfo |
| `build_static_lib`            | Build Hyperscan as a static library.                                              | on             |
| `build_shared_lib`            | Build Hyperscan as a shared library.                                              | off            |
| `build_static_and_shared_lib` | Build both static and shared Hyperscan libs.                                      | off            |
| `debug_output`                | Enable very verbose debug output.                                                 | off            |
| `install_prefix`              | Install directory for install target                                              | /usr/local     |
| `use_cache`                   | Allows caching build outputs to improve execution time.                           | false          |
| `cache_key`                   | An explicit key for restoring and saving the cache                                |                |
| `upload_artifact`             | Upload and share library between jobs and store data once a workflow is complete. | false          |

## License

This project is licensed under either of Apache License ([LICENSE-APACHE](LICENSE-APACHE)) or MIT license ([LICENSE-MIT](LICENSE-MIT)) at your option.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in Futures by you, as defined in the Apache-2.0 license, shall be
dual licensed as above, without any additional terms or conditions.
