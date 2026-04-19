# trends-agent-crewai

Live trend data tools for CrewAI agents and crews. Connect Trends MCP
and your CrewAI agent gets typed tools for search, social, ecommerce,
news, and developer trends across one MCP server.

> Live trend data for AI agents. One connection. Every platform.
> Powered by [trendsmcp.ai](https://trendsmcp.ai)

## Get your free API key

1. Go to [trendsmcp.ai](https://trendsmcp.ai)
2. Enter your email, key sent instantly
3. 100 free requests per month, no credit card

[Get your free key](https://trendsmcp.ai)

## Install

Use the official CrewAI MCP adapter shipped in `crewai-tools`:

```bash
pip install "crewai-tools[mcp]" crewai
```

## Use Trends MCP as CrewAI tools

```python
import os
from crewai import Agent, Task, Crew
from crewai_tools import MCPServerAdapter

server_params = {
    "url": "https://api.trendsmcp.ai/mcp",
    "transport": "streamable-http",
    "headers": {"Authorization": f"Bearer {os.environ['TRENDS_MCP_API_KEY']}"},
}

with MCPServerAdapter(server_params) as trends_tools:
    analyst = Agent(
        role="Trend Analyst",
        goal="Find rising topics across search and social platforms",
        backstory="Senior analyst with a nose for early signals.",
        tools=trends_tools,
    )

    task = Task(
        description=(
            "Compare 1 year YouTube vs TikTok trends for 'Stanley cup'."
            " Return a 3 bullet summary with the deltas."
        ),
        expected_output="Three bullet points with growth percentages.",
        agent=analyst,
    )

    crew = Crew(agents=[analyst], tasks=[task])
    result = crew.kickoff()
    print(result)
```

Set the API key in your shell:

```bash
export TRENDS_MCP_API_KEY=YOUR_API_KEY
```

The exact CrewAI MCP API can evolve, see
[docs.crewai.com](https://docs.crewai.com) for the current reference.

## Use the REST API directly

If you prefer not to use the MCP adapter, you can wrap the REST API as
a custom CrewAI `BaseTool`:

```python
import requests
from crewai_tools import BaseTool

class TrendsTool(BaseTool):
    name: str = "Get Trend"
    description: str = "Get a 5 year trend for a keyword on a given source."

    def _run(self, source: str, keyword: str) -> dict:
        return requests.post(
            "https://api.trendsmcp.ai/api",
            headers={"Authorization": "Bearer YOUR_API_KEY"},
            json={"source": source, "keyword": keyword},
            timeout=30,
        ).json()
```

## Three tools your AI gets

- `get_trends`, historical time series for any keyword on any source
  (5 years weekly, 30 days daily where supported).
- `get_growth`, percentage change over any period (7D, 1M, 3M, 6M, 1Y,
  YTD, custom dates).
- `get_top_trends`, what is trending right now across live platform feeds.

## All data sources in one connection

One API key gives your CrewAI agents every keyword source and every live
feed below.

### Keyword sources (Get Trends and Get Growth)

| source | What it measures | Keyword format |
| --- | --- | --- |
| `google search` | Google search volume | Any keyword or phrase |
| `google images` | Google image search volume | Any keyword or phrase |
| `google news` | Google News search volume | Any keyword or phrase |
| `google shopping` | Google Shopping search volume | Any keyword or phrase |
| `youtube` | YouTube search volume | Any keyword or phrase |
| `tiktok` | TikTok hashtag volume | Hashtag or topic |
| `reddit` | Subreddit subscribers | Subreddit name only, no `r/` prefix |
| `amazon` | Amazon product search volume | Product name or category |
| `wikipedia` | Wikipedia page views | Article title or topic |
| `news volume` | News article mention volume | Any keyword or phrase |
| `news sentiment` | News sentiment score (positive / negative) | Any keyword or phrase |
| `app downloads` | Mobile app download estimates (Android) | Package id or app identifier |
| `npm` | npm package weekly downloads | Exact package name e.g. react |
| `steam` | Steam concurrent players (monthly) | Game display name e.g. Elden Ring |

All values are normalized to a 0 to 100 scale where the pipeline supports
it, so you can compare different sources directly.

### Live leaderboards (Get Top Trends)

Google Trends, Google News Top News, TikTok Trending Hashtags, YouTube
Trending, X (Twitter) Trending, Reddit Hot Posts, Reddit World News,
Wikipedia Trending, Amazon Best Sellers Top Rated, Amazon Best Sellers by
Category, App Store Top Free, App Store Top Paid, Google Play, Top
Websites, Spotify Top Podcasts, Steam Most Played, GitHub Trending Repos,
IMDb MOVIEmeter, Trending Books.

The source list evolves over time. Authoritative reference at
[trendsmcp.ai/docs](https://trendsmcp.ai/docs).

## Crew patterns that work well

- **Trend Researcher + Writer crew.** Researcher pulls multi-source
  data, Writer turns it into a brief or post.
- **Investment Crew.** Researcher fetches news sentiment, Reddit
  growth, and Wikipedia views; Strategist scores; Writer drafts the
  memo.
- **SEO Crew.** Keyword Researcher pulls Google + YouTube + Amazon
  growth; Editor builds the topic cluster.
- **Product Crew.** Trend Scout pulls TikTok and Amazon Best Sellers;
  Designer drafts product positioning.

## Pricing

Free forever for 100 requests per month. Paid plans available. See
[trendsmcp.ai/pricing](https://trendsmcp.ai/pricing).

## Resources

- [Get a free API key](https://trendsmcp.ai)
- [Documentation and full reference](https://trendsmcp.ai/docs)
- [Pricing](https://trendsmcp.ai/pricing)
- [All TrendsMCP repositories](https://github.com/trendsmcp)

## Security

Never commit your API key to a public repository. Use environment
variables or your OS secret store. Keys can be rotated at any time from
your TrendsMCP dashboard. The `YOUR_API_KEY` placeholder above is a
literal string to replace with your own key after you copy the snippet
locally.

## License

MIT. See [LICENSE](./LICENSE).
