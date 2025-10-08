# AGENTS.md - AI Assistant Documentation

## Overview

This file contains documentation and notes for AI assistants working with this repository. It is intentionally ignored by Git and does not affect the Upptime application functionality.

## About This Repository

This is an **Upptime** monitoring repository that tracks the uptime and response times of Palmera Infrastructure services.

### What is Upptime?

Upptime is an open-source uptime monitor and status page powered entirely by GitHub Actions and GitHub Issues. It uses:

- **GitHub Actions** for automated health checks (every 5 minutes)
- **GitHub Pages** for hosting the status website
- **GitHub Issues** for incident management
- **GitHub API** for all operations

### Repository Structure

```
├── .upptimerc.yml           # Main configuration file - CRITICAL
├── history/                 # Historical data for each service
│   ├── summary.json        # Consolidated summary of all services
│   └── *.yml               # Individual service history files
├── api/                     # JSON files for shields.io badges
│   └── [service-slug]/     # 10 JSON files per service
├── graphs/                  # PNG response time graphs
│   └── [service-slug]/     # 5 PNG files per service
└── README.md               # Auto-generated status page content
```

### Key Files Explanation

#### `.upptimerc.yml`

- **Purpose**: Main configuration file defining all monitored services
- **Format**: YAML (must be valid YAML)
- **Critical**: Any syntax error breaks the entire monitoring system
- **Structure**:

  ```yaml
  sites:
    - name: Service Name
      url: https://endpoint-url/check/  # or /health/ready/
  ```

#### `history/summary.json`

- **Purpose**: Consolidated metrics for all services
- **Format**: JSON array of service objects
- **Updates**: Automatically updated by Upptime CI
- **Manual Changes**: Should match `.upptimerc.yml` services

#### `README.md`

- **Purpose**: Status page with all service metrics
- **Format**: Markdown with embedded badges/links
- **Auto-generated**: Partially updated by Upptime
- **Manual Sections**: Between `<!--start-->` and `<!--end-->` markers

### Service Naming Patterns

**Transaction Services** (use `/check/` endpoint):

- Pattern: `Palmera Tx Service ([Network Name])`
- URL: `https://prod.[network-slug].keypersafe.xyz/check/`
- Slug: `palmera-tx-service-[network-slug]`

**Client Gateway Services** (use `/health/ready/` endpoint):

- Pattern: `Palmera Client Gateway ([Network Name])`
- URL: `https://prod.[network-slug]-client-gateway.keypersafe.xyz/health/ready/`
- Slug: `palmera-client-gateway-[network-slug]`

### GitHub API Token Consumption

⚠️ **CRITICAL WARNING**: Upptime consumes GitHub API tokens heavily!

#### Token Usage per Service per Day

- **Uptime checks**: 288 requests/day (every 5 minutes)
- **Updates**: 48 requests/day (every 30 minutes)
- **Graphs/Response Time**: 48 requests/day each
- **Summary**: 8 requests/day (every 3 hours)
- **Total per service**: ~400-500 API requests/day

#### GitHub API Rate Limits

- **Free tier**: 5,000 requests/hour
- **Pro/Team**: 5,000 requests/hour (per user)
- **Actions**: 1,000 requests/hour (for workflows)

#### Best Practices to Minimize Token Usage

1. **Batch Changes**: Always commit all service changes in a SINGLE commit
   - Adding/removing multiple services should be one atomic operation
   - Prevents triggering multiple CI runs

2. **Use `[skip ci]` in commits**: When making documentation-only changes

   ```bash
   git commit -m "docs: Update AGENTS.md [skip ci]"
   ```

3. **Avoid Frequent Pushes**: Group multiple changes before pushing

4. **Monitor Token Usage**: Check GitHub Actions usage regularly

5. **Optimize Cron Jobs**: Already configured in `.upptimerc.yml`
   - Uptime checks: Every 5 minutes (necessary)
   - Updates/Graphs: Every 30 minutes
   - Summary: Every 3 hours

### Workflow for Adding/Removing Services

#### Step 1: Update `.upptimerc.yml`

- Add/remove service entries in the `sites` section
- Verify YAML syntax (critical!)

#### Step 2: Create/Delete History Files

- Location: `history/[service-slug].yml`
- Template:

  ```yaml
  url: https://service-url/
  status: up
  code: 200
  responseTime: 200
  lastUpdated: 2025-10-08T00:00:00.000Z
  startTime: 2025-10-08T00:00:00.000Z
  generator: Upptime <https://github.com/upptime/upptime>
  ```

