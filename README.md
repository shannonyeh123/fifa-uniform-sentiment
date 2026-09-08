# FIFA Uniform Color-Sentiment App

Data mining social media (Mastodon) to find out **which uniform colors people react to positively
or negatively** when they talk about FIFA / World Cup kits.

The project is a single Jupyter notebook, `FIFA_uniform_color_sentiment.ipynb`, built as four programs:

| # | Program | What it does |
|---|---------|--------------|
| 1 | Authentication | Connects to the Mastodon API and requests the account object (returned as JSON) to confirm the credentials work. |
| 2 | Collect posts | Pulls recent public posts from uniform-related hashtags, strips the HTML from each post body with BeautifulSoup, and saves the cleaned text to `fifa_uniform_posts.json`. |
| 3 | Sentiment analysis | Scores every post with **TextBlob** (polarity and subjectivity) and demonstrates a second, machine-learning method with TextBlob's **`NaiveBayesAnalyzer`**, trained on the NLTK movie-review corpus. |
| 4 | Color sentiment | Detects color keywords in each post, attributes that post's polarity to every color it mentions, aggregates into an average sentiment per color, and visualizes the result as a bar chart plus a word cloud. |

## Running it

```bash
pip install -r requirements.txt
jupyter notebook FIFA_uniform_color_sentiment.ipynb
```

The notebook downloads the NLTK corpora it needs (`movie_reviews`, `punkt`, `punkt_tab`) on first run.

### Live data vs. sample data

The notebook runs end to end with or without API access.

To use **live Mastodon posts**, create a `keys.py` file next to the notebook:

```python
mastodon_api_base_url = "https://mastodon.social"
mastodon_access_token = "your-access-token"
```

Get a token by registering an application under *Preferences → Development* on your Mastodon
instance; the `read` scope is enough. `keys.py` is listed in `.gitignore` and must not be committed.

Without `keys.py`, the notebook falls back to a built-in list of 30 sample posts (`SAMPLE_POSTS`)
so every cell still produces output. **The results below describe that fallback dataset.**

## Findings (sample dataset, n = 30)

Overall sentiment toward FIFA uniforms is mildly positive: average polarity **+0.089**, with 21
positive and 9 negative posts.

| Color | Mentions | Avg. sentiment |
|-------|---------:|---------------:|
| gold | 4 | +0.365 |
| yellow | 2 | +0.323 |
| blue | 6 | +0.302 |
| red | 6 | +0.244 |
| white | 4 | +0.200 |
| navy | 2 | +0.172 |
| crimson | 2 | +0.155 |
| green | 5 | +0.106 |
| black | 2 | -0.044 |
| orange | 3 | -0.030 |
| grey | 2 | -0.151 |
| maroon | 1 | -0.521 |
| silver | 1 | -0.750 |
| teal | 1 | -1.000 |

Single-mention colors (azure, pink, purple, sky blue) are omitted from the table above.

Saturation appears to matter more than hue: vivid colors attract words like *stunning*, *vibrant*
and *bold*, while desaturated ones attract *dull*, *lifeless* and *forgettable*.

## Known limitations

- The sample dataset is small, and several colors rest on a single post, so their averages are
  anecdotal. Treat the extremes as illustration, not evidence.
- The color matcher counts compound names twice: "navy blue" is credited to both *navy* and *blue*,
  and "sky blue" to both *sky blue* and *blue*.
- TextBlob's lexicon is general-purpose, so football-specific praise and criticism are not always
  scored the way a domain reader would score them.
