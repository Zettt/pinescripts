## Key issues found

- YES. We only allow two bands now. An inner one, and an outer one: The two multiplier inputs are both titled “Keltner Bands Normal Multiplier,” which is confusing; titles should be distinct for Tight vs Normal to prevent user error. [^21]
- YES: Unused or misused variables exist (e.g., atrlength and keltnerEMA are defined but never applied in band calculations), and comments such as “plot mean deivations” contain typos and mislead maintenance. [^21]
- YES. I need some examples though, before allowing this as feature: “firstFreeBarUp/Down” are states (being inside a band) rather than event conditions; ta.barssince() should be applied to a discrete event like “first bar back inside after being outside,” not a persistent state. [^21]
- YES. We should allow users to set whether to use close or high though. Aggressive traders might want to use highs, rather than closes.: Cross logic is asymmetric: longCross uses wick (high >= upper) while shortCross uses close (close <= lower); consistency and a user option to choose wick vs body is advisable. [^21]


## 20 improvements for v3.0

- Fix input labels and UX
    - YES. Tooltips will be very good: Give unique, precise titles and tooltips, e.g., “Tight Multiplier,” “Normal Multiplier,” “Extreme Multiplier,” and add short descriptions to clarify the visual/logic effects. [^21]
    - YES. See below: Change showOnlyFirstSignal from a hardcoded variable to a user input input.bool with a tooltip explaining “emit only the first signal after a reset.” [^21]
    - YES: Replace bandsToUse input.string with an input.enum to reduce typos and speed comparisons. [^21]
- Make band math explicit and configurable
    - YES: Actually use atrlength by adding a user input for ATR length and computing Keltner bands explicitly: basis = selected MA of close; range = ATR(atrLength); up = basis + mult*range; down = basis − mult*range; this removes ambiguity from ta.kc defaults and uses the declared atrlength. [^21]
    - YES/MAYBE. Less important though: Add a selectable “Mean type” input (EMA/SMA/WMA/RMA/HMA) so users can adapt the “mean reversion” anchor to instrument characteristics. [^21]
    - YES: Offer a setting to compute bands from typical price hlc3 or ohlc4, not only close, which can reduce noise on some markets. [^21]
- Correct “free bars” into events and improve deviations
    - YES: Redefine “first free bar back inside” as a discrete event: backInsideUpper = (high <= up and close <= up) and (high[^21] > up[^21]); barsSinceUpperReentry = ta.barssince(backInsideUpper); same for lower; this aligns with the intended “first bar back in” logic. [^21]
    - YES. Needs refinement. Why, what, how, examples: Compute and optionally color by a price-to-band z-score-like metric, e.g., dev = (close − basis)/(ATR*mult) to make deviation intensity consistent across multipliers. [^21]
    - NO: Add a user option to color only bodies, only wicks, or entire bars, and a transparency control to avoid overpowering chart themes. [^21]
- Standardize crossing logic and add signal modes
    - YES: Provide a “Signal source” option: Wick (high/low crossing), Close (close crossing), or Confirmed (barstate.isconfirmed) to minimize repaint and match different styles. [^21]
    - YES. See below: Normalize long/short cross symmetry: either both use wick or both use close, controlled by the “Signal source” input to avoid bias. [^21]
- Improve visuals and context
    - YES: Use fill() to shade between upper and lower bands (tight, normal, extreme) with separate toggles and transparency so traders can visually parse regimes at a glance. [^21]
    - MAYBE. Needs some refinement and more explanations: Add a “Squeeze” detector measuring normalized bandwidth, e.g., (up − down)/basis, with a threshold input to mark compression expansions. [^21]
    - MAYBE. Consolidation, yes. Color-blind palette, probably no: Consolidate colors into var color constants and add a “Color-blind palette” switch to enhance accessibility. [^21]
- Alerts and automation
    - MAYBE: Add alertcondition() for all user-relevant events: upper/lower band crosses, first bar back inside, free-bar breakouts, and squeeze begin/end, with descriptive messages and placeholders for ticker/timeframe. [^21]
    - PROBABLY NO: Provide an option to output compact labels instead of shapes, reducing clutter while maintaining alert functionality. [^21]
