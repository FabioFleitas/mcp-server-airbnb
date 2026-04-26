[![downloads](https://img.shields.io/npm/dm/@openbnb/mcp-server-airbnb)](https://www.npmjs.com/package/@openbnb/mcp-server-airbnb)

# Airbnb Search & Listings - MCP Bundle (MCPB)

A comprehensive MCP Bundle for searching Airbnb listings with advanced filtering capabilities and detailed property information retrieval. Built as a Model Context Protocol (MCP) server packaged in the MCP Bundle (MCPB) format for easy installation and use with compatible AI applications.

---

## 🚀 Prefer not to run a local server? Try [openbnb.ai](https://openbnb.ai/)

> **👉 [openbnb.ai](https://openbnb.ai/) is a hosted MCP server that solves the same problem — searching Airbnb listings — without any local setup.**

If you don't want to deal with installing and running this server yourself, **[openbnb.ai](https://openbnb.ai/)** is a separate, fully-hosted alternative you can connect to directly from your MCP client. It goes beyond what this open-source server offers:

- ✅ **Zero setup** — no Node, no `npx`, no config files, no updates to manage
- ✅ **Advanced filters** — richer search controls beyond the base tools here
- ✅ **MCP UI** — interactive UI components for browsing results, not just plain text
- ✅ **Managed & maintained** — kept running for you

Head to **[openbnb.ai](https://openbnb.ai/)** for setup instructions and the connection details for your MCP client.

If you'd rather self-host the open-source server, read on.

---

## Features

### 🔍 Advanced Search Capabilities
- **Location-based search** with support for cities, states, and regions
- **International location support** via client-side geocoding, so non-US queries (e.g. "Paris, France", "Copenhagen, Denmark") return results in the right city
- **Google Maps Place ID** integration for precise location targeting
- **Property type filtering** for entire homes, private rooms, shared rooms, or hotel rooms
- **Amenity filtering** including Wifi, A/C, kitchen, washer, free parking, pool, hot tub, king bed, and self check-in
- **Quality and booking filters** including Guest Favorite (Airbnb's curated bucket) and Instant Book
- **Minimum bedroom/bed/bathroom counts** for sizing requirements
- **Manual bounding-box override** to skip the third-party geocoder when you already have map coordinates
- **Date filtering** with check-in and check-out date support
- **Guest configuration** including adults, children, infants, and pets
- **Price range filtering** with minimum and maximum price constraints
- **Pagination support** for browsing through large result sets

### 🏠 Detailed Property Information
- **Comprehensive listing details** including amenities, policies, and highlights
- **Location information** with coordinates and neighborhood details
- **House rules and policies** for informed booking decisions
- **Property descriptions** and key features
- **Direct links** to Airbnb listings for easy booking

### 💬 Guest Reviews
- **Full-text reviews** with reviewer name, date, language, and host responses
- **Server-side keyword search** so agents can answer questions like "any reviews mentioning noise?" without scanning everything
- **Tag filtering** by Airbnb's AI-generated review categories (Cleanliness, Location, Hospitality, etc.)
- **Pagination** through listings with hundreds of reviews

### 🛡️ Security & Compliance
- **Robots.txt compliance** with configurable override for testing
- **Request timeout management** to prevent hanging requests
- **Enhanced error handling** with detailed logging
- **Rate limiting awareness** and respectful API usage
- **Secure configuration** through MCPB user settings

## Installation

### For Claude Desktop
This extension is packaged as an MCP Bundle (`.mcpb`) file. To install:

1. Download the `.mcpb` file from the [latest release](https://github.com/openbnb-org/mcp-server-airbnb/releases/latest)
2. Open the file — Claude Desktop will show an installation dialog
3. Configure the extension settings as needed

To ignore robots.txt, open Claude Desktop settings, navigate to the extension, and enable the **Ignore robots.txt** toggle.

### For Cursor, etc.

Before starting make sure [Node.js](https://nodejs.org/) is installed on your desktop for `npx` to work.
1. Go to: Cursor Settings > Tools & Integrations > New MCP Server

2. Add one the following to your `mcp.json`:
    ```json
    {
      "mcpServers": {
        "airbnb": {
          "command": "npx",
          "args": [
            "-y",
            "@openbnb/mcp-server-airbnb"
          ]
        }
      }
    }
    ```

    To ignore robots.txt for all requests, use this version with `--ignore-robots-txt` args

    ```json
    {
      "mcpServers": {
        "airbnb": {
          "command": "npx",
          "args": [
            "-y",
            "@openbnb/mcp-server-airbnb",
            "--ignore-robots-txt"
          ]
        }
      }
    }
    ```
3. Restart.


## Configuration

The extension provides the following user-configurable options:

### Ignore robots.txt
- **Type**: Boolean (checkbox)
- **Default**: `false`
- **Description**: Bypass robots.txt restrictions when making requests to Airbnb
- **Recommendation**: Keep disabled unless needed for testing purposes

### Disable third-party geocoding
- **Type**: Boolean (checkbox)
- **Environment variable**: `DISABLE_GEOCODING`
- **Default**: `false`
- **Description**: Skip the Photon/Nominatim geocoding step and let Airbnb resolve the location string on its own. Enabling this restores the pre-PR behavior — every search goes only to `airbnb.com`, no third-party calls.
- **Recommendation**: Keep disabled unless you specifically need zero third-party outbound traffic. With it enabled, non-US searches could return incorrect results. See [External Services](#external-services).

## Tools

### `airbnb_search`

Search for Airbnb listings with comprehensive filtering options.

**Parameters:**
- `location` (required): Location to search (e.g., "San Francisco, CA"). When supplied without `placeId`, the server geocodes this string client-side via Photon/Nominatim — see [External Services](#external-services).
- `placeId` (optional): Google Maps Place ID. Overrides `location` and skips client-side geocoding entirely (no third-party calls).
- `checkin` (optional): Check-in date in YYYY-MM-DD format
- `checkout` (optional): Check-out date in YYYY-MM-DD format
- `adults` (optional): Number of adults (default: 1)
- `children` (optional): Number of children (default: 0)
- `infants` (optional): Number of infants (default: 0)
- `pets` (optional): Number of pets (default: 0)
- `minPrice` (optional): Minimum price per night
- `maxPrice` (optional): Maximum price per night
- `cursor` (optional): Pagination cursor for browsing results
- `propertyType` (optional): Filter by property type — `entire_home`, `private_room`, `shared_room`, or `hotel_room`
- `amenities` (optional): Array of amenity names to require. Supported values: `wifi`, `air_conditioning`, `washer`, `kitchen`, `free_parking`, `pool`, `hot_tub`, `king_bed`, `self_checkin`. King bed is an amenity in Airbnb's filter modal, not a separate bed-type filter.
- `instantBook` (optional): Filter to listings with Instant Book enabled (no host approval needed)
- `guestFavorite` (optional): Filter to Airbnb's curated "Guest favorite" quality bucket
- `minBedrooms` (optional): Minimum number of bedrooms
- `minBeds` (optional): Minimum number of beds (any type — to filter by type use `amenities`)
- `minBathrooms` (optional): Minimum number of bathrooms
- `ne_lat`, `ne_lng`, `sw_lat`, `sw_lng` (optional): Manual bounding-box corners. Provide all four together to override the geocoded bbox and skip the third-party geocoder entirely for this request.
- `ignoreRobotsText` (optional): Override robots.txt for this request

**Returns:**
- Search results with property details, pricing, and direct links
- Pagination information for browsing additional results
- Search URL for reference

### `airbnb_listing_details`

Get detailed information about a specific Airbnb listing.

**Parameters:**
- `id` (required): Airbnb listing ID
- `checkin` (optional): Check-in date in YYYY-MM-DD format
- `checkout` (optional): Check-out date in YYYY-MM-DD format
- `adults` (optional): Number of adults (default: 1)
- `children` (optional): Number of children (default: 0)
- `infants` (optional): Number of infants (default: 0)
- `pets` (optional): Number of pets (default: 0)
- `ignoreRobotsText` (optional): Override robots.txt for this request

**Returns:**
- Detailed property information including:
  - Location details with coordinates
  - Amenities and facilities
  - House rules and policies
  - Property highlights and descriptions
  - Direct link to the listing

### `airbnb_listing_reviews`

Fetch guest reviews for a specific listing, with optional server-side keyword search and tag filtering.

**Parameters:**
- `id` (required): Airbnb listing ID
- `query` (optional): Free-text keyword search across review content (e.g. `"noise"`, `"air conditioning"`, `"wifi"`). Multi-word queries supported. Matched terms are returned wrapped in `<mark>` tags inside `comments`. Combines with `tagName`. Note: Airbnb's search is literal — it won't match synonyms (e.g. `"noise"` will not match `"loud"`); for exhaustive scans, omit `query` and search the full reviews client-side.
- `tagName` (optional): Filter to a single Airbnb-tagged category. Use the uppercase `name` from the `reviewTags` field of the response, e.g. `"CLEANLINESS"`, `"LOCATION"`, `"HOSPITALITY"`, `"WALKABILITY"`, `"PARKING"`, `"VIEW"`. Combines with `query`.
- `limit` (optional): Maximum number of reviews to return. Omit to fetch all matching reviews (popular listings can have hundreds — token-heavy).
- `offset` (optional): Number of reviews to skip before returning (default: 0). Use with `limit` for paging.
- `sortingPreference` (optional): `MOST_RECENT` (default), `BEST_QUALITY`, `RATING_DESC`, or `RATING_ASC`. `MOST_RECENT` gives a fair cross-section; `BEST_QUALITY` is Airbnb's default and surfaces positive reviews first.
- `ignoreRobotsText` (optional): Override robots.txt for this request

**Returns:**
- `total` — total review count for the listing (or filtered subset)
- `returned` — number of reviews in this response
- `reviewTags` — Airbnb's AI-generated category tags with counts (use the `name` for the `tagName` filter)
- `reviews` — array of `{id, createdAt, language, reviewer, comments, hostResponse}`

## Technical Details

### Architecture
- **Runtime**: Node.js 18+
- **Protocol**: Model Context Protocol (MCP) via stdio transport
- **Format**: MCP Bundle (MCPB) v0.3
- **Dependencies**: Minimal external dependencies for security and reliability

### External Services

In addition to `airbnb.com`, the server makes geocoding requests to two third-party services to translate location queries into accurate map bounding boxes. This bypasses Airbnb's own server-side geocoder, which produces incorrect results for many non-US queries (e.g. "Paris, France" lands in Vendée; "Copenhagen, Denmark" lands in Wisconsin).

| Service | Endpoint | Used for | Notes |
| --- | --- | --- | --- |
| [Photon](https://photon.komoot.io/) | `photon.komoot.io` | Primary geocoder, called on every search without `placeId` | Free OSM-based service hosted by Komoot. One request per search. |
| [Nominatim](https://nominatim.openstreetmap.org/) | `nominatim.openstreetmap.org` | Fallback geocoder, called only when Photon does not return a bounding box | Subject to the [OSMF usage policy](https://operations.osmfoundation.org/policies/nominatim/) (max ~1 req/sec). |

Each search sends only the `location` string from the request to the geocoder — no other request fields, no IP geolocation, no tracking identifiers. The location string itself is, of course, the same string the user typed.

**Opting out:** there are two ways to skip the geocoders:

- **Per-request:** supply an explicit `placeId`. When `placeId` is present, the server uses Airbnb's own place lookup directly with no third-party calls.
- **Globally:** set the environment variable `DISABLE_GEOCODING=true`. The server will skip Photon/Nominatim entirely and pass the raw location string to Airbnb. This restores the pre-PR behavior for every search and guarantees zero third-party outbound traffic — at the cost of broken results for non-US locations that Airbnb's own geocoder mishandles. Defaults to `false`.

If a geocoder is unreachable or returns no result, the server falls back to sending the location string to Airbnb directly, exactly as it did before — so the worst case for an outage is that international searches degrade to the previous (broken) behavior, not that the search fails entirely.

### Error Handling
- Comprehensive error logging with timestamps
- Graceful degradation when Airbnb's page structure changes
- Timeout protection for network requests
- Detailed error messages for troubleshooting

### Security Measures
- Robots.txt compliance by default
- Request timeout limits
- Input validation and sanitization
- Secure environment variable handling
- No sensitive data storage

### Performance
- Efficient HTML parsing with Cheerio
- Request caching where appropriate
- Minimal memory footprint
- Fast startup and response times

## Compatibility

- **Platforms**: macOS, Windows, Linux
- **Node.js**: 18.0.0 or higher
- **Claude Desktop**: 0.10.0 or higher
- **Other MCP clients**: Compatible with any MCP-supporting application

## Development

### Building from Source

```bash
# Install dependencies
npm install

# Build the project
npm run build

# Watch for changes during development
npm run watch
```

### Testing

The extension can be tested by running the MCP server directly:

```bash
# Run with robots.txt compliance (default)
node dist/index.js

# Run with robots.txt ignored (for testing)
node dist/index.js --ignore-robots-txt
```

## Legal and Ethical Considerations

- **Respect Airbnb's Terms of Service**: This extension is for legitimate research and booking assistance
- **Robots.txt Compliance**: The extension respects robots.txt by default
- **Rate Limiting**: Be mindful of request frequency to avoid overwhelming Airbnb's servers
- **Data Usage**: Only extract publicly available information for legitimate purposes

## Support

- **Issues**: Report bugs and feature requests on [GitHub Issues](https://github.com/openbnb-org/mcp-server-airbnb/issues)
- **Documentation**: Additional documentation available in the repository
- **Community**: Join discussions about MCP and MCPB development

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please read the contributing guidelines and submit pull requests for any improvements.

---

**Note**: This extension is not affiliated with Airbnb, Inc. It is an independent tool designed to help users search and analyze publicly available Airbnb listings.
