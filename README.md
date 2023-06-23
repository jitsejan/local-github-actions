# Local Github Actions

You can use a JSON file as input for the strategy matrix by using the following as the workflow file inside `.github/workflows/main.yml`:

```yaml
name: main
on: push

jobs:

configure:
runs-on: ubuntu-latest
outputs:
matrix: ${{ steps.set-matrix.outputs.matrix }}
steps:
- name: Checkout to repository
uses: actions/checkout@v3
- name: Set matrix data
id: set-matrix
run: echo "matrix=$(jq -c . < ./config.json)" >> $GITHUB_OUTPUT

print:
runs-on: ubuntu-latest
needs: configure
strategy:
matrix: ${{ fromJson(needs.configure.outputs.matrix) }}
steps:
- run: echo ${{ matrix.name }}
```

where the configuration should be written with the `include` key explicitly:

```json
{
    "include": [
    {
        "name": "Mario"
    },
    {
        "name": "Luigi"
    }
    ]
}
```

To test this locally I have used [act](https://github.com/nektos/act) by simply running `act` in the root directory of the repository.