- Multi-timeframe and filters
    - NO: Add a higher-timeframe trend filter: compute HTF basis and require alignment (e.g., long signals only when close > HTF basis) to reduce countertrend trades; expose HTF timeframe input. [^21]
    - YES. We'll add this as potential feature request. I like the idea but needs more examples and example math to make it more approachable for me: Include optional volume or volatility filters (e.g., above average volume or ATR percentile) to gate signals in dead markets. [^21]
    - NO: Session and day-of-week filters let users avoid illiquid hours or specific weekdays known to skew behavior. [^21]
- Strategy/backtest mode
    - NO. TradingView doesn't play with this type of indicator. It's either a strategy or an indicator: Add a “Mode” switch: Indicator vs Strategy; when Strategy, generate entries on band-cross or reentry events, and exits via stop/target inputs (ATR-based or fixed %), with slippage/commission inputs to produce realistic stats. [^21]
    - NO: Provide optional pyramiding and max concurrent signals per direction to reflect “show only first” behavior consistently in backtests. [^21]
- Refactoring and maintenance
    - MAYBE. I don't understand this: Factor repeated logic (tight/normal/extreme) into a small function returning basis/up/down for the selected regime to reduce duplication and potential drift between branches. [^21]
    - YES. Needs all occurrences as list: Clean typos and dead code: remove unused keltnerEMA, actually apply atrlength, fix stray commented lines, and correct comments like “mean deivations” for clarity. [^21]


## Example event fix (snippet)

- Replace the current “first free bar” state with an event and use it with ta.barssince() to ensure discrete, non-repainting triggers. [^21]

```pine
// Example for the chosen band set “upBand/downBand”
insideUpper   = high <= upBand and close <= upBand
outsideUpper1 = high[^21] > upBand[^21]
reentryUpper  = insideUpper and outsideUpper1
barsSinceReentryUpper = ta.barssince(reentryUpper)

// Same idea for lower band
insideLower   = low >= downBand and close >= downBand
outsideLower1 = low[^21] < downBand[^21]
reentryLower  = insideLower and outsideLower1
barsSinceReentryLower = ta.barssince(reentryLower)
```

- This change makes “reentry” a one-bar event that is easy to alert on and to measure with ta.barssince for cooldown logic. [^21]


## Optional extensions

- MAYBE: Add an on-chart summary table showing the current regime, squeeze state, last signal timing, and HTF alignment to speed decision-making. [^21]
- MAYBE. Needs more explanation: Offer an “adaptive” profile that scales multipliers or ATR length by recent volatility percentiles, keeping bands effective across regimes without manual retuning. [^21]


## License notes

- The header already references MPL 2.0; keep the notice intact, and if distributing modified versions, preserve copyright and license notices and clarify modifications. [^21]
- Consider adding a brief LICENSE section in the script’s description pointing to MPL terms for clarity for end users and downstream modifiers. [^21]


## Remove outright

- YES: Remove the **Advanced Mode** toggle, since it only flips colors/shapes and adds cognitive load without changing the trading logic. [^21]
- Remove Free Bar “star” markers (plotchar ★), as they duplicate the idea of band crosses/re-entries and clutter the chart. [^21]
- NO. The mean deviations heatmap is supposed to indicate to traders whether a reversal is more likely, as an extended move outside the bands increases the likelihood of a reversal: Remove the Mean Deviations bar coloring (barcolor heatmap), which often overpowers candles and duplicates the role of clear, discrete signals. [^21]
- YES. Needs refinement as users would need a smart system to set the distance between two bands. At the moments I'm thinking of only allowing the use of up to two bands. We could apply a single color (okay) or gradient color (better) to inside of the bands: Remove plotting of multiple band systems simultaneously (tight/normal/extreme) to cut visual noise and prevent conflicting cues. [^21]


## Consolidate or deprecate

- YES. We only allow the most inner band to be used for indications. Users can just set the band wider: Remove bandsToUse and use a single, primary band set for both plotting and signal logic to avoid divergence between visuals and alerts. [^21]
- YES: Remove separate “Show Tight Band?” and “Show Extreme Band?” toggles; expose one band set with a single multiplier instead of three fixed presets. [^21]
- Remove the separate “Show Mean EMA?” middle-line toggle; if a basis is needed, keep it as a style option under one band set rather than a distinct feature. [^21]
- YES: Remove asymmetric cross definitions (wick for long, close for short) by consolidating to one signal mode; asymmetry confuses interpretation. [^21]


