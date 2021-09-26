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
      - run: |
          sudo apt-get update
          sudo apt-get install -yq build-essential ca-certificates cmake libboost-dev libbz2-dev libpcap-dev \
            ninja-build pkg-config python2.7 ragel wget zlib1g-dev
      - uses: flier/install-hyperscan@v1
        with:
          hyperscan_version: 5.4.0 # The Hyperscan version to download (if necessary) and use.
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
      - name: Install Linux dependencies
        if: startsWith(matrix.os, 'ubuntu-')
        run: |
          sudo apt-get update
          sudo apt-get install -yq build-essential ca-certificates cmake libboost-dev libbz2-dev libpcap-dev \
            ninja-build pkg-config python2.7 ragel wget zlib1g-dev

      - name: Install MacOS dependencies
        if: startsWith(matrix.os, 'macos-')
        run: |
          brew update
          brew install pkg-config libpcap ragel cmake boost ninja lzlib wget

      - name: Install Hyperscan ${{ matrix.hyperscan }} with PCRE ${{ matrix.pcre }}
        uses: flier/install-hyperscan@v1
        with:
          hyperscan_version: ${{ matrix.hyperscan }}
          pcre_version: ${{ matrix.pcre }}
```

### Cached

```yaml
jobs:
  build-and-test:
    runs-on: ubuntu-20.04
    steps:
      - run: |
          sudo apt-get update
          sudo apt-get install -yq build-essential ca-certificates cmake libboost-dev libbz2-dev libpcap-dev \
            ninja-build pkg-config python2.7 ragel wget zlib1g-dev

      - name: Cache Hyperscan library
        id: cache-hyperscan
        uses: actions/cache@v2
        env:
          cache-name: cache-hyperscan-library
        with:
          path: ${{ github.workspace }}/dist
          key: ${{ runner.os }}-build-hyperscan

      - name: Install Hyperscan ${{ matrix.hyperscan }} with PCRE ${{ matrix.pcre }}
        if: steps.cache-hyperscan.outputs.cache-hit == false
        uses: flier/install-hyperscan@v1
        with:
          hyperscan_version: 5.4.0
          build_static_lib: on
          install_prefix: ${{ github.workspace }}/dist/
```

## License

This project is licensed under either of Apache License ([LICENSE-APACHE](LICENSE-APACHE)) or MIT license ([LICENSE-MIT](LICENSE-MIT)) at your option.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in Futures by you, as defined in the Apache-2.0 license, shall be
dual licensed as above, without any additional terms or conditions.
