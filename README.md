# Kaleidoswap OpenAPI Specifications

This repository contains versioned OpenAPI specifications for Kaleidoswap APIs.

## Structure

```
specs/
├── kaleidoswap.json          # Latest Kaleidoswap API spec
├── versions/                 # Historical versions
│   ├── kaleidoswap-v0.1.0.json
│   ├── kaleidoswap-v0.2.0.json
│   └── ...
└── README.md                 # This file
```

## Usage

**Latest version:**
```
https://raw.githubusercontent.com/kaleidoswap/specs/main/kaleidoswap.json
```

**Specific version:**
```
https://raw.githubusercontent.com/kaleidoswap/specs/main/versions/kaleidoswap-v{version}.json
```

## Automatic Updates

This repository is automatically updated on new releases.

The update process:
1. Generates the OpenAPI specification
2. Validates the spec
3. Commits to this repository
4. Creates a versioned copy in `versions/`

## Manual Usage

To use the spec locally:

```bash
# Download latest spec
curl -o kaleidoswap.json \
  https://raw.githubusercontent.com/kaleidoswap/specs/main/kaleidoswap.json

# Download specific version
curl -o kaleidoswap.json \
  https://raw.githubusercontent.com/kaleidoswap/specs/main/versions/kaleidoswap-v0.2.0.json
```

## License

MIT