## Replace with simpler alternatives

- YES. Maybe rename to "compute only x signals" or something different: Replace “Show Only First Signal” with a numeric cooldown bars input for explicit, flexible de-duplication. [^21]
- MAYBE. Needs suggestions: Standardize on one unobtrusive marker for all signals, replacing shape-style toggles tied to Advanced Mode. [^21]
- YES: Keep a single accessible color palette that reads well on light/dark themes, removing multiple aesthetic-only color options. [^21]
- I don't understand this one: Use one consistent iconography so the same event always looks the same, improving scan-ability. [^21]


## Streamline inputs

- YES. How would this look like if we only allow one or two bands as an option?: Collapse duplicate multiplier concepts into one multiplier slider with sensible defaults for simplicity. [^21]
- YES. DUPLICATE. If we remove advanced mode, I don't see which other features this would affect: Remove inputs that only affect aesthetics, retaining those that materially change calculations or alerts. [^21]
- NO. There are no sliders in Pine Script. Where do we use string inputs?: Prefer enums over strings and numeric sliders where appropriate to reduce ambiguity and user error. [^21]


## Logic alignment

- YES. As mentioned, we allow at most two bands. The indicator will always use the more tighter one for its calculations: Ensure a single source of truth for both display and signal checks by unifying the band set used for plotting and logic. [^21]
- MAYBE. The two features are very different. A band cross shows traders when momentum is picking up, and closes outside of the first band, whereas free bars show extended momentum, likely to reverse, because it's just too extreme. Suggestions?: Collapse overlapping event types (e.g., cross vs “free bar breakout”) into a single, unambiguous crossing definition to avoid double signaling. [^21]
- YES. I believe this makes sense. Although I need to know where we'd apply this: Prefer discrete events over persistent-state pseudo-events to reduce confusion and improve alert clarity. [^21]

<span style="display:none">[^1][^10][^11][^12][^13][^14][^15][^16][^17][^18][^19][^2][^20][^3][^4][^5][^6][^7][^8][^9]</span>

<div style="text-align: center">⁂</div>

[^1]: https://www.tradingview.com/scripts/

[^2]: https://www.youtube.com/watch?v=6oyjpKMcVTY

[^3]: https://www.tradingview.com/chart/BTCUSD/9wGoeKnE-TOP-10-BEST-TRADINGVIEW-INDICATORS-FOR-2025/

[^4]: https://www.mindmathmoney.com/articles/best-tradingview-indicators-for-2025-custom-settings-included

[^5]: https://wundertrading.com/journal/en/learn/article/best-tradingview-indicators

[^6]: https://www.reddit.com/r/TradingView/comments/1lqg6ra/best_tradingview_indicators_3_years_experience/

[^7]: https://www.youtube.com/watch?v=M3pEFqI1OB4

[^8]: https://www.tradingview.com/chart/XAUUSD/0zs1MK6A-5-Most-Popular-Momentum-Indicators-to-Use-in-Trading-in-2025/

[^9]: https://www.youtube.com/watch?v=gHoDHXzb_CE

[^10]: https://www.reddit.com/r/Trading/comments/1kcfkoc/is_anyone_still_using_indicators_in_2025/

[^11]: https://www.vestinda.com/blog/top-trading-indicators-2025

[^12]: https://www.youtube.com/watch?v=b9k0g_wg2p0

[^13]: https://www.reddit.com/r/TradingView/comments/1jc8w67/10_best_indicators_on_tv_8_years_experience/

[^14]: https://onlinelibrary.wiley.com/doi/10.1002/lary.32033

[^15]: https://ijournalse.org/index.php/ESJ/article/view/2605

[^16]: https://nbpublish.com/library_read_article.php?id=75306

[^17]: https://restpublisher.com/wp-content/uploads/2025/06/Machine-Learning-Driven-Impact-of-Recent-Amendments-in-GST-Law-on-Businesses-and-Professions-Using-RANDOM-Forest-Regression-and-Support-Vector-Regression-SVR-Models.pdf

[^18]: https://ijsenet.com/index.php/IJSE/article/view/138

[^19]: http://medrxiv.org/lookup/doi/10.1101/2025.08.19.25334041

[^20]: https://www.frontiersin.org/articles/10.3389/fpsyt.2025.1601765/full

[^21]: https://mozilla.org/MPL/2.0/

