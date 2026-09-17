# Google Jobs Scraper

[<img width="950" height="300" alt="Google Jobs Scraper tool" src="https://github.com/user-attachments/assets/40bda5e4-5b53-49c1-8ea1-aa59f983340e" />](https://serpapi.com/google-jobs-api?utm_source=github_google_jobs_scraper)

Google Jobs Scraper - A tool to scrape Google Jobs results with a simple API. Get job titles, company names, locations, descriptions, application links, salaries, and employment details when available.

Get results as structured JSON for applications or Markdown for LLMs and AI agents, without managing HTML parsing or proxies.

## How to scrape Google Jobs?

Using a simple GET request, you can retrieve Google Jobs search results:

``` text
https://serpapi.com/search?engine=google_jobs&q=software+engineer&location=Austin%2C+Texas%2C+United+States&gl=us&hl=en&api_key=YOUR_SERPAPI_API_KEY
```

- Register at [SerpApi to get your API Key](https://serpapi.com?utm_source=github_google_jobs_scraper). Replace `YOUR_SERPAPI_API_KEY` in the examples with your private key. Keep it out of source control, shared URLs, and logs; use server-side code rather than exposing it in a browser.
- `q` parameter: defines the job search query.
- `location` parameter (optional): defines where the search originates. A city-level location is recommended; without it, results may use the proxy's location.
- `gl` and `hl` parameters (optional): define the country and language.

## Output formats: JSON and Markdown

JSON is the default output and is useful when you need individual job fields. Add `output=md` to receive Markdown for text-based workflows, LLMs, and AI agents.

The examples below use the same query, location, country, and language in both formats.

### Markdown request

``` bash
curl --get --fail-with-body --max-time 60 https://serpapi.com/search \
 --data-urlencode engine="google_jobs" \
 --data-urlencode q="software engineer" \
 --data-urlencode location="Austin, Texas, United States" \
 --data-urlencode gl="us" \
 --data-urlencode hl="en" \
 --data-urlencode output="md" \
 --data-urlencode api_key="YOUR_SERPAPI_API_KEY"
```

Markdown is returned as text, not a JSON object. Use a text response reader instead of a JSON parser. The field guide below describes JSON output, not a guaranteed Markdown structure.

## Code examples

### cURL Integration

``` bash
curl --get --fail-with-body --max-time 60 https://serpapi.com/search \
 --data-urlencode engine="google_jobs" \
 --data-urlencode q="software engineer" \
 --data-urlencode location="Austin, Texas, United States" \
 --data-urlencode gl="us" \
 --data-urlencode hl="en" \
 --data-urlencode output="json" \
 --data-urlencode api_key="YOUR_SERPAPI_API_KEY"
```

These cURL examples require cURL 7.76 or later for `--fail-with-body`. They surface HTTP failures and keep the error body visible; inspect JSON responses for a top-level `error` as well.

### Python Integration

Create a `main.py` file and install [requests](https://pypi.org/project/requests/):

``` bash
pip install requests
```

Add this code to your file:
``` py
import requests

SERPAPI_API_KEY = "YOUR_SERPAPI_API_KEY"

params = {
    "api_key": SERPAPI_API_KEY,
    "engine": "google_jobs",
    "q": "software engineer",
    "location": "Austin, Texas, United States",
    "gl": "us",
    "hl": "en",
    "output": "json"
}

search = requests.get("https://serpapi.com/search", params=params, timeout=60)
search.raise_for_status()
response = search.json()
if "error" in response:
    raise RuntimeError(response["error"])
print(response)
```

After checking for errors, `response["jobs_results"]` gives the array of jobs when present.

To request Markdown instead, keep the imports, API key, and parameter definitions above, and replace the request and response-handling lines with:

``` py
params["output"] = "md"
search = requests.get("https://serpapi.com/search", params=params, timeout=60)
search.raise_for_status()
print(search.text)
```

### JavaScript Integration

Create an `index.js` file and install the [SerpApi JavaScript package](https://github.com/serpapi/serpapi-javascript):

``` bash
npm install serpapi
```

Add this code for JSON results:
``` js
const { getJson } = require("serpapi");
const API_KEY = "YOUR_SERPAPI_API_KEY";

getJson({
  api_key: API_KEY,
  engine: "google_jobs",
  q: "software engineer",
  location: "Austin, Texas, United States",
  gl: "us",
  hl: "en",
  output: "json"
}, (json) => {
  if (json.error) {
    throw new Error(json.error);
  }
  console.log(json);
});
```

For Markdown, use a text-capable HTTP client rather than `getJson`. This standalone alternative needs Node.js 18 or later and no package installation:

``` js
async function main() {
  const params = new URLSearchParams({
    api_key: "YOUR_SERPAPI_API_KEY",
    engine: "google_jobs",
    q: "software engineer",
    location: "Austin, Texas, United States",
    gl: "us",
    hl: "en",
    output: "md"
  });

  const response = await fetch(`https://serpapi.com/search?${params}`, {
    signal: AbortSignal.timeout(60000)
  });
  const text = await response.text();
  if (!response.ok) {
    throw new Error(`SerpApi HTTP ${response.status}: ${text}`);
  }
  console.log(text);
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

### Other Programming Languages
Use a simple GET request from any programming language, or explore the ready-to-use libraries in [SerpApi Integrations](https://serpapi.com/integrations?utm_source=github_google_jobs_scraper).

## Google Jobs Scraper Parameters
The main parameters from the current Google Jobs API documentation are:

| Name | Description | Requirement |
|------|-------------|-------------|
| engine | Must be set to `google_jobs`. | Required |
| api_key | Your SerpApi private API key. | Required |
| q | The job search query. | Required |
| **Geographic Location** | | |
| location | Search origin, preferably at city level. Cannot be used with `uule`. Use the [Locations API](https://serpapi.com/locations-api) to find supported locations. | Optional |
| uule | Google-encoded location. Cannot be used with `location`. Coordinate-based values require a matching `gl` for consistent results. | Optional |
| **Localization** | | |
| google_domain | Google domain to use. Defaults to `google.com`. | Optional |
| gl | Two-letter country code, such as `us` or `uk`. See the [supported Google Jobs countries](https://serpapi.com/google-jobs-countries). | Optional |
| hl | Language code, such as `en`, `es`, or `fr`; regional variants such as `en-gb` are also supported. | Optional |
| **Pagination** | | |
| next_page_token | Token from `serpapi_pagination.next_page_token` for the next page. Each page returns up to 10 results. | Optional |
| **Filtering** | | |
| uds | Google-provided filter string from the response's `filters` section. Use the filter's associated query as well. | Optional |
| lrad | Search radius in kilometers. This does not strictly limit the results to that radius. | Optional |
| **SerpApi Parameters** | | |
| no_cache | Defaults to `false`. Set to `true` to request fresh results instead of using the one-hour cache for identical parameters. Cannot be combined with `async`. | Optional |
| async | Defaults to `false`. Set to `true` to submit a search for later retrieval through the [Searches Archive API](https://serpapi.com/search-archive-api). Cannot be combined with `no_cache` or used with Ludicrous Speed enabled. | Optional |
| zero_trace | Enterprise only; defaults to `false`. Set to `true` to skip storing search parameters, files, and metadata on SerpApi's servers. | Optional |
| output | `json` (default) for structured results, `html` for raw HTML, or `md` for Markdown. | Optional |
| json_restrictor | Limit the JSON fields returned; see the [JSON Restrictor documentation](https://serpapi.com/json-restrictor). | Optional |

Google has discontinued offset-based pagination with `start` and deprecated the `chips` and `ltype` filters. Use `next_page_token` for pagination and the returned `filters` for filtering instead.

Visit [our documentation](https://serpapi.com/google-jobs-api) for all available parameters.

### Pagination

Use JSON output to read `serpapi_pagination.next_page_token`. To fetch the next page, repeat the current search with `next_page_token` replaced by that token, retaining the query, location, localization, and any active `uds` filter. Alternatively, use `serpapi_pagination.next` with your API key without changing the returned search parameters.

Each page contains up to 10 results. Stop when no next-page token is returned, not merely when a page contains fewer than 10 jobs. Do not increment a `start` offset. Each additional page is a separate search.

### Applying filters

When filters are available, each entry in `filters` provides `parameters.uds` and `parameters.q`, along with a `serpapi_link`. Use both returned parameters for a new search, retaining your location and localization, or follow the `serpapi_link` with your API key. Do not invent, decode, or concatenate filter strings. Remove any previous pagination token when changing the query, location, or filter, then paginate the new search using its own returned tokens.

## Available data on Google Jobs (JSON Response)
The fields returned depend on the job listing. The following is a field guide, not a literal API response:

``` json
{
  "jobs_results": [
    {
      "title": "String - Job title",
      "company_name": "String - Hiring company",
      "location": "String - Job location",
      "via": "String - Source job board or website",
      "share_link": "String - Google link to the job",
      "thumbnail": "String - Company thumbnail URL",
      "description": "String - Job description",
      "extensions": ["String - Displayed job attributes"],
      "detected_extensions": {
        "posted_at": "String - When the job was posted",
        "salary": "String - Advertised salary",
        "schedule_type": "String - Employment type",
        "health_insurance": "Boolean - Health insurance benefit",
        "dental_coverage": "Boolean - Dental coverage benefit",
        "paid_time_off": "Boolean - Paid time off benefit"
      },
      "job_highlights": [
        {
          "title": "String - Section heading, such as Qualifications",
          "items": ["String - Highlighted requirement or benefit"]
        }
      ],
      "apply_options": [
        {
          "title": "String - Application provider",
          "link": "String - Application URL"
        }
      ],
      "job_id": "String - Job identifier"
    }
  ],
  "filters": [
    {
      "name": "String - Filter label",
      "parameters": {
        "uds": "String - Google filter value",
        "q": "String - Query associated with the filter"
      },
      "link": "String - Google search URL",
      "serpapi_link": "String - SerpApi URL for the filtered search"
    }
  ],
  "serpapi_pagination": {
    "next_page_token": "String - Token for the next page, when available",
    "next": "String - SerpApi URL for the next page, when available"
  }
}
```

Salary, benefits, highlights, filters, and other optional fields may be missing. A missing benefit field does not mean the employer does not offer that benefit. The example focuses on result fields rather than the full response envelope.

Job descriptions, highlights, and `apply_options` are returned directly in `jobs_results` when available. Use application links from that array; this workflow does not require a separate Google Jobs Listing request. The [Google Jobs Listing documentation](https://serpapi.com/google-jobs-listing-api) notes that only `ratings` remains there; its former `salaries`, `salaries_comparison`, `apply_options`, and `similar_jobs` fields are no longer available.

For Markdown output, use `output=md` and read the response as text as shown above.

## Use cases
- Build job boards that collect listings for specific roles and locations.
- Track hiring activity across companies or industries.
- Compare advertised salaries where listings include compensation.
- Analyze in-demand qualifications and skills from job descriptions.
- Create job alerts with links to application providers.
- Research employment trends across cities and job categories.

## Blog tutorial
- [How to Scrape Google Jobs Results](https://serpapi.com/blog/how-to-scrape-google-jobs-results/)
- [Google Jobs Search Query Operators](https://serpapi.com/blog/google-jobs-search-query-operators/)
- [Scrape Google Jobs to easily make Job Lists using SerpApi](https://serpapi.com/blog/scrape-google-jobs-to-easily-make-job-lists-using-serpapi/)

Use the current request examples above when adapting older tutorial code. Pagination uses `next_page_token`, and current filters use `uds`, not `start`, `chips`, or `ltype`.

## Video tutorial
- [Scrape Google Jobs Data with simple API (Python + SerpApi)](https://www.youtube.com/watch?v=4C6Z67InBrE)

The video is an introductory walkthrough; use the pagination and filtering rules above for current behavior.

## Contacts
Feel free to reach out via `contact@serpapi.com`.

Check other [Google Scrapers](https://github.com/serpapi/google-scraper) from SerpApi.