#### Step 3: Create/Delete API Directories

- Location: `api/[service-slug]/`
- Required files (10 total):
  - `response-time-day.json`
  - `response-time-week.json`
  - `response-time-month.json`
  - `response-time-year.json`
  - `response-time.json`
  - `uptime-day.json`
  - `uptime-week.json`
  - `uptime-month.json`
  - `uptime-year.json`
  - `uptime.json`

#### Step 4: Create/Delete Graphs Directories

- Location: `graphs/[service-slug]/`
- Required files (5 total):
  - `response-time-day.png`
  - `response-time-week.png`
  - `response-time-month.png`
  - `response-time-year.png`
  - `response-time.png`

#### Step 5: Update `history/summary.json`

- Add/remove service entries
- Match the structure of existing entries
- Ensure valid JSON format

#### Step 6: Update `README.md`

- Add/remove service rows in the table
- Follow existing format precisely
- Update between status markers

#### Step 7: Commit & Push (ONE SHOT!)

- **CRITICAL**: Review ALL changes before committing
- Use descriptive commit message
- Include all changes in a single commit
- Push once, verify success

### Common Pitfalls to Avoid

1. **Multiple Commits**: Each push triggers full CI workflow (expensive!)
2. **Invalid YAML**: Breaks entire monitoring system
3. **Mismatched Slugs**: URLs won't work if slugs don't match
4. **Missing Files**: Service pages will show errors
5. **JSON Syntax Errors**: Badges won't render correctly
6. **Incomplete Deletions**: Orphaned files consume space

### Testing Changes Locally

Before pushing:

```bash
# Validate YAML syntax
yamllint .upptimerc.yml

# Check JSON validity
jq . history/summary.json

# Verify all files exist
ls history/ api/ graphs/
```

### Emergency Rollback

If something goes wrong:

```bash
# Revert to previous working commit
git revert HEAD

# Force push if necessary (last resort)
git reset --hard HEAD~1
git push -f origin main
```

### Service Addition Checklist

- [ ] Update `.upptimerc.yml` with new service
- [ ] Create `history/[service-slug].yml`
- [ ] Create `api/[service-slug]/` directory with 10 JSON files
- [ ] Create `graphs/[service-slug]/` directory with 5 PNG files
- [ ] Update `history/summary.json` with new entry
- [ ] Update `README.md` with new table row
- [ ] Verify all files created correctly
- [ ] Commit ALL changes in single commit
- [ ] Push once and monitor CI

### Service Removal Checklist

- [ ] Remove service from `.upptimerc.yml`
- [ ] Delete `history/[service-slug].yml`
- [ ] Delete `api/[service-slug]/` directory
- [ ] Delete `graphs/[service-slug]/` directory
- [ ] Remove entry from `history/summary.json`
- [ ] Remove row from `README.md`
- [ ] Verify all references removed
- [ ] Commit ALL changes in single commit
- [ ] Push once and monitor CI

## Current Services (as of 2025-10-08)

### Mainnet Services

- Lit Chain
- XRPL Mainnet
- Flare
- Coti Mainnet
- Hyperliquid
- Z-Chain
- Sonic
- Somnia Mainnet
- Injective Mainnet
- Goat Network
- Perennial
- Matchain
- Hedera Mainnet
- Zeronet
- Neox
- RedBelly Network

### Testnet Services

- Chronicle Yellowstone Testnet
- XRPL Testnet
- Z-Chain Testnet
- Somnia Testnet
- Injective EVM Testnet
- Neox Testnet
- Hedera Testnet
- Story Testnet
- RedBelly Network Testnet

### Other Services

- Story (Mainnet)

## Repository Information

- **Owner**: keyper-labs
- **Repository**: palmera-services-status
- **Status Website**: <https://status.palmeradao.xyz>
- **Technology**: Upptime (GitHub Actions + GitHub Pages)

## Additional Notes

- This file (`AGENTS.md`) is ignored via `.gitignore` and `.gitattributes`
- Changes to this file do NOT trigger CI workflows
- This file can be updated freely without affecting the monitoring system
- Use `[skip ci]` in commit messages for documentation changes

---

*Last Updated: 2025-10-08*
*Maintained by: AI Assistants & DevOps Team*
