# Twelve Data + JavaScript Stock Charts

Display real-time and historical stock market data as interactive candlestick charts using [Twelve Data API](https://twelvedata.com/) and [JavaScript Stock Charts](https://github.com/day-trading-simulator/javascript-stock-charts) - a free, zero-dependency charting library.

100% client-side. No backend required.

**[JavaScript Stock Charts Library](https://github.com/day-trading-simulator/javascript-stock-charts)** | **[Twelve Data API](https://twelvedata.com/)**

![Twelve Data + JavaScript Stock Charts](https://raw.githubusercontent.com/day-trading-simulator/javascript-stock-charts/main/javascript-stock-chart1.png)

## Features

- Interactive candlestick and line charts with Twelve Data market data
- Timeframe switching (1min, 5min, 15min, 1hour, 4hour, daily, weekly, monthly)
- Infinite scroll - automatically loads older data as you pan left
- 15+ built-in technical indicators (RSI, MACD, Bollinger Bands, etc.)
- Candlestick pattern detection
- Drawing tools (trendlines, Fibonacci retracements)
- Dark/light mode, fullscreen, screenshot to clipboard
- Mobile-friendly with touch/pinch zoom
- Zero dependencies - pure HTML5 Canvas

## Get Your API Key

1. Sign up for a free account at [twelvedata.com](https://twelvedata.com/register)
2. Copy your API key from the dashboard
3. Free tier: **8 API calls/minute**, **800 calls/day**

## Quick Start

Create an HTML file and paste:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Twelve Data + JavaScript Stock Charts</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/day-trading-simulator/javascript-stock-charts@main/stock-chart.css">
</head>
<body style="margin:0; background:#0a0a0f;">
    <div id="chart" style="width:100%; height:500px;"></div>

    <script src="https://cdn.jsdelivr.net/gh/day-trading-simulator/javascript-stock-charts@main/indicators.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/day-trading-simulator/javascript-stock-charts@main/patterns.js"></script>
    <script src="https://cdn.jsdelivr.net/gh/day-trading-simulator/javascript-stock-charts@main/stock-chart.js"></script>
    <script>
        const API_KEY = 'YOUR_TWELVEDATA_API_KEY';
        const TICKER = 'AAPL';

        fetch(`https://api.twelvedata.com/time_series?symbol=${TICKER}&interval=1day&outputsize=500&apikey=${API_KEY}`)
            .then(r => r.json())
            .then(json => {
                const data = json.values.reverse().map(bar => ({
                    t: new Date(bar.datetime).getTime(),
                    open: parseFloat(bar.open),
                    high: parseFloat(bar.high),
                    low: parseFloat(bar.low),
                    close: parseFloat(bar.close),
                    volume: parseInt(bar.volume)
                }));

                new StockChart('chart', {
                    data: data,
                    ticker: TICKER,
                    chartType: 'candlestick',
                    darkMode: true,
                    timeframe: '1day'
                });
            });
    </script>
</body>
</html>
```

Replace `YOUR_TWELVEDATA_API_KEY` with your key, open in a browser, and you'll have a working stock chart.

## Data Transformation

Twelve Data returns OHLCV values as **strings** in **reverse chronological order** (newest first). You must:

1. Call `.reverse()` to get chronological order (oldest first)
2. Use `parseFloat()` / `parseInt()` to convert strings to numbers
3. Convert `datetime` strings to millisecond timestamps

```javascript
function transformTwelveData(json) {
    return json.values.reverse().map(function(bar) {
        return {
            t: new Date(bar.datetime).getTime(),
            open: parseFloat(bar.open),
            high: parseFloat(bar.high),
            low: parseFloat(bar.low),
            close: parseFloat(bar.close),
            volume: parseInt(bar.volume)
        };
    });
}
```

## Timeframe Mapping

| Chart Timeframe | Twelve Data Interval | Output Size |
|----------------|---------------------|-------------|
| 1min | 1min | 500 |
| 5min | 5min | 500 |
| 15min | 15min | 500 |
| 1hour | 1h | 500 |
| 4hour | 4h | 500 |
| 1day | 1day | 500 |
| 1week | 1week | 260 |
| 1month | 1month | 120 |

## Advanced: Timeframe Switching & Infinite Scroll

The chart library has a built-in timeframe selector. Monitor it to auto-fetch new data when the user switches timeframes, and use `onReachingStart` for infinite scroll:

```javascript
var chart = new StockChart('chart', {
    data: chartData,
    ticker: 'AAPL',
    chartType: 'candlestick',
    darkMode: true,
    timeframe: '1day',
    onReachingStart: async function() {
        // Fetch older data using end_date parameter
        var endDate = formatDate(earliestTimestamp);
        var url = `https://api.twelvedata.com/time_series?symbol=AAPL&interval=1day&outputsize=500&end_date=${endDate}&apikey=${API_KEY}`;
        var json = await fetch(url).then(r => r.json());
        var older = transformTwelveData(json);
        chart.prependHistoricalData(older);
    }
});

// Monitor timeframe changes
setInterval(function() {
    if (chart.timeframe !== previousTimeframe) {
        previousTimeframe = chart.timeframe;
        loadChart(); // re-fetch with new interval
    }
}, 500);
```

See [examples/](examples/) for complete working implementations.

## Examples

- **[examples/basic-chart.html](examples/basic-chart.html)** - Minimal setup, fetch and render
- **[examples/full-featured.html](examples/full-featured.html)** - Timeframe switching, infinite scroll, error handling

## Resources

- [Twelve Data API Documentation](https://twelvedata.com/docs)
- [Twelve Data Time Series Endpoint](https://twelvedata.com/docs#time-series)
- [JavaScript Stock Charts Library](https://github.com/day-trading-simulator/javascript-stock-charts)
- [Live Chart Demo](https://simul8or.com/javascript-stock-chart.php)

## License

MIT - see [LICENSE](LICENSE) for details.

---

Data provided by [Twelve Data](https://twelvedata.com/) | Charts powered by [JavaScript Stock Charts](https://github.com/day-trading-simulator/javascript-stock-charts)
