# coverity-on-polaris-workflow
A Github action workflow for Coverity on Polaris

## Input

```yaml
inputs:
  api_url:
    description: |
      Coverity on Polaris API URL
      Usually something along the lines of https://{instance_name}.cop.blackduck.com
    required: true
  access_token:
    description: 'Polaris access token'
    required: true
  windows_install_into_temp:
    description: |
      Set to "true" to enable
      Windows Only: Should Coverity on Polaris be installed into a temporary folder in TEMP
      Normal github private runners only have 14GB of space, if you have a huge repository, or build big binaries, you might run out of space on the scratch disk.
      There is more space available on the system disk where the TEMP location is.
    required: false
```

## Usage

### Linux

```yaml
name: SAST
on:
  workflow_dispatch:
  workflow_call:
  schedule:
    - cron: "0 12 * * *" # Runs at 12:00 everyday

env:
  DOTNET_NOLOGO: 1
  DOTNET_CLI_TELEMETRY_OPTOUT: 1

jobs:
  analyze:
    name: Capture and send
    runs-on: ubuntu-latest
    steps:
        - name: Checkout repo
          uses: actions/checkout@v3

        - name: Install dependencies
          run: |
            dotnet restore

        - name: Static application security testing
          uses: visma-prodsec/coverity-on-polaris-workflow@v2.1
          with:
            api_url: ${{ vars.COVERITY_ON_POLARIS_API_URL }}
            access_token: ${{ secrets.COVERITY_ON_POLARIS_ACCESS_TOKEN }}
```

### Windows

```yaml
name: SAST
on:
  workflow_dispatch:
  workflow_call:
  schedule:
    - cron: "0 12 * * *" # Runs at 12:00 everyday

env:
  DOTNET_NOLOGO: 1
  DOTNET_CLI_TELEMETRY_OPTOUT: 1

jobs:
  analyze:
    name: Capture and send
    runs-on: windows-latest
    steps:
        - name: Checkout repo
          uses: actions/checkout@v3

        - name: Setup MSBuild.exe
          uses: microsoft/setup-msbuild@v1.1

        - name: Install dependencies
          run: dotnet restore

        - name: Static application security testing
          uses: visma-prodsec/coverity-on-polaris-workflow@v2.1
          with:
            api_url: ${{ vars.COVERITY_ON_POLARIS_API_URL }}
            access_token: ${{ secrets.COVERITY_ON_POLARIS_ACCESS_TOKEN }}
            windows_install_into_temp: true
```
