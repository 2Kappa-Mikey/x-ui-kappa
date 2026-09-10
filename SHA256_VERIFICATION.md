# SHA-256 Verification Implementation Guide

## Overview
This document describes the SHA-256 integrity verification system implemented in `x-ui-pro.sh` to address supply-chain security vulnerabilities.

## Implemented Features

### 1. Core Verification Functions

Located at the beginning of `x-ui-pro.sh` (lines 9-91):

- **`verify_sha256(file, expected_hash)`**: Verifies a file against a known SHA-256 hash
- **`fetch_github_checksums(repo, tag)`**: Fetches checksums.txt from GitHub releases
- **`get_hash_from_checksums(content, filename)`**: Extracts specific file hash from checksums
- **`verify_with_online_checksums(repo, tag, filename, filepath)`**: Complete verification workflow

### 2. Protected Downloads

#### sub2sing-box (Line 1170-1194)
- Uses hardcoded known hash from `KNOWN_HASHES` array
- Hash: `e856caac1ec45a5ecb0c5c89d6b9d6f4030b1e831285ab38a731ed59dc37d955`
- File: `sub2sing-box_0.0.9_linux_amd64.tar.gz`

#### 3x-ui Panel (Lines 1027-1032, 1051-1056)
- Attempts to fetch checksums.txt from GitHub release
- Falls back gracefully if checksums unavailable
- Works for both latest and specific version installations

#### Web Assets (Lines 1429-1449)
- Verifies files are not empty after download
- Placeholder hashes defined for future use
- Basic integrity checking implemented

#### Fake Site Templates (Lines 1390-1402)
- Verifies downloaded archive is not empty
- Checks that expected content exists in archive
- Prevents installation of corrupted templates

## Updating Hashes

### For sub2sing-box Updates

1. Download the new release:
```bash
curl -sL "https://github.com/legiz-ru/sub2sing-box/releases/download/vX.Y.Z/sub2sing-box_X.Y.Z_linux_amd64.tar.gz" -o /tmp/test.tar.gz
```

2. Calculate SHA-256:
```bash
sha256sum /tmp/test.tar.gz
```

3. Update the `KNOWN_HASHES` array in `x-ui-pro.sh` (line 12):
```bash
declare -A KNOWN_HASHES=(
    ["sub2sing-box_X.Y.Z_linux_amd64.tar.gz"]="NEW_HASH_HERE"
)
```

### For 3x-ui Panel Updates

The script automatically fetches checksums from GitHub. If the project starts providing checksums.txt files, verification will work automatically.

To manually verify before an update:
```bash
# Get latest version
TAG=$(curl -sL "https://api.github.com/repos/MHSanaei/3x-ui/releases/latest" | jq -r '.tag_name')

# Download checksums if available
curl -sL "https://github.com/MHSanaei/3x-ui/releases/download/${TAG}/checksums.txt"

# Or calculate manually
curl -sL "https://github.com/MHSanaei/3x-ui/releases/download/${TAG}/x-ui-linux-amd64.tar.gz" -o /tmp/xui.tar.gz
sha256sum /tmp/xui.tar.gz
```

### For HTML/JS Assets

To add SHA-256 verification for web assets:

1. Download each file:
```bash
curl -sL "https://github.com/legiz-ru/x-ui-pro/raw/master/sub-3x-ui.html" -o /tmp/sub.html
```

2. Calculate hash:
```bash
sha256sum /tmp/sub.html
```

3. Update placeholder hashes (lines 1404-1413):
```bash
declare -A SUB_PAGE_HASHES=(
    ["sub-3x-ui.html"]="ACTUAL_HASH_HERE"
)
```

4. Add verification code after download similar to sub2sing-box implementation.

## Testing Verification

Run the test script to verify functionality:
```bash
bash /tmp/test_verify.sh
```

Expected output:
```
=== Test 1: sub2sing-box verification ===
 ✓ SHA-256 verification PASSED for test_sub.tar.gz
Test 1 PASSED

=== Test 2: Verification with wrong hash (should fail) ===
 ✗ SHA-256 verification FAILED for test_sub2.tar.gz
Test 2 PASSED (correctly detected bad hash)

All tests completed!
```

## Security Benefits

1. **Supply-Chain Attack Prevention**: Detects tampered binaries from compromised GitHub accounts
2. **Download Corruption Detection**: Catches network errors or incomplete downloads
3. **Man-in-the-Middle Protection**: Identifies modified files during transit
4. **Automatic Cleanup**: Removes failed/corrupted files automatically

## Limitations and Future Work

### Current Limitations
- HTML/JS files use placeholder hashes (require manual updates)
- No signature verification (only hash-based)
- Dependent on GitHub availability for online checksums

### Recommended Improvements
1. Implement GPG signature verification for critical binaries
2. Add automated hash update mechanism via CI/CD
3. Maintain hash allowlist in separate secure file
4. Add support for multiple architectures in KNOWN_HASHES
5. Implement certificate pinning for HTTPS downloads

## Troubleshooting

### "Hash not found in checksums"
- The release may not provide checksums.txt
- Check GitHub release page manually
- Consider adding to KNOWN_HASHES array

### "SHA-256 verification FAILED"
- File may be corrupted during download
- Network interference possible
- **CRITICAL**: Could indicate supply-chain attack
- Script automatically deletes failed files

### "Failed to fetch checksums from GitHub"
- Network connectivity issue
- GitHub API rate limiting
- Repository made private or deleted
- Fallback to manual verification recommended

## Compliance Notes

This implementation addresses:
- CWE-347: Improper Verification of Cryptographic Signature
- OWASP A05:2021: Security Misconfiguration
- Supply-chain security best practices

For production use, consider additional hardening measures as outlined in the security recommendations.
