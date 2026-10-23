# sml-salesinsights-databricks

AtScale SML catalog "Sales Insights - Databricks": the AdventureWorks Internet Sales demo on Databricks, with row security and a slowly changing dimension example.

This repository holds the Databricks version of the Sales Insights demo, written in SML (Semantic Modeling Language, the YAML format that AtScale uses to define a catalog as code). The shared customer, date, geography, gender, and product objects come from the package [sml-salesinsights-common](https://github.com/muellermarco/sml-salesinsights-common). The repository is connected to the [atscale-cicd-pipeline](https://github.com/muellermarco/atscale-cicd-pipeline).

## Technologies

- AtScale 2026.9.2
- sml-cli 2025.12.0 (pinned in the pipeline's `validate` workflow)
- SML catalog `version: 1.5` (see `catalog.yml`)
- Databricks, through the AtScale connection `Databricks`
- GitHub Actions (reusable workflows from `muellermarco/atscale-cicd-pipeline`)

## Catalog at a glance

| Item | Value |
|---|---|
| Catalog | `Sales Insights - Databricks` (`catalog.yml`) |
| Connection | AtScale connection `Databricks`. Database and schema: see `connections/Databricks.yml`. |
| Package | `Common-Objects` from sml-salesinsights-common, branch `databricks`, commit `3af95a4` (see `package.yml`) |

## Models

| Model | Purpose |
|---|---|
| `Internet Sales` | Sales metrics by customer, date, order, and product |
| `Internet Sales RLS` | `Internet Sales` with the security dimensions from the package, so users see only their rows |
| `Internet Sales Simple` | `Internet Sales` with one date relationship (order date) to `Date Dimension Simple` |
| `Sales` | Sales metrics by customer, date, order, and product |
| `Sales Product SCD` | Type 2 slowly changing product dimension. See [docs/SCD-README.md](docs/SCD-README.md). |

## Repository contents

| Folder | Count | Contents |
|---|---|---|
| `connections/` | 1 | `Databricks` |
| `datasets/` | 4 | `dimorder`, `dimorder_scd`, `dimproduct_scd`, `factinternetsales_scd` |
| `dimensions/` | 4 | `Date Dimension Simple`, `Order Dimension SCD`, `Order Dimension3`, `Product SCD Dimension` |
| `metrics/` | 16 | Base metrics, for example sales amount, order quantity, customer count |
| `calculations/` | 133 | Calculated metrics, mainly time-relative (Retail 4-4-5 and custom 4-4-5 periods) |
| `models/` | 5 | See the table above |
| `docs/` | | SCD walkthrough, workbook, and screenshots |

The package adds more datasets, dimensions, and row security objects at install time.

## How the pipeline deploys this repo

| Event | Workflow | Result |
|---|---|---|
| Pull request | `validate` | sml-cli installs the package and validates the catalog. No deploy. |
| Merge to `main` | `deploy-qa` | The catalog deploys to the QA instance. |
| Tag `v*` | `release-live` | The catalog deploys to the live instance. |

QA and live deploy the catalog as `Sales Insights - Databricks_main`.

## Change the model and release it

1. Create a branch from `main`.
2. Edit the SML files.
3. Install the package: `npx -y sml-cli@2025.12.0 install .`.
4. Validate: `npx -y sml-cli@2025.12.0 validate .`.
   Result: the output contains `Validation SUCCESSFUL`.
5. Open a pull request to `main`.
   Result: the `validate` check is green.
6. Merge the pull request.
   Result: `deploy-qa` deploys the catalog to the QA instance.
7. Push a release tag, for example `git tag v1.6 && git push origin v1.6`.
   Result: `release-live` deploys the catalog to the live instance.

## License

MIT License. See [LICENSE](LICENSE).
