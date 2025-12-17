# Dependency Audit Report

**Date:** December 17, 2025
**Repository:** github-slideshow
**Auditor:** Automated Analysis

---

## Executive Summary

This repository has **significant security vulnerabilities** and outdated dependencies that require immediate attention. The most critical issues are:

1. **reveal.js 3.9.2** - Extremely outdated (5+ major versions behind), known XSS vulnerabilities
2. **nokogiri 1.11.4** - Multiple high-severity CVEs (CVSS 8.6-8.8)
3. **github-pages 214** - 18 versions behind current (232)

---

## Critical Security Vulnerabilities

### 1. Nokogiri (Ruby) - HIGH SEVERITY

| Current Version | Latest Version | Severity |
|-----------------|----------------|----------|
| 1.11.4 | 1.18.10 | **HIGH** |

**Known CVEs affecting version 1.11.4:**

- **CVE-2021-3517** (CVSS 8.6) - libxml Out-of-bounds Write vulnerability
- **CVE-2021-30560** (CVSS 8.8) - libxslt vulnerability - fixed in 1.13.2
- **CVE-2022-23308** - libxml2 vulnerability - fixed in 1.13.2
- **ReDoS vulnerability** - Fixed in 1.13.4
- **Input Type-Checking vulnerability** - Fixed in 1.13.6
- **Null Pointer Exception** - Fixed in 1.13.10

**Recommendation:** Upgrade immediately to nokogiri >= 1.16.0

### 2. reveal.js (Node.js) - MEDIUM-HIGH SEVERITY

| Current Version | Latest Version | Severity |
|-----------------|----------------|----------|
| 3.9.2 | 5.2.1 | **MEDIUM-HIGH** |

**Known Issues:**

- **XSS vulnerability** reported on HackerOne (affects versions around 3.8.0-3.9.x)
- Version 3.x is no longer maintained
- Missing 5+ years of security patches
- Version 4.x and 5.x include significant security improvements

**Recommendation:** Upgrade to reveal.js 5.x (breaking changes expected - API updates required)

### 3. REXML (Ruby) - MEDIUM SEVERITY

| Current Version | Latest Version | Severity |
|-----------------|----------------|----------|
| 3.2.5 | 3.3.x | **MEDIUM** |

**Known CVEs:**

- **CVE-2024-35176** - DoS via crafted XML
- **CVE-2024-39908** - DoS vulnerability
- **CVE-2024-41123** - DoS vulnerability
- **CVE-2024-41946** - DoS vulnerability

**Recommendation:** Update via github-pages gem upgrade

---

## Outdated Dependencies

### Ruby Dependencies (Gemfile.lock)

| Gem | Current | Latest | Status |
|-----|---------|--------|--------|
| github-pages | 214 | 232 | **18 versions behind** |
| html-proofer | 3.14.1 | 5.x | **Major version behind** |
| jekyll | 3.9.0 | 4.3.x | Pinned by github-pages |
| activesupport | 6.0.3.6 | 7.x | Pinned by github-pages |
| kramdown | 2.3.1 | 2.4.x | Minor update available |
| sass | 3.7.4 | N/A | **DEPRECATED** |

### Node.js Dependencies (package-lock.json)

| Package | Current | Latest | Status |
|---------|---------|--------|--------|
| reveal.js | 3.9.2 | 5.2.1 | **Major version behind** |

---

## Unnecessary Bloat Analysis

### Unused Theme Gems

The `github-pages` gem bundles **13 Jekyll themes** by default. If you're only using one theme (or a remote theme), these add unnecessary dependencies:

- jekyll-theme-architect
- jekyll-theme-cayman
- jekyll-theme-dinky
- jekyll-theme-hacker
- jekyll-theme-leap-day
- jekyll-theme-merlot
- jekyll-theme-midnight
- jekyll-theme-minimal
- jekyll-theme-modernist
- jekyll-theme-primer
- jekyll-theme-slate
- jekyll-theme-tactile
- jekyll-theme-time-machine

**Impact:** These cannot be individually removed when using the `github-pages` meta-gem. Consider using individual gems if theme selection is important.

### Deprecated Dependencies

1. **sass gem (LibSass)** - LibSass is deprecated. The industry has moved to Dart Sass. However, this is pinned by `github-pages`.

2. **coffee-script** - CoffeeScript is largely deprecated in favor of modern JavaScript/TypeScript.

---

## Recommended Changes

### Immediate Actions (Security Critical)

#### 1. Update Gemfile

```ruby
source "https://rubygems.org"

gem 'github-pages', '~> 232'  # Updated from >= 207
gem 'html-proofer', '~> 5.0'   # Updated from >= 3.13.0
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw]
```

#### 2. Add package.json (if reveal.js is needed)

Create a proper `package.json` to manage Node.js dependencies:

```json
{
  "name": "github-slideshow",
  "private": true,
  "dependencies": {
    "reveal.js": "^5.2.1"
  }
}
```

**Note:** Upgrading reveal.js from 3.x to 5.x is a breaking change. The API has changed significantly. Review the [reveal.js migration guide](https://revealjs.com/upgrading/) before upgrading.

### After Updates - Run These Commands

```bash
# Update Ruby dependencies
bundle update

# Check for vulnerabilities
bundle audit

# Update Node.js dependencies (if package.json exists)
npm update
npm audit
```

---

## Risk Assessment Summary

| Category | Risk Level | Items |
|----------|------------|-------|
| Critical Security | **HIGH** | nokogiri CVEs, reveal.js XSS |
| Outdated Core | **MEDIUM** | github-pages, html-proofer |
| Deprecated | **LOW** | sass/LibSass, coffee-script |
| Bloat | **LOW** | Unused themes |

---

## References

- [Nokogiri Security Advisories](https://github.com/advisories?query=nokogiri)
- [reveal.js HackerOne Report](https://hackerone.com/reports/691977)
- [GitHub Pages Gem](https://rubygems.org/gems/github-pages)
- [kramdown CVE-2021-28834](https://github.com/advisories/GHSA-mqm2-cgpr-p4m6)
- [REXML Security Advisories](https://www.ruby-lang.org/en/news/)

---

## Next Steps

1. **Priority 1:** Update `github-pages` gem to version 232 (addresses nokogiri and other CVEs)
2. **Priority 2:** Evaluate reveal.js upgrade path (major version change, requires testing)
3. **Priority 3:** Update `html-proofer` to version 5.x
4. **Priority 4:** Consider removing `node_modules` from version control if reveal.js is loaded via CDN or updated dependency management
