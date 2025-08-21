# set-imds-region.sh

Bulk-set **EC2 Instance Metadata Service (IMDS)** options for instances in a given AWS region.  
The script finds instances based on filters (state, tags, or explicit IDs) and updates them to require IMDSv2 (`--http-tokens required`) and enable the endpoint (`--http-endpoint enabled`).

---

## Features

- **Region detection** via:
  1. `--region` argument  
  2. `$AWS_REGION` or `$AWS_DEFAULT_REGION` environment variables  
  3. `aws configure get region`  

- **Filters for discovery**:
  - Instance state(s) (`--state`)
  - Name tag (`--name`)
  - Any tag (`--tag Key=Value`)
  - Specific instance IDs (`--instance-ids`)

- **Parallel execution** with `--parallel` for faster updates.
- **Dry-run mode** to preview changes before applying.
- **Safe exit on errors** with `set -euo pipefail`.

---

## Requirements

- **AWS CLI v2**  
  [Install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

- **jq** (optional, for better output formatting)  
  [Install jq](https://stedolan.github.io/jq/download/)

- Bash shell with `xargs`, `awk`, and `sort`.

---

## Usage

```bash
./set-imds-region.sh [--region REGION]
                     [--state running,stopped,...]
                     [--name NAME]
                     [--tag Key=Value]
                     [--instance-ids "i-abc i-def ..."]
                     [--parallel 8]
                     [--dry-run]
                     [-h|--help]
```

### Examples

- **All running instances in current region**
  ```bash
  ./set-imds-region.sh
  ```

- **Only instances with Name=web-prod**
  ```bash
  ./set-imds-region.sh --name web-prod
  ```

- **Include stopped instances, filter by tag**
  ```bash
  ./set-imds-region.sh --tag Environment=prod --state running,stopped
  ```

- **Specific instances**
  ```bash
  ./set-imds-region.sh --instance-ids "i-0123 i-0456"
  ```

- **Dry run (no changes)**
  ```bash
  ./set-imds-region.sh --dry-run
  ```

---

## Output

For each instance:
- Prints updated IMDS settings (`HttpTokens`, `HttpEndpoint`)
- Shows `DRYRUN OK` if `--dry-run` is enabled
- Parallel execution speed depends on `--parallel` value (default: 8)

---

## Exit Codes

- **0** → Success  
- **1** → Invalid arguments or AWS CLI errors  
- **2** → No matching instances found  

---

## License

MIT License. See [LICENSE](LICENSE) for details.